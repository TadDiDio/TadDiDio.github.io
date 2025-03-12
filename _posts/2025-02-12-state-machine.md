---
layout: post
title: "General, Hierarchical State Machine"
date: 2025-02-12
categories: [Portfolio]
order: 9
description: A package I wrote to reduce boiler plate code when making hierarchical state machines.
---


## About
The general, hierarchical state machine package I wrote removes all the monotonous boilerplate code from developers while allowing them to focus on defining how states should behave and transition.

## Motivation
I wanted to be able to focus on defining what happened in a given state, and how they transition without copy-pasting the same boilerplate code all the time. I also wanted to avoid having to use enums just to define the states a machine could be in.

## Technical
To implement this, I designed an object oriented system with two main classes, State, and StateMachine