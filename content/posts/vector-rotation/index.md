---
title: "Vector Rotation"
date: 2023-03-27T18:58:30-07:00
lastmod: 2023-03-27T18:58:30-07:00
summary: "How to calculate rotation when presenting vector-based graphics"
draft: false
toc: true
keywords: "programming, vector, graphics, games"
---

![Rotating spaceship](/vector-rotation/assets/rotation.gif)
# Introduction

While creating an Asteroids clone, I needed to be able to handle rotation of the
player's space ship which I had drawn using vector graphics. Three X/Y
coordinates were being saved and lines drawn between them in order to draw the
ship which needed to be rotated based on which direction the player was facing
at any given time.

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

# Algorithm

new x = x + L * cos(A / 360)<br>
new y = y + L * sin(A / 360)

Where:
- L = Length from center point
- A = Angle


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
collision detection.
