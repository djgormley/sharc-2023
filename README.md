# SHaRC 2023 paper

This repository preserves the source and archival PDF for:

> D. J. Gormley, “A Hybrid Technique to Increase Throughput of the Streaming Spectrum Sensor,” *2023 IEEE Space Hardware and Radio Conference (SHaRC)*, Las Vegas, NV, USA, pp. 34–36, 2023, doi: [10.1109/SHaRC56958.2023.10046267](https://doi.org/10.1109/SHaRC56958.2023.10046267).

`sharc2023.pdf` is the historical three-page paper. It has not been overwritten.

`output/pdf/sharc2023-fact-checked.pdf` is a September 2026 post-publication author revision. It corrects identifiable mathematical, arithmetic, hardware-part, attribution, bibliography, and production-format issues, incorporates recoverable details from an author-supplied implementation snapshot, and qualifies conclusions that the surviving evidence cannot establish. It is not the IEEE version of record.

See [`ERRATA.md`](ERRATA.md) for the formal author-prepared corrections and [`FACT_CHECK.md`](FACT_CHECK.md) for the full audit trail, primary sources, compliance checks, and remaining evidence gaps.

## Build

From the repository root, run:

```sh
latexmk -pdf -interaction=nonstopmode -halt-on-error -outdir=tmp/build sharc2023.tex
```

The source is configured for the 2023 RWW final-manuscript page geometry: U.S. Letter, 0.75-inch top and side margins, 1.125-inch bottom margin, a 0.25-inch column gutter, and 10-point two-column IEEE conference text.
