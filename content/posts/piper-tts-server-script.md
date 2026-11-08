---
title: "Piper TTS Server Script"
author: "Mike Gray"
date: 2024-01-21T20:35:42-06:00
draft: false
type: post

thumbnail: "images/piper.png"
slug: piper-tts-server-script # Will take the filename as the slug.

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2024).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

# Running a Piper TTS Server on a Raspberry Pi

Over the course of the last year, I've spent a considerable amount of time helping [Neon](https://neon.ai) and [OVOS](https://openvoiceos.com) users customize their voice assistants. OVOS and Neon are both incredibly flexible platforms, which makes them powerful, but also complex. The two most frequently requested text-to-speech (TTS) options are Coqui TTS and Piper TTS. Coqui is the spiritual successor to Mozilla's DeepSpeech and unfortunately [no longer going to be supported](https://www.linkedin.com/pulse/coqui-shutting-down-josh-meyer-fpsve/). Piper is a continuation of Larynx, both of which were created by Mike Hansen. Mike is the creator of Rhasspy, another personal voice assistant platform. He is also formerly of Mycroft AI, their last lead developer before they went under, and currently working at Nabu Casa, the company behind Home Assistant. Mike is a busy guy, and I'm grateful for all the work he's done on these projects.

OVOS has several community-maintained TTS servers that run Piper, but the ultimate goal of a private assistant is to be completely private and local as much as possible. Running everything on one device can be challenging, but OVOS and Neon make it easy to run your TTS and STT servers on a separate machine on your home network and then connect your assistant to it.

## Piper TTS Server Setup Script

I've written about this broadly and provided some examples, but for the first time, I've created a script to automate setting up a Piper TTS server on a Raspberry Pi. This script is designed to be run on a Raspberry Pi 4 with a fresh install of Raspberry Pi OS, but any Debian-based OS will work. Most of it should work for other Linux distros but it hasn't been tested on them. The script will install Piper, download a pre-trained model, and provide instructions to configure Piper to run as a service. It will also install a few dependencies and configure the Pi to run as a headless server. The script is available on [GitHub Gists](https://gist.github.com/mikejgray/13cbaae8920c05a450ae642e3dd54323) and I'm including a copy in this post as a backup.

```bash
#!/bin/bash

set -e

IP=$(hostname -I | cut -d' ' -f1)
cd ~ || echo "No home directory for this user, please install with a user that has a home directory." || exit 1

command -v espeak-ng >/dev/null 2>&1 || { echo >&2 "Piper TTS requires espeak-ng but it's not installed. Please install it before running this script."; exit 1; }

$PIP = ""
if command -v pip >/dev/null 2>&1; then
    $PIP = "pip"
fi
if command -v pip3 >/dev/null 2>&1; then
    $PIP = "pip3"
fi
if command -v python -m pip >/dev/null 2>&1; then
    $PIP = "python -m pip"
fi
if command -v python3 -m pip >/dev/null 2>&1; then
    $PIP = "python3 -m pip"
fi
if [ -z "$PIP" ]; then
    echo >&2 "Piper TTS requires pip but it's not available. Please install it before running this script."
    exit 1
fi

echo "********************************************************************************"
echo "Making sure we can create a Python virtual environment..."
sudo apt install python3.11-venv

echo "********************************************************************************"
echo "Creating virtual environment at ~/tts_venv"
python3 -m venv tts_venv
source ~/tts_venv/bin/activate

echo "********************************************************************************"
echo "Installing TTS requirements"
pip install --upgrade pip wheel
pip install --pre ovos-tts-server ovos-tts-plugin-piper

echo "********************************************************************************"
echo "Configuring TTS with default alan-low voice. You can change this later by editing ~/.config/mycroft/mycroft.conf"
echo "Alternate voice options can be found at https://github.com/rhasspy/piper/releases/tag/v0.0.2"
echo ""
mkdir -p ~/.config/mycroft
echo "********************************************************************************"
echo "Backing up any existing mycroft.conf to ~/.config/mycroft/mycroft.conf.bak"
echo ""
cp ~/.config/mycroft/mycroft.conf ~/.config/mycroft/mycroft.conf.bak

cat <<EOF > ~/.config/mycroft/mycroft.conf
{
  "tts": {
    "module": "ovos-tts-plugin-piper",
    "ovos-tts-plugin-piper": {
      "model": "alan-low"
    }
  }
}
EOF

echo "********************************************************************************"
echo "TTS installed, you can now start the server with:"
echo "ovos-tts-server --engine ovos-tts-plugin-piper --host $IP --cache"
echo ""
# Give optional instructions for systemd service
echo "If you want to run this as a service, you can use the following systemd unit file:"
echo ""
cat <<EOF > ~/ovos-tts-server.service
[Unit]
Description=OVOS Piper TTS Server
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=/home/$USER/tts_venv/bin/ovos-tts-server --engine ovos-tts-plugin-piper --host $IP --cache
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
cat ~/ovos-tts-server.service

echo ""
echo "You can set this up as a long-running service with:"
echo "********************************************************************************"
echo "sudo cp ~/ovos-tts-server.service /etc/systemd/system/ovos-tts-server.service"
echo "sudo systemctl daemon-reload"
echo "sudo systemctl enable ovos-tts-server.service"
echo "sudo systemctl start ovos-tts-server.service"
echo "********************************************************************************"
```

## Connecting OVOS and Neon to the Piper TTS Server

Once your server is running and you've confirmed your operating system firewall isn't blocking inbound traffic on TCP 9666, you can connect your OVOS or Neon device to it. On OVOS, you can do this by editing `~/.config/mycroft/mycroft.conf` and adding the following lines:

```json
"tts": {
    "module": "ovos-tts-plugin-server",
    "ovos-tts-plugin-server": {
      "model": "alan-low",
      "host": "http://<IP of your Piper TTS server>:9666"
    }
  }
```

You may also want to add public OVOS Piper servers as a fallback in case your server is down. You can do this by editing `~/.config/mycroft/mycroft.conf` and using the following config:

```json
"tts": {
    "module": "ovos-tts-plugin-server",
    "ovos-tts-plugin-server": {
      "model": "alan-low",
      "host": [
        "http://<IP of your Piper TTS server>:9666",
        "https://pipertts.ziggyai.online",
        "https://tts.smartgic.io/piper"
      ]
    }
  }
```

On Neon, you can do this by editing `~/.config/neon/neon.yaml` and adding the following lines:

```yaml
tts:
  module: ovos-tts-plugin-server
  ovos-tts-plugin-server:
    model: alan-low # Or your preferred voice
    host:
      - http://<IP of your Piper TTS server>:9666
      # You can optionally add the public OVOS TTS server as a fallback in case your server is down
      # - https://pipertts.ziggyai.online
      # - https://tts.smartgic.io/piper
```

Note that if you're on a Mycroft Mark 2 devices running Neon, you will still have a fallback to Coqui on the device, which consumes a ton of memory and is very slow. You may want to consider disabling your fallback plugin, but adding public OVOS Piper servers in case yours is down. You can do this by editing `~/.config/neon/neon.yaml` and using the following config:

```yaml
tts:
  module: ovos-tts-plugin-server
  ovos-tts-plugin-server:
    model: alan-low # Or your preferred voice
    host:
      - http://<IP of your Piper TTS server>:9666
      - https://pipertts.ziggyai.online
      - https://tts.smartgic.io/piper
  fallback_module: ""
```

The `host` values are read in order, so if yours is first in the array, it will be used as long as it's online.

## Conclusion

I hope this script is helpful to some of you. If you have any questions or suggestions, please let me know in [Matrix](https://matrix.to/#/#OpenVoiceOS-Support:matrix.org) or via [email](mailto:mike@graywind.org). As always, I'm open to requests for posts and any help you might need getting your Piper TTS server working.
