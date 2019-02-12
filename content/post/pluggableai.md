+++
title = "Designing Pluggable AI for Enemies with Unity"
description = "State controllers in Unity can quickly get out of hand if you throw the kitchen sink into them. Taking the time to break apart your state machine and crafting pluggable components will make your system simple, modular, testable, flexible, clean, and composable."
date = "2019-02-11"
categories = ['Programming', 'Game Development']
tags = ['Unity', '2D', 'C Sharp']
thumbnail = "img/posts/pluggableai/pluggableai.png"
+++

State controllers in Unity can quickly get out of hand if you throw the kitchen sink into them. Taking the time to break apart your state machine and crafting pluggable components will make your system testable, flexible, clean, and composable.

## Just Put It In Update

_Hint: Don't do this!_

Defining behaviors for entities in Unity is typically done with a `StateController` that implements Unity's `MonoBehavior` class. One approach to controlling a enemy behavior is by defining when to move, how to move, when to shoot, how to shoot, etc. all together in your `update()` method. As you can probably imagine this approach while maybe not to challenging to implement, it will be very difficult to maintain and understand.

//TODO: Add a code example here.

Yuck. _Maybe_ you can get by with this in a game jam or some small project but in a large scale project you will feel pain.

## Refactor You Mess

_Hint: Best results occur before Mom has to tell you twice!_

A better alternative would be to spilt up behaviors into separate coroutines which are `IEnumerators` running within the game loop. You will have a much easier time understanding and maintaining these smaller methods especially when you organize them into some logical order.

//TODO: Add a code example here. 

Better. We have some separation now and it's more clear of what's happening with our state controller. _Still_, this is a lot of code that we have to consider and finding your issue or tweaking a behavior can be more tedious than it has to be. Plus, what if we have a several enemies that exhibit some of the same behaviors? It sure would be nice to not rewrite or copy/paste that code all over and balloon up our code base.

## Plug And Chug

_Hint: Beer and code work well together when they are modular events. Ask me how I know!_

The best solution involves a pattern in which you can use pluggable components to turn your state controller into simple building blocks that are simple, modular, testable, flexible, clean, and composable.

To do this let's think about the core of what components make an enemy. We are going to think about an enemy in 5 different contexts, **Data**, **Actions**,  **States**, **Decisions**, and **Transitions**.

## Data

To keep things clean we will hold all the properties of a enemies in a straight up data class called `EnemyStats`. These are all descriptive properties of what the enemy is composed of. Things like `health` and `speed`.

## Actions

Actions are essentially the "verbs" of the enemy. An enemy can `Patrol`, `Shoot`, and `Chase`. This is where most of your implementation and time spent will be held. Later on, we will see how to define these actions. 

## States

States are well... states! Think of states as an one or two word "present tense" description of the actions of the enemy. For instance, the enemy is in the `Patrolling` state or the `Chasing` state.

## Decisions

Somehow we have to decide when to change states and start doing some other action. Maybe something is on a timer, or maybe it's an interaction with the player can changes our behavior. We will see how to implement couple of decisions like `PlayerDetected`.

## Transitions

Once we have made a decision we got to have some way of transitioning to the next action. A transition is simply how we get from "a" to "b".

Now that we have some definitions and a framework in which to start crafting together an enemy, let's try to build a simple enemy.

Let's build an enemy that can patrol some waypoints, shoot at the player, and chase the player.

Let's dive into building up our enemy AI. We will start with the most straight forward piece of the puzzle, the *Data*.

//TODO: Define an enemy attributes
//TODO: Define the enemy Actions
//ToDO: Define the enemy States
//TODO: Define the enemy Decisions
//TODO: Define the enemy transitions

//TODO: Hook all these up
//TODO: Show combining multiple actions and decisions
