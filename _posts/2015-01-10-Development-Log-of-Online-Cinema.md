---
layout: post
title:  "Development Diary of Online Cinema"
date:   2015-01-10 11:49
categories: digest
tags: ["JavaFX","PHP","Cryptography"]
---

多设备电影同步观看系统项目开发日志

*  [JavaFX客户端](https://github.com/SeffyVon/FYP_JavaFX_Client)

*  [PHP Server](https://github.com/SeffyVon/FYP_PHP_Server)

** **

####To do list####

* Framework to integrate Websocket and REST request. Change to Json.
* Authentication.
* DRM.
* Client: message management System.
* Client: File download request management.

** **

####Jan 16, 2015
* Changed the view in the current cinema controller.
* Used Ratchet WebSocket for Download Request, Sync, Chat, Status Changes. Passed JSON between servers and clients.
* Added view for addding new movie.
* Used Config.Profile class to store current user and current group.
* Used Config.Interface class to store some global interface.

####Jan 12, 2015
* Read [JavaFX vs HTML 5](http://www.slideshare.net/rcuprak/javafx-versus-html5-javaone-2014?related=1)
* Started with Laravel

####Jan 11, 2015####

**Set up of Laverel:**

1. Added php_mcrypt.so and php_pdo_pgsql.so extension to php.ini. 
2. Migration of the sample database. 
3. Routing of the sample website.

**Deployment of a sample Laverel.**

**Read primitive of DRM algorithms:**

1. **Broadcast Encryption**: [Shamir's Secret Sharing](http://en.wikipedia.org/wiki/Shamir%27s_Secret_Sharing) k out of n. Any k + 1 participants can then combine their shares to recover s via interpolation. each authorized recipient deduces the secret, while all others deduce nonsense. `Can I use the server as the very vital K?`

2. **Traitor Tracing**：Each user holds a decryption key that is fingerprinted and bound to his identity. In the case of a leakage, a specialized traitor tracing algorithm ensures that the identities of the leaking entities (the traitors) can be revealed.
* Tracing Based on `Leaked Decryption Keys`: Boneh and Franklin[17] Boneh and Shaw[22]
* Tracing Based on `Leaked Decrypted Content`: watermarking techniques. First, personalized keys are distributed to the users via any broadcast encryption scheme; then, each variant is encrypted under its own key.
* These tracing methods are suitable for subscription. But my goal is to maximise the leaking cost of the content after decryption.

3. **public-key or symmetric-key based**:

** **

####Jan 10, 2015####
* Recently I have been looking at the authentication and security side of my PHP server, especially for integrating with the Digital Right Management (DRM) system. I adopted the Laravel framework, for `class autoloading`, `front controller routing` and `authentication`.

** **

####Jan 9, 2015####
* Moved user and group file into global static variables, which provides easy access from different controllers.
* Add the page for adding new movie.
* Read [REST vs WebSocket](http://blog.arungupta.me/rest-vs-websocket-comparison-benchmarks/)

** **

####Jan 8, 2015####
* Changed live chat from probing to websocket. 

** **

####Things done in 2014####
* The user interface has been built 70%, with JavaFX.
* The basic query was done on PHP server.

