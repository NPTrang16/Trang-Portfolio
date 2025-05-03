![image](https://github.com/user-attachments/assets/6fb482aa-9cbc-4098-9adf-b05b86d4bd4d)

# Introduction 
I started playing the piano at the age of 6. Although I took a 6 year break, I'm now more passionate than ever. With the scientific knowledge I've gained throughout my education, I’ve started to wonder if I can understand music in mathematical terms. <br/>
<br/>
This study was made on Jupyter Notebook.

## What's a MIDI file?
To get started, in music, a MIDI file (Musical Instrument Digital Interface) is a type of file which contains information about a song—such as which notes to play, their pitch (how high or low they are), and their velocity (how long or short they are played). Unlike MP3 or WAV files, MIDI files don’t contain actual audio. Instead, they store performance data, acting more like sheet music for digital instruments. <br/>
<br/>
Today, I chose to work with a piano MIDI file of the main theme from the movie **Pirates of the Caribbean**, titled _He's a Pirate_. The piece was composed by Geoff Zanelli, Hans Zimmer, and Klaus Badelt and [beautifully covered by nanoMIDI](https://www.youtube.com/watch?v=iEO9WZgYj7s) on YouTube. <br/> 
<br/> 
Here's what its MIDI file sounds like: [Listen to MIDI He's a Pirate](https://github.com/NPTrang16/Audios/blob/main/pirate.mid)

## An overview of Mido
Mido, short for MIDI Objects, is a Python library designed to work with MIDI files. It translates familiar music notes like Do, Re, Mi into numbers, turning them into data that can be read and processed. For instance, Do in the 4th octave of a standard piano is represented by the number 60.<br/> 
<br/> 
![image](https://github.com/user-attachments/assets/0c8d02cc-a628-4eef-9286-201f3eac0edb)

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

pirate_song = mido.MidiFile('pirate.mid')
````
## Step 2: Creating a list containing all the music notes

````
notes = []
for track in pirate_song.tracks: #run through each tracks
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
Output: [(50, 106)]
````
The most common note is 50 (Re# in the 4th octave). It appeared 116 times throughout the 1 minute and 22 seconds song.

````
print(int(np.min(notes_array)))
print(int(np.max(notes_array)))
Output: 21
Output: 94
````
The lowest note of the piece is 21 (La in the 0th octave.). The highest one is 94 (Sol# in the 6th octave).
````
print(int(np.mean(notes_array)))
print(int(np.std(notes_array)))
Output: 56
Output: 15
````
The average note is 56 (Sol♯ in the 4th octave). <br/> 
The standard deviation is 16, ranging in average from the note 40 (Mi in the 2nd octave) to 72 (Do in the 5th octave).<br/> 
<br/> 
With those informations, we can map out a first draft of the Normal Distribution. Indeed, the amplitude isn't determined yet. It will be studied later.

## Step 6: Creating a histogram
We will use Matplotlib to create a histogram.
````
import matplotlib.pyplot as plt
plt.hist(notes_array, bins=50)
````
<img width="362" alt="image" src="https://github.com/user-attachments/assets/0f0789ad-4c92-48e4-8975-ec5ca0b53c22" /> <br/> 
<br/> 
`bins=50` is chosen for visual reasons.

## Step 7: Fitting a Normal Distribution curve onto the histogram
````
from scipy.optimize import curve_fit
def gaussian(x, amp, mean, std): #define the Gaussian function
    return amp * np.exp(-(x - mean)**2 / (2 * std**2)) 
````
After defining the Gaussian function, several steps are performed to create the best-fitting and most optimized normal curve:<br/>
- Calculate the center of each bin. <br/>
````
bin_centers = np.mean([bins[:-1], bins[1:]], axis=0)
````
- Provide starting parameters to the Matplotlib algorithm. <br/>
````
amp_guess = 1
mean_guess = np.mean(notes_array)
std_guess = np.std(notes_array)
````
- Let the algorithm find the best parameters. <br/>
````
popt, _ = curve_fit(gaussian, bin_centers, n, p0=[amp_guess, mean_guess, std_guess])
````
- Generate x and y values of the curve. <br/>
````
x_fit = np.linspace(min(bin_centers), max(bin_centers), 1000)
y_fit = gaussian(x_fit, *popt)
````
<img width="380" alt="image" src="https://github.com/user-attachments/assets/22294810-162b-47db-96e9-b86dcc126151" /> <br/> 
<br/> 
And voilà! That’s how we can visualize a music sheet on a mathematical graph using Python.
## Remarks
It is important to note that not every songs follow a Normal distribution. For example, I've tried _Cornfield Chase_, the main theme of one of my favorite movies **Interstellar**, and Matplotlib was unable to find a curve as it stays flat on the x axis. <br/>
<img width="364" alt="image" src="https://github.com/user-attachments/assets/3a091ff3-1553-46e4-9564-e280b5c5b69d" />
