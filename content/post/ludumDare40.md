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

I was not feeling well at all on Sunday so my development time was suffering. I hooked in the music and audio effects. Then I created the particle effects for the explosions and projectile hits. I finished up with creating a scoring system and assign point values to enemies.

__Day 4__

I finally fixed the UI on my canvas and printed out the score, hit points, and number of alternative shot left. I still was not feeling well, so I had to take a nap. When I returned I tested the build and did a not of playtesting. I decided with one hour left before submission to refactor my spawning system so that I could spawn multiple enemy times on each wave. Frantically, I wired this up, ran the build, and turned in my submission.

I learned a lot from this experience and had a great time putting this game together. I know some of the systems in this game are painfully coded so I'm planning on returning to some of the systems and making them more abstract and adoptable for other games in the future. In the immediate future I'll be diving into writing some enemy AI functions for another project but it will be interesting to see what I can apply back to this game. My only regret of LD40 is not having a better title for my game ( and being sick for 2 days of it). 

 