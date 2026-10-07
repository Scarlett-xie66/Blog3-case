# When Young Children Are at Home, Who Is at Work?

**Author:** Scarlett Xie  
**Article date:** September 28, 2026  
**Updated:** October 6, 2026

This project examines employment among US adults aged 25–54 living with young children. It compares women and men separately, distinguishes labor-force participation from unemployment, and evaluates how the observed employment gaps change after accounting for age and education.

The English article follows a newspaper-report structure: an opening chart, household motivation and highlighted questions, finding-led analysis, practical interpretation, conclusions, Takeaway, numbered Footnotes and Reproducibility. Analysis code is hidden in the rendered article. Each figure has a separate title, short explanatory deck, HTML legend, source and expandable Chart notes.

## Research questions and scope

1. How does employment differ for women and men living with a young child, relative to their counterparts with no own minor child at home?
2. How much of the comparison reflects labor-force participation rather than unemployment, and what pattern remains after age and education adjustment?

The analysis uses the **2025 IPUMS CPS ASEC**, sample `cps2025_03s`. This is a single cross-section, not a 2024–2026 time series. Its findings describe associations; they do not identify the causal effect of having a child or of providing childcare.

## Project structure

Generated estimates, tables and figures appear after a successful run. The tree shows the main interfaces rather than every auxiliary file.

```text
index.qmd
README.md
Blog3.Rproj
EDITORIAL_WINDOW.md
STYLE_GUIDE.md
code/
├── install_packages.R             # install analysis dependencies
├── 01_clean_analyze.R              # acquire/read CPS and estimate results
├── 02_make_figures.R               # restore aggregates and generate figures
└── blog3_analysis.R                # complete extraction of QMD R chunks
data/
├── raw/
│   └── README.md                   # private-input placement instructions
├── processed/
│   ├── weighted_rates.csv          # generated in microdata mode
│   └── adjusted_gaps.csv           # generated in microdata mode
└── reference_only/
    ├── weighted_rates.csv          # earlier aggregates for explicit preview
    └── adjusted_gaps.csv
result/
├── figures/
│   ├── figure1_employment.png
│   ├── figure2_labor_force.png
│   └── figure3_adjusted_gaps.png
├── tables/
│   ├── weighted_rates.csv
│   ├── adjusted_gaps.csv
│   ├── model_comparison.csv
│   ├── model_contrasts_display.csv
│   ├── employment_decomposition.csv
│   ├── model_coefficients.csv       # microdata mode
│   ├── model_summary.txt            # microdata mode
│   ├── model_diagnostics.csv        # microdata mode
│   ├── sample_audit.csv             # microdata mode
│   ├── analysis_objects.rds         # aggregate-only plotting objects
│   ├── data_coverage.csv
│   ├── execution_audit.csv
│   ├── session_info_report.txt
│   └── run_status.txt
├── review_for_codex/
└── review_for_codex.zip             # created when the zip package is available
index_reference_preview.html        # earlier-aggregate preview, not a fresh fit
```

Licensed person records and credentials are kept outside the published project. `data/raw/` documents their placement; it is not the default location of the private extract.

## Requirements and first-time setup

Use R, RStudio and Quarto. The analysis depends on `ipumsr`, `dplyr`, `srvyr`, `survey`, `ggplot2`, `readr` and `knitr`; the project installation script also handles its supporting dependencies.

Open `Blog3.Rproj` and run this once in the **RStudio Console**:

```r
source("code/install_packages.R")
```

The first API request requires an IPUMS account registered for **CPS** and a valid key associated with that account. Registration for another IPUMS collection does not replace CPS registration.

### Save the API key privately

Check whether your existing key is available without printing it:

```r
nzchar(Sys.getenv("IPUMS_API_KEY"))
```

If this returns `TRUE`, no additional key setup is needed. If it returns `FALSE`, open your user environment file:

```r
file.edit(path.expand("~/.Renviron"))
```

Add the following line, replacing the placeholder with your own key:

```text
IPUMS_API_KEY=YOUR_ACTUAL_KEY
```

Save the file, select **Session → Restart R**, and repeat the presence check. Do not put the key in `index.qmd`, scripts, README or GitHub. A key saved in the user `.Renviron` can be loaded by a new R session used for rendering.

### Use an existing extract

Keep the IPUMS XML dictionary and its matching `.csv.gz` or `.dat.gz` file together in a private folder. The extract must contain the 2025 ASEC sample, the required variables and all 160 person replicate weights.

To select its dictionary in the current R session:

