# Damage Proxy Mapping with Time Series Prediction of InSAR Phase Standard Deviation
# README: Damage Proxy Mapping with Time Series Prediction of InSAR Phase Standard Deviation

## Overview

This project focuses on generating **Damage Proxy Maps (DPMs)** using time series data derived from **Interferometric Synthetic Aperture Radar (InSAR)**. By leveraging predictions of InSAR phase standard deviation (STD) through a Long Short-Term Memory (LSTM) model, the project identifies surface displacements post-earthquake, which serve as proxies for assessing the extent of damage. The workflow involves preprocessing multi-temporal SAR images, constructing time series data, training a model for phase STD prediction, and calculating a damage score to map areas of significant change.

### Key Features

1. **Data Preprocessing**:

   * The **Sentinel-1 SAR data** undergoes preprocessing steps including **baseline estimation**, **multi-looking**, **image registration**, **filtering**, and **geocoding**. These steps prepare the raw SAR data for interferometric processing and subsequent analysis.
   * **Interferometric processing** is performed to calculate the pixel-level **phase standard deviation (STD)** for each interferogram formed from adjacent image pairs.

2. **InSAR Time Series Construction**:

   * A time series is constructed by arranging the phase STD values chronologically for each pixel. This series, along with relevant temporal features (e.g., year, month, day), serves as input for the **LSTM network**.
   * The **LSTM model** is trained to predict phase STD values under an assumed **post-disaster scenario**, simulating conditions as if no disaster had occurred. This provides a baseline for detecting anomalies caused by the disaster.

3. **Damage Score Calculation**:

   * The **damage score** is constructed by comparing the **LSTM-predicted phase STD** with the observed **post-disaster phase STD**. This score quantifies the extent of displacement or change and acts as an indicator of damage.
   * **Normalized difference** calculations are used to highlight discrepancies between predicted and observed values, helping identify areas of potential damage.

4. **Geocoding and Subsetting**:

   * After generating the damage scores, the data is **geocoded** using the `geocode.py` tool to associate it with geographic coordinates (latitude and longitude) using mintpy (https://github.com/insarlab/MintPy).
   * A **subset extraction** is then performed, focusing on regions of interest, and the results are saved as **GeoTIFF** files for visualization and further analysis.

5. **Damage Proxy Mapping (DPM)**:

   * The final output includes high-precision **damage proxy maps** (DPMs) that visualize surface displacement and standard deviation, enabling disaster response teams to efficiently assess the damage.

---

## Project Workflow

### 1. Data Preprocessing

The first step in the workflow involves the collection and preprocessing of multi-temporal SAR images, which includes baseline estimation, multi-looking, image registration, filtering, and geocoding. This is performed using the `stackSentinel.py` script (https://github.com/isce-framework/isce2/blob/main/contrib/stack/topsStack/stackSentinel.py), which automates the entire process:

* **SAR Image Collection**: Multiple Sentinel-1 SAR images are collected for the desired time period.
* **Preprocessing**: This includes operations such as multi-looking to reduce the speckle noise, baseline estimation to determine the geometric relationship between the image pairs, and image registration to align the images for interferometric analysis.
* **Geocoding**: The SAR data is geocoded to associate the interferogram with geographic coordinates, allowing for easier interpretation in GIS tools.

### 2. Interferometric Processing and Time Series Construction

After preprocessing, **interferometric processing** is applied to the SAR data. The **pixel-level phase STD** for each interferogram formed from adjacent image pairs is calculated. The phase STD values are then arranged chronologically to construct an **InSAR time series** for each pixel.

* The time series includes both **pre-disaster** and **post-disaster** phase STD data, with temporal features such as the year, month, and day incorporated into the input for the LSTM model.

### 3. LSTM Model for Phase STD Prediction

An **LSTM neural network** is used to model the temporal relationships in the InSAR data. The model is trained on the phase STD values and their associated temporal features to predict the phase STD under a **post-disaster scenario** (i.e., simulating conditions as if no disaster had occurred). This predicted phase STD represents the expected surface displacement under normal conditions.

* **Training the LSTM Model**: The LSTM model learns to predict the future phase STD values, which are then compared to the observed post-disaster values to construct the damage score.

### 4. Damage Score Calculation

The **damage score** is derived by comparing the **predicted phase STD** (from the LSTM model) with the **observed post-disaster phase STD**. The normalized difference score is used to highlight discrepancies, where large differences indicate significant changes or damage.

* **Normalized Difference**: The normalized difference score is calculated for each pixel in the interferogram, providing a quantitative measure of damage.

### 5. Geocoding and Subsetting

Once the damage scores are calculated, the next step involves **geocoding** the data using latitude and longitude files, ensuring that each data point is associated with geographic coordinates.

* **Subset Extraction**: Geographic subsets are extracted based on the desired bounding box coordinates, focusing on regions of interest.
* **Saving as GeoTIFF**: The subsetted data is saved as **GeoTIFF files** for visualization and further analysis in GIS applications.

### 6. Damage Proxy Mapping (DPM)

The final step is the generation of **damage proxy maps (DPMs)**, which visualize the damage score and surface displacement across the region of interest. These maps serve as important tools for disaster response, allowing for a visual representation of the areas most affected by the disaster.

* **Visualization**: The damage proxy maps are visualized using **Matplotlib** and can be saved as images or GeoTIFF files for GIS analysis.

---

## File Structure

```
/WORKDIR/
├── merged/
│   ├── interferograms/
│   │   ├── cropped/    # Contains cropped coherence files
│   │   ├── predict/    # Contains prediction results
│   │   ├── dataset/    # Contains pre-processed InSAR time series data
│   │   └── geom_reference/  # Geometry reference files (lat, lon, etc.)
│   └── dataset/        # Final processed InSAR time series dataset
```

