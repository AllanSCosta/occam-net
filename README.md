


# OccamNet Implementation

The neural network architecture is specified in `neural_net.py`, and training is programmed in the file `train.py`. We run OccamNet through an "Experiment" interface, which is specified in a .json file present in the subdirectory `experiments`.

To install system requirements with conda, run:
```
conda env create -f environment.yml
```
Specifying the prefix in the last of the .yml file to properly set up where to install the dependencies (Note that installation will fail otherwise). More information can be found [here](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

Paper can be found [here](https://arxiv.org/abs/2007.10784) 


## Code



### Hyperparameters Specification

To run OccamNet, we specify a json file in the `experiments` folder as follows:


```
{
  "name": <string, name of experiment collection>,
  "collection": [
    {
      "name": <string, name of experiment>,
      "architecture": <string, default: "OccamNet", used for benchmarking against different architectures>,
      "bases": <array of strings corresponding to bases, eg: ["SINE", "ADDITION"]>,
      "constants": <array of strings corresponding to cosntants, eg: ["ONE", "PI"]>,
      "target_function": <string for the target function, eg: ["GRAVITATION"]> ,
      "domain_type": <"continuous", "discrete_range", "discrete_set">,
      "data_domain": <array specifying the domain of data input>,
      "recurrence_depth": <integer>,
      "recurrence_search": <boolean, considers all recurrence hidden states when considering updates>,
      "depth": <integer>,
      "repeat": <integer, how many times to run OccamNet with these hyperparameters>,
      "record": <boolean, specifies if video is to be saved>,
      "recording_rate": <integer, specifies how often we save frames for videos>,
      "dataset_size": <integer, number of data points to be sampled uniformly from the domain>,
      "batch_size": <integer>,
      "sampling_size": <integer>,
      "truncation_parameter": <integer>,
      "number_of_inputs": <integer>,
      "number_of_outputs": <integer>,
      "learning_rate": <float>,
      "variances": <float>,
      "temperature": <float, temperatura for all layers except last layer>,
      "last_layer_temperature": <float>,
      "epochs": <integer>,
      "skip_connections": <boolean, specifies if skip connections are used>,
      "visualization": <array of strings, possible options: "network", "loss", "expression", "image" for visualization>
    }
  ]
}

```



Note that the dataset is automatically generated using parameters from `domain`, `domain_type` and `dataset_size`.

The strings corresponding to different bases and constants, as well as targets, are defined in the files `bases.py` and `targets.py`, respectively. Note that specifying a new target function requires proper formatting for pytorch (with special attention to tensor shape). As an example, consider the function that takes the max of the inputs. In our implementation, it is specified in `targets.py` as:

```
MAX_FUNCTION = ['MAX_FUNCTION', lambda x: torch.max(x, dim=-1)[0].unsqueeze(-1)]
(...)
TARGET_SET = [ (...), MAX_FUNCTION,  (...)]
```



### Running Experiments

Once a hyperparameter json is specified, to run OccamNet use:

```
 python run_experiment.py --collection_name <collection_name>
```


We include some example experiments. In order to test them, run the above command with `<collection-name>` set to `example_analytic`, `example-inequality` or `example-implicit`


Once an experiment finishes running, if the json `record` parameter is set to `true`, the corresponding experiment's video will be saved to directory `experiments/video`.


## Videos

We include a folder with videos showing the evolution of different neural networks. Different videos show how OccamNet evolves for different function fitting tasks. Each video shows a number of plots. The topmost visualization shows the neural network, and the connections between images and arguments. Throughout training, it is possible to visualize how the network reaches sparsity and becomes interpretable. Some videos include a plot of the signal we aim to fit and the function resulting from the total expectation of our model. These are shown in black and green lines, respectively. For multi-input, multi-output problems, this last visualization is substituted by a dynamically changing equation corresponding to the most likely function to be sampled from the network. Finally, in some videos we also include the evolution of the fitness for the best sampled functions (denoted mean $$G$$) in the graph.
