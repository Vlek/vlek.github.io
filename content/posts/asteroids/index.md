---
title: "Remaking Asteroids"
date: 2023-03-15T00:00:00-07:00
draft: false
toc: true
---

<iframe src="https://www.lexaloffle.com/bbs/widget.php?pid=22904" allowfullscreen width="621" height="513" style="border:none; overflow:hidden"></iframe>

# Introduction

Asteroids, the space shooter game originally developed by Atari in 1979, has an iconic visual style
and gameplay that has seen many revisions and spawned an entire genre of video games
under the top-down shooter moniker. Due to its simple design and appeal, it is
appealing to programmers to recreate the classic game in an attempt
to get a better understanding of game making principles, including vector
graphics, velocity, and handling state including from the new game
screen, the gameplay, and a game over screen along with the in-game state of
having multiple objects moving around and interacting with each other through
collision detection.

In this article, I will be going over my implementation of the classic Asteroids
game and detailing the things that I learned along the way, including tidbits
regarding the Pico-8 game engine that I used to do it.

# Environment Setup

First thing is first, I needed to get my tooling set up in a way that would make
the game development easier than working within Pico-8. While the game engine
does provide an editor, it lacks a lot of features that would make programming
easier, including syntax highlighting, warnings and error notifications, and
smart indent/outdent along with a host of macros.

## Moving to Atom

During the time that I was working on this, Atom had a vibrant community and
included the necessary configurations to make Pico-8 development easier. This
IDE made single-file editing a breeze and even came with a theme to make the IDE
appear Pico-8-like if that was the developer's choice. There was also a plugin
available so that the IDE would recognize the underlying helper Lua functions
and global variables that the engine provided.

I had even done a write-up where others weighed in on the configuration that I
had done, adding to the conversation with me updating my post to include other
insights that the community had included which helped me further hone my IDE.

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

![Start Screen](/remaking-asteroids/images/asteroids_start_screen.png)

## Creating the player's spaceship

First thing is first, the player needs to be able to take control of a spaceship
in order to destroy asteroids. I felt like this was as good a place as any to
start with. I first went with creating my three-line spaceship and set it so that
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

For my article related to rotation, [click here]({{< relref
"posts/vector-rotation" >}})

## Adding asteroids

Asteroids are added based on the level number. Additional asteroids are given
for higher levels with a maximum of four big ones being given at high enough
levels. This is due to the small screen real estate and something that could and
very likely should be dialed in further, even with smaller asteroids thrown into
the mix.

## Handling collisions

While it will cause collisions when not strictly correct, I am using
circle/circle collision detection. What this means is that, the spaceship is
touching an asteroid if it's the case that the midpoints of the two objects are
close enough that their radius' are overlapping.

This works a lot better for things that are more circular, such as the shots
that the ship fires.

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

For my article related to velocity, [click here]({{< relref "posts/velocity" >}}).

## Setbacks

### Rotation

As noted in my [article]({{< relref "posts/vector-rotation" >}}) on rotation,
there were some quirks in the Pico-8 engine which caused the actual algorithm
that I am using to be different than the standard one that would generally be
used. The one that I am using appears to be a truncated one due to the quirky
behavior of the cos and sin functions, so what I did is likely not reproducible
in other engines without using what would instead be considered the correct
algorithm.

### Engine limitations

#### Screen size

This is probably the most irritating of the setbacks that were faced. The
limitation on the number of pixels that are given is a huge issue for this type
of game specifically. It seems like 128x128 is just not enough to really give
the player breathing room between them and the asteroids that are coming,
especially at some of the higher velocities that I currently have them set at.

If I was going to do this game over again, I would want probably at least
600x400, which would probably give me enough to double the size of the objects
in the game and still give the necessary breathing room and even add in the
alien ships.

#### Lack of built-ins

The engine purposely gives a very small number of tools and allows the user to
remake a lot of things by hand. This, coupled with the fact that there is a code
size limitation, causes the developer to make choices in terms of what can be
done.

The issues that I ran into are trying to find the correct algorithms for things
like collision, rotation, velocity, etc. Also trying to keep data in a sane way
for all of the objects and access them.

### Lua

The engine uses the Lua language. The language itself has very little tools
included purposefully in order to keep the language size down. Its syntax is
also not the most easy to read either. It definitely felt like more of a
hindrance than something that makes the engine shine. I feel like I would have
had an easier time if I were working with Python for instance where I would have
had classes and things, maybe even multiple file support.

## Code

<details>
{{< highlight lua >}}
-- asteroids
-- by vlek

