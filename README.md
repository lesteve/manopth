Manopth
=======

[MANO](http://mano.is.tue.mpg.de) layer for [PyTorch](https://pytorch.org/) (tested with v0.4 and v1.x)

ManoLayer is a differentiable PyTorch layer that deterministically maps from pose and shape parameters to hand joints and vertices.
It can be integrated into any architecture as a differentiable layer to predict hand meshes.

![image](assets/mano_layer.png)

ManoLayer takes **batched** hand pose and shape vectors and outputs corresponding hand joints and vertices.

The code is mostly a PyTorch port of the original [MANO](http://mano.is.tue.mpg.de) model from [chumpy](https://github.com/mattloper/chumpy) to [PyTorch](https://pytorch.org/).
It therefore builds directly upon the work of Javier Romero, Dimitrios Tzionas and Michael J. Black.

This layer was developped and used for the paper *Learning joint reconstruction of hands and manipulated objects* for CVPR19.
See code [coming soon](https://github.com/hassony2/obman)

If you find this code useful for your research, consider citing:

- the original [MANO](http://mano.is.tue.mpg.de) publication:

```
@article{MANO:SIGGRAPHASIA:2017,
  title = {Embodied Hands: Modeling and Capturing Hands and Bodies Together},
  author = {Romero, Javier and Tzionas, Dimitrios and Black, Michael J.},
  journal = {ACM Transactions on Graphics, (Proc. SIGGRAPH Asia)},
  publisher = {ACM},
  month = nov,
  year = {2017},
  url = {http://doi.acm.org/10.1145/3130800.3130883},
  month_numeric = {11}
}
```

- the publication this PyTorch port was developped for:

```
@INPROCEEDINGS{hasson19_obman,
  title     = {Learning joint reconstruction of hands and manipulated objects},
  author    = {Hasson, Yana and Varol, G{\"u}l and Tzionas, Dimitris and Kalevatykh, Igor and Black, Michael J. and Laptev, Ivan and Schmid, Cordelia},
  booktitle = {CVPR},
  year      = {2019}
}
```

# Installation

## Get code and dependencies

- `git clone https://github.com/hassony2/manopth`
- `cd manopth`
- Install the dependencies listed in [environment.yml](environment.yml)
  - In an existing conda environment, `conda env update -f environment.yml`
  - In a new environment, `conda env create -f environment.yml`, will create a **manopth** conda environment

## Download MANO pickle data-structures

- Go to [MANO website](http://mano.is.tue.mpg.de/)
- Create an account by clicking *Sign Up* and provide your information
- Download Models and Code (the downloaded file should have the format mano_v*_*.zip). Note that all code and data from this download falls under the [MANO license](http://mano.is.tue.mpg.de/license).
- unzip and copy the *models* folder into the manopth/mano folder
- Your structure should look like this:
```
manopth/
  mano/
    models/
      MANO_LEFT.pkl
      MANO_RIGHT.pkl
      ...
  manopth/
    __init__.py
    ...
```

To check that everything is going well, run `python manopth/manopth_mindemo.py`, which should generafrom te a random hand using the MANO layer !

## Install as local package

To be able to import and use *ManoLayer* in another project

`python setup.py install`

`cd /path/to/other/project`

You can now `from manopth import ManoLayer` in this other project!

# Usage 

## Minimal usage script

See [manopth_mindemo.py](manopth_mindemo.py)

Simple forward pass with random pose and shape parameters through MANO layer

```python
import torch
from manopth.manolayer import ManoLayer
from manopth import demo

batch_size = 10
# Select number of principal components for pose space
ncomps = 6

# Initialize MANO layer
mano_layer = ManoLayer(mano_root='path/to/mano/models', use_pca=True, ncomps=ncomps)

# Generate random shape parameters
random_shape = torch.rand(batch_size, 10)
# Generate random pose parameters, including 3 values for global axis-angle rotation
random_pose = torch.rand(batch_size, ncomps + 3)

# Forward pass through MANO layer
hand_verts, hand_joints = mano_layer(random_pose, random_shape)
demo.display_hand({'verts': hand_verts, 'joints': hand_joints}, mano_faces=mano_layer.th_faces)
```

Result :

![random hand](assets/random_hand.png)

## Demo 

With more options, forward and backward pass, and a loop for quick profiling

`python manopth_demo.py`

