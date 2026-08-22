Hi HN,

I'm 18, from Balangir, Odisha. No laptop. No lab. No institution. Math done by hand in a notebook, typed on my phone.

THE PROBLEM:

GPS timing fails silently when:
- Jammed (~$200 jammer = km radius denial)
- Jungle canopy (attenuation + multipath)
- Submarines, bunkers (no penetration)

When GPS fails, PTP loses its grandmaster. NTP loses its root. The entire timing hierarchy collapses.

Use cases: field hospitals, power grids, military comms, remote telecom towers.

MY SOLUTION — UPTSS v1
(Ground + Jungle scope only):

GPS-independent, peer-to-peer, wireless timing mesh. No central server. No GPS. No wired infra.

Core bandwidth problem:
  1M pairs × 20 bytes = 20 MB/cycle
  LoRa 250 kbps → 800s to transmit. Infeasible.

Solution: Compute locally, transmit 112-byte summary only:
  μ (mean)         8B  → weighted consensus
  σ² (variance)    8B  → stability scoring
  skewness         8B  → bias detection
  n (count)        4B  → confidence interval
  std error        8B
  timestamp        8B
  node_id          4B
  Ed25519 sig     64B
  ──────────────────
  Total:         112B → 3.6ms on LoRa ✓

Basis: (μ, σ², skew, n) are sufficient statistics for Gaussian timing errors (Fisher–Neyman theorem).

FOUR PROBLEMS I ADDRESSED:

1. LoRa collisions (22% loss with random TX):
   TDMA slots per node + 2000ms interval.
   OCXO drift in 2s ≈ 0.02 ns. Acceptable.

2. Jungle multipath bias:
   Lucky Packet Filter — bottom 15% RTT from 10s window.
   Direct path = minimum RTT. Physics enforced.

3. EMA→Kalman→PID cascade (phase delay + D noise):
   Removed EMA (violates Kalman assumptions).
   Removed D term (noise amplifier on OCXO voltage).
   New: raw e_i → Kalman → PI → OCXO DAC.
   (Gardner PLL textbook: PI only for oscillators.)

4. Delay-injection attack:
   RTT bound = 2d/c + overhead + margin.
   Attacker cannot beat speed of light.
   RTT > threshold → reject.


WHAT I'M HONEST ABOUT:

Hardware: ZERO. Nothing built.
Precision values: design targets only. No measurements.
Gain values (Kp, Ki, K, α, γ): starting points. TBD.
Convergence: NOT guaranteed. No formal proof exists.
Byzantine: HEURISTIC only. Not formal BFT.
T_unified: UPTSS internal time. NOT UTC.


THREE-TIER ARCHITECTURE


Tier 1: FPGA Kintex-7 + Intel X710 + OCXO → 10ns res
Tier 2: Raspberry Pi 5 + LoRa SX1276 → ~1µs res
Tier 3: Any node + GPS → UTC anchor

Tiers handled automatically via EQ04 stability scoring.
Higher precision = higher weight = dominates consensus.

ONE OPEN PROBLEM:

Replaced EMA→Kalman→PID with Kalman→PI for OCXO control. Gardner confirms PI-only for PLLs. But I haven't found formal stability analysis of combined Kalman observer + PI specifically for OCXO EFC voltage control. References welcome.

STATUS:

✓ 44 formulas — analytically validated
✓ 19 corrections documented
✓ System flowchart + white paper written
✓ GitHub repo public
✗ Hardware — zero
✗ Simulation — not yet
✗ Any measured data — none

WHAT I'M ASKING FOR

Not money.

1. I'm asking for a mentor, a lab , 1chance to test this on real hardware 

2. Prior art I missed — especially:
   - Lucky Packet Filtering in timing protocols
   - Entropy-weighted softmax consensus (EQ41)

3. Lab access — FPGA, oscilloscope, RF equipment.
   

4. Professor introductions — distributed timing,
   GPS-denied nav, wireless mesh protocols.


Contact: lokeswarbarik02@gmail.com

I'm probably wrong on several things.
That's exactly why I'm here.

**Author**: Lokeswar Barik  
**Published**: Aug 18, 2026  
**DOI**: [10.5281/zenodo.21999029](https://doi.org/10.5281/zenodo.21999029)

## Abstract
This paper presents UPTSS v1, a GPS-independent, fully wireless, peer-to-peer time synchronization framework for GPS-denied environments such as dense jungle and open ground. The system uses LoRa-based nodes with TDMA scheduling, drift compensation, and filtering to achieve sub-100us synchronization accuracy without GPS.

## 📄 Paper
**Read the full paper**: [Zenodo DOI](https://doi.org/10.5281/zenodo.21999029)  

## Citation