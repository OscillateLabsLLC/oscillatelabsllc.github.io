---
title: "FasterWhisper STT Server Script"
author: "Mike Gray"
date: 2024-05-15T00:00:00-00:00
draft: false
type: post

thumbnail: "images/fasterwhisper.webp"
slug: fasterwhisper-stt-server-script # Will take the filename as the slug.

# Lists
keywords: ["homelab", "mycroft", "neon", "ovos"]
tags: ["homelab", "mycroft", "neon", "ovos"]
categories: ["home automation", "personal voice assistant"]
---

{{< alert >}}
**Archived (2024).** Some steps here are out of date. For current setup, see [Self-Hosted Speech Servers](/posts/ovos-speech-servers/).
{{< /alert >}}

# Running a FasterWhisper STT Server on a Local Server

**UPDATE (November 2026):** This post is out of date and no longer maintained. For current, tested STT and TTS servers, including onnx-asr, which I now recommend over FasterWhisper, read [Self-Hosted Speech Servers for OVOS and Neon]({{< relref "ovos-speech-servers.md" >}}). The new servers run in Docker or in a Python venv.

**NOTE:** A previous version of this post recommended running FasterWhisper on a Raspberry Pi. While this is technically achievable, the latency is too high for most users. I recommend running FasterWhisper on a laptop or dedicated server for best results, ideally on a CUDA-enabled GPU. If you do try running on a Raspberry Pi, try using a `tiny` model and expect it will be slow and somewhat inaccurate, especially with accented English.

Over the course of the last year, I've spent a considerable amount of time helping [Neon](https://neon.ai) and [OVOS](https://openvoiceos.com) users customize their voice assistants. OVOS and Neon are both incredibly flexible platforms, which makes them powerful, but also complex. The biggest hurdle to getting a voice assistant running on a single small machine, such as Raspberry Pi, has been Speech-To-Text (STT). STT models are large and require significantly more computing power than other portions of a voice assistant.

Thankfully, in the last two years, OpenAI's Whisper model has emerged as an extremely powerful and efficient STT model. Whisper is a lightweight, fast, and accurate STT model that can run on most consumer hardware with ease. Whisper is the foundation of FasterWhisper, a community-driven project that provides pre-trained models of varying sizes. It's multilingual but does offer some English-only models, which can improve performance and efficiency if you know you're only going to be sending it data in English.

OVOS has several community-maintained STT servers that run FasterWhisper, but the ultimate goal of a private assistant is to be completely private and local as much as possible. Running everything on one device can be challenging, but OVOS and Neon make it easy to run your TTS and STT servers on a separate machine on your home network and then connect your assistant to it.

## FasterWhisper STT Server Setup Script

For the first time, I've created a script to automate setting up a FasterWhisper STT server on a laptop or tower. This script is designed to be run on any Debian-based OS (Debian, Ubuntu, etc). Most of it should work for other Linux distros but it hasn't been tested on them - you'll need to adjust the part where python3.11-venv is installed. The script will install FasterWhisper, download a pre-trained model, and provide instructions to configure FasterWhisper to run as a service. It will also install a few dependencies and configure the Pi to run as a headless server.

```bash
#!/bin/bash

set -e

IP=$(hostname -I | cut -d' ' -f1)
cd ~ || echo "No home directory for this user, please install with a user that has a home directory." || exit 1

PIP=""
if command -v pip >/dev/null 2>&1; then
    PIP="pip"
fi
if command -v pip3 >/dev/null 2>&1; then
    PIP="pip3"
fi
if command -v python -m pip >/dev/null 2>&1; then
    PIP="python -m pip"
fi
if command -v python3 -m pip >/dev/null 2>&1; then
    PIP="python3 -m pip"
fi
if [ -z "$PIP" ]; then
    echo >&2 "FasterWhisper STT requires pip but it's not available. Please install it before running this script."
    exit 1
fi

echo "********************************************************************************"
echo "Making sure we can create a Python virtual environment..."
sudo apt install python3.11-venv

echo "********************************************************************************"
echo "Creating virtual environment at ~/STT_venv"
python3.11 -m venv STT_venv
source ~/STT_venv/bin/activate

echo "********************************************************************************"
echo "Installing STT requirements"
pip install --upgrade pip wheel
pip install --pre ovos-stt-http-server ovos-stt-plugin-fasterwhisper "ovos-utils>0.0.38"

echo "********************************************************************************"
echo "Configuring STT with default whisper-large-v3-turbo model. You can change this later by editing ~/.config/mycroft/mycroft.conf"
echo "Alternate model options can be found at https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper/"
echo ""
mkdir -p ~/.config/mycroft
echo "********************************************************************************"
echo "Backing up any existing mycroft.conf to ~/.config/mycroft/mycroft.conf.bak"
echo ""
# If the file exists, make a backup copy
if [ -f ~/.config/mycroft/mycroft.conf ]; then
    cp ~/.config/mycroft/mycroft.conf ~/.config/mycroft/mycroft.conf.bak
fi

cat <<EOF > ~/.config/mycroft/mycroft.conf
  {
    "stt": {
      "module": "ovos-stt-plugin-fasterwhisper",
      "ovos-stt-plugin-fasterwhisper": {
        "model": "whisper-large-v3-turbo",
        "use_cuda": false
      }
    }
  }
EOF

echo "********************************************************************************"
echo "STT installed, you can now start the server with:"
echo "/home/$USER/STT_venv/bin/ovos-stt-server --engine ovos-stt-plugin-fasterwhisper --host $IP"
echo ""
# Give optional instructions for systemd service
echo "If you want to run this as a service, you can use the following systemd unit file:"
echo ""
cat <<EOF > ~/ovos-stt-server.service
[Unit]
Description=OVOS FasterWhisper STT Server
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=/home/$USER/STT_venv/bin/ovos-stt-server --engine ovos-stt-plugin-fasterwhisper --host $IP
Restart=on-failure

[Install]
WantedBy=multi-user.target
EOF
cat ~/ovos-stt-server.service

echo ""
echo "You can set this up as a long-running service with:"
echo "********************************************************************************"
echo "sudo cp ~/ovos-stt-server.service /etc/systemd/system/ovos-stt-server.service"
echo "sudo systemctl daemon-reload"
echo "sudo systemctl enable ovos-stt-server.service"
echo "sudo systemctl start ovos-stt-server.service"
echo "********************************************************************************"

```

