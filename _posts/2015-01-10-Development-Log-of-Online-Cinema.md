---
layout: post
title:  "Development Dialog of Online Cinema"
date:   2015-01-10 11:49
categories: digest
---

多设备电影同步观看系统项目开发日志。

*  [JavaFX客户端](https://github.com/SeffyVon/FYP_JavaFX_Client)

*  [PHP Server](https://github.com/SeffyVon/FYP_PHP_Server)

####Things done in 2014####
* The user interface has been built 70%, with JavaFX.
* The basic query was done on PHP server.

####Jan 8, 2015####
* Changed live chat from probing to websocket. 

####Jan 9, 2015####
* Moved user and group file into global static variables, which provides easy access from different controllers.
* Add the page for adding new movie.
* Read [REST vs WebSocket](http://blog.arungupta.me/rest-vs-websocket-comparison-benchmarks/)

####Jan 10, 2015####
* Recently I have been looking at the authentication and security side of my PHP server, especially for integrating with the Digital Right Management (DRM) system. I adopted the Symphony framework, for `class autoloading`, `front controller routing` and `authentication`.

####To do list####
* Framework to integrate Websocket and REST request. Change to Json.
* Authentication.
* DRM.
* Client: message management System.
* Client: File download request management.


