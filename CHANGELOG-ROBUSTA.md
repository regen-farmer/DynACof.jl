# DynACof-RB: robusta changes to DynACof.jl

Grove's fork of [DynACof.jl](https://github.com/VEZY/DynACof.jl) (Rémi Vezy, CIRAD; GPL-3.0) for the APCCO project (Danida; robusta coffee agroforestry in Uganda, WP3 led by Grove/Regen Farmer). Bug fixes are offered upstream as pull requests.

- Branch `rb-v0` = upstream v1.5.3 (`fd6cb44`), the baseline of Benchmark 1.
- Branch `robusta` = our changes. Each released version is frozen as a branch `rb-v1`, `rb-v2` … (Claude's session can push branches, not tags).
- **With the original parameters every change leaves results unchanged** (checked on the Aquiares example). Robusta behaviour comes from a parameter set, recorded below with sources.
- Every change has an ID (DYN-01 …), and an entry below and in the change register of `APCCO WP3 - Model Improvements, Iteration 1 (Specification).md` (Grove project): what, why, evidence before → after, who decided.

## Changes

| ID | Version | Date | What | Why | Switch | Evidence before → after | Decided |
|---|---|---|---|---|---|---|---|
| – | rb-v0 | 2026-11-20 | Baseline: upstream v1.5.3 | Benchmark 1 (Vietnam): test MAPE 18 %; harvest Jun–Oct; can't run without shade trees | – | – | – |
| DYN-01 | rb-v1 | 2026-11-21 | `soil.jl`: use `wleaf_Tree` only when there are shade trees | Without shade trees the parameters have no `wleaf_Tree`, so full-sun coffee crashed | None needed: with shade trees nothing changes | Full sun runs (Benchmark 1 rerun) | Kris, 20 Nov (iteration 1) |

## Robusta parameter set rb-v1 (DYN-02, DYN-03, DYN-04)
Parameters only, no code: given to `dynacof` like any other parameter set.

| Parameter | Arabica (Aquiares) | Robusta (Central Highlands) | Why |
|---|---|---|---|
| `DVG1`, `DVG2` | 105, 244 | 120, 300 | Vegetative season = the monsoon (May–Oct) |
| `F_Tffb` | 4000 dd | 200 dd | Floral buds form at the end of the rains (Nov), not ~a year later |
| `F_buds1`, `F_buds2` | 840, 2562 dd | 600, 2000 dd | Buds ready by ~Dec; dormancy can last until the first irrigation or rain (Jan–Apr) |
| `F_rain` | 40 mm | 40 mm | Unchanged (blossom shower) |
| `u_log`, `F_over` | 1418, 3304 dd | 1914, 4460 dd | Robusta ripens ~1.35× slower (9–11 months) |
| `Stocking_Coffee` | 5580 /ha | 1100 /ha | Robusta at 3 × 3 m |
| `DELM` | 2.0 gC/plant/d | 10.1 | Per-plant leaf demand scaled with the lower density (5580/1100) |
