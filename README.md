![image](https://github.com/user-attachments/assets/d2d0d0f0-9386-4ff0-a762-829a5c9f1628)

# Geomagnetic Storm and Ionospheric Variation Forecasting

## Project Overview

This project forecasts Vertical Total Electron Content (VTEC) in the ionosphere to predict geomagnetic storms. VTEC impacts GPS accuracy and radio communications, making early forecasting essential for mitigating ionospheric disturbances.

- Uses time series data from Egyptian Space Agency GNSS stations, including Cairo.
- Incorporates geomagnetic parameters from NASA's OMNI dataset.
- Forecasts VTEC 24 hours ahead to support early warning of ionospheric disturbances.

---

## Data Description

### Data Sources

- **OMNI Data**  
  Includes key geomagnetic and solar parameters:  
  - Scalar magnetic field (nT)  
  - Solar wind plasma speed (km/s)  
  - Kp index (geomagnetic activity)  
  - Sunspot number (R)  
  - Dst index (nT)  
  - F10.7 solar flux index

- **VTEC Data**  
  Actual Vertical Total Electron Content values from Egyptian GNSS stations.

### Features and Target

Input features include 22 signals per time step, such as:

- Geomagnetic field components (BS, BZ, MagMN)
- Solar wind parameters (SWD, SWS)
- Geomagnetic indices (Kp, Dst)
- Solar flux values (f10, Rs)
- Location data (latitude, longitude)
- Time cyclic encodings (hour of day and day of year encoded as sine and cosine)
- Engineered features:  
  - Exponential Moving Averages of VTEC (4-day and 30-day) for smoothing trends  
  - First and second derivatives of VTEC to capture rate and acceleration of changes  
  - Lagged VTEC values at 1 hour and 24 hours to capture autocorrelation and daily periodicity  

Target variable:

- Average TEC (VTEC) to be predicted 24 hours ahead

### Feature Engineering Rationale

- **EMAs (4-day & 30-day):** Smooth short-term fluctuations and capture seasonal trends.  
- **Derivatives:** Highlight dynamic changes in VTEC useful for detecting disturbances.  
- **Lags:** Exploit ionosphere's autocorrelation and daily cycle.  
- **Sin/Cos Encoding:** Preserves cyclical nature of time features, improves model’s ability to learn periodic patterns in ionospheric behavior.

---

## Model Architecture

### Input

- Time series input of shape (72, 22):  
  72 time steps represent 3 days of hourly data  
  22 features per time step

- Hour input (scalar): hour of prediction used in penalty layer

### Layers

- 3 stacked Bidirectional LSTM layers with tanh activation:  
  - 128 units (return sequences)  
  - 64 units (return sequences)  
  - 32 units (final output)  

- Batch Normalization and Dropout (0.3) after first two LSTM layers  
- Dropout (0.3) after third LSTM layer  

- Dense layers with swish activation:  
  - 64 units + Dropout (0.2)  
  - 32 units  

- Output: Single scalar for predicted VTEC  

### Custom Loss Layer: DayNightPenalty

- Penalizes unrealistic predictions based on scientific ionospheric behavior:  
  - Night (hours < 6 or > 18): VTEC predictions penalized if above 10 TECU  
  - Day (hours 6–18): VTEC predictions penalized if below 30 TECU  

- Scaled penalty added to loss during training to improve physical plausibility

---

## Training Details

- Loss function: Mean Squared Error + DayNightPenalty  
- Optimizer: Adam (learning rate 0.001)  
- Batch size: 32  
- Epochs: Up to 100 with early stopping (patience=10) and learning rate reduction on plateau (patience=5)  
- Validation split: 20% of dataset reserved for validation  
- Callbacks used:  
  - EarlyStopping to avoid overfitting  
  - ReduceLROnPlateau to adapt learning rate  
  - ModelCheckpoint to save best weights  

---

## Data Preparation

- Data sorted by datetime  
- Features and target scaled using MinMaxScaler between 0 and 1  
- Dataset split: 80% training, 20% testing  
- Sequences created using sliding window of 72 time steps  
- Time features encoded using sine and cosine transforms for hour and day  
- Engineered features included as described above

---

## Model Evaluation

- Predictions inverse-scaled to original VTEC units  
- Metrics used:  
  - Root Mean Squared Error (RMSE): emphasizes larger errors  
  - Mean Absolute Error (MAE): average magnitude of errors  

Typical training and testing dataset shapes:  
- Training samples ~591,487 sequences  
- Testing samples ~147,818 sequences  
- Each sequence: (72 time steps × 22 features)

---

## Forecasting Procedure

1. Load saved model with custom DayNightPenalty layer  
2. Select target forecast date  
3. Extract preceding 72-hour feature sequences  
4. Predict VTEC hourly for the forecast day  
5. Plot predicted versus actual VTEC values for validation and analysis

---

## Repository Contents

- Data preprocessing and feature engineering scripts  
- LSTM model definition and training scripts  
- Custom DayNightPenalty layer implementation  
- Forecasting and plotting utilities  
- Requirements file specifying Python dependencies  

---

## Usage Instructions

1. Prepare input data in required format with all features  
2. Run preprocessing script to scale data and create sequences  
3. Train model using training scripts with defined parameters  
4. Save trained model checkpoint  
5. Use forecasting script to generate 24-hour ahead VTEC predictions  
6. Visualize results by comparing forecasts to observed VTEC  

---

## Software Environment

- Python 3.x (tested on 3.11)  
- TensorFlow 2.11.0  
- NumPy 1.22.4  
- Pandas 1.5.3  
- Matplotlib 3.6.2  
- Scikit-learn 1.2.2  

Install dependencies via:

```bash
pip install numpy pandas tensorflow matplotlib scikit-learn
```

## Contact
For any questions or further information, please contact maryamesh1911@gmail.com.

