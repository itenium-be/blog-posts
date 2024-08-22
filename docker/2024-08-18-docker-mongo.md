---
layout: post
author: Wouter Van Schandevijl
title: "Docker + Mongo: a guide"
subTitle: "Load data with js or sh"
date: 2024-08-18
desc: >
  Loading data in Mongo when spinning
  it up with Docker. Apparently not that
  easy!
bigimg:
  url: docker-mongo-big.png
  origin: Midjourney
  prompt: "loading containers onto a ship"
img:
  url: docker-mongo.png
  desc: "Hours spent... The final solution? More Docker!"
  origin: Midjourney
  prompt: "docker mongo initial data load"
categories: docker
tags: [synology,mongo]
interesting:
  - url: https://hub.docker.com/_/mongo
    desc: "Docker Hub: Mongo (Community Maintained)"
  - url: https://hub.docker.com/r/mongodb/mongodb-community-server
    desc: "Docker Hub: Mongo (MongoDb Company)"
  - git: docker-library/mongo
    desc: "Github repo for Docker Image (Community)"
  - url: https://tutorialsight.com/how-to-install-mongodb-on-your-synology-nas/
    desc: "Step by step Docker+Mongo Tutorial (with screenshots;)"
extras:
  - githubproject: https://github.com/itenium-be/Docker-Mongo
    githubtext: "The docker + mongo examples"
toc:
  title: Docker + Mongo
  icon: icon-mongodb
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_itenium-itenium2-keeponlearning-activity-7231963696757944320-StZp?utm_source=share&utm_medium=member_desktop"
  instagram: "https://www.instagram.com/p/C-7YXiAt0Af/"
  twitter: "https://x.com/itenium_be/status/1826197579496919217"
  facebook: "https://www.facebook.com/share/p/QQDdPbr5xfpqsHyq/?mibextid=oFDknk"
---

How to load some initial data after spinning up a mongo
with Docker.

If you want to follow the examples:

```sh
git clone https://github.com/itenium-be/Docker-Mongo
cd Docker-Mongo
docker compose up -d
```

<!--more-->

# Simple Run

```sh
docker run --name some-mongo \
  -e MONGO_INITDB_ROOT_USERNAME=mongoadmin \
  -e MONGO_INITDB_ROOT_PASSWORD=pwd \
  -p 27017:27017
  -d mongo
```


# Initial Load

Scripts are executed in alphabetical order,
executed on `MONGO_INITDB_DATABASE`.

For Mongo <6 by `mongo` and by `mongosh` for mongo >=6.

