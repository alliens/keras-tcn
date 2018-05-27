# Keras TCN
*Keras Temporal Convolutional Network*

 * [Keras TCN](#keras-tcn)
    * [Why Temporal Convolutional Network?](#why-temporal-convolutional-network)
    * [API](#api)
       * [Regression (Many to one) e.g. adding problem](#--regression-many-to-one-eg-adding-problem)
       * [Classification (Many to one) e.g. copy memory task](#--classification-many-to-one-eg-copy-memory-task)
       * [Classification (Many to one) e.g. sequential mnist task](#--classification-many-to-one-eg-sequential-mnist-task)
    * [Installation](#installation)
    * [Run](#run)
    * [Tasks](#tasks)
    * [References](#references)

## Why Temporal Convolutional Network?

- TCNs exhibit longer memory than recurrent architectures with the same capacity.
- Constantly performs better than LSTM/GRU architectures on a vast range of tasks (Seq. MNIST, Adding Problem, Copy Memory, Word-level PTB...).
- Parallelism, flexible receptive field size, stable gradients, low memory requirements for training, variable length inputs...

<p align="center">
  <img src="misc/Dilated_Conv.png">
  <b>Visualization of a stack of dilated causal convolutional layers (Wavenet, 2016)</b><br><br>
</p>

## API

After installation, the model can be imported like this:

```
from tcn import tcn
```

In the following examples, we assume the input to have a shape `(batch_size, timesteps, input_dim)`.

The model is a Keras model. The model functions (`model.summary`, `model.fit`, `model.predict`...) are all functional.



### - Regression (Many to one) e.g. adding problem

```
model = tcn.dilated_tcn(output_slice_index='last',
                        num_feat=input_dim,
                        nb_filters=24,
                        kernel_size=8,
                        dilatations=[1, 2, 4, 8],
                        nb_stacks=8,
                        max_len=timesteps,
                        activation='norm_relu',
                        regression=True)
```

### - Classification (Many to many) e.g. copy memory task

```
model = tcn.dilated_tcn(num_feat=input_dim,
                        num_classes=10,
                        nb_filters=10,
                        kernel_size=8,
                        dilatations=[1, 2, 4, 8],
                        nb_stacks=8,
                        max_len=timesteps,
                        activation='norm_relu')
```

### - Classification (Many to one) e.g. sequential mnist task

```
model = tcn.dilated_tcn(output_slice_index='last',
                        num_feat=input_dim,
                        num_classes=10,
                        nb_filters=64,
                        kernel_size=8,
                        dilatations=[1, 2, 4, 8],
                        nb_stacks=8,
                        max_len=timesteps,
                        activation='norm_relu')
```

## Installation

```
git clone git@github.com:philipperemy/keras-tcn.git
cd keras-tcn
virtualenv -p python3.6 venv
source venv/bin/activate
pip install -r requirements.txt # change to tensorflow if you dont have a gpu.
pip install . # install keras-tcn
```

## Run

```
cd adding_problem/
python main.py # run adding problem task

cd copy_memory/
python main.py # run copy memory task

cd mnist_pixel/
python main.py # run sequential mnist pixel task
```

## Tasks

### Adding Task

#### Explanation

<p align="center">
  <img src="misc/Adding_Task.png">
  <b>Adding Problem Task</b><br><br>
</p>

#### Implementation results

```
200000/200000 [==============================] - 451s 2ms/step - loss: 0.1749 - val_loss: 0.1662
200000/200000 [==============================] - 449s 2ms/step - loss: 0.1681 - val_loss: 0.1676
200000/200000 [==============================] - 449s 2ms/step - loss: 0.1677 - val_loss: 0.1663
200000/200000 [==============================] - 449s 2ms/step - loss: 0.1676 - val_loss: 0.1652
200000/200000 [==============================] - 449s 2ms/step - loss: 0.1165 - val_loss: 0.0093
200000/200000 [==============================] - 448s 2ms/step - loss: 0.0083 - val_loss: 0.0033
200000/200000 [==============================] - 448s 2ms/step - loss: 0.0040 - val_loss: 0.0012
```


<p align="center">
  <img src="misc/Copy_Memory_Task.png">
  <b>Copy Memory Task</b><br><br>
</p>

### Sequential MNIST

#### Explanation

The idea here is to consider MNIST images as 1-D sequences and feed them to the network. This task is particularly hard because sequences are 28*28 = 784 elements. In order to classify correctly, the network has to remember all the sequence. Usual LSTM are unable to perform well on this task.

<p align="center">
  <img src="misc/Sequential_MNIST_Task.png">
  <b>Sequential MNIST</b><br><br>
</p>

#### Implementation results

```
60000/60000 [==============================] - 569s 9ms/step - loss: 0.2209 - acc: 0.9303 - val_loss: 0.0699 - val_acc: 0.9781
60000/60000 [==============================] - 545s 9ms/step - loss: 0.0784 - acc: 0.9760 - val_loss: 0.0507 - val_acc: 0.9843
60000/60000 [==============================] - 553s 9ms/step - loss: 0.0599 - acc: 0.9824 - val_loss: 0.0512 - val_acc: 0.9840
60000/60000 [==============================] - 555s 9ms/step - loss: 0.0493 - acc: 0.9851 - val_loss: 0.0569 - val_acc: 0.9824
60000/60000 [==============================] - 549s 9ms/step - loss: 0.0421 - acc: 0.9868 - val_loss: 0.0424 - val_acc: 0.9864
60000/60000 [==============================] - 558s 9ms/step - loss: 0.0358 - acc: 0.9886 - val_loss: 0.0416 - val_acc: 0.9874
60000/60000 [==============================] - 536s 9ms/step - loss: 0.0317 - acc: 0.9901 - val_loss: 0.0566 - val_acc: 0.9835
60000/60000 [==============================] - 483s 8ms/step - loss: 0.0272 - acc: 0.9915 - val_loss: 0.0565 - val_acc: 0.9845
60000/60000 [==============================] - 489s 8ms/step - loss: 0.0278 - acc: 0.9915 - val_loss: 0.0421 - val_acc: 0.9874
60000/60000 [==============================] - 483s 8ms/step - loss: 0.0227 - acc: 0.9929 - val_loss: 0.0464 - val_acc: 0.9882
60000/60000 [==============================] - 484s 8ms/step - loss: 0.0203 - acc: 0.9935 - val_loss: 0.0428 - val_acc: 0.9890
60000/60000 [==============================] - 484s 8ms/step - loss: 0.0212 - acc: 0.9934 - val_loss: 0.0539 - val_acc: 0.9884
60000/60000 [==============================] - 483s 8ms/step - loss: 0.0167 - acc: 0.9947 - val_loss: 0.0393 - val_acc: 0.9900
```



## References
- https://github.com/locuslab/TCN/ (TCN for Pytorch)
- https://arxiv.org/pdf/1803.01271.pdf (An Empirical Evaluation of Generic Convolutional and Recurrent Networks
for Sequence Modeling)
- https://arxiv.org/pdf/1609.03499.pdf (Original Wavenet paper)
