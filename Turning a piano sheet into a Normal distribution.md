# Introduction 
I started playing the piano at the age of 6. Although I took a 6 year break, I'm now more passionate than ever. With the scientific knowledge I've gained throughout my education, I’ve started to wonder if I can understand music in mathematical terms.

## What's a MIDI file?
To get started, in music, a MIDI file (Musical Instrument Digital Interface) is a type of file which contains information about a song—such as which notes to play, their pitch (how high or low they are), and their velocity (how long or short they are played). Unlike MP3 or WAV files, MIDI files don’t contain actual audio. Instead, they store performance data, acting more like sheet music for digital instruments. <br/>
<br/>
Today, I chose to work with a piano MIDI file of the main theme from one of my favorite movies, **Interstellar**, titled _Cornfield Chase_. The piece was composed by Hans Zimmer and [beautifully covered by Cosmic PIANO](https://www.youtube.com/watch?v=57SdNVHYdNk) on YouTube. <br/> 
<br/> 
Here's what its MIDI file sounds like: [Listen to MIDI Cornfield Chase](https://github.com/NPTrang16/Audios/blob/main/Cornfield%20Chase.mid)

## An overview of Mido
Mido, short for MIDI Objects, is a Python library designed to work with MIDI files. It translates familiar music notes like Do, Re, Mi into numbers, turning them into data that can be read and processed. For instance, Do in the 4th octave of a standard piano is represented by the number 60.<br/> 
<br/> 
In Mido, a message is an instruction for a note. Here's an example:

```
>>> import mido
>>> msg = Message('note_on', note=60) 
>>> print(msg)
Output: note_on channel=0 note=60 velocity=64 time=0
```
What I just did was create a MIDI message and print out its general information:<br/> 
- **note type**: whether the note is turned on or off.
- **channel**: represents the instrument track — for example, channel 0 is usually piano, channel 1 might be guitar, and so on.
- **velocity**: how strongly the note is played, ranging from 0 to 127. If not specified, it defaults to 64.
- **time**: the delay (in ticks) before the note is played.<br/> 
<br/> 
Those attributes can be accessed individually:<br/> 
<br/>

````
>>> msg.type
Output: 'note_on'
>>> msg.note
Output: 60
>>> msg.velocity
Output: 64
````

## Step 1: Importing the MIDI file to Python

````
import mido
import numpy as np
from collections import Counter

cornfield = mido.MidiFile('cornfield.mid')
````
## Step 2: Creating a list containing all the music notes

````
notes = []
for track in cornfield.tracks: #run through each tracks
  for msg in track: #run through each notes
    if msg.type == 'note_on' and msg.velocity > 0:
      notes.append(msg.note)
````
## Step 3: Converting the list into a NumPy array
````
notes_array = np.array(notes)
````
Here, if we print out the array, we’ll get a list of numbers representing each note. In the next step, using the `Counter` function, we’ll be able to see how many times each note is played throughout the song.

## Step 4: Counting notes
````
notes_count = Counter(notes_array)
````
## Step 5: Finding the most common note, minimum/maximum notes, average note and the standard deviation
````
most_common = notes_count.most_common(1)
print(most_common)
Output: [(76, 116)]
````
The most common note is E5 (Mi in the 5th octave). It appeared 116 times throughout the 2 minutes and 3 seconds song.

````
print(int(np.min(notes_array)))
print(int(np.max(notes_array)))
Output: 28
Output: 88
````
The lowest note of the piece is 28 (Do# in the 2nd octave). The highest one is 88 (Sol# in the 5th octave).
````
print(int(np.mean(notes_array)))
print(int(np.std(notes_array)))
Output: 65
Output: 15
````
The average note is 65 (Fa in the 4th octave). The standard deviation is 15, ranging in average from the note 50 (Re# in the 4th octave) to 80 (Sol in the 5th octave).<br/> 
<br/> 
With those informations, we can map out a first draft of the Normal Distribution:

## Step 6: Creating a histogram
