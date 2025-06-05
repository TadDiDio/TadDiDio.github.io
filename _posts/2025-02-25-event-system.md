---
layout: post
title: "Event System as Middleware"
date: 2025-02-25
categories: [Portfolio]
order: 7
description: An event system package I wrote to act as middleware between components in a Unity application.
---


## About
I wrote a general event system framework to act as middleware between C# subsystems to allow for higher levels of decoupling in larger projects. 

## Motivation
Often when working on applications or games which will grow to be large, it is beneficial to plan distinct subsystems and keep them as decoupled as possible. Not only does this make testing and debugging easier, but it also allows for much more maintainable code because these systems can be designed with simpler assumptions than if the entire application is one large system. However, in almost every case, these modules will still need to communicate with on another, and an event system acting as middleware allows them to communicate with no knowledge of each other whatsoever, thus keeping them completely decoupled. 

## Technical
I implemented a system which manages a global list of events, organized into containers for readibility and maintainability, with the intention of every module in an entire project having access to it. I did this using the singleton pattern, which I normally avoid due to its bad habit of increasing coupling despite removing references. In this particular case, trying to enable communication between subsystems, a singleton is good because we can access it from anywhere and it's generally safe to make the application dependant upon there being a middleware system to facilitate communication. It's certainly better than having direct references between each subsystem.

With a list of events in global memory, any system can publish or subscribe to them. This allows subsystems to communicate indirectly by notifying the middleware of events that have happened and listening to only those which it cares about. This also means that subsystems can still exist and function in isolation, they just won't receive stimulation from any system events without the middleware. Building an application in this way is much more scalable as long as one properly manages which components are sending messages and does not rely excessively on the middleware. For example, if we consider a game in which a player takes damage, the middleware is a great way to propagate the damage event to distinct subsystems like UI and audio, without holding any references. However, it would be a bad design choice to use a global event system to pass messages within a subsystem, for example, the UI receiving the damage event and updating the players health display, then firing another event to loop back and cause the display to shake for a feedback animation. In this case, a direct reference to a animation module is much better because the animation module is also part of the UI subsystem and we can avoid having overly specific events globally visible to all subsystems. 

There is still one additional problem with the system as described at this point. If an object is created after the program starts, a very common action, it may need data that is sourced from another subsystem immediately. However, the system makes no guarentees about when events will fire, so that object may not see an update for a long time. To remedy this, I've implemented caching into the event system. All global events that pass data will cache the most recent data sent, so that late spawning objects can query the system immediately. 

Another thing I want to mention about this system is that I enforce maintainable habits by only allowing a single, generic parameter to pass through. This means that if a developer wants to pass more than one piece of information through an event, they should make their own data type to encapsulate and convey what each parameter means. For example:

```c#
// This is confusing 
Event<uint, int, bool, string> PlayerConnected;

// This is much more intuitive and self documenting
public struct PlayerConnectionData
{
    public uint clientID;
    public int serverTick;
    public bool asHost;
    public string username;
}

Event<PlayerConnectionData> PlayerConnected;
```

One final thing to mention is about how I combat the levels of misdirection and fragmented logic while using this system. If abused, the event system can quickly make debugging nearly impossible as various event handlers will add snippets of logic, then produce another event which will in turn be handled and filtered by even more logic and so on. Sequencing is also difficult to see at a glance because everything is event driven rather than neatly packed in a function. To solve both of these problems, I will introduce a sequencing event layer between the event system and any subsystems which need to coordinate. This extra layer intercepts all raw events and performs all logical filtering and sequencing before distrubting events as commands to the child subsystems under it. This means that a single sequencer class is handling all the logic making debugging and flow analysis much easier. 

I've learned all that I've discussed in this post though a lot of iteration and research, mainly though Locomotive 115 and my developer console which are covered in other posts on my site here. Checkout my github to see the implementation (link at the bottom of the left side bar)!