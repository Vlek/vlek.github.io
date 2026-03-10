---
date: '2026-02-26T15:39:43-08:00'
draft: false
title: 'AutoHotkey: Still Good'
---

## Introduction
 
AutoHotkey is one of those things that gets written off as a hacky automation
choice as it is primarily used to automate UIs. It's clunky, it has its own
language that has a myriad of problems, but people still use it. That's because,
for the niche that it tries to fill, it actually does a pretty good job. At
least far better than the alternatives. Not to mention, it is still maintained!

There are no other pieces of software that only need an executable and a
plaintext script file that can do the kinds of things it can. At least not
anything with the same feature set. This is a godsend if you have a locked down
company computer, a set of repetitive steps that you have to perform over and
over, and a little bit of time in between tasks.

Today, the kinds of things I use it for are mostly work related. I find that it
offers quality of life macroing changes that make certain combinations easier to
hit or would otherwise take one or more mouse clicks to do. I will go over them
in detail to give you an idea of what is possible.

## What I Use It for Today

Early on, the videogames that I would play would have macroing capabilities. I
believe that, when something is hard to navigate, my brain automatically thinks
to go to a settings menu, open a macroing section, and be able to set key
combinations in order to do things within the UI. Obviously, that is something
frustratingly siloed to games, but my brain still does it.

To get around that, I have set up a script that auto-starts when my PC turns on
that has a number of goodies in it that makes my normal workday computing
easier. If you notice, they are mostly focused on left-handed combinations. That
is so that I can also be navigating with the mouse at the same time.

### Managing the AutoHotkey Script

Funnily enough, when you are trying to get something in AutoHotkey to work, you
are going to be opening, closing, and reloading the script a lot. It just makes
sense then to use AutoHotkey to make that easier.

``` Autohotkey
^=::
    ; Open the current ahk script in notepad++
    Run, C:\Program Files\Notepad++\notepad++.exe %A_ScriptFullPath%
return

^+r::
    ; Reload the current script
    Reload
return
```

### Quality of Life Hotkeys

I use these multiple times per day. It's very helpful if you're trying to take
notes on an online training to be able to hit Win + Space to pause the video,
keep typing away, and then Win + Space again to continue the video. It keeps
you from moving your hand to the mouse and losing the flow that you're in.

I really like Win + x for close window. It's awkward enough that I have never
accidentally hit it, but close enough to be easy to hit when I do want it.

I have also had several instances where the Windows 11 start menu just won't
open no matter if I click on it, try to restart explorer.exe, etc. Win + q
shutting down the system and bypassing that is great.

``` AutoHotkey
#Space::
    ; Play/pause
    Send, {Media_Play_Pause}
return

#x::
    ; Much easier to reach than Alt + F4
    Send !{F4}
return

#q::
    ; Shutdown the PC
    Shutdown, 1
return
```

### Program Shortcuts

This I think mostly came from my time with Gnome on Ubuntu. These are pretty
standard-looking program shortcut macros that you would use. It's Ctrl + Alt +
\<letter\> that references the program the best except for the terminal one
where it's usually Win + t, and I added a modified version for PowerShell.

Personally, I feel much more comfortable in linux, so that's my default. You may
want to switch if you find yourself using PowerShell/cmd more.

``` AutoHotkey
^!n::
    Run, C:\Program Files\Notepad++\notepad++.exe
return

#t::
    Run, Ubuntu
return

^#t::
    Run, powershell
return
```

### Standard Macros

This is a great macro because not everything has a dictionary built
into it. Sometimes I am writing a Teams message and want to double-check
that the work that I want to use actually means what I think it does.

Usage: Highlight a word and press Ctrl + l. It'll open dictionary.com to the
definition of the word.
``` AutoHotkey
^l::
    Send, ^c
    Sleep, 100
    Run, https://www.dictionary.com/browse/%clipboard%
return
```

### Key Remaps
Turn the capslock button into a second left-ctrl button. This feels more
natural for some linux-based things. It also doesn't cause me to rotate my wrist
as much.

``` AutoHotkey
CapsLock::Ctrl
```

## Full Script

{{< 
  accordion
  mode="closed"
  separated=true
>}}
  {{<
    accordionItem
    title="Full Script"
    icon="code"
    open=false
  >}}
  ``` AutoHotkey
  ; AutoHotkey script by Derek McCammond
  ; Ctrl: ^, Alt: !, Win: #, Shift: +

  #NoEnv
  SetWorkingDir %A_ScriptDir%
  #Persistent
  #SingleInstance, force
  DetectHiddenWindows, On
  SetTitleMatchMode 2

  SetCapsLockState, AlwaysOff
  SetNumLockState, AlwaysOn

  ; ---- Editing AutoHotkey Script ----

  ^=::
      ; Open the current ahk script in notepad++
      Run, C:\Program Files\Notepad++\notepad++.exe %A_ScriptFullPath%
  return

  ^+r::
      ; Reload the current script
      Reload
  return

  ; ---- QoL Macros ----

  #Space::
      Send, {Media_Play_Pause}
  return

  #x::
      ; Much easier to reach than Alt + F4
      Send !{F4}
  return

  #q::
      Shutdown, 1
  return

  ; --- Program Shortcuts ----

  ^!n::
      Run, C:\Program Files\Notepad++\notepad++.exe
  return

  #t::
      Run, Ubuntu
  return

  ^#t::
      Run, powershell
  return

  ; ---- Standard Macros ----

  ^l::
      Send, ^c
      Sleep, 100
      Run, https://www.dictionary.com/browse/%clipboard%
  return

  ; ---- Button re-mapping ----

  ; Turn the capslock button into a second left ctrl button.
  ; This feels more natural for some linux-based things.
  CapsLock::Ctrl
  ```
  {{</ accordionItem >}}
{{</ accordion >}}

## Conclusion

AutoHotkey is a powerful tool that can be used in a wide variety of
user-facing scenarios. Hopefully going through my script gives you some
ideas as to how you may use it yourself. Just be warned, when you get into it,
you may try to reach for COM objects and potentially use it in places that you
would be better suited grabbing a real scripting language to get done!