tick = 1
blink = true
explosion_colors = {6,7,12}
game = {}
player = {}
laser_shots = {}
asteroids = {}
explosions = {}

brighter = {1,5,14,11,9,13,7,7,14,10,7,7,7,6,15,7}
function line(x1,y1,x2,y2,c)
  dx=x2-x1
  dy=y2-y1
  len= sqrt(dx*dx+dy*dy)
  if len != 0 then
    for f=0,len,0.3 do
      x=x1+dx*f/len+0.5
      y=y1+dy*f/len+0.5
      pset(x,y,brighter[pget(x,y)+1])
    end
  end
end

function _init()
  game_menu()
end

function _draw()
  game.draw()
end

function _update()
  game.update()
end

function game_menu()
  generate_asteroids(8)
  game.update = menu_update
  game.draw = draw_menu
end

function menu_update()
  for i=0,5,1 do
    if btnp(i) then new_game() end
  end
  for asteroid in all(asteroids) do
    do_move(asteroid)
  end
  do_tick()
end

function draw_menu()
  cls()
  rectfill(0,0,128,128,1)
  draw_asteroids()
  centerprint('asteroids',64,36)
  if blink then centerprint('press any button to start',64,94) end
end

function new_game()
  player = {}
  player.x = 64
  player.y = 64
  player.vx = 0
  player.vy = 0
  player.thrust = 0.1
  player.max_thrust = 4
  player.facing_angle = 275
  player.score1 = 0
  player.score2 = 0
  player.lives = 4
  player.level = 0
  player.level_display_counter = 0
  player.ticks_until_next_level = 60
  player.isdead = false
  player.ticks_until_respawn = 90
  player.isinvulnerable = false
  player.invulnerability_counter = 60
  laser_shots = {}
  asteroids = {}
  explosions = {}
  game.update = game_update
  game.draw = game_draw
end

function game_update()
  for explosion in all(explosions) do
    if explosion.steps < 10 then
      explosion.steps += 1
      for bit in all(explosion.bits) do
        do_move(bit)
      end
    else
      del(explosions, explosion)
    end
  end
  for laser_beam in all(laser_shots) do
    -- check for collisions with asteroids
    local collided = false
    for asteroid in all(asteroids) do
      if check_collision(laser_beam.x,laser_beam.y,asteroid.x,asteroid.y,2,asteroid.collision_size) then
        collided = true
        asteroid_collision(asteroid)
        break
      end
    end
    if collided or laser_beam.movement_count > 29 then
      del(laser_shots, laser_beam)
    else
      do_move(laser_beam)
      laser_beam.movement_count += 1
    end
  end
  if #asteroids > 0 then
    for asteroid in all(asteroids) do
      -- check for collision with player spaceship
      if check_collision(player.x,player.y,asteroid.x,asteroid.y,4,asteroid.collision_size) and not player.isdead and not player.isinvulnerable then
        asteroid_collision(asteroid)
        player_collision()
      else
        do_move(asteroid)
      end
    end
  -- if there are no more asteroids, then spawn more
  else
    if player.ticks_until_next_level < 1 then
      player.level += 1
      player.level_display_counter = 60
      level_asteroids = 1
      if player.level < 5 then
        level_asteroids += player.level
      else
        level_asteroids = 5
      end
      generate_asteroids(level_asteroids)
      player.ticks_until_next_level = 90
    else
      player.ticks_until_next_level -= 1
    end
  end
  do_move(player)
  -- we're only tracking button presses when the player is alive
  if not player.isdead then
    if btn(0) then
      player.facing_angle += 5
      if player.facing_angle > 360 then
        player.facing_angle -= 360
      end
    end
    if btn(1) then
      player.facing_angle -= 5
      if player.facing_angle < 0 then
        player.facing_angle += 360
      end
    end
    if btn(2) then
      -- update velocity and v-angle
      player.vx = mid(-player.max_thrust,
                      player.vx + cos(player.facing_angle/360) * -player.thrust,
                      player.max_thrust)
      player.vy = mid(-player.max_thrust,
                      player.vy + sin(player.facing_angle/360) * -player.thrust,
                      player.max_thrust)
      sfx(1)
    else
      -- otherwise, we're going to apply the space breaks (physics be damned)
      player.vx *= 0.99
      player.vy *= 0.99
    end
    -- if they're trying to shoot and they have less than five bullets on screen,
    if btnp(4) and #laser_shots < 5 then
      local nose_angle = player.facing_angle - 180
      shoot(player.x + 5 * cos(nose_angle/360),
            player.y + 5 * sin(nose_angle/360),
            cos(player.facing_angle/360) * -2,
            sin(player.facing_angle/360) * -2)
      sfx(0)
    end
    if player.invulnerability_counter > 0 then
      player.invulnerability_counter -= 1
      if player.invulnerability_counter == 1 then
        player.isinvulnerable = false
      end
    end
  else
    -- if we have no more lives, game over
    if player.lives == 0 then
      if player.ticks_until_respawn == 0 then
        for i=0,5,1 do
          if btnp(i) then
            new_game()
            break
          end
        end
      else
        player.ticks_until_respawn -= 1
      end
    else
      if player.ticks_until_respawn == 0 then
        player.isdead = false
      else
        player.ticks_until_respawn -= 1
      end
    end
  end
  if player.level_display_counter > 0 then player.level_display_counter -= 1 end
  do_tick()
