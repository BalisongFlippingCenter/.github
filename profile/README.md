# 🗡 BalisongFlippingCenter

**BalisongFlippingCenter** is a full-stack community platform for balisong knife enthusiasts — a place to share flipping content, catalog knife collections, look up knife specs, discover new tricks, and connect with other flippers. It ships with **Latch**, an AI assistant that answers questions about the site and its knives using live data from the platform's own API.

🌐 Live at [balisongflippingcenter.com](https://www.balisongflippingcenter.com)

This organization hosts the active development of the platform, rebuilt and improved from the original [BalisongFlippingHub](https://github.com/BalisongFlippingHub) project.

---

## 🏗 Repositories

| Repo | Description | Stack |
|---|---|---|
| [BalisongFlippingCenterServer](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterServer) | REST API backend — auth, posts, collections, knife catalog, messaging, moderation | Java · Spring Boot · PostgreSQL · AWS |
| [BalisongFlippingCenterWeb](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterWeb) | Frontend web application, including the Latch chat widget | React · TypeScript |
| [BalisongFlippingCenterAIPython](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterAIPython) | **Latch** — the AI assistant microservice | Python · FastAPI · Claude on AWS Bedrock · Redis |
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
                         ┌──────────────▼──────────────┐       ┌──────────────┐
                         │  Spring Boot REST API (EC2)  │ ────▶ │ PostgreSQL 16│
                         │  JWT auth · STOMP WebSocket  │       │ (Flyway)     │
                         └──────┬───────────────┬──────┘       └──────────────┘
                                │               │
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
- **Eval harness** — 29 scenario cases run against the real model and graded by deterministic checks plus an LLM judge. Eval-driven fixes took the pass rate from **62% to 92%**.
- **Layered security** — the backend validates per-client credentials and rate-limits each session before relaying; the AI service only accepts traffic carrying a shared internal secret.

---

## 🛠 Tech stack

**Backend:** Java 22 · Spring Boot 3 · Spring Security · JWT + refresh tokens · PostgreSQL 16 · Flyway · STOMP WebSocket · AWS SDK (S3) · Docker · Maven · JUnit + Testcontainers

**Frontend:** React · TypeScript · Vitest

**AI service:** Python 3.13 · FastAPI · Claude (Haiku 4.5) on AWS Bedrock · Redis · pytest

**Infrastructure:** AWS (EC2, S3, CloudFront, Route 53, ECR, SSM, Bedrock) · Terraform · GitHub Actions with OIDC

---

## 👤 About the developer

Built and maintained by **Tyler Zenisek**, a backend-focused software engineer specializing in Java, Spring Boot, and cloud-native development.

🌐 [tylerzeniseks.com](https://www.tylerzeniseks.com) · 💻 [github.com/tzenisekj](https://github.com/tzenisekj)
