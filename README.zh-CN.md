# niliu-injection-engine（技能）

给 LLM 代理用的 **Agent Skill**，驱动 [niliu](https://github.com/Skyburgerking/niliu) —— 单机游戏
二进制改值引擎。

分工是这套东西的关键：

- **你（模型）** 负责理解目标、读 JSON 回执；
- **引擎** 负责找带证据的站点、排序、落一处、独立回读、给回滚命令。

你不读汇编、不挑地址、不手写补丁。

---

## 推荐用法

> **建议由 LLM 协助开发与操作，并加载本技能。**
> 这是设计上的正路，不是可选项。之所以写成技能卡，就是因为「手工翻汇编」正是人和模型都最容易出错的地方。

一次典型会话：

```
用户：   "把这游戏里阳光花费从 50 改成 25"
代理：   加载技能 → python niliu.py change <exe> 50 25      （干跑）
        读 4 条候选 + 证据 → 给用户看
        用户确认 → python niliu.py change <exe> 50 25 --write
        回读验证通过 → 重启游戏确认
```

---

## 前提

| 条件 | 原因 |
|---|---|
| 支持工具调用的 LLM（Claude、GPT、DeepSeek 等） | 卡片假设模型能调引擎、读 JSON |
| 已安装 niliu 引擎 | `git clone https://github.com/Skyburgerking/niliu` |
| Python 3.10+ 与 `capstone>=5.0` | 引擎依赖 |
| Windows + PE x86/x64 目标 | 引擎只处理 PE |
| 自己拥有的单机游戏 | 仅限个人使用与学习 |

**范围与底线：** 单机、离线、自己的游戏。不做联机/竞技作弊，不绕反作弊，不再分发改过的游戏。
改什么、用来做什么，由使用者负责。

---

## 安装

```bash
# Claude Code / 读取 ~/.claude/skills 的代理
git clone https://github.com/Skyburgerking/niliu-skill.git ~/.claude/skills/niliu-injection-engine

# DeepCode 风格的代理
git clone https://github.com/Skyburgerking/niliu-skill.git ~/.deepcode/skills/niliu-injection-engine
```

Windows PowerShell：

```powershell
git clone https://github.com/Skyburgerking/niliu-skill.git "$env:USERPROFILE\.claude\skills\niliu-injection-engine"
```

目录名必须与 `SKILL.md` 里的 `name`（`niliu-injection-engine`）一致。

---

## 文件

| 文件 | 语言 | 作用 |
|---|---|---|
| `SKILL.md` | 英 | 执行卡（frontmatter + 铁律 + 协议 + 判据 + 坑表） |
| `SKILL.zh-CN.md` | 中 | 同一张卡的中文版 |
| `examples/job.json` | — | 最小批处理 job |

---

## 卡片里有什么

- **前提**：LLM 驱动、引擎已装、合法范围；
- **三条铁律**：「没成」就是没成；不许读汇编挑站点；一次只改一处；
- **上下文纪律**：最多 40 条指令、3 轮、禁止全量扫描；
- **八步流程**：摸底 → 定目标 → 选判据 → 读回执 → 交叉验证 → 落一处 → 进游戏验 → 记账/回滚；
- **22 个 op**，含 10 个只读活内存 op；
- **10 条判据**及各自放行条件；
- **回执格式**与 `evidence` 读法；
- **「没变」诊断顺序**；
- **实测基线**（2026-10-23：守卫 12/12、考试 14/18、通用性 4/6）；
- **边界**：跳表、虚调用、i16/i8、Unity/ARM64/ELF；
- **PvZ 1.0.0.1051** 实例与带日期的坑表。

---

## 相关

- 引擎：https://github.com/Skyburgerking/niliu
- 用法：https://github.com/Skyburgerking/niliu/blob/master/docs/USAGE.zh-CN.md
- 设计：https://github.com/Skyburgerking/niliu/blob/master/docs/DESIGN.md

## 许可

MIT，见 [LICENSE](LICENSE)。

> **一句话：** 模型说 `from → to`，引擎回几处带证据的站点；落一处，得到验证过的补丁和一条回滚命令。
