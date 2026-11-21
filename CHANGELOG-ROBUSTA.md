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
| DYN-01 | rb-v1 | 2026-11-21 | `soil.jl`: use `wleaf_Tree` only when there are shade trees | Without shade trees the parameters have no `wleaf_Tree`, so full-sun coffee crashed | None needed: with shade trees nothing changes (Vietnam benchmark with 1 tree/ha: identical yields with the fork and v1.5.3) | Full sun runs | Kris, 20 Nov (iteration 1) |
| DYN-09 | rb-v1 | 2026-11-21 | `Harvest_latest_doy` (coffee parameter): with `harvest = "quantity"`, harvest on that day at the latest if there was no harvest in the last 200 days | Rerun with the robusta set: in ~1 year in 4 (late first flowering, overlapping cohorts) the next crop's young fruits kept fruit NPP above overripening, so the 10-day harvest rule never fired and the crop was lost (Dak Lak 2005, 2007: yield 0, fruit overripened) | 0 = off (default) | Fires in 18 of 95 province-years (harvest on 20 Jan of the next year). Doesn't rescue Dak Lak 2005, 2007, 2010, 2016 or Kon Tum 2010, 2016: there the fruit had shrunk below `Min_Fruit_CM` by 20 Jan (see Known problems) | Claude, 21 Nov (found in the rerun) |

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

## rb-v1 results (Benchmark 1, Vietnam, frozen 21 Nov 2026)
Five provinces, ERA5 weather, irrigation and N as in the benchmark protocol; harvests counted to the crop year (Jan–Feb harvests go to the previous year); calibrated `u_log`, `Optimum_Berry_DM`, `RNL_base` on 2000–2010, tested on 2011–2018. Results: `grove-data/research/robusta/benchmarks/dynacof_vietnam/result_rb_v1_fork.json`.

| | Test RMSE (t/ha) | Test MAPE | Detrended r | Harvest |
|---|---|---|---|---|
| rb-v0 (1 shade tree/ha), calibrated | 0.50 | 17.9 % | −0.84 | Jun–Oct |
| rb-v1 (full sun), calibrated | 0.81 | 30.9 % | 0.00 | Oct–Dec (median ~day 330) |

rb-v1 moves the harvest to the right season but fits worse out of sample: 6 of 95 province-years still give no crop.

## Known problems (for rb-v2)
- **DYN-10 (proposed): late-flowering fruit never matures.** In dry starts (Dak Lak 2005, 2007, 2010, 2016) bud break waits until March–April. The fruit can't reach maturity by the end of the year and then shrinks (fruit maintenance respiration over supply) from November, so neither the quantity rule nor DYN-09 harvests it. In practice farmers irrigate in Jan–Feb to force one flowering, and the late fruit would still be picked in Jan–Feb. Options: force bud break with the first irrigation, let maturation continue across the year end, or harvest at a fixed fraction of maturity. Needs flowering and harvest dates from the field (WP1) to choose.
