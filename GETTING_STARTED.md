# UCNet Steganalysis - Getting Started Guide for Beginners

This guide will help beginners run the UCNet_Steganalysis project from scratch.

## Table of Contents
1. [Project Introduction](#project-introduction)
2. [System Requirements](#system-requirements)
3. [Environment Setup](#environment-setup)
4. [Dataset Preparation](#dataset-preparation)
5. [Running the Project](#running-the-project)
6. [Troubleshooting](#troubleshooting)

## Project Introduction

UCNet (Universal Color Network) is a deep learning network for color image steganalysis. This project is the PyTorch implementation of the paper "Universal Deep Network for Steganalysis of Color Image based on Channel Representation" (TIFS 2022).

The project can detect steganography in two domains:
- **Spatial Domain**: Using `UCNet_Spatial.py`
- **JPEG Domain**: Using `UCNet_JPEG.py`

## System Requirements

### Hardware Requirements
- **GPU**: NVIDIA GPU (GTX 1080 or better recommended)
- **RAM**: At least 16GB
- **Storage**: At least 50GB free space (for datasets)

### Software Requirements
- **Operating System**: Linux (Ubuntu 18.04/20.04 recommended) or Windows 10+
- **CUDA**: 10.2 or higher
- **cuDNN**: 7.4.1 or higher
- **Python**: 3.6.9 or higher (3.7-3.9 recommended)

## Environment Setup

### Step 1: Install Python

If Python is not installed, download it from: https://www.python.org/

Check Python version:
```bash
python3 --version
```

### Step 2: Install CUDA and cuDNN (Required for GPU Training)

1. Download CUDA Toolkit from NVIDIA: https://developer.nvidia.com/cuda-toolkit
2. Download cuDNN from NVIDIA: https://developer.nvidia.com/cudnn

Refer to NVIDIA's official documentation for detailed installation instructions.

### Step 3: Clone the Repository

```bash
git clone https://github.com/XiaopanZhang/UCNet_Steganalysis.git
cd UCNet_Steganalysis
```

### Step 4: Create a Python Virtual Environment (Recommended)

Using a virtual environment helps avoid dependency conflicts:

```bash
# Create virtual environment
python3 -m venv ucnet_env

# Activate virtual environment
# Linux/Mac:
source ucnet_env/bin/activate
# Windows:
ucnet_env\Scripts\activate
```

### Step 5: Install Dependencies

```bash
# Install all required Python packages
pip install -r requirements.txt

# For GPU support, install PyTorch with CUDA support matching your version
# CUDA 10.2:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu102

# CUDA 11.3:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu113

# CUDA 11.8:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

### Step 6: Verify Installation

Run the following Python code to verify PyTorch and CUDA are correctly installed:

```python
python3 -c "import torch; print('PyTorch version:', torch.__version__); print('CUDA available:', torch.cuda.is_available()); print('CUDA version:', torch.version.cuda if torch.cuda.is_available() else 'N/A')"
```

## Dataset Preparation

### ALASKA Dataset

This project uses the ALASKA dataset for training and testing.

1. **Download Dataset**: 
   - Visit the ALASKA challenge website: https://alaska.utt.fr/
   - Download the ALASKA_v2_TIFF_256_COLOR dataset
   - Extract to a directory, e.g., `/data/ALASKA_v2_TIFF_256_COLOR/`

2. **Prepare Stego Images**:
   - You need to generate stego images using steganography algorithms
   - Supported algorithms include:
     - Spatial domain: CMDC-HILL, CMDC-SUNIWARD, GINA-HILL, GINA-SUNIWARD
     - JPEG domain: Related methods
   - Steganography code must be obtained from respective paper authors (see `steganography methods/readme.txt`)

3. **Set Data Paths**:
   
   Edit `UCNet_Spatial.py` (or `UCNet_JPEG.py`) file, find lines 469-470:
   
   ```python
   ALASKA_COVER_DIR = '/path/xxx'
   ALASKA_STEGO_DIR = '/path/xxx_{}_{}/'.format(STEGANOGRAPHY, EMBEDDING_RATE)
   ```
   
   Modify to your actual paths:
   
   ```python
   ALASKA_COVER_DIR = '/data/ALASKA_v2_TIFF_256_COLOR/cover'
   ALASKA_STEGO_DIR = '/data/ALASKA_v2_TIFF_256_COLOR/stego_{}_{}/'.format(STEGANOGRAPHY, EMBEDDING_RATE)
   ```

### Data Index Files

The project includes data index files in the `index_list/` directory:
- `alaska_train_index_14000.npy`: Training set indices (14,000 images)
- `alaska_val_index_1000.npy`: Validation set indices (1,000 images)
- `alaska_test_index_5000.npy`: Test set indices (5,000 images)

These files specify randomly selected image numbers from the ALASKA dataset.

## Running the Project

### Basic Usage

#### Train Spatial Domain Steganalysis Model

Simplest example:

```bash
# Detect CMDC-HILL algorithm, embedding rate 0.4 bpp, using GPU #0
python3 UCNet_Spatial.py -alg CMDC-HILL -rate 0.4 -g 0
```

Parameter explanation:
- `-alg` or `--STEGANOGRAPHY`: Steganography algorithm
  - Options: `CMDC-HILL`, `CMDC-SUNIWARD`, `GINA-HILL`, `GINA-SUNIWARD`
- `-rate` or `--EMBEDDING_RATE`: Embedding rate
  - Options: `0.2`, `0.3`, `0.4`
- `-g` or `--gpuNum`: GPU number
  - Options: `0`, `1`, `2`, `3`
- `-l` or `--statePath`: (Optional) Load pretrained model path
- `-i` or `--DATASET_INDEX`: (Optional) Dataset index identifier

#### Train JPEG Domain Steganalysis Model

```bash
# Using JPEG domain model
python3 UCNet_JPEG.py -alg J-UNIWARD -rate 0.4 -g 0
```

### Using Pretrained Parameters

The project includes a pretrained parameter file `J-UNIWARD-pretrain-parameters.pt`, which is pretrained on a small ImageNet dataset.

To use pretrained parameters:

```bash
python3 UCNet_JPEG.py -alg J-UNIWARD -rate 0.4 -g 0 -l J-UNIWARD-pretrain-parameters.pt
```

### Training Output

During training, the following files will be generated:
- **Model parameters**: In `UCNet_Spatial/` or `UCNet_JPEG/` directory
  - Format: `{algorithm}-{rate}-{index}-params-lr={lr}.pt`
- **Training logs**: In the same directory
  - Format: `{algorithm}-{rate}-{index}-model_log-lr={lr}.log`

### Monitor Training Progress

Training logs will display in real-time:
- Current epoch
- Training loss
- Validation accuracy
- Test accuracy

Example output:
```
Epoch: [0][0/560]	Time 2.345 (2.345)	Data 0.123 (0.123)	Loss 0.6931 (0.6931)
...
Best Accuracy: 0.8567 (Epoch: 15)
```

## Troubleshooting

### 1. ModuleNotFoundError: No module named 'numpy'

**Problem**: Missing required Python packages

**Solution**:
```bash
pip install -r requirements.txt
```

### 2. RuntimeError: CUDA out of memory

**Problem**: Insufficient GPU memory

**Solution**:
- Reduce batch size: Edit `UCNet_Spatial.py` or `UCNet_JPEG.py`, modify `BATCH_SIZE` variable
  ```python
  BATCH_SIZE = 16  # Change to smaller value like 8 or 4
  ```
- Use smaller image size (not recommended, affects performance)

### 3. FileNotFoundError: [Errno 2] No such file or directory

**Problem**: Incorrect dataset path configuration

**Solution**:
- Check and modify `ALASKA_COVER_DIR` and `ALASKA_STEGO_DIR` paths
- Ensure image files exist in the specified paths
- Ensure image file format is correct (.ppm or other specified format)

### 4. ImportError: cannot import name 'all_normalized_hpf_list'

**Problem**: Cannot find SRM filter module

**Solution**:
- Ensure `High-pass filters/srm_kernel_filters.py` file exists
- Modify import path if needed:
  ```python
  # Add at the beginning of UCNet_Spatial.py
  import sys
  sys.path.append('High-pass filters')
  from srm_filter_kernel import all_normalized_hpf_list
  ```

### 5. CUDA not available

**Problem**: PyTorch cannot detect CUDA

**Solution**:
1. Check if NVIDIA driver is installed:
   ```bash
   nvidia-smi
   ```
2. Reinstall PyTorch with CUDA support:
   ```bash
   pip uninstall torch torchvision
   pip install torch torchvision --index-url https://download.pytorch.org/whl/cu102
   ```

### 6. Training is very slow

**Problem**: Training takes too long

**Solution**:
- Ensure using GPU instead of CPU
- Increase `num_workers` parameter (line 454 in code):
  ```python
  kwargs = {'num_workers': 4, 'pin_memory': True}  # Change to 4 or more
  ```
- Use less training data (modify index files)

### 7. No steganography algorithm code

**Problem**: Need steganography algorithms to generate stego images

**Solution**:
- Check papers listed in `steganography methods/readme.txt`
- Contact respective paper authors for code
- Or use publicly available steganography tools such as:
  - S-UNIWARD
  - WOW (Wavelet Obtained Weights)
  - HILL

## Advanced Usage

### Modify Network Hyperparameters

Main hyperparameters are defined at the beginning of the file:

```python
IMAGE_SIZE = 256          # Image size
BATCH_SIZE = 25           # Batch size
LR = 0.02                 # Learning rate
WEIGHT_DECAY = 5e-4       # Weight decay
TRAIN_FILE_COUNT = 14000  # Training file count
```

### Use Your Own Dataset

1. Prepare your image data (cover and stego image pairs)
2. Create index files (NumPy array format)
3. Modify the `MyDataset` class to adapt to your data format
4. Update data paths and index file paths

## Getting Help

If you encounter other issues:
1. Check the project's GitHub Issues: https://github.com/XiaopanZhang/UCNet_Steganalysis/issues
2. Contact the project author: weikk5@mail2.sysu.edu.cn
3. Read the original paper for more technical details

## Citation

If you use this project, please cite the original paper:
```
Universal Deep Network for Steganalysis of Color Image based on Channel Representation
IEEE Transactions on Information Forensics and Security (TIFS), 2022
```

## License

Please check the project's LICENSE file for terms of use.
