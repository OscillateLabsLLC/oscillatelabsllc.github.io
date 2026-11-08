---
title: "Neon Custom Wakeword"
author: "Mike Gray"
date: 2023-05-14T15:37:17-07:00
draft: false
type: post

slug: neon-custom-wakeword # Will take the filename as the slug.

description: "Using a custom wakeword in the NeonAI Personal Voice Assistant."
thumbnail: "images/robot.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

[Neon.AI](https://neon.ai) is a channel partner and software alternative for [Mycroft AI](https://mycroft.ai), an open-source voice assistant. Since [Mycroft's announcement that they will be closing software operations](https://mycroft.ai/blog/update-from-the-ceo-part-1/), Neon has taken over those responsibilities for the Mycroft private personal voice assistant ecosystem.

Neon is considered the consumer option for the current ecosystem of voice assistants, which also includes Mycroft Classic Core (unmaintained and several years out of date), Mycroft Dinkum (not compatible with the existing ecosystem of Mycroft skills, only functions on Mycroft Mark II hardware, and introduces several breaking changes that are incompatible with other voice assistant platforms), and [OpenVoice OS (OVOS)](https://openvoiceos.com/). Neon is built on top of OVOS, which is currently geared towards developers or those using the KDE BigScreen voice assistant option.

## Using a custom wakeword

The Neon platform [comes with several wakewords by default](../neon-change-wakeword), but if you want to use something other than those three defaults, there are options available.

### Vosk

Vosk wakewords are constructed using phonemes and do not require a pre-trained ML model to operate. All you have to do is provide a configuration at `~/.config/neon/neon.yaml`:

```yaml
hotwords:
  computer: # The wakeword should go here. Multiple words should be combined with an underscore, like hey_computer
    module: ovos-ww-plugin-vosk
    active: true
    sound: snd/start_listening.wav
    debug: false
    rule: fuzzy
    lang: en
    samples:
      - computer # Start with one
      # - sample1
      # - sample2
```

That's all that's needed for a Vosk wakeword. Keep in mind that you will probably need a few samples as you experiment with your wakeword. Neon comes with a skill that displays what it heard when it isn't sure how to handle it. Take that output and add it to your samples, removing the `# ` from the example above and replacing `sample1` or `sample2` with your samples.

Also, consider that Vosk works based on interpreting phonemes, so try to pick a unique combination of sounds but not too complex.

## Precise-Lite

Precise-Lite wakewords require a pre-trained `.tflite` model to operate. Training a model of your own is beyond the scope of this blog post, but [OVOS maintains several pre-trained models of commonly requested wakewords](https://github.com/OpenVoiceOS/precise-lite-models/tree/master/wakewords/en). To use them, try this configuration:

```yaml
hotwords:
  computer:
    module: ovos-ww-plugin-precise-lite
    sound: snd/start_listening.wav
    active: true
    expected_duration: 3
    trigger_level: 3
    sensitivity: 0.5
    model: "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/computer.tflite"
```

Replace `computer` in the configuration above with a different model if you'd like. This is only an example of one option.

If you're setting up a `precise-lite` model wakeword, be sure to run `pip install ovos-ww-plugin-precise-lite[tflite]` first. This is especially important on Neon Mark 2 images.

## Caveats

[As mentioned in the original blog post](/posts/neon-change-wakeword/), the configuration for Neon is constructed from multiple locations and overrides don't always behave as expected. You may need to include your custom wakeword along with all of the other default wakewords, and set the default wakewords to `active: false`.

(EDIT) One of the developers at Neon had a brief correction: "...you only need to add `active: false` to disable the default WW; `listen` shouldn't be evaluated if the WW is marked as not `active`."

## OVOS

The same settings you see above should also work in OVOS! Expect another blog post detailing the defaults and nuances of OVOS, but for now, try editing `~/.config/mycroft/mycroft.conf` with the following:

### Vosk

```json
{
  "hotwords": {
    "computer": {
      "module": "ovos-ww-plugin-vosk",
      "active": true,
      "sound": "snd/start_listening.wav",
      "debug": false,
      "rule": "fuzzy",
      "lang": "en",
      "samples": ["computer"]
    }
  }
}
```

### Precise

```json
{
  "hotwords": {
    "computer": {
      "module": "ovos-ww-plugin-precise-lite",
      "active": true,
      "model": "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/computer.tflite",
      "sound": "snd/start_listening.wav",
      "expected_duration": 3,
      "trigger_level": 3,
      "sensitivity": 0.5
    }
  }
}
```

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org).
