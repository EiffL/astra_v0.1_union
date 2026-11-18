# Union2.1 flat ΛCDM fit — agent notes

This is a Lightcone project. `astra.yaml` is the source of truth for inputs,
outputs, decisions and universes. It reproduces the SN-only flat ΛCDM Ωm
constraint of Suzuki et al. 2012 (arXiv:1105.3470, Table 7) from the public
Union2.1 distance moduli and covariances in `data/`. Code is in `src/`, with
one script per output plus the shared `src/union21.py`. The report is
`index.md` (MyST).

## Commands

```bash
astra validate                     # the LOCAL astra (0.3.x), never `uvx astra-tools@0.2.18`
lc compute launch --wait           # start the local cluster (idles out after 30 min)
lc run local -- python src/...     # probe in the recipe sandbox; write probes under /tmp
lc materialize local               # build every stale result; commits each one
lc materialize --check             # the gate: exits 0 when everything is current
lc status --json
```

This `lc` (0.5.2.dev) takes a cluster as the first positional argument of
`run` and `materialize`, unlike the lightcone skill's examples.

## Project Notes

- **Goal (2026-11-18):** reproduce Suzuki+2012 Table 7, SNe only, flat
  ΛCDM. The targets are Ωm = 0.277 +0.022/−0.021 (stat) and
  0.295 +0.043/−0.040 (stat+sys), both Δχ² = 1 limits. Universes `baseline`
  (stat+sys) and `stat_only` cover them. The user picked flat ΛCDM only;
  non-flat ΛCDM and wCDM were offered and not taken.
- **Literature pass skipped** at the user's request. The Table 7 numbers
  are hardcoded as literal constants in the `om_posterior_figure` recipe and
  were read from the cached arXiv PDF
  (`astra paper path 10.48550/arXiv.1105.3470`). No prior_insights with
  verified quotes exist yet.
- **Data quirks:**
  - `SCPUnion2.1_covmat_nosys.txt` is exactly diag(σμ²), so the
    `covariance.diagonal` option is excluded as identical to `stat`.
  - The sys covariance contains that diagonal plus the systematic terms.
  - The table's μ were standardized with the stat-only M(h=0.7): the
    stat+sys fit's best offset (+0.0099 mag) equals the header's M gap.
  - z ranges from 0.015 to 1.414, with 580 SNe.
- **Known small gap:** our stat-only errors (≈ ±0.019) are slightly tighter
  than Suzuki's (±0.021–0.022). We take μ as published, with α, β, δ fixed,
  whereas Suzuki refit them jointly with cosmology. Central values agree to
  < 0.001.
- **Validate-on-save hook noise:** the lightcone plugin's hooks call
  `uvx astra-tools@0.2.18`, which rejects the 0.3 schema (`spec_version`,
  `label`, map-style inputs, `universes`). Ignore those reports and trust
  `astra validate`.
