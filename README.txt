===============================================================================
PitRossa
Fuel-Corrected Tyre Degradation Modelling and Pit-Stop Decision Support for F1
===============================================================================

Course : AI1215 - Introduction to Machine Learning (Summer 2026)
Team   : Anti-Ferrari Disasterclass
Author : Abdelmonaim Bounite
Topic  : Custom topic (motorsport). Predict Formula 1 tyre degradation from
         public timing/weather data and turn it into a pit-stop decision.
Task   : Supervised regression (target: DegradationDelta) + a decision layer.


-------------------------------------------------------------------------------
1. WHAT THIS PROJECT DOES
-------------------------------------------------------------------------------
Lap times in F1 rise as tyres wear. The raw lap time is dominated by fuel load
and by the circuit, which hide the degradation signal. We:

  1. remove fuel by regressing lap time on fuel mass per race (gamma);
  2. remove circuit/car pace by subtracting each stint's fresh pace, giving the
     target DegradationDelta = FuelCorrectedLapTime - StintFreshPace;
  3. fit and compare models that predict DegradationDelta on held-out circuits;
  4. turn the chosen model into a greedy pit-stop policy, add an uncertainty
     band, and a belief-state POMDP that learns a tyre's degradation rate live.

The model is evaluated on circuits it has never seen (a by-circuit split), so
the reported error measures generalisation, not memorisation.


-------------------------------------------------------------------------------
2. FOLDER LAYOUT
-------------------------------------------------------------------------------
Run the notebook from INSIDE the code/ folder. The notebook uses relative
paths (../data, ../figs, ../cache), so code/ must sit next to data/.

  <project_root>/
  |-- code/
  |     |-- lab.ipynb          <- main notebook (run this)
  |-- data/
  |     |-- all_laps.csv       <- pre-processed dataset (61,185 laps)
  |-- figs/                    <- created automatically when the notebook runs
  |     |-- eda/ degradation_raw/ degradation_corrected/ screening/
  |     |-- data/ model/ decision/
  |-- cache/                   <- FastF1 cache (Phases 1-4 only, auto-created)
  |-- README.txt
  |-- requirements.txt
  |-- report.pdf

data/all_laps.csv already contains the processed output of Phases 1-4, so the
modelling results can be reproduced WITHOUT internet (see path B below).

The code for data loading (Phases 1-4) is included but commented for
convenience as the FastF1 API has rate-limiting constraints.


-------------------------------------------------------------------------------
3. ENVIRONMENT SETUP (tested on conda environment)
-------------------------------------------------------------------------------
Python 3.11 recommended. (3.11.15 is the version used)

conda create -n pitrossa python=3.11 -y && conda activate pitrossa
pip install -r requirements.txt


-------------------------------------------------------------------------------
4. HOW TO REPRODUCE
-------------------------------------------------------------------------------
Open the notebook from the code/ folder so the relative paths resolve:

  cd code
  jupyter notebook lab.ipynb     # then Run All

There are two reproduction paths:

  PATH A - Full pipeline from scratch (needs internet, rate-limiting is inevitable and would take time)
    Phases 1-4 download timing/weather data with FastF1, clean it, build the
    features, screen all 69 races, and regenerate data/all_laps.csv plus the
    figures in figs/eda, figs/degradation_raw, figs/degradation_corrected and
    figs/screening. First run is slow (FastF1 downloads then caches).
    Create the cache folder first:   mkdir -p ../cache ../figs

  PATH B - Modelling + decision results only (NO internet, fast) (Recommended)
    With data/all_laps.csv present, run from "Phase 5: Modelling" to the end.
    This reproduces the model comparison, calibration, backtest and the
    belief-state POMDP, and writes the figures in figs/data, figs/model and
    figs/decision. This is the quickest way to verify the headline numbers.

------------------------------------------------------------------------------
5. DATA PROVENANCE
-------------------------------------------------------------------------------
Source : FastF1 Python API (Oehrly, 2025), https://github.com/theOehrly/FastF1
Seasons: 2022-2025 (the current ground-effect regulation era)
Scope  : dry, green-flag laps only (track status 1); wet running excluded
Fuel   : not published by F1, derived as a linear burn from 109 kg to ~0
Fields : lap time, tyre compound, stint, pit laps, position, track temperature
The notebook (Phases 1-4) is the collection-and-processing script that produces
data/all_laps.csv; that file is also included so results reproduce offline.


-------------------------------------------------------------------------------
6. NOTES
-------------------------------------------------------------------------------
- Use of AI assistance: AI tools were used to assist in the writing
and proofreading of this report, generating the README file, organizing thoughts, providing
conceptual guidance, and assist with the mathematical correctness of the implementation. All
final modelling decisions, code, and analytical findings were exclusively authored and verified by the student.