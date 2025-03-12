---
layout: post
title: "Unity Developer Console"
date: 2025-03-03
categories: [Portfolio, Game Development]
order: 4
description: I am writing a developer console package for Unity which gives massive levels of control over a project during edit mode and runtime.
---


## About
The developer console is written to behave in a style similar to a linux terminal so that it is immediately familiar to developers. It is scriptable in many places throughout the execution pipeline via hooks, and adding commands is incredibly easy using a custom DSL attribute based system. All parsing and argument validation is handled automatically, so users wishing to add new commands only need to specify what happens in the execute method of the command class they're writing/ They can then add fields decorated by attributes to have the parser automatically fill them with parsed values from the command line which they can then use with full type saftey in the execute method all without ever performing null checks or casting. This system is fully available during edit mode and at runtime including in development builds, so your workflow does not need to be fragmented. 

## Motivation
The inspector in Unity is very helpful for debugging but it is unable to actually run logic, find nested values, or interact with the game in arbitrary ways. It cannot, for instance, automatically move the player to a required state in a game to accelerate testing and is totally unavailable in builds. It also cannot accelerate edit mode tasks like changing scenes to avoid digging through the project directory or editting settings. 

My developer console can do all these things and much, much more. It is primarily designed as a tool for software engineers to create commands that their team will find useful, but due to multiple levels of abstraction, in the future a fully GUI based interation mode is possible and planned. The console system, which acts like a shell around Unity, will help accelerate testing, debugging, and edit time workflows.

## Technical
There are many technical aspects of this project which are interesting, such as the asynchronous command execution structure or testing process, but I want to talk about the mini DSL I created in an effort to make command scripting as ergononmic as possible. A DSL is a domain specific langauge and I've created a system where commands are nearly fully specified in terms of their attributes. A user is only responsible for writing the Execute() method which determines the actual function of the command, while everything else is provided by default but scriptable when desired. Below is an example of creating a command using my system.

```c#

[Command("test", "A test command", true)]
public class TestCommnand : SimpleCommand
{
  [PositionalArg(0)]
  [InRange(-30, 30)]
  private int favoriteNumber;

  [SwitchArg("verbose", 'v')]
  private bool verbose;

  [VariadicArgs]
  private List<Color> colors;

  public CommandResult Execute(CommandContext context)
  {
    string colors = string.Join(", ", colors);
    
    return new($"Favorite number is {favoriteNumber}, favorite colors are {colors}, and verbose = {verbose}.");
  }
}
```

As you can see, as the user we are not required to perform any checks in the execute method and when this command is invoked, the parser will handle filling the decorated fields and also ensure that any other validators are satisfied, such as the InRange attribute shown above. Users are free to script their own attributes to perform custom validation by inheriting from a base class. These are automatically scanned for and picked up when the command is executed. Users can also parse arbitrary types on the command line by extending a base type parser class. These type parsers can consume more than one token to allow for more natural input styles, i.e. instead of inputting a Vector3 as 10,5,20 we can input it as 10 5 20.

Custom attributes can also be added to link subcommands, mark switches are required, or run pre execution validation steps on the command as a whole such as requiring confirmation or permissions. Also, a binding system allows these commands to easily find game objects in the scene at runtime. By simply using an attribute like this:

```c#
[Bind]
private Player player;
```

We tell the system that this command needs a reference to a Player object. By default, it will automatically inject the first one found, but we can also filter by name and/or tag. If a binding cannont be found or if it is still ambiguous, the user can selected which object they want to bind from a list or using their mouse to click on it. All this is possible because commands are asynchronous under the hood which allows for arbitrary waiting on things like input, selection, long running processes, web requests, or more. However, since the common case does not require this, SimpleCommand is a base class which hides the complexity of the C# async system by providing a typical template method as shown in the command above. Users have the option to use the asynchronous functionality, but don't need to by default.