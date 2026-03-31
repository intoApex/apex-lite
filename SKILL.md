---
name: apex-lite
description: "Free OpenClaw cost optimization kit. Model routing config, heartbeat fix ($50/mo to $0.52), bootstrap trim, session hygiene, security checklist. Saves 60-80% on API costs. Real numbers from a live deployment."
version: 1.0.0
author: IntoApex
license: MIT
tags: [cost-optimization, model-routing, heartbeat, security, configuration, free]
---

# APEX Lite — Cut Your OpenClaw API Costs 60-80%

Most OpenClaw setups waste money on three things. This kit fixes all three in under 10 minutes.

Real numbers from our own deployment: $7.60/day down to $0.24/day. Same functionality.

## Fix 1: Heartbeat model routing

Your heartbeats run every 30 minutes on your default model. If that is Sonnet, you are paying around $50/month for HEARTBEAT_OK responses.

Change your heartbeat model to Haiku:

```json
{
  "heartbeat": {
    "model": "anthropic/claude-haiku-4-5"
  }
}
```

Result: $50/month to $0.52/month. Same functionality. 96x cheaper.

## Fix 2: Bootstrap file bloat

OpenClaw injects SOUL.md, AGENTS.md, TOOLS.md, IDENTITY.md, USER.md, and HEARTBEAT.md into EVERY API call. Default AGENTS.md alone is around 8,000 bytes (about 2,000 tokens) of group chat rules, emoji advice, and heartbeat instructions that most autonomous setups never use.

Trim each file to only what the agent needs for every task. Move everything else to MEMORY.md which loads on demand via memory_search.

Target: under 2,500 bytes total across all bootstrap files.

Our setup: 12,000 bytes trimmed to 2,335 bytes. Saved roughly 2,500 tokens per API call. At 50 calls/day on Sonnet, that is $0.75-1.50/day saved.

## Fix 3: Session hygiene for cron jobs

Enable compaction with memory flush so long sessions do not snowball:

```json
{
  "agents": {
    "defaults": {
      "compaction": {
        "reserveTokensFloor": 20000,
        "memoryFlush": {
          "enabled": true,
          "softThresholdTokens": 4000,
          "prompt": "Write lasting notes to memory/YYYY-MM-DD.md. Reply NO_REPLY if nothing to store."
        }
      }
    }
  }
}
```

Use --light-context and --thinking off on cron jobs that do not need full reasoning. This alone cut our engagement session cost from $0.40 per reply to $0.04.

## Fix 4: Memory search instead of context loading

Set up embeddings (Gemini free tier works) so the agent uses memory_search to retrieve relevant context instead of loading everything into the bootstrap:

```bash
openclaw memory index --force
openclaw memory status --deep
```

The agent retrieves what it needs per task instead of injecting all files every call.

## Fix 5: Security hardening checklist

```
[ ] Gateway bound to 127.0.0.1 (not 0.0.0.0)
[ ] Gateway token changed from default (openssl rand -hex 32)
[ ] SSH key auth only (password auth disabled)
[ ] Firewall active (ufw allow SSH only)
[ ] Exec mode set to sandboxed with allowed command list
[ ] Browser tool disabled unless specifically needed
[ ] All ClawHub skills reviewed before installing
```

## Real numbers from our live deployment

Running 6 autonomous cron jobs daily on a $48/month DigitalOcean VPS:

| Metric | Before | After |
|--------|--------|-------|
| Bootstrap size | 12,000 bytes | 2,335 bytes |
| Cost per engagement session | $0.80 | $0.13 |
| Daily post cost | $0.30 | $0.05 |
| Daily LLM total | $7.60 | $0.24 |
| Monthly LLM cost | $228 | $7.20 |

Total system cost after optimization: $55/month (VPS + LLM).

## Want the full system?

APEX Lite gives you cost optimization. The full APEX system adds 7 pre-built automation modules: social media autopilot, business intelligence, support triage, outbound prospecting, content pipeline, Reddit monitoring, and competitor watch. All cost-optimized and deployable in 30 minutes.

Learn more: https://apex-landing-neon.vercel.app
Follow the build journey: @IntoApex on X
