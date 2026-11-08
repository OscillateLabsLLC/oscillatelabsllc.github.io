---
title: "Using Coqui-TTS on Neon and OVOS"
author: "Mike Gray"
date: 2023-05-18T00:06:50-05:00
draft: false
type: post

slug: coqui-tts-neon-ovos # Will take the filename as the slug.

description: "Changing the TTS engine in the NeonAI Personal Voice Assistant to Coqui-TTS."

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. Coqui, the company behind Coqui-TTS, shut down in early 2024. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

[Neon.AI](https://neon.ai) and [OpenVoice OS (OVOS)](https://openvoiceos.com/) both offer out-of-the-box smart speaker/voice assistant platforms, using a combination of their own aggregated text-to-speech (TTS) and speech-to-text (STT) hosted options as well as low-power open source engines in case the internet is not available. While both companies go out of their way to be as privacy-respecting as possible, ultimately I don't want my voice assistant to be sending my voice to a server somewhere else on the internet or sharing the text that will be spoken aloud. I want it to be as private as possible, and I want to be able to use it even if my internet is down.

[Coqui-TTS](https://github.com/coqui-ai/TTS) is my favorite current open-source TTS option. It has the capability to train your own voice models or use pre-trained models that balance quality and size. It's fast enough on a CPU, with certain models, to use in a voice assistant. So, how do we get it working on Neon and OVOS?

## Prerequisites

This post assumes you already have a Coqui-TTS server running somewhere locally. If you don't, check out [this post](/posts/coqui-tts-mac/) on how to get it running on an M1/M2 Mac. You can also run it on an x86 Linux machine with a systemd service, or even on WSL.

If you have a server running on Linux but no systemd service yet, here's an example service file:

```ini
[Unit]
Description=Coqui-TTS

[Service]
ExecStart=tts-server --model_name tts_models/en/ljspeech/vits
Restart=always

[Install]
WantedBy=multi-user.target
```

You'll also need to have an existing Neon or OVOS setup, either on specialized smart speaker hardware or some custom hardware like a Raspberry Pi or x86 Mini PC.

## Required plugins

If you're running Neon, the plugin is pre-installed (as of May 2023). However, if you're running OVOS or just want to be sure with Neon, SSH to your device and run `pip install neon-tts-plugin-mozilla-remote`. This will install the plugin that allows Neon to use a remote TTS server, as opposed to on-device Coqui-TTS. On a containerized setup, add `neon-tts-plugin-mozilla-remote` to your `docker/config/neon.yaml` file (Neon) or `~/ovos/config/mycroft.conf` file (OVOS).

## Configuring Neon/OVOS

### Neon

If you're running Neon, you'll need to edit your `~/.config/neon/neon.yaml` file.

```yaml
tts:
  module: mozilla_remote
  mozilla_remote: { "api_url": "http://YOUR_SERVER_IP:5002/api/tts" }
```

### OVOS

If you're running OVOS, you'll need to edit your `~/.config/mycroft/mycroft.conf` file and add this to the root of the object.

```json
{
  "tts": {
    "module": "mozilla_remote",
    "mozilla_remote": { "api_url": "http://YOUR_SERVER_IP:5002/api/tts" }
  }
}
```

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org).
