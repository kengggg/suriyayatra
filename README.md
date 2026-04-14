# Diagram Exploration: ASCII vs Mermaid

Testing which format works best for each diagram type.

---

## Diagram 1: Kammaj Decomposition Tree

### Mermaid (flowchart — natural fit)

```mermaid
graph TD
    T["<b>total_kammaj</b> = 292207 × CS + 373<br/>= 405,583,689"]

    T -->|"÷ 800"| D800["<b>Day decomposition</b>"]
    T -->|"÷ 292207"| D292["<b>Year decomposition</b>"]

    D800 --> Q1["quotient = 506,979<br/><b>หรคุณ 0น.</b><br/>(days to midnight)"]
    D800 --> R1["remainder = 489<br/><b>กัมมัชพลถึงเวลาเถลิงศก</b><br/>(kammaj to Thaloengsok)"]

    Q1 --> H["+1 → 506,980<br/><b>หรคุณเถลิงศก</b><br/>(days to end of day)"]
    R1 --> TM["×108 → 52,812 sec<br/><b>14:40:12</b>"]
    R1 --> KP["800−489 = 311<br/><b>กัมมัชพลเถลิงศก</b><br/>(kammaj remaining)"]

    D292 --> Q2["quotient = 1388<br/><b>CS</b> (complete years)"]
    D292 --> R2["remainder = 373<br/><b>epoch offset</b><br/>(kammaj into current year)"]
```

### ASCII

```
   total_kammaj = 292207 × CS + 373 = 405,583,689
          │
          ├── ÷ 292207 ──┬── quotient = 1388 (= CS, complete years)
          │               └── remainder = 373 (epoch offset)
          │
          └── ÷ 800 ─────┬── quotient = 506,979 (หรคุณ 0น.)
                          │    └── +1 = 506,980 (หรคุณเถลิงศก)
                          │
                          └── remainder = 489 (กัมมัชพลถึงเวลาเถลิงศก)
                               ├── × 108 = 52,812 sec = 14:40:12
                               └── 800 - 489 = 311 (กัมมัชพลเถลิงศก)
```

**Verdict**: Mermaid wins — the tree structure is its sweet spot.

---

## Diagram 2: Songkran Three-Day Window

### Mermaid (timeline)

```mermaid
timeline
    title Songkran BE 2569 — True Sun vs Mean Sun
    April 14 : Maha Songkran
             : TRUE sun = 0° Aries
             : 10꞉42꞉36
    April 15 : Wan Nao
    April 16 : Thaloengsok
             : MEAN sun = 0° Aries
             : 14꞉40꞉12
```

### Mermaid (gantt — shows the gap visually)

```mermaid
gantt
    title Songkran BE 2569 — The Mandaphala Gap
    dateFormat YYYY-MM-DD HH:mm
    axisFormat %b %d

    section Events
    Maha Songkran (true sun = 0°)  :milestone, ms, 2026-04-14 10:42, 0d
    Wan Nao                        :milestone, wn, 2026-04-15 12:00, 0d
    Thaloengsok (mean sun = 0°)    :milestone, ts, 2026-04-16 14:40, 0d

    section Gap
    mandaphala gap ~2.165 days     :active, gap, 2026-04-14 10:42, 2026-04-16 14:40
```

### ASCII

```
     April 14          April 15          April 16
 ────────┼─────────────────┼─────────────────┼──────────
         │                 │                 │
  TRUE sun = 0°            │          MEAN sun = 0°
  ▼ Maha Songkran          │          ▼ Thaloengsok
  10:42:36                  │          14:40:12
         │                 │                 │
         │    Wan Nao      │                 │
         │                                   │
         ├───── mandaphala gap ≈ 2.165 days ─┤
               (1732 kammaj)
```

**Verdict**: ASCII wins — the precise positioning and annotations (arrows,
gap markers) communicate better than Mermaid's timeline/gantt here.

---

## Diagram 3: Day-Level Number Line

### Mermaid (gantt)

```mermaid
gantt
    title Thaloengsok Day — April 16, 2026
    dateFormat HH:mm:ss
    axisFormat %H:%M

    section Time spans
    489 kammaj to Thaloengsok  :active, a1, 00:00:00, 14h40min12s
    311 kammaj remaining       :a2, 14:40:12, 9h19min48s

    section Markers
    midnight (หรคุณ 0น.)       :milestone, m1, 00:00:00, 0d
    Thaloengsok moment         :milestone, m2, 14:40:12, 0d
    midnight (หรคุณ)           :milestone, m3, 23:59:59, 0d
```

