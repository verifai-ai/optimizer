# ==============================================================================                                                        # Copyright 2020 Verifai Inc All Rights Reserved.                                                                                       #                                                                                                                                        # ==============================================================================
# verifai
Verifying hardware and software with deep learning



## Installation

There are two options to download and install the VerifAI optimizer on your Linux or MacOS machine.

1. *Create a virtual environment*  -- Download the wheel [*VERIFAI.AI-1.0-py3-none-any.whl*](https://github.com/verifai-ai/optimizer/blob/master/VERIFAI.AI-1.0-py3-none-any.whl)     
2. *Use your existing python3 setup to run* -- Download a tarball [*VERIFAI.AI-1.0.tar.gz*](https://github.com/verifai-ai/optimizer/blob/master/VERIFAI.AI-1.0.tar.gz)  


## Create a virtual environment

```bash
python3 -m venv VerifaiAPI
cp VERIFAI.AI-1.0-py3-none-any.whl VerifaiAPI/
source VerifaiAPI/bin/activate
cd VerifaiAPI
python -m ensurepip
```

### Install the wheel file and then sign up

```bash
bin/pip3 install VERIFAI.AI-1.0-py3-none-any.whl
python bin/signup.py
```
This lets user create a login for the api, with a username and password and receive a token to access the VerifaiAPI


### Run the sample optimize example Using

```bash
python bin/optimize.py -c config.json -tr FIFO_knobs.csv -iter 3
```

##  Use your existing python3 setup 


Untar it and install requirements
```bash
tar -xvf  *VERIFAI.AI-1.0.tar.gz*
cd VERIFAI.AI-1.0
pip install -r requirements.txt
```
```bash
python signup.py
```

Run the sample optimize example Using

```bash
python optimize.py -c config.json -tr FIFO_knobs.csv -iter 3
```


## Use cases

Let's say we have a dataset with a set of input features which can be controllable knobs for a particular target column. These knobs can be set on some simulator and the simulator returns the value of the target variable for those settings.
The role of the optimizer is to find the best knob settings to maximize the value of the target variable.

The optimizer uses both supervised and reinforcement learning to map the knobs to the reward using a neural network as a function approximator and then uses global optimisation algorithms to find the optimum input knobs.

Some other examples of this are in optimal control of electric power.


## Hardware Verification Example: The FIFO cache controller experiment

VCS installation instructions here

```bash
export VCS_HOME=<Path to VCS dir>
```

### Run FIFO run_sims

```bash
cd example
python run_sims.py -c config.json -ir random_knobs.csv  -iter 2
```


### The FIFO cache controller experiment

Cache Controller Design - Experiment 1
The experiment is a Cache Controller design shown in Fig 1. The controller supports up to four CPU ports. Input transaction collisions are checked at the input stage where four FIFOs - one per CPU port - holds transactions which fail collision checks with transactions from another CPU ports and hence must be serviced later.

<img src="images/image34.png"
         width="500" height="300" />


Figure-1:  Cache Controller with 4 CPU Ports

Input FIFO’s can be so difficult to fill in the presence of random traffic that it seldom or perhaps never reaches a full state even across a very large number of simulations. This has implications for DV coverage since the FIFO must be filled in order to ensure correct operation and to uncover bugs resulting from a FIFO full condition.
After a suitable number of simulation runs were completed, the resulting DV control settings and the corresponding FIFO occupancy simulation results were fed to an VerifAI-RL agent which, once trained, generated a set of recommended settings to use in a subsequent set of runs. After completing this second set of simulation runs using the settings from the RL agent, the results were again fed into the RL agent which generated a subsequent improved set of recommended settings. This process is summarized in Figure-2  below:

<img src="images/FIFO-RL-Img1.png"
         width="500" height="300" />

Figure-2:  Cache Controller Design VerifAI-RL Flow


Figure below shows the resulting FIFO occupancy for each subsequent iteration (red curve).

<img src="images/FIFO-RL-Img3.png"
         width="600" height="400" />

Figure-3:  FIFO Occupancy with VerifAI-RL generated knobs versus Random

Note that the RL agent quickly learns how to adjust the DV environment control settings in order to maximize the FIFO occupancy. A set of runs using purely random settings without the benefit of RL-based machine learning is also shown for comparison (blue curve).
The results shown in Figures below demonstrate how an RL-based approach can quickly and automatically optimize design coverage parameters resulting in better design coverage and accelerated stress testing of a design.

<img src="images/FIFO-RL-Img4.png"
         width="400" height="400" />
   <img src="images/FIFO-RL-Img2.png"
 width="400" height="400" />

Figure 4:  Average  FIFO Depth with Random Stimulus and Iteration-6 using Verifai’s ML/RL

The resulting plots are placed in the folder 'images/mesi_cache_fifo' 
The resulting output of the optimizer are VERIFAI_RL_FIFO_input_*.csv , these are the inputs knobs settings for the simulator.

Here is a link to the entire article published on Arxiv describing this experiment. [Doing better than random](https://arxiv.org/abs/1909.13168)