See the [Github Docker-Mongo Repository](https://github.com/itenium-be/Docker-Mongo)
for these examples.


## With JavaScript

Example `compose.yaml` file:

```yaml
services:
  mongo-js:
    container_name: mongo-js-load
    image: mongo
    volumes:
      - db-js:/data/db
      - ./load-js:/docker-entrypoint-initdb.d
    ports:
      - "27009:27017"
    environment:
      MONGO_INITDB_DATABASE: jsapp
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USER}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PWD}

volumes:
  db-js: {}
```

The `.env` file:

```ini
MONGO_USER=mongoadmin
MONGO_PWD=pwd
```

The `load-js` directory contains a `load.js` file:

```js
db.celebrities.insertMany([
  { name: "Jon Skeet" },
  { name: "Martin Fowler" },
  { name: "Erich Gamma" }
]);
```

## With Shell

```yaml
services:
  mongo-sh:
    container_name: mongo-sh-load
    image: mongo
    volumes:
      - db-sh:/data/db
      - ./load-sh/scripts:/docker-entrypoint-initdb.d
      - ./load-sh/data:/mongo-seed-jsons
    ports:
      - "27008:27017"
    environment:
      MONGO_INITDB_DATABASE: shapp
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USER}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PWD}

volumes:
  db-sh: {}
```

In `load-sh/data` put your json files to be imported.

You can either already provide the ids in the json
with `"_id": { "$oid": "5bb0b3050581bf10c4bb89c3" }`
or omit the `_id` to have mongo create one for you.

In `load-sh/scripts` you can import the jsons with
a `.sh` file:

```sh
# A json with an []
mongoimport --db $MONGO_INITDB_DATABASE \
  --collection celebrities \
  --jsonArray \
  --file /mongo-seed-jsons/celebrities.json \
  --authenticationDatabase admin \
  --username $MONGO_INITDB_ROOT_USERNAME \
  --password $MONGO_INITDB_ROOT_PASSWORD

# A json with a single {}
mongoimport --db $MONGO_INITDB_DATABASE \
  --collection socials \
  --file /mongo-seed-jsons/socials.json \
  --authenticationDatabase admin \
  --username $MONGO_INITDB_ROOT_USERNAME \
  --password $MONGO_INITDB_ROOT_PASSWORD
```


# Mongo Shell


```sh
docker exec -it mongo-js-load mongosh \
  --username mongoadmin \
  --password pwd \
  --authenticationDatabase admin \
  jsapp
```

In the shell:

```js
db.celebrities.find()

use admin
show collections
exit
```

If you just want to bash into it:

```sh
docker exec -it mongo-js-load sh
```


# mongod.conf

By default mongo will not load any conf file. If you want to work
with a custom [mongod.conf](https://www.mongodb.com/docs/manual/reference/configuration-options/):

```yaml
services:
  mongo-conf:
    container_name: mongo-conf
    image: mongo
    command:
      - '-f'
      - '/etc/mongod.conf'
    volumes:
      - db-conf:/data/db
      - ./mongod.conf:/etc/mongod.conf
    ports:
      - "27007:27007"
    environment:
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USER}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PWD}

volumes:
  db-conf: {}
```

The container port is `27007` because of this
`mongod.conf`:

```yaml
net:
  port: 27007
  bindIp: 127.0.0.1
```

Inside the container check [`/etc/mongod.conf.orig`](https://www.mongodb.com/docs/manual/administration/configuration/#std-label-base-config) for a basic one. Or if you have installed it locally:

| Platform | Install Method     | Configuration File  |
|----------|--------------------|---------------------|
| Linux    | apt, yum or zypper | /etc/mongod.conf
| macOS    | brew               | /usr/local/etc/mongod.conf (Intel processor)
|          |                    | /opt/homebrew/etc/mongod.conf (Apple M1 processors)
| Windows  | MSI                | \<install directory\>\bin\mongod.cfg
{: .table-code}


# Synology

If you run these examples on Synology, chances are they won't work!
You may see the following error:

WARNING: MongoDB 5.0+ requires a CPU with AVX (Advanced Vector Extensions) support, and your current system does not appear to have that!
{: .notice--danger}

Check [here](https://kb.synology.com/en-me/DSM/tutorial/What_kind_of_CPU_does_my_NAS_have) to see if yours has AVX.
Basically all models from the 21+, 22+, 23+ 24+ series.

<!--
see https://jira.mongodb.org/browse/SERVER-54407
see also https://www.mongodb.com/community/forums/t/mongodb-5-0-cpu-intel-g4650-compatibility/116610/2
see also https://github.com/docker-library/mongo/issues/485#issuecomment-891991814
-->

You'll also run against a permissions issue when executing the `js` or `sh` files,
which we'll fix with a custom `Dockerfile`:


```yaml
services:
  mongo-synology:
    container_name: mongo-syno-load
    build:
      context: ./load-syno
      dockerfile: Dockerfile
    volumes:
      - db-syno:/data/db
    ports:
      - "27006:27017"
    environment:
      MONGO_INITDB_DATABASE: synoapp
      MONGO_INITDB_ROOT_USERNAME: ${MONGO_USER}
      MONGO_INITDB_ROOT_PASSWORD: ${MONGO_PWD}

volumes:
  db-syno: {}
```

The `load-syno/Dockerfile` file:

```dockerfile
# Last version without AVX:
FROM mongo:4.4.29

# Copy your sh file and adjust permissions:
COPY import.sh /docker-entrypoint-initdb.d
RUN chmod +x /docker-entrypoint-initdb.d/import.sh

CMD ["mongod"]
```
