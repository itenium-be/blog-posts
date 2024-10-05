---
layout: post
author: Wouter Van Schandevijl
title: "QA TechEvent: SQL Training"
subTitle: "Gamification - our internal SQL competition"
date: 2024-10-03
desc: >
  Our internal SQL Training was not just a pptx, it was a self-validating game:
  solve SQL exercises and get awarded points when the resultsets match!
bigimg:
  url: sql-training-big.png
  origin: Midjourney
  prompt: "sql training with gamification"
img:
  url: sql-training.png
  desc: "Prize for the winner with banana for scale!"
categories: databases
tags: [sql,product,fun]
interesting:
  - url: https://mystery.knightlab.com/
    desc: "SQL Murder Mystery: Can you find out whodunnit?"
  - git: openfootball/worldcup
    desc: "The free dataset where we got the worldcup data from"
extras:
  - githubproject: https://github.com/itenium-be/sql-training
    githubtext: "The SQL Training game (datasets, docker containers, game & leaderboard)"
toc:
  title: SQL Training
  icon: icon-mssql
socials:
  linkedin: ""
  instagram: ""
  twitter: ""
  facebook: ""
---

This month our QA TechEvent was "SQL Training". Goal was to score most points by solving
more and more complex SQL exercises against three datasets: countries of the world,
teachers + departments (postgres) and the football worldcups (sql server).

Which country scored the most owngoals? Read on to find out!

![Special SQL Skillset Taken meme](/assets/blog-images/sql-training-meme.png "Special SQL Skillset"){: .img-responsive}

<!--more-->

# The Game

The game started with a hickup, other machines were not discoverable on the guest network so the leaderboard server had to be deployed on an external server -- while everyone was already solving exercises! This messed up our scoreboard considerably, especially the ranking for "solved fastest" 😃

After this it was pretty smooth sailing. Tom found an error in the calculation of the query to calculate the average GDP per capita in South America. That and other small issues were fixed on the spot, on production -- as it should! 😉

## Before we start

![Meme SQL is always all caps!](/assets/blog-images/sql-training-meme2.png "It can only be SELECT FROM!!"){: .img-responsive}


## Notable Hacks

Even though we had definitely said "no cheating" during the intro powerpoint, some contestants still tried to get extra points for "leanest solution" or just "fixed" their query to get to the hinted at result 😀



### Mike

Q: _Which countries have a GDP greater than every country in Europe?_

```sql
select name
from countries
where gdp > 3425956000000
-- I think we were looking instead for this:
-- WHERE gdp > (SELECT MAX(gdp) FROM countries WHERE continent='Europe')
order by gdp
```

Straight to jail Mike! ;)


### Alexander

Alexander scored some `+4` style points for this one!

Q: _Select the year of the worldcup in which the most penalties were scored, also select the amount of penalties._

```sql
SELECT top 1 s.name, COUNT(*)+4 AS o
FROM goals g JOIN matches m ON g.match_id = m.id
JOIN groups a ON m.group_id = a.id
JOIN events e ON a.event_id = e.id JOIN seasons s ON e.season_id = s.id
WHERE g.penalty = 1
GROUP BY s.name order by o desc
```

The reason was the join on the `groups` table. This led to penalties scored after the "group phase"
to not be counted in his query!



## The Leaderboards

The 🏆 top 4 on the final leaderboard 💯:

| Rank   | Player     | Points | # Fastest | # Shortest | Total Score |
|:------:|------------|-------:|:---------:|:----------:|------------:|
| 1      | Michael    | 35     | 6         | 8          | 63          |
| 3      | Mikedonna  | 32     | 4         | 2          | 44          |
| 2      | Jonathan   | 34     | 1         | 2          | 40          |
| 4      | Gery       | 30     | 3         | 1          | 38          |
{: .table-code}


## The Prize

In the end Michael proved too strong and with 63 points, he went home with the title of **itenium SQL Master** and the gaming cup 🎈 🌟 🚀!

{% include post/image.html file="sql-training-winner.png" alt="SQL Training prize for the winner" desc="Michael scored most points and went home with the prize! 🎉" maxWidth="500px" %}


# Host Your Own?

You want to host your own SQL Training competition?  
You can! The game, exercises and datasets are on our [Github](https://github.com/itenium-be/sql-training)!

Each contestant runs his own React frontend and databases with Docker:

```sh
git clone https://github.com/itenium-be/sql-training
cd sql-training
docker compose up -d --build
```

The game leader hosts the leaderboard:

```sh
cd sql-leaderboard
cp .env.template .env
docker compose up -d --build
```

You'll have to change some configuration (see the [README](https://github.com/itenium-be/sql-training/blob/master/README.md)) but that's basically it!  
Possible solutions can be found on a separate [branch](https://github.com/itenium-be/sql-training/blob/solutions/SOLUTIONS.md).


# Interesting Queries

## World: largest country per continent

Q: _Find the largest country (by area) in each continent, show the continent, the name and the area._

During the preparation, I solved this exercise with the following query:

```sql
SELECT continent, name, area
FROM countries
WHERE area = (
  SELECT MAX(area)
  FROM countries AS subquery
  WHERE subquery.continent = countries.continent
)
ORDER BY continent
```

Mike was using ChatGPT extensively and by just entering the exercise question, it came up with the following
query:

```sql
SELECT DISTINCT ON (continent), continent, name, area
FROM countries
ORDER BY continent, area DESC
```

A pretty interesting solution which won him the "leanest query" award for this question. I didn't even
know the `DISTINCT ON (col_name)` was a thing!

It's the `ORDER BY area DESC` part that makes this work. Pretty neat!


## World: all vowels, no spaces

Q: _Find the country that has all the vowels and no spaces in its name._

Michael came up with a cool solution for this one:

```sql
select name from countries where name not like ('% %')
and name like all (array['%a%', '%e%', '%i%', '%o%', '%u%'])
```

But I was suprised no one solved this with a good old regex!?
It would most definitely have won the "leanest query" award!

```sql
SELECT name FROM countries
WHERE name ~ '^(?=.*a)(?=.*e)(?=.*i)(?=.*o)(?=.*u)[^ ]+$'
```

## Teachers: Teacher seniority

Typically calculated like:

```sql
EXTRACT(YEAR FROM AGE(NOW(), employed_at)) AS seniority
```

Michael won the leanest query by instead doing:

```sql
(CURRENT_DATE - employed_at)/365
```

## Worldcup: country with most own goals

As promised in the intro...

```sql
SELECT TOP 1 c.name, COUNT(g.id) AS owngoals
FROM goals g
JOIN teams t ON g.team_id = t.id
JOIN countries c ON t.country_id = c.id
WHERE g.owngoal = 1
GROUP BY c.name
ORDER BY owngoals DESC
```

And the "winner" is... France, with 4 own goals!  
(Valladdares, Yobo, Behich, Mandžukic)


## Unsolved

**World-8**: Get confusing top level domains: tld that start/end in a different country name.  
An exercise on joining a table with itself.

**Woldcup-4**: Find players that played for different countries and a "," separated list of countries they played for.  

**Worldcup-5**: Which countries always survived the "Group Stage" when present? (A round not called "Matchday")

**Worldcup-6**: For the player that scored the most goals, list the worldcup.YYYY, the round name, the match date, the goal minute, scored Y/N and the total amount of goals scored so far. To keep things "simple", evaluate only events.id 20, 21 and 22 and team.id 1.


Anyone willing to give them a go? 😀
