# PipaSound Dataset

A small collection of audio clips I recorded and gathered to train an on-device
sound classifier for my Pipa practice app — a personal exploration of audio
machine learning on iOS. The classifier acts as a gate: it decides whether the
microphone is hearing a Pipa, another instrument, speech, or just background
noise, so the app only runs pitch and fingering analysis on actual Pipa playing.

This repository holds the **training audio** behind that model
(`PipaSoundClassifier.mlmodel`). It is shared so the experiment is reproducible
and the recording process is documented.

## What is inside

Four folders, one per class:

| Folder             | What it contains                                                              | Approx. size |
| ------------------ | ----------------------------------------------------------------------------- | ------------ |
| `pipa/`            | My own Pipa playing — practice phrases, open strings, scales, short passages  | ~40 MB       |
| `other_instrument/`| Recordings of other instruments — wind/brass, drum kit, electric & acoustic guitar, zhongruan, piano and voice, plus older clips like `Battle Hymn.mp3`, `Chinese mid-autumn festival (1).mp3`, `吉他01.m4a`. Ten of the clips (drum / guitar / zhongruan / voice+piano) were captured as video and kept as `.m4a` files that carry **both** an AAC audio track and a video track re-encoded to 640x480, so the performance stays visible as well as audible. | ~445 MB      |
| `speech/`          | Spoken voice — my own speech and counting used as a contrast to Pipa timbre   | ~29 MB       |
| `background/`      | Room ambience, silent gaps, and everyday environmental noise                  | ~4 MB        |

All clips are either self-recorded by the author or cleared for public sharing
by their respective recordists. Total: 34 clips (~520 MB), in `.m4a` / `.mp3`.

The ten video-derived clips are stored with Git LFS. Their video tracks are
downscaled to 640x480 to keep the repository manageable — they are kept for
documentation (showing that the recordings really are performances on those
instruments), while training consumes only the audio, downsampled to 16 kHz.

## How the model uses it

The classifier is a Core ML model (`PipaSoundClassifier.mlmodel`) that performs
a **4-way classification**: `pipa / other_instrument / speech / background`.

Preprocessing applied before training (and at inference time in the app):

- Capture at the device sample rate (typically 48 kHz), mixed to mono.
- Software down-sample to **16 kHz** (linear interpolation).
- Slice into **15,600-sample windows (~0.975 s)**, advanced with **50% overlap**.
- Feed the raw waveform (not spectrogram features) to the classifier.

The released model window is 15,600 frames at 16 kHz, matching the clips here.

## Reproducing the training

1. Place the four class folders as shown above.
2. Apply the preprocessing: mono, 16 kHz, 15,600-sample windows at 50% overlap.
3. Train a 4-class audio classifier (e.g. Create ML's Sound Classifier, or an
   equivalent small CNN on raw waveforms) with the four labels
   `pipa`, `other_instrument`, `speech`, `background`.
4. Export to Core ML as `PipaSoundClassifier.mlmodel`.

The app pairs this gate with a Vision-based fingering classifier (pluck / roll /
sweep) and audio analysis to give feedback during practice.

## License

Released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
See `LICENSE`.

---

*Personal project — grown out of my own Pipa practice, still evolving.*
