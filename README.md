<div align="center">
  <h1>🌌 UEFN Verse Expert Skills</h1>
  <p><b>The Ultimate "Swiss Army Knife" for Fortnite & Verse Development</b></p>
  
  <p>
    <img alt="UEFN" src="https://img.shields.io/badge/UEFN-Fortnite-purple?style=for-the-badge" />
    <img alt="Language" src="https://img.shields.io/badge/Language-Verse-00599C?logo=epicgames&logoColor=white&style=for-the-badge" />
    <img alt="Antigravity Ready" src="https://img.shields.io/badge/Agent-Antigravity%20Ready-blueviolet?style=for-the-badge" />
    <img alt="Zero Hallucination" src="https://img.shields.io/badge/Status-Zero%20Hallucination-success?style=for-the-badge" />
  </p>
</div>

<br/>

## 📖 Overview

Welcome to the **UEFN Verse Expert Skills Library**. 

This folder contains **35 specialized Markdown files (`SKILL.md`) and 35 comprehensive Reference Dumps** designed to give AI Agents (and human developers) total architectural mastery over the Verse programming language and Unreal Editor for Fortnite (UEFN).

These skills bypass basic tutorials and dive straight into **Structured Concurrency**, **Device Orchestration**, and **Failable Context Control Flow**—the true pillars of advanced Verse development.

---

## 🗂️ The 5 Pillars of Verse Mastery

### 1. 🛑 Control Flow & Safety (Failure Contexts)
Verse replaces traditional `true/false` logic and exceptions with failable expressions (`<decides>`).
- `verse-failure-contexts.md`: Understanding how `if`, `for`, and `not` handle failures and trigger automatic state rollbacks (`<transacts>`).
- `verse-option-types.md`: Eliminating Null Pointer crashes using `?type` and unwrapping.
- `verse-math-logic.md`: Managing failable math operations like `Floor`, `Ceil`, `Int[]`, and `Mod`.

### 2. ⏱️ Structured Concurrency (Time Management)
Verse introduces time as a native language construct (`<suspends>`).
- `verse-concurrency-structured.md`: Mastering `sync`, `race`, `rush`, and `branch` to manage complex asynchronous events.
- `verse-defer-execution.md`: Using `defer` to guarantee cleanup (e.g., removing UI or stopping cinematics) when a task loses a `race` condition.
- `verse-custom-events-signal.md`: Using the native `event()` class for cross-coroutine synchronization (`Signal()` / `Await()`).

### 3. 🎮 Device Architecture & Game States
How Verse interacts with physical UEFN devices.
- `verse-device-authoring.md`: Building `creative_device` classes and exposing properties via `@editable`.
- `verse-event-subscription.md`: Binding to device events using `.Subscribe()` and managing `cancelable` memory leaks.
- `verse-game-state-loop.md`: Building a master Game Loop to manage Pre-game, Active, and End-game phases entirely in code.
- `verse-spawning-despawning.md` & `verse-prop-manipulation.md`: Moving (`MoveTo`) and spawning (`SpawnProp`) items dynamically.

### 4. 👤 Player Hierarchy & Persistence
Managing the human behind the screen.
- `verse-player-hierarchy.md`: Navigating the difference between `agent`, `player`, and `fort_character` to fetch health and shields.
- `verse-weak-map-persistence.md`: Creating Save Data (`<persistable>`) and preventing memory leaks for disconnected players using `weak_map`.
- `verse-team-management.md`: Dynamic infection and team swapping using `fort_team_collection`.
- `verse-character-elimination-events.md`: Native `DamagedEvent` and `EliminatedEvent` for building Lifesteal or kill-trackers.

### 5. 🎨 Advanced UI, Audio & Mechanics
- `verse-ui-canvas-custom.md` & `verse-hud-controller.md`: Building custom Canvas UI and hiding default Fortnite HUD elements.
- `verse-string-localization.md`: Why you can't use `+` for strings and how to use `<localizes>` `message` types.
- `verse-input-triggers.md` & `verse-camera-api.md`: Hijacking the camera for Top-Down modes and capturing custom Keybinds.
- `verse-vfx-spawner.md`: Controlling Niagara VFX dynamically.
- `verse-npc-behavior-script.md`: Building custom AI logic with `npc_behavior` and `NavigateTo`.

---

## 📂 The `references/` Directory (Memory Dumps)

Verse syntax can be highly specific, especially when dealing with complex Canvas UI or custom Listenable wrappers. 
**For every skill in this folder, there is an accompanying file in the `references/` directory.**

These reference files act as **Memory Dumps**, containing complete, copy-pasteable Verse templates (e.g., a complete Custom Event Wrapper, or a complex UI StackBox layout). 

## 🤖 How to Use (For AI Agents)

1. **Check the Skill**: If tasked with "Creating a Timer", read `verse-timer-device.md` to understand if you should use a physical `timer_device` or a simple Verse `Sleep()` in a `race` block.
2. **Check the Reference**: Open `references/verse-timer-device-ref.md` to get the exact syntax for `.SuccessEvent.Subscribe()`.
3. **Verify**: Run through the `Verification Checklist` at the bottom of the skill before outputting the final Verse code.
