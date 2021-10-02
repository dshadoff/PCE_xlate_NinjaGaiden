# PCE_xlate_NinjaGaiden

Ninja Gaiden is a HuCard-based game for the PC Engine.  The game actually already
contained an English translation for text (not graphics); there is a special code
you can enter at the startup screen in order to set the game to use this English
text... but not many people are aware that it supports English, or how to enable
the English mode.  (At the title screen, if you press I + II + Select simultaneously,
it will switch between Japanese and English).

On a Discord in 2020, I was asked whether it would be possible to set the English
mode as default, because people don't tend to know about it.  I came up with the
patch within an hour or so.

This mini-repository contains the patch and a description of how it was made.


## What Does the Patch Do ?

As described above, pressing the I + II + Select buttons simultaneously switches
the game between Japanese and English display formats.  Using a debugging emulator,
I traced through the program to determine that this changes the memory location $3227
back and forth between values '0' and '1'.  Since it is initialized at startup to the
value '0' and is Japanese, I inferred that the value '1' forces English.

The patch is small enough that it was created and applied by hand.  At a very early
point in startup, control needs to be redirected and a '1' placed into $3227.  So the
code is actually quite trivial, and looks like this:

```
.include "ninjabase.pce"
.BANK  0

; grab control during initialization
.ORG   $E02C
CALL   $FE00   ; originally $FCBE

; supplement to initialization
; this is a blank spot in the first bank
.ORG   $FE00
LDA    #1
STA    $3227
JMP    $FCBE   ; back to where the program was going
```
