---
title: "Installing Neon Skills"
author: "Mike Gray"
date: 2023-06-04T19:54:39-04:00
draft: false
type: post

slug: installing-neon-skills # Will take the filename as the slug.
description: "How to install skills to Neon OS."
thumbnail: "images/install-skills.avif"

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

As of this writing (June 2023), the [Neon voice assistant](https://neon.ai) does not have a non-technical entrypoint to installing skills. This is partly due to the upstream project, [OVOS](https://openvoiceos.com), having other pressing priorities at the moment. However, it is possible to install skills on Neon, and it's not terribly difficult. This guide will walk you through the process.

## SSH Access

The Neon docs have a short section on [Secure SHell (SSH)](https://neongeckocom.github.io/neon-docs/neon_os/tips/#ssh) access to the Mark 2 Neon software, which is how you can install skills. By default, the Mark 2 is SSH-enabled, with a default username of `neon` and password of `neon`. The first time you log in, the password must be changed. SSH access is granted via the terminal.

### Mac OS

Mac OS has a built-in SSH client. To access the Mark 2, open the Terminal app and run the following command:

```zsh
ssh neon@neon
```

Then, enter the password and follow the prompts.

### Windows

The latest builds of Windows 10 and 11 have OpenSSH installed by default. To access the Mark 2, open PowerShell or Windows Terminal and run the following command:

```powershell
ssh neon@neon
```

If this doesn't work, go to your Mark 2 and drag the screen from the top to the bottom. Select the Wireless configuration option and note the IP address. Then, run the following command:

```powershell
ssh neon@<IP address>
```

Then, enter the password and follow the prompts.

## Installing Skills

All Neon and OVOS skills are compatible with both voice assistant operating systems. Original Mycroft skills are also largely compatible. The primary exceptions are media skills (music, news, podcasts, etc.) and IoT/Home Automation skills (such as Home Assistant and Hubitat), although IoT/Home Automation skills should still work for the time being.

The main methods of installing skills that we'll cover in this post are via `pip` and using the Neon configuration file (`~/.config/neon/neon.yaml`). The sections below will explain when to use each method and provide examples.

### Installing via pip

All Neon skills and many [official OVOS skills](https://github.com/orgs/OpenVoiceOS/repositories?language=&page=2&q=skills&sort=&type=all) should be installable via pip. Original Mycroft skills are probably not. If you're not sure, check the skill's GitHub repo for a `setup.py` file. If it has one, you can install it via pip.

From the command line you accessed in the [SSH Access](#ssh-access) section, run the following command:

```bash
pip install git+$SKILL_GIT_URL
```

Where `$SKILL_GIT_URL` is the URL of the skill's GitHub repo. For example, to install the [OVOS Easter Egg skill](https://github.com/OpenVoiceOS/ovos-skill-easter-eggs), you would run `pip install git+https://github.com/OpenVoiceOS/ovos-skill-easter-eggs`.

You can also search for skills on [PyPi](https://pypi.org), Python's official repository for packages, if you know the name. For example, the OVOS Easter Egg skill can be found on PyPi as `ovos-easter-egg-skill`. The command to run would be `pip install skill-easter-eggs`.

Finally, restart your Neon services using the Mark 2 dropdown from the top.

### Installing via the Neon configuration file

Original Mycroft skills and any skill that does not have a `setup.py` file can be installed via the Neon configuration file. You can also install `pip`-installable skills this way, if you prefer. This has the added benefit of keeping the skill up-to-date, as Neon will check for updates to the skill and install them automatically. It does slow down the startup time of the Neon software, however, so if you're installing a lot of skills, you may want to consider installing them via `pip` instead.

To install a skill via the Neon configuration file, you'll need to edit the file. From the command line you accessed in the [SSH Access](#ssh-access) section, run the following command:

```bash
nano ~/.config/neon/neon.yaml
```

This will open the Neon configuration file in the `nano` text editor. You can use any text editor you prefer, but `nano` is installed by default on Neon and is easy to use. Using the down arrow, go down to the `skills:` section. If you've never installed a skill this way before, it will look like this:

```yaml
skills:
  blacklisted_skills:
    - skill-ovos-setup.openvoiceos
    - skill-messaging.neongeckocom
    - skill-custom_conversation.neongeckocom
    - skill-instructions.neongeckocom
    - skill-audio_record.neongeckocom
    - mycroft-wiki.mycroftai
    - mycroft-joke.mycroftai
```

To install a skill, add a `default_skills` section and add the Github URL or the `pip` package name to it. For example, to install the OVOS Easter Egg skill and the [Naptime skill](../naptime-skill):

```yaml
skills:
  blacklisted_skills:
    - skill-ovos-setup.openvoiceos
    - skill-messaging.neongeckocom
    - skill-custom_conversation.neongeckocom
    - skill-instructions.neongeckocom
    - skill-audio_record.neongeckocom
    - mycroft-wiki.mycroftai
    - mycroft-joke.mycroftai
  default_skills:
    - skill-easter-eggs
    - https://github.com/OpenVoiceOS/skill-ovos-naptime
```

Finally, restart your Neon services using the Mark 2 dropdown from the top.

### A note about YAML files

YAML is a space-sensitive file format. Make sure the spaces are exactly as you see above or the entire configuration may fail to load! If that makes you nervous, copy and paste your `neon.yaml` file into the [YAML lint](https://www.yamllint.com/) webpage after you've made your changes. It will tell you if the file is valid YAML, meaning all the spaces and dashes and colons are in the correct place, and try to fix it if it's not.

## Feedback

Questions? Comments? Feedback? This can be challenging if you're not used to working in a terminal. Let me know on the [Mycroft Community Forums](https://community.mycroft.ai/) or [Neon OS public chat on Matrix](https://matrix.to/#/#NeonMycroft:matrix.org). I'm available to help and so is the rest of the community. We're all learning together!
