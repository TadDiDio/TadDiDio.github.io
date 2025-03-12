---
layout: post
title: "Nested Menu System"
date: 2025-03-10
categories: [Portfolio, Game Development]
order: 8
description: This flexible menu system package I built allows for easy UI construction in Unity.
---


## About
This is a flexible UI system for unity which aims to prevent code duplication and messy webs of logic that normally occur when trying to explicitly set up references to all parts of a menu system. It gives developers an easy tool to quickly link up UI systems without having to worry about scripting any transition logic.

 <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; width: 100%;">
  <iframe 
    src="https://www.youtube.com/embed/jBhCRT6e_hE?si=EDixeVNfi8b93pky"
    title="YouTube video player" 
    frameborder="0" 
    allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" 
    referrerpolicy="strict-origin-when-cross-origin" 
    allowfullscreen 
    style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;">
  </iframe>
</div>

## Motivation
I made this package so that projects can be more coherent by using the same solution in every case and to combat code duplication.

## Technical
The video linked about goes into some of the technical details of this project. There are two classes which are the backbone of the whole pacakge, the Screen class and the Controller class. Screens represent any visible UI screen and control logic specific to that screen, while controllers manage navigation between screens by using a stack. I chose to make the Controller class a child of the Screen class which allows for complex behavior with no additional code. Since controllers can be screens themselves, we can have multiple active stacks at once to show multiple screens at the same time in a managed way. This package also automatically handles the showing and hiding logic of all screens with overrideable methods which developers can use to implement custom behavior on these events.