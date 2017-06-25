# Transfer Learning Tensorflow

Edit: The following contains the code for freezing the first k layers and retraining the last (n-k) layers:
[Retraining multiple layers](Re-train%20multiple%20layers)

[June Python Pune meetup slides](Presentation.pdf)

I followed Tensorflow's tutorial on retraining the final layer of Inception model and tested the results on the flowers dataset(mentioned in the tutorial) as well as on a custom dataset(cats vs dogs).

This tutorial documents the same process along with the problems that I faced while doing so and the links to the solutions.


![N|Solid](https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRdjFiBlkV6tyAEE_nhizN1lJGWXwNJx1Y9CWBrUWvYUuIu_6hu)

The definition, from Wikipedia:
>Transfer learning or inductive transfer is a research problem in machine learning that focuses on storing knowledge gained while solving one problem and applying it to a different but related problem.

# Links:
| Name | URL |
| ------ | ------ |
| Tutorial link| https://www.tensorflow.org/tutorials/image_retraining |
| Install Tensorflow | https://www.tensorflow.org/install/install_sources|
| Configuration | https://www.tensorflow.org/install/install_sources#ConfigureInstallation |

# Setup:

- Install bazel:
	```sh
   echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee/etc/apt/sources.list.d/bazel.list
   curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add -
   sudo apt-get update && sudo apt-get install bazel
    ```
- Clone tensorflow:
  ```sh
  git clone https://github.com/tensorflow/tensorflow 
  ```	
- Change into tensorflow directory:
  ```sh
  ./configure
  ```
  
# Dataset 1: Flower dataset
##### 5 categories (600-800 images for each )
- daisy
- sunflowers
- dandelion
- tulips
- roses

Download the dataset
```sh
  cd ~
  curl -O http://download.tensorflow.org/example_images/flower_photos.tgz
  tar xzf flower_photos.tgz
```

Retraining:
```sh
  bazel build tensorflow/examples/image_retraining:retrain
```

Retrain on the flower dataset
```sh
  bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir ~/flower_photos 
```

Final test accuracy = 89.1% (N=384)

# Visualize the retraining

```sh
tensorboard --logdir /tmp/retrain_logs
```
![Solid](https://github.com/HusainZafar/TransferLearningTutorial/blob/master/Visualize.png?raw=true)

Trained model is stored as /tmp/output_graph.pb
It is reused for further testing.

/tmp/output_labels.txt contains the labels given for training, i.e., the folder names.

Testing on an image 21652746_cc379e0eea_m.jpg:

```sh
bazel-bin/tensorflow/examples/label_image/label_image  --graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt  --output_layer=final_result  --image=$HOME/Downloads/flower_photos/daisy/21652746_cc379e0eea_m.jpg --input_layer=Mul
```

#### Result:
2017-06-05 13:05:24.554667: I tensorflow/examples/label_image/main.cc:251] daisy (2): 0.998173

2017-06-05 13:05:24.554708: I tensorflow/examples/label_image/main.cc:251] sunflowers (3): 0.00125897

2017-06-05 13:05:24.554717: I tensorflow/examples/label_image/main.cc:251] dandelion (4): 0.000368108

2017-06-05 13:05:24.554725: I tensorflow/examples/label_image/main.cc:251] tulips (0): 0.000137791

2017-06-05 13:05:24.554735: I tensorflow/examples/label_image/main.cc:251] roses (1): 6.20492e-05

The flower was correctly detected.

# Dataset 2: Cats Dogs dataset
I downloaded 218 images of cats and dogs, each and created a folder 'animals', containg 'cats' and 'dogs' folders, in the home directory.

Retraining on the animals folder:
```sh
bazel-bin/tensorflow/examples/image_retraining/retrain --image_dir ~/animals
```
Final test accuracy = 100.0% (N=36)

Let's test on an image of a dog and a cat:

![Solid](https://github.com/HusainZafar/TransferLearningTutorial/blob/master/animals/dogs/132.jpg?raw=true)
```sh
bazel-bin/tensorflow/examples/label_image/label_image  --graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt  --output_layer=final_result  --image=$HOME/Pictures/132.jpg --input_layer=Mul
```
Result:
2017-06-05 13:25:35.234769: I tensorflow/examples/label_image/main.cc:251] dogs (1): 0.998014

2017-06-05 13:25:35.234809: I tensorflow/examples/label_image/main.cc:251] cats (0): 0.00198587

![solid](https://github.com/HusainZafar/TransferLearningTutorial/blob/master/animals/cats/117.jpg?raw=true)

```sh
bazel-bin/tensorflow/examples/label_image/label_image  --graph=/tmp/output_graph.pb --labels=/tmp/output_labels.txt  --output_layer=final_result  --image=$HOME/Pictures/117.jpg --input_layer=Mul
```
Result:
2017-06-05 13:26:53.620682: I tensorflow/examples/label_image/main.cc:251] cats (0): 0.99999

2017-06-05 13:26:53.620725: I tensorflow/examples/label_image/main.cc:251] dogs (1): 1.00321e-05

### Errors encountered
- E tensorflow/examples/label_image/main.cc:350] Running model failed: Not found: FeedInputs: unable to     find feed output input
 
  Solution: https://github.com/tensorflow/serving/issues/295  @davidsmandrade

- InvalidArgumentError (see above for traceback): NodeDef mentions attr 'dct_method' not in Op image:uint8; attr=channels ...
 
  Solution: Tensorflow version issues. Graph was created using another version and being tested using another version.
- ImportError: cannot import name pywrap_tensorflow

  Solution: https://stackoverflow.com/a/35963479

License
----

MIT
