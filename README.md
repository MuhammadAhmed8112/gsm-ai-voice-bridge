# GSM-to-AI Voice Bridge

An AI voice agent answering a **real SIM card** — not a cloud phone number.

![Architecture](architecture.svg)

---

## The problem

Every "AI voice agent" tutorial starts the same way: buy a Twilio number, point a
webhook at it, done. That works right up until a client says *"we already have a
number, it's a GSM line our customers have been calling for nine years, and it stays."*

A cloud telephony API hands you two things: a SIP endpoint and an HTTP webhook. A
physical GSM line gives you neither. There is no webhook. There is no SIP URI. There
is a SIM card in a gateway, an analogue-ish audio stream, and a call that somebody has
to answer.

## What this does

Bridges that gap. A caller dials the client's existing GSM number and an AI agent
picks up, holds a real conversation, and handles the call end to end. The caller never
knows anything unusual is happening — from their side it is an ordinary phone call.

## How it works

**1. Asterisk terminates the GSM channel.**
Asterisk owns the call. The dialplan decides what happens on answer, on hangup, on
no-answer, and on handoff. This is the piece that makes the GSM line addressable at
all — everything downstream depends on it.

**2. AGI scripts sit inside the audio path.**
Asterisk Gateway Interface scripts are invoked mid-call, with the audio stream in
hand. They stream caller audio out to the agent and stream the agent's speech back
down the same channel, in real time. Latency here is the whole game: too slow and the
caller hears dead air and starts saying "hello? hello?".

**3. The AI agent handles conversation and logic.**
Retell AI runs the actual conversation — intent, turn-taking, business rules, and
whatever the call is meant to accomplish. It is the only part of this stack that would
look familiar to someone who has only ever built on Twilio.

**4. Everything ships as containers.**
Docker Compose brings up Asterisk and the AGI layer together, with a one-command
deploy and written handover documentation, so the client can stand it up on their own
hardware without the developer on a call.

## Why it is harder than it looks

| Cloud API | Physical GSM line |
|---|---|
| SIP endpoint provided | You terminate the channel yourself |
| HTTP webhook on call events | No webhook exists — the dialplan is your event system |
| Audio handled for you | You own the audio path, both directions |
| Provider handles carrier issues | Signal, SIM state, and gateway quirks are yours |

The interesting engineering is not the AI. It is everything underneath it.

## When this approach is the right one

- The client's phone number is not negotiable
- Regulatory or cost reasons rule out porting to a cloud provider
- The deployment has to run on hardware the client controls
- An existing PBX has to keep working alongside the agent

If none of those apply, use a cloud provider — it is genuinely simpler and this
repository would be the wrong tool.

## Stack

Asterisk · AGI · Retell AI · Docker · Python

---

## About this repository

This is a **showcase repository**: architecture and technical write-up for a system
built under a client engagement. The client's dialplan, credentials, business logic,
and deployment specifics are not included and will not be published.

If you are evaluating me for voice or telephony work and want to walk through the
implementation in detail, get in touch and I will happily do that live.

**Muhammad Rayyan** — AI developer, Lahore, Pakistan
[Upwork](https://www.upwork.com/freelancers/~010f3dc36382e38053) ·
[Portfolio](https://rayyan-portfolio-psi.vercel.app) ·
<agharayyan@gmail.com>
