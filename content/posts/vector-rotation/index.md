---
title: "Vector Rotation"
date: 2023-03-27T18:58:30-07:00
lastmod: 2023-03-27T18:58:30-07:00
summary: "How to calculate rotation when presenting vector-based graphics"
draft: false
toc: true
keywords:
- programming
- vector
- graphics
- games
---

![Rotating spaceship](/vector-rotation/assets/rotation.gif)
# Introduction

Rotating a point or a shape around a fixed point is useful in a number of
scenarios, especially when it comes to game making. A number of effects, such as
having a facing direction, floating shapes, rotation, and other graphical
changes can all be handled using this base formula.

While creating an Asteroids clone, I needed to be able to handle rotation of the
player's space ship which I had drawn using vector graphics. Three X/Y
coordinates were being saved and lines drawn between them in order to draw the
ship which needed to be rotated based on which direction the player was facing
at any given time. For this, I used 2D rotation around an origin point formula.

# Algorithm

For the Pico-8 engine, the algorithm is going to be different than the one that
is generally given in a math-based answer. This is due to two factors:
- The Y-axis is inverted. Usually negative numbers are lower than positive.
  - When dealing with screens, one must remember that (0,0) is the top-left most
    point.
- The cos and sin functions operate differently than in other languages.
  - From the [documentation](https://pico-8.fandom.com/wiki/Cos): <i>PICO-8 uses an input range of 0.0 to 1.0 to
    represent the angle, a percentage of the unit circle. Some refer to these
    units as "turns". For instance, 180° or π (3.14159) radians corresponds to
    0.5 turns in PICO-8's representation of angles. In fact, for fans of τ
    (tau), it's just a matter of dropping τ from your expression.</i>
  - Also it looks like the rotation is reversed. Positive is clockwise, not
    counter-clockwise.


new x = x + R * cos(A / 360)<br>
new y = y + R * sin(A / 360)

Where:
- R = The radius of the circle that is made between the origin and the point
- A = The degrees off of the base facing angle (0-360 inclusive)

# Example case: Asteroids Spaceship

In my specific case, I was doing a 2D Asteroids remake where I had a player with
a given X/Y position. Based on this position, I would draw three lines that go
between points which outlined the ship to create a triangle. These points were
given as measurements off of the player's position as though they were facing
north.

In order for me to handle rotation, an additional variable was kept for the
angle that the player was currently facing given in terms of 360 degrees. If it
was the case that a player was going to go above or below the 0-359 degree
rotation thresholds, then it would wrap around to the other side to complete the
circle.

## Setup code

{{< highlight lua >}}
-- Create a table to hold the player's variables
player = {}

-- For the game engine I was using, the screen was always 128x128.
-- Initialize the player's location, starting in the middle.
player.x = 64
player.y = 64

-- New variable for keeping track of where the player is facing
player.direction = 275
{{< / highlight >}}

# Example Case Code
{{< highlight lua >}}
function draw_spaceship(x, y, ship_angle)
  -- First, we calculate where the nose of the plane is.
  local nose_angle = ship_angle - 180
  
  -- Then we generate the points using the formula
  local nose_x = x + 4 * cos(nose_angle/360)
  local nose_y = y + 4 * sin(nose_angle/360)
  
  -- Next, we must gather the two other points
  -- creating a table to hold the x/y pairs.
  local line_points = {}
  
  -- The spaceship itself has a point with 60 degrees.
  -- We will grab points 30 degrees off the center line.
  -- Think of this like folding a paper airplane.
  for angle in all({30,-30}) do
    -- We will need these points later to finish the ship.
    local x = x + 4 * cos((ship_angle + angle)/360)
    local y = y + 4 * sin((ship_angle + angle)/360)
    add(line_points, {x,y})
    
    -- But we can use the nose and the current point as well.
    line(nose_x,nose_y,x,y,7)
  end
  
  -- Finally, connect the two legs of the spaceship
  line(
    line_points[1][1],
    line_points[1][2],
    line_points[2][1],
    line_points[2][2],
    7
  )
end
{{< / highlight >}}

# Conclusion

The algorithm that can be applied to a number of points around a fixed position
allows for their rotation which, in some cases (especially vector graphics
games), can be the desired effect.

Note that the rotation may need to be accounted for in one's code dealing with
collision detection. This may also work in a 3D space if the rotation that is
being applied only happens in a single plane that is at a right-angle. Any
deviation from that would require that the third dimension be accounted for in
the calculation.
