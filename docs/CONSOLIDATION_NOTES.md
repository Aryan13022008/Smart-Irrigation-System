# Consolidation notes

## Source archives
- `h_esa.zip`: selected as the main source for the integrated ESP32, dashboard, training, weed-detection, and agricultural dataset code.
- `s_esa.zip`: supplied the dashboard integration shell, a distinct future-dryness model + source data + training scripts, and a live-forecast helper.

## Decisions
- Kept one dashboard (`app.py`), based on the more integrated dashboard from `s_esa`, and added a separate research forecast page.
- Kept the main inference implementation and model artifacts from `h_esa` because its model metadata records the matching estimator and feature definitions.
- Did not mix the two incompatible `random_forest_11feat.pkl` files. The `s_esa` copy's metric report differs substantially and the supplied artifacts do not establish a like-for-like evaluation.
- Retained `s_esa`'s `future_dryness_model.joblib` with its own source data and scripts. It is isolated because its input units differ from the ESP32 raw ADC values.
- Did not include the local SQLite history database, Python bytecode/cache, duplicate `app.py` / `final_dashboard.py`, or the syntactically broken `website2.py`.
- Did not claim crop-disease detection as a working feature because neither archive contained the referenced `disease_detection` module.

## Validation status
This consolidation is a source-level integration. Syntax, import paths, model bundle loading, and dashboard startup should be verified in the target Mac environment with the requirements installed and, separately, with actual ESP32 hardware.
