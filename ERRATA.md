# Author-prepared errata

Paper: D. J. Gormley, “A Hybrid Technique to Increase Throughput of the Streaming Spectrum Sensor,” *2023 IEEE Space Hardware and Radio Conference (SHaRC)*, pp. 34–36, 2023. DOI: [10.1109/SHaRC56958.2023.10046267](https://doi.org/10.1109/SHaRC56958.2023.10046267).

Prepared: 2026-09-02

This document is an author-prepared errata, not an IEEE-issued correction. Page and equation references identify the three-page paper of record. The corrected manuscript is `output/pdf/sharc2023-fact-checked.pdf`; the detailed evidence and compliance audit is in `FACT_CHECK.md`.

## Corrections

1. **Page 1, (1): define the frequency normalization.** The factors printed as `exp(-j2πkn)` and `exp(-j2πvn)` are ambiguous because `k` and `v` are not defined as normalized frequencies. For physical trial frequencies `f_k` and `α_v` sampled at `f_s`, read these factors as `exp[-j2πf_k n/f_s]` and `exp[-j2πα_v n/f_s]`, respectively. The corrected manuscript also writes the filtering operation explicitly and denotes the finite-record estimate with a hat.

2. **Page 2, (3): correct and bound the DFT notation.** For zero-based DFT bin `m`, read the kernel as `exp(-j2πmn/N)` and the physical cycle frequency as `α_m = mf_s/N`. The DC bin must be excluded from candidate testing. Since the FFT input `|X[n]|²` is real, only unique positive-frequency bins need be tested. Off-grid leakage and multiple-bin testing still require peak grouping and false-alarm calibration.

3. **Page 1, Section III-A: correct the throughput arithmetic.** Reducing 11 downstream evaluations to two eliminates nine evaluations, an 81.8% reduction. It does not produce a ninefold speedup. Ignoring preprocessor cost, the upper bound for the illustrated serial-work ratio is `11/2 = 5.5`. Including the SRP cost makes the ratio smaller. The paper reports no serial-baseline measurement, so “increases throughput by a factor of nine” should be read as an unverified design motivation, not a result.

4. **Pages 1–2, Section III-A: correct attribution.** The instantaneous-power FFT/CAF preprocessor concept predates this paper. It is described by M. V. Koch and J. A. Downey, “Interference Mitigation Using Cyclic Autocorrelation and Multi-Objective Optimization,” NASA/TM–2019-220226, 2019, and is recommended in the author's 2021 thesis. The contribution claimed here is integration of that concept into the StSS architecture.

5. **Page 2, Section III-A: qualify the FFT-memory statement.** An `N`-point FFT requires order-`N` frame state, but it does not universally store all state in BRAM or wait for an entire frame before any processing. BRAM, distributed-memory, scaling, latency, and output-order behavior depend on the configured FFT architecture.

6. **Page 2, Sections III-B and III-D: qualify performance and reliability wording.** The two-tap result was an observation for earlier simulation cases; it does not “force periodogram convergence” generally. No retained before/after measurement supports “dramatically improved” center-frequency accuracy. Likewise, the updated distributor is structurally simpler, but reliability was not measured and candidates are discarded when all CFDs are busy.

7. **Page 3, Fig. 6 caption and following paragraph: correct the meaning of `β`.** `β` is a dimensionless threshold multiplier, not the probability of false alarm. `P_fa` is the resulting operating characteristic under a specified waveform, channel, search grid, filter, fixed-point, and decision configuration. Additional CFD instances increase service capacity; they do not reduce the statistical precision needed to calibrate `β`.

8. **Page 3, Section IV and Table I: correct the FPGA part.** “Xilinx xc7k7-t” is not an identifiable device. The listed capacities—303,600 LUTs, 607,200 flip-flops, 1,030 36-Kb BRAMs, and 2,800 DSP slices—match the Virtex-7 **XC7VX485T**. The package and speed grade cannot be recovered from the repository and should not be inferred. The four printed utilization percentages are arithmetically correct.

9. **Page 3, Section IV: qualify latency and scaling claims.** The reported 34.36-s first-result latency cannot be reproduced without the sample rate, FPGA clock, search-grid dimensions, FFT configuration, filter transients, cycle counts, and stall behavior. One two-CFD utilization point cannot establish how BRAM scales with CFD count or demonstrate an end-to-end throughput increase.

10. **Page 3, Section V: replace the conclusion.** The preserved evidence shows only that the reported two-CFD configuration fits an XC7VX485T at the listed utilization. It does not demonstrate improved throughput, timing closure, power, reliability, maintained `P_d/P_fa`, or suitability for smaller edge devices. Those claims require new baseline-versus-hybrid measurements and release of the HDL, testbench, configuration, and synthesis artifacts.

11. **Reference [1]: correct the thesis title.** The official title is “A Low-Memory Spectral-Correlation Analyzer for Digital QAM-SRRC Waveforms.” References should use complete venue data, DOI fields, and persistent online locations as shown in the corrected bibliography.

## Production corrections in the author revision

The author revision applies the 2023 RWW final-paper geometry, uses `\columnwidth` for one-column figures, removes PDF bookmarks and link annotations, normalizes figure/table references and SI-unit spacing, embeds PDF metadata, and balances the final columns. Raster figure resolution and the missing historical corresponding-author telephone number remain documented limitations; no values were invented.
