# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

A Python library for calculating Songkran dates according to the Thai Suriya Yatra (สุริยยาตร์) astronomical calendar system. See RESEARCHES.md for the mathematical foundations and formula derivations.

## Development

Uses `uv` for project management. Python 3.12+.

```bash
uv run python main.py        # Run
uv run pytest                 # Test
uv run pytest -x             # Stop on first failure
uv run pytest -k "test_name" # Run single test
```

## Key Domain Concepts

- **Kammaj** (กัมมัช): fundamental time unit, 1 kammaj = 108 seconds, 800 kammaj = 1 day
- **Horakhun** (หรคุณ): cumulative day count from CS 0 epoch (1-indexed)
- **Kammajaphon** (กัมมัชพล): kammaj remaining until end of Thaloengsok day
- **CS** (จ.ศ., Chula Sakarat): Thai minor era year = BE - 1181 = CE - 638
- **Thaloengsok** (วันเถลิงศก): New Year day, computed exactly by kammaj formula
- **Maha Songkran** (วันมหาสงกรานต์): sun enters Aries, approximated as Thaloengsok - 1732 kammaj
- **สมผุส** (samaphut): true solar longitude after mandaphala correction
