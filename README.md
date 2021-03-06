# DeformableRavens

Code for the paper *Learning to Rearrange Deformable Cables, Fabrics, and Bags
with Goal-Conditioned Transporter Networks*. [Here is the project website][5].
Contents of this README:

- [Installation](#installation)
- [Environments and Tasks](#environments-and-tasks)
- [Code Usage](#code-usage)
- [Miscellaneous](#miscellaneous)


## Installation

*Note: Instructions here are for local machines, not for gCloud.* Please see `gCloud.md` for details on gCloud usage,
however it may be harder for us to provide support for this use case.

First, get conda on the machine if it isn't there already:

```
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh
```

Then, create a new Python 3.7 conda environment (e.g., named "py3-defs") and
activate it:

```
conda create -n py3-defs python=3.7
conda activate py3-defs
```

Then install:

```
./install_python_ubunutu.sh
```

**Note I**: It is tested on **Ubuntu 18.04**. We have not tried other Ubuntu
versions or other operating systems.

**Note II**: Installing TensorFlow using conda is usually easier than pip
because the conda version will ship with the correct CUDA and cuDNN libraries,
whereas the pip version is a nightmare regarding version compatibility.

**Note III**: the code has only been tested with **PyBullet 3.0.4**. In fact,
there are some places which explicitly hard-code this requirement. Using later
versions may work but is not recommended.

## Environments and Tasks

This repository contains tasks in the ICRA 2021 submission and the predecessor
paper on Transporters (presented at CoRL 2020). For the latter paper, there are
(roughly) 10 tasks that came pre-shipped; the Transporters paper doesn't test
with `pushing` or `insertion-translation`, but tests with all others. See
`Tasks.md` for some task-specific documentation

Each task subclasses a `Task` class and needs to define its own `reset()`. The
`Task` class defines an oracle policy that's used to get demonstrations (so it
is not implemented within each task subclass), and is divided into cases
depending on the action, or `self.primitive`, used.

Similarly, different tasks have different reward functions, but all are
integrated into the `Task` super-class and divided based on the `self.metric`
type: `pose` or `zone`.

## Code Usage

Experiments start with `python main.py`, with `--disp` added for seeing the
PyBullet GUI (but not used for large-scale experiments). The general logic for
`main.py` proceeds as follows:

- Gather expert demonstrations for the task and put it in `data/{TASK}`, unless
  there are already a sufficient amount of demonstrations. There are
  sub-directories for `action`, `color`, `depth`, `info`, etc., which store the
  data pickle files with consistent indexing per time step. **Caution**: this
  will start "counting" the data from the existing `data/` directory. If you
  want entirely fresh data, delete the relevant file in `data/`.

- Given the data, train the designated agent. The logged data is stored in
  `logs/{AGENT}/{TASK}/{DATE}/{train}/` in the form of a `tfevent` [file for
  TensorBoard][4]. **Note**: it will do multiple training runs for statistical
  significance.

To inspect the data, use `ravens/dataset.py`.

For deformables, we actually use a separate `load.py` script, due to some
issues with creating multiple environments.

See `gCloud.md` for commands we can use for experiments (some of which are
gCloud-specific).

# Miscellaneous

If you have questions, **please use the public issue tracker**, so that all of
us can benefit from your questions.

If you find this code or research paper helpful, please consider citing it:

```
@inproceedings{seita_bags_2021,
    author  = {Daniel Seita and Pete Florence and Jonathan Tompson and Erwin Coumans and Vikas Sindhwani and Ken Goldberg and Andy Zeng},
    title   = {{Learning to Rearrange Deformable Cables, Fabrics, and Bags with Goal-Conditioned Transporter Networks}},
    journal = {arXiv preprint arXiv:2012.03385},
    Year    = {2020}
}
```

[1]:https://www.tensorflow.org/hub/installation
[2]:https://github.com/tensorflow/addons/issues/1132
[3]:https://partner-code.googlesource.com/project-reach/+/75459a560ea9ae4b9d7283ef39d4a4d99598ab81
[4]:https://stackoverflow.com/a/56537286/3287820
[5]:https://berkeleyautomation.github.io/bags/
