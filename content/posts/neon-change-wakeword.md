---
title: "Change Wakeword in NeonAI Personal Voice Assistant"
author: "Mike Gray"
date: 2023-05-14T14:14:36-07:00
draft: false
type: post

slug: neon-change-wakeword

description: "Changing the wakeword in the NeonAI Personal Voice Assistant, using provided defaults."
thumbnail: "images/mark2.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

[Neon.AI](https://neon.ai) is a channel partner and software alternative for [Mycroft AI](https://mycroft.ai), an open-source voice assistant. Since [Mycroft's announcement that they will be closing software operations](https://mycroft.ai/blog/update-from-the-ceo-part-1/), Neon has taken over those responsibilities for the Mycroft private personal voice assistant ecosystem.

Neon is considered the commercial option for the current ecosystem of voice assistants, which also includes Mycroft Classic Core (unmaintained and several years out of date), Mycroft Dinkum (not compatible with the existing ecosystem of Mycroft skills, only functions on Mycroft Mark II hardware, and introduces several breaking changes that are incompatible with other voice assistant platforms), and [OpenVoice OS (OVOS)](https://openvoiceos.com/). Neon is built on top of OVOS, which is currently geared towards developers or those using the KDE BigScreen voice assistant option.

## Wakewords

By default, Neon comes with several possible wake words (wake word engine in parentheses):

- Hey Mycroft (Precise)
- Hey Neon (Vosk)
- Hey Ziggy (Vosk)
- Thank you (Vosk - courtesy wake word, does not trigger listening for skills)
- Wake up (Vosk)

Depending on the version of Neon Core you are running, it will respond to either "Hey Neon" or "Hey Mycroft" out of the box.

The default wake words use one of two different plugins, Precise or Vosk. Precise is typically the more accurate of the two because it is trained on recordings and uses an ML model. Vosk translates sounds to phonemes and will generally be more sensitive and prone to error, especially for something like "Hey Neon," which will trigger almost any time it picks up an "EE" sound.

Neon users frequently report that "Hey Neon" is too sensitive. "Hey Mycroft" currently uses the same trained model that the Mark 2 Dinkum software used, which included more samples of higher-pitched voices, so it is more responsive to children and women. "Hey Ziggy" is a Vosk wake word. Due to the less common combination of sounds in English, it is generally more responsive and less prone to accidental activation. At this time, users have not reported much feedback on "Wake Up" or "Thank you."

Neon users who are comfortable with the command line have the option to import any number of supported [wake word models](https://github.com/OpenVoiceOS/precise-lite-models/tree/master/wakewords/en) and [plugins](https://openvoiceos.github.io/ovos-technical-manual/ww_plugins/). If you prefer not fussing much adding models and want to set up one of the provided wake words, here is how:

- Log into the Neon CLI via SSH, which must be enabled from the "Device Settings" menu first.
  - To get to Device Settings, drag from the top center of the screen down. You'll hear a click and the menu will appear. Press "Enable SSH"
  - The default username and password is neon:neon.
- Open `~/.config/neon/neon.yaml` in your preferred text editor, usually `nano` or `vim` but `emacs` is also a popular choice. If you're not sure, choose `nano`.
- Since the Neon configuration is constructed from multiple files on the device, _you must include all of the possible default options in your `~/.config/neon/neon.yaml` file to override the defaults._ [Example](https://github.com/NeonGeckoCom/NeonCore/blob/master/neon_core/configuration/neon.yaml#L108):
- If you're setting up a `precise-lite` model wakeword, be sure to run `pip install ovos-ww-plugin-precise-lite[tflite]` (the exact command, as written) first. This is especially important on Neon Mark 2 images. **EDIT**: As of Neon v2, you need to add this to your `~/.config/neon/neon.yaml` file under `skills.default_skills`:

```yaml
skills:
  default_skills:
    - ovos-ww-plugin-precise-lite[tflite]
```

```yaml
hotwords:
  hey_mycroft:
    active: false
    module: ovos-ww-plugin-precise-lite
    sound: snd/start_listening.wav
    model: "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/hey_mycroft.tflite"
    expected_duration: 3
    trigger_level: 3
    sensitivity: 0.5
  hey_neon:
    module: ovos-ww-plugin-vosk
    active: false
    sound: snd/start_listening.wav
    debug: false
    rule: fuzzy
    lang: en
    samples:
      - hey neon
  hey_ziggy:
    active: false
    module: ovos-ww-plugin-vosk
    sound: snd/start_listening.wav
    debug: false
    rule: fuzzy
    lang: en
    samples:
      - hey ziggy
  thank_you:
    module: ovos-ww-plugin-vosk
    sound: snd/acknowledge.mp3
  wake_up:
    module: ovos-ww-plugin-vosk
```

- Now that you have all of the defaults in one location, set `active: false` to whichever wake words you **don't want to use**. You'll also now want to add `listen: true` to the wakeword you **do want to use**. Note that you don't need to add `active: true` to the wake words you _do_ want to use, because that is the default value for `active`. If `active` or `listen` are not available in your configuration they may be added.
  - Be cautious - YAML is whitespace sensitive! Make sure your indentations are correct. Use a YAML validator like [yamllint.com](https://www.yamllint.com/) to validate your file is correct.
  - (EDIT) One of the developers at Neon had a brief correction: "...you only need to add `active: false` to disable the default WW; `listen` shouldn't be evaluated if the WW is marked as not `active`."
- Save the file and exit your editor (ctrl+x in nano, :wq! in vim)
- Since you're on the command line already, run `sudo systemctl restart neon-voice`. It will take some time for the service to come back up.
  - If you prefer, you can open the Device Settings menu and select "Restart Services" to restart all of Neon's services.

You will now be able to activate your Neon device using your preferred wake word! From the available defaults, I usually prefer "Hey Ziggy" or "Hey Mycroft" depending on my mood and how many voice assistants I have within "earshot."

## OpenVoice OS (OVOS)

The same settings you see above should also work in OVOS! Expect another blog post detailing the defaults and nuances of OVOS, but for now, try editing `~/.config/mycroft/mycroft.conf` with the following:

```json
{
  "hotwords": {
    "hey_mycroft": {
      "active": false,
      "module": "ovos-ww-plugin-precise-lite",
      "sound": "snd/start_listening.wav",
      "model": "https://github.com/OpenVoiceOS/precise-lite-models/raw/master/wakewords/en/hey-mycroft.tflite",
      "expected_duration": 3,
      "trigger_level": 3,
      "sensitivity": 0.5
    },
    "hey_neon": {
      "module": "ovos-ww-plugin-vosk",
      "active": false,
      "sound": "snd/start_listening.wav",
      "debug": false,
      "rule": "fuzzy",
      "lang": "en",
      "samples": ["hey neon"]
    },
    "hey_ziggy": {
      "active": false,
      "module": "ovos-ww-plugin-vosk",
      "sound": "snd/start_listening.wav",
      "debug": false,
      "rule": "fuzzy",
      "lang": "en",
      "samples": ["hey ziggy"]
    },
    "thank_you": {
      "module": "ovos-ww-plugin-vosk",
      "sound": "snd/acknowledge.mp3"
    },
    "wake_up": {
      "module": "ovos-ww-plugin-vosk"
    }
  }
}
```

The same rules apply: set `"active": false` for the wakeword(s) you don't want, a set `"listen": true` for the wakeword(s) you do want.

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org).
