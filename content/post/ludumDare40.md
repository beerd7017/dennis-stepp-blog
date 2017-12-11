+++
title = "Postmortem: Ludum Dare 40"
description = "A postmortem analysis of Ludum Dare 40"
date = "2017-12-11"
categories = ['Game Development', 'Design','Productivity', 'Programming']
tags = ['Ludum Dare', 'Game Development', 'Design', 'Productivity', 'Programming']
thumbnail = "img/posts/ld40/spaceBadies.png"
+++

Recently, I participated in [Ludum Dare 49](www.ldjam.com), a global game jam in which thousands of participants come together to pair up or fly solo to create a game from scratch. There are two different modes you can decide from: the *compo* (48 hours, solo) or the *jam* (72 hours, solo/team). For LD 40, I ended up working by myself mostly.

__Weeks Before LD39__

The weeks leading up LD40 the plan was to work with a couple other guys but everyone couldn't get time off work. So the day before LD40 started I decided I'd go it alone.

__Day 1__

The theme this time was "**the more you have, the worse it gets.**" I decided I wanted to do a side-scrolling shoot 'em up and got to work on the player controller. Next I worked on firing projectiles and created a bounding box so that the player couldn't leave the view screen. Next, I created the enemy and decided to have the enemy to chase the player. I wanted to simulate the concept of boids but that seemed a bit out of scope for me in 72 hours (plus get the other stuff done).  

__Day 2__

The plan was to start at 8AM. So at 2PM I logged on and began working on tweaking the player speed, projectile speed and fire rate, and enemy speed. Afterward I worked on the damage and power up system. Then I closed the day with creating the spawning system, which only could spawn one type on enemy per wave in that implementation.

__Day 3__

__Day 4__