```r
Sys.setenv(BLOG3_DDI_PATH = "C:/your/private/folder/cps_00001.xml")
```

This is an example path, not a value to copy unchanged. For the same selection to apply to a separate Render process, save `BLOG3_DDI_PATH` in the user `.Renviron` and restart R.

If no complete extract is selected or found, the script requests one through the API. Its default private cache is `~/.ipums-cps-blog3-cache`; `BLOG3_CACHE_DIR` can override that location. Multiple complete cached extracts require an explicit dictionary selection. The extract request is cached; a processing timeout should be retried using that cached request rather than creating duplicate submissions.

## Reproduce in the standalone project

Open `Blog3.Rproj`. In the **RStudio Console**, run:

```r
Sys.setenv(BLOG3_ANALYSIS_MODE = "microdata")
source("code/01_clean_analyze.R")
source("code/02_make_figures.R")
```

The first script acquires or reads the extract, selects the sample, estimates group rates and fits the model. It saves aggregate analysis objects. The second reads those objects, produces the three figures, writes execution information and assembles the review package. The scripts can be sourced in separate R sessions.

Alternatively, run the complete extracted script from the standalone project root:

```r
Sys.setenv(BLOG3_ANALYSIS_MODE = "microdata")
source("code/blog3_analysis.R")
```

Choose either the staged scripts or the complete script; running both is unnecessary. Open `index.qmd` and click **Render** to rebuild the article. Rendering executes the analysis again, normally reusing a complete cached extract. The QMD can also execute the complete workflow without first running the separate scripts.

Check `result/tables/run_status.txt` for the current run's `COMPLETE` marker and `execution_audit.csv` for `analysis_mode = microdata` and `microdata_model_refitted = TRUE`. A successful render alone does not establish which input mode was used.

## Reproduce within the website

Retain the entire post folder at `blog/posts/post3/`. Open the website's RStudio project, whose root contains `_quarto.yml`.

In the **RStudio Console**, run:

```r
source("blog/posts/post3/code/install_packages.R")  # first-time setup
Sys.setenv(BLOG3_ANALYSIS_MODE = "microdata")
source("blog/posts/post3/code/01_clean_analyze.R")
source("blog/posts/post3/code/02_make_figures.R")
```

The staged scripts locate their post directory from their own source paths, so their outputs remain under `blog/posts/post3/`.

In the **Terminal**, from the website root, rebuild the website and its listing pages:

```bash
quarto render
```

The output directory follows the website's `_quarto.yml`. When `output-dir: docs` is configured, the article is generated at `docs/blog/posts/post3/index.html`. Keep the generated PNG resources with the published website.

To start a local website preview:

```bash
quarto preview --port 4444
```

Use the address printed in the Terminal. When port 4444 is selected, the article is at `http://localhost:4444/blog/posts/post3/` and the list is at `http://localhost:4444/blog/`. If the browser does not open automatically, paste the address manually. Keep the preview process running; `Watching files for changes` is normal. Press **Ctrl+C** to stop it.

Generating HTML does not itself publish the website to GitHub Pages. Publish the rebuilt output using the website's existing deployment configuration.

## Listing thumbnail and article metadata

The latest QMD explicitly specifies its list image, description and PNG resources:

```yaml
image: "result/figures/figure1_employment.png"
description: "How employment differs for women and men living with young children, and what participation, age and education reveal about the gap."
resources:
  - "result/figures/*.png"
```

The `image` path is relative to the post's `index.qmd`, not to `blog/index.qmd`. Figure 1 must exist before the completed website is published. `resources` declares that the PNG files should accompany the website, including when the article itself embeds its images. The explicit description prevents a figure deck from being used as the automatically extracted summary.

After changing these fields, render the whole website so the parent listing is rebuilt. For a `docs` output directory, check that this file exists:

```text
docs/blog/posts/post3/result/figures/figure1_employment.png
```

