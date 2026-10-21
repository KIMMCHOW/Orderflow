# Orderflow — CLC Trading Decision Tree

> Research framework for GC / NQ intraday trading (5–30 minutes).  
> **CLC:** Context → Strategy Thesis → Location → Confirmation → Risk & Execution.

中文：本仓库用于研究可复现的订单流决策规则。**Strategy Thesis 由 Context 决定；Trapped Trader Zone 是 Location，而不是一个独立 Setup。** 所有信号均为待验证假设，不代表已证明存在正期望。

## CLC Decision Tree v1.0

```mermaid
flowchart TD
    A["CLC Trading System"] --> B{"1. Context<br/>Market Regime"}
    B -->|Balance| C["Strategy Thesis:<br/>Mean Reversion"]
    B -->|Imbalance| D["Strategy Thesis:<br/>Trend Continuation"]
    B -->|Transition / Unclear| X["NO TRADE<br/>Reassess Context"]

    C --> E{"2. Location"}
    D --> F{"2. Location"}

    E --> E1["Balance Edge"]
    E --> E2["VAH / VAL"]
    E --> E3["LVN"]
    E --> E4["PDH / PDL"]

    F --> F1["Trapped Trader Zone<br/>Revisit of prior trapped entries"]
    F --> F2["Pullback Zone"]
    F --> F3["VWAP / Anchored VWAP"]
    F --> F4["LVN / Breakout Retest"]

    E1 & E2 & E3 & E4 --> G{"3. Confirmation"}
    F1 & F2 & F3 & F4 --> H{"3. Confirmation"}

    G --> G1["Absorption + Rejection"]
    G --> G2["Exhaustion + Reclaim"]
    G --> G3["Sweep + Failed Auction"]

    H --> H1["Initiative Flow + Price Progress"]
    H --> H2["Passive Defense + Reclaim"]
    H --> H3["Acceptance + Retest"]

    G1 & G2 & G3 --> I{"Confirmation Valid?"}
    H1 & H2 & H3 --> I

    I -->|No| X
    I -->|Yes| J{"4. Risk Gate"}

    J -->|Invalidation unclear| X
    J -->|Risk unacceptable| X
    J -->|Risk acceptable| K["EXECUTE"]

    K --> L["Protective Stop"]
    K --> M["Predefined Exit"]
    K --> N["Journal + MAE / MFE"]

    classDef context fill:#dbeafe,stroke:#2563eb,color:#1e3a8a
    classDef strategy fill:#dcfce7,stroke:#16a34a,color:#14532d
    classDef location fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef confirm fill:#ede9fe,stroke:#7c3aed,color:#4c1d95
    classDef risk fill:#fee2e2,stroke:#dc2626,color:#7f1d1d

    class A,B context
    class C,D strategy
    class E,F,E1,E2,E3,E4,F1,F2,F3,F4 location
    class G,H,G1,G2,G3,H1,H2,H3,I confirm
    class X,J,K,L,M,N risk
```

## Decision Rules / 决策规则

| Layer | Question / 判断 | If not satisfied / 不满足 |
| --- | --- | --- |
| Context | Is the market regime sufficiently clear? / 市场状态是否清晰？ | No trade |
| Strategy Thesis | Does the selected strategy fit the current Context? / 策略逻辑是否匹配背景？ | No trade |
| Location | Has price returned to a predefined meaningful zone? / 价格是否回到预设位置？ | Wait |
| Confirmation | Did the predefined response and order-flow sequence occur? / 是否出现预设确认？ | Wait or reject |
| Risk | Are invalidation, stop, sizing and exit specified? / 风险边界是否明确？ | No trade |
| Execution | All gates satisfied? / 所有门槛均满足？ | Execute according to plan |

## Definitions / 概念边界

- **Context:** Balance, Imbalance, or Transition/Unclear. Classification requires an explicit timeframe and reproducible rules.
- **Strategy Thesis:** Mean Reversion is the initial default for Balance; Trend Continuation is the initial default for Imbalance. These are research defaults, not universal laws.
- **Location:** Pre-identified trading zone. A **Trapped Trader Zone** marks a prior group of unfavorable aggressive entries; the plan waits for a *revisit* and observes the reaction. Its boundaries and formation criteria must be defined before testing.
- **Trigger vs Confirmation:** A sweep or touch can start observation; rejection, reclaim, acceptance, and other responses can provide confirmation. The event order, thresholds and observation window must be specified for each setup.
- **Order Flow Evidence:** Absorption, exhaustion, initiative flow and liquidity behavior are context-dependent evidence, not automatic trade instructions.
- **Risk:** Protective stop, position size and exit logic are defined before entry. Do not widen risk ad hoc after entry.
- **Validation:** Record all eligible signals, including skipped trades; study costs, MAE/MFE and out-of-sample net expectancy.

> **Research-only:** This decision tree organizes discretionary decision-making. It is not a validated trading strategy or investment recommendation.