### ASCII

```
  midnight                    Thaloengsok              midnight
  0h                          14:40:12                 24h
  ├───────── 489 kammaj ──────┤────── 311 kammaj ──────┤
  │     (remainder from ÷800) │  (800 − remainder)     │
  │                           │                         │
  │←── หรคุณ 0น. = 506,979 ──│                         │
  │←──────── หรคุณ = 506,980 ─────────────────────────→│
  │                           │
  │                    mean sun = 0°
```

**Verdict**: ASCII wins — the double-ended annotations (หรคุณ 0น. vs หรคุณ)
and the mean sun marker need precise positioning that Gantt can't express.

---

## Diagram 4: Zodiac / Angular View

### Mermaid

No circular layout available. A flowchart can show relationships but not geometry:

```mermaid
graph LR
    A["☉ Apogee<br/>80°"] --- B["Mean Sun<br/>~358°"]
    B --- C["True Sun<br/>0° Aries"]

    B -->|"+131 lipda<br/>(mandaphala)"| C
    A -.->|"kendra = 278°<br/>(mean − apogee)"| B
```

### ASCII

```
                      0° Aries ← TRUE sun here
                         │
                  ╭──────┼──────╮
             330°╱  mean ↗│      ╲30°
            ╱    sun     │           ╲
       300°│    ~358°    │            │60°
           │         +131 lipda       │
  270° ────┤      (mandaphala)        ├── 90°
           │             │            │
       240°│             │  ☉ 80°     │120°
            ╲            │ apogee    ╱
             210°╲       │     ╱150°
                  ╰──────┼──────╯
                        180°
```

**Verdict**: ASCII wins decisively — Mermaid simply cannot do circular layouts.

---

## Diagram 5: Concept Relationship Map

### Mermaid (flowchart — this is where Mermaid shines)

```mermaid
graph TB
    CS["CS year<br/>(จ.ศ.)"] -->|"× 292207 + 373"| TK["total kammaj"]
    TK -->|"÷ 800"| HK["หรคุณ 0น.<br/>(day count)"]
    TK -->|"mod 800"| KM["กัมมัชพล<br/>(time of day)"]

    HK --> MS["มัธยมอาทิตย์<br/>(mean sun)"]
    KM --> TS_TIME["Thaloengsok<br/>date & time"]
    HK --> TS_TIME

    MS -->|"+ mandaphala"| SP["สมผุสอาทิตย์<br/>(true sun)"]
    SP -->|"find when = 0°"| SONGKRAN["Maha Songkran<br/>date & time"]
    MS -->|"= 0° at"| TS["Thaloengsok<br/>(mean sun = 0°)"]

    SONGKRAN -.->|"≈ TS − 2.165 days"| TS

    style SONGKRAN fill:#f9d,stroke:#333
    style TS fill:#dfd,stroke:#333
```

### ASCII

```
  CS year ──→ × 292207 + 373 ──→ total_kammaj
                                     │
                          ┌──────────┼──────────┐
                          ÷ 800               mod 800
                          ↓                     ↓
                     หรคุณ 0น.             กัมมัชพล
                     (day count)         (time of day)
                          │                     │
                          ↓                     │
                   มัธยมอาทิตย์ ──────────────→ Thaloengsok
                   (mean sun)              date & time
                          │
                    + mandaphala
                          ↓
                   สมผุสอาทิตย์
                    (true sun)
                          │
                  find when = 0°
                          ↓
                   Maha Songkran ·····≈ TS − 2.165 days
```

**Verdict**: Mermaid wins — the styled nodes and labeled edges are clearer.

---

## Summary

| Diagram | Best Format | Why |
|---|---|---|
| Kammaj decomposition tree | **Mermaid** | Tree structure is Mermaid's strength |
| Songkran 3-day window | **ASCII** | Needs precise timeline annotations |
| Day-level number line | **ASCII** | Double-ended ranges, precise markers |
| Zodiac circle | **ASCII** | Mermaid has no circular layout |
| Concept relationship map | **Mermaid** | Labeled edges and styled nodes |