If it exists locally but the online thumbnail is broken, verify that this file and the rebuilt list page were included in the published output. See the official [Quarto listing documentation](https://quarto.org/docs/websites/website-listings.html) and [site-resource documentation](https://quarto.org/docs/websites/website-tools.html#site-resources).

## Indicators and estimation

The extract requests `YEAR`, `AGE`, `SEX`, `YNGCH`, `EMPSTAT`, `EDUC`, `ASECWT` and `REPWTP`. The analytic sample contains adults aged 25–54 with valid analysis variables and positive person weights. The sample audit distinguishes downloaded person records, prime-age records and final analytic records.

Child groups use the youngest own child living at home: 0–5, 6–12, 13–17, or no own minor child. “No minor child” includes adults whose own children are absent or aged 18 or older; it does not mean never a parent. `SEX` records sex, not gender identity.

- **Employment:** employed adults divided by all adults in the group.
- **Participation:** employed plus unemployed adults divided by all adults.
- **Unemployment:** unemployed adults divided by labor-force participants.

Figure 2 uses all adults as the denominator for every segment, so its unemployed share is not the unemployment rate.

The weighted linear probability model is:

```r
employed ~ sex * child_group + age_band + college
```

Its reference categories are men, no minor child, ages 25–34 and below a bachelor's degree. Age bands are 25–34, 35–44 and 45–54. Person weights are `ASECWT`; uncertainty uses all 160 `REPWTP` replicates with successive-difference replication and `mse = TRUE`. Adjusted contrasts use t(159) intervals and account for covariance between main and interaction coefficients.

The women's employment-gap decomposition separates participation and unemployment components through an accounting identity. These components do not identify causes. Linear probability models can produce fitted values outside zero and one; inspect the exported diagnostics alongside the results.

## Figures and model table

| Output | Comparison |
|---|---|
| `figure1_employment.png` | Weighted employment rates by child group, separately for women and men |
| `figure2_labor_force.png` | Employment, unemployment and nonparticipation shares, totaling 100% |
| `figure3_adjusted_gaps.png` | Observed versus age/education-adjusted young-child employment gaps |
| `model_comparison.csv` | Numerical observed gaps, adjusted gaps and adjusted confidence bounds |
| `model_contrasts_display.csv` | Display table with observed gaps, adjusted gaps and adjusted 95% intervals |

Figure 3 uses paired horizontal bars, separate panels for men and women, a common signed percentage-point scale and a zero line. Positive values indicate higher employment in the young-child group; negative values indicate lower employment. Adjusted confidence intervals appear in the model table rather than crowding the chart. The women-minus-men row compares the two within-sex associations; it is not the overall female-minus-male employment gap.

## Execution modes and publication review

`microdata` is the default mode and fits the model from person records. To inspect the draft using earlier aggregate files, explicitly select reference mode in an R session:

```r
Sys.setenv(BLOG3_ANALYSIS_MODE = "reference")
```

Reference mode does not fit a new model or validate sample selection, credentials or current uncertainty calculations. The supplied `index_reference_preview.html` uses earlier aggregates and was produced through equivalent R chunks with knitr/Pandoc; it is not evidence of a fresh microdata fit or a native Quarto website build.

Before a final Render, confirm that a persistent `BLOG3_ANALYSIS_MODE=reference` setting is not present in the user or project environment file. Console environment changes may not reach a separate Render process. Default mode is microdata when no mode is supplied.

After the actual run, review the estimates, intervals, model diagnostics, figure labels, economic interpretations, conclusions and recommendations together. Inline numbers update automatically, but interpretation still requires review. `COMPLETE` means the computation finished; it does not certify the final article's conclusions.

For a subsequent editorial revision, provide:

- `result/review_for_codex.zip`, or a manually compressed `result/review_for_codex/` folder;
- the locally rendered article HTML;
- `index.qmd` if you edited it.

The review package contains aggregate tables, figures and execution information. Do not add raw person records, API keys or environment files. `EDITORIAL_WINDOW.md` records the remaining review tasks.

## Repository link

Set the QMD's `params: repository-url` to the actual repository containing this project, then render again:

```yaml
params:
  repository-url: "YOUR_ACTUAL_REPOSITORY_URL"
```

Leave it empty until the repository exists. The article then states that the code accompanies the project instead of displaying an invented link.

## Common problems

| Symptom | Action |
|---|---|
| IPUMS 401 or invalid registration | Verify that the key's account has active CPS registration and restart R after updating the private environment file |
| Several complete cached extracts | Select the intended XML using `BLOG3_DDI_PATH` |
| Extract still processing after timeout | Rerun using the cached request; allow processing to finish |
| Missing `analysis_objects.rds` | Run `01_clean_analyze.R` before the figure script |
| HTML generated but no browser opens | Use `quarto preview` or open the generated HTML; generation and preview are distinct |
| Broken thumbnail on the blog list | Check the explicit image path, generated PNG, resource copy and full-site rebuild |
| Results unexpectedly use earlier aggregates | Check the execution audit and remove persistent reference-mode settings before the final run |

Auxiliary editorial requirements and the example-led style standard are documented in `blog_workflow.md`, `STYLE_GUIDE.md` and `requirements_checklist.md`.
