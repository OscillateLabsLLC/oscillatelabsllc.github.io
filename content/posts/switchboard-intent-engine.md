---
title: "Switchboard Intent Engine"
author: "Mike Gray"
date: 2026-10-14T21:30:19-05:00
draft: false
type: post

slug: switchboard-intent-engine # Will take the filename as the slug.
thumbnail: "images/switchboard.jpg"

summary: "I got a wild hair, and created the Switchboard Intent Engine for OpenVoiceOS!"
description: "A new intent engine for OpenVoiceOS that allows for more flexible and powerful voice interaction."

# Lists
keywords:
  ["homelab", "mycroft", "ovos", "intent engine", "machine learning", "llm"]
tags: ["homelab", "mycroft", "ovos", "machine learning", "llm"]
categories: ["home automation", "personal voice assistant"]
---

Over the weekend I was thinking about the work the OpenVoiceOS development team was doing on the default intent pipeline. I started to wonder if we were close to what the big players were doing. I popped open a conversation with Claude and it looked up the published information from Amazon and Google about their pipelines. We bounced some ideas around, then I had it get to work.

I call the result Switchboard. It's a series of intent pipeline steps that together replace Padatious, Adapt and Model2Vec. It's faster than Padatious, and the steps without an LLM are fully deterministic: same input, same decision, every time. In my tests, the LLMs repeated every decision too.

Even the lowest-cost version, with no LLM at all, outperforms OVOS's default pipeline (Adapt, Padatious, Model2Vec) in my benchmarks. If you add an LLM (as small as 1.7B in my tests), it improves further and also handles requests that aren't in any skill's intent data: "what time is it," "what does the clock say" and "read the clock to me" all get you the current time. There's also an experimental decision model (think [Jev](https://typesafe.ai/blog/introducing-system-one-models-and-jev)) for setups without an LLM.

One of Switchboard's key features is ignoring speech that doesn't seem meant for the assistant: the TV, people talking in the room, the assistant hearing itself. So far it's only been measured in English. Other languages work, but in Galician it sometimes ignores real requests, and the LLM prompts are English for now.

It starts in shadow mode, so you can try it safely: it logs what it would have done and changes nothing until you switch it on. It needs ovos-core 3.7+.

Switchboard is very flexible, so I expect people will find ways to use it that I didn't think of. I plan to use it on all my assistants, and I hope the community finds it useful too!

- [https://github.com/OscillateLabsLLC/switchboard](https://github.com/OscillateLabsLLC/switchboard): the intent engine
- [https://github.com/OscillateLabsLLC/switchboard-eval](https://github.com/OscillateLabsLLC/switchboard-eval): the benchmarks
- [https://github.com/OscillateLabsLLC/hangup](https://github.com/OscillateLabsLLC/hangup): the hangup skill, which allows Switchboard to silently ignore requests it doesn't think are meant for the assistant
- [https://github.com/OscillateLabsLLC/doublecheck](https://github.com/OscillateLabsLLC/doublecheck): the doublecheck skill, which allows Switchboard to ask for confirmation on risky requests like unlocking doors or opening the garage
