# Retraining multiple layers of Inception

Links:

- https://keras.io/applications/
- https://blog.keras.io/building-powerful-image-classification-models-using-very-little-data.html
- https://keras.io/models/sequential/

Note:
- Output labels were and should be one-hot encoded
- Train.npy and tlabels.npy has been truncated to include only 50 samples due to size constraints. Accuracy/Loss values should be ignored
- All images were converted to ndarray of size (1,150,150,3) and saved in train.npy file. train.npy has size (n,150,150,3) and tlabels/npy has size (n,2) as we have 2 labels
