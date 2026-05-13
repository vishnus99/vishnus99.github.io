---
layout: post
title: "GoodLooks - An AI-powered CLI for local to-do management"
date: 2026-05-13
categories: cli, productivity, machine-learning
permalink: /goodlooks/
---

## Introduction

Scatterbrain syndrome is a condition where one is unable to remember all the tasks they have to finish due to the sheer volume of tasks to do. It is compounded by ability paralysis, which is a term I have coined to describe the feeling of not doing something because you don't know where to start. To address this common problem for developers, I created GoodLooks: a local to-do manager that lives in the terminal. Tasks stay on your machine unless you choose a cloud language model, and recommendation requests are sent only when you run `recommend`. By combining the speed of terminal-level execution with helpful assistance from Ollama, it has never been easier to track the tasks you need to finish and get step-by-step guidance for completing them.

![GoodLooks terminal UI]({{ '/blog_images/goodlooks_dashboard.png' | relative_url }})

[Check out the repo here!](https://github.com/vishnus99/goodlooks)

## Core CLI experience

GoodLooks was built with quick, user-focused functionality as the highest priority. A to-do list shouldn't need a fancy GUI or an unintuitive syntax in order to get your tasks organized and completed. With this in mind, the CLI has all the commands you would need in order to manipulate your to-do list: add, rm, edit, done. These four commands cover adding and removing tasks, editing them, and marking them complete. As always, 'help' is available to see the full menu of available commands.

## Board-style status view

The 'status' command provides the most visibility. Pending and completed tasks are grouped separately, and both are vibrantly color-coded to differentiate them at a glance.

![GoodLooks status]({{ '/blog_images/goodlooks_status.png' | relative_url }})

## AI recommendations

GoodLooks is augmented by a LangChain implementation that enables it to provide users with AI recommendations to complete the given task. By using the 'recommend' command with a task id, you can either use the built-in Ollama integration (no API key required) or an OpenAI language model of your choice (requires an OpenAI API key) to get a concrete starting point for any task. `recommend` returns a short, actionable list of next steps for the task. If you use OpenAI, that task text is sent to the provider for that request.

### Provider options

```bash
export GOODLOOKS_RECOMMENDER_PROVIDER=openai
# or
export GOODLOOKS_RECOMMENDER_PROVIDER=ollama
export GOODLOOKS_LLM_MODEL=llama3.1
```

### Ollama integration

The following commands start Ollama, check its health, pull the configured model, and use it with the 'recommend' command.

```bash
goodlooks ollama start
goodlooks ollama status
ollama pull llama3.1
goodlooks recommend --id 2
```
Here's how it looks in practice:

![GoodLooks Ollama recommend]({{ '/blog_images/goodlooks_ollamarec.png' | relative_url }})

### Heuristic fallback

If the Ollama instance fails for whatever reason, a rule-based heuristic recommender is used instead to provide a more general set of recommendations for a given task.

![GoodLooks Ollama fallback]({{ '/blog_images/goodlooks_localfallback.png' | relative_url }})

## Setup and configuration

The default configuration is an easily editable JSON file that can be configured to use any supported OpenAI model and a custom Ollama base URL.

```bash
goodlooks setup
```

```json
{
  "backend": "langchain",
  "provider": "auto",
  "model": "llama3.1",
  "timeout_sec": 8,
  "ollama_base_url": "http://127.0.0.1:11434"
}
```

## Validating the setup

If anything breaks, the built-in 'doctor' command provides diagnostic output regarding the overall health of the application. The 'doctor' command also includes a built-in `--fix` flag that automatically repairs issues that arise from the Ollama instance or the backend. The `--json` flag provides a JSON version of the health diagnostics.

```bash
goodlooks doctor
goodlooks doctor --fix
goodlooks doctor --json
```
![GoodLooks doctor]({{ '/blog_images/goodlooks_doctor.png' | relative_url }})

## Lessons Learned and Future Work

GoodLooks already supports Ollama and OpenAI today. Future work will broaden that provider surface to include additional hosted and local backends. There are also plans to expand the autofix capabilities of the 'doctor' command by using LangChain to suggest and apply more targeted remediation steps when something goes wrong.

## Try it yourself

Install from the [GoodLooks repository](https://github.com/vishnus99/goodlooks):

```bash
git clone https://github.com/vishnus99/goodlooks.git
cd goodlooks
python3 -m pip install -e .
goodlooks --help
```
