# Forecasting and Analysis of Geomagnetic Storms and Ionospheric Variations

## Overview
This project focuses on predicting VTEC (Vertical Total Electron Content) values to forecast geomagnetic storms before they occur. We utilize time series data collected from the Egyptian Space Agency station and Cairo station. The OMNI data, sourced from NASA's OMNIweb, provides key geomagnetic parameters, while CARO data includes actual VTEC values.

## Data Sources
1. **OMNI Data**: Sourced from [NASA OMNIweb](https://omniweb.gsfc.nasa.gov/form/dx1.html). Selected parameters include:
   - Scalar B, nT
   - SW Plasma Speed, km/s
   - Kp index
   - R (Sunspot Number)
   - Dst-index, nT
   - f10.7_index

2. **CARO Data**: Contains actual VTEC values, which are used to train and test our predictive model.

## Objective
The primary goal is to predict VTEC values, which are influenced by the OMNI parameters, to anticipate geomagnetic storms before their occurrence.

## Approach
- **Model**: Simple Recurrent Neural Network (RNN) is employed for forecasting.
- **Feature Engineering**: Extensive feature engineering techniques are applied to enhance model performance.
- **Data**: Time series data spans from January 1, 2023, to December 31, 2023.
- **Prediction**: The model predicts VTEC values for 24 hours of January 1, 2024.

## Confidentiality
Please note that both the data and the outputs (including plots) are confidential and will not be shared. However, the code is available for review.

## Files Included
- **Code**: Contains scripts for data processing, feature engineering, model training, and prediction.


## Usage
Follow the instructions in the code to preprocess data, perform feature engineering, train the RNN model, and make predictions.

## License
This project is licensed under the [MIT License](LICENSE).

## Contact
For any questions or further information, please contact maryamesh1911@gmail.com.