end

function game_draw()
  cls()
  rectfill(0,0,128,128,1)
  for explosion in all(explosions) do
    for bit in all(explosion.bits) do
      pset(bit.x,bit.y,bit.color)
    end
  end
  for laser_beam in all(laser_shots) do
    circfill(laser_beam.x,laser_beam.y,1,7)
  end
  draw_asteroids()
  -- we're only going to draw the spaceship if the player isn't dead
  if player.isdead then
    if player.lives == 0 then
      if blink then
        centerprint('game over',64,36,7)
      end
      centerprint('play again?',64,85)
    end
  else
    if not player.isinvulnerable or player.isinvulnerable and tick%15>7 then
      draw_spaceship(player.x, player.y, player.facing_angle)
    end
    if player.level_display_counter > 0 then
      local level_string = 'level '..player.level
      print(level_string,64-#level_string*4/2,36,7)
    end
  end
  print_score()
  for i=1,player.lives-1,1 do
    draw_spaceship(i*5,11,271)
  end
  --print('2016 vlek',45,122)
end

function draw_spaceship(x_coord, y_coord, ship_angle)
  local nose_angle = ship_angle - 180
  local nose_x_coord = x_coord + 4 * cos(nose_angle/360)
  local nose_y_coord = y_coord + 4 * sin(nose_angle/360)
  local line_points = {}
  for angle in all({30,-30}) do
    local x = x_coord + 4 * cos((ship_angle + angle)/360)
    local y = y_coord + 4 * sin((ship_angle + angle)/360)
    add(line_points, {x,y})
    line(nose_x_coord,nose_y_coord,x,y,7)
  end
  -- connect the two legs of the spaceship
  line(line_points[1][1],line_points[1][2],line_points[2][1],line_points[2][2],7)
end

function draw_asteroids()
  for asteroid in all(asteroids) do
    for point=1,#asteroid.points-1,1 do
      line(asteroid.points[point][1]+asteroid.x,
           asteroid.points[point][2]+asteroid.y,
           asteroid.points[point+1][1]+asteroid.x,
           asteroid.points[point+1][2]+asteroid.y,
           7)
    end
    -- lastly, draw the line between the first and last points:
    line(asteroid.points[1][1]+asteroid.x,
         asteroid.points[1][2]+asteroid.y,
         asteroid.points[#asteroid.points][1]+asteroid.x,
         asteroid.points[#asteroid.points][2]+asteroid.y,7)
  end
end

function shoot(x, y, vx, vy)
  local laser_beam = {}
  laser_beam.x = x
  laser_beam.y = y
  laser_beam.vx = vx
  laser_beam.vy = vy
  laser_beam.movement_count = 0
  add(laser_shots, laser_beam)
end

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

function do_tick()
  if tick < 29 then
    tick += 1
  else
    tick = 1
    if blink then
      blink = false
    else
      blink = true
    end
  end
end

function randint(min, max)
  local range=(max+1)-min
  return flr(rnd(range)+min)
end

function randfloat(max)
  -- this is really misleading, but it gives a positive or a negative
  -- float up to but not including the maximum
  if randint(0,1) == 1 then
    return rnd(max)
  else
    return rnd(max) * -1
  end
end

function create_asteroid(x,y,vx,vy,size)
  local asteroid = {}
  asteroid.x = x
  asteroid.y = y
  asteroid.vx = vx
  asteroid.vy = vy
  asteroid.size = size
  asteroid.points = {}
  asteroid.collision_size = 0
  local degrees = 360
  while degrees > 0 do
    if degrees < 30 then
      degrees = 0
    else
      degrees -= randint(20,50)
    end
    local new_x = randint(size/2,size)
    local dx = new_x * cos(degrees/360)
    local new_y = randint(size/2,size)
    local dy = new_y * sin(degrees/360)
    for v in all({new_x, new_y}) do
      if v > asteroid.collision_size then
        asteroid.collision_size = v
      end
    end
    add(asteroid.points, {dx,dy})
  end
  add(asteroids, asteroid)
end

function create_explosion(x,y)
  local explosion = {}
  explosion.x = x
  explosion.y = y
  explosion.bits = {}
  for i=0,randint(5,9),1 do
    local bit = {}
    bit.x = x
    bit.y = y
    bit.vx = randfloat(2)
    bit.vy = randfloat(2)
    bit.color = explosion_colors[randint(1,#explosion_colors)]
    add(explosion.bits, bit)
  end
  explosion.steps = 0
  add(explosions, explosion)
end

function check_collision(x1,y1,x2,y2,rad1,rad2)
  -- check collision for two objects based on central points and radii
  return rad1+rad2 >= sqrt((x2-x1)^2+(y2-y1)^2)
end

function generate_asteroids(asteroid_count)
  for pico=1,asteroid_count,1 do
    local x = randint(1,127)
    local y = randint(1,127)
    if randint(0,1) == 1 then
      y = randint(0,1) * 127
    else
      x = randint(0,1) * 127
    end
    create_asteroid(x,y,randfloat(1),randfloat(1),8)
  end
end

function asteroid_collision(asteroid)
  -- if it wasn't a small asteroid, spawn more
  if asteroid.size == 8 or asteroid.size == 4 then
    for i=1,2,1 do
      create_asteroid(asteroid.x,asteroid.y,randfloat(2),randfloat(2),asteroid.size/2)
    end
  end
  if asteroid.size == 8 then
    increase_score(10)
  elseif asteroid.size == 4 then
    increase_score(25)
  else
    increase_score(50)
  end
  create_explosion(asteroid.x,asteroid.y)
  del(asteroids, asteroid)
  sfx(2)
end

function player_collision()
  player.lives -= 1
  player.isdead = true
  player.ticks_until_respawn = 30
  player.x = 64
  player.y = 64
  player.vx = 0
  player.vy = 0
  player.isinvulnerable = true
  player.invulnerability_counter = 60
  if player.lives == 0 then sfx(4) end
end

function centerprint(text,x,y,color)
  if color == nil then color = 7 end
  print(text,x-#text*4/2,y,color)
end

function increase_score(points)
  local old_score = player.score1
  player.score1 += points
  if flr((player.score1 / 5000)) > flr((old_score / 5000)) then
    player.lives += 1
    sfx(3)
  end
  if player.score1 >= 10000 then
    player.score2 += 1
    player.score1 -= 10000
  end
end

function print_score()
  -- cleverly printing the player's score
  color(7)
  local x_offset = 0
  if player.score2 > 0 then
    print(player.score2)
    x_offset += 20 - #(player.score1.."") * 4
  end
  for i=4,x_offset-4,4 do
    print('0',i,0)
  end
  print(player.score1,x_offset,0)
end
{{< / highlight >}}
</details>

# Conclusion

I was able to make a fully-functional version of Asteroids in a relatively small
amount of code in a toy game engine. The game itself is a little aggravating to
play due to the small resolution that the engine offers, but is good for some
edge-of-your-seat gameplay as asteroids quickly fly passed.

As a learning project, I feel like this was a great opportunity. There were
several things, especially fundamental game making concepts, that I was able to
touch such as holding state for the intro, gameplay, and game over screens.
There are a number of things that I would do differently today with the
knowledge that I gained from the project, but I believe that that is a very
common thing in programming which only shows growth.

## Overall assessment

I am proud of the finished product and also am happy that I took the time to
learn what I did. I tried my best to first try things on my own before
researching how others do things as a way to test myself. This retrofitting has
caused some disjointed code and the single-file nature of the game files does
not help this problem either.

## Things that could be better

### Gameplay

The game, due to the low resolution, makes it very difficult to play for long
without dying. The controls themselves are fine, but, with how little game play
area there is, the asteroids are upon the user quicker than in the original
game. If I was going to tweak this, I would probably reduce the number of
asteroids that appear on screen, make them a bit smaller, or change the
asteroid sizes when they do appear.

### Difficulty options

Right now there is no setting for how hard the game ought to be. Even something
like an Easy, Medium, and Hard mode selection would help with it dictating
things like the number of lives that the user starts with and how big and how
many asteroids are generated per level.

## Suggestible?

As a learning project, Asteroids is one of the better ones that I did. I would
suggest others give it a shot and try their best to come up with efficient ways
to add all of the functionality themselves.
