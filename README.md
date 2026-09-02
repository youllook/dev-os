# dev-os · 多 AI 協同開發作業系統

> **不同的活，派不同等級的模型。** 高階模型規劃、低階模型實作、中階模型審查——
> 而三者之間的交接介面，就是 GitHub Issue 本身。

一套可繼承的多 AI 開發流程規範：定義**情境路由**、**角色職責**與 **GitHub 交互邏輯**，
以 git submodule 讓每個新專案直接繼承同一套規則。

[English](#english) · [快速開始](#快速開始) · [七個情境](#情境路由) · [為什麼這樣設計](#為什麼這樣設計)

---

## 問題

多數人用 AI 開發的方式是：開一個對話，把所有事情丟給同一個模型，從規劃到寫 code 到 review 一路做完。

這會有兩個代價：

1. **成本錯配** —— 用最貴的模型去做逐條實作，是拿計畫主持人的時薪去做打字員的工作。
2. **狀態遺失** —— 對話一長，context 被壓縮，AI 忘了自己做到哪、為什麼這樣決定。

`dev-os` 解的就是這兩件事。

## 解法

**成本錯配 → 依任務難度分派模型等級**

| 角色 | 模型層級 | 職責 |
|---|---|---|
| **Planner** | 高階 | 規劃、情境判斷、撰寫 issue |
| **Builder** | 低階 | 逐條 issue 實作、push |
| **Reviewer** | 中階 | code review、build 驗證、缺陷回報 |

規劃需要判斷力，實作只需要照著規格填——**這兩件事不該用同一個價位的模型。**

**狀態遺失 → 把狀態放在 GitHub，不放在對話裡**

Issue 的開關狀態與 comment 就是唯一真相來源。任何一個 AI 角色接手時，
讀 issue 就知道進度，不需要讀完整段對話歷史。

```
Planner 建立 issue → Open
        │
Builder 開發完成 → Comment「開發完成」→ Closed
        │
Reviewer 發現問題 → Comment 問題清單 → Reopen
        │
Planner 回情境 02 → 建立修復 issue（新 issue，不改原 issue）
```

---

## 快速開始

在新專案根目錄：

```bash
git submodule add https://github.com/youllook/dev-os.git .dev-os
git commit -m "chore: add dev-os submodule"
```

每次與 AI 開工時，prompt 開頭放這一句：

```
請載入 .dev-os/router.md，依當前專案狀態判斷情境，告知目前應進入哪個階段。
```

就這樣。AI 會自己讀 router、判斷現在該做什麼、載入對應的 workflow 規則。

<details>
<summary>其他 submodule 指令</summary>

```bash
# clone 含 submodule 的專案
git clone --recurse-submodules {專案 repo URL}

# clone 後才發現沒有 .dev-os/
git submodule update --init

# 同步 dev-os 的更新
git submodule update --remote .dev-os
```
</details>

---

## 情境路由

AI 每次開工先讀 `router.md`，依五個問題判斷當前狀態，進入對應情境：

```
收到任務
  │
  ├─ repo 未初始化？ ──────────────→ 01 INIT          (Planner)
  ├─ 無 issue / 有新需求？ ─────────→ 02 ISSUE IMPORT  (Planner)
  ├─ 有未完成 issue？ ──────────────→ 03 DEVELOP       (Builder)
  ├─ 全部 push 完，未 review？ ──────→ 04 REVIEW & BUILD (Reviewer)
  │        ├─ 通過 → 建 release branch → 06
  │        └─ 不通過 → 回 02（修復走 issue 流程）
  ├─ review 通過，未部署？ ──────────→ 06 DEPLOY        (Planner)
  └─ 已部署 ───────────────────────→ 07 NOTIFY & STANDBY
           ├─ 人工測試不通過 → 回 02
           ├─ 通過 + 有新任務 → 回 02
           └─ 通過 + 無任務 → 待機
```

每個情境都有明確的**觸發條件、執行角色、載入規則、產出定義**，
規則檔在 [`workflows/`](workflows/)，角色規格在 [`ai-roles/`](ai-roles/)。

---

## 為什麼這樣設計

**修復問題永遠開新 issue，不改原 issue。**
原 issue 記錄的是「當初要做什麼」，改掉它等於銷毀證據。新 issue 才能讓「做錯了幾次、錯在哪」留下軌跡。

**重構建議不開 issue**，記錄在 release branch 的 commit message。
不是每個改善意見都值得成為一張待辦——那只會製造永遠清不完的 backlog。

**AI review 只擋 build 失敗，人工測試不通過才回頭建修復 issue。**
AI 適合抓「這編不過」，不適合裁決「這是不是使用者要的」。後者留給人。

**Issue Template 刻意不放在這個 repo。**
GitHub 只讀專案本身的 `.github/` 目錄，submodule 內的無效——這是平台限制，不是疏漏。

---

## 目錄結構

```
.dev-os/
  ├── router.md              ← 情境判斷入口（AI 每次開工先讀）
  ├── workflows/
  │     ├── 01-init.md
  │     ├── 02-issue-import.md
  │     ├── 03-develop.md
  │     ├── 04-review-build.md
  │     ├── 06-deploy.md
  │     └── 07-notify.md
  └── ai-roles/
        ├── planner.md       ← 高階 AI 角色規格
        └── reviewer.md      ← 中階 AI 角色規格
```

---

## 狀態

`v0.1` — 初版。規範已完整，實戰調整仍在進行中。
歡迎 issue 討論，特別是「你的團隊怎麼分派模型等級」這類經驗。

---

## English

**A multi-agent development OS.** Different work, different model tiers — a high-tier model plans,
a low-tier model implements, a mid-tier model reviews. The handoff interface between them is the
GitHub Issue itself.

It solves two problems with the usual "one long chat does everything" approach:

- **Cost mismatch** — using your most expensive model for line-by-line implementation is paying
  architect rates for typing. Roles are mapped to model tiers explicitly.
- **State loss** — long conversations get compacted and the agent forgets where it was.
  Here, state lives in GitHub issues, not in the chat. Any agent can pick up by reading the issue.

Seven routed scenarios (init → issue import → develop → review/build → deploy → notify/standby),
each with defined trigger conditions, owning role, rule file, and expected output. Inherited into
new projects as a git submodule, so every project runs the same protocol.

Rule files are in Traditional Chinese. Install:

```bash
git submodule add https://github.com/youllook/dev-os.git .dev-os
```

Then open every session with: *"Load `.dev-os/router.md`, determine the current scenario from repo
state, and tell me which stage we're in."*

---

## 授權

MIT
