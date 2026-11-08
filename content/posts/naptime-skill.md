---
title: "Naptime Skill"
author: "Mike Gray"
date: 2023-05-15T21:45:30-05:00
draft: false
type: post

slug: naptime-skill
description: "Ensuring Extra Privacy for OVOS/NeonAI Personal Voice Assistant."
thumbnail: "images/mute.png"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Update.** OVOS and Neon both dropped Pocketsphinx support. For a custom wake word without training a model, use Vosk instead.
{{< /alert >}}

[Neon.AI](https://neon.ai) is a channel partner and software alternative for [Mycroft AI](https://mycroft.ai), an open-source voice assistant. Since [Mycroft's announcement that they will be closing software operations](https://mycroft.ai/blog/update-from-the-ceo-part-1/), Neon has taken over those responsibilities for the Mycroft private personal voice assistant ecosystem.

Neon is considered the commercial option for the current ecosystem of voice assistants, which also includes Mycroft Classic Core (unmaintained and several years out of date), Mycroft Dinkum (not compatible with the existing ecosystem of Mycroft skills, only functions on Mycroft Mark II hardware, and introduces several breaking changes that are incompatible with other voice assistant platforms), and [OpenVoice OS (OVOS)](https://openvoiceos.com/). Neon is built on top of OVOS, which is currently geared towards developers or those using the KDE BigScreen voice assistant option.

## Extra Privacy

My general approach to privacy with a personal voice assistant is to only use local Speech-to-Text and Text-to-Speech plugins, so all my data stays on my own network. What if you want some extra assurance that the voice assistant isn't listening when you don't want it to? [The OVOS team has a skill that can be installed in any Neon or OVOS voice assistant that will put the voice assistant to sleep](https://github.com/OpenVoiceOS/skill-ovos-naptime) when you want it to stop listening.

With the Mycroft voice assistant software, skills were either installed via their Marketplace or with the Mycroft Skills Kit. OVOS and Neon take the approach that a skill should just be another Python package, so you can simply install it with a `pip install ovos-skill-naptime` command. If you're using Neon, especially with the Docker containers option, you can also edit your `~/.config/neon/neon.yaml` file to include it as a default skill:

```yaml
skills:
  default_skills:
    - ovos-skill-naptime
```

After installing the skill, you'll need to restart the skills service or, if you're using a Mark 2 or other device with a screen, pull down the top menu and select `Reboot Services`. Once the skill is installed, you can say "Hey Mycroft, nap time" to put the voice assistant to sleep. The skill will not reactivate the microphone until you've said "Hey Mycroft, wake up."

## "Wake Up" Wake Word Configuration

The "Wake Up" wake word is a special case that really only works when the naptime skill is installed. Both OVOS and Neon should have a default configuration for this wake word. However, speaking with one of the OVOS developers, he recommends changing it from using Vosk to using [PocketSphinx](https://github.com/OpenVoiceOS/ovos-ww-plugin-pocketsphinx). Both plugins are based on detecting phonemes, but PocketSphinx does not load the model into memory, so it is less resource-intensive. To change the wake word to use PocketSphinx, edit your configuration file:

### Neon

`~/.config/neon/neon.yaml`:

```yaml
hotwords:
  wake_up:
    module: ovos-ww-plugin-pocketsphinx
    threshold: 1e-45
```

### OVOS

`~/.config/mycroft/mycroft.conf`:

```json
{
  "hotwords": {
    "wake_up": {
      "module": "ovos_ww_plugin_pocketsphinx",
      "threshold": 1e-45
    }
  }
}
```

## Use cases

The naptime skill can be useful for privacy reasons as well as convenience reasons. You can turn on naptime when:

- You are in a meeting and don't want the voice assistant to react to something someone says, or potentially record it and send it to the cloud (default behavior for OVOS and Neon, even if the data is not tied to your voice assistant or identity)
- You are watching a movie or TV show and don't want the voice assistant to react to something someone says
- You are having a conversation with someone and don't want the voice assistant to react to something someone says
- You are asleep and prefer to keep the voice assistant off overnight

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org).
