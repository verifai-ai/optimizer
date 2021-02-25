   <img src="images/4-BlackLogo-01.png"
         width="100" height="58" />
    

=============================================================================                                                        

Copyright 2020 Verifai Inc All Rights Reserved.                                                                                         

==============================================================================

# VerifAI Optimizer
Verifying hardware and software with deep learning. 
Here is a link to the entire article published on Arxiv describing this experiment. [Doing better than random](https://arxiv.org/abs/1909.13168)


## Installation

There are two options to download and install the VerifAI optimizer on your Linux or MacOS machine.

1. *Create a virtual environment*  -- Download the wheel [*VERIFAI.AI-1.5-py3-none-any.whl*](https://github.com/verifai-ai/optimizer/blob/master/VERIFAI.AI-1.5-py3-none-any.whl)     
2. [*Use your existing python3 setup to run*](#Use-your-existing-python3-setup) -- Download a tarball [*VERIFAI.AI-1.5.tar.gz*](https://github.com/verifai-ai/optimizer/blob/master/VERIFAI.AI-1.5.tar.gz)  


## Create a virtual environment (Recommended)

```bash
python3 -m venv verifai
cp VERIFAI.AI-1.5-py3-none-any.whl verifai/
source verifai/bin/activate
cd verifai
python3 -m ensurepip
```

### Install the wheel file and then sign up

```bash
export VERIFAI_HOME=~/verifai
bin/pip3 install VERIFAI.AI-1.5-py3-none-any.whl
python3 bin/signup.py
```
This lets users create a login for the api, with a username and password and receive a token to access the VerifaiAPI Server
If you already have credentials on the VerifAI server, skip to the next step and use that username and password to signin, when prompted.


## Example 1 Run the sample optimizer example (test the flow)

```bash
python3 bin/optimize.py -c config.json -tr FIFO_knobs.csv

This will produce a CSV file and a combined_histogram.png plot.
```
<img src="images/combined_histogram.png"
         width="400" height="600" />

Figure-1: The histogram distribution of the column to optimize has been shifted to the right using the optimizer

## DV Challenge #1 The FIFO Cache Controller 

### Install Verilator 

The first step is installing [Verilator](https://www.veripool.org/projects/verilator/wiki/Installing) on your machine. 


### Run FIFO run_sims

```bash
cd FIFO
python3 run_sims.py -c config.json -ir random_knobs.csv  -iter 2
```
### Expected Results
```
The run_sims.py command will produce multiple output files that look like this:
VERIFAI_RL_FIFO_input_1.csv   --- Output from the Simulator and Input to the VerifAI Optimizer
VERIFAI_RL_FIFO_output_1.csv  --- Output from the Optimizer and Input to the Simulator
VERIFAI_RL_FIFO_input_2.csv
VERIFAI_RL_FIFO_output_2.csv
fifo_results.png         -----  Final Histogram with your results and scores
```
<img src="images/fifo_results.png"
         width="400" height="600" />

Figure-2: The FIFO DV Challenge Results

### Hacking DNN hyper-parameters in the config.json file to improve your scores

-- Change the hidden_units to specify different hidden layers

-- Change the ensemble_size to specify a larger or smaller ensemble network

-- Change the number of training epochs : training_num_epochs

-- Change the number of knob settings to generate per iteration: num_knobs_to_generate":100  (This will increase run time)

-- Change the num_rand_iterations: Increasing this will result in an increase in runtime

### Hacking the DV test-bench

-- If you are a DV expert, feel free to hack the test-bench to improve your scores

-- If you submit your scores for consideration for the DV Challenge, you will be required to mail your changes to the test-bench to hello@verifai.ai


### Submiting your Scores
```
To submit your scores for consideration to the DV Challenge, do the following:
bin/submit-challenge
```

### About the Cache controller experiment

Cache Controller Design - Experiment 1

The Cache Controller design is an opensource design from [opencores.org](http://www.opencores.org) and its licensed under [LGPL](http://www.opencores.org/lgpl.shtml).

The experiment is a Cache Controller design shown in Fig 1. The controller supports up to four CPU ports. Input transaction collisions are checked at the input stage where four FIFOs - one per CPU port - holds transactions which fail collision checks with transactions from another CPU ports and hence must be serviced later.

<img src="images/image34.png"
         width="500" height="300" />


Figure-2:  Cache Controller with 4 CPU Ports

Input FIFO’s can be so difficult to fill in the presence of random traffic that it seldom or perhaps never reaches a full state even across a very large number of simulations. This has implications for DV coverage since the FIFO must be filled in order to ensure correct operation and to uncover bugs resulting from a FIFO full condition.
After a suitable number of simulation runs were completed, the resulting DV control settings and the corresponding FIFO occupancy simulation results were fed to an VerifAI-RL agent which, once trained, generated a set of recommended settings to use in a subsequent set of runs. After completing this second set of simulation runs using the settings from the RL agent, the results were again fed into the RL agent which generated a subsequent improved set of recommended settings. This process is summarized in Figure-2  below:

<img src="images/FIFO-RL-Img1.png"
         width="500" height="300" />

Figure-3:  Cache Controller Design VerifAI-RL Flow


Figure below shows the resulting FIFO occupancy for each subsequent iteration (red curve).

<img src="images/FIFO-RL-Img3.png"
         width="600" height="400" />

Figure-4:  FIFO Occupancy with VerifAI-RL generated knobs versus Random

Note that the RL agent quickly learns how to adjust the DV environment control settings in order to maximize the FIFO occupancy. A set of runs using purely random settings without the benefit of RL-based machine learning is also shown for comparison (blue curve).
The results shown in Figures below demonstrate how an RL-based approach can quickly and automatically optimize design coverage parameters resulting in better design coverage and accelerated stress testing of a design.

<img src="images/FIFO-RL-Img4.png"
         width="400" height="400" />
   <img src="images/FIFO-RL-Img2.png"
 width="400" height="400" />

Figure-5:  Average  FIFO Depth with Random Stimulus and Iteration-6 using Verifai’s ML/RL

The resulting plots are placed in the folder 'images/mesi_cache_fifo' 
The resulting output of the optimizer are VERIFAI_RL_FIFO_input_*.csv , these are the inputs knobs settings for the simulator.

There is also a combined plot generated that is called 'combined_actual.png' , this plot shows the 'ground truth' results from the simulator after each iteration.

<img src="images/combined_actual.png"
         width="400" height="600" />

Figure-6:  Average FIFO Depth after each batched-iteration of the simulation (ground truth)

## About VerifAI Optimizer

Let's say we have a dataset with a set of input features which can be controllable knobs for a particular target column. These knobs can be set on some simulator and the simulator returns the value of the target variable for those settings.
The role of the optimizer is to find the best *knob settings* to maximize the value of the *target columns*.

Example dataset CSV file

<img src="images/OptimizerCSV.png"
         width="650" height="100" />
         

The optimizer uses both supervised and reinforcement learning to map the knobs to the reward using a neural network as a function approximator and then uses global optimization algorithms to find the optimum input knobs.

Some other examples of this are in optimal control of electric power. In this repo we will demonstrate how to optimize and fill up the FIFO queues on a [MESI Cache Controller](#Example-2-The-FIFO-Cache-Controller-experiment) from [opencores.org](http://www.opencores.org).

Maximizing the FIFO queue depth is a non-trivial task for random tests, and requires a DV expert to write directed-tests to achieve high FIFO queue depths.
This example shows that the VerifAI Optimizer, can maximize the FIFO depth significantly better than a random test, and also better than  directed tests.

You can also run your [own csv datasets](#Example-3-Run-your-own-CSV-file-thru-the-optimizer) thru the optimizer to find the best *knob settings* to maximize  *target column(s)*

## Example 3 Run your own CSV file thru the optimizer

Upload your dataset in CSV format and identify the columns that are 'knobs' and the ones that are the 'target columns'.
Edit the file config.json and reflect the knobs and columns you want to optimize.

Example dataset CSV file

<img src="images/TempOptimizer.png"
         width="650" height="90" />

```
"Config": {
  "RewardPredictor":{
    "reward_column": ["CostPerMonth"], <<-- Change this
    "experiment_type":"TempratureControl",  <<-- Your experiment name
    "reward_stat": Null,  <<-- If you have multiple reward_columns, you can specify 'max | min | average'
                               <<-- If single column name, this value should be None
      }                                       
  }

python3 bin/optimize.py -c config.json -tr <your-dataset.csv>
```


##  Use your existing python3 setup 

Untar it and install requirements
```bash
tar -xvf  *VERIFAI.AI-1.5.tar.gz*
cd VERIFAI.AI-1.5
pip3 install -r requirements.txt
```
```bash
python3 signup.py
```

Run the sample optimize example Using

```bash
python3 optimize.py -c config.json -tr FIFO_knobs.csv
```


