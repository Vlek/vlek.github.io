---
title: "Velocity"
date: 2023-03-29T18:56:42-07:00
lastmod: 2023-03-29T18:56:42-07:00
summary: "Quick tutorial and example of dealing with velocities in video game
logic."
draft: false
toc: true
keywords:
- programming
- pico-8
- game making
---

# Introduction

Things in video games tend to move. In order to calculate this movement and hold
its state in memory, the direction and velocity needs to be held.

There are a lot of cases, even in user interfaces, where things tend to slide in
and out of view, either linearly or non-linearly (where things either speed up or
slow down as they get nearer or further away from their starting and ending
positions). This is especially useful in video games however where things like a
player's character needs to respond to gravity when they fall or are jumping
from one platform to another.

The way that this tracked is by holding a few variables which indicate the
direction and speed that the object was previously going and is updated based on
whether it has gotten to a floor position or may even increase in speed as it
falls. Note here that there may be a bounce effect that is desired as well, but
I will not be going into that here. Just know that this can be applied in those
cases as well, but one would need to account for the current acceleration of the
object as it hits a surface and the direction to figure out how quickly and in
what direction it should bounce towards.

# Algorithm

In order to hold this state, each object that is movable should have the
following variables:
- x_velocity: The positive or negative amount across the x-axis to move
- y_velocity: The positive or negative amount across the y-axis to move

Notice that we're only dealing with two directions, but, as some may remember
from algebra class, we can create angles with this data by saving dissimilar
values for the X and Y.

# Example case: Asteroids

For the case where I needed this, I had remade Asteroids and needed to hold the
velocity for my spaceship, asteroids, the explosion particles, and the shots
that were fired. I had abstracted the items in such a way that I was able to use
the same function for everything:

{{< highlight lua >}}
function do_move(obj)
  -- perform movement:
  obj.x += obj.vx
  obj.y += obj.vy
  -- if we're out of screen bounds, throw us back in on the other side:
  for axis in all({'x','y'}) do
    if obj[axis] < 0 then
      obj[axis] += 128
    elseif obj[axis] > 128 then
      obj[axis] -= 128
    end
  end
end
{{</ highlight >}}

As you can see, the actual movement is simply applying the velocity that is
saved to the given object's x/y coordinates. This is why the values can either
be positive or negative to have the full 2D range as possible velocity values.

The other part deals with the classic Asteroids case where items go off screen
and should reappear on the other side of the screen. In the Pico-8 case, the
screen is always 128 (Note I did this well before I had a degree, I did not
save these as a configuration).

# Things to keep in mind

## Collision

One thing you may have noticed is that I have not accounted for collisions in my
example case. The reason for that is, in Asteroids, when things collide, they
tend to stop existing or at least reset. In the case of something like a
side-scrolling adventure game, you are likely going to need to handle the case
where the player jumps from one platform to another and comes to rest.

## Changing velocity
For things like UI elements that are supposed to glide across the screen and
stop, there may even be a desired change in the velocity as the item goes across
the screen that will need to be accounted for. This is likely going to be in the
form of a formula that's applied across the distance traveled.

Note that in my case, I did have a slowing effect applied. I took the x_velocity
and y_velocity values and multiplied them by 0.99 to create gradual slowing if
speed was not constantly applied.

## Bounce effects
Another thing I have not touched on other than to mention I will not address it
is if a bounce effect is wanted. This is the case where an item should fall from
a height, hit a surface, and then have at least some of its velocity applied in
the opposite direction. For the case where the game is simulating a rubber
object, it may be reduced realistically based on the bounciness of the object
that is being simulated. In the case of a puzzle game, such as with Portal with
the light sphere, the velocity does not change, merely the direction. For that,
the x_velocity and y_velocity values would be inverted (i.e., value * - 1).
