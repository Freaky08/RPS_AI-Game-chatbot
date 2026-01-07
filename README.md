# Rock–Paper–Scissors–Plus — AI Referee (Google ADK)

## Overview

This project implements a minimal AI referee for a **Rock–Paper–Scissors–Plus** game using **Google ADK**.  
The referee manages game flow, validates moves, enforces rules, tracks state across turns, and automatically ends the game after three rounds.

The system is designed to be **deterministic**, **robust**, and **easy to reason about**.

---

## State Model

Game state is maintained in a single in-memory Python dictionary (`game_state`) that persists across turns.

It includes:

- `round`: Current round number (max 3)
- `user_score`, `bot_score`: Scores for each player
- `user_bomb_used`, `bot_bomb_used`: Tracks one-time bomb usage
- `history`: Per-round audit log (moves and winner)

State is **not stored in prompts** and is updated only through explicit game logic, ensuring predictable behavior and easy testing.

---

## Agent & Tool Design

The solution uses **Google ADK’s `Agent` and `FunctionTool` primitives**.

### Tools

Three tools encapsulate all core game logic:

- **`validate_move`** – Validates input, enforces allowed moves, and applies penalties for illegal bomb reuse  
- **`resolve_round`** – Determines the winner based on game rules  
- **`update_game_state`** – Mutates persistent state after each round  

These tools cleanly separate **game logic** from **control flow**.

---

### Agent

A custom **`RPSRefereeAgent`** extends the ADK `Agent` and orchestrates the game:

- Interprets user input
- Calls validation and resolution logic
- Manages penalties and round progression
- Produces user-facing responses

The agent acts as the **control boundary**, while tools act as **pure logic units**.

---

## Tradeoffs

- **Deterministic logic over LLM reasoning**  
  Game decisions are handled in code rather than through LLM inference to ensure correctness and avoid nondeterministic behavior.

- **CLI-based interaction**  
  A simple chat-style CLI loop was chosen to focus on logic and architecture rather than UI complexity.

- **Declarative ADK tools**  
  Due to ADK’s design, tools are registered with the agent but invoked directly in deterministic flows, which aligns with current SDK constraints.

---

## Improvements With More Time

- Add structured JSON output for easier UI or API integration
- Implement smarter bot strategies instead of random choice
- Add automated tests for edge cases and rule enforcement
- Extend to multi-game sessions with reset and replay support
- Integrate natural-language intent parsing for freer user input

---

## Summary

This implementation prioritizes **correctness**, **clarity**, and **maintainability**.  
It demonstrates a clean separation of state, logic, and orchestration while using **Google ADK** appropriately within its current capabilities.
