# 🗡 BalisongFlippingCenter

**BalisongFlippingCenter** is a full-stack community platform for balisong knife enthusiasts — a place to share flipping content, catalog knife collections, look up knife specs, discover new tricks, and connect with other flippers. It ships with **Latch**, an AI assistant that answers questions about the site and its knives using live data from the platform's own API — on the site, and in any Discord server through the public **Balisong Bot**.

🌐 Live at [balisongflippingcenter.com](https://www.balisongflippingcenter.com)

This organization hosts the active development of the platform, rebuilt and improved from the original [BalisongFlippingHub](https://github.com/BalisongFlippingHub) project.

---

## 🏗 Repositories

| Repo | Description | Stack |
|---|---|---|
| [BalisongFlippingCenterServer](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterServer) | REST API backend — auth, posts, collections, knife catalog, messaging, moderation | Java · Spring Boot · PostgreSQL · AWS |
| [BalisongFlippingCenterWeb](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterWeb) | Frontend web application, including the Latch chat widget | React · TypeScript |
| [BalisongFlippingCenterAIPython](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterAIPython) | **Latch** — the AI assistant microservice | Python · FastAPI · Claude on AWS Bedrock · Redis |
| [BalisongFlippingCenterDiscordBot](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterDiscordBot) | **Balisong Bot** — public Discord bot: Latch, catalog lookups, combos, dares, and games | Node.js · discord.js · Docker |
| [BalisongFlippingCenterTerraformProd](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterTerraformProd) | Infrastructure as code for the production AWS environment | Terraform · AWS |

---

## ⚙️ Platform architecture

```
                         ┌─────────────────────────────┐
  Browser  ────────────▶ │  CloudFront                  │
                         │   /*     → S3 (React SPA)    │
                         │   /api/* → Spring Boot API   │
                         └──────────────┬──────────────┘
                                        │
  ┌──────────────────┐   ┌──────────────▼──────────────┐       ┌──────────────┐
  │ Discord bot      │──▶│  Spring Boot REST API (EC2)  │ ────▶ │ PostgreSQL 16│
  │ (same EC2,       │   │  JWT auth · STOMP WebSocket  │       │ (Flyway)     │
  │  own container)  │   └──────┬───────────────┬──────┘       └──────────────┘
  └────────▲─────────┘          │               │
        Discord                 │               │
                         ┌──────▼─────┐  ┌──────▼──────────────────┐
                         │   AWS S3   │  │  Latch AI service (EC2)  │ ────▶ Claude
                         │   media    │  │  FastAPI · Redis history │      (AWS Bedrock)
                         └────────────┘  └──────┬──────────────────┘
                                                │ tool calls back into
                                                └─▶ the public REST API
```

All infrastructure is defined in Terraform, with secrets in SSM Parameter Store. Services run as Docker containers, and GitHub Actions builds images to ECR and deploys over SSM — every deploy is gated on the test suite passing.

---

## 🤖 Latch — the AI assistant

Latch is a streaming chat assistant built into the site. Ask it about a knife, a trick, a user, or where to find something, and it answers from the platform's real data rather than model memory.

- **Real tool calling** — Claude decides when to search posts, look up profiles and collections, query the knife catalog, or file a moderation report (logged-in users only), then calls the platform's own REST API and answers from the results.
- **Token-by-token streaming** from Bedrock's Converse API, through the Spring Boot relay, to the browser.
- **Conversation memory** in Redis, trimmed to recent turns with old tool results compacted to keep input tokens bounded.
- **Prompt caching** on the static system prompt and tool definitions (~4,300 tokens served from cache per request).
- **Eval harness** — scenario cases for both the website and Discord personas, run against the real model and graded by deterministic checks plus an LLM judge. Eval-driven fixes took the pass rate from **62% to 92%**.
- **Two clients, one service** — the website and the Discord bot each authenticate with their own client key; the client picks the persona and tool set, so Discord gets a general balisong expert with catalog tools plus web search, and none of the site-only tools.
- **Layered security** — the backend validates per-client credentials and rate-limits each session before relaying; the AI service only accepts traffic carrying a shared internal secret.

---

## 🎮 Balisong Bot — Latch on Discord

A public Discord bot any server can add: **[invite it](https://discord.com/oauth2/authorize?client_id=1511180293107617853&permissions=309237762048&scope=bot%20applications.commands)** · [repo](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterDiscordBot)

- **Ask Latch** with `/latch` or an @mention — answers stream into a thread with per-thread conversation memory.
- **Catalog tools** — knife and maker lookups, side-by-side comparisons, and filtered recommendations from the BFC catalog.
- **Challenges and games** — level-based combo generation from a curated trick list, video-proof landings, dares between users, a daily trick, a timed catalog quiz with a leaderboard, and B.A.L.I. (H.O.R.S.E. for flipping).
- **Built to scale safely** — no privileged intents, replies that can't mass-ping, and per-server and global caps on AI usage as a cost ceiling.
- **Zero added hosting cost** — runs as its own container on the existing backend instance, deployed by GitHub Actions over OIDC → ECR → SSM.

---

## 🛠 Tech stack

**Backend:** Java 22 · Spring Boot 3 · Spring Security · JWT + refresh tokens · PostgreSQL 16 · Flyway · STOMP WebSocket · AWS SDK (S3) · Docker · Maven · JUnit + Testcontainers

**Frontend:** React · TypeScript · Vitest

**AI service:** Python 3.13 · FastAPI · Claude (Haiku 4.5) on AWS Bedrock · Redis · pytest

**Discord bot:** Node.js 22 · discord.js v14 (Components V2, modals, threads) · Docker

**Infrastructure:** AWS (EC2, S3, CloudFront, Route 53, ECR, SSM, Bedrock) · Terraform · GitHub Actions with OIDC

---

## 👤 About the developer

Built and maintained by **Tyler Zenisek**, a backend-focused software engineer specializing in Java, Spring Boot, and cloud-native development.

🌐 [tylerzeniseks.com](https://www.tylerzeniseks.com) · 💻 [github.com/tzenisekj](https://github.com/tzenisekj)
