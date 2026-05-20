# 🗡 BalisongFlippingCenter

**BalisongFlippingCenter** is a full-stack community platform for balisong knife enthusiasts — a place to share flipping content, discover new tricks, and connect with other flippers.

This organization hosts the active development of the platform, rebuilt and improved from the original [BalisongFlippingHub](https://github.com/BalisongFlippingHub) project.

---

## 🏗 Repositories

| Repo | Description | Stack |
|---|---|---|
| [BalisongFlippingCenterServer](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterServer) | REST API backend — auth, media, community features | Java · Spring Boot · MongoDB · AWS |
| [BalisongFlippingCenterWeb](https://github.com/BalisongFlippingCenter/BalisongFlippingCenterWeb) | Frontend web application | React · TypeScript |

---

## ⚙️ Platform architecture

```
┌─────────────────────┐        ┌──────────────────────────┐
│   React / TypeScript │  ───▶  │  Spring Boot REST API     │
│   Frontend (Web)     │        │  JWT Auth · Spring Security│
└─────────────────────┘        └──────────┬───────────────┘
                                           │
                              ┌────────────┴────────────┐
                              │                         │
                        ┌─────▼──────┐         ┌───────▼──────┐
                        │  MongoDB   │         │   AWS S3      │
                        │  Database  │         │  Media Storage│
                        └────────────┘         └──────────────┘
```

Deployed via Docker with CI/CD through GitHub Actions.

---

## 🛠 Tech stack

**Backend:** Java 22 · Spring Boot 3 · Spring Security · JWT · MongoDB · AWS SDK · Docker · Maven

**Frontend:** React · TypeScript · CI/CD via GitHub Actions

---

## 👤 About the developer

Built and maintained by **Tyler Zenisek**, a backend-focused software engineer specializing in Java, Spring Boot, and cloud-native development.

🌐 [tylerzeniseks.com](https://www.tylerzeniseks.com) · 💻 [github.com/tzenisekj](https://github.com/tzenisekj)
