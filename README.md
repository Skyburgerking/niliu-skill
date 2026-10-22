# niliu-injection-engine (skill)

An **Agent Skill** for driving [niliu](https://github.com/Skyburgerking/niliu) — a binary-patching
engine for single-player games — from an LLM agent.

The division of labour is the point:

- **you** (the model) reason about the goal and read JSON receipts;
- **the engine** finds evidence-backed sites, ranks them, patches one, verifies it and hands back a
  rollback command.

You never read assembly, never pick an address, never write a patch by hand.

---

## Recommended workflow

> **Use an LLM to assist development and operation, with this skill loaded.**
> That is the intended path, not an optional extra. The skill card exists because hand-patching and
> manual assembly reading are exactly where both humans and models go wrong.

A normal session looks like this:

```
user:   "make the sun cost 25 instead of 50 in this game"
agent:  load skill -> python niliu.py change <exe> 50 25        (dry run)
        read 4 candidates + evidence -> show them
        user confirms -> python niliu.py change <exe> 50 25 --write
        patch verified by read-back -> restart the game and check
```

---

## Prerequisites

| Requirement | Why |
|---|---|
| An LLM agent with tool use (Claude, GPT, DeepSeek, …) | the card assumes the model calls the engine and reads JSON |
| The niliu engine installed | `git clone https://github.com/Skyburgerking/niliu` |
| Python 3.10+ and `capstone>=5.0` | engine dependency |
| Windows + a PE x86/x64 target | the engine only handles PE |
| A single-player game you own | personal use and study only |

**Ethics / scope:** single-player, offline, your own copy. No online or competitive cheating, no
bypassing anti-cheat, no redistributing patched builds. You are responsible for what you patch.

---

## Install

```bash
# Claude Code / any agent that reads ~/.claude/skills
git clone https://github.com/Skyburgerking/niliu-skill.git ~/.claude/skills/niliu-injection-engine

# DeepCode-style agents
git clone https://github.com/Skyburgerking/niliu-skill.git ~/.deepcode/skills/niliu-injection-engine
```

Windows PowerShell:

```powershell
git clone https://github.com/Skyburgerking/niliu-skill.git "$env:USERPROFILE\.claude\skills\niliu-injection-engine"
```

The folder name must match the skill name in `SKILL.md` (`niliu-injection-engine`).

---

## Files

| File | Language | Purpose |
|---|---|---|
| `SKILL.md` | English | the execution card (frontmatter + rules + protocol + gates + pitfalls) |
| `SKILL.zh-CN.md` | 中文 | same card in Chinese |
| `examples/job.json` | — | a minimal batch job |

---

## What the card covers

- **prerequisites** (LLM-driven, engine installed, legal scope);
- **three iron rules**: "didn't work" means it didn't work; never read assembly to pick a site;
  one site at a time;
- **context discipline**: 40 instructions max, 3 rounds max, no full scans;
- **8-step flow**: probe -> goal -> gate -> receipt -> cross-check -> land -> verify -> archive;
- **22 ops**, including 10 read-only live-memory ops;
- **10 gates** and what each one accepts;
- **receipt format** and how to read `evidence`;
- **failure diagnosis** for "nothing changed";
- **verification numbers** (guards 12/12, exam 14/18, generality 4/6 on 2026-10-23);
- **limits**: jump tables, virtual calls, i16/i8, Unity/ARM64/ELF;
- a worked **PvZ 1.0.0.1051** example and a dated pitfall table.

---

## Related

- Engine: https://github.com/Skyburgerking/niliu
- Usage: https://github.com/Skyburgerking/niliu/blob/master/docs/USAGE.md
- Design: https://github.com/Skyburgerking/niliu/blob/master/docs/DESIGN.md

## License

MIT — see [LICENSE](LICENSE).

> **One line:** the model states `from -> to`; the engine returns evidence-backed sites; landing one
> gives a verified patch and a rollback command.
