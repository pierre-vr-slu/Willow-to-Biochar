# Willow biochar on Swedish arable land

Code and input data for

> Van Rysselberge, P., Ericsson, N., Karlsson, J., Sundberg, C., Hansson, P.-A. Climate impact of
> short-rotation coppice willow converted to biochar for soil application in Sweden: a spatially
> resolved, time-explicit assessment.

The notebook `willow_biochar_lca.ipynb` reproduces the results of the paper: soil organic carbon
under willow (ICBM), a yearly life cycle inventory for each of the 89 Swedish harvest zones,
GWP100 and temperature-response results, four scenarios, and the Monte Carlo and Sobol analysis
for the reference zone.

## Contents

```
willow_biochar_lca.ipynb   the analysis, run top to bottom
environment.yml            Python environment
data/                      input data (below)
reference_results/         main tables and figures from our run, for comparison
outputs/                   created when the notebook runs
```

| File in `data/` | Content | Source |
|---|---|---|
| `yield_potential.tif` | Willow yield, potential productivity (t DM ha⁻¹ yr⁻¹), base case | Mola-Yudego (2010) |
| `yield_top_quartile.tif` | Willow yield projected for the top 25 % of commercial growers, low-yield scenario | Mola-Yudego (2010) |
| `harvest_zones_sweref99.*` | Swedish harvest zones (skördeområden), SWEREF 99 TM | Swedish Board of Agriculture |
| `spinup_inputs.xlsx` | Zone-mean above- (Ia) and below-ground (Ib) carbon inputs of the previous annual cropping, t C ha⁻¹ yr⁻¹ | Crop statistics and allocation factors, see Supplementary Material S4.4 |
| `model_grid.tif` | Empty raster defining the soil carbon grid (1.34 km, ETRS89 LAEA) | |
| `inventory_template.xlsx` | Willow cultivation inventory per hectare over 100 years, linked to ecoinvent by activity code | After Hammar et al. (2014) and Ericsson et al. (2013) |
| `biochar_decay_fits_Azzi2024.xlsx` | Fitted biochar decay models | Azzi et al. (2024), Geoderma 441, 116761 |

## Requirements

- Python 3.11 with the packages in `environment.yml` (Brightway 2.4: bw2data 3.6, bw2calc 1.8, bw2io 0.8).
- ecoinvent 3.9.1, cut-off system model, in ecoSpold2 format. ecoinvent is licensed and not included.
  The inventory template refers to ecoinvent activities by code in a database named `cutoff391`,
  so keep that name.

```
conda env create -f environment.yml
conda activate willow-biochar
jupyter lab
```

## Running

1. In the first code cell, set `ECOINVENT_DIR` to the `datasets` folder of ecoinvent 3.9.1 cut-off.
   The project (`PROJECT_NAME`, default `willow_biochar`) is created on the first run and ecoinvent
   is imported into it; later runs reuse it.
2. Run all cells. Keep the notebook in this folder: paths are relative to it, and section 10 reads
   the notebook file to rebuild the other scenarios.

A full run takes about 15–20 minutes on a laptop once ecoinvent is imported and the Monte Carlo
background draws exist. The first run also imports ecoinvent and computes the 500 background draws of
section 12.2 (cached in `outputs/`), which together take roughly one to two hours.

The notebook stops with an assertion if one of its consistency checks fails: the spin-up against the
analytical steady state, the linear Monte Carlo model against Brightway, the ICBM and decay
sub-models against the database, and the temperature contributions against the all-zone results.

## Outputs

Tables are written to `outputs/` and figures to `outputs/figures/`:

