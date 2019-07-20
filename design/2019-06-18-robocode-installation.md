---
layout: post
author: Wouter Van Schandevijl
title:  "Robocode Installation & Debugging"
date:   2019-06-18 00:00:00
desc: >
  Robocode tutorials for the itenium roborumble™
bigimg:
  url: robocode-big.png
img:
  url: robocode.png
  desc: "Photo by Ray Rui"
  origin: https://unsplash.com/photos/SyzQ5aByJnE
categories: design
tags: [fun]
series: robocode
extras:
  - githubproject: https://github.com/itenium-be/Robocode
    githubtext: "Example Eclipse and Visual Studio Robocode projects"
interesting:
  - url: https://robocode.sourceforge.io/
    desc: "Official Robocode site"
  - url: http://robowiki.net/wiki/Robocode
    desc: "Robocode Wiki"
toc:
  title: 💣 Installation
package-versions:
  robocode: 1.9.3.3
redirect_from: /blog/productivity/robocode-installation/
---

{% include github-stars.html url="robo-code/robocode" desc="Build the best - destroy the rest!" %}

> Robocode is a **programming game**, where the goal is to develop a robot battle tank to battle against
> other tanks in Java or .NET. The robot battles are running in real-time and on-screen.


<!--more-->

# Install Robocode

Prerequisites:  
- [Java JDK 6+](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [robocode-1.9.3.3-setup.jar](https://sourceforge.net/projects/robocode/files/robocode/1.9.3.3/)

Or follow the official [step by step instructions](http://robowiki.net/wiki/Robocode/System_Requirements).


If you get a _"'java' is not recognized as an internal or external command"_, then
[add your JDK to PATH](http://robowiki.net/wiki/Robocode/System_Requirements#Setting_environmental_variables_on_Windows).



# Building a Robot

## In the Robocode UI

Robot > Source Editor (Control + E) > New Robot (Control + N)

This will generate something like:
```java
public class MyRobot extends Robot
{
    public void run() {
        setColors(Color.red, Color.blue, Color.green); // body, gun, radar

        while(true) {
            ahead(100); // pixels
            turnGunRight(360); // degrees
            back(100);
            turnGunRight(360);
        }
    }

    public void onScannedRobot(ScannedRobotEvent e) {
        fire(1); // Strength between 0.1 and 3.0
    }

    public void onHitByBullet(HitByBulletEvent e) {
        back(10);
    }
    
    public void onHitWall(HitWallEvent e) {
        back(20);
    }   
}
```

Extend either `Robot` or `AdvancedRobot`. An AdvancedRobot:  
- Loses energy when bumping into walls. 
- Can have non-blocking calls (`setXXX() methods`), custom events and FileSystem access.

When you are ready for battle, use: Compiler > Compile (Control + B) in the Source Editor.


## In Eclipse

New Project:  
- Libraries > Add External JARs...: Add `libs/robocode.jar`.
    - External annotations > Edit: Javadoc is bundled with Robocode.
- Do not create a `module-info.java`.
- Add a new Java Class with Superclass: `robocode.Robot`.

Be sure your final robot is [packaged](http://robowiki.net/wiki/Robocode/Package_Robot)!
A `*` appears ingame next to a robot's name if it is a development version.


#### Robocode UI

Add the project's `bin` folder in the Robocode UI:  
Options > Preferences > Development Options > Add  


### Debugging

Run as > Run configurations > Java Application

- Main class: `robocode.Robocode`
- Arguments > VM arguments: `-Xmx512M -Dsun.io.useCanonCaches=false -Ddebug=true`
- Arguments > Working directory > Other > Robocode install path (ex: c:\robocode)

Do not point to a Robocode directory where the .NET plug-in is also installed!



## In Visual Studio

Download the [.NET plug-in for Robocode](https://sourceforge.net/projects/robocode/files/robocode/1.9.3.3/) 
and **install in the same directory as Robocode itself**!

Create a .NET Framework project and reference `libs\robocode.dll`.
See [`dotnet-bots`](https://github.com/itenium-be/Robocode) for a minimal example. 


#### Robocode UI

Add the `bin\Debug` folder in the Robocode UI:  
Options > Preferences > Development Options > Add  

[Wiki](http://robowiki.net/wiki/Robocode/.NET/Create_a_.NET_robot_with_Visual_Studio)


### Debugging

Project > Properties > Debug:  
- Start external program: `C:\Program Files\Java\jdk1.8.0_211\bin\java.exe`
- Command line arguments: 
```
-Ddebug=true -Xmx512M -cp libs/robocode.jar;libs/jni4net.j-0.8.7.0.jar -XX:+IgnoreUnrecognizedVMOptions "--add-opens=java.base/sun.net.www.protocol.jar=ALL-UNNAMED" "--add-opens=java.base/java.lang.reflect=ALL-UNNAMED" "--add-opens=java.desktop/sun.awt=ALL-UNNAMED" robocode.Robocode
```
- Working directory: Where installed (ex: c:\robocode)

Be sure your final robot is a `Release` build!

[Wiki](http://robowiki.net/wiki/Robocode/.NET/Debug_a_.NET_robot_in_Visual_Studio)



# More Debugging

Start Robocode UI in debug mode:  
`java -Ddebug=true -Xmx512M ...`  
Find the CLI arguments in `robocode.bat`.


#### Robot Console

See `out.println()` (or `Console.WriteLine()`) in your robot log by clicking on your robot name in the right top battle
view pane in the UI. SYSTEM events and crash stack traces will also be printed in the robot console.
