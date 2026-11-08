---
title: "Voice and Wakeword Combos For OVOS/Neon"
author: "Mike Gray"
date: 2023-10-29T11:58:58-04:00
draft: false
type: post

description: "Combinations of TTS voices and wakewords to get interesting assistants."
thumbnail: "images/Theatre-Masks.jpg"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

[Neon.AI](https://neon.ai) and [OpenVoice OS (OVOS)](https://openvoiceos.com/) both offer out-of-the-box smart speaker/voice assistant platforms. Neon has spent much of 2023 focused on the Mycroft Mark 2 smart speaker, although they have recently branched out into Orange Pi and Raspberry Pi offerings. OVOS can run headless (without a GUI) on almost any platform that can run Docker - most flavors of Linux, Windows 10/11 (with WSL2), and MacOS. Because both organizations have recently been thrust into the position of carrying the torch for the now-defunct Mycroft.AI, they have been focused on getting the core functionality working and have not yet had time to focus on making the "fun" stuff - read voices and wakewords - accessible to less technical users.

Let's change that, shall we?

Neon users who are less technical, I'm working on an update to a Neon skill that will allow you to use these voices and wakewords without having to edit any files. Stay tuned!

## A British Butler Named Jarvis

Jarvis is a British butler who is always dry. We'll want to address him by name - "Hey Jarvis" - and his voice needs to match. Using the OpenWakeWord plugin combined with the classic Alan Pope voice in Piper, Jarvis comes to life.

Here are the configs I use to create Jarvis (note that installing these packages on Neon requires adding them to `neon.yaml` to make sure they persist):

Python packages:

```sh
pip install 'ovos-tts-plugin-server>=0.0.2a6'
pip install 'ovos-ww-plugin-openWakeWord'
pip install 'openwakeword<0.6.0' # 0.6.0 drops pre-packaging of the "Hey Jarvis" model, so force a downgrade.
```

**Edit:** As of February 2024, user stalwartgrace reported that OpenWakeWord version 6.0 doesn't package the "Hey Jarvis" wakeword anymore. For simplicity, I'm suggesting you use a version of OpenWakeWord before 0.6.0, but long-term I'll reach out to the maintianer to see how to use 0.6.0+ and pull down the correct model.

`~/.config/neon/neon.yaml`

```yaml
skills:
  default_skills:
    - "ovos-tts-plugin-server>=0.0.2a6"
    - "openwakeword<0.6.0"
    - "ovos-ww-plugin-openWakeWord"
hotwords:
  hey_jarvis:
    module: ovos-ww-plugin-openwakeword
tts:
  module: ovos-tts-server-plugin # Default voice is Alan Pope and uses OVOS' public TTS servers, so this is all you need!
```

OR

`~/.config/mycroft/mycroft.conf`

```json
{
  "hotwords": {
    "hey_jarvis": {
      "module": "ovos-ww-plugin-openwakeword"
    }
  },
  "tts": {
    "module": "ovos-tts-server-plugin"
  }
}
```

On Neon, you also need to adjust the user info file, otherwise Jarvis will have no idea how to pronounce things (Americans and their silly accents):

`~/.config/neon/ngi_user_info.yml`

```yaml
speech:
  tts_language: en-uk
  tts_gender: male
  secondary_tts_gender: male
```

Restart your Neon or OVOS services and you should be good to go! Say "Hey Jarvis" and he should respond.

## Classic Mycroft

The original Mycroft settings bring back a lot of nostalgia for people who were involved in the community at that time. You can recreate that experience with a combination of the "Hey Mycroft" wake word and the classic Alan Pope voice, using either the original Mimic plugin or Piper for a more modern TTS experience.

On OVOS, "Hey Mycroft" is the default wake word. On Neon, you can say "change my wake word to Hey Mycroft" and it will update it for you.

To use Mimic, install the Mimic plugin with `pip install ovos-tts-plugin-mimic` and then add the following to your `mycroft.conf` file:

```json
{
  "tts": {
    "module": "ovos-tts-plugin-mimic"
  }
}
```

Or on Neon:

```yaml
skills:
  default_skills:
    - "ovos-tts-plugin-mimic"
tts:
  module: ovos-tts-plugin-mimic
```

The default voice is Alan Pope so nothing more needs to be done. If you prefer a high fidelity voice, [see the previous section](#a-british-butler-named-jarvis) to configure the OVOS TTS server to use the Alan Pope voice with Piper.

## A Friendly Droid

When you're traveling through space in the distant past, sometimes you need a good druid at your side. Never fear - you can use the Beep Speak TTS plugin to create a friendly droid to help you out. Combine it with the "Hey Mycroft" wake word, [or any other Precise Lite wake words](/posts/neon-custom-wakeword/), to get started.

Please note that unless you are fluent in this droid dialect, you will have no idea what your assistant is saying! You can check the audio logs on the device to see what it said, but that's not a great experience. This is just for fun.

For TTS, you'll need to `pip install ovos-tts-plugin-beepspeak`, then use the following configuration:

```json
  "tts": {
    "module": "ovos-tts-plugin-beepspeak",
    "ovos-tts-plugin-beepspeak": {
      "time_step": 0.1
    }
  }
```

OR on Neon:

```yaml
skills:
  default_skills:
    - "git+https://github.com/OpenVoiceOS/ovos-tts-plugin-beepspeak"
tts:
  module: ovos-tts-plugin-beepspeak
  ovos-tts-plugin-beepspeak:
    time_step: 0.1
```

## Conclusion

These are just a few of the combinations you can use with the wide array of wake word options and TTS plugins available for OVOS and Neon. I have two distinct assistant "personalities" running at home that are different from these and will eventually have [custom wake words using OpenWakeWord](https://www.home-assistant.io/voice_control/create_wake_word/).

The future of voice assistants is rich, and while companies such as Amazon and Google may have mostly abandoned their efforts, the open source community is still going strong. With a private and personal voice assistant, many of the features that most users would not accept from a big company become viable, and the community is catching up with what the big companies have to offer in terms of STT, TTS, and wake word technology.

Who is your voice assistant? Feel free to [share your combinations](mailto:mike@graywind.org) and I'll add them to a future post!

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
