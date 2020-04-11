# Abstract 
This is a cheat sheet for using the ML library *TensorFlow*. 

**TensorFlow** is an end-to-end open source platform for machine learning. 
It is used for both research and production at Google. 
  
  
  > The used version for this is *tf v1.14.0* \
  > Here' s the link to the [TensorFlow official website](https://www.tensorflow.org/tutorials/structured_data/time_series) \
  
  ## 1. LSTM forecasting time series 
  Import library 
  ```python
  import tensorflow as tf
  ``` 
  
  [Downloads a file from a URL](https://www.tensorflow.org/api_docs/python/tf/keras/utils/get_file) if it not already in the cache. 
  ```python
  zip_path = tf.keras.utils.get_file(
      origin='https://storage.googleapis.com/tensorflow/tf-keras-datasets/jena_climate_2009_2016.csv.zip',
      fname='jena_climate_2009_2016.csv.zip',
      extract=True)  
  ``` 
