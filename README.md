# 🖥️ New PC — Dev Environment Setup

> Setup documentation for getting a development machine ready to work on the
> **Curia** project (AI-Powered Darija Medical Triage).

**Last updated:** May 22, 2026
**Maintained by:** Najda team

---

## 📌 Purpose

This repository collects everything needed to set up a **fresh PC** for Curia
development from scratch. Instead of remembering which tools, versions, and
extensions to install every time, this repo serves as the single reference.

Use it when:
- Setting up a brand-new computer
- Reinstalling Windows / migrating drives
- Onboarding a new teammate
- Reproducing the same environment across machines

---

## 📂 What's Inside

| File | What it covers |
|------|----------------|
| `README.md` | This file — overview of the repo |
| `02-new-pc-full-environment-setup.md` | **Main guide.** Full new-PC setup: Git, Node.js, Python, JDK, VS Code + extensions, Flutter, Android Studio, emulators. Start here. |
| `01-flutter-android-emulator-setup.md` | **Flutter deep-dive.** Detailed Flutter SDK, Android Studio, Android SDK, emulator, and iOS notes. Use when focusing on the mobile side. |

---

## 🧰 Tools Covered

The setup guides walk through installing and configuring:

- **Git** — version control
- **Node.js 22.11.0 LTS** (via nvm-windows) — backend + web frontend
- **Python 3.11.15** — AI service (FastAPI + transformers)
- **JDK 17 (Temurin)** — Flutter Android builds
- **VS Code** + all required extensions
- **Flutter SDK 3.44.0** — mobile app
- **Android Studio** + Android SDK + emulator
- **Docker Desktop** — optional, for deployment

---

## 🎯 Curia Project Stack

For context, these guides target the Curia stack:

| Layer | Technology |
|-------|------------|
| Web frontend | React + TypeScript + Vite + TailwindCSS |
| Backend | Node.js + Express + TypeScript |
| Database / Auth | Supabase (PostgreSQL) |
| AI service | Python + FastAPI (transformers, embeddings) |
| Mobile app | Flutter + Dart |

---

## 🚀 How to Use This Repo

1. Open **`02-new-pc-full-environment-setup.md`** — it's the master checklist.
2. Follow it top to bottom, installing tools in the recommended order.
3. When you reach the mobile section, switch to
   **`01-flutter-android-emulator-setup.md`** for the detailed Flutter steps.
4. Run the verification commands at the end to confirm everything works.

---

## 📝 Notes

- All version numbers reflect what the Curia project currently requires.
- Runtimes and IDEs should be installed on the `C:` drive; project repos,
  SDK folders, and large files can live on a secondary drive.
- Keep this repo updated whenever a tool or required version changes.

---

## 📅 Changelog

| Date | Change |
|------|--------|
| May 22, 2026 | Initial version — new-PC environment documentation created |
