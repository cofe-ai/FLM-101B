### 中|[En](README.md)


# FLM-101B: An Open LLM and How to Train It with \$100K Budget

FLM-101B是一个开源的decoder-only架构的语言模型，参数规模101B。训练过程采用模型生长技术，通过训练前期在小规模模型上快速学习知识，后期将模型逐步生长成大模型的方式，实现了千亿规模模型的低成本(~$100K)训练。
FLM-101B支持中英双语，训练上下文窗口长度为2048，得益于使用了[xPos](https://arxiv.org/pdf/2212.10554.pdf)旋转位置编码，推理时窗口大小可进行良好的拓展。
为推动千亿规模LLM技术发展，FLM-101B现已全面开源。

作为FLM-101B开源计划的一部分，本项目是FLM-101B的训练框架。该框架的研发基于 [Meagtron-LM](https://github.com/NVIDIA/Megatron-LM)。在此基础上我们进行了一列该开发和优化，包括但不限于：加入[FreeLM](https://arxiv.org/pdf/2305.01616.pdf)模型的训练方法、[Loss Prediction](https://arxiv.org/abs/2304.06875)、[xPos](https://arxiv.org/pdf/2212.10554.pdf)，以及全新的可配置dataloader系统等等。

**我们准备了全面的代码存储库，这对于我们论文的主要观点非常重要。 推理代码已在 https://huggingface.co/CofeAI/FLM-101B 上发布，用于复现实验。 本代码仓（`FLM-101B`）是预训练代码。 我们计划在论文发表后公开完整的存储库。**

# 为什么使用FLM-101B

- 开源的千亿级中英双语模型
- 已知的最大规模的使用了xPos训练的语言模型
- 已知的最大规模的成功实现了μp transfer以及loss prediction的语言模型
- 已知的最大规模的成功实现了progressive learning with model growth的语言模型


# 并行策略 & 硬件浮点利用率
本训练框架支持我们论文中提到的三种参数规模的模型进行训练：16B、51B和101B。理论上可以支持TB级模型的训练（但收敛性未实验确认）。我们在24 x NVIDIA A800 GPUs的集群上进行了实际训练实验，下表是我们FLM-101B训练的并行策略方案及浮点运算效率。

| **Params(billion)** | **TP Size**  | **PP Size**  | **DP Size**  | **Number of GPUs**  | **Batch Size**  | **TFLOP/s per GPU**  | **GPU Utilization**  |
|---------------------|--------------|--------------|--------------|---------------------|-----------------|----------------------|----------------------|
| 16                  | 2            | 1            | 96           | 192                 | 2304            | 162                  | 51.90%               | 
| 51                  | 4            | 2            | 24           | 192                 | 2304            | 160                  | 51.30%               | 
| 101                 | 4            | 4            | 12           | 192                 | 2160            | 165                  | 52.88%               | 

# 系统需求

## 硬件要求

`FLM-101B`需要运行在 NVIDIA A100/A800 GPU 服务器上。一般来说，为了支持大模型训练，我们建议配置不低于：
* 集群节点总数: 16
* GPUs / node: 8
* 内存 / node: 1TB
* 卡间通讯方案: NVLink
* 节点间通讯方案: InfiniBand (400/800 Gb/s)

## 软件要求

### 系统要求

本仓库代码支持在Linux系统上运行。并在以下系统版本测试通过：
Linux: Ubuntu 20.04

### Python依赖

`FLM-101B` 的依赖安装: `requirements.txt` 和 [apex](https://github.com/NVIDIA/apex) 包.

# 安装指引

1. 安装 `apex`

```
git clone https://github.com/NVIDIA/apex
cd apex
pip install --global-option="--cpp_ext" --global-option="--cuda_ext" --no-cache -v --disable-pip-version-check .  2>&1 | tee build.log
```

2. 安装 `cofe-ai/FLM-101B`
```
git clone https://github.com/cofe-ai/FLM-101B.git
cd FLM-101B
pip install -r requirements.txt
```

# 重要的方法

- **[Extrapolatable Position Embedding (xPos)](https://arxiv.org/pdf/2212.10554.pdf)**
- **[Flash Attention (In Training)](https://arxiv.org/pdf/2205.14135.pdf)**
- **[Model Growth](https://arxiv.org/pdf/2305.02869.pdf)**
- **[Loss Prediction](https://arxiv.org/abs/2304.06875)**
- **[FreeLM Model](https://arxiv.org/pdf/2305.01616.pdf)**


# 引用

如何我们的工作对您产生了帮助，请引用FLM-101B的论文：
```
@article{flm-101b,
  author       = {Xiang Li and Yiqun Yao and Xin Jiang and Xuezhi Fang and Xuying Meng and
                  Siqi Fan and Peng Han and Jing Li and Li Du and Bowen Qin and Zheng Zhang and
                  Aixin Sun and Yequan Wang},
  title        = {FLM-101B: An Open LLM and How to Train It with \$100K Budget},
  year         = {2023}
}
```

您也可以同时包含引用早期的FreeLM相关研究工作：
```
@article{freelm,
  author       = {Xiang Li and Xin Jiang and Xuying Meng and Aixin Sun and Yequan Wang},
  title        = {FreeLM: Fine-Tuning-Free Language Model},
  year         = {2023},
  url          = {https://doi.org/10.48550/arXiv.2305.01616}
}
```

# 联系我们

tshwangyequan@gmail.com

