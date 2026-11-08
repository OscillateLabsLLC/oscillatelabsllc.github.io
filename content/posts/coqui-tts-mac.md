---
title: "Coqui-TTS on an M1/M2 Mac"
author: "Mike Gray"
date: 2023-05-17T23:25:26-05:00
draft: false
type: post

slug: coqui-tts-mac # Will take the filename as the slug.

# Lists
keywords: ["homelab", "machine learning", "text-to-speech", "tts", "coqui-tts"]
tags: ["homelab", "machine learning", "text-to-speech", "tts", "coqui-tts"]
categories:
  ["homelab", "machine learning", "text-to-speech", "tts", "coqui-tts"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. Coqui, the company behind Coqui-TTS, shut down in early 2024. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

![Coqui-TTS](/images/coqui.png)

Coqui-TTS is an open-source text-to-speech engine. It's a great alternative to proprietary options like Google's TTS. It's also a great way to use local TTS for your voice assistants, announcements to your home automation system, or even just to read eBooks aloud.

Officially, [Coqui-TTS does not support Apple Silicon chips](https://github.com/coqui-ai/TTS/issues/2052#issuecomment-1273044171). However, it is possible to get it running on an M1/M2 Mac. This post will walk you through the steps to get it running.

This post assumes you are comfortable with the command line and have some familiarity with Python and virtual environments. Since some of the required packages are challenging to install on an M1/M2 Mac (they don't offer wheels for ARM64), using [Conda](https://docs.conda.io/en/latest/) is the easiest way to get everything installed nicely. We'll also use [Homebrew](https://brew.sh/) to install some other dependencies.

These commands are not fully automated - several steps are interactive.

## Installing Homebrew (if you don't already have it)

As of the time of this writing (May 2023), this is how to install Homebrew. Be sure to check the [Homebrew website](https://brew.sh/) for the latest instructions.

```zsh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## Conda Setup

```zsh
# Install Conda
mkdir ~/coqui && cd ~/coqui # Or wherever you want to install Conda/Coqui
curl https://repo.anaconda.com/miniconda/Miniconda3-latest-MacOSX-arm64.sh -o miniconda3.sh
chmod +x ./miniconda3.sh
./miniconda3.sh
```

At this point you'll want to go through the interactive installer. It is fairly straightforward. After installation, run `source ~/.zshrc` to get Conda in your path. This step is necessary to continue.

## Installing Coqui and its dependencies

```zsh
conda create --name coqui python=3.10 # Then hit y and enter to accept
conda activate coqui
git clone https://github.com/coqui-ai/TTS.git
# Install requirements
brew install mecab espeak
pip install numpy==1.21.6
conda install scipy scikit-learn Cython
# Install Coqui-TTS
cd TTS
make install
```

Now you can follow the normal documented steps to use your speech-to-text engine! I personally like to us the `tts-server` that comes with it to set up a webserver, using this voice model: `tts-server --model_name tts_models/en/ljspeech/vits`. It will spin up a dev Flask server on your machine at port 5002, which you can access via your browser at http://localhost:5002.

Note that since Coqui doesn't officially support Apple Silicon, you can't take advantage of the GPU on that chip, but the CPU is still plenty fast. Enjoy! Once I get it working with launchd, I'll post a follow-up on how to run this as a system service. That way you can use it with your voice assistant or home automation system.
