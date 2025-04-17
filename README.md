# DeepNP: Deep Learning-Based Noise Prediction for Ultra-Reliable Low-Latency Communications

This repository contains the implementation of **DeepNP** (Deep Learning-Based Noise Prediction), a novel approach that combines network coding with deep learning-based noise prediction for Ultra-Reliable Low-Latency Communications (URLLC).

## Variants

DeepNP comes in two variants:

1. **ER-DeepNP (Erasure-Rate DeepNP)**: 
   - Used as a noise predictor in the transport layer

2. **CL-DeepNP (Cross-Layer DeepNP)**:
   - Considers the relationship between signal-to-noise ratio (SNR) and physical layer code rate
   - Dynamically adjusts SNR thresholds during transmission

## Project Structure

```
├── Data/
├── Code/
  ├── main.py                # Main entry point for running the code
  ├── config/                
  │   └── config.py          # Configuration parameters
  └── utils/                 
      ├── config.py          # Handles config/config.py parameters 
  ├── BaseClasses/           # Base classes for simulation and training
  │   └── ModelBase.py       # Model training wrapper
  │   └── BaseProtocol.py    # Transmission simulation
  ├── acrlnc/                # Adaptive Causal RLNC implementation
  │   └── encoder.py         # Encoder - AC-RLNC window managemnet
  │   └── decoder.py         # Decoder - Counts Degrees of Freedom
  │   └── protocol.py        # Handles Encoder-Decoder Communicatoin
  ├── dataloader/            # Data loading and preprocessing
  ├── model/                 # Learning Model implementations
  │   ├── dnn_*.py           # Deep architecture for each case
  │   ├── model_*.py         # Model build and training
  │   └── loss_fn.py         # Loss function options
  └── full_system/           # System integration 
      ├── full_system.py     # Set erasure prediction method and proceed to run the simulation
      ├── rates.py           # SNR to rate conversion - and a soft diffrentiable surrogate function
      └── <other>.py         # Plot result
├── Results/                 # If does not exists, created during run
```

## Running the Code

### Configuration

1. Set parameters in `main.py`
2. Additional parameters can be configured in `config/config.py`

### Run Modes

The code supports three main run modes:

1. **Train** - Train a DeepNP model separate from the protocol procedure
   - Will force you to choose a new run name

2. **Test** - Run the protocol to test the model or other erasure prediction options
   - Select the relevant "eval_foldername" to evaluate the model

3. **Plot** - Visualize the results
   - Choose a plot name in "new_foldername"
   - A new folder with the same name plus 'PLOT' suffix will be created
   - Plotting files and other parametes (but not all) are configured in the plot files themselves

### Prediction Types

The following prediction types are available (`pred_type_list`):

- **Gini** - A "Genie" model that knows the actual noise realization
- **Stat** - Statistical estimation (mean over binary feedback information)
- **Model** - Deep learning model with the following options (`model_type_list`):
  - **Bin** - Binary input model
  - **Par** - SNR input model (in CL option, this is the "Parallel" option, determined by the number of sinr inputs in `sinr_th_list`)
  - **TH** - CL learning option, where the threshold is learned as part of the training

## Implementation Details

- **Simulation**: The simulation is index-based and assumes a fixed RTT
- **Data Processing**: The dataloader takes a series and cuts it into sub-series for supervised learning
- **Learning Models**:
  - All models are based on `BaseClasses/ModelBase.py` which handles loading data, choosing loss functions, plotting and saving
  - Different model variants are implemented in `model_*.py` files - builds the model from dnn_*.py and handels training.
  - Neural network architectures are defined in `dnn_*.py` files
- **Result Visualization**:
  - `SINRvsBIN_Paper.py` - ER-DeepNP results
  - `SINRvsBIN_Paper_Robust.py` - ER-DeepNP robustness results
  - `Plot_all_results_PARvsTH.py` - CL-DeepNP results

