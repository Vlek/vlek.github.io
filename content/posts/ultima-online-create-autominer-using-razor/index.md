---
title: "Creating an Auto-miner Using UORazor"
date: 2022-06-12T14:13:28-07:00
draft: false
toc: true
---

![UO Nostalgia](/creating-an-auto-miner-using-uorazor/images/uo_background.jpg)
# Introduction

I am a long-time Ultima Online player. I have gone through the game during many
of its periods, experienced the game before assistant programs were widely used,
and I have also used many different macroing systems. This is what sparked my
interest in computer science and ultimately what I cut my teeth on when I was
first starting out. I wouldn't suggest that anyone goes about it the way I did,
especially since, for many servers (and many other games for that matter), what
I was doing was considered cheating. Luckily the server that I was playing at
the time, UOHybrid, did not consider macroing cheating so long as you were using
an approved client assistant program and you were there at your PC watching it
work.

One of the first client assistant programs that came out that got widely used
(and, for some reason, still is used today even though there are other, better
programs out there that replace the client completely with something that is not
stuck at <30 FPS) is UORazor. It's a program that is used to act as a MitM with
the client and the server to allow for packets to be interpreted and injected by
the assistant to allow for very basic automated actions to occur (think opening
doors when you get close to them so you don't have to manually open them. This
is, after all, a game from the 90's.)

<!--
![UORazor Logo](/creating-an-auto-miner-using-uorazor/images/uorazor_logo.png)
-->

While I had moved on to things like UOSteam to really take advantage of being
able to type out scripts, having the ability to hold state in variables
(revolutionary, I know), and other client-side niceties, a fair number stayed on
UORazor and were screaming for help to deal with its shortcomings. One of the
never-ending debates that came from the forums for UO were what assistant to
use. This was largely a factor of what the server allowed, but, by and large,
they were unable to detect (or simply didn't care enough to programmatically
track) someone doing otherwise.

During some of these debates on what clients people should be using, the topic
of how capable they are of automation would inevitably be one of the factors.
UOSteam and others were considered, and rightfully so, capable of more
automation and thus considered cheater clients. One argument I usually posited
was that, regardless of what is or is not cheating, I was having fun. Learning
how to program, implementing it, and seeing things run was very rewarding. Using
those experiences, I landed and excelled in a process automation developer role.
Others were completely willing to overlook the fun aspect of what I was
attempting to do, so I generally also would posit as an aside that UORazor was
also likely capable of that same sort of full process automation as well. It is
that statement of likelihood that I would like to address in this article and
fully flesh out whether it is in fact possible or if I was making false claims.

# Environment Setup

One thing I was not looking forward to was figuring out how best to set up my
environment given that it is two old Windows programs working together. However,
the process was made simple by using Lutris. I followed the instructions given
in [this Reddit thread](https://www.reddit.com/r/wine_gaming/comments/ejoh2x/comment/fczh7o2/?utm_source=share&utm_medium=web2x&context=3).
In case it goes down, they are as follows:

<details>
  <summary>Steps to install Windows app using Lutris</summary>

```
(Posted by u/E3FxGaming on r/wine_gaming 2020/01/03)

## Install Wine Version with Lutris:

Note: Alternatively if you've already installed something like Wine-staging on your
PC you could theoretically skip this. You'll always have the option to pick the Wine
version installed on your system, however the versions Lutris offers also take advantage
of fixes introduced by Proton, so you may want to follow this guide part even with
Wine-staging installed.

1. In Lutris use the three vertical dots in the top right corner > activate "Show Left
   Side Panel" if it's deactivated
2. Hover your mouse cursor over the "Wine" entry in the left side panel, there should be
   a download icon "Manage Versions" - click that.
3. Tick the Wine version you want to use. It'll download the Wine version - you can click
   the "OK" button to close the dialogue afterwards.

## Install Game:

1. Create a new folder somewhere under which the new wine prefix will be set-up.
2. In Lutris in the top left corner click the plus icon > "Add Games..."
3. Set the game name, choose "Wine (Runs Windows games)" as a runner (this is important
   because it changes the content of the "Game options" tab
4. On the "Game options" tab select the executable you've got (the installer one)
5. On the "Game options" tab select the Wine prefix (just the empty folder you created
   during step 1)
6. On the "Runner options" tab make sure your desired Wine version is used.
7. Click the "Save" button in the bottom right corner to close the dialogue. Select the
   newely created game entry in your Lutris library and use the "Play" button (right side
   panel - if not enabled you can enable it with the three vertical dots menu in the top
   right corner).
8. Follow and complete the installation wizard.

## Edit Stuff and Install Additional Patches:

1. Right click on your newely created Lutris library entry > "Configure".
2. On the "Game options" tab point the executable entry towards the exe that would launch
   your game (should be in some sub-folder of the wine prefix folder you created). Do not
   point it to one of your patches, there is an easier way to install those than constantly
   editing your Lutris game configuration.
4. Click the "Save" button in the bottom right corner to save your change and close the
   dialogue.
6. Once more use the "Play" button to confirm your base game works. After confirming it
   close your game. (you can use the "Kill all Wine processes" option in the right side
   panel to ensure all Wine processes have ended)
9. Eiter right click your game or use the right side panel > "Run EXE inside wine prefix" > 
   choose your patch exe and follow the wizard instructions.
   
Congrats, game should be installed and patched, the "Play" button should still start the
game like normal.
```

</details>

It was honestly very easy. I do not know why I have had issues in the past
installing things outside of Steam because it was straightforward. I had a bit
of an issue initially because UO.com now only ships a lite installer that does
not initially do the updating necessary to get the client.exe and other files
necessary to play. So I had to go in and run UO.exe to update to get the needed
files. After that was done, it was simple to install UORazor and it
automatically detected the version of UO installed within the container.

# A re-look at UORazor's macroing system

First thing first, I wanted to try to start fresh by going over the macroing
system as if I were viewing it for the first time. Since the last time I opened
the program, I have gone through an entire master's program and have several
years of development experience under my belt now.

![Blank Macro
Screen](/creating-an-auto-miner-using-uorazor/images/blank_macro_canvas.png)

## The things I noticed

- The macroing system does allow for typing in commands now, but albeit not in a
  way that is readily usable. It is still better to record actions, copy them
  out, and then paste them into the editor than trying to type them out.
- It is incredibly hard to get all of the actions done in a way that it would be
  able to record all in one go, but that seems to be the way the editor wishes
  one to do things.
- I tried to chain macros by calling one after another, but it seems to treat a
  macro call like a callback function.
  - This does mean however that that called macro can call another macro and
    they can be daisy-chained in such a way that they allow for the control flow
    to be passed along. Not much is lost using this method because we do not
    have the ability to have state-holding variables anyway.
  - One seriously irksome thing though with this is that, if you are using
    categories to try to keep your macros in a sane directory tree, those macros
    need to be placed into there respective categories first before being called
    in another macro otherwise it creates a duplicate of the called macro in the
    previous location where it existed.

## Getting around limitations

### Variables for holding state

Unfortunately the assistant does not have the ability to save any variables
whatsoever. This is very bothersome for managing the locations that we are going
to teleport to and ensuring that a progressive order is being kept.

The way that I found to get around this however is to keep a bundle of a
specific item that is not used for this process in the main container space of
my bank. This allows me to check its count and use that as a way to know, using
the limited conditional statement availability, where to go next.

```
If there are no arrows,
  take from the main stack of arrows the maximum rune count number in bank box
  
Cast Recall
  
If count >= 16:
  teleport to rune 16
Else:
  If count >= 15:
    teleport to rune 15
  Else:
    etc...
  End If
Enf If
```

### Trying to make sure we are not overweight when teleporting back home

There really is not a good way to do this, even assistants like UOSteam were not
great at handling this. The best thing to do would be to match the ore colors
and combine them into their lightest type (It doesn't make sense, but there is a
small pile graphic that is much lighter.)

In order to deal with this, we will instead for-loop enough times that, even if
we got the big piles every time, we would be at or below our max weight.

### Getting the most ore back possible

When overweight, it might make sense to jettison some of the collected ore. We
want to do that as little as possible. There is also the problem of leaving a
vein not mined completely so we are not getting as much bang for our teleport.
We have to deal with this limitation by analyzing the amount of loops we are
doing and ensuring a good balance is struck.

### Leaving as few seconds wasted per location as possible

There are very clearly areas where the macro is going to be slow, especially
with recalling, ensuring that things are not stepping on each other as state is
not held back while queued actions happen, and server latency is an issue. This
is again not something with an elegant solution, it is just a matter of multiple
runs and playing with times until things generally work most of the time.

### Trying not to die/get caught by game masters

The best way that I have found to handle this is not with code but by ensuring
the locations that the user goes to collect resources are in areas that do not
have monsters nor any real foot traffic. These may even be in areas in town
which rarely get looked at.

### Dealing with in-game server saves

This unfortunately is one that is just going to be a pain and not one that we
can really get around in a good way.

The way that I could imagine dealing with this is by pigeon holing the system
message when it happens, teleporting to a safe location, and closing out of the
game. Another process would be watching for the game client process and restart
it if the game client closes with the last log messages being that a save was
happening and not something like the player died or ran out of resources.

### Resources suck and you run out of them quicker than you think

With RunUO's buy agent, it is possible that a full cycle bot could be created so
long as the player was flush with cash or was also placing items on a vendor to
sell for money. That is pretty deep, usually the place I stopped because I had
issues getting it to work correctly, so I am going to say that this is likely
possible with a bunch of asterisks applied.

### Z-Axis Targeting

For some reason, some of the time when targeting the ground it wants to take
into consideration the Z-Axis which means that we may be targeting an area that
is above or below the actual ground as the terrain is not flat. While the RunUO
code does not appear to care, Razor apparently does. Some of the runes that were
previously marked for other assistant programs may need to be re-marked in
locations where the terrain is flat enough for Razor to register.

#### Runes with issues:

In the end after going through all 32 runes several times, I came up with a list
of the ones that I had to make changes to and the reason why:
- 3: Unhappy alligator
- 8: Unable to target
- 13: Unable to target
- 15: Unable to target
- 16: A harpy lives here, does not appreciate company

# Solution Design

Having done some research into what is possible, it was time to put that work to
use and figure out the best way to put those lessons learned into practice.

## Pseudocode



```
while not dead

  for each mining location
  
    put any ore in inventory in the bank  

    check for resources and restock
    
    if we do not have enough resources
      break with exception message
        
    teleport to location
    
    mine until the spot is empty or we are overweight
    
      if overweight, dump ore until at or under weight limit
      
      if our tools break, make a new one
      
    teleport back
```

## Logical submacros

While-loops are not available in the code, at least not by name. We also cannot
search for and for-loop over a range, so that does not allow us to deal with the
runebook GUIs to be able to recall to different locations. Taking these
considerations into mind from the previous research, I broke up the logic into
several chunks:

- Banking Ore
- Restocking inventory
- Teleporting to next location
- Mining Ore
- Teleporting back to the bank

The state machine diagram is as follows:

![Submacro state machine](/creating-an-auto-miner-using-uorazor/images/macro-state-machine.svg)

# Implementation

Originally when trying to implement this years ago, I believe I had given up due
to the constraints thinking that there was no real way around them, or,
rightfully so, that, if I was going to achieve this feat, it was going to be
with better tools.

## Setbacks

I had completely forgotten about server saves until I loaded up the game and
started taking inventory of the stuff that I had. As soon as the first one
happened, I knew immediately that it was going to return and haunt me. What
happens is, every so often, the server pauses and performs a save. This means
that anything you are doing is paused, the server does not allow for any new
input, and things are in a holding pattern until the save completes, anywhere
from less than a second for a personal server to well over a minute in some
cases for very large servers. In other assistants, they have the ability to read
system log messages and conditionally handle them, which allows them to wait out
saves until the save complete message is given. Not for us however, this is just
going to be a thorn in my side the whole time.


Unfortunately, I believe they were having race conditions, the macro window did
not know which scope I was in half the time, and it was just very clear that
Razor was not intended nor really capable of handling what I was trying to do.

## Workarounds

What I found is that there is a problem with the restock/organizer agents in
that they continue on as though they spawned off another process to get them
done. This means that the macros were continuing on even though there were item
moves being added to a movement queue that was causing issues. This was smoothed
over with waits, but it is not an elegant solution by any means.

To get around the macro window issue, I copy and pasted all of my working code
into a single macro. Luckily it was a straight shot through, there were no real
areas were things were interconnected except for the exception message I was
giving if I ran out of resources.

## Recording

{{< video "/creating-an-auto-miner-using-uorazor/images/autominer_run.mp4" >}}

## Code

<details>

```
!Loop
Assistant.Macros.SpeechAction|0|52|3|ENU|2|16|2|Banco
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.HotKeyAction|0|Organizer Agent-1
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.WaitForStatAction|0|1|95|3600
Assistant.Macros.WaitForStatAction|1|1|95|3600
Assistant.Macros.HotKeyAction|0|Restock Agent-1
Assistant.Macros.AbsoluteTargetAction|0|0|214848|5272|3971|37|400
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.IfAction|50|0|0|Black Pearl
Assistant.Macros.HotKeyAction|0|Play: AutoMiner_SubMacros\AutoMiner_Exceptions\Exception_RanOutOfResources
Assistant.Macros.EndIfAction
Assistant.Macros.IfAction|50|0|0|Mandrake Root
Assistant.Macros.HotKeyAction|0|Play: AutoMiner_SubMacros\AutoMiner_Exceptions\Exception_RanOutOfResources
Assistant.Macros.EndIfAction
Assistant.Macros.IfAction|50|0|0|Blood Moss
Assistant.Macros.HotKeyAction|0|Play: AutoMiner_SubMacros\AutoMiner_Exceptions\Exception_RanOutOfResources
Assistant.Macros.EndIfAction
Assistant.Macros.DoubleClickAction|1074419899|3705
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.IfAction|50|0|0|Arrows
Assistant.Macros.HotKeyAction|0|Restock Agent-2
Assistant.Macros.AbsoluteTargetAction|0|0|1074419899|29|123|0|3705
Assistant.Macros.PauseAction|00:00:03
Assistant.Macros.EndIfAction
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.LiftTypeAction|3903|1
Assistant.Macros.LiftAction|1081626294|1|3903
Assistant.Macros.DropAction|0x400A58BB|(-1, -1, 0)|0
Assistant.Macros.PauseAction|00:00:01
Assistant.Macros.MacroCastSpellAction|32
Assistant.Macros.WaitForTargetAction|30
Assistant.Macros.IfAction|50|0|1|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074530688|45|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|2|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074527814|60|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|3|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074528518|75|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|4|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074529318|90|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|5|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074529671|106|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|6|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074528271|122|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|7|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074529940|139|66|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|8|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074527427|45|83|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|9|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074531668|61|82|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|10|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074529472|76|82|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|11|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074531101|90|82|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|12|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074529038|106|81|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|13|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074531976|122|81|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|14|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074527078|140|80|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|15|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074526834|45|100|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|16|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1074531896|61|100|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|17|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145013|124|96|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|18|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145029|141|97|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|19|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145041|45|114|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|20|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145008|61|114|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|21|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082144978|76|114|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|22|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082144984|91|113|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|23|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145010|108|113|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|24|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082144985|124|112|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|25|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082144973|141|113|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|26|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145026|45|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|27|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145017|61|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|28|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145015|75|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|29|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145018|90|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|30|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082144989|108|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|31|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145031|122|129|0|7956
Assistant.Macros.ElseAction
Assistant.Macros.IfAction|50|0|32|Arrows
Assistant.Macros.AbsoluteTargetAction|0|0|1082145049|141|129|0|7956
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.EndIfAction
Assistant.Macros.ForAction|13
Assistant.Macros.IfAction|50|0|1|Tinker's Tools
Assistant.Macros.PauseAction|00:00:00.7500000
Assistant.Macros.DoubleClickTypeAction|7864|True
Assistant.Macros.WaitForGumpAction|949095101|True|300
Assistant.Macros.GumpResponseAction|23|0|0
Assistant.Macros.WaitForGumpAction|949095101|True|300
Assistant.Macros.GumpResponseAction|0|0|0
Assistant.Macros.EndIfAction
Assistant.Macros.PauseAction|00:00:00.2500000
Assistant.Macros.IfAction|50|0|0|Pickaxe
Assistant.Macros.PauseAction|00:00:01.5000000
Assistant.Macros.DoubleClickTypeAction|7864|True
Assistant.Macros.WaitForGumpAction|949095101|True|300
Assistant.Macros.GumpResponseAction|114|0|0
Assistant.Macros.WaitForGumpAction|949095101|True|300
Assistant.Macros.GumpResponseAction|0|0|0
Assistant.Macros.EndIfAction
Assistant.Macros.DoubleClickTypeAction|3718|True
Assistant.Macros.WaitForTargetAction|30
Assistant.Macros.TargetRelLocAction|0|-1
Assistant.Macros.PauseAction|00:00:00.9500000
Assistant.Macros.EndForAction
Assistant.Macros.MacroCastSpellAction|32
Assistant.Macros.WaitForTargetAction|30
Assistant.Macros.AbsoluteTargetAction|0|0|1086580354|45|66|0|3834
```

</details>

# Analytics

While static analysis is impractical and code quality is lacking, analysis can
still be performed on the output of the process to ensure that it is functioning
correctly and within a reasonable timeframe.

## Methodology

While in-game monitoring was only going to allow me to measure things in the
aggregate, I opted to have the in-game logs output to a file and analyze the
results that way. It was able to give me per-teleport and per-run numbers.

In order to measure a full run's worth of material, I cleared out my ore bag,
counted my starting resource amounts, and run the process again after modifying
the code such, when it would replenish my arrow stack to start over again, it
instead stops with a message saying that the run was complete.

### Tooling

The tools used to perform the analysis were the standard UO client, UORazor, and
linux-based commandline tools (grep, wc, etc.) along with Vim.

### Changes to Process

- In order to have data to analyze, the client needed to be configured to output
the log file which required some configuration changes.
- We also should have done a single click on the arrow type in the backpack to
  be able to know which spot was being mined.

## Results

### Starting Resources:

- Iron Ingots: 9141
- Black Pearl: 826
- Mandrake Root: 1238
- Blood Moss: 1188

### Ending Resources:

- Iron Ingots: 9085
- Black Pearl: 638
- Mandrake Root: 1052
- Blood Moss: 1000

### Mining Outcomes:

- Iron: 628
- Shadow: 492
- Failed to mine: 79
- Resources depleted: 22

### Ore Mined:

Iron: 4194
Shadow: 3344
Gold: 76

All ore was converted down into the smallest ore piles *

### Tools Broken:

- Pickaxes: 14

# Conclusion

## Overall assessment

Due to the new achievement system, I was working with a clean slate. By the time
I felt like I had a working script done, I was just passed 20k ore mined, which
is quite an achievement and something that would have taken some time to mine by
hand.

I feel accomplished, like I had gotten something done that was nagging me in the
back of my mind for a while. Although not perfect, I feel like I have proven
within an afternoon that this is possible, additional polish may be applied to
get it working even better, but what I have presented works.

You know that what you made works when it is the case that you feel comfortable
taking your eyes off of it. During the results run, I was able to have the
process run for nearly two hours without intervention before an errant lag spike
caused it to trip up.

## Things that could be better

### Teleporting

One of the slowest parts of the bot is the selection for which spot to teleport
next. What I did works, but the nested if statements are all parsed in order so
there is a significant lag as it goes through all of the `Ifs` and then the `End
Ifs`. The way that I could think of doing this that will scale is to do a binary
search using submacros. This will get around most of the nested `If`'s where
possible.

### Locations

I was using the books and spots that I had. However, these were not necessarily
the best spots. I do not know if the two books I had were not repeats.

## Applicability

While I did this specifically for mining ore, the technique could readily be
applied to other types of resources. I am thinking specifically lumberjacking as
a potential candidate.

## Suggestible?

In no way would I suggest someone do this. Not only is it potentially not
allowed by the server that one is playing on, it also is just silly. There are
other clients, other means of doing the same thing. This was, as stated in the
introduction, merely to show it is possible.
