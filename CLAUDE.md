# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Research on calculating Songkran dates according to the Thai Suriya Yatra (สุริยยาตร์) astronomical calendar system. See README.md for the mathematical foundations, formula derivations, and diagrams.

## Key Domain Concepts

- **Kammajaphon** (กัมมัชพล): dual-nature unit measuring both time (108 sec) and solar angular position
- **Horakhun** (หรคุณ): cumulative day count from CS 0 epoch to end of day (24h)
- **Horakhun 0h** (หรคุณ 0 น.): day count to start of day (0h); horakhun = horakhun_0h + 1
- **CS** (จ.ศ., Chula Sakarat): Thai minor era year = BE - 1181 = CE - 638
- **Thaloengsok** (วันเถลิงศก): day the **mean sun** enters Aries (0°); new CS year
- **Maha Songkran** (วันมหาสงกรานต์): day the **true sun** enters Aries; ~2.165 days before Thaloengsok
- **มัธยม** (matthayom): mean position (uniform speed)
- **สมผุส** (samaphut): true position (corrected for non-uniform speed)
- **มันทผล** (mandaphala): equation of center; correction from mean to true position
