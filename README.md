# Complex Activity Recognition in Eating Patterns Using GLEAM Dataset

## Introduction

Activity recognition is increasingly used in healthcare (e.g., tracking diet, exercise, and managing chronic conditions). Eating is a fundamental health-related behavior, but detecting it unobtrusively is challenging. Traditional sensors (wrist-worn, neck accelerometers or microphones) can be intrusive or imprecise. In contrast, head-mounted sensing is a novel, less invasive approach. Head-worn inertial sensors (e.g., on smart eyewear) can capture subtle head motions associated with eating, while maintaining user comfort. For example, Google Glass integrates accelerometers and gyroscopes in a lightweight form factor, enabling continuous head-motion capture with minimal privacy concerns.

## Goal

The goal of this project is to **identify and classify eating-related activities** from head-motion sensor data. By analyzing time-series inertial data, we aim to distinguish ‘eating’ gestures from other daily activities. Successful recognition can support automated dietary monitoring or mealtime interventions.

## Dataset Description
 http://skleinberg.org/data/GLEAM.tar.gz
We use the **Glass Eating and Motion (GLEAM)** dataset (Rahman et al., 2015), which contains head-mounted sensor recordings and activity annotations:

- **Participants & Protocol:** 38 subjects wore Google Glass and performed scripted activities (including eating meals, drinking, walking, reading, etc.) over ~2 hours each.
- **Sensors & Data:** Google Glass’s built-in inertial sensors (3-axis accelerometer, gyroscope, rotation vector, magnetometer, and light sensor) sampled at irregular intervals (~200–400 ms).
- **Annotations:** Time-stamped labels for eating, drinking, walking, talking, and free-time activities, recorded via a custom Android app.
- **Data Structure:** Individual CSV files per participant containing raw sensor streams, an annotation CSV, and food images (start, halfway, end). A master `Demographics_Glass_data.csv` contains participant info (age, sex, chair type, utensils used, food type).

## Methodology and Approach

1. **Data Preprocessing**
   - Load raw sensor and annotation CSVs.
   - Convert timestamps to datetime, drop duplicates, and synchronize streams.
   - Resample each sensor to a fixed rate (e.g., 200 ms) with interpolation to handle irregular sampling.

2. **Exploratory Data Analysis (EDA)**
   - Plot sensor signals for eating vs. non-eating segments.
   - Inspect class balance and sensor distributions.

3. **Sliding-Window Segmentation**
   - Segment the time series into fixed-length windows (e.g., 5 s) with optional overlap (50%).
   - Label each window by the majority activity within its timeframe.

4. **Feature Extraction**
   - Compute time-domain and frequency-domain features (see next section).
   - Normalize features (zero-mean, unit variance) for modeling.

5. **Model Training**
   - Split data into training and test sets (by participant or random split).
   - Train classifiers on feature vectors (Random Forest, SVM, Logistic Regression, CNN/LSTM).
   - Tune hyperparameters via cross-validation.

6. **Evaluation**
   - Assess performance using accuracy, precision, recall, F1-score, and confusion matrices.

## Feature Engineering

- **Statistical Features:** mean, standard deviation, min, max, median, interquartile range, skewness, kurtosis, peak count.
- **Energy Features:** signal energy (mean squared value), signal magnitude area, average resultant acceleration.
- **Frequency-Domain Features:** Fast Fourier Transform (FFT) magnitudes, dominant frequency, spectral centroid, band energies.
- **Windowing:** fixed-length windows (e.g., 5 s) with 50% overlap to capture multiple eating gestures.
- **Irregular Sampling Handling:** resample to uniform grid and linear interpolation to ensure consistent sample counts per window.

## Modeling

We experimented with both traditional and deep learning models:

- **Random Forest:** Ensemble of decision trees, robust to feature heterogeneity.
- **Support Vector Machine (SVM):** Linear and nonlinear kernels for binary classification.
- **Logistic Regression:** Baseline linear model.
- **Neural Networks (CNN/LSTM):** 1D convolutional and LSTM architectures on raw or windowed data.

Tree-based and SVM models achieved the highest accuracy (~90–93%) in our experiments.

## Insights

- **High Detection Accuracy:** Best models reached ~92–93% overall accuracy.
- **Class Metrics:** Eating precision/recall ~0.76–0.80; non-eating ~0.95.
- **Key Features:** Variance, energy, and spectral peaks were most discriminative of eating gestures.
- **Window Size Impact:** 5 s windows outperformed 2 s windows, capturing more context.
- **Failure Modes:** Transitional and subtle gestures occasionally led to misclassifications.


