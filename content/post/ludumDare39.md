+++
title = "Ludum Dare 39: Running Out of Power"
description = "A postmortem analysis of Ludum Dare 39"
date = "2017-08-01"
categories = ['Game Development', 'Design','Productivity', 'Programming', 'Unibear Studio']
tags = ['Unibear Studio', 'Ludum Dare', 'Game Development', 'Design', 'Productivity', 'Programming']
thumbnail = "img/posts/ld39/fizzle-itchbanner.png"
+++

Yesterday concluded [Ludum Dare 39](www.ldjam.com), a global game jam in which thousands of participants come together to pair up or fly solo to create a game from scratch. There are two different modes you can decide from: the *compo* (48 hours, solo) or the *jam* (72 hours, solo/team). For the 39th Ludum Dare, my good friend Daniel, artist/composer came to stay with me over the weekend (I went to see him on the 38th Ludum Dare). Meanwhile, our lead programmer was staying at his home.

__Weeks Before LD39__

The weeks leading up to the event were torturous for me because of the excitement and anticipation of the announcement of the themes. For every event a theme is announced and the timer starts, but beforehand community members can submit their own themes which are then judged by others in the community. After three rounds of judging, the final round begins. The final round consisted of 16 themes, one of which would be announced the theme of the LD39. If there was one thing that I learn from LD38, it was that preparation was absolutely imperative to the success of the weekend.

__Days Before LD39__

The hardest thing about [Ludum Dare](www.ldjam.com) is finding a good idea for interesting/feasible game mechanics and story. To help this process along I decided that I would look at each of the 16 themes that could end up being the one and create a "mind-map" for each them. [Mind-mapping] (http://www.mindmapping.com/) is an effective technique to have in your arsenal when you exploring the idea of something. I decided I would time-box my approach dedicating 5 minutes to each theme and scribbling down any thoughts or images that came to me during those 5 minutes. By the end of the exercise I had two pages of notes to pull from when the time came to design the game we wanted to make.

My second item of preparation was preparing the workspace, which was the dining room table where Dan and I could both comfortably work. I also cleaned up around the house, doing laundry, dishes, dusting,...etc. so that our surroundings were pristine.

Most importantly was gathering food and drinks for the weekend so that we always had snacks and meals on hand. A big thanks to my girlfriend for preparing most of these meals for us over the weekend!

Now that all the preparations were in order it was time to begin the jam.

__Day 1__

Of course the theme that was chosen was one of the ones I had the least amount of notes and good ideas for. The theme was "**Running Out of Power**." We went back and forth on a few ideas and then we came around full circle to and idea. This wasn't good. It was the same exact pattern of LD38, where we damn near scraped the entire project of day 2 and started over. Luckily, I remembered something that had resonated with me from a video I found on YouTube, where concept artist, **Matt Rhodes** discusses the importance of [knowing the story of a character] (https://www.youtube.com/watch?v=m2BRl1hWnnY&t=2612s) when drawing it. I think it's really the same kind of thing for game design. I think things tend to work better when you first start with the character instead of the setting. So I told Dan, the artist, to shut up and just draw the first thing that came to his mind. The result was this little service robot.
 
 ![fizzle_bot](/img/posts/ld39/fizzlebot.png  "Unit-F155L3")
 
Once we all saw the service robot, a very fuzzy outline of a story started coming to light. This was enough to get us started and work on art assets, the main game play system, and the menu UI began. It was just after 1AM Saturday morning when we all decided to go to bed and get back to work at 8AM.

__Day 2__

I spent most of my morning watching Unity menu tutorials because I'll be the first to admit: I don't know WTF I'm doing in Unity. This project was the second time I had really ever even opened [Unity] (https://unity3d.com/) much less actually use it. Lunch time came around and Jordan wasn't feeling well and had to go lay down, Dan and I decided to go take a break and get some tacos from the new local place that just opened up near my house in Knoxville, TN. Once we got back I pretty much finished the UI layout, after much cursing and screaming because I'd thought I'd be a baller and animate between the menu screen. The animation of the camera was extremely frustrating but I'm glad I went through that pain because it turned out pretty nice at the end. I found myself in the evening kind of just sitting there without anything to do, which on one hand I guess its a good thing because I wasn't still trying to build the menu like last time. I got a build from Jordan and started play testing it into the night. Then it was off to bed again.

__Day 3__

I woke up and decided I was going to start writing a story about what had happened to the power plant and why the robot was in there. Dan woke up from a dream that morning and he told me in that dream he had decided the name of the power company in our game was named "Sunflower." Really for no other reason than it sounded pretty good. I opened up a text pad and the story just dumped right onto the page. I highly doubt I would had been able to come up with anything comparable had it not been for the "sunflower" word. Dan was furiously working on the last of the art assets and beginning to write music. Jordan was wiring up all the game mechanics and I was trying to fix all the grammar and misspellings I did in the dialogue of the game. (Yes, there were plenty of mistakes found in our release build.)

__Day 4__

I pretty much idled all day. Went over the dialogue again. Missed all the mistakes I had made again. I gave my menu scene to Jordan to integrate. "What the fuck, man?" was the response I heard when he opened it up. See he had forced the game to play in 832 x 832 resolution whereas I had mine set to full-screen. So to fix that I had to change the canvas sizes and shuffle my UI elements around about 15 minutes later Jordan had integrated my menu to the rest of the game. There was one other slight issue. I had written my UI scripts in JavaScript, mainly because I wanted to sharpen my JavaScript skills to help me out at work. Jordan however has been writing all his scripts in C#, which is the more common approach in Unity.  In hindsight, using JavaScript to interact with GameObjects is kind of stupid and it just feels wrong. Maybe that's the object oriented programmer in me speaking but damn it just feels wrong. So Jordan was able to convert my JavaScript to C# in about 15 minutes, which I think speaks to both of our skills, Jordan being a excellent programmer in C# and me having writing understandable JavaScript. The rest of the afternoon was spent idle watching all the art and music assets getting loaded in until we got a build of the game and began play-testing. I think we ended up with 3 or 4 build before we got the release candidate, which we then submitted as our entry.

Overall, this was a very enjoyable weekend and I had a lot of fun. I also learned a lot of lessons and was able to used what I experience from the last LD to our advantage for LD39. If you feel so inclined you can check out our game at these links below:

Now on [itch.io] (https://unibear-studio.itch.io/fizzle)!

![fissle_background](/img/posts/ld39/fizzle_background.png "Fizzle Desktop Background")

You can also check out [Unibear Studio](http://unibearstudio.com/)!

![unibear_studio](/img/posts/ld39/unibearstudio.png "Unibear Studio Logo")
