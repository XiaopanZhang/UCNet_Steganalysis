# UCNet_Steganalysis

This is the PyTorch implementation of the paper "Universal Deep Network for Steganalysis of Color Image based on Channel Representation", TIFS 2022. 

## 📚 Documentation

**New to this project?** We have comprehensive guides to help you get started:

- **🇨🇳 中文新手入门指南**: [GETTING_STARTED_CN.md](GETTING_STARTED_CN.md) - 详细的中文教程
- **🇬🇧 English Beginner's Guide**: [GETTING_STARTED.md](GETTING_STARTED.md) - Detailed English tutorial

These guides include:
- Step-by-step environment setup
- Dataset preparation instructions
- How to run training and testing
- Troubleshooting common issues

## Quick Start

### Requirements
- CUDA (10.2+)
- cuDNN (7.4.1+)
- Python (3.6.9+)

### Installation

```bash
# Clone the repository
git clone https://github.com/XiaopanZhang/UCNet_Steganalysis.git
cd UCNet_Steganalysis

# Install dependencies
pip install -r requirements.txt
```

### Basic Usage

"UCNet_Spatial.py" and "UCNet_JPEG.py" are the main programs in spatial and JPEG domain, respectively. 

**Example**: If you want to detect CMDC-HILL steganography method at 0.4 bpc (on GPU #1):

```bash
python3 UCNet_Spatial.py -alg CMDC-HILL -rate 0.4 -g 1
```

## Project Structure

- **High-pass filters/**: Contains the 30 SRM filters
- **J-UNIWARD-pretrain-parameters.pt**: Pretrained parameters on a small ImageNet dataset (training/validation: 190,000/8,820 images) with J-UNIWARD steganography
- **index_list/**: Dataset index files for train/validation/test splits
- **Selected dataset/**: Dataset configuration files
- **steganography methods/**: Documentation about steganography algorithms

## Contact

If you have any questions, please contact: weikk5@mail2.sysu.edu.cn

## Citation

If you use this project, please cite our paper:
```
Universal Deep Network for Steganalysis of Color Image based on Channel Representation
IEEE Transactions on Information Forensics and Security (TIFS), 2022
```
