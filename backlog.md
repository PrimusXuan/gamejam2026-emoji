# EMOJI//MASK — Product Backlog（MVP v0.1 / Godot / 单局闭环）

## MVP目标（Sprint 1 必须达成）
做出“一局可玩”的体验：节奏输入 → 处理气泡 → 得分/压力 → 可胜可败可重开。
（遵循：一个机制、一个目标、一个循环、一个体验；第一晚跑通一整局）:contentReference[oaicite:1]{index=1}

---

## 全局规则（写死）
### 输入
- 仅一个输入：Space（Godot InputMap: action = `hit`）

### 节奏判定（不做 accuracy）
- 仅有两种结果：Hit / Miss
- hitTime 暂时不是数组，而是“窗口化节拍”：
  - 每隔 `BEAT_INTERVAL_SECONDS` 生成一个 Beat
  - 每个 Beat 有一个命中窗口：`[center - HIT_WINDOW_HALF_WIDTH, center + HIT_WINDOW_HALF_WIDTH]`
  - 每个窗口最多消耗 1 次 Hit（防止狂按刷分）

### Miss 行为（必须明确）
- Miss = 按下 Space 时不在任何命中窗口内，或该窗口已被消耗
- Miss 只产生反馈，不改变游戏状态：
  - 不清气泡
  - 不加分/不扣分（Score 不变）
  - 不加压力（Overflow 不变）
  - 仅显示“Miss”反馈（文字/闪烁/音效任选其一）

### 处理气泡（不叫 combat）
- Hit 时：若存在气泡 → 清理“最早生成的一个”（FIFO）并加分
- Hit 时：若无气泡 → 不报错，默认不加分

### 结算
- Win：Score ≥ SCORE_THRESHOLD
- Fail（双判定）：OverflowCount ≥ OVERFLOW_LIMIT AND Score < SCORE_THRESHOLD
- 仅满足其中之一：不失败（继续）

---

## 参数占位（先给粗值，Day2再调）:contentReference[oaicite:2]{index=2}
- BEAT_INTERVAL_SECONDS = (TBD)
- HIT_WINDOW_HALF_WIDTH = (TBD)
- HIT_SCORE = (TBD)
- SCORE_THRESHOLD = (TBD)
- OVERFLOW_LIMIT = (TBD)
- BUBBLE_SPAWN_INTERVAL = (TBD)
- BUBBLE_TIMEOUT_SECONDS = (TBD)

---

# Epic E1：单局核心循环（P0 必须）

## PB-01 自动开始一局（无开始界面）
As a player, I want the round to start automatically so I can play immediately.
Priority: P0
Acceptance:
- Given GameScene loads
- When scene is ready
- Then music starts automatically
- And HUD shows Score=0, Overflow=0
- And spawners/judge are active
- And no manual Start UI is required

## PB-02 单一输入（Space → `hit`）
As a player, I want Space to be the only input so controls are simple.
Priority: P0
Acceptance:
- Given the game is Playing
- When I press Space
- Then a single input event is captured (no crash, no double-trigger)

## PB-03 节拍窗口判定（Hit/Miss）
As a player, I want my input judged as Hit or Miss so I know if I pressed in time.
Priority: P0
Acceptance:
- Given time is within a beat window and that window is unused
- When I press Space
- Then result = Hit and that beat window becomes used
- Given time is outside all beat windows OR the current window is already used
- When I press Space
- Then result = Miss
Notes:
- Beat windows are generated from BEAT_INTERVAL_SECONDS + HIT_WINDOW_HALF_WIDTH
- Only one Hit allowed per window

## PB-04 Boss 生成气泡并显示队列
As a player, I want the boss to spawn dialogue bubbles so I have targets to handle.
Priority: P0
Acceptance:
- Given the game is Playing
- When time passes
- Then bubbles spawn every BUBBLE_SPAWN_INTERVAL
- And bubbles appear in UI in spawn order (FIFO)

## PB-05 气泡超时 → Overflow 增加
As a player, I want unhandled bubbles to increase pressure over time.
Priority: P0
Acceptance:
- Given a bubble exists longer than BUBBLE_TIMEOUT_SECONDS
- When timeout occurs
- Then OverflowCount += 1
- And that bubble is removed (or marked expired and no longer targetable)

## PB-06 Hit → 处理一个气泡（FIFO）+ 加分
As a player, I want a Hit to clear one bubble and increase score so I feel progress.
Priority: P0
Acceptance:
- Given bubbleQueue has N > 0
- When Hit happens
- Then the oldest bubble is removed (FIFO)
- And Score += HIT_SCORE
- Given bubbleQueue is empty
- When Hit happens
- Then nothing is cleared and no error occurs

## PB-07 Miss 反馈（不影响分数/压力/气泡）
As a player, I want Miss to show feedback without changing score or pressure.
Priority: P0
Acceptance:
- Given bubbleQueue has any size
- When Miss happens
- Then no bubble is removed
- And Score unchanged
- And Overflow unchanged
- And Miss feedback is shown (text/flash/sfx)

## PB-08 胜利结算（Score 阈值）
As a player, I want to win when I reach the score threshold.
Priority: P0
Acceptance:
- Given Score ≥ SCORE_THRESHOLD
- Then the game ends in Win state and shows result UI

## PB-09 失败结算（双判定）
As a player, I want to fail only when overflow is too high AND score is insufficient.
Priority: P0
Acceptance:
- Given OverflowCount ≥ OVERFLOW_LIMIT AND Score < SCORE_THRESHOLD
- Then the game ends in Fail state
- Given only one condition is true
- Then the game continues (no fail)

## PB-10 结算与重开（Restart）
As a player, I want to restart after Win/Fail so I can replay quickly.
Priority: P0
Acceptance:
- Given result UI is shown
- When I press Restart
- Then Score/Overflow/bubbles/beat-window state reset
- And a new round starts automatically (same as PB-01)

## PB-11 HUD 显示关键数值与反馈
As a player, I want to see Score and Overflow clearly so I understand my status.
Priority: P0
Acceptance:
- HUD always shows current Score and Overflow
- Hit shows a visible feedback (Hit)
- Miss shows a visible feedback (Miss)

---

# Epic E2：可理解性与交付（P1 可选，先放 Ready 但不阻塞）

## PB-12 简易教学提示（1图/2行字）
As a player, I want a simple instruction so I understand controls immediately.
Priority: P1
Acceptance:
- On first seconds of the round, show "Press Space on the beat to clear bubbles."

## PB-13 最小音效/视觉反馈
As a player, I want basic feedback so hits feel responsive.
Priority: P1
Acceptance:
- Hit has one feedback (sfx or flash)
- Bubble cleared has one feedback (pop or fade)

---

# Later（明确不做，放 Later 列）:contentReference[oaicite:3]{index=3}
- 多关卡地图（Stage1–4）
- Emoji 解锁/稀有 emoji
- Perfect/Good/Miss accuracy 细分
- BPM解析/自动谱面
- 多目标/复杂战斗/克制系统
- 复杂剧情文本树

---

## MVP完成定义（DoD - MVP级）
- 可从 GameScene 自动开始一局
- 能 Hit/Miss，有反馈
- Hit 能清泡泡并加分；泡泡超时会加 Overflow
- 能 Win、能 Fail（双判定）、能 Restart
- 主分支可运行，可录屏展示 30 秒
