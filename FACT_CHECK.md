# Fact-check and compliance audit

Audit date: 2026-09-03
Repository revision reviewed: `97f4559331b3c573510396e953bf24704efac086`
Supplemental archive reviewed: `spectrum-sensor-20260903T023016Z-1-001.zip`, SHA-256 `4960a061ccf22ab7382eafa4eb4e30ddd9e07cd0d7a29f4062755c34a639a8cf`

## Outcome

The repository's historical paper was published at the 2023 IEEE Space Hardware and Radio Conference (SHaRC), pp. 34–36, under DOI [10.1109/SHaRC56958.2023.10046267](https://doi.org/10.1109/SHaRC56958.2023.10046267). The original PDF is preserved unchanged. The revised source and `output/pdf/sharc2023-fact-checked.pdf` correct claims that can be resolved from the paper repository and the author-supplied supplemental implementation archive, while explicitly labeling conditional calculations and results that still cannot be independently regenerated.

This is an author-side audit, not an erratum accepted by IEEE. IEEE's [post-publication policy](https://conferences.ieeeauthorcenter.ieee.org/author-ethics/guidelines-and-policies/post-publication-policies/) governs any correction to the version of record.

## Material factual corrections

| Priority | Original claim | Finding | Disposition |
|---|---|---|---|
| Critical | Fourier kernels used `exp(-j2πkn)` and `exp(-j2πvn)` without defining normalized frequencies. | The expression is ambiguous and is wrong if `k` and `v` are integer bins or physical frequencies. | Rewritten with physical frequencies `f_k`, `α_v`, and sample rate `f_s`; the SRP DFT is written with integer bin `m/N`. |
| Critical | Reducing 11 serial symbol-rate evaluations to two gives a factor-of-nine throughput increase. | Nine is the number eliminated. Ignoring preprocessor cost, the upper-bound work ratio is `11/2 = 5.5`; actual speedup is smaller. | Corrected and labeled an illustrative upper bound, not a measurement. |
| Critical | The target device was “Xilinx xc7k7-t.” | That is not an identifiable Xilinx part. The table capacities exactly match the Virtex-7 XC7VX485T in [AMD DS180](https://docs.amd.com/v/u/en-US/ds180_7Series_Overview). | Corrected to XC7VX485T; unknown package and speed grade are disclosed. |
| Critical | `β` was the probability of false alarm. | The cited predecessor defines `β` as a threshold scale factor. `P_fa` is an operating characteristic induced by the threshold and test conditions. | Corrected throughout. Separate SRP/CFD calibration and family-wise false-alarm measurement are required. |
| High | The FFT preprocessor was presented as a new component without primary attribution. | The underlying instantaneous-power FFT/CAF preprocessor is described by Koch and Downey in [NASA/TM–2019-220226](https://ntrs.nasa.gov/citations/20190027051), and the 2021 thesis recommends it. | Primary attribution added; the paper now claims integration/architecture rather than invention of the method. |
| High | An FFT must store `N` values in BRAM before producing a result. | Storage and latency depend on core architecture, scaling, and output order. The supplemental core is 65,536-point, 16-bit fixed point, pipelined streaming, unscaled, natural order, and BRAM-backed, but its XCKU5P checkpoint is not the Table I build. | Replaced with the saved configuration and provenance caveat; see [AMD PG109](https://docs.amd.com/v/u/en-US/pg109-xfft). |
| High | More CFDs reduce the precision required for `β`. | Parallel CFDs change service capacity, not the detector's statistical operating point. | Removed; the corrected text discusses bottlenecks and candidate drops. |
| High | Two taps “force periodogram convergence,” and the filter bank “dramatically” improves accuracy. | A two-tap smoother cannot establish convergence generally. The supplemental snapshot retains 128 banks of 41 signed 16-bit taps but no before/after accuracy data. | Restricted to the prior reported simulation observation; the hardware filter specification is recorded and the improvement remains unquantified. |
| High | The design increased throughput, improved reliability, and was suitable for edge devices. | The paper provides one utilization point and no serial baseline, measured throughput, post-SRP `P_d/P_fa`, timing closure, power, or drop-rate result. The 34.36-s value is reconstructible only as a conditional finest-grid calculation. | Title, abstract, results, and conclusion now describe reported fit, the conditional latency calculation, and required validation rather than claiming demonstrated system improvement. |

The four resource percentages were recomputed and are correct: 7.40% LUTs, 2.54% flip-flops, 18.11% 36-Kb BRAM equivalents, and 10.14% DSP48E1 slices. Resource names were aligned with DS180.

## Algorithmic caveats added

- The SRP excludes the DC bin. With `q[n] = |X[n]|²`, the DC statistic equals mean input power and will trivially exceed the power-scaled threshold whenever `β < 1`.
- Because `q[n]` is real, negative FFT bins duplicate positive bins by conjugate symmetry. For the reported even `N`, the strictly positive unique range is `1` through `N/2 - 1`; the surviving RTL further suppresses bin 1 and emits bins 2 through 32,767.
- Off-grid cycle frequencies leak into adjacent bins. Candidate grouping, search-grid multiplicity, FFT scaling, and filter equivalent noise bandwidth affect the false-alarm rate.
- A candidate is discarded whenever every CFD is busy. The active top-level path has no candidate backpressure, and a write presented to a full result FIFO is rejected without retry. Without arrival, drop, and collision measurements, the scheduling change does not establish improved reliability.
- The reported 34.36-s first-result latency is strongly reconstructed as `2^16` center-frequency hypotheses times `2^16` samples at 125 MHz, or 34.359738368 s, for `FcStep = 1`, continuous one-sample-per-clock input, and no stalls. It is a conditional calculation rather than a retained end-to-end measurement.
- One two-CFD synthesis point cannot establish resource scaling with CFD count.

## Supplemental implementation archive

The 62,810,971-byte supplemental archive was inspected as a separate implementation snapshot; it is not part of the paper repository. Its embedded Git data is incomplete: the pack data are absent, `HEAD` does not resolve to a local branch, and the working tree cannot be tied reliably to an exact surviving commit. Several source headers and project artifacts name incompatible devices and tool settings. These limitations prevent treating the snapshot as the exact conference synthesis build.

Recoverable details include:

- The top-level defaults and testbench use `N = 2^16`, 16-bit samples and frequency-control words, two CFD instances, and a 125-MHz sample clock.
- `CenterFrequencyDetector.vhd` begins at the signed 16-bit value for `-f_s/2` and advances one `FcStep` after each `N`-sample frame. This produces the 34.36-s reconstruction above for `FcStep = 1`. The saved testbench instead derives `FcStep = 1638` from 3.125 MHz; a separate four-CFD waveform reaches its first result at 22.544868 ms, consistent with 41 search frames plus approximately two startup frames.
- `xfft_0.xci` configures a 65,536-point Xilinx FFT v9.1 with 16-bit fixed-point input, pipelined-streaming I/O, unscaled arithmetic, convergent rounding, natural-order output, and block-RAM data and phase-factor storage. The customization target is 125 MHz, but interface metadata and the out-of-context constraint use 100 MHz.
- `GenericFir_pkg.vhd` contains 128 coefficient banks with 41 signed 16-bit taps each. The generator specifies a 125-MHz sample rate, order 40, and 488.28125-kHz bank spacing. No equivalent-noise-bandwidth or before/after detection study is retained.
- The SRP emits every eligible above-threshold bin rather than grouping adjacent peaks. The distributor accepts a candidate only while a CFD is ready; otherwise it discards the candidate. Its aggregate ready signal has inverted polarity but is dormant because the corresponding top-level flow-control block is disabled. Result FIFOs reject writes while full without an internal retry or drop counter. On a center-frequency sweep wrap, the saved detector reports and resets its stored peak without comparing the final hypothesis; registered output alignment also requires re-verification.
- The saved threshold test value `1.23×10^-4` is quantized to `8/2^16`. The retained ROC script stops with an execution error and contains mislabeled result fields, so it does not establish a calibrated optimum or a target `P_fa`. The only saved successful MATLAB example uses two signals at 14-dB `E_b/N_0`, one QPSK and one 16-QAM case, both with 0.35 rolloff; it is illustrative rather than statistical validation.

The only synthesis checkpoint is for the FFT alone, targets `xcku5p-sfvb784-1LV-i`, and reports 4,951 LUTs, 7,956 registers, 194.5 BRAM equivalents, and 84 DSPs. Its BRAM count alone exceeds the paper's reported whole-design total of 186.5, proving that it is not the Table I artifact. No complete top-level synthesis, utilization, timing, or power report survives.

## Bibliography corrections

- The thesis title now matches the [official OhioLINK record](https://etd.ohiolink.edu/acprod/odb_etd/r/etd/search/10?p10_accession_num=csu1622636550863441): “A Low-Memory Spectral-Correlation Analyzer for Digital QAM-SRRC Waveforms.”
- The CCAAW and JRFID entries use their official venue/journal names, page ranges, and DOI fields. The [NASA CCAAW manuscript](https://ntrs.nasa.gov/citations/20210016644) also confirms that `β` is a scalar selected from a scenario-specific ROC tradeoff, not `P_fa` itself.
- Koch and Downey's primary technical memorandum and the relevant AMD device/FFT documentation were added.
- The unused blog citation was removed from the revised manuscript rather than retained as a general-purpose technical authority.

## 2023 RWW format audit

The historical [summary guide](https://web.archive.org/web/20220809190759/https://www.radiowirelessweek.org/authors/summary-submission-guide/) specified a three-page review-paper and 4-MB limit. The historical [final-manuscript guide](https://web.archive.org/web/20221208235057/https://www.radiowirelessweek.org/authors/final-manuscript-submission-guide/) allowed four pages and 4 MB and specified U.S. Letter, 0.75-inch top/side margins, 1.125-inch bottom margin, a 0.25-inch gutter, two columns, and at least 10-point body text.

Corrections made:

- Explicit RWW page geometry replaces bare `IEEEtran` defaults.
- The title is bold, single-column graphics use `\columnwidth`, floats prefer the top of a column, table terminology is corrected, and the final-page columns are balanced.
- `orcidlink`/`hyperref` was removed. The historical PDF contained bookmarks, 29 link annotations, and visible colored link boxes; IEEE's [Xplore requirements](https://conferences.ieeeauthorcenter.ieee.org/write-your-paper/meet-ieee-xplore-requirements/) prohibit bookmarks and links.
- PDF title, author, subject, and keyword metadata were added without generating hyperlinks.
- Reference fields and online-source presentation were normalized to IEEE style.

Checks retained from the historical PDF include U.S. Letter size, embedded/subset fonts, searchable text, no encryption, no page numbers, and a file size well below 4 MB.

## Remaining limitations

The six figures are 157–250 effective ppi in the historical PDF and 166–264 ppi in the corrected PDF. IEEE recommends at least 300 ppi for color/grayscale images and 600 ppi for line art; see its [graphics guidance](https://conferences.ieeeauthorcenter.ieee.org/write-your-paper/improve-your-graphics/) and [resolution standards](https://journals.ieeeauthorcenter.ieee.org/create-your-ieee-journal-article/create-graphics-for-your-article/resolution-and-size/). Vector `.vsdx` originals are present, but vector PDF/EPS exports are not. Export those originals before a new formal submission. Figure 1 should also distinguish curves by dash/marker pattern, not color alone, and small labels in the block diagrams should be enlarged.

The 2023 guide requested phone and email for the corresponding author. The email is present; no historical phone number was available, so none was invented. This remains a metadata item to supply if the document is resubmitted.

No HDL, testbench, waveform data, synthesis report, tool version, or scripts are present in the paper repository itself. The separately supplied implementation snapshot recovers important configuration and control details, including a strong derivation of the latency figure, but it is internally inconsistent, not portable or self-checking, and cannot be tied to the build behind Table I. The reported detection statistics, full-design synthesis utilization, timing, power, and end-to-end throughput therefore remain independently unverified.
