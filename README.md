# MachineGuard: Machine Fault Detection

MachineGuard is a small learning project that uses machine sensor readings to spot observations that look different from normal operation. It uses ideas from linear algebra and a Jupyter notebook to explain how the result is produced.

This is a classroom demonstration. Its health score and maintenance suggestion are project-defined examples, not a certified machine-safety or maintenance system.

## What is in this repository?

- `machine_fault_detection.ipynb` — the analysis, charts, explanations, and interactive machine report.
- `machine_fault_project_dataset_5_parameters_random_ids.csv` — the data used by the notebook. Each row is one machine observation.

The data includes a sample ID, five sensor readings, a fault type, and a severity:

| Column | What it means |
| --- | --- |
| `Sample_ID` | Identifier used to look up an observation |
| `Temperature_C` | Temperature in degrees Celsius |
| `Vibration_RMS_g` | Vibration reading in g |
| `Current_RMS_A` | Electrical current in amperes |
| `Acoustic_RMS_Pa` | Acoustic reading in pascals |
| `RPM` | Rotational speed |
| `Fault_Type` | Dataset label, such as Normal or a fault category |
| `Severity` | Dataset severity label, when present |

## Run it in VS Code on Windows

1. Download or clone this repository. Keep the notebook and CSV in the same folder.
2. Open that folder in VS Code and open `machine_fault_detection.ipynb`.
3. Open VS Code's terminal in the repository folder and create a Python environment:

   ```powershell
   py -m venv .venv
   .\.venv\Scripts\Activate.ps1
   python -m pip install jupyter numpy pandas matplotlib seaborn sympy scipy
   ```

4. In the notebook's top-right kernel picker, select the Python interpreter from `.venv`.
5. Click **Run All**. The final machine checker asks for a `Sample_ID`; enter an ID that exists in the CSV, such as `1`.
6. Save the notebook with **Ctrl+S** to keep the cell outputs.

Run the notebook with its working folder set to this repository. Otherwise, it may not find the CSV file.

## What the notebook does

1. **Builds a data matrix.** It arranges the five sensor readings into a table of observations and features.
2. **Puts sensor readings on comparable scales.** Temperature, vibration, current, acoustic level, and RPM use different units, so the notebook standardizes them before comparing them.
3. **Explores linear algebra concepts.** It demonstrates row reduction and rank, orthogonal bases, projections, least-squares prediction, and eigenvalues and eigenvectors.
4. **Learns a normal operating pattern.** The improved detector uses a repeatable split of the labeled rows: 70% for training, 15% for calibration, and 15% for a held-out test set. The split is made separately within each fault category, using random seed 42.
5. **Builds the normal baseline.** Only training rows labeled `Normal` are used to calculate the sensor averages, scaling values, and principal-component directions.
6. **Sets an alert threshold.** Calibration rows labeled `Normal` set the threshold at the 95th percentile of their anomaly scores.
7. **Checks held-out observations.** The notebook compares test scores with the threshold and shows a table and charts by fault type.
8. **Creates a machine health report.** The report shows an anomaly score, status, health score, dominant sensor, and a project-defined maintenance category.

The anomaly score combines two distances: how unusual an observation is along the selected normal patterns, and how far it falls outside those patterns. A larger score means a greater difference from the learned normal behavior.

The health score is calculated as:

`Health Score = 100 / (1 + Anomaly Score / Threshold)`

It is a simple way to display relative deviation for this project; it is not a measured probability that a machine is healthy.

## Understanding the results

- **Normal** means the score is at or below the calibration threshold.
- **Anomaly** means the score is above that threshold.
- The held-out table compares the detector's result with the dataset's `Fault_Type` labels.
- The dominant sensor is the sensor with the largest squared projection residual. It points to one part of the deviation; it does not prove that sensor caused a fault.
- Results depend on this dataset, the selected two principal components, and the chosen threshold. They should not be treated as guaranteed performance on new machines.

The notebook includes short concept–purpose–outcome notes to help explain the mathematics during a demonstration or viva.
