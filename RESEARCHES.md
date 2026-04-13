# Suriya Yatra Songkran Calculation — Research Notes

Research findings for building a Python library to calculate Songkran dates
according to the Suriya Yatra (สุริยยาตร์) calendar system.

## 1. Overview

The Thai Songkran festival has three days:

| Day | Thai | Meaning |
|---|---|---|
| Maha Songkran | วันมหาสงกรานต์ | Sun enters Aries (ราศีเมษ) |
| Wan Nao | วันเนา | Day after Maha Songkran |
| Thaloengsok | วันเถลิงศก | New Chula Sakarat year begins |

The modern Thai calendar fixes Songkran at April 13-15, but the dates computed
by the Suriya Yatra are typically **April 14-16** (with exceptions such as
BE 2551 and BE 2555 where it falls on April 13-15).

## 2. Time Units

The Suriya Yatra uses the **kammaj** (กัมมัช) as its fundamental time unit:

| Unit | Value |
|---|---|
| 1 kammaj | 108 seconds |
| 1 day | 800 kammaj (= 86,400 seconds) |
| 1 sidereal year | 292,207 kammaj (= 365.258750 days) |

This gives a year length of exactly **365 days 6 hours 12 minutes 36 seconds**.

Angular units follow the Indian astronomical tradition:

| Unit | Value |
|---|---|
| 1 circle | 360 degrees = 12 rasi = 21,600 lipda |
| 1 rasi (ราศี) | 30 degrees = 1,800 lipda |
| 1 degree (องศา) | 60 lipda (ลิปดา, arc-minutes) |
| 1 lipda | 60 pilipda (วิลิปดา, arc-seconds) |

## 3. Epoch

| Property | Value |
|---|---|
| Epoch event | Thaloengsok of Chula Sakarat 0 (จ.ศ. 0) |
| Gregorian date | Sunday, 25 March 638 CE (proleptic) |
| Buddhist Era | BE 1181 (พ.ศ. 1181) |
| Julian Day at midnight | 1,954,167.5 |
| Time of Thaloengsok | 11:11:24 (= 373 kammaj from midnight) |

Year conversions:

```
CS (จ.ศ.) = BE - 1181 = CE - 638
```

## 4. Thaloengsok Calculation

### 4.1 Kammaj Formula (Traditional)

From the คัมภีร์สุริยยาตร์:

```
total_kammaj = 292207 × CS + 373
horakhun     = floor(total_kammaj / 800) + 1
kammajaphon  = 800 - (total_kammaj mod 800)
```

Where:
- **horakhun** (หรคุณ) = cumulative day count from epoch (1-indexed)
- **kammajaphon** (กัมมัชพล) = kammaj remaining until end of Thaloengsok day

The time of Thaloengsok within the day:

```
remainder   = total_kammaj mod 800         # kammaj from midnight
time_seconds = remainder × 108
```

### 4.2 Julian Day Formula

```
JD_thaloengsok = floor((292207 × CS + 373) / 800) + 1954167.5
```

### 4.3 Gregorian Decimal Formula

Attributed to พลตรี บุนนาค ทองเนียม (Lt. Gen. Boonnag Thongniam):

```
VT = CS × 0.25875
     + floor(CS / 100 + 0.38)
     - floor(CS / 4 + 0.5)
     - floor(CS / 400 + 0.595)
     - 5.53375
```

Result: VT is a decimal number where `int(VT)` = April day number and
`frac(VT) × 24` = hours from midnight.

### 4.4 Equivalence

**All three formulas are algebraically equivalent.** Verified computationally
across BE 2540-2589 with zero difference. The Gregorian formula adds
correction terms for the Gregorian calendar's leap year pattern:

| Term | Corrects for |
|---|---|
| `CS × 0.25875` | Suriya Yatra year excess over 365 days |
| `floor(CS/4 + 0.5)` | Gregorian leap day every 4 years |
| `floor(CS/100 + 0.38)` | Gregorian century skip |
| `floor(CS/400 + 0.595)` | Gregorian 400-year restoration |
| `5.53375` | Base epoch alignment |

### 4.5 Worked Example: BE 2569 (CE 2026)

