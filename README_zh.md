# OpenKBP 开放挑战赛

![](read-me-images/aapm.png)

_open-kbp_ 仓库提供了帮助参与者开发放射治疗剂量预测模型的代码，用于 OpenKBP 挑战赛。项目详情请参阅我们的[论文](https://aapm.onlinelibrary.wiley.com/doi/epdf/10.1002/mp.14845)。该仓库现已作为一个开放框架，供研究者开发剂量预测方法。注意：有兴趣开发自定义计划优化方法的研究者，请参考 [open-kbp-opt 仓库](https://github.com/ababier/open-kbp-opt)。

![](read-me-images/pipeline.png)

**建议**：该仓库既可在本地机器上使用，也可在云端（免费）通过 [Google Colab](https://colab.research.google.com) 运行。Google Colab 无需占用您现有硬件即可参与 OpenKBP 竞赛，它免费提供高质量的 CPU 和 GPU，但每次连续会话最长为 12 小时（[常见问题解答](https://research.google.com/colaboratory/faq.html)）。

## 引用

如需引用该数据集或代码仓库，请使用以下论文：

A. Babier, B. Zhang, R. Mahmood, K.L. Moore, T.G. Purdie, A.L. McNiven, T.C.Y. Chan, "[OpenKBP: The open-access knowledge-based planning grand challenge and dataset](https://aapm.onlinelibrary.wiley.com/doi/epdf/10.1002/mp.14845)," _Medical Physics_, Vol. 48, pp. 5549-5561, 2021.

# 目录

- [数据](#数据)
- [代码功能说明](#代码功能说明)
- [环境要求](#环境要求)
- [目录结构](#目录结构)
- [快速开始](#快速开始)
  + [在 Colab 中运行](#在-colab-中运行)
  + [在本地机器上运行](#在本地机器上运行)
- [运行代码](#运行代码)
  + [在 Colab 中](#在-colab-中)
  + [在本地机器上](#在本地机器上)
- [竞赛结果](#竞赛结果)
  + [第一名](#第一名)
  + [亚军](#亚军)
  + [最终测试阶段排行榜](#最终测试阶段排行榜)
- [使用 OpenKBP 数据集的研究示例](#使用-openkbp-数据集的研究示例)
- [竞赛组织者](#竞赛组织者)

## 数据

数据详情见论文 [OpenKBP: The open-access knowledge-based planning grand challenge and dataset](https://aapm.onlinelibrary.wiley.com/doi/epdf/10.1002/mp.14845)。简而言之，我们提供了 340 名接受调强放射治疗（IMRT）的头颈部癌症患者数据。数据集分为训练集（*n*=200）、验证集（*n*=40）和测试集（*n*=100）。每位患者的数据均包含剂量分布、CT 图像、结构掩膜、可行剂量掩膜（即剂量可以非零的区域掩膜）以及体素尺寸。

## 代码功能说明

该代码将训练一个小型神经网络用于剂量预测。运行 _main\_notebook.ipynb_ 和 _main.py_ 需要以下五个 PY 文件。以下是每个文件功能的简要说明（文件内部有更详细的说明）：

- _data_loader.py_：包含 _DataLoader_ 类，以标准格式从数据集中加载数据。提供多种数据格式（如剂量体积直方图），以适应不同的建模技术。
- _dose_evaluation_class.py_：包含 _EvaluateDose_ 类，用于计算竞赛评估指标。
- _general_functions.py_：包含多种用途的通用函数。
- _network_architectures.py_：包含 _DefineDoseFromCT_ 类，构建基础 U-Net 模型的网络架构。该类由 _PredictionModel_ 类继承。请注意，我们有意使用了一个**非**最先进的网络架构，仅作为占位符供您替换为更先进的模型。
- _network_functions.py_：包含 _PredictionModel_ 类，对 _DefineDoseFromCT_ 中构建的模型应用一系列方法。

## 环境要求

以下是运行该 Notebook 的基本要求（您也可以根据需要使用其他硬件或软件）。

### 在 Google Colab 上运行

- 标准 Google 账户

### 在本地机器上运行

- Linux
- Python 3.10.9
- 带有 CUDA 和 CuDNN 的 NVIDIA GPU（推荐）

## 目录结构

本仓库将在名为 _open-kbp_ 的目录下创建如下文件结构。_results_ 目录用于存储模型预测结果和模型文件（此示例中模型名为 baseline）。OpenKBP 竞赛的所有数据（含原始训练/验证/测试划分）存放在 _provided-data_ 目录下。代码还会创建 _submissions_ 目录，用于存放可提交至 CodaLab 排行榜的 zip 文件。目录结构参考如下（运行代码后会自动创建）：

```
open-kbp
├── provided-data
│   ├── train-pats
│   │   ├── pt_*
│   │       ├── *.csv
│   ├── valid-pats
│   │   ├── pt_*
│   │       ├── *.csv
│   └── test-pats
│       ├── pt_*
│           ├── *.csv
├── results
│   ├── baseline
│   │   ├── models
│   │   │   ├── epoch_*.h5
│   │   ├── validation-predictions
│   │   │   ├── pt_*.csv
│   │   └── test-predictions
│   │       ├── pt_*.csv
│   ├── **新建模型时结构重复**
└── submissions
    ├── baseline.zip
    ├── **新建模型时结构重复**
```

## 快速开始

以下提供了在 Google Colab 和本地机器上配置该仓库的说明。

### 在 Colab 中运行

这是参与 OpenKBP 竞赛最简便的方式，所需的剂量预测软件均安装在云端，无需昂贵硬件。

1. 打开 [Colab](https://colab.research.google.com)
2. 选择"GitHub" &rarr; 粘贴 [`main_notebook.ipynb`](https://github.com/ababier/open-kbp/blob/main/main_notebook.ipynb) 的链接 &rarr; 按 ENTER &rarr; 点击文件名
3. 在 Google Colab 工具栏中选择：Runtime &rarr; Change Runtime，在弹窗中确认 Runtime type 为 Python 3，Hardware accelerator 为 GPU。

完成上述步骤后即可运行代码。

### 在本地机器上运行

1. 创建虚拟环境并激活：
    ```
    virtualenv -p python3 open-kbp-venv
    source open-kbp-venv/bin/activate
    ```
2. 克隆本仓库，进入目录并安装依赖。注意，若要在 GPU 上运行 TensorFlow 2.1，可能需要从源码编译，官方说明见[此处](https://www.tensorflow.org/install/source)，也可参考第三方教程（[链接](https://gist.github.com/kmhofmann/e368a2ebba05f807fa1a90b3bf9a1e03)）。

    ```
    git clone https://github.com/ababier/open-kbp
    cd open-kbp
    pip3 install -r requirements.txt
    ```

## 运行代码

无论在哪个平台上运行，操作都很简单。如遇报错，通常是由于数据目录不正确。代码运行正常时，神经网络的训练进度将输出到 Colab 的输出单元格或命令行中。

### 在 Colab 中

在 Google Colab 工具栏中选择：Runtime > Run all；也可使用快捷键 <Ctrl+F9>。

**或者**

逐个点击每个单元格的运行按钮；也可使用 <Shift+Enter> 运行当前单元格。

### 在本地机器上

在虚拟环境中运行主文件：

```
python3 main.py
```

也可在 Jupyter Notebook 或 Jupyter Lab 中本地运行 Notebook，但需注释掉与 Google Drive 相关的命令，并修改提供数据的存储路径和结果保存路径。

## 竞赛结果

OpenKBP 挑战赛共吸引了来自 28 个国家的 195 名参赛者。竞赛于 2020 年 2 月 21 日开始，2020 年 6 月 1 日结束。44 支团队（共 73 人）在验证阶段提交了 1750 次提交，28 支团队（共 54 人）参加了测试阶段。顶级团队如下所示。**注意：验证集和测试集的剂量数据于竞赛结束后的 2020 年 6 月 23 日才公开发布。**

### 第一名

**剂量与 DVH 赛道**：刘硕林、张晶晶、李腾、闫慧、刘建飞，*LSL 安徽大学团队*，安徽大学，中国。[\[GitHub 仓库\]](https://github.com/LSL000UD/RTDosePrediction) [\[论文\]](https://aapm.onlinelibrary.wiley.com/doi/full/10.1002/mp.15034)

### 亚军

**剂量赛道**：Mary P. Gronberg, Skylar S. Gay, Tucker J. Netherton, Dong Joo Rhee, Laurence E. Court, Carlos E. Cardenas，*SuperPod*，MD Anderson 癌症中心，美国。[\[论文\]](https://aapm.onlinelibrary.wiley.com/doi/full/10.1002/mp.14827)

**DVH 赛道**：Lukas Zimmermann, Erik Faustmann, Christian Ramsl, Dietmar Georg, Gerd Heilemann，*PTV - Prediction Team Vienna*，维也纳医科大学，奥地利。[\[论文\]](https://aapm.onlinelibrary.wiley.com/doi/full/10.1002/mp.14774)

### 最终测试阶段排行榜

该排行榜为本次挑战赛的最终结果，是首次对多机构 KBP 方法实现进行受控盲测的测试。

![](read-me-images/final_leaderboard.png)

研究者仍可在 [CodaLab](https://competitions.codalab.org/competitions/?q=openkbp) 上注册并提交至实时排行榜。但**由于结果不再是盲测，无法保证验证集和测试集按预期使用（即不提前查看测试数据）**。

## 使用 OpenKBP 数据集的研究示例

- D. Nguyen, A.S. Barkousaraie, G. Bohara, A. Balagopal, R. McBeth, M. Lin, S. Jiang, "[A comparison of Monte Carlo dropout and bootstrap aggregation on the performance and uncertainty estimation in radiation therapy dose prediction with deep learning neural networks](https://iopscience.iop.org/article/10.1088/1361-6560/abe04f)," _Physics in Medicine & Biology_, Vol. 60, p. 054002, 2021.
- I. Isler, C. Lisle, J. Rineer, P. Kelly, D. Turgut, J. Ricci, U. Bagci, "[Enhancing Organ at Risk Segmentation with Improved Deep Neural Networks](https://arxiv.org/abs/2202.01866)," arXiv:2202.01866, 2022.

## 竞赛组织者

OpenKBP 由 Aaron Babier、Binghao Zhang、Rafid Mahmood 和 Timothy Chan（加拿大多伦多大学）；Andrea McNiven 和 Thomas Purdie（加拿大玛格丽特公主癌症中心）；Kevin Moore（美国加州大学圣地亚哥分校）共同组织。本次挑战赛得到[美国医学物理师协会（AAPM）](https://www.aapm.org/GrandChallenge/OpenKBP/)的支持。
