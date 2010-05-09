---
title: Projects I have worked on
layout: default
---

Past Projects
-------------

This page details some of the projects that I have worked on in the past.

* [BeeFS][] is a distributed file system that harnesses the free disk space of desktop machines already deployed in a corporation. Like some special-purpose rack-aware file systems, it uses a hybrid architecture that follows a client-server approach for serving metadata and a peer-to-peer one for serving data. This characteristic allows BeeFS to aggregate the spare space of desktop disks to build a single logical volume on top of which a general purpose fully POSIX-compliant file system is implemented. BeeFS is not only more efficient than the state-of-practice that uses a dedicated server approach, but also cheaper and naturally scalable. Experimental results show that, in average, BeeFS outperforms NFS in 74% for write operations and 30% for read operations in the best case. In the worst case, BeeFS results in a 56% improvement in write operations and 20% for read operations when compared with NFS. Moreover, in all cases, BeeFS improves in at least 30% all metadata operations. Reduced ownership cost is achieved by increasing the utilisation of desktop disks, while sudden increases in the demand for storage, normally caused by the arrival on new users, is usually matched by the extra disk space that is available in the machines allocated to the new users.

* GCore is a data-driven game development framework built over [JMonkeyEngine][JME]. It is based on a [componentized architecture][comp-arch] for game objects that is becoming a de-facto standard in modern game engines. I’ve used it as a testbed for my previous research on [dependency injection][dig] in game programming and as a prototyping tool for some of my games.

    GCore is developed in Java using the [Eclipse IDE][eclipse]. The [downloadable][get-gcore] Eclipse project includes full source code and simple demos.

* [Tank Battles][TanksGame] is an effort to build a 3D simulator for teaching robot artificial inteligence. Tank Battles is heavily influenced by IBM's Alphaworks [Robocode][], but differs from the former in that it adds the complexity of a fully 3D scene with realistic physics. Instead of relying on predictable, almost deterministic, movement code, as found in robocode, one has to implement tank behavior by using methods that depends on the physics engine simulation to complete. This aproach makes Tank Battles chalenges closer to real world robots coding.

* JHeat is productive way for developing Web 2.0 business applications with Java. It allows rapid and easy developing of [CRUD][] modules and report generation, but also it's flexible enough to develop complex real life business applications as health care systems, customer relationship, project management, etc. The essence of JHeat is that the developer writes the Model and defines, instead of programming, the user interface, which is generated, while the Controllers are reused.

[gdocsfs]:   http://code.google.com/p/gdocsfs    "Google Docs File System"
[BeeFS]:     http://www.lsd.ufcg.edu.br/beefs    "Beehive File System"
[JME]:       http://www.jmonkeyengine.com/       "JMonkeyEngine"
[TanksGame]: http://tankbattles.sourceforge.net/ "Tank Battles"
[robocode]:  http://robocode.sourceforge.net/    "Robocode"
[eclipse]:   http://www.eclipse.org              "Eclipse.org home"
[get-gcore]: http://www.ic.uff.br/%7Eepassos/gcore.zip "Download GCore" 
[comp-arch]: http://sertao3d.wordpress.com/2008/01/29/data-driven-game-development/
[dig]:       http://sertao3d.wordpress.com/2008/03/30/dependency-injection-in-games
[crud]:      http://en.wikipedia.org/wiki/CRUD_%28acronym%29

