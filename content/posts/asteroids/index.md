---
title: "Making Asteroids"
date: 2023-03-15T00:00:00-07:00
draft: false
toc: false
---

# Introduction

Asteroids the space shooter game originally developed by the company Atari in the
year 1979 was first introduced as a cabinet arcade game. Its iconic visual style
and gameplay has seen many revisions and spawned an entire genre of videogames
under the top-down shooter moniker. Due to its simple design and appeal, it is
appealing to programmers to attempt to recreate the classic game in an attempt
to get a better understanding of game making principles, including vector
graphics, implementing inertia, and handling state including from the new game
screen, the gameplay, and a game over screen along with the in-game state of
having multiple objects moving around and interacting with each other through
collision detection.

In this article, I will be going over my implementation of the classic Asteroids
game and detailing the things that I learned along the way, including tid bits
regarding the Pico-8 game engine that I used to do it.

# Environment Setup

First thing is first, I needed to get my tooling set up in a way that would make
the game development easier than working within Pico-8. While the game engine
does provide an editor, it lacks a lot of features that would make programming
easier, including syntax highlighting, warnings and error notifications, and
smart indent/dedent along with a host of macros.

## Moving to Atom

During the time that I was working on this, Atom had a vibrant community and
included the necessary configurations to make Pico-8 development easier. This
IDE made single-file editing a breeze and even came with a theme to make the IDE
appear Pico-8-like if that was the developer's choice. There was also a plugin
available so that the IDE would recognize the underlying helper Lua functions
and global variables that the engine provided.

I had even done a write-up where others weighed in on the configuration that I
had done, adding to the conversation with me updating my post to include other
insights that the community had included.

## Auto-update and play

One thing that the Pico-8 IDE allowed for that was hard to get working outside
of it was a macro so that the file could be saved and opened in Pico-8 to begin
play testing. Through some configuration changes, I was able to get Atom to have
this feature as well and bound it to Ctrl + F5 like one would normally for
running a currently open project.

# Solution Design

During this time, I was much less focused on solutioning. I went into it knowing
that there were parts that I was going to need to add and then sought to add
them in chunks as I finished them. The main parts that I sought to add were a
spaceship that the user controlled with the left/right rotation and the ability
to increase velocity with the acceleration button, randomly generated asteroids,
and the ability to fire one's weapon and have it destroy asteroids that it came
in contact with which would crease pieces of itself until it eventually
disintegrated.

Having done a few Pico-8 games in the past, I was aware that I was going to need
to use both the Update and Draw functions in order to both accept user input and
have the game state reflected on the screen.

# Implementation

## Creating the player's spaceship

First thing is first, the player needs to be able to take control of a spaceship
in order to destroy asteroids. I felt like this was as good a place as any to
start with. I first went with creating my four-line spaceship and set it so that
it was facing up. I would then use 360 degrees as a method of having it rotate
in place.

Little did I know ahead of time that this was going to involve calculus in order
to do the rotation of the spaceship. I was trying to reinvent the wheel myself
while not having the underlying math necessary to really be able to do what it
was that I was setting out to do. I had tried a number of things to see if I
could challenge myself, and I made some funny mistakes that really got me
excited about the possibility of doing this sort of math for vector-based
graphics. In one instance, I had the spaceship rotating and warping in and out
of view as though it was traveling across the face of an invisible ball.

## Adding asteroids

## Handling collisions

## Handling movement

Velocity was a fun concept to try to deal with. I found a good article that went
over how to handle it, but the basic concept is that, for each item that the
developer wishes to have move, it needs to have both an X and Y velocity amount
that is either positive or negative that reflects how fast the given item is
moving in that direction. During the game's update function, that amount is
either increased, decreased, or remains the same based on the rules of the
object and whether anything is acting upon it (think the player's input of the
velocity button increasing the velocity of the ship in the direction it is
pointing.)

Thankfully for me, while I was working on things, I came to the realization that
the bit of code that was handling the collision detection as well as the
movement were both generalizable to all of the objects in the game. Each thing,
including the graphical bits of the asteroids and spaceship that are generated
on a collision, are all moved using the same code!

## Setbacks

## Workarounds

## Recording

## Code

<details>
```
```
</details>

# Analytics

## Methodology

### Tooling

### Changes to Process

## Results

# Conclusion

## Overall assessment

## Things that could be better

## Applicability

## Suggestable?
