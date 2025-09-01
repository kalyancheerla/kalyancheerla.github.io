---
title: "VoxPilot: Private Voice-Augmented AI Assistant for Hands-Free Web Browsing"
author: Kalyan Cheerla
pubDatetime: 2025-08-06T19:45:00Z
description: A Chrome extension that uses Gemini Nano to provide a privacy-first, on-device AI assistant for seamless, hands-free, voice-driven web browsing.
tags:
  - project
---
## TL;DR
VoxPilot is a privacy-first, on-device Chrome extension that lets you browse the web hands-free using natural voice or text prompts. It enables seamless navigation, content summarization, and Q&A without sending data to the cloud. Designed for accessibility and productivity, VoxPilot interprets flexible commands in real time using Gemini Nano and browser APIs. Form filling is coming soon!

## Table of Contents
## Introduction
VoxPilot is a privacy-first Chrome extension that lets users navigate, summarize, and interact with web content hands-free using natural voice or text prompts.

### Key Goals
* Assistive AI **Navigation**
* Assistive AI **Summarization with Q&A Prompting**
* Assistive AI **Form Filling**

## Team
* [Bhavani Rachakatla](https://www.linkedin.com/in/bhavani-rachakatla), [Kalyan Cheerla](https://www.linkedin.com/in/kalyancheerla)
* Mentor/Advisor: [Ron Dagdag](https://www.linkedin.com/in/rondagdag)

## Motivation
Many assistive tools still force specially-abled users to memorize rigid scripts, making web browsing frustrating and limiting. VoxPilot removes that barrier by enabling natural, flexible voice or text prompts for **navigation**, **summarization**, and **Q&A**, delivering a seamless, privacy-first browsing experience.

## How It Works
VoxPilot runs entirely on-device, combining built-in browser APIs for speech-to-text (STT) and text-to-speech (TTS), Gemini Nano for natural language understanding, and DOM-aware action execution. Instead of relying on server calls or rigid scripts, it interprets flexible prompts and maps them directly to browser interactions.

### Flow:
* 🎙️ Capture voice input and convert it to text
* 🧠 Use Gemini Nano to parse intent and context
* 🌐 Embed DOM elements for navigation, summarization, or Q&A
* ⚡ Execute actions instantly in the browser

## Designs
![VoxPilot Prompt Engineering Design](/assets/images/VoxPilot_Prompt_Engineering_Design.jpg)

## Video
<iframe style="width: 100%; aspect-ratio: 1 / 0.5; margin: auto; display: block; overflow: hidden;" src="https://www.youtube.com/embed/Hy009vb03WM?si=NKe9sgZz7_U5zb5P" title="VoxPilot Demo Pitch" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

## Outputs
Below are some sample outputs demonstrating VoxPilot's capabilities in action.
### Q&A Prompting
![Q&A Prompting Output](/assets/images/VoxPilot_QnA_Prompting.gif)
### Web Navigation
![Web Navigation Output](/assets/images/VoxPilot_WebNavigation.gif)

## Current Limitations
* AI-assisted form filling is still a work in progress.
* Currently, Google's STT relies on the cloud for transcription; we are exploring on-device, browser-runnable STT models.

## Resources
[VoxPilot PPT](/assets/files/VoxPilot_Pitch_at_DallasAI.pdf), GitHub Link (Coming Soon)

