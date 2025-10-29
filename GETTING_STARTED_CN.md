# UCNet 隐写分析 - 新手入门指南

本指南将帮助初学者从零开始运行 UCNet_Steganalysis 项目。

## 目录
1. [项目简介](#项目简介)
2. [系统要求](#系统要求)
3. [环境安装](#环境安装)
4. [数据集准备](#数据集准备)
5. [运行项目](#运行项目)
6. [常见问题](#常见问题)

## 项目简介

UCNet (Universal Color Network) 是一个用于彩色图像隐写分析的深度学习网络。本项目是论文 "Universal Deep Network for Steganalysis of Color Image based on Channel Representation" (TIFS 2022) 的 PyTorch 实现。

该项目可以检测两种域的隐写术：
- **空间域 (Spatial Domain)**: 使用 `UCNet_Spatial.py`
- **JPEG 域 (JPEG Domain)**: 使用 `UCNet_JPEG.py`

## 系统要求

### 硬件要求
- **GPU**: NVIDIA GPU (推荐 GTX 1080 或更好)
- **内存**: 至少 16GB RAM
- **硬盘**: 至少 50GB 可用空间（用于数据集）

### 软件要求
- **操作系统**: Linux (Ubuntu 18.04/20.04 推荐) 或 Windows 10+
- **CUDA**: 10.2 或更高版本
- **cuDNN**: 7.4.1 或更高版本
- **Python**: 3.6.9 或更高版本（建议 3.7-3.9）

## 环境安装

### 步骤 1: 安装 Python

如果还没有安装 Python，请从官网下载安装：https://www.python.org/

检查 Python 版本：
```bash
python3 --version
```

### 步骤 2: 安装 CUDA 和 cuDNN（仅 GPU 训练需要）

1. 从 NVIDIA 官网下载 CUDA Toolkit: https://developer.nvidia.com/cuda-toolkit
2. 从 NVIDIA 官网下载 cuDNN: https://developer.nvidia.com/cudnn

详细安装教程可参考 NVIDIA 官方文档。

### 步骤 3: 克隆项目

```bash
git clone https://github.com/XiaopanZhang/UCNet_Steganalysis.git
cd UCNet_Steganalysis
```

### 步骤 4: 创建 Python 虚拟环境（推荐）

使用虚拟环境可以避免依赖冲突：

```bash
# 创建虚拟环境
python3 -m venv ucnet_env

# 激活虚拟环境
# Linux/Mac:
source ucnet_env/bin/activate
# Windows:
ucnet_env\Scripts\activate
```

### 步骤 5: 安装依赖包

```bash
# 安装所有需要的 Python 包
pip install -r requirements.txt

# 如果需要 GPU 支持，根据你的 CUDA 版本安装对应的 PyTorch
# CUDA 10.2:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu102

# CUDA 11.3:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu113

# CUDA 11.8:
pip install torch torchvision --index-url https://download.pytorch.org/whl/cu118
```

### 步骤 6: 验证安装

运行以下 Python 代码验证 PyTorch 和 CUDA 是否正确安装：

```python
python3 -c "import torch; print('PyTorch version:', torch.__version__); print('CUDA available:', torch.cuda.is_available()); print('CUDA version:', torch.version.cuda if torch.cuda.is_available() else 'N/A')"
```

## 数据集准备

### ALASKA 数据集

本项目使用 ALASKA 数据集进行训练和测试。

1. **下载数据集**: 
   - 访问 ALASKA 挑战网站: https://alaska.utt.fr/
   - 下载 ALASKA_v2_TIFF_256_COLOR 数据集
   - 下载后解压到一个目录，例如 `/data/ALASKA_v2_TIFF_256_COLOR/`

2. **准备隐写图像**:
   - 你需要使用隐写算法生成隐写图像
   - 支持的算法包括：
     - 空间域：CMDC-HILL, CMDC-SUNIWARD, GINA-HILL, GINA-SUNIWARD
     - JPEG 域：相关方法
   - 隐写算法代码需要从相应论文作者处获取（见 `steganography methods/readme.txt`）

3. **设置数据路径**:
   
   编辑 `UCNet_Spatial.py`（或 `UCNet_JPEG.py`）文件，找到第 469-470 行：
   
   ```python
   ALASKA_COVER_DIR = '/path/xxx'
   ALASKA_STEGO_DIR = '/path/xxx_{}_{}/'.format(STEGANOGRAPHY, EMBEDDING_RATE)
   ```
   
   修改为你的实际路径：
   
   ```python
   ALASKA_COVER_DIR = '/data/ALASKA_v2_TIFF_256_COLOR/cover'
   ALASKA_STEGO_DIR = '/data/ALASKA_v2_TIFF_256_COLOR/stego_{}_{}/'.format(STEGANOGRAPHY, EMBEDDING_RATE)
   ```

### 数据索引文件

项目已包含数据索引文件在 `index_list/` 目录下：
- `alaska_train_index_14000.npy`: 训练集索引（14,000 张图像）
- `alaska_val_index_1000.npy`: 验证集索引（1,000 张图像）
- `alaska_test_index_5000.npy`: 测试集索引（5,000 张图像）

这些文件指定了从 ALASKA 数据集中随机选择的图像编号。

## 运行项目

### 基本使用

#### 训练空间域隐写分析模型

最简单的运行示例：

```bash
# 检测 CMDC-HILL 算法，嵌入率 0.4 bpp，使用 GPU #0
python3 UCNet_Spatial.py -alg CMDC-HILL -rate 0.4 -g 0
```

参数说明：
- `-alg` 或 `--STEGANOGRAPHY`: 隐写算法
  - 可选值：`CMDC-HILL`, `CMDC-SUNIWARD`, `GINA-HILL`, `GINA-SUNIWARD`
- `-rate` 或 `--EMBEDDING_RATE`: 嵌入率
  - 可选值：`0.2`, `0.3`, `0.4`
- `-g` 或 `--gpuNum`: GPU 编号
  - 可选值：`0`, `1`, `2`, `3`
- `-l` 或 `--statePath`: （可选）加载预训练模型路径
- `-i` 或 `--DATASET_INDEX`: （可选）数据集索引标识

#### 训练 JPEG 域隐写分析模型

```bash
# 使用 JPEG 域模型
python3 UCNet_JPEG.py -alg J-UNIWARD -rate 0.4 -g 0
```

### 使用预训练参数

项目包含一个预训练参数文件 `J-UNIWARD-pretrain-parameters.pt`，这是在小型 ImageNet 数据集上预训练的结果。

要使用预训练参数：

```bash
python3 UCNet_JPEG.py -alg J-UNIWARD -rate 0.4 -g 0 -l J-UNIWARD-pretrain-parameters.pt
```

### 训练输出

训练时会生成以下文件：
- **模型参数**: `UCNet_Spatial/` 或 `UCNet_JPEG/` 目录下
  - 格式：`{算法名}-{嵌入率}-{数据集索引}-params-lr={学习率}.pt`
- **训练日志**: 同一目录下
  - 格式：`{算法名}-{嵌入率}-{数据集索引}-model_log-lr={学习率}.log`

### 监控训练进度

训练日志会实时显示：
- 当前 epoch
- 训练损失
- 验证准确率
- 测试准确率

示例输出：
```
Epoch: [0][0/560]	Time 2.345 (2.345)	Data 0.123 (0.123)	Loss 0.6931 (0.6931)
...
Best Accuracy: 0.8567 (Epoch: 15)
```

## 常见问题

### 1. ModuleNotFoundError: No module named 'numpy'

**问题**: 缺少必要的 Python 包

**解决方案**:
```bash
pip install -r requirements.txt
```

### 2. RuntimeError: CUDA out of memory

**问题**: GPU 内存不足

**解决方案**:
- 减小 batch size: 编辑 `UCNet_Spatial.py` 或 `UCNet_JPEG.py`，修改 `BATCH_SIZE` 变量
  ```python
  BATCH_SIZE = 16  # 改为更小的值，如 8 或 4
  ```
- 使用更小的图像尺寸（不推荐，会影响性能）

### 3. FileNotFoundError: [Errno 2] No such file or directory

**问题**: 数据集路径设置不正确

**解决方案**:
- 检查并修改 `ALASKA_COVER_DIR` 和 `ALASKA_STEGO_DIR` 路径
- 确保路径中的图像文件存在
- 确保图像文件格式正确（.ppm 或其他指定格式）

### 4. ImportError: cannot import name 'all_normalized_hpf_list'

**问题**: 找不到 SRM 滤波器模块

**解决方案**:
- 确保 `High-pass filters/srm_kernel_filters.py` 文件存在
- 修改导入路径（如果需要）：
  ```python
  # 在 UCNet_Spatial.py 开头添加
  import sys
  sys.path.append('High-pass filters')
  from srm_filter_kernel import all_normalized_hpf_list
  ```

### 5. CUDA not available

**问题**: PyTorch 没有检测到 CUDA

**解决方案**:
1. 检查 NVIDIA 驱动是否安装：
   ```bash
   nvidia-smi
   ```
2. 重新安装支持 CUDA 的 PyTorch：
   ```bash
   pip uninstall torch torchvision
   pip install torch torchvision --index-url https://download.pytorch.org/whl/cu102
   ```

### 6. 训练速度很慢

**问题**: 训练时间过长

**解决方案**:
- 确保使用 GPU 而不是 CPU
- 增加 `num_workers` 参数（在代码第 454 行）：
  ```python
  kwargs = {'num_workers': 4, 'pin_memory': True}  # 改为 4 或更多
  ```
- 使用更少的训练数据（修改索引文件）

### 7. 没有隐写算法代码

**问题**: 需要隐写算法生成隐写图像

**解决方案**:
- 查看 `steganography methods/readme.txt` 中列出的论文
- 联系相应论文作者获取代码
- 或者使用公开可用的隐写工具，如：
  - S-UNIWARD
  - WOW (Wavelet Obtained Weights)
  - HILL

## 进阶使用

### 修改网络超参数

主要超参数在文件开头定义：

```python
IMAGE_SIZE = 256          # 图像尺寸
BATCH_SIZE = 25           # 批次大小
LR = 0.02                 # 学习率
WEIGHT_DECAY = 5e-4       # 权重衰减
TRAIN_FILE_COUNT = 14000  # 训练文件数
```

### 使用自己的数据集

1. 准备你的图像数据（cover 和 stego 图像对）
2. 创建索引文件（NumPy 数组格式）
3. 修改 `MyDataset` 类以适配你的数据格式
4. 更新数据路径和索引文件路径

## 获取帮助

如果遇到其他问题：
1. 查看项目的 GitHub Issues: https://github.com/XiaopanZhang/UCNet_Steganalysis/issues
2. 联系项目作者: weikk5@mail2.sysu.edu.cn
3. 阅读原始论文了解更多技术细节

## 引用

如果你使用本项目，请引用原始论文：
```
Universal Deep Network for Steganalysis of Color Image based on Channel Representation
IEEE Transactions on Information Forensics and Security (TIFS), 2022
```

## 许可证

请查看项目的 LICENSE 文件了解使用条款。
