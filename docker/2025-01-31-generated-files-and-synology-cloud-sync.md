---
layout: post
author: Wouter Van Schandevijl
title: "Share files generated by Docker with Synology CloudSync"
subTitle: "Just what is going on here"
date: 2025-01-31
desc: >
  A CRON job running with docker-compose on our Synology
  creates files that are synced with a third party using
  CloudSync. But the files are not synced at their end??
bigimg:
  url: synology-docker-cloud-sync-big.png
  origin: Midjourney
  prompt: "docker whale in the clouds"
img:
  url: synology-docker-cloud-sync.png
  desc: ""
  origin: Midjourney
  prompt: "docker in the clouds"
categories: docker
tags: [synology]
interesting:
  - url: https://synocommunity.com/package/inotify-tools
    desc: "SynoCommunity package: inotify-tools"
  - url: https://www.synology.com/en-global/dsm/feature/cloud_sync
    desc: "Synology CloudSync"
  - url: https://www.reddit.com/r/synology/comments/pejrp5/files_created_by_docker_container_not_seen_by/
    desc: "Files created by docker container not seen by Synology Drive"
extras:
  - githubproject: https://github.com/itenium-be/synology-cloud-sync-vs-docker
    githubtext: "Minimal Example Code"
toc:
  title: CloudSync
  icon: server
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_synology-cloudsync-not-picking-up-files-generated-activity-7315675047791132672-WYVr"
  twitter: "https://x.com/itenium_be/status/1909909186873380974"
  facebook: "https://www.facebook.com/share/p/15c7bpza67/"
---

We needed to generate some files and share them with a third party.
They preferred we put the files on our SharePoint since they already had access to it.

So we made a `docker-compose` that scheduled a CRON
job to create the files in the shared folder.

The files were getting generated alright and other files were
getting synced alright, but the generated files were not!?

<!--more-->

# In CloudSync Folder

## Synology Configuration

### Enable NFS

Steps: Control Panel > File Services > NFS > Enable

![Enabling NFS v4 on Synology](/assets/blog-images/synology-docker-cloud-sync-1-enable-nfs.png "Enabling NFS v4 on Synology"){: .img-responsive}

### Enable NFS on Shared Drive

- Control Panel > Shared Folder > right Click "workspace" > Edit > NFS Permissions
  - Create > Hostname or IP: 127.0.0.1 > Save

![Enabling NFS on Synology Shared Folder](/assets/blog-images/synology-docker-cloud-sync-2-nfs-for-cloud-sync-share.png "Enabling NFS on Synology Shared Folder"){: .img-responsive}


## The Code

### compose.yaml

Requires an `.env` file with: `SYNC_DIR=/volume1/CloudSyncedFolder/shared-with-3rd-party`

```yaml
version: "3"

services:
  cron_job:
    # Sets up the CRON job
    build: .
    # Scheduled job write to /export
    volumes:
      - exportVolume:/export

volumes:
  # The required magic
  exportVolume:
    driver: local
    driver_opts:
      type: nfs
      o: nfsvers=4,addr=127.0.0.1,rw
      # The folder the job writes to
      # Shared with the 3rd party
      device: ":${SYNC_DIR}"
```

### Other files

[Minimal CRON job setup](https://github.com/itenium-be/synology-cloud-sync-vs-docker)
- Dockerfile: Copies `sh` files to the container and runs start.sh
- start.sh: Calls `crontab` to run create-file.sh and starts the daemon
- create-file.sh: `touch /export/export.csv`

## NFWhat?

NFS or [Network File System](https://en.wikipedia.org/wiki/Network_File_System),
a distributed file system protocol which allows you to access files over a network
as if they were on the local disk. It has been around since 1984 and is pretty much
the standard on Linux systems.

Thanks to NFS we get proper `inotify` events and CloudSync picks up our files.
Crisis averted!


# Outside CloudSync Folder

It might not be possible to create the files directly into the folder that
CloudSync is active on.

The easy solution would be to create a symbolic link to the CloudSync folder;
but, of course, that doesn't work.

```sh
ln -s /volume1/Projects/GeneratedFiles /volume1/Dropbox/GeneratedProjectFiles
```

## Scheduled rsync Task

An easy solution here is creating a recurring task to run `rsync`.

- Synology DSM
- Control Panel > Services > Task Scheduler
- Create > Scheduled Task > User-defined script
  - General: Set a task name
  - Schedule: I kept the default of a daily sync
  - Task Settings > Run command

```sh
rsync -av --delete /volume1/Projects/GeneratedFiles /volume1/Dropbox
```

This will create a folder `GeneratedFiles` in `/volume1/Dropbox`.

rsync options:
- `-a`: Archive mode to preserve file attributes (permissions, timestamps etc)
- `-v`: Verbose output. You'll have to select a folder for the output in Task Scheduler > Settings to actually see the output.
- `--delete`: If your Docker container also deletes files
- `--chown=CloudSync:CloudSync`: I didn't have to change ownership for CloudSync to pick up the files

Of course you could also just add this to the `docker-compose` 😀
