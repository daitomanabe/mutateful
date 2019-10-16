# `mutate4l`

**tl;dr:** `mutate4l` enables live coding in Ableton Live's session view. Set up transformations that trigger whenever a source clip is changed, including arpeggiation, shuffling, and ratcheting/retriggering.

<p align="center">
  <img src="https://github.com/carrierdown/mutate4l/blob/feature/new-readme/assets/mu4l-walkthrough.gif" alt="mutate4l in action">
</p>

> With live coding being all the rage this looks like a groovy new idea to bring that excitement and its new possibilities to Ableton. I’m watching closely.
>
> &mdash; Matt Black, co-founder of Ninja Tune & Coldcut.

## Concept
`mutate4l` reimagines Ableton Live's session view as a spreadsheet-like live coding setup. You add formulas to dynamically shuffle, arpeggiate, constrain, scale, retrigger, and otherwise transform clips in myriad ways. Formulas operate on other clips and/or textual inputs to produce new clips. Unlike most experimental sequencers, `mutate4l` leverages Live's built-in sequencing functionality, allowing you to use any existing MIDI clip as a source of fresh inspiration.

## Usage examples

### Creating an arpeggio from a sustained note and a chord

`=C1 slice 1/2 transpose 0 7 slice 1/16 1/8 transpose -by B1`

This formula does the following: Start with the single sustained note contained in the clip at position `C1`. Slice it in half, keeping the first note unchanged and transposing the second note 7 semitones up (a perfect fifth). Now, slice the resulting two half-notes into a pattern alternating between 1/16 and 1/8th notes. Finally, transpose this pattern with the chord contained in the clip located at position `B1`.

### Interleaving two clips together

`=b1 c1 interleave -mode event`

This formula combines the notes contained in the clips located at positions B1 and C1, so that the notes from each clip are played one after the other in an alternating fashion.

![Alt text](./assets/Generated637012367962797269-clip.svg)

### Turning a beat and a chord into an arpeggio

`=c1 scale -by b2 -strict transpose -12 transpose 0 7 5 monophonize`

This formula takes the beat contained in clip C1, transforming notes as needed so as to fit with the notes (or scale if you will) contained in clip B2. It does this in a strict manner, meaning that the resulting notes will get the same absolute pitch instead of the closest pitch in the current octave. All notes are then transposed down an octave, and are then transposed so that the resulting notes alternate between 0, 7, and 5 semitones transposition. Finally the result is made monophonic so that only one note is sounding at any given moment. 

<!--
 Formulas are composed of one or more commands operating on one or more clips. Most commands have various options that can be set depending on your specific needs. They range from simple things like filtering out all notes with a length shorter than a given interval, to more esoteric things like arpeggiating one clip based on another, or creating glitchy beats by adding ratcheting/retriggering to specific notes.
-->

## See it in action

<table cellpadding="0" cellspacing="0" style="border:none;"><tr><td><a href="https://www.youtube.com/watch?v=YNI9ZxhSkWQ"><img alt="mutate4l demo video 1" src="https://img.youtube.com/vi/YNI9ZxhSkWQ/0.jpg" width="250"><p>Demo #1: concat, constrain, transpose</p></a></td><td><a href="https://www.youtube.com/watch?v=bGMBDap1-ko"><img alt="mutate4l demo video 2" src="https://img.youtube.com/vi/bGMBDap1-ko/0.jpg" width="250"><p>Demo #2: ratchet, shuffle, interleave</p></a></td></tr></table>

## A musical spreadsheet

The easiest way to understand what `mutate4l` does is by comparing it to a traditional spreadsheet. Let's say you have two numbers that you'd like to multiply. You put one number in cell `A1`, another in `A2`, and in `A3` you enter the following (very simple) formula: `=A1 * A2`. Cell `A3` will then contain the result of this operation, and will update automatically whenever `A1` or `A2` changes. 

Since the session view in Ableton Live presents clips in a spreadsheet-like grid, `mutate4l` works the same way, only with more musically interesting commands. For instance, you could shuffle the contents of clip `A1` using the contents of clip `A2`. The pitch values of the various notes in clip `A2` would then be used to shuffle the order of notes in `A1`. Similar to the example above, we would like the result to be inserted into clip `A3`, but instead of using a spreadsheet command we will use a `mutate4l` command, as follows: `=A1 shuffle -by A2`. In this example, `A1` is a *source clip* (i.e. the clip that will be transformed), and `A2` is the *control clip* (i.e. the clip that controls the transformation). The latter could be omitted, in which case clip `A1` would be shuffled using itself as the control clip. The formula for this would simply be `=A1 shuffle`.

