---
title: "Indirect Prompt Injection: Lessons from testing VoxPilot"
author: Kalyan Cheerla
pubDatetime: 2025-09-16T13:53:00Z
description: Tested indirect prompt injection on my AI assistant - VoxPilot, and saw how easily these attacks can scale. Here's why everyone should care.
tags:
  - AI Security
---

When I started building [**VoxPilot**](/posts/voxpilot-client-side-genai) — my browser-based AI assistant that can summarize, navigate, and fill forms — I knew security would be important. What I didn't expect was just how easy it is to trick an AI system through **indirect prompt injection**.

Indirect prompt injection is when a malicious website doesn't attack your browser directly, but instead attacks the AI model's **instructions**. This can hijack the model's behavior, making it ignore your original request and follow the attacker's hidden instructions instead.

Recently, I ran some tests inspired by [Zack's tweet](https://x.com/zack_overflow/status/1959308058200551721), and the results were fascinating — and worrying.

---

### The Tests
### 1. Clean Website + Summarization
I started with a clean website, no hidden prompts. VoxPilot summarized the page perfectly.
<figure>
  <img src="/assets/images/Indirect-prompt-injection-pic1.png" alt="Clean website, no hidden prompts, worked nice">
  <figcaption style="text-align: center;">Clean website, no hidden prompts, worked nice</figcaption>
</figure>

### 2. Visible Indirect-Prompt
Then I added a visible indirect prompt — literally text on the page telling the model to sing a lullaby instead of summarizing. VoxPilot obeyed! Instead of a summary, I got a lullaby. This confirmed the vulnerability.
<figure>
  <img src="/assets/images/Indirect-prompt-injection-pic2.png" alt="Visible indirect prompt, VoxPilot disobeyed">
  <figcaption style="text-align: center;">Visible indirect prompt, VoxPilot disobeyed</figcaption>
</figure>

### 3. Hidden Indirect-Prompt (Ignored)
Next, I hid the prompt using techniques that **screen readers ignore** (like `aria-hidden`). Since VoxPilot only reads what a screen reader would parse, no lullaby — it worked as expected.
<figure>
  <img src="/assets/images/Indirect-prompt-injection-pic3.png" alt="Ignored hidden indirect-prompt, summarized right">
  <figcaption style="text-align: center;">Ignored hidden prompt, summarized right</figcaption>
</figure>

### 4. Hidden Indirect-Prompt through CSS (Successful)
Finally, I hid the malicious prompt using **CSS tricks** (e.g., `display:none`, `visibility:hidden`). The text was invisible to the user but still in the DOM. VoxPilot picked it up and got hijacked again. This was the most concerning result — a malicious site could look perfectly normal but still inject instructions.
<figure>
  <img src="/assets/images/Indirect-prompt-injection-pic4.png" alt="Hidden indirect prompt, VoxPilot disobeyed">
  <figcaption style="text-align: center;">Hidden indirect prompt, VoxPilot disobeyed</figcaption>
</figure>

---

## Why This Matters
These might sound like fun experiments, but the implications are serious:

* **Any website you visit can manipulate AI output.**
* If an AI tool has memory or can take actions (post to Reddit, send emails, etc.), malicious sites can exfiltrate sensitive information.
* Users may never know that they were attacked — they just see "weird" model output.

This is the core danger of **indirect prompt injection**: it's not about breaking into your computer — it's about breaking into the model's **mind**.

---

## Takeaways for Builders
Based on these experiments, here are a few strategies I recommend and I'd love to hear other ideas from the community:
* **Content filtering:** Strip or sanitize hidden text and suspicious patterns before sending it to the model.
* **User transparency:** Warn users when visiting untrusted sites or when hidden prompts are detected.
* **Model-side defenses:** Instruct the model to ignore or refuse page content that tries to override user intent. Think of this like SQL injection prevention, where we use prepared statements to separate data from instructions. Here we need mechanisms to separate the user's request from untrusted page content.

---

## Closing Thoughts
Testing indirect prompt injection on VoxPilot was eye-opening. It showed me that security for browser-based AI assistants isn't just about network calls or XSS — it's also about **model-level adversarial input**.

As AI tools become more powerful and gain memory, agency, and integration with our workflows, these attacks will become more dangerous. Builders need to design for this now, not after the first big exploit.

---

Would love to hear your thoughts! Have you seen other clever prompt injection techniques? I’d love to chat more about these emerging AI security vulnerabilities.