| Figure | File |
|---|---|
| 4, decay curves | `fig04_decay_curves.png` |
| 5, SOC across zones | `fig05_soc_trajectory.png` |
| 6, SOC change map | `fig06_soc_change_map.png` |
| 7, temperature response across zones | `fig07_temperature_response.png` |
| 8, temperature response by component | `fig08_temperature_contributions.png` |
| 9, GWP100 contributions over one plantation | `fig09_gwp100_contributions.png` |
| 10, GWP100 map | `fig10_gwp100_map.png` |
| 11, scenarios | `fig11_scenarios.png` |
| 12, Monte Carlo | `fig12_monte_carlo.png` |
| 13, Sobol indices | `fig13_sobol.png` |

Figures 1–3 are diagrams and are not produced by the code.

Main tables: `gwp100_zones_<scenario>.csv` (GWP100 per zone with and without heat substitution,
kg CO₂-eq ha⁻¹ over 100 years), `scenarios_zone_1821.csv`, `temperature_<scenario>_*.csv`,
`contribution_*.csv`, `mc_interval_*.csv` and `mc_sobol_*.csv`. `reference_results/` holds the same
tables from our run; small differences can come from the solver, but the values reported in the paper
should agree to the decimals given.

## Model notes

- Years are end-of-year steps from 0 to 100. Year 0 has no flows; yearly flows are spread over years
  1–100. Plantations last 25 years, with harvests every third year and the stools removed in year 25.
- Willow carbon inputs to ICBM follow Hammar et al. (2014, Table 2). The stem growth behind them equals
  the biomass harvested in the inventory, so soil carbon and biochar production describe the same stand.
- Zone values are means over the pixels with a positive yield, the same pixels used by ICBM.
- Biochar decay is the mean of the Azzi et al. (2024) strategy S2 fits inside the scenario's BC₁₀₀ band
  (85–95 % in the base case). Biochar applied in a harvest year decays from the next year.
- Exported heat displaces Swedish district heating (55.3 g CO₂-eq kWh⁻¹) or, in the fossil-heat
  scenario, natural-gas heat (199 g). Results are reported with and without this credit.
- The reference zone, 1821, is the zone whose result without heat substitution equals the median
  across zones.

## License

Copyright © 2026 P. Van Rysselberge, N. Ericsson, J. Karlsson, C. Sundberg, P.-A. Hansson.

The code, documentation, inventory template, derived input tables and reference results in this
repository are licensed under the
[Creative Commons Attribution-ShareAlike 4.0 International License (CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/).
See [`LICENSE`](LICENSE) for the full text. You may share and adapt the material for any purpose,
provided you give appropriate credit (cite the paper above), indicate if changes were made, and
distribute your contributions under the same license.

Input data from third parties keep their original terms and are included here for reproducibility;
credit their sources as listed in the `data/` table above:

- `yield_potential.tif`, `yield_top_quartile.tif`: after Mola-Yudego (2010).
- `harvest_zones_sweref99.*`: Swedish Board of Agriculture (Jordbruksverket).
- `biochar_decay_fits_Azzi2024.xlsx`: Azzi et al. (2024).

ecoinvent is not included and is not covered by this license.

## References

Azzi, E.S., Li, H., Cederlund, H., Karltun, E., Sundberg, C., 2024. Modelling biochar long-term carbon
storage in soil with harmonized analysis of decomposition data. Geoderma 441, 116761.

Ericsson, N., Porsö, C., Ahlgren, S., Nordberg, Å., Sundberg, C., Hansson, P.-A., 2013. Time-dependent
climate impact of a bioenergy system – methodology development and application to Swedish conditions.
GCB Bioenergy 5, 580–590.

Hammar, T., Ericsson, N., Sundberg, C., Hansson, P.-A., 2014. Climate impact of willow grown for bioenergy
in Sweden. BioEnergy Research 7, 1529–1540.

Leppäkoski, L., Marttila, M., Uusitalo, V., Levänen, J., Halonen, V., Mikkilä, M., 2021. Assessing the
carbon footprint of biochar from willow grown on marginal lands in Finland. Sustainability 13, 10097.

Mola-Yudego, B., 2010. Regional potential yields of short rotation willow plantations on agricultural
land in Northern Europe. Silva Fennica 44, 63–76.
