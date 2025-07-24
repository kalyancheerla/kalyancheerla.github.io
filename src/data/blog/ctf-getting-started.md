---
title: "Hacking Legally: My Journey into CTFs and How You Can Start Too"
author: Kalyan Cheerla
pubDatetime: 2025-07-22T11:48:00Z
description: An evolving guide for beginners in cybersecurity competitions – based on my experience, including links, platforms, and a roadmap to start your CTF journey.
tags:
  - personal
  - cybersecurity
  - ctfs
---

## 🚀 TL;DR – How to Start in 60 Seconds
1. Play [Bandit](https://overthewire.org/wargames/bandit/).
2. Register on [picoCTF](https://picoctf.org).
3. Join a local or online community (like [UNTCybersecurity Club](https://unt.campuslabs.com/engage/organization/cyber-security-club)).
4. Keep a repo of your notes & writeups (like [my writeups](https://github.com/kalyancheerla/writeups/)).

---

## Table of contents

---

## 🧠 Why This Post?
Hi, I'm Kalyan — kite, gh0st, M4j0rZer0, l0new0lf — many hacker names; don't you want one? I like breaking, building, and fixing things. [About me](/about).

If you're a student or beginner interested in cybersecurity, this might be your journey too.

---

## 🎯 My journey into CTFs

Like many others, I started by tinkering with Linux, solving small puzzles, and doing challenges on platforms like OverTheWire. Eventually, I began participating in actual CTFs (mostly online).

Recently, I took part in **CANSecCTF** at OU in Norman and ended up winning **2nd place (and a Nintendo Switch Lite 🎮)**. That lit a spark in me, and I've been hooked ever since.

Find my writeups here at [KC writeups](https://github.com/kalyancheerla/writeups). Give it a ⭐ if it helps!

<iframe
  src="https://www.linkedin.com/embed/feed/update/urn:li:share:7258223083264581633?collapsed=1"
  style="width: 100%; aspect-ratio: 1 / 0.9; margin: auto; display: block; overflow: hidden;"
  frameborder="0"
  allowfullscreen
  title="LinkedIn post by UNT Department of CSE"
  scrolling="auto"
/>

HIDDEN TEXT.

---

## 🚩 What Are CTFs?

CTFs — or Capture the Flag competitions — are like digital treasure hunts in the world of cybersecurity. Participants solve puzzles to discover hidden strings called *flags*, like `flag{welc0m3_t0_TH3_w0r1d_0f_Cybers3curity}`, then submit them for points. Think hacking, pentesting — but legally, and for fun!

> Want the formal scoop? Check out [Wikipedia](https://en.wikipedia.org/wiki/Capture_the_flag_(cybersecurity)).

There are different flavors of CTFs:
- **Jeopardy-style:** Solve bite-sized puzzles in categories like crypto, web, reverse engineering, and more.
- **Attack-Defense:** Defend your machine while attacking others — fast-paced and team-based.
- **Real-World style:** Simulate real penetration tests or bug bounty scenarios.

Common challenge categories: [Web exploitation](https://ctf101.org/web-exploitation/overview/), [Binary exploitation](https://ctf101.org/binary-exploitation/overview/), [Cryptography](https://ctf101.org/cryptography/overview/), [Reverse Engineering](https://ctf101.org/reverse-engineering/overview/), [Forensics](https://ctf101.org/forensics/overview/), [OSINT](https://en.wikipedia.org/wiki/Open-source_intelligence), [Steganography](https://en.wikipedia.org/wiki/Steganography), etc.

> 🐞 CTFs vs Bug Bounties: CTFs are great for learning in a controlled environment. Bug bounties are real-world — higher stakes, but higher rewards. Start with CTFs to build confidence.

---

## 🧪 Try a Mini Challenge!

Ready for your first *real* flag? Let's see if you can crack this tiny puzzle:

```txt
Wm14aFozdDVNSFZ5WDJGZlpuSmxRR3NoYm1kZlIyVnVhWFVrWDJKMVpDRjlDZz09Cg==
```

> 💡 Hint: Try base64, then see if it looks familiar...
<details> <summary>🔓 Click here for the flag</summary>
`flag{y0ur_a_fre@k!ng_Geniu$_bud!}`
</details>

🔍 Stuck? Scroll down to the [solution](#solution) when you're ready.

---

## 🧰 What Skills You'll Need

Start small — you don't need to be an expert to begin!

**Here are foundational skills that will help:**
* **Linux**: ssh, cli tools (sed, grep, awk), basic scripting, nano/vi
* **Programming**: Python, Bash
* **Networking**: Ports, IP, HTTP, DNS, TLS/SSL,
* **Crypto Basics**: XOR, base64, hashing, AES, RSA
* **Web Tech**: HTTP methods, RESTful, Cookies, JavaScript basics, XSS, SQL Injection
* **Tools**: `strings`, `file`, `netcat`, `nmap`, `wireshark`, `Burpsuite`, `Ghidra`, `pwntools` etc.


---

## 🧭 A Beginner's Roadmap

1. Play **Bandit** on [OverTheWire](https://overthewire.org/wargames/bandit/)
2. Register and solve a few problems on [picoCTF](https://picoctf.org)
3. Join [UNTCybersecurity Club](https://unt.campuslabs.com/engage/organization/cyber-security-club) or band together
4. Keep your writeups in a repo (like [mine](https://github.com/kalyancheerla/writeups))
5. Follow upcoming competitions on [CTFtime.org](https://ctftime.org)
6. Ask questions, read others' writeups, and participate!

---

## 💻 Practice, practice, practice.

| Platform        | Description                              | Link                                                |
| --------------- | ---------------------------------------- | --------------------------------------------------- |
| **picoCTF**     | Beginner-friendly with story-based CTFs  | [picoctf.org](https://picoctf.org)                  |
| **OverTheWire** | Linux war games via SSH                  | [overthewire.org](https://overthewire.org/wargames) |
| **247CTF**      | Competitive Jeopardy-style CTFs          | [247ctf.com](https://247ctf.com)                    |
| **TryHackMe**   | Guided labs for real-world skills        | [tryhackme.com](https://tryhackme.com)              |
| **HackTheBox**  | VM-based hacking playground              | [hackthebox.com](https://www.hackthebox.com)        |
| **VulnHub**     | Downloadable VMs for hacking             | [vulnhub.com](https://www.vulnhub.com)              |

---

## 🧪 About My CSCE 3550 Guest Lecture

During the **Summer 2025** semester, I delivered a guest lecture on Capture the Flag (CTF) competitions for the Summer 2025 session of *CSCE 3550: Foundations of Cybersecurity*, taught by Dr. Lotfi Ben Othmane.

During the talk, I:
* 🎥 Demonstrated real CTF challenges (web, binary, and crypto)
* 💬 Discussed how CTFs have shaped my journey into cybersecurity
* 📜 Shared my experience competing in CANSecCTF (2nd place 🥈!)
* 🔗 Provided curated resources and tips to help students get started

Thank you to the students who attended and shared kind words after the session!

📂 You can find the slides here: [`CTF Guest Lecture Slides (PDF)`](/assets/files/Getting_started_with_CTFs.pdf)

🧭 This post will continue to serve as a reference hub for all the resources mentioned in the lecture — feel free to explore the links and start your CTF journey!

---

## 🔗 Resources

* [OverTheWire](https://overthewire.org/wargames)
* [picoCTF](https://picoctf.org), [247CTF](https://247ctf.com), [CTFtime](https://ctftime.org)
* [HackTheBox](https://www.hackthebox.com), [TryHackMe](https://tryhackme.com), [VulnHub](https://www.vulnhub.com/)
* [HackTricks Wiki](https://book.hacktricks.wiki/en/index.html)
* bugbounty: [Bugcrowd](https://bugcrowd.com)
* others: [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)
* mine: [writeups](https://github.com/kalyancheerla/writeups)

---

## 👋 Adios

This blog's a work-in-progress — like all of us. I'm no guru, just curious (you should be too). Pick a challenge, break stuff, learn something.

Stuck? Need ideas? Ping me at [LinkedIn](https://linkedin.com/in/kalyancheerla) or [GitHub](https://github.com/kalyancheerla).

Happy hacking 🥷 — Bon voyage, KC.

---

## 🔓 Solution

* If you observe, string ends with `==`, a common sign of base64 encoding. Let's try decoding it.
```sh
🔥 echo "Wm14aFozdDVNSFZ5WDJGZlpuSmxRR3NoYm1kZlIyVnVhWFVrWDJKMVpDRjlDZz09Cg==" | base64 -d
ZmxhZ3t5MHVyX2FfZnJlQGshbmdfR2VuaXUkX2J1ZCF9Cg==
```
* Still looks base64-ish? Let's decode it again:
```sh
🔥 echo "Wm14aFozdDVNSFZ5WDJGZlpuSmxRR3NoYm1kZlIyVnVhWFVrWDJKMVpDRjlDZz09Cg==" | base64 -d | base64 -d
flag{y0ur_a_fre@k!ng_Geniu$_bud!}
```
* Boom! 🧨 You've got the flag.