## Testing the server

Once your server is running, a quick test is in order. You can test the server by running the following commands:

```bash
wget https://github.com/OpenVoiceOS/ovos-stt-plugin-fasterwhisper/raw/dev/jfk.wav ~/jfk.wav
curl -X POST -H "Content-Type: audio/wav" --data-binary "@/home/$USER/jfk.wav" http://localhost:8080/stt
```

You should see the following output (as an example):

```bash
❯ curl -X POST -H "Content-Type: audio/wav" --data-binary "@/home/$USER/jfk.wav" http://localhost:8080/stt
"And so, my fellow Americans, ask not what your country can do for you, ask what you can do for your country."%
```

Having trouble with the CURL command? Try hardcoding the path of the wav file. Sometimes the $USER variable doesn't work as expected.

## Connecting OVOS and Neon to the FasterWhisper STT Server

Once your server is running and you've confirmed your operating system firewall isn't blocking inbound traffic on TCP 8080, you can connect your OVOS or Neon device to it. On OVOS, you can do this by editing `~/.config/mycroft/mycroft.conf` and adding the following lines:

```json
"stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": "http://<IP of your FasterWhisper STT server>:8080/stt"
    }
  }
```

For Neon (`~/.config/neon/neon.yaml`):

```yaml
stt:
  module: ovos-stt-plugin-server
  ovos-stt-plugin-server:
    url: http://<IP of your FasterWhisper STT server>:8080/stt
```

You may also want to add public OVOS FasterWhisper servers as a fallback in case your server is down. You can do this by editing `~/.config/mycroft/mycroft.conf` and using the following config:

```json
"stt": {
    "module": "ovos-stt-plugin-server",
    "ovos-stt-plugin-server": {
      "url": [
        "http://<IP of your FasterWhisper STT server>:8080/stt",
        "https://fasterwhisper.ziggyai.online/",
        "https://stt.smartgic.io/fasterwhisper"
      ]
    }
  }
```

On Neon, you can do this by editing `~/.config/neon/neon.yaml` and adding the following lines:

```yaml
stt:
  module: ovos-stt-plugin-server
  ovos-stt-plugin-server:
    url:
      - http://<IP of your FasterWhisper STT server>:8080/stt
      - https://fasterwhisper.ziggyai.online/
      - https://stt.smartgic.io/fasterwhisper
```

Note that if you're on a Mycroft Mark 2 devices running Neon, you will still have a fallback to STT on the device, which consumes a ton of memory and is very slow. You may want to consider disabling your fallback plugin, but adding public OVOS FasterWhisper servers in case yours is down. You can do this by editing `~/.config/neon/neon.yaml` and using the following config:

```yaml
stt:
  module: ovos-stt-plugin-server
  ovos-stt-plugin-server:
    url:
      - http://<IP of your FasterWhisper STT server>:8080/stt
      - https://fasterwhisper.ziggyai.online/
      - https://stt.smartgic.io/fasterwhisper
  fallback_module: ""
```

The `url` values are read in order, so if yours is first in the array, it will be used as long as it's online.

## Conclusion

I hope this script is helpful to some of you. If you have any questions or suggestions, please let me know in [Matrix](https://matrix.to/#/#OpenVoiceOS-Support:matrix.org) or via [email](mailto:mike@graywind.org). As always, I'm open to requests for posts and any help you might need getting your FasterWhisper STT server working.
