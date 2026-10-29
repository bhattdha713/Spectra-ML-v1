# Spectra ML v1

**Spectra ML** is a computational tool for estimating molecular UV–Visible absorption maxima (lambda_max (maximum absorption wavelength)) and generating modelled spectra under selected solvent and experimental-condition inputs. The implementation combines cheminformatics, machine-learning predictors, analogue retrieval, empirical spectral rules, optional quantum-chemical calculations, condition-dependent corrections, uncertainty-related scoring, and visualization in a Google Colab notebook.

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.23402688.svg)](https://doi.org/10.5281/zenodo.23402688)

- **Version:** `v13.7.3`
- **Interface:** Google Colab / Jupyter Notebook
- **Primary contact:** [dkbhatt1307@gmail.com](mailto:dkbhatt1307@gmail.com)
- **Secondary contact:** [spectra.ml.v13.7.3@gmail.com](mailto:spectra.ml.v13.7.3@gmail.com)
- **Notebook:** [Open Spectra ML v13.7.3 in Google Colab](https://colab.research.google.com/drive/1Rlp3hXHdpqLTQX5PruvIbz3HFopt8m_F?usp=drive_link)
- **DOI:** 10.5281/zenodo.23402688

## Overview

Spectra ML accepts molecular structures in SDF format and user-selected conditions (solvent, pH, temperature, and concentration). It estimates a primary lambda_max (maximum absorption wavelength), generates a simulated spectrum, and can display model-confidence and applicability-domain information. The notebook also supports feedback collection, retraining, evaluation by selected data groups, and ablation analysis.

The software is intended for computational investigation, method development, and hypothesis generation in molecular spectroscopy. Quantitative performance should be reported with the exact software/data version, evaluation protocol, sample count, and reference-data provenance. Predictions and modelled spectra should be interpreted alongside experimental evidence where scientific conclusions depend on specific compounds or conditions.

## Key capabilities

- **Structure input:** reads molecules from SDF using RDKit.
- **Molecular representation:** builds a fixed-length feature representation with a configured dimension of 1,024.
- **Chromophore analysis:** uses a SMARTS-based chromophore dictionary and structural heuristics.
- **Hybrid prediction:** combines available machine-learning predictors, analogue information, empirical corrections, and condition-related components.
- **Optional quantum chemistry:** can attempt TD-DFT calculations through PySCF.
- **Spectrum generation:** creates a wavelength-dependent modelled curve from predicted bands and applies concentration/path-length scaling and instrument-effect handling.
- **Prediction support indicators:** reports confidence-related and applicability-domain information.
- **Feedback workflow:** records reference lambda_max (maximum absorption wavelength) values, tracks errors, retrains selected components, and supports evaluation and ablation workflows.
- **Visualization:** plots simulated spectra and a feedback-error history.

## Screenshots and example outputs

### Example 1 — Methoxsalen in methanol

<img width="1072" height="525" alt="fig 1- test molecule Methoxsalen" src="https://github.com/user-attachments/assets/c9bc4c30-5246-44c5-9d43-d304a3b51c35" />

The supplied run used methanol, pH 7.0, temperature 300 K, concentration 0.1 M, TD-DFT disabled, and two refinement iterations. The log reports a final predicted lambda_max (maximum absorption wavelength) of approximately **295.25 nm**, with confidence `0.07`. The spectrum figure marks the peak at approximately 296 nm.

This is an example of one run, not a general performance estimate. The low reported confidence should be retained when interpreting the output.

### Example 2 — Orciprenaline in water

<img width="1072" height="525" alt="fig 2- training molecule Orciprenaline" src="https://github.com/user-attachments/assets/0f128c2a-14bb-4871-8f80-fc4605637945" />

The supplied run used water, pH 6.0, temperature 300 K, concentration 0.1 M, TD-DFT disabled, and two refinement iterations. The log reports a final predicted lambda_max (maximum absorption wavelength) of approximately **300.75 nm**, confidence `0.15`, and a **Low** applicability-domain status. Feedback was then entered with an actual lambda_max (maximum absorption wavelength) of 276 nm, giving a logged absolute error of approximately **24.8 nm**.

This example illustrates why the predicted value, confidence/applicability information, and any reference measurement should be considered together.

### Feedback error history

<img width="1072" height="525" alt="feedback vs errors" src="https://github.com/user-attachments/assets/8b8d7292-b21a-4657-b9f4-39fe92516ab2" />

The supplied feedback plot shows absolute prediction errors across feedback iterations. Errors are highly variable, with occasional large excursions; the moving average also fluctuates. This plot is useful for monitoring learning history, but it is not, by itself, a controlled test-set learning curve.

## How to run the notebook

1. Open the [Google Colab notebook](https://colab.research.google.com/drive/1Rlp3hXHdpqLTQX5PruvIbz3HFopt8m_F?usp=drive_link).
2. Start the runtime using **Connect** and run the notebook cells in order.
3. At the model-source prompt, choose one of the following:
   - `1` — attempt to load the shared model from its configured central Google Drive location.
   - `2` — attempt to load saved model/data from your personal Google Drive.
   - `3` — start without loading a saved pre-trained model.
4. Select the solvent by entering its displayed ID code.
5. Enter pH, temperature in kelvin (K), and concentration in molarity (M). The notebook defaults are pH `7.0`, temperature `298.15 K`, and concentration `0.01 M`.
6. Upload an SDF file. Other supported chemical-structure formats can be converted to SDF using a structure editor/converter such as Open Babel; inspect the converted structure before use.
7. Answer `y` or `n` to the TD-DFT prompt. Enabling TD-DFT can increase computation time and may fail for some structures or environments.
8. Enter the number of refinement iterations. Enter `0` to skip iterative refinement; positive integers request repeated prediction/refinement.
9. Review the predicted lambda_max (maximum absorption wavelength), spectrum plot, confidence-related output, applicability-domain result, and any warnings.
10. If prompted, decide whether to collect reference feedback, run detailed evaluation, run an ablation study, and save updated model/data artifacts to Google Drive.

A successful run depends on runtime resources, installed package versions, Google Drive authorization, and access to compatible saved model/data files. Starting fresh does not imply that a model has been trained on an adequate dataset.

## Model-source options

| Choice | Behaviour |
|---|---|
| `1` — Shared model | Loads the shared model and related artifacts from the configured central Drive location, if accessible. |
| `2` — Personal Drive | Loads model/data artifacts from the user's configured Drive location, if available. |
| `3` — Start fresh | Does not load the saved pre-trained model from those locations; subsequent behaviour depends on the available code path and data. |

## Computational workflow

At a high level, the notebook follows this workflow:

```text
Molecular structure (SDF) + solvent/pH/temperature/concentration
                              |
                              v
                  RDKit parsing and validation
                              |
                              v
           Molecular features + chromophore detection
                              |
                              v
        Available prediction components and analogue search
                              |
                              v
       Meta-combination / empirical / condition corrections
                              |
                              v
           Optional TD-DFT and conformational information
                              |
                              v
              Refinement and spectrum construction
                              |
                              v
       Predicted lambda_max + spectrum + support indicators
                              |
                              v
           Optional reference feedback and retraining
                              |
                              v
              Evaluation / subgroup metrics / ablation
```

This diagram describes the intended high-level architecture. The active path can vary according to loaded artifacts, installed dependencies, user selections, and whether optional calculations succeed.

### Main code components

The notebook is divided into seven main sections:

1. **Dependency installation and imports** — installs/imports numerical, cheminformatics, machine-learning, plotting, and quantum-chemistry libraries.
2. **Configuration** — defines wavelength limits and step size, feature dimensions, neural-network settings, defaults, and training/evaluation settings.
3. **Chromophore detector and solvent database** — contains SMARTS patterns and solvent-related descriptors/parameters.
4. **Core prediction classes** — includes molecular feature extraction, predictors and correction components, spectrum generation, confidence scoring, and visualization.
5. **Empirical rule corrector** — implements selected classical UV–Visible rules, including Woodward–Fieser, Fieser–Kuhn, and Scott-type rules.
6. **Helper functions** — includes random-seed setup, scaffold-aware splitting, artifact loading/saving, and provenance/manifest checks.
7. **Main execution** — handles interactive inputs, prediction, plotting, feedback, optional evaluation, ablation, and saving.

## Methods and equations

### Wavelength grid

The configuration defines a wavelength range from 200 to 800 nm with a 0.5 nm step:

```text
WL_MIN = 200 nm
WL_MAX = 800 nm
WL_STEP = 0.5 nm
```

### Gaussian band construction

The spectrum generator adds Gaussian-shaped bands to construct a simulated spectrum:

```text
A(lambda) = sum_i [ I_i * exp( -((lambda - lambda_i)^2) / (2 * sigma_i^2) ) ]
```

Here, `lambda_i` is the centre of band i, `I_i` is its intensity scale, and `sigma_i` controls band width. This is a spectrum-construction model; the equation alone does not establish that the resulting curve is experimentally calibrated.

### Concentration and path-length scaling

The code applies a Beer–Lambert-style scaling operation:

```text
A_scaled(lambda) = A_model(lambda) * concentration * path_length
```

The configured path length is `1.0` in the notebook. Since the modelled band intensity is not necessarily a calibrated molar absorption coefficient, the scaled values should be interpreted as model outputs unless an experimental calibration establishes their physical scale.

### Empirical spectral rules

The empirical corrector implements selected rules for structural classes, including:

- **Woodward–Fieser rules** for conjugated dienes and selected α,β-unsaturated carbonyl systems.
- **Fieser–Kuhn rule** for suitable extended conjugated polyenes.
- **Scott-type rules** for selected aromatic carbonyl systems.

These rules provide structure-dependent estimates under their respective assumptions. Their applicability depends on the molecule matching the rule's chemical scope.

### Machine learning and hybrid components

The source code includes or configures multiple complementary components. The supplied execution logs show components such as:

- A primary neural-network predictor (`UVVisNN`).
- A deep residual CNN (`DR-CNN`).
- An equivariant graph-neural component (`e3nn`).
- A Hückel-based predictor.
- Analogue retrieval and similarity-based information.
- A meta-combiner and residual correction.
- Solvent-related learning and Kamlet–Taft-related modelling.
- Conformational correction.
- Applicability-domain and outlier/anomaly components.
- Chromophore-based empirical correction and chemistry-memory information.

Not every component necessarily contributes to every run. The final estimate depends on the loaded artifacts, available dependencies, data, and active execution path. The notebook should therefore be described as a **hybrid prediction workflow**, not as a single universally active model.

### Feature representation and reproducibility

The configuration specifies a feature dimension of `1024`, neural-network hidden layers `[512, 256, 128, 64]`, dropout `0.3`, and random seed `42`. The source also uses RDKit and includes DeepChem-related feature extraction where available. These are implementation settings, not evidence on their own of predictive performance.

The scaffold-splitting helper attempts to group molecules using Bemis–Murcko scaffolds, with a random-split fallback when there are too few scaffold groups. The configuration sets nominal test and validation proportions of 15% each. The effective split must be verified from the saved manifest and actual run log.

## Feedback, training, and evaluation

After predictions, users may provide a reference lambda_max (maximum absorption wavelength). The feedback workflow records the prediction, reference value, error, conditions, and data role (train, validation, or test), then may retrain selected components. The code includes a retraining threshold and rebuilds selected correction/model components after feedback.

**Use measured/reference values for feedback whenever possible.** Do not treat the model's own predictions as independent experimental ground truth. Preserve test-set separation: feedback from test molecules must not be used to train a model that is then evaluated on those same molecules as if the test set were still untouched.

The notebook can report:

| Metric | Meaning |
|---|---|
| MAE (nm) | Mean absolute difference between predicted and reference lambda_max (maximum absorption wavelength). |
| RMSE (nm) | Root mean squared error; more sensitive to large errors. |
| R-squared | Coefficient of determination; can be negative on evaluation data. |
| SEP | Standard error of prediction as implemented by the notebook. |
| RPD | Ratio of performance to deviation. |
| CCC | Concordance correlation coefficient. |
| ME | Mean error/bias; interpret using the notebook's sign convention. |

Optional subgroup analyses report metrics by solvent, wavelength region, and chemical class. The ablation workflow compares selected component combinations to examine their effect on the chosen evaluation records.

### Important note about the supplied evaluation logs

The attached logs show a saved feedback database with roughly 762–764 records, around 595–596 training records, 81 validation records, and 86–87 records reported for the locked-test benchmark, depending on the run/artifact snapshot. The logs also show a locked-test MAE around 36.6–36.9 nm and RMSE around 48.6–48.7 nm. These values are reproduced here as **reported run outputs, not as a verified final benchmark claim**.

## Interpretation of confidence and applicability

The notebook produces confidence-related scores and applicability-domain information using signals such as similarity, outlier/applicability checks, ensemble variation, and optional quantum-related information. These values are implementation-specific indicators of prediction support. Unless calibration has been independently demonstrated, they should not be interpreted as probabilities that a prediction is correct or as formal prediction intervals.

A low applicability status, unfamiliar structure, weak analogue match, large disagreement between prediction components, or missing optional calculation should prompt closer review of the result.

## Current scope and limitations

- Prediction quality depends on the amount, quality, diversity, and provenance of the reference data.
- Spectral behaviour can depend on solvent, pH, temperature, concentration, ionization, tautomerism, aggregation, and experimental protocol. These effects may not be fully represented for every compound.
- Structural heuristics and empirical rules have defined applicability ranges and can fail on unusual or complex molecules.
- The generated spectrum is modelled; its intensity scale should not be assumed to be calibrated absorbance or molar absorptivity without supporting calibration.
- TD-DFT is optional, method-dependent, computationally demanding, and may fail for some structures or runtime environments.
- Applicability and confidence outputs should be interpreted according to their implementation and validation.
- Results can depend on saved model artifacts, package versions, Drive permissions, and runtime resources.

These considerations are part of documenting the scope of the software and supporting reproducible use. Performance claims should be based on a documented evaluation protocol and reference measurements appropriate to the intended application.

## Generative-AI disclosure

The core project idea and its inspiration are original to the project author. ChatGPT and DeepSeek were used as assistance tools for debugging, improving understanding of selected code, and reviewing the README wording. They were not the origin of the project's central idea. The author is responsible for the software, scientific claims, verification, and final published content.

## Citation and release

- **Software:** Spectra ML
- **Version:** v13.7.3
- **DOI:** 10.5281/zenodo.23402688
- **DOI url:** [10.5281/zenodo.23402688](https://zenodo.org/records/23402688)
- **GitHub url:** [Spectra ML](https://github.com/bhattdha713/Spectra-AI)
- **Contributors:** Dhairya Bhatt, Avani Doshi, Mishti Tekwani
- **Notebook:** [Google Colab](https://colab.research.google.com/drive/1Rlp3hXHdpqLTQX5PruvIbz3HFopt8m_F?usp=drive_link)
