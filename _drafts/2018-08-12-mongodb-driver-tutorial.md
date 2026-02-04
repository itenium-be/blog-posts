---
layout: post
author: Wouter Van Schandevijl
title:  MongoDB.Driver tutorial
date:   2099-08-12 20:00:00 +0200
categories: dotnet
desc: >
    MongoDb tutorial
img:
  url: mongo-likes-candy.png
  desc: Mongo likes candy from Blazing Saddles (1974, Mel Brooks), a satirical western.
tags: [tutorial,database]
extras:
  - githubproject: https://github.com/itenium-be/MongoDBTutorial
    githubtext: The accompanying github project contains all code as UnitTests
toc:
    title: MongoDb Tutorial
    icon: icon-mongodb
---


Separate articles for this stuff..

Also an article:
- initial load to setup users
- use secrets instead of environment variables: https://docs.docker.com/compose/use-secrets/



# UI

Desktop Apps: Compass, Robo 3T, Studio 3T

Or via Docker
- Adminer
- Mongoclient
- https://hub.docker.com/_/mongo-express / https://github.com/mongo-express/mongo-express-docker
- https://www.mongodb.com/products/tools/vs-code

--> other?





# Backup

```sh
docker exec some-mongo sh -c 'exec mongodump -d <database_name> --archive' > /volum1/backups/all-collections.archive
```

--> and then also reload!


vs mongoimport / mongoexport ?






<!--more-->

Start a MongoDB  
```

```

Install  
```
Install-Package MongoDB.Driver
```


# Connection


For more examples - as UnitTests - check the [Github source][github-source].

[github-source]: https://github.com/itenium-be/MongoDBTutorial
[mongo-like-candy]: https://www.youtube.com/watch?v=zo7hUa76Ukc
[blazing-saddles]: https://www.imdb.com/title/tt0071230/?ref_=nv_sr_1
