# auto-apply-agent 🤖 — An AI agent that fills job applications for you.

<p align="center">
  <img src="assets/hero.png" alt="auto-apply-agent hero" width="1100">
</p>

<p align="center">
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white" alt="TypeScript">
  <img src="https://img.shields.io/badge/Puppeteer-40B5A4?style=for-the-badge" alt="Puppeteer">
  <img src="https://img.shields.io/badge/Claude-191919?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude">
  <img src="https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white" alt="Node.js">
</p>

An autonomous AI agent that fills out Greenhouse job applications end-to-end. Navigates multi-step forms, answers screening questions using context from my resume, handles file uploads, and manages security codes — all without human intervention.

> **This is a closed-source project.** The README documents the architecture and learnings.

## What it does

- Fills Greenhouse applications autonomously from URL to submission
- Answers free-text and multiple-choice screening questions with LLM
- Uploads resume and cover letter (generated per-application)
- Handles CAPTCHA and security code challenges
- Manages browser sessions with automatic cleanup and memory leak prevention
- Detects and recovers from stale page states

## How it works

```
Job URL ──► Launch Browser ──► Navigate to Form
                                     │
                                     ▼
                              Map Form Fields
                                     │
                    ┌────────────────┼────────────────┐
                    ▼                ▼                ▼
              Fill Personal    Answer Questions   Generate Cover
              Info (profile)   (Claude + resume)  Letter (Claude)
                    │                │                │
                    └────────────────┼────────────────┘
                                     ▼
                              Upload Documents
                                     │
                                     ▼
                              Handle Security
                              Code (email poll)
                                     │
                                     ▼
                                  Submit
                                     │
                              ┌──────┴──────┐
                              ▼              ▼
                           Success       Auto-Restart
                                        (crash recovery)
```

Error recovery includes auto-restart on crash, session cleanup, and DOM polling with exponential backoff.

## Tech stack

- **Runtime:** Node.js with Puppeteer for browser automation
- **AI:** Claude via Bedrock for screening question answers and cover letter generation
- **Browser:** Headless Chromium with stealth plugins
- **Infra:** AWS Lightsail, PM2 for process management

## What I learned

- **Browser automation at scale requires treating the browser as an unreliable service** — expect crashes, leaked memory, and stale sessions. Every run needs a cleanup guarantee.
- **LLMs are surprisingly good at answering "Why do you want to work at X?"** when given the job description and your background. The output is better than most human rush-job answers.
- **Security code handling was the hardest edge case** — you need email polling with timeout and retry, and the code expires fast.
