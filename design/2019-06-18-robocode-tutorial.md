---
layout: post
author: Wouter Van Schandevijl
title:  "Robocode Tutorial"
date:   2019-06-18 01:00:00
desc: >
  Robocode tutorials for the itenium roborumble™
bigimg:
  url: robocode-big.png
img:
  url: robocode-pew-pew.png
categories: design
tags: [fun]
series: robocode
extras:
  - githubproject: https://github.com/itenium-be/itenium-be.github.io
    githubtext: "Example Eclipse and Visual Studio Robocode projects"
interesting:
  - url: https://robocode.sourceforge.io/
    desc: "Official Robocode site"
  - url: http://robowiki.net/wiki/Robocode
    desc: "Robocode Wiki"
toc:
  title: 🎇 Tutorial
package-versions:
  - package: robocode
    version: 1.9.3.3
redirect_from: /blog/productivity/robocode-tutorial/
---

{% include github-stars.html url="robo-code/robocode" desc="Build the best - destroy the rest!" %}

> Robocode is a **programming game**, where the goal is to develop a robot battle tank to battle against
> other tanks in Java or .NET. The robot battles are running in real-time and on-screen.


<!--more-->

# Game Physics

![BattleFieldWidth and BattleFieldHeight](/assets/blog-images/robocode-battleground.gif "BattleFieldWidth and BattleFieldHeight"){: .img-responsive}{: style="max-width: 500px"}


- Default grid for 1v1: 800x600
- Time is measured in ticks = turns.
- 1 distance unit = 1 pixel unless the game scaled down the battlefield to fit on the screen.
- Acceleration (a): Robots accelerate at 1px/turn and decelerate at 2px/turn.
- Velocity (v): Max: 8px/turn. The robot movies in the direction of the `GunHeading`.
- Distance (d): distance = velocity * time.

[Wiki](http://robowiki.net/wiki/Robocode/Game_Physics)


# The Robot

A Robot consists of:

## Body

- `Energy`: 
    - Start with 100.
    - Drops when firing, getting hit, running into robots or walls and being inactive
    - At 0, it is game over! 
- `Ahead(double distance)` & `Back`: Distance in pixels.
    - Collision with another robot: 0.6 energy damage
    - Collision with a wall `abs(velocity) * 0.5 - 1 (never < 0)` (AdvancedRobot only)
- `Velocity`: Measured in pixels/turn. Max: 8px/turn. Accelerate: 1px/turn. Decelerate: 2px/turn.
- `TurnLeft(double degrees)` & `TurnRight`
    - Maximum rate of rotation is `(10 - 0.75 * abs(velocity)) deg/turn`.
    - The faster you're moving, the slower you turn.
    - `Heading`: Direction the tank is facing. Returns 0 to 360 (exclusive). 0 is North. 90 is East etc.
- `Width` & `Height`: Dimensions of the Robot. (36x36)


## Gun

Pew! Pew! Pew!!

- Mounted on the Body.
- `GunHeat`:
    - Can only `Fire` when this is zero.
    - Each turn the Gun cools with `GunCoolingRate`. Default: 0.1 / turn.
    - At game start `GunHeat` is 3.
- `Fire` & `FireBullet(double power): Bullet`:
    - With `power` between 0.1 and 3.
    - Costs `power` amount of `energy`. If power > energy, you become disabled. 
    - Damage: `(4 * power)`. If power > 1, an additional `2 * (power - 1)` damage.
    - If hit, you regain `(3 * power)` worth of `energy`.  
    - `GunHeat` increases with `1 + (power / 5)`.
    - Generates `onBullHit`, `onBulletHitBullet` and `onBulletMissed` events.
    - Bullets move with a velocity of `20 - (3 * power)`
- `turnGunLeft` and `turnGunRight`.
    - The maximum rate of rotation is: 20 deg/turn.
    - `GunHeading`: Direction the gun is facing. Returns 0 to 360 (exclusive). 0 is North. 90 is East etc.
    - `IsAdjustGunForRobotTurn`: True = Guns turns separately of Body.



## Radar

Scans for other robots.

- Mounted on the Gun.
- `turnRadarLeft(double degrees)` and `turnRadarRight`.
    - The maximum rate of rotation is: 45 deg/turn
- Generates `onScannedRobot` events when another robot is detected.
- `RadarHeading`: Direction the radar is facing.
- `IsAdjustRadarForGunTurn`: True = Radar turns separately of Gun.
- `IsAdjustRadarForRobotTurn`: True = Radar turns separately of Body.



# Battling other robots

The best way to debug your robot is, of course battling others!

- [Sample Bots](http://robowiki.net/wiki/Sample_Bots): Packaged with Robocode. Practice against these first :)
- [Super Sample Bots](http://robowiki.net/wiki/Category:Super_Sample_Bots): The next step.
- [How to download other Robots](http://robowiki.net/wiki/Robocode/Learning_from_Robots)
- [Some Super Robots](http://robowiki.net/wiki/Robocode/Downloading_Robots#Robots_to_download)
- [Open Source Robots](http://www.robowiki.net/wiki/Category:Open_Source_Bots)


# Learning Resources

#### Learning to

- [Move](http://robowiki.net/wiki/Movement)
- [Use the radar](http://robowiki.net/wiki/Radar)
- [Targeting](http://robowiki.net/wiki/Targeting)
- [More tutorials](http://robowiki.net/wiki/Tutorials)


#### Other resources

- [Robocode articles](http://robowiki.net/wiki/Robocode/Articles)
- [Graphical Debugging](http://robowiki.net/wiki/Robocode/Graphical_Debugging)


#### Youtube

- [Programming Games: Robocode // Patrick Cupka](https://www.youtube.com/watch?v=oTjjkXebCAY)
- [Introduction to Robocode // Philip Johnson](https://www.youtube.com/watch?v=wJzJ57ZC1sc)
- [Walk-through of testing code for robocode // Philip Johnson](https://www.youtube.com/watch?v=dHrupYhq7sI)
