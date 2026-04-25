# What is a Note?

When asked this question, your first response might well be visual: a **♩ symbol** on a five-line staff. After all, we are highly visual beings. But closely after that, you may have pictured the **sound** of an instrument playing a single note, this piano for instance...

![audio/mpeg](../assets/audios/C3.mp3)

This is what we will focus on: a musical note as a physical object, a sound wave propagating through the air, whose pressure variations will be transmitted by our eardrums. A sound.

## Stripping down the note

There are still many elements within this sound wave. First there is the *attack*, the way the sound begins and the sometimes abrupt transition from silence to the note; then the *loudness*, the overall volume of the sound; finally the *decay*, the generally much more gradual decrease in volume back to zero.

All of this forms what is called the **envelope** of the note, and if you cancel it out, you get a stable, sustained sound, which would correspond to a perpetually vibrating piano string:

![audio/mpeg](../assets/audios/C3_norm.mp3)

Then there is what makes the note *sound like* a piano, the instrument's characteristic texture or color, which allows you to distinguish it from one played by an oboe or sung by a human voice. This part is called the **timbre**, and we have to take a much closer look at the sound wave to understand where it comes from. Here is what 5/100 of a second of our sustained piano sound wave look like:

```python exec="1" session="1" html="1"
from io import StringIO

import numpy as np
import matplotlib.pyplot as plt

import soundfile as sf

audio, sampling_frequency = sf.read("docs/assets/audios/C3_norm.mp3")
if audio.ndim == 2:
    audio = audio.mean(axis=1)
    
n_points = len(audio)
times = np.arange(n_points)/sampling_frequency

figure = plt.figure(figsize=(20, 5))
ax = figure.gca()
ax.plot(times, audio, color='r', lw=3, alpha=0.5)

ax.set_xlim(1, 1.05)
ax.set_xticks([])
ax.set_ylim(-np.abs(audio).max(), np.abs(audio).max())
ax.set_yticks([])

figure.tight_layout()

buffer = StringIO()
plt.savefig(buffer, format="svg")
print(buffer.getvalue())
```

It is all these small variations in the sound wave, specific to the instrument, that give it its unique character. Physically speaking, they correspond to *frequencies* of the sound wave, and it is the way an instrument shapes all the frequencies (the "spectrum") that textures the sound. Among all those frequencies, one is more prominent than all the others; if we keep only this one, we get a perfect sine wave:

```python exec="1" session="1" html="1"
import numpy as np
from scipy.fft import fft, fftshift, ifft, ifftshift

F = fft(audio)
mask = np.zeros_like(F)
mask[1:][np.argsort(np.abs(F[1:]))[-2:]] = 1
filtered_audio = ifft(F * mask).real

figure = plt.figure(figsize=(20, 5))
ax = figure.gca()
ax.plot(times, filtered_audio, color='r', lw=3, alpha=0.5)

ax.set_xlim(1, 1.05)
ax.set_xticks([])
ax.set_ylim(-1.2*np.abs(filtered_audio).max(), 1.2*np.abs(filtered_audio).max())
ax.set_yticks([])

figure.tight_layout()

buffer = StringIO()
plt.savefig(buffer, format="svg")
print(buffer.getvalue())
```

This core frequency defines the **pitch** of the sound, and it is what determines whether you hear a higher or lower note. We can see a musical instrument as an object that turns a single frequency like this one into a rich, recognizable sound. But for the purposes of studying harmony theory, when we speak of notes from now on, *we will only consider their pitch*; we'll treat them as a pure, stable sine waves composed of a single frequency. However, the sound of such a note would likely feel hollow or even unpleasant:

![audio/mpeg](../assets/audios/C3_pitch.mp3)

Therefore, when illustrating with sound samples, we will continue to use notes *played by an instrument*, even if we only have their pitch in mind.

## From frequencies to notes

Still, a pure sinusoidal sound wave could take an infinite number of possible frequencies: pitch lies within a *continuous* domain. But this is not the case for notes, which come in a rather limited number of well-defined frequencies. The question that naturally arises is then [how did we choose those frequencies and their number](why_12_notes.md), and how did we arrive the set of notes we all know?