```
CS = 2569 - 1181 = 1388

Kammaj formula:
  total_kammaj = 292207 × 1388 + 373 = 405,583,689
  405,583,689 / 800 = 506,979 remainder 489
  horakhun     = 506,980
  kammajaphon  = 311
  time         = 489 × 108 = 52,812 sec = 14:40:12

Gregorian formula:
  VT = 1388 × 0.25875 + floor(14.26) - floor(347.5) - floor(4.065) - 5.53375
     = 359.145 + 14 - 347 - 4 - 5.53375
     = 16.61125
  → April 16, 14:40:12
```

**Result: April 16, 2026 at 14:40:12**

## 5. Maha Songkran Calculation

### 5.1 Approximate Formula (Fixed Offset)

Maha Songkran is approximated as Thaloengsok minus a fixed interval:

```
offset = 1732 kammaj = 2 days 3 hours 57 minutes 36 seconds = 2.165 days
```

Three equivalent expressions:

```
# JD formula
JD_songkran = floor((292207 × CS - 1359) / 800) + 1954167.5

# Gregorian formula
MS = VT - 2.165

# Kammaj formula
total_kammaj_ms = 292207 × CS - 1359
```

The constant -1359 comes from: 373 (epoch offset) - 1732 (Maha Songkran offset) = -1359.

### 5.2 Worked Example: BE 2569

```
MS = 16.61125 - 2.165 = 14.44625
   → April 14, 10:42:36

Equivalently:
  total_kammaj = 292207 × 1388 - 1359 = 405,581,957
  405,581,957 / 800 = 506,977 remainder 357
  time = 357 × 108 = 38,556 sec = 10:42:36
```

### 5.3 Precision Limitations

The Wikipedia source text explicitly describes the 1732-kammaj offset as an
**approximation** (ประมาณ):

> "สำหรับวันมหาสงกรานต์ สามารถ**ประมาณ**ได้จากหรคุณเถลิงศก"

It then describes the more precise alternative:

> "หรืออาจจะคำนวณตำแหน่งที่สังเกตได้จริง (สมผุส) ของดวงอาทิตย์
> ว่าย้ายเข้าสู่ราศีเมษ ณ วันเวลาใด"

(Or compute the actual apparent position (สมผุส) of the sun to find when
it enters Aries.)

**Kammaj quantization limits the approximate formula's precision.**

The formula produces times quantized to the **108-second kammaj grid**
(every result is an integer kammaj × 108 seconds):

```
Nearest grid points around the Maha Songkran time:
  356 kammaj = 38,448 sec = 10:40:48
  357 kammaj = 38,556 sec = 10:42:36  ← formula result
  358 kammaj = 38,664 sec = 10:44:24
```

The maximum possible error from this quantization is **±54 seconds**. The
actual solar ingress (sun entering Aries) is an astronomical event that has
no reason to land exactly on this 108-second grid.

**Thaloengsok is unaffected** because it is DEFINED by the kammaj formula —
no astronomical observation is involved. Maha Songkran is different because
it represents an actual astronomical event (solar ingress into Aries).

### 5.4 The Full สมผุส Method

To compute the exact Maha Songkran time beyond the kammaj grid, the Suriya
Yatra prescribes computing the sun's true longitude (สมผุส) and finding when
it crosses 0° Aries. This involves the mandaphala (equation of center)
correction, which varies with the sun's position relative to its apogee.
The offset from Thaloengsok to Maha Songkran is therefore NOT exactly 1732
kammaj every year — it varies slightly depending on the mandaphala at the
Aries ingress point.

## 6. The สมผุส (True Solar Longitude) Calculation

The full Suriya Yatra method for finding Maha Songkran involves computing
the sun's **true longitude** (สมผุส) and finding when it crosses 0° Aries.

### 6.1 Mean Sun (มัธยมสุริย์)

The mean solar longitude is proportional to elapsed time:

```
mean_sun_lipda = (total_kammaj × 21600 / 292207) mod 21600
```

The mean sun rate is 21,600 lipda per 292,207 kammaj = 0.073919 lipda/kammaj.

### 6.2 The Mandaphala (มันทผล) — Equation of Center

Corrects the mean sun for non-uniform apparent solar motion. Key parameters:

