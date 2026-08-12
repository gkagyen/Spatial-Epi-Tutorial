
<!-- README.md is generated from README.Rmd. Please edit that file -->

# Spatial Epidemiology R Tutorial

<!-- badges: start -->

[![R](https://img.shields.io/badge/R-%3E%3D4.1-blue.svg)](https://cran.r-project.org/)
[![Quarto](https://img.shields.io/badge/Powered%20by-Quarto-75AADB.svg)](https://quarto.org/)
[![License:
MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

<!-- badges: end -->

## A comprehensive, hands-on tutorial for public health researchers - Built entirely in R.

This repository contains the complete source materials for the **Spatial
Epidemiology in R** tutorial, developed for the **Ghana R User
Community** in collaboration with **AfreDAC**. The tutorial teaches
spatial data import, disease mapping, spatial autocorrelation analysis,
geographically weighted regression, and machine learning for spatial
epidemiology — all without leaving the R environment.

------------------------------------------------------------------------

## 📖 Overview

Spatial epidemiology helps us understand *where* diseases occur, *why*
they cluster, and *how* to target interventions. This tutorial provides
a progressive, six-module learning pathway that takes you from raw
spatial data to publication-ready maps and predictive models.

**Key highlight:** The tutorial showcases the
[`mlspatial`](https://cran.r-project.org/package=mlspatial) package
(Azeez & Noel, 2025), which integrates spatial mapping with Random
Forest, XGBoost, and Support Vector Regression in a single,
peer-reviewed workflow.

------------------------------------------------------------------------

## 📂 Repository Structure

``` text
Spatial Epidemiology R Tutorial/
├── Spatial Epidemiology R Tutorial.Rproj   # RStudio project file
├── _quarto.yml                             # Quarto book configuration
├── styles.css                              # Custom styling
├── index.qmd                               # Welcome & how-to-use
├── 01-setup.qmd                            # Module 1: R Environment Setup
├── 02-mapping.qmd                          # Module 2: Disease Mapping
├── 03-autocorrelation.qmd                  # Module 3: Spatial Autocorrelation
├── 04-regression.qmd                       # Module 4: GWR & Regression
├── 05-ml.qmd                               # Module 5: Machine Learning
└── 06-integration.qmd                      # Module 6: Data to Decision
```

------------------------------------------------------------------------

## 🚀 Quick Start

### Prerequisites

- [R](https://cran.r-project.org/) (version ≥ 4.1)
- [RStudio](https://posit.co/download/rstudio-desktop/) (recommended)
- [Quarto](https://quarto.org/docs/get-started/) (for rendering the
  book)

### Installation

1.  **Clone this repository:**

    ``` r
    gert::git_clone(
      url = "https://github.com/ghana-r-user-community/spatial-epi-tutorial.git",
      path = "~/spatial-epi-tutorial"
    )
    ```

2.  **Open the project in RStudio:** Double-click
    `Spatial Epidemiology R Tutorial.Rproj`.

3.  **Install required R packages:**

    ``` r
    pkgs <- c("sf", "terra", "tmap", "ggplot2", "dplyr", "tidyr", "readr",
              "spdep", "GWmodel", "mlspatial", "randomForest", "xgboost",
              "e1071", "caret", "knitr", "rmarkdown")

    install_if_missing <- function(p) {
      if (!require(p, character.only = TRUE, quietly = TRUE)) {
        install.packages(p, repos = "https://cloud.r-project.org/")
        library(p, character.only = TRUE)
      }
    }
    invisible(sapply(pkgs, install_if_missing))
    ```

4.  **Render the tutorial:**

    ``` r
    quarto::quarto_render()
    ```

    Or open any `.qmd` file and press **Ctrl+Shift+K** (Windows/Linux)
    or **Cmd+Shift+K** (Mac) to render a single module.

------------------------------------------------------------------------

## 📚 Tutorial Modules

| Module | Topic | Key Packages |
|----|----|----|
| **1** | R Environment Setup and Spatial Data Import | `sf`, `mlspatial` |
| **2** | Disease Mapping in R | `tmap`, `ggplot2`, `mlspatial` |
| **3** | Spatial Autocorrelation and Cluster Analysis | `spdep`, `mlspatial` |
| **4** | Spatial Regression and Environmental Factor Analysis | `dplyr`,`tidyr`, `GWmodel` |
| **5** | Machine Learning for Spatial Epidemiology | `mlspatial`, `randomForest`, `xgboost`, `e1071` |
| **6** | Integrated Application from Data to Decision | `sf`, R Markdown / Quarto |

------------------------------------------------------------------------

## ✨ Key Features

- **R-Only Workflow:** No QGIS required — everything from data import to
  predictive modelling happens in R.
- **Built-in Datasets:** Uses `mlspatial::africa_shp` and
  `mlspatial::panc_incidence` so you can run code immediately.
- **Cross-Validated Machine Learning:** Honest model evaluation with
  out-of-fold predictions (not training-set overfitting).
- **Reproducible Research:** Quarto book format ensures code, output,
  and narrative are tightly integrated.
- **Ethics & Methodology:** Dedicated section on MAUP, ecological
  fallacy, and privacy protection with spatial health data.

------------------------------------------------------------------------

## 🛠️ Built With

- [Quarto](https://quarto.org/) — Scientific and technical publishing
- [RStudio](https://posit.co/) — IDE for R
- [mlspatial](https://cran.r-project.org/package=mlspatial) — Machine
  learning and mapping for spatial epidemiology

------------------------------------------------------------------------

## 🤝 Contributing

This tutorial is created by George K. Agyen from the **Ghana R User
Community**. Contributions are welcome!

- **Found a bug?** Open an
  [issue](https://github.com/ghana-r-user-community/spatial-epi-tutorial/issues).
- **Have a suggestion?** Start a
  [discussion](https://github.com/ghana-r-user-community/spatial-epi-tutorial/discussions).
- **Want to add a module?** Fork the repo, create a branch, and submit a
  pull request.

Please ensure your code follows the existing style and includes runnable
examples.

------------------------------------------------------------------------

## 📄 License

This tutorial is licensed under the [MIT License](LICENSE). You are free
to use, modify, and distribute it for educational and research purposes.
Please cite the original work when adapting it for your own courses or
publications.

## 💬 Questions?

Reach out via: - GitHub
[Discussions](https://github.com/gkagyen/Spatial-Epi-Tutorial/discussions) -
Ghana R User Community meetups - Email: <your-email@example.com>

------------------------------------------------------------------------
