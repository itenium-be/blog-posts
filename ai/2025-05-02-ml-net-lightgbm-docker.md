---
layout: post
author: Van Schandevijl Wouter
title: "Training with ML.NET and LightGBM on Synology with Docker"
subTitle: "It just worked on Windows 😀"
date: 2025-05-02
categories: docker
tags: [war-story,net,synology]
bigimg:
  url: ml-net-lightgbm-big.png
  desc: "By Midjourney"
  origin: Midjourney
  prompt: "A developer surrounded by glowing code and terminal windows, working late night inside a sleek futuristic server room, Docker whales floating as holograms, AI models visualized as flowing data streams, mechanical arms building software, clean lighting, high-tech ambiance, digital chaos yet calm cinematic lighting, ultra-detailed, concept art"
img:
  url: ml-net-lightgbm.png
  desc: ""
  origin: Midjourney
  title: "By Midjourney"
  prompt: "A heroic Linux penguin wielding a keyboard sword, battling giant digital dragons labeled 'lib_lightgbm.so' and 'libgomp.so.1', inside a dark futuristic server cave, glowing error messages flying through the air, Docker container shields, epic fantasy-tech crossover, humorous tone, digital fantasy art, high detail, vibrant lighting"
desc: >
  "lib_lightgbm" is almost 1GIG so it does make sense
  to not install it by default...
interesting:
  - url: "https://github.com/dotnet/machinelearning/issues/7454"
    desc: "Issue: LightGbm in Linux Container"
extras:
  - githubproject: https://github.com/TTC-AAlst/PongRank
    githubtext: "Predicting next year player rankings"
toc:
  icon: icon-shell
  title: LightGBM Installation
socials:
  linkedin: "https://www.linkedin.com/posts/itenium_dotnet-machinelearning-dockerstruggles-activity-7351913817586425857-ke2j"
  twitter: "https://x.com/itenium_be/status/1943611265777021175"
  instagram: "https://www.instagram.com/p/DMPrjwkuOoy/"
  facebook: "https://www.facebook.com/share/p/1BEoHvQYHw/"
---

{% include github-stars.html url="dotnet/machinelearning" desc="ML.NET is an open source and cross-platform machine learning framework for .NET." %}
{% include github-stars.html url="microsoft/LightGBM" desc="A fast, distributed, high performance gradient boosting (GBT, GBDT, GBRT, GBM or MART) framework based on decision tree algorithms, used for ranking, classification and many other machine learning tasks." %}

After writing some code to fetch and mold the data, I was ready to train
"my" AI, dubbed "PongRank" which was easy enough and all was good until
I added a small WebApi to automate future syncing, retraining and predictions.

<!--more-->

# Docker Training

As, once deployed, training failed with:

**System.DllNotFoundException**: Unable to load shared library 'lib_lightgbm' or one of its dependencies. In order to help diagnose loading problems, consider using a tool like strace. If you're using glibc, consider setting the LD_DEBUG environment variable:  
libgomp.so.1: cannot open shared object file: No such file or directory  
/app/lib_lightgbm.so: cannot open shared object file: No such file or directory
{: .notice--danger}

It's a conscious decision by the team to not include LightGBM by default,
and that makes perfect since it is a 1gig dependency which is only
used when using that trainer.

# Installing LightGBM

A simple Google search got me to the LightGBM installation instructions:

```sh
git clone --recursive https://github.com/microsoft/LightGBM.git
cd LightGBM && mkdir build && cd build
cmake .. && make -j && make install
```

## CMake Detour

The `apt-get install cmake -y` installed version 3.25.1 which was just not
good enough for LightGBM!

CMake 3.28 or higher is required.  You are running version 3.25.1
{: .notice--danger}

And now it was really starting to show that I'm very far from a Linux
installation/compilation hero. After trying to build CMake for a few
hours, I ended up installing `pip` to install CMake with.

```sh
apt-get install python3-pip -y
pip install cmake --upgrade --break-system-packages

# Check version:
cmake --version
```

The `--break-system-packages` obviously isn't a very good sign but since
we will be doing this in the build step of our Dockerfile, it's fine 😅

# Final Dockerfile

```sh
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

RUN apt-get update
RUN apt-get install git clang-3.9 libunwind8 curl libomp-dev -y

RUN apt-get install python3-pip -y
RUN pip install cmake --upgrade --break-system-packages

RUN git clone --recursive https://github.com/microsoft/LightGBM.git
RUN cd LightGBM && mkdir build && cd build && cmake .. && make -j && make install

# Usual steps for building the .NET app
# dotnet restore/publish etc

FROM mcr.microsoft.com/dotnet/aspnet:8.0 as runtime

COPY --from=build /app /app
COPY --from=build /usr/local/lib/lib_lightgbm.so /usr/local/lib/lib_lightgbm.so
COPY --from=build /usr/lib/x86_64-linux-gnu/libgomp.so.1 /usr/lib/x86_64-linux-gnu/libgomp.so.1

ENTRYPOINT [ "dotnet", "/app/WebApi.dll" ]
```

I figured out which files needed to be copied with the following command:

```sh
# Enter running container
# (with lightgbm installed)
docker exec -it lightgbm bash

# Find location
find / -name 'lib_lightgbm.so' 2>/dev/null

# List dynamic dependencies
ldd /usr/local/lib/lib_lightgbm.so
```

And there we have it, a WebApi that creates the models!
