# 🚀 Rotating Machinery Anomaly Detection & Order Analysis

This repository contains a Python-based pipeline for machine condition monitoring, specifically designed to detect and diagnose mechanical unbalance in rotating equipment using vibration analysis and machine learning.

The project is split into two primary modules:
1. **Unsupervised Anomaly Detection** using Isolation Forests and density visualizations.
2. **Diagnostic Order Tracking** using Fast Fourier Transforms (FFT) to isolate mechanical faults irrespective of fluctuating motor speeds.

## ✨ Features

### 1. Anomaly Detection Pipeline (Isolation Forest)
This script processes raw vibration and tachometer (RPM) data to build a baseline of "healthy" machine operation and flags degradation on unseen data.
* **Feature Extraction:** Chops continuous data into 1-second chunks, extracting time-domain statistics (RMS, Peak-to-Peak, Kurtosis, Skewness) and frequency-domain features (Dominant Order).
* **Strict Baseline Training:** Trains an `IsolationForest` on tolerable operating states with a strict 15% contamination rate to tightly bound "normal" behavior.
* **Signal Smoothing:** Applies a 15-second rolling average to raw anomaly scores to eliminate transient noise and prevent false alarms.
* **Density Visualization:** Utilizes Seaborn Violin Plots to show the exact distribution and density of anomaly scores across progressive states of equipment damage.

### 2. Frequency Spectrum & Order Tracking
This diagnostic tool looks inside the vibration frequencies to physically explain *why* an anomaly was flagged.
* **Order Analysis:** Converts the frequency X-axis from Hertz (Hz) to Orders (multiples of running speed). This ensures the 1X peak (running speed) stays centered at 1.0 even if the motor speed fluctuates.
* **Robust 1X Peak Extraction:** Hunts for specific RPM ranges (e.g., 1400–1600 RPM) and takes the median 1X peak across all valid windows, mathematically filtering out random noise spikes.
* **Stacked Spectral Comparison:** Generates a stacked chronological chart comparing multiple fault states, visually demonstrating the growth of specific fault frequencies (like the 1X unbalance peak) as damage worsens.

## 🛠️ Dependencies

This project relies on standard data science and machine learning libraries. You can install them via pip:

```bash
pip install pandas numpy matplotlib seaborn scipy scikit-learn
```

## 📂 Data Structure Requirements

The scripts expect a local directory (configured by default as `D:\vibration_dataset`) containing CSV files representing different states of machine health (e.g., `0D.csv`, `1D.csv` for training; `0E.csv`, `4E.csv` for evaluation).

Each CSV file must contain at least the following columns sampled at **4096 Hz**:
* `Vibration_1`: The raw vibration amplitude array.
* `Measured_RPM`: The instantaneous running speed of the shaft.

## 🚀 Usage

**Module 1: Run the Anomaly Detection**
Execute the first script to train the Isolation Forest, evaluate the unseen evaluation datasets, and generate the Violin Plot and Chronological Scatter Plot. 

**Module 2: Run the Order Tracking**
Execute the second script to dive into the frequency domain. It will hunt for the ~1500 RPM baseline across your datasets, perform the FFT, and plot the stacked Order Spectrum to highlight exactly how the 1X amplitude shifts as the equipment degrades.

## 📊 Visual Outputs

* **Density View (Violin Plot):** Highlights the shift in anomaly score distributions from Healthy (above 0) to Alarm (below 0).
* **Chronological Scatter Plot:** Provides a time-series view comparing a completely healthy baseline against severely damaged data over continuous 1-second rolling windows.
* **Stacked Order Spectrum:** A 5-tier diagnostic chart showing the FFT magnitude (in *g*) plotted against Machine Orders, complete with median 1X peak annotations.
