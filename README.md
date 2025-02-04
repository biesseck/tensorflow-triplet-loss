# BERNARDO
conda env with python 3.8

tensorflow==2.2.0

cuda 10.1, cuDNN v7.6.5, gcc 7, g++ 7

export PROTOCOL_BUFFERS_PYTHON_IMPLEMENTATION=python

____________________________

conda env with python 2.7

pip install numpy, pytest, tensorflow-gpu==1.6.0

cuda 9.0, cuDNN v7.0.5, gcc 5, g++ 5

cuda 9.0 (patches):

https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/1/cuda_9.0.176.1_linux-run
https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/2/cuda_9.0.176.2_linux-run
https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/3/cuda_9.0.176.3_linux-run
https://developer.nvidia.com/compute/cuda/9.0/Prod/patches/4/cuda_9.0.176.4_linux-run

```
# CONFIG ENV VARIABLES
export CUDA_HOME_TMP="/usr/local/cuda-9.0" && conda env config vars set CUDA_HOME=$CUDA_HOME_TMP
export LD_LIBRARY_PATH_TMP="$CUDA_HOME_TMP/lib64" && conda env config vars set LD_LIBRARY_PATH=$LD_LIBRARY_PATH_TMP
export PATH_TMP="$CUDA_HOME_TMP:$CUDA_HOME_TMP/bin:$LD_LIBRARY_PATH_TMP:$PATH" && conda env config vars set PATH=$PATH_TMP
export CUDA_CACHE_MAXSIZE_TMP=2147483648 && conda env config vars set CUDA_CACHE_MAXSIZE=$CUDA_CACHE_MAXSIZE_TMP
conda deactivate
conda activate <env_name>
```
____________________________

# Triplet loss in TensorFlow [![Build Status](https://travis-ci.org/omoindrot/tensorflow-triplet-loss.svg?branch=master)](https://travis-ci.org/omoindrot/tensorflow-triplet-loss)
*Author: Olivier Moindrot*

This repository contains a triplet loss implementation in TensorFlow with online triplet mining.
Please check the [blog post][blog] for a full description.

The code structure is adapted from code I wrote for [CS230](https://cs230.stanford.edu) in [this repository](https://github.com/cs230-stanford/cs230-code-examples) at `tensorflow/vision`.
A set of tutorials for this code can be found [here](https://cs230-stanford.github.io).


## Requirements

We recommend using python3 and a virtual environment.
The default `venv` should be used, or `virtualenv` with `python3`.

```bash
python3 -m venv .env
source .env/bin/activate
pip install -r requirements_cpu.txt
```

If you are using a GPU, you will need to install `tensorflow-gpu` so do:
```bash
pip install -r requirements_gpu.txt
```

## Triplet loss

|![triplet-loss-img] |
|:--:|
| *Triplet loss on two positive faces (Obama) and one negative face (Macron)* |



The interesting part, defining triplet loss with triplet mining can be found in [`model/triplet_loss.py`](model/triplet_loss.py).

Everything is explained in the [blog post][blog].

To use the "batch all" version, you can do:
```python
from model.triplet_loss import batch_all_triplet_loss

loss, fraction_positive = batch_all_triplet_loss(labels, embeddings, margin, squared=False)
```

In this case `fraction_positive` is a useful thing to plot in TensorBoard to track the average number of hard and semi-hard triplets.

To use the "batch hard" version, you can do:
```python
from model.triplet_loss import batch_hard_triplet_loss

loss = batch_hard_triplet_loss(labels, embeddings, margin, squared=False)
```

## Training on MNIST

To run a new experiment called `base_model`, do:
```bash
python train.py --model_dir experiments/base_model
```

You will first need to create a configuration file like this one: [`params.json`](experiments/batch_all/params.json).
This json file specifies all the hyperparameters for the model.
All the weights and summaries will be saved in the `model_dir`.

Once trained, you can visualize the embeddings by running:
```bash
python visualize_embeddings.py --model_dir experiments/base_model
```

And run tensorboard in the experiment directory:
```bash
tensorboard --logdir experiments/base_model
```

Here is the result ([link][embeddings-gif] to gif):

|![embeddings-img] |
|:--:|
| *Embeddings of the MNIST test images visualized with T-SNE (perplexity 25)* |



## Test

To run all the tests, run this from the project directory:
```bash
pytest
```

To run a specific test:
```bash
pytest model/tests/test_triplet_loss.py
```


## Resources

- [Blog post][blog] explaining this project.
- Source code for the built-in TensorFlow function for semi hard online mining triplet loss: [`tf.contrib.losses.metric_learning.triplet_semihard_loss`][tf-triplet-loss].
- [Facenet paper][facenet] introducing online triplet mining
- Detailed explanation of online triplet mining in [*In Defense of the Triplet Loss for Person Re-Identification*][in-defense]
- Blog post by Brandom Amos on online triplet mining: [*OpenFace 0.2.0: Higher accuracy and halved execution time*][openface-blog].
- Source code for the built-in TensorFlow function for semi hard online mining triplet loss: [`tf.contrib.losses.metric_learning.triplet_semihard_loss`][tf-triplet-loss].
- The [coursera lecture][coursera] on triplet loss


[blog]: https://omoindrot.github.io/triplet-loss
[triplet-types-img]: https://omoindrot.github.io/assets/triplet_loss/triplets.png
[triplet-loss-img]: https://omoindrot.github.io/assets/triplet_loss/triplet_loss.png
[online-triplet-loss-img]: https://omoindrot.github.io/assets/triplet_loss/online_triplet_loss.png
[embeddings-img]: https://omoindrot.github.io/assets/triplet_loss/embeddings.png
[embeddings-gif]: https://omoindrot.github.io/assets/triplet_loss/embeddings.gif
[openface-blog]: http://bamos.github.io/2016/01/19/openface-0.2.0/
[facenet]: https://arxiv.org/abs/1503.03832
[in-defense]: https://arxiv.org/abs/1703.07737
[tf-triplet-loss]: https://www.tensorflow.org/api_docs/python/tf/contrib/losses/metric_learning/triplet_semihard_loss
[coursera]: https://www.coursera.org/learn/convolutional-neural-networks/lecture/HuUtN/triplet-loss
