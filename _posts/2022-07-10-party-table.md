---
layout: post
title: "RockSat-X at Virginia Tech"
date: 2022-07-10
categories: [Hardware]
tags: [project]
order: 10
---


## About
I built a party table that cost entirely too much but looks awesome! The table has a frosted acrylic surface which allows for LEDs underneath it to shine through and diffuse nicely across the entire thing. It also features cup lights which activate or deactivate depending on whether a cup is on them or not. A bright VT logo in the center also has independently controllable lighting which makes it stand out from the rest of the surface.

## Motivation
To make parties more fun :D

## Technical
The table uses magnetic switches and 3D printed stencils to shine LEDs through the diffused acrylic in the correct locations. This worked way better than I was expecting once I swapped out the magnets in the bottom of the cups for more powerful ones which could trip the switches through the acrylic. The lights are controlled using an Arduino Mega and I built a hardware abstraction layer to make it easier to upload custom color profiles and animations without having to care about the underlying GPIO pins, voltage levels, and communication.