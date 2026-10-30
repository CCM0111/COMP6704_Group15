# Release validation

Validated on 30 October 2026 in a fresh Python 3.12 environment with `reproduce/requirements.lock`.

- Source snapshot hash verification passed.
- All 224 saved models passed independent recomputation.
- 192 original runs and 32 recorded supplement runs were integrated with 7,721 unique checkpoints.
- Analysis notes and 12 PNG/SVG figure pairs regenerated successfully.
- A fresh 32-run E01/E02 comparison completed; all runs converged.
- The fresh-run integration also passed all 224 model checks.
- Release outputs were then restored to the original report supplement.

New runtime measurements are excluded from the report result tables. The recorded medians remain 0.912 ms for CD on E01 and 2.663 ms for ADMM on E02.
