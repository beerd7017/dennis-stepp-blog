+++
title = "Unity: Introduction to the Core UI"
description = "Introduction to the Core UI of the Unity game engine."
date = "2017-10-18"
categories = ['Programming', 'Game Development']
tags = ['Unity', '2D', 'C#']
thumbnail = "img/posts/helloUnity/unity_thumbnail.png"
+++

You could say I'm pretty green when it comes to knowing my way around the [Unity game engine](https://unity3d.com/). Come with me on a journey through some core UI elements and a few silly examples, some of which may actually be practical in your next Unity project.

# Update Unity Preference

I'm going to do you a real solid. Trust me, you'll want to update the `Playmode tint` to a really obnoxious color like `#E70C6EFF` so that you know for a fact when you are in `Play` mode. Props to [@DJFariel](https://twitter.com/DJFariel) for that tip.   

![1_preferences](/img/posts/helloUnity/1_preferences.png  "Preferences")

This is something that drove me __batshit crazy__!

![nickcage](/img/posts/helloUnity/nickcage.gif "Nick Cage")

# The Obligatory: `Hello World`

For this simple exercise we are going to use these three Core UI components:

* __Visual UI__: These are elements like __Button__, __Image__, __Text__, __Toggle__, etc.
* __Rect Transform__: __Rect Transforms__ are reference points for anchoring, scaling, resizing, and rotation.
* __Canvas__: UI elements are children of a __canvas__. You can have multiple __canvases__ in a single game scene. If you have no __canvas__ but add some other UI GameObject, one will be automatically created and your UI GameObject will become a child of it.

Canvases have three different rendering modes:

* __World Space__: The __canvas__ is a flat plane in the __Camera__ scene, but the plane is relative to the __camera's__ orientation.
* __Screen Space - Camera__: The __canvas__ is a flat plane and is always facing the __camera__. 
* __Screen Space - Overlay__: UI elements are displayed without reference to a __camera__.  

Since I'm three beers into this post already, I don't really want to think in a 3-Dimensional space. So let's render the canvas using __Screen Space - Overlay__ mode.

In a new 2D project, let's Add a `Text` object to our scene. `GameObject | UI | Text` and let's name it `_textHello`.

![2_hierarchry](/img/posts/helloUnity/2_hierarchry.png  "Hierarchry")

In the `Inspector` perspective, let's set the properties of `_textHello`:

* __Text__ = `Hello World`
* __Font__ = `Arial`
* __Font size__ = `35`
* __Alignment__ = `horizontal` and `vertical center`
* __Horizontal__ and __Vertical Overflow__ = `Overflow`
* __Color__ = Your choice.

![3_inspector](/img/posts/helloUnity/3_inspector.png  "Inspector")

Now let's run the game and take a look at what we have done so far (notice the obnoxious pink color that overlays the Unity UI around the game scene).

![4_helloworld](/img/posts/helloUnity/4_helloworld.png  "Hello World")

# Anybody Know What Time It Is?

Let's build a digital clock. Add a `Text` objects to our scene. `GameObject | UI | Text` and let's name it `_textClock`.

![5_hierarchry](/img/posts/helloUnity/5_hierarchry.png  "Hierarchry")

In the `Inspector` perspective, let's set the properties of `_textClock`:

* __Text__: `time will go here`
* __Font__ = `Arial`
* __Font size__ = `35`
* __Alignment__ = `horizontal` and `vertical center`
* __Horizontal__ and __Vertical Overflow__ = `Overflow`
* __Color__ = Your choice.

Also, we need to set the __Rect Transform__ anchor to middle/center and the `Pos Y` position to `-60`.

![6_inspector](/img/posts/helloUnity/6_inspector.png  "Inspector")

We want the text of our clock to tell us the current time. To do this, we are going to write a C# script. Create a folder named `Scripts` create a new C# script called `DigitalClock`.

![7_project](/img/posts/helloUnity/7_project.png  "Project")

DigitalClock.cs
    
    using System;
    using UnityEngine;
    using UnityEngine.UI;

    public class DigitalClock : MonoBehaviour
    {
	    private Text _clockText;
	
	    void Start ()
    	{
		    _clockText = GetComponent<Text>();
	    }
	
	    void Update () {
		    DateTime time = DateTime.Now;
		    string hour = LeadingZero(time.Hour);
		    string minute = LeadingZero(time.Minute);
	    	string second = LeadingZero(time.Second);
    
	    	_clockText.text = hour + ":" + minute + ":" + second;
    	}

	    string LeadingZero(int timeUnit)
	    {
		    return timeUnit.ToString().PadLeft(2, '0');
	    }
    }

Now we'll drag our `DigitalClock` script to our `_textClock` GameObject. 

![8_drag](/img/posts/helloUnity/8_drag.gif "Dragging")

When you run the game, you can watch the clock update on the screen.

![9_time](/img/posts/helloUnity/9_time.gif "Gameplay")

That's pretty much a wrap on this basic tutorial there will be more to come on this later. I just got one question: 

# Do You Know What Time It Is?

![tooltime](/img/posts/helloUnity/tooltime.gif "Tool Time")