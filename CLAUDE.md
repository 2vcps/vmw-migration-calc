# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

VMware to OpenShift Virtualization Migration Calculator — a single-page tool that compares migration timelines across three transfer technologies (Network Copy at 100 MB/s, XCopy at 500 MB/s, VolCopy at 800 MB/s). Users configure VM count, concurrency, migration windows, and per-VM data to see projected completion times.

## Architecture

Single self-contained HTML file (`migration-calculator.html`) with inline CSS and JavaScript. No build tools, bundler, or package manager.

**External CDN dependencies (loaded at runtime):**
- Chart.js 4.4.0 — bar charts for completion time and weekly throughput
- html2canvas 1.4.1 + jsPDF 2.5.1 — lazy-loaded on PDF export

**Core data flow:**
1. `technologies` array defines the three transfer methods with speed/color/description
2. `calculate()` reads all input fields, computes per-technology metrics (transfer time, VMs/week, weeks/months needed), then calls the three update functions
3. `updateCharts()` destroys and recreates Chart.js instances on each recalculation
4. `updateTable()` renders the comparison table via innerHTML
5. `updateSummary()` builds summary cards and the bottom-line text, using Network Copy (index 0) as the baseline for savings calculations

**Key assumption:** The baseline for savings percentages is always the first technology in the `technologies` array (Network Copy).

## Development

Open `migration-calculator.html` directly in a browser. All inputs trigger `calculate()` via `onchange`. No build step or server required.
