---
layout: post
title: "Separate instrument tracks from a song"
description: "How to separate instrument tracks from a song into vocals, drums, bass, and other."
author: tom
image: assets/images/1.jpg
tags: [python, machine learning]
comments: true
---

How to separate instrument tracks from a song into vocals, drums, bass, and other.

**NOTE**: For research purposes only. Use at your own risk.

I study/read/work with soundtracks. That's the best way I can focus. I cannot concentrate listening to music that has vocals. I've been wondering what is like to listen to other music I like without the vocals.

I wonder what Daft Punk sounds without vocals. I know. Most songs by Daft Punk are already instrumental.

I found this repo with a list of ML projects. See [here](https://github.com/dvschultz/ml-art-colabs)

One of them is a project called `demucs`, used to separate tracks from songs. More about the project [here](https://pypi.org/project/demucs/)

## Installing demucs

Install using this:

    pip install demucs
    
The output shows that a lot of dependencies need to be installed:

* demucs
* diffq>=0.2.1
* dora-search
* einops
* julius>=0.2.3
* lameenc>=1.2
* openunmix
* torch>=1.8.1 (this was the largest with approx 900MB)
* torchaudio>=0.8
* tqdm
* Cython
* numpy
* nvidia-cudnn-cu11==8.5.0.96 (also a big one 500MB)
* nvidia-cublas-cu11==11.10.3.66 (300MB)
* nvidia-cuda-nvrtc-cu11==11.7.99
* nvidia-cuda-runtime-cu11==11.7.99
* retrying
* treetable
* omegaconf
* submitit
* antlr4-python3-runtime==4.9.*
* cloudpickle>=1.2.1

More output:

	Successfully built demucs julius dora-search antlr4-python3-runtime treetable
	Installing collected packages: lameenc, antlr4-python3-runtime, treetable, tqdm, retrying, omegaconf, nvidia-cuda-runtime-cu11, nvidia-cuda-nvrtc-cu11, nvidia-cublas-cu11, numpy, einops, Cython, cloudpickle, submitit, nvidia-cudnn-cu11, torch, torchaudio, julius, dora-search, diffq, openunmix, demucs
	Successfully installed Cython-0.29.33 antlr4-python3-runtime-4.9.3 cloudpickle-2.2.1 demucs-4.0.0 diffq-0.2.3 dora-search-0.1.11 einops-0.6.0 julius-0.2.7 lameenc-1.4.2 numpy-1.24.2 nvidia-cublas-cu11-11.10.3.66 nvidia-cuda-nvrtc-cu11-11.7.99 nvidia-cuda-runtime-cu11-11.7.99 nvidia-cudnn-cu11-8.5.0.96 omegaconf-2.3.0 openunmix-1.2.1 retrying-1.3.4 submitit-1.4.5 torch-1.13.1 torchaudio-0.13.1 tqdm-4.64.1 treetable-0.2.5

## Running demucs

Run with this:

    demucs path-to-file

Output running the program

	Important: the default model was recently changed to `htdemucs` the latest Hybrid Transformer Demucs model. In some cases, this model can actually perform worse than previous models. To get back the old default model use `-n mdx_extra_q`.
	Downloading: "https://dl.fbaipublicfiles.com/demucs/hybrid_transformer/955717e8-8726e21a.th" to /home/tom/.cache/torch/hub/checkpoints/955717e8-8726e21a.th
	80.2M/80.2M
	Selected model is a bag of 1 models. You will see that many progress bars per track.
	Separated tracks will be stored in /home/tom/Music/separated/htdemucs

Waiting...

It took 25 minutes to run an `8MB` mp3 song.

## Reviewing the output

Only for research purposes I used the song "Touch" by Daft Punk.

The result was four `.wav` tracks named `bass`, `drums`, `other`, and `vocals`. Each one was `88MB`.

Loaded them into Audacity.

The result is wow. Amazing.

Listening to each individual track. They are almost like different songs.

Only bass and drums. Or bass and vocals. Or bass and other. You could make a whole album mixing this song in different ways.

The `vocals` were removed from the beginning, end, and when you can clearly hear the singer. In the end, you only hear the piano. The vocals couldn't be removed in the middle when there is a mix of human/robot singing. The angelic vocals were almost removed. You can hear them very quietly in the background. The vocals track also has the sound of wind blowing in the beginning.

## Conclusion

The `demucs` program is a product of Facebook Research. See the repo [here](https://github.com/facebookresearch/demucs). It has a research paper with all the proper complicated math that requires superhuman understanding. It did an amazing job with the song I tried.