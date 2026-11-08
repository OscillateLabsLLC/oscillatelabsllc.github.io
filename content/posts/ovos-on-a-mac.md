---
title: "OVOS on a Mac"
author: "Mike Gray"
date: 2023-10-15T13:56:36-05:00
draft: false
type: post

slug: ovos-on-a-mac # Will take the filename as the slug.

keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2023).** Some steps here are out of date. [ovos-installer](https://github.com/OpenVoiceOS/ovos-installer) now supports macOS natively.
{{< /alert >}}

I've written quite a few posts already about configuring different aspects of [Neon.AI](https://neon.ai), a private, open source voice assistant built on top of [OpenVoiceOS (OVOS)](https://openvoiceos.com). However, all of those posts assume you already have a device running one of those platforms. In this post, I'll walk through the process of setting up a Mac to run OVOS. This is a great way to get started with OVOS if you don't have a Raspberry Pi or other device available, or you just want to take advantage of the great processing power available on a Mac.

Since Neon doesn't have a Mac version of its platform at the time of this writing (October 2023), we'll focus on running OVOS in Docker. Please note that currently, the OVOS GUI does not work on a Mac, so you'll be running the assistant "headless."

## Prerequisites

The official [ovos-docker](https://github.com/OpenVoiceOS/ovos-docker) project has [dedicated and comprehensive documentation](https://openvoiceos.github.io/ovos-docker/). I'll be following that documentation closely, but I'll also be adding some additional steps to discuss some of the issues you may run into on a Mac.

First, you'll need Docker installed, or an alternative that will run on a Mac. I'm using [Docker Desktop](https://www.docker.com/products/docker-desktop) for Mac, which is free for personal use. You'll also need to install [Homebrew](https://brew.sh) if you don't already have it. Homebrew is a package manager for Mac that makes it easy to install and manage software. We'll use it to install some additional tools we'll need.

Install both of those following the instructions in the official documentation. Once you have Homebrew available, you'll need to install PulseAudio. PulseAudio is a sound server that allows you to route audio between applications. We'll use it to route audio from OVOS to the Mac's speakers and to route the microphone on the Mac to OVOS. With Homebrew, it's simple: `brew install pulseaudio`.

I did run into some issues with the PulseAudio configuration out of the box, so here are a few additional steps to take that should get you up and running:

```zsh
brew services stop pulseaudio
sed -i "" "s/#load-module module-native-protocol-tcp/load-module module-native-protocol-tcp/g" $(brew ls pulseaudio | grep default.pa$)
brew services start pulseaudio
```

## Official docs

Note: The official docs are updated regularly, so if some steps here don't work for you, please [check there first to see if something has changed](https://openvoiceos.github.io/ovos-docker/getting-started/docker/installation/requirements/#__tabbed_2_3).

First, get the latest version of the `ovos-docker` repo and create the directories we'll need:

```zsh
git clone https://github.com/OpenVoiceOS/ovos-docker.git -b dev
mkdir -p ~/ovos/{config,share,tmp}
chown ${USER}:${USER} -R ~/ovos
cd ~/ovos-docker/compose
```

The containers will be configured via a combination of environment variables and config files located in the directories you created above. For a quick start, execute `cp .env.example .env` to use the default values for Mac. You can edit the `.env` file to change the values if you want, but we'll be using the defaults for this post.

Next, we'll create the `mycroft.conf` file. OVOS is a fork of the original Mycroft project (now defunct), hence the name. The directions [in the official docs](https://openvoiceos.github.io/ovos-docker/getting-started/docker/installation/configuration/) explain this well, but if you're in a hurry, this config will get you started:

`~/ovos/config/mycroft.conf`

```json
{
  "logs": {
    "path": "stdout"
  },
  "play_wav_cmdline": "aplay %1",
  "lang": "en-us",
  "listener": {
    "VAD": {
      "module": "ovos-vad-plugin-silero"
    }
  },
  "sounds": {
    "start_listening": "/home/ovos/.venv/lib/python3.11/site-packages/ovos_dinkum_listener/res/snd/start_listening.wav"
  }
}
```

With configuration in place, it's time to start the services:

```zsh
docker compose --project-name ovos --file docker-compose.macos.yml --env-file .env up --detach
```

The first time you do it, this could take several minutes, since the Docker images are quite large and must be downloaded at runtime. Subsequent starts will be much faster, but note that each time you restart OVOS, Docker will check for an updated version of the image.

That's it for the basics! You can watch all the logs with `docker compose logs -f` and you should see OVOS start up. The default wake word to activate the assistant is "Hey Mycroft," so say that and then follow up with something simple like "what time is it?" or "who are you?" to test it out.

## Next Steps

### New skills

The basic skills that come with ovos-docker are very barebones and won't provide much of an exciting experience. You'll want to install some additional skills to get the most out of OVOS. There are two ways to run skills:

1. Part of the `ovos_core` container, by creating a file structured like a Python `requirements.txt` file in the `~/ovos/config/skills.list` file (must be created if you haven't done so before).
2. As a standalone container, which is the recommended way to run skills despite some tradeoffs around resource utilization.

To use the sample skills that come with ovos-docker, run `docker compose --project-name ovos --file docker-compose.macos.yml --file docker-compose.skills.yml --env-file .env up --detach`.

If you want to try [other skills available from OVOS](https://github.com/openvoiceos?q=skill-ovos&type=all&language=&sort=) or classic Mycroft skills, you can add them to the `~/ovos/config/skills.list` file. For example, to add the [Easter Egg skill](https://github.com/OpenVoiceOS/ovos-skill-easter-eggs), add this to your `~/ovos/config/skills.list` file:

```
git+https://github.com/OpenVoiceOS/ovos-skill-easter-eggs
```

Then restart OVOS with `docker compose --project-name ovos --file docker-compose.macos.yml --file docker-compose.skills.yml --env-file .env up --detach`.

**NOTE**: OVOS is guaranteeing support for classic Mycroft skills up to 0.1.0 (currently, they're working on releasing 0.0.8), but after that, they may not work. Any classic Mycroft skills that are not installable via pip (e.g. doesn't have setup.py or pyproject.toml) will not work with OVOS Docker unless those are added. I have [a tool that can help retrofit those skills](https://github.com/mikejgray/ovos-skill-projen#retrofitting-mycroft-skills-wip) if you'd like to fork an original skill and make it work with OVOS.

Also, any Mycroft CommonPlay skills (such as Pandora, Spotify, etc.) will not work with OVOS. OVOS has its own CommonPlay implementation called OVOS Common Play (OCP) that is not compatible with the Mycroft implementation.

### Customizing the assistant

I've written before about [changing the wake word](../neon-change-wakeword) and [using a custom wake word](../neon-custom-wakeword). Those posts are specific to Neon, but the process is the same for OVOS, and I included sections of the `mycroft.conf` file for easy setup.

OVOS maintains public servers for Speech-To-Text (STT) and Text-To-Speech (TTS) and promises as private an experience as possible, but if you want to use your own STT and TTS servers, you can do that as well. I'll cover that in future posts, but in the meantime I've written on [running Coqui TTS on a Mac](../coqui-tts-mac) and [the plugin necessary to use that Coqui instance](../coqui-tts-neon-ovos).

## Feedback

Questions? Comments? Feedback? Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [OVOS support chat on Matrix](https://matrix.to/#/#openvoiceos-support:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
