---
title: "Coming Full Circle: Easyuo Miner"
date: 2022-07-04T18:37:04-07:00
draft: true
toc: true
---

# Abstract

A lot of programmers have a story about what got them interested in programming.
For me, it started watching my father attempt to create an auto-miner bot in
Ultima Online using EasyUO. Seeing him write some text into the editor, pressing
play, and seeing the character on screen perform actions was magical. I had
played the game for a while by then and I had never thought beyond putting a
weight on a keyboard key to cause the in-game macro system to continuously try
to use the hiding skill to gain that as a means to automate the game. His reply
was that I needed to learn programming first, plopped down an easily thousand
page C++ book on my desk, and told me to read it cover to cover first. I was
eleven at the time, so there was little to no chance that I was actually going
to do it, especially without any guidance. Later in life I found other avenues
to actually start programming, but I never got back to the task that had
originally captivated me. In this post, I will do my best to complete what I had
set out to do using the tools that my father was using.

Note that I have already completed this several times using other tools,
including using Razor which is a feat in and of itself. This is specifically a
reflection on using the EasyUO tool which has its own benefits and drawbacks
compared to the other tools I have used previously, such as Razor, UOSteam,
Stealth, and ClassicUO.

# Environment setup

## Ultima Online Client
I cloned my environment that I made for the Razor article. This allows me to
start from an environment that I know already works and gives me the ability to
make any changes I need without worrying about current and future contamination.

## Ultima Online Server
From the time of starting this project to when I finished, the server, UOHybrid,
that I was logging in to has gone offline. From the forum posts, it seems to be
going down for good even though there has not been any official statements made.

In order to get around this, I knew that I would be starting fresh on a
different server, so I created a local game server instance using RunUO which
would allow me to get all of the resources I would need quicker than starting
out on a different server as I would have admin rights and I would also not be
breaking the rules in another server that may not allow EasyUO botting.

## EasyUO:

### Client:
The biggest hurdle it seems with getting EasyUO up and running is that the
website has never really stayed up-to-date security-wise. It appears to now be
completely failing with a TLS error now, not even giving the option to bypass on
Firefox.

Thankfully I have saved one version that appears to be from 2013, which will
hopefully still work. I also saved a version of the documentation.

Trying to get the program to work, it did not want to "see" the active client
when I attempted to start both separately via Lutris. Attempting to start the
client within EasyUO attempted to contact the official servers with the client.
Updating Login.cfg to point to the IP address for UOHybrid causes the client to
stall on account verification.

Finally, what I had to do was use Qutebrowser to go to the site, TLS security be
damned, and grab the latest EasyUO version. Now it appears to be functioning
correctly.

In looking at the client, it is exactly as I remembered. The script editor looks
almost like a souped-up notepad instance with the white background and not a
whole lot going on. The right-hand side is taken up by in-game variables that
will be filled out if it is the case that there is an active client in game that
EasyUO is able to detect. In my case, it finally was. Some glaringly annoying
things are the fact that there appears to be syntax highlighting for keywords
but no linting. It is also possible to click anywhere in the editor and have the
cursor start at the clicked position instead of at the last character as one
would expect.

### Documentation:
The documentation for the client is OK. It is just a list of what is available
in the client without any examples. It is going to be hard to work with this and
construct anything without also knowing datatypes and things that can be passed
around. For instance, it lists that there are things such as call, exit, halt,
and return but does not mention whether there can be supplied values associated
with these things. While this is helpful as a jumping-off point, it is going to
require additional time to get off the ground just by playing with the language.

It also does not include things like what datatypes are able to be used. This is
going to be more painful than I thought. Here is to hoping there is some sort of
array or list.

For posterity, I have included the documentation pdf that I am using here.

### Language:
I was surprised to see some Stealth-like abilities, such as dealing with things on
a per-event basis. There is also flow control, allowing for scripts to run other
script files, and the use of VB-like subs. This means that we have function-like
behavior, albeit with the side-effect of muddling with out-of-scope variables.

There is also really powerful tools to do out-of-game menus that were magic back
in the day. It is still an extremely powerful and useful tool to this day, so I
might try to make use of it to display state throughout the process.

Due to the lacking additional documentation, it is unclear whether there are
going to be issues passing variables around or what datatypes are available.

This also follows a very VB-like language structure throughout. The last time I
saw something like this I had to do some VBA code to do some Excel scripting.

The menu system is sort of like AutoHotkey in how it is listed line-by-line
instead of altogether as a single object. It appears that it takes for granted
that only one menu will appear at a time.

The language also appears to be dynamically typed and quotes are not required?

I'm also really interested to hear how `execute` and `send` both work. One will
execute an external program and the other will do an http request and run the
returned code. I think I could use one or both of these to potentially save run
data to a text file.

## Code:

### Repository

I decided to embed the repository for the script in my EasyUO repo on GitHub. I
thought it would be easier to have a one-stop-shop for everything that I was
going to make in relation to the script. It would also be helpful to have the
test scripts, documentation, and EasyUO versions there as well. I was able to
also uncover a few versions of EasyUO by doing a Duckduckgo search, but I was
not able to get any version history regarding what versions of UO they were
meant to be used with, what changed from one version to the next, etc.

# Researching EasyUO

## Gathering info not in docs
I thought a good place to start would be to look at some of the scripts that
others had already made for EasyUO. What I found was a bit surprising. There
were scripts that people had been performing upkeep on that had iterations
that spanned over a decade. The code itself absolutely mirrored that insofar
as some areas had not been touched since the code's inception and other areas
that seemed to have gotten some more recent attention.

To highlight the point, I noticed that one script spanning over a thousand
lines of code had several areas copy/pasted at the end that had timestamps
in the comments from the early 2000's while other areas had comments that
looked like they were dealing with other segments almost as if it was giving
historical context to why things were the way that they were well after the
fact like the developer had written it so long ago that they needed to
re-learn what they had done and then decided at that point to write a comment
regarding what was happening in the logic.

### Variable types
Going into my research, I am very worried that everything is considered a string
based on the wording of the `deleteVar` built-in sub.

### Collections

One interesting way that I noticed that collections could be made was using
Windows' Registry. The client was giving the option for scripts to save items
directly to the Registry which seemed insane. I remember a script that I had
used roughly around 2008 from CyberPope that used this method to save the
locations of the types of resources that could be mined in different areas,
so I knew that this was not just theoretical.

### Passing variables

## What should be easy

## What will be hard

### Not dying

### Dealing with z-axis

### Buying more reagents

### Selling ingots

### Handling death

### Watching out for GMs
Because I am using my own personal server, I am going to completely bypass this
issue entirely. However, this would have been a difficult task if I had not.

The best way that I can think of to reduce the likelihood of this is to stay
in areas that get little to no foot traffic, have randomized breaks, have the
script lookout not only for attackers but for the GM robe object as that changes
the Gamemaster's character's body id, and also consider times outside of when
people generally play.

### Timing
There is no way to do timestamps without doing some cooky stuff, like using an
outside program with the http request subroutine. Handling world saves and doing
analytics within EasyUO are going to be difficult for instance because of this.

## Limitations

# Design

## State Diagram

# Development

# Testing

# Results

## Metrics

## Recording

## Reflection
