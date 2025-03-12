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
To implement this, I designed an object oriented system with three main classes, State, StateMachine, and Transition. In order to allow for hierarchical statemachine desing, or the nesting of statemachines inside other statemachines, I made the StateMachine class inherit from State. This may seem backwards or unintuitive at first, but it allows us to treat a statemachine as just another state to be updated. This allows for much more organized statemachines without writing any additional code! Check out my github (link at the buttom of the left sidebar) for more details on my implementation!