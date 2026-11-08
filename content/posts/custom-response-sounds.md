---
title: "Custom Response Sounds For OVOS/Neon"
author: "Mike Gray"
date: 2025-04-04T11:58:58-04:00
draft: false
type: post

slug: custom-response-sounds
description: "Configuring custom response sounds for OVOS/Neon Personal Private Voice Assistants."
thumbnail: "images/response.webp"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

[Neon.AI](https://neon.ai) and [OpenVoice OS (OVOS)](https://openvoiceos.com/) both offer out-of-the-box smart speaker/voice assistant platforms, with Neon using OVOS as a base for their own implementation. Both platforms are designed to be privacy-respecting and run on low-power hardware, making them ideal for home automation and personal voice assistant projects. They are also both designed to be extensible, allowing you to add your own customizations and features.

One of the most fun customizations you can make to your OVOS/Neon voice assistant is to change the response sounds. By default, OVOS and Neon use a set of standard sounds for various events, but you can easily replace these with your own custom sounds. This can be a fun way to personalize your voice assistant and make it feel more like your own.

In this blog post, we'll walk through a method shared by Matrix user Lord Ivanhoe for configuring custom response sounds for OVOS/Neon, specifically using generated TTS files. This method is simple and effective, allowing you to easily customize the sounds your voice assistant uses for various events.

## How it works

First, you'll need to create a directory for your custom sounds. This can be done anywhere on your system, but for the sake of this example, we'll create a directory called `custom_sounds` in your home directory.

```bash
mkdir ~/custom_sounds
```

Next, you'll need to create one or more TTS wav files for the sounds you want to use. For this example we'll focus on spoken responses using the assistant's TTS voice. You can use either `ovos-say-to "<text>"` (OVOS) or `mana say-to "<text>"` (Neon) to generate the TTS files. Once they've been generated, you can find them in the `~/.cache/<mycroft|neon>/tts/<your_tts_engine>/` directory - it may require a little digging to find it. Identify and copy the files you want to use into your `custom_sounds` directory.

```bash
cp ~/.cache/neon/tts/MozillaRemoteTTS/en-us/female/076a246442790ce54f016254bff985f1.wav ~/custom_sounds/ack1.wav
```

Alternately, you can use any TTS or audio service to generate the sounds you want. Just make sure they are in the correct format (wav) and are named in a way you can easily identify them.

## Configurating A Single Sound

Next, you'll need to configure OVOS/Neon to use your custom sounds. This is done by editing user configuration files. For OVOS this is located in `~/.config/mycroft/mycroft.conf`, and for Neon it's located in `~/.config/neon/neon.yaml`. Open the appropriate file in your favorite text editor and add the following configuration:

OVOS:

```json
{
  "start_listening": "/home/ovos/custom_sounds/ack1.wav"
}
```

Neon:

```yaml
start_listening: "/home/ovos/custom_sounds/ack1.wav"
```

This will configure OVOS/Neon to use the `ack1.wav` file as the sound for the `start_listening` event. You can replace `ack1.wav` with any other sound file you want to use.

After changing your configuration it should automatically reload, but if it doesn't, you can restart your listener service to apply the changes.

## Configuring Multiple Sounds

As of ovos-dinkum-listener version 0.4.0a1, OVOS officially supports multiple sound options for the same event. The configuration is the same as above, but you can specify multiple sounds in a list. For example, to configure multiple sounds for the `start_listening` event, you would use the following configuration:
OVOS:

```json
{
  "start_listening": [
    "/home/ovos/custom_sounds/ack1.wav",
    "/home/ovos/custom_sounds/ack2.wav"
  ]
}
```

Neon:

```yaml
start_listening:
  - "/home/ovos/custom_sounds/ack1.wav"
  - "/home/ovos/custom_sounds/ack2.wav"
```

Note that Neon will take some time to support this version of the listener, but eventually it will be available.

After changing your configuration it should automatically reload, but if it doesn't, you can restart your listener service to apply the changes.

## Conclusion

A big thank you to Lord Ivanhoe for sharing this method with the community! This is a simple and effective way to customize the response sounds for your OVOS/Neon voice assistant. By using TTS-generated sounds, you can easily create a personalized experience that reflects your own style and preferences.

Questions? Comments? Feedback? Let me know on the [Open Conversational AI Community Forums](https://community.openconversational.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
