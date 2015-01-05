---
layout: post
title:  "Dark Destiny RTS Game Project"
date:   2014-03-30 01:08
categories: Project

---

This is a game developed in Java within 10 weeks with 3 other teammates: Han Jiang, Issac Lam and Hei Yin Wong.

* Game name: Dark Destiny
* Game type: Real Time Strategy game
* Game theme: zombie-themed survival game
* Language: Basic Java, without any additional library or game engine
* Development team: 4 people
* Development time: 10 weeks

[![ScreenShot] (https://cloud.githubusercontent.com/assets/3908463/5158073/b11b8072-7328-11e4-9233-5c2fd8efcee3.gif)](http://youtu.be/1po4sz6yDoU.)
[![ScreenShot] (https://cloud.githubusercontent.com/assets/3908463/5158032/de1bacac-7326-11e4-8b84-60f7d24145f7.gif)](http://youtu.be/1po4sz6yDoU.)


##System requirement
1. JRE1.6_65 (**IMPORTANT**) as in other JRE there would be inconsistent functions
2. 1 GB free memory for JVM Mac 10.9 Mavericks 10.9.0 or above


##How to run
1. Change the JRE environment to Java 1.6_65 (**IMPORTANT**)
2. Allocate at least 1 GB memory to your JVM (i.e.: Add "-Xms1024m -Xmx1024m" to your JVM argument)
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5157979/860e1a56-7324-11e4-9160-ebefef13ad25.png "configuration")
3. Click Run in your IDE, and choose the main class as com.darkdensity.main.Main

##Feature
* Control the sprites and run away from the zombie as easily as a single click
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158176/deb42a54-732b-11e4-975e-8714cc95684a.png "basic scene")

* Scrout the building for *food*, *other survivors* and *wood* to maintain health
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158143/f4d29650-732a-11e4-9bc2-442173c9b67e.png "scout building")

* Unite with other survivors
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158151/2a2a8d9e-732b-11e4-85b4-f95c293517cc.png "other survivors")

* Add Barricade (**gained from the scout**) to block the zombie
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158069/8a7cee88-7328-11e4-89a7-dbbd0a6f5270.png "block the zombie")

##User Interface
* **Construction Icon**:To create barricades.
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158423/4b337f9c-7334-11e4-948c-b8d68682f024.png "Construction Icon")

* **Selection Sprite/Building Panel**:The sprite or building being selected.

![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158461/78eef49c-7335-11e4-8356-499698258a28.png "Selection Sprite/Building Panel 1")   ![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158458/78e698d8-7335-11e4-8ce3-f34be1c120f8.png "Selection Sprite/Building Panel 2")

* **Time and Resources Indicator Panel**:The remaining time needed to survive in order to win. 
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158459/78e7eddc-7335-11e4-8f0e-8a95d2c105c8.png "Time and Resources Indicator Panel")

* **Minimap**:A minimap, the yellow points on it are the zombies, and the blue points are the survivors
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158460/78e8a448-7335-11e4-838b-4ac725116302.png "Minimap")

##Game Control
After entering the game world, the following controls would be:
* **Select the Sprite/Building**: **Click** the sprite, or use your mouse to **drag a square** to enclose the sprites within the dragging square. If the selection is successful, the sprite would surrounded by yellow glows, and the status bar on the bottom of the user interface would show the sprite, or the first sprite being selected (if multiple sprites are selected).
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158461/78eef49c-7335-11e4-8356-499698258a28.png "Selection Sprite/Building Panel 1") 

* **Control the Sprite**: After selection of the sprite, **right click** on the game world, the sprite would find them the way there (implemeted by efficient path-finding algorithm).

* **Add a barricade**: **Click** the construction icon ![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158423/4b337f9c-7334-11e4-948c-b8d68682f024.png "construction icon")on the left of your User Interface, a barricade would appear in your mouse, simply add it to the place available. To see if a place is available for adding a barricade, just see the indicating color of the barricade.

* **Scout a building**: **Select** a sprite and **right click** a building.
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158458/78e698d8-7335-11e4-8ce3-f34be1c120f8.png "Selection Sprite/Building Panel 2")


##Efficient Path-Finding Algorithm
Based on A* Algorithm, but we have some improvement on the performance:
* Insead of finding every tiles (in our game, one tile is 16px *16px) to the destination, we first found the crossroads we need to go to the destination.
* We smoothed the path where we don't neccessarily go to thoses crossing points, see the path on the testing.
* When there was some changed on the path, e.g. a barricade is added to the path, the path would be changed accordingly. There is a path manager to manage all the pahts, and it listens to the change of the path all the time.

**Testing Output**

![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158058/4de779c0-7328-11e4-97c2-09c62f68bb3b.png "A* test 1")
![Alt text](https://cloud.githubusercontent.com/assets/3908463/5158059/51829ef2-7328-11e4-8400-88758e6766f1.png "A* test 2")



##Demo Video
Check out full one-minute game playing video on YouTube (http://youtu.be/1po4sz6yDoU) or Youku in China (http://v.youku.com/v_show/id_XODMzNDU2NDA0.html) check out this project on GitHub(https://github.com/SeffyVon/DarkDestiny)!

[![ScreenShot] (https://cloud.githubusercontent.com/assets/3908463/5158073/b11b8072-7328-11e4-9233-5c2fd8efcee3.gif)](http://youtu.be/1po4sz6yDoU.)
[![ScreenShot] (https://cloud.githubusercontent.com/assets/3908463/5158032/de1bacac-7326-11e4-8b84-60f7d24145f7.gif)](http://youtu.be/1po4sz6yDoU.)


Thank you!

