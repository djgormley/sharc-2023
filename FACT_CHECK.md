# Fact-check and compliance audit

Audit date: 2026-09-02
Repository revision reviewed: `97f4559331b3c573510396e953bf24704efac086`

## Outcome

The repository's historical paper was published at the 2023 IEEE Space Hardware and Radio Conference (SHaRC), pp. 34–36, under DOI [10.1109/SHaRC56958.2023.10046267](https://doi.org/10.1109/SHaRC56958.2023.10046267). The original PDF is preserved unchanged. The revised source and `output/pdf/sharc2023-fact-checked.pdf` correct claims that can be resolved from the archived record and explicitly label results that cannot be independently reproduced.

This is an author-side audit, not an erratum accepted by IEEE. IEEE's [post-publication policy](https://conferences.ieeeauthorcenter.ieee.org/author-ethics/guidelines-and-policies/post-publication-policies/) governs any correction to the version of record.

## Material factual corrections

| Priority | Original claim | Finding | Disposition |
|---|---|---|---|
| Critical | Fourier kernels used `exp(-j2πkn)` and `exp(-j2πvn)` without defining normalized frequencies. | The expression is ambiguous and is wrong if `k` and `v` are integer bins or physical frequencies. | Rewritten with physical frequencies `f_k`, `α_v`, and sample rate `f_s`; the SRP DFT is written with integer bin `m/N`. |
| Critical | Reducing 11 serial symbol-rate evaluations to two gives a factor-of-nine throughput increase. | Nine is the number eliminated. Ignoring preprocessor cost, the upper-bound work ratio is `11/2 = 5.5`; actual speedup is smaller. | Corrected and labeled an illustrative upper bound, not a measurement. |
| Critical | The target device was “Xilinx xc7k7-t.” | That is not an identifiable Xilinx part. The table capacities exactly match the Virtex-7 XC7VX485T in [AMD DS180](https://docs.amd.com/v/u/en-US/ds180_7Series_Overview). | Corrected to XC7VX485T; unknown package and speed grade are disclosed. |
| Critical | `β` was the probability of false alarm. | The cited predecessor defines `β` as a threshold scale factor. `P_fa` is an operating characteristic induced by the threshold and test conditions. | Corrected throughout. Separate SRP/CFD calibration and family-wise false-alarm measurement are required. |
| High | The FFT preprocessor was presented as a new component without primary attribution. | The underlying instantaneous-power FFT/CAF preprocessor is described by Koch and Downey in [NASA/TM–2019-220226](https://ntrs.nasa.gov/citations/20190027051), and the 2021 thesis recommends it. | Primary attribution added; the paper now claims integration/architecture rather than invention of the method. |
| High | An FFT must store `N` values in BRAM before producing a result. | Storage and latency depend on core architecture, scaling, and output order; AMD documents pipelined-streaming and burst configurations using BRAM and/or distributed memory. | Replaced with an `O(N)` state statement and configuration caveat; see [AMD PG109](https://docs.amd.com/v/u/en-US/pg109-xfft). |
| High | More CFDs reduce the precision required for `β`. | Parallel CFDs change service capacity, not the detector's statistical operating point. | Removed; the corrected text discusses bottlenecks and candidate drops. |
| High | Two taps “force periodogram convergence,” and the filter bank “dramatically” improves accuracy. | A two-tap smoother cannot establish convergence generally, and the repository has no before/after accuracy data or coefficients. | Restricted to the prior reported simulation observation; improvement is marked unquantified. |
| High | The design increased throughput, improved reliability, and was suitable for edge devices. | The paper provides one utilization point and one unexplained latency, but no serial baseline, measured throughput, post-SRP `P_d/P_fa`, timing closure, power, or drop-rate result. | Title, abstract, results, and conclusion now describe implementation fit and required validation rather than claiming demonstrated system improvement. |

The four resource percentages were recomputed and are correct: 7.40% LUTs, 2.54% flip-flops, 18.11% 36-Kb BRAM equivalents, and 10.14% DSP48E1 slices. Resource names were aligned with DS180.

## Algorithmic caveats added

- The SRP excludes the DC bin. With `q[n] = |X[n]|²`, the DC statistic equals mean input power and will trivially exceed the power-scaled threshold whenever `β < 1`.
- Because `q[n]` is real, negative FFT bins duplicate positive bins by conjugate symmetry; only unique positive cycle-frequency bins need be tested.
- Off-grid cycle frequencies leak into adjacent bins. Candidate grouping, search-grid multiplicity, FFT scaling, and filter equivalent noise bandwidth affect the false-alarm rate.
- A candidate is discarded whenever every CFD is busy. Without a candidate-arrival/drop-rate experiment, the scheduling change does not establish improved reliability.
- The reported 34.36-s first-result latency is not reproducible without sample rate, FPGA clock, grid dimensions, FFT settings, cycle counts, filter transients, and stall/backpressure behavior.
- One two-CFD synthesis point cannot establish resource scaling with CFD count.

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

No HDL, testbench, waveform data, synthesis report, tool version, or scripts are present in this repository. Therefore the architecture and prose can be checked, but the numerical latency, functional behavior, detection statistics, and synthesis utilization cannot be independently regenerated from the repository alone.