| Parameter | Thai | Value |
|---|---|---|
| Sun's apogee | อุจจ์อาทิตย์ | 80° (4,800 lipda) — fixed |
| Epicycle (even quadrants) | มันทวงจร | 14° |
| Epicycle (odd quadrants) | มันทวงจร | 13° 40' |
| Max mandaphala | มันทผลสูงสุด | ~134 lipda (~2.23°) |

**Procedure:**

1. Compute kendra (mean anomaly): `kendra = mean_sun - 4800` (mod 21600)
2. Reduce to bhuja (first quadrant): 0-90° equivalent
3. Look up mandaphala from sine table (see below)
4. Apply sign: subtract if kendra < 180°, add if kendra ≥ 180°
5. True sun = mean sun ± mandaphala

### 6.3 Sine Tables

**7-entry simplified table** (from mahamodo-api, pre-computed mandaphala in lipda):

| Bhuja | 0° | 15° | 30° | 45° | 60° | 75° | 90° |
|---|---|---|---|---|---|---|---|
| Mandaphala (lipda) | 0 | 35 | 67 | 94 | 116 | 129 | 134 |

**24-entry Surya Siddhanta table** (Jya values, R = 3438):

```
  3.75°:  225    7.50°:  449   11.25°:  671   15.00°:  890
 18.75°: 1105   22.50°: 1315   26.25°: 1520   30.00°: 1719
 33.75°: 1910   37.50°: 2093   41.25°: 2267   45.00°: 2431
 48.75°: 2585   52.50°: 2728   56.25°: 2859   60.00°: 2978
 63.75°: 3084   67.50°: 3177   71.25°: 3256   75.00°: 3321
 78.75°: 3372   82.50°: 3409   86.25°: 3431   90.00°: 3438
```

With the full Surya Siddhanta method and variable epicycle:

```
circumference = 14 - (14 - 13.667) × |Jya(kendra)| / 3438
phala = (circumference / 360) × Jya(bhuja)
mandaphala = arcJya(phala)        # inverse sine table lookup
```

### 6.4 Precision Levels

| Method | Angular resolution | Time resolution |
|---|---|---|
| 7-entry table, integer lipda | 1 lipda (~0.017°) | ~24 minutes |
| 24-entry table, integer lipda | ~0.15 lipda | ~2 minutes |
| Floating-point with table | continuous | sub-second |

### 6.5 Challenge: The Integer vs Continuous Gap

The traditional integer calculation (mahamodo-style) gives true_sun = -1
lipda at the 1732-kammaj offset, with the crossing to 0 occurring at offset
1725 (a 7-kammaj / 12.6-minute jump due to discrete lipda steps).

The continuous calculation gives true_sun ≈ +29 lipda at the same offset,
implying the crossing happened ~12 hours earlier. This large gap results
from the ~32-lipda difference between integer and continuous mean sun
computations.

**The traditional system was calibrated with integer arithmetic** — the 1732
constant was derived within the integer framework where systematic rounding
errors compensate each other. The continuous version diverges because those
compensating errors are absent.

To achieve sub-kammaj precision for the Maha Songkran time, a hybrid
approach is likely needed: either the traditional integer framework extended
to sub-lipda precision, or a modern continuous computation calibrated to
match the Suriya Yatra's sidereal frame.

## 7. Atta Thaloengsok (อัตตาเถลิงศก) — Additional Calendar Values

Beyond Songkran dates, the Suriya Yatra computes these values at each
Thaloengsok for use in the lunisolar calendar:

```
horakhun    = floor((CS × 292207 + 373) / 800) + 1
kammajaphon = 800 - (CS × 292207 + 373) mod 800
masaken     = floor((703 × horakhun + 650) / (692 × 30))     # lunar months
tithi       = floor(((703 × horakhun + 650) / 692) - 30 × masaken)  # lunar day
avoman      = (703 × horakhun + 650) mod 692                  # lunar excess
uccapon     = (horakhun + 2611) mod 3232                       # moon apogee
war         = horakhun mod 7                                   # weekday
```

These values determine intercalary months (อธิกมาส) and intercalary days
(อธิกวาร) in the Thai lunisolar calendar.

## 8. Existing Implementations

### 8.1 Python

