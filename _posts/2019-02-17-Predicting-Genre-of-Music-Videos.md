---
layout: post
Title: Predicting Genre of Music Videos
---

## Introduction

For my third project at Metis I was required to explore classification techniques. Coming from a background in music, I thought that it may be interesting to see how machine learning can be applied to audio. I was specifically interested in how styles of music could be differentiated from on another. I ultimately decided to build a web app that would take in a YouTube link and spit out a predicted genre along with its associated certainty.

Here's an example of its performance classifying a selection from the oeuvre of Smash Mouth: All-Star. 

![Smash Mouth Prediction](images/predictor_app_smashmouth.png)


## The Free Music Archive

I found an excellent resource to help me in this endeavor: https://github.com/mdeff/fma. This repository contains code that constructs a database extracted features from audio files collected from [The Free Music Arhive](http://freemusicarchive.org). The Free Music Archive is a website where amateur and professional musicians can upload their work under a creative commons license. A consequence of this open liscensing is that researches can be confident that using these tracks for research purposes and releaseing the results does not run afould of copyright law. 

The database contains information about over 100,000 tracks spanning many dozens of genres. I decided to only focus on 5 genres: Rock, Hip-Hop, Folk, Electronic, and Classical. I excluded genres such as Experimental and Pop because I believed these to be less well defined. I also excluded genres that were not very well represented in the FMA database, such as Bebop or Bluegrass.

This work was all performed by the researchers: Michaël Defferrard, Kirell Benzi, Pierre Vandergheynst, and Xavier Bresson. The stated purpose of the database is to serve as a benchmark dataset for genre recognition in a similar way that MNIST is the benchmark for digit classification. An explanation of their fantastic project can be found in their [arXiv paper](https://arxiv.org/abs/1612.01840).

## MIR and Feature Retrieval

In an effort to comprehend the work that went into constructing this dataset, I ended up diving into the deep-end of the field called Music Information Retrieval. Significant preprocessing is necessary to prepare an audio file for a machine learning algorithm. Many techniques have been developed for the purpose of  extracting features from audio. I wish to breifly touch upon a few of these techniques.

One of the most popular techniques is the Fourier Transform. Essentially, the Fourier Transform extracts the pitches in a song from the raw audio. It takes a function that describes a wave (e.g. pressure vs time, a sound wave), and translates this to a amplitude vs frequency space. This allows us to look at the frequencies that make up a song  (and their relative prevalence) specifically. We can further look at the Short-time Fourier transform, which is performed by cutting the audio signal into small segments, usually on the order of a second or less, and performing a Fourier transform on each segment. In this manner, a spectrogram can be produced. Once we have the spectrogram, some interesting features can be derived, such as the Mel-frequency Cepstrum Coefficients or the Chromagram STFT.

An additional features that can be extracted is the zero crossing rate, that is the rate at which the amplitude passes from positive to negative. The zero crossing rate can be considered an estimate for the fundamental frequency of a sound-wave. Intuitively, we can approximate the fundamental frequency as a sinusoid having a fequency equalling that of the zero-crossing rate. This zero-crossing rate can vary across time, representing a changing fundamental frequency, an approximation of which can be obtained by taking the zero-crossing rate for small segments, similar to the Short-time Fourier Transform.

There are many more potential features to be extracted from the audio file (including, but not limited to) [tonnetz features](https://en.wikipedia.org/wiki/Tonnetz) and [Root Mean Squared Energy](https://musicinformationretrieval.com/energy.html).

## The Flask App

As an application for my model, I decided to build a flask app that can tell what genre a music video is. I utilized the [pytube library](https://github.com/nficano/pytube) to download youtube videos. I then used the [LibROSA library](https://librosa.github.io/) to extract the features from the song. I trained a model on the Free Music Archive dataset using logistic regression with a one-versus-rest technique to determine the genre of the song. The accuracy of this practice is 80% within the dataset, but I would be interested in doing some tests on how it performs on YouTube specifically. 

I plan on updating this blog post with a link to the web-app when I host it, so stay tuned for that. You can see a screenshot of the app in action at the top of this post.