Full documentation of all commands will follow at a later date. In the meantime, star this repo and/or follow me at [twitter.com/KnUpland](https://twitter.com/KnUpland) for updates.

## Quick command reference

Basic syntax: [ClipReference #1](#parameter-types) ... [ClipReference #N](#parameter-types) commandname -parameter1 value -parameter2 value

Command | Parameters (default values in **bold**) | Description
--- | --- | ---
arpeggiate | -rescale <[Number](#parameter-types): **2**> -removeoffset -by <[Clip reference](#parameter-types)> | Arpeggiates the given clip using another clip, or itself.
concat |  | Concatenates two or more clips together.
crop | <list of [Musical fraction](#parameter-types): **2**> | Crops a clip to the desired length, or within the desired region.
filter | <[Musical fraction](#parameter-types): **1/64**> -invert | Filters out notes shorter than the length specified (default 1/64). If -invert is specified, notes longer than the specified length are removed.
interleave | -chunkchords -solo -mode Event&#124;**Time** -ranges <list of [Musical fraction](#parameter-types): **1**> -repeats <list of [Number](#parameter-types): **1**> -skip | Combines notes from two or more clips in an interleaved fashion.
legato |  | Removes silence between notes. Basically the same as the built-in legato function in Live, but often useful in the context of a mutate4l formula as well.
mask | -by <[Clip reference](#parameter-types)> | Creates a masking clip which is used to remove or shorten notes not overlapping with the mask clip. If no -by clip is specified, a sustained note is used instead, effectively inversing the clip rhythmically.
monophonize |  | Makes the clip monophonic by removing any overlapping notes. Lower notes have precedence over higher notes.
quantize | <list of [Musical fraction](#parameter-types): **1/16**> -amount <[Decimal number](#parameter-types): **1.0**> -by <[Clip reference](#parameter-types)> | Quantizes a clip by the specified amount against a regular or irregular set of divisions, or even against the timings of another clip.
ratchet | <list of [Number](#parameter-types)> -autoscale -by <[Clip reference](#parameter-types)> -mode Velocity&#124;**Pitch** -shape **Linear**&#124;EaseInOut&#124;EaseIn&#124;EaseOut -strength <[Decimal number](#parameter-types): **1.0**> -velocitytostrength | Creates retriggers/ratchets in the current clip, based on a sequence of passed in values or another clip. The ratchets produced can be scaled and shaped in various ways.
relength | <[Decimal number](#parameter-types): **1.0**> | Changes the length of all notes in a clip by multiplying their lengths with the specified factor.
resize | <[Decimal number](#parameter-types): **1.0**> | Resizes the current clip based on the specified factor (i.e. 0.5 halves the size of the clip, effectively doubling its tempo)
scale | -by <[Clip reference](#parameter-types)> -strict | Uses a clip passed in via the -by parameter as a scale to which the current clip is made to conform. If -strict is specified, notes are made to follow both the current pitch and octave of the closest matching note.
setlength | <list of [Musical fraction](#parameter-types): **1/16**> | Sets the length of all notes to the specified value(s). When more values are specified, they are cycled through.
setrhythm | -by <[Clip reference](#parameter-types)> | Retains pitch and velocity from the current clip while changing the timing and duration to match the clip specified in the -by parameter.
shuffle | <list of [Number](#parameter-types)> -by <[Clip reference](#parameter-types)> | Shuffles the order of notes by a list of numbers of arbitrary length, or by another clip. When another clip is specified, the relative pitch of each note is used to determine the shuffle order.
skip | <list of [Number](#parameter-types): **2**> | Creates a new clip by skipping every # note from another clip. If more than one skip value is specified, they are cycled through.
slice | <list of [Musical fraction](#parameter-types): **1/16**> | Slices a clip (i.e. cutting any notes) at a regular or irregular set of divisions.
take | <list of [Number](#parameter-types): **2**> | Creates a new clip by taking every # note from another clip. If more than one skip value is specified, they are cycled through.
transpose | <list of [Number](#parameter-types)> -by <[Clip reference](#parameter-types)> -mode **Absolute**&#124;Relative&#124;Overwrite | Transposes the notes in a clip based on either a set of passed-in values, or another clip.

## Parameter types

Type | Description
--- | ---
Clip reference | Cells in the session view are referenced like they would be in a spreadsheet, i.e. tracks are assigned letters (A-Z) and clip rows are assigned numbers (1-N). Example: track 1 clip 1 becomes A1, track 2 clip 3 becomes B3.
Musical fraction | These are commonly used in sequencer software to denote musical fractions like quarter notes, eight notes and so on. Examples: quarter note = 1/4, eighth note = 1/8.
Number | Whole number (integer), either negative or positive
Decimal number | Decimal number, from 0.0 and upwards