- **pythaidate** ([github.com/hmmbug/pythaidate](https://github.com/hmmbug/pythaidate),
  [PyPI](https://pypi.org/project/pythaidate/))
  - Computes horakhun, kammajaphon, avoman, uccapon, masaken, tithi
  - Handles lunisolar calendar intercalation (adhikamas/adhikavara)
  - Does NOT compute สมผุส / true solar position
  - Does NOT compute Songkran dates

- **splendidmoons** ([github.com/splendidmoons/splendidmoons](https://github.com/splendidmoons/splendidmoons))
  - Similar lunisolar calendar engine for Buddhist Uposatha days
  - Same Suriya Yatra constants, no สมผุส

### 8.2 JavaScript

- **mahamodo-api** ([github.com/realfactory/mahamodo-api](https://github.com/realfactory/mahamodo-api))
  - **Most complete Suriya Yatra implementation found**
  - Full สมผุส calculation for Sun, Moon, and all planets
  - Uses 7-entry mandaphala table, integer lipda resolution
  - Thai astrology horoscope casting
  - Key file: `mahamodo-app/app/helpers/main.js` (lines 4933-5560)

### 8.3 Other Languages

- **KhmerCalendarBar** (Swift) — Cambodian calendar, same Suriya Yatra basis,
  different epoch constants (499 vs 373)
- **thai_calendar** (Ruby) — Thai calendar with JD-based horakhun
- **surya-siddhanta-audit** (TypeScript) — Full Surya Siddhanta with
  24-entry sine table, not Thai-specific

## 9. Key References

### Primary Sources
- คัมภีร์สุริยยาตร์ ตามแนวทางอาจารย์ พลตรี บุนนาค ทองเนียม
  (ISBN 9786163822161)
- คัมภีร์สุริยยาตร์พิศดาร by ทองเจือ อ่างแก้ว
- คัมภีร์โหราศาสตร์ไทยมาตรฐานฉบับสมบูรณ์ by หลวงวิศาลดรุณกร (อั้น สาริกบุตร)

### Web Sources
- [Wikipedia: สงกรานต์](https://th.wikipedia.org/wiki/สงกรานต์) — Formula
  derivation and worked examples
- [kitty.in.th: Songkran Calculation](https://kitty.in.th/index.php/2011/04/13/songkran-day-calculation/) —
  Gregorian decimal formula with worked example
- [kitty.in.th: Songkran BE 2569](https://kitty.in.th/index.php/2026/04/12/วันสงกรานต์-พ-ศ-2569/) —
  Calculation for 2026

### Academic
- Eade, J.C. (2018). *The Calendrical Systems of Mainland South-East Asia*
- Gislen, L. & Eade, J.C. (2019). *The Calendars of Southeast Asia 2:
  Burma, Thailand, Laos and Cambodia*
- Faraut, F.G. (1910). *Astronomie Cambodgienne*

## 10. Open Questions

1. **What is the exact procedure for the full สมผุส Maha Songkran
   calculation?** The traditional integer Suriya Yatra (lipda resolution)
   is too coarse for sub-minute precision. A refined method with sub-lipda
   or floating-point arithmetic is needed.

2. **What ayanamsa (if any) aligns the Suriya Yatra sidereal frame with
   the actual stellar positions?** The Suriya Yatra uses a fixed sidereal
   frame that may have drifted from the original calibration over centuries.

3. **Can the mahamodo-api's integer สมผุส calculation be extended to
   sub-lipda precision** for more accurate Maha Songkran timing?

4. **How much does the Maha Songkran offset from Thaloengsok actually vary
   year to year?** The mandaphala at the Aries ingress should vary slightly
   since the mean sun's position relative to the apogee shifts each year.

## 11. Recommended Library Approach

### Tier 1 — Simple Formula (covers practical needs)
- Thaloengsok: **exact** (defined by the kammaj formula)
- Maha Songkran: **approximate** (-2.165 day / -1732 kammaj offset, ±54 sec max)
- Wan Nao: day after Maha Songkran
- Traditional values: horakhun, kammajaphon, day of week, CS year
- JD ↔ Gregorian conversion

### Tier 2 — Full สมผุส (research project)
- True solar longitude with mandaphala correction
- Port mahamodo-api logic, extend to sub-lipda precision
- Goal: compute Maha Songkran time beyond the ±54 second kammaj grid
