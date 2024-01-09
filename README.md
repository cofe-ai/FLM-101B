### [中](README_zh.md)|En


# FLM-101B: An Open LLM and How to Train It with \$100K Budget

FLM-101B is an open-source decoder-only LLM with 101 billion parameters. During the training process, model growth technique was employed. The model rapidly acquires knowledge on a small-scale model(16B) in the early stages of training and gradually scales up to 101B, resulting in a cost-effective 100B-scale LLM training(costing approximately $100,000).
FLM-101B supports both Chinese and English languages. It has a context window length of 2048 in training. Thanks to the use of [xPos](https://arxiv.org/pdf/2212.10554.pdf) rotary position embedding, it allows for efficient expansion of the window size during inference.

To advance the development of 100B-scale Large Language Models (LLMs), FLM-101B has now been fully open-sourced.

As part of the FLM-101B open source project, this code repository is the training framework for FLM-101B. It is a detached fork of [Meagtron-LM](https://github.com/NVIDIA/Megatron-LM). On this basis, we have carried out some development and optimization, including but not limited to: adding [FreeLM](https://arxiv.org/pdf/2305.01616.pdf) model training method, [Loss Prediction](https://arxiv.org/abs/2304.06875), [xPos](https://arxiv.org/pdf/2212.10554.pdf), and a new configurable dataloader system, etc.

**We have prepared a comprehensive code repository that is essential to our paper's main claims. The reproduction code has been released at https://huggingface.co/CofeAI/FLM-101B. And this repository(`FLM-101B`) is the pre-training code. We intend to make the complete repository publicly accessible upon the publication of our paper.**

# Why use FLM-101B

- It's an open-source 100B scaled Chinese-English bilingual model.
- It's the largest known language model trained with xPos.
- It's the largest known language model that successfully implements μp transfer and loss prediction.
- It's the largest known language model that successfully implements progressive learning with model growth.


# Parallel Strategies & HFU(Hardware FLOPs Utilization)
This training framework supports training of models with three parameter sizes mentioned in our paper: 16B, 51B and 101B. Theoretically, it can support the training of TB-level models (but the convergence has not been confirmed experimentally). We conducted actual training experiments on a cluster of 24 x NVIDIA A800 GPUs. The following table shows our actual FLM-101B configured parallel strategy scheme and floating point computing efficiency.

| **Params(billion)** | **TP Size**  | **PP Size**  | **DP Size**  | **Number of GPUs**  | **Batch Size**  | **TFLOP/s per GPU**  | **GPU Utilization**  |
|---------------------|--------------|--------------|--------------|---------------------|-----------------|----------------------|----------------------|
| 16                  | 2            | 1            | 96           | 192                 | 2304            | 162                  | 51.90%               | 
| 51                  | 4            | 2            | 24           | 192                 | 2304            | 160                  | 51.30%               | 
| 101                 | 4            | 4            | 12           | 192                 | 2160            | 165                  | 52.88%               | 


# System Requirements

## Hardware requirements

`FLM-101B` requires to run on a NVIDIA-A100/A800-GPU-equipped cluster. Typically, in order to support large model training, we recommend that the configuration is no less than:

* Total number of cluster nodes: 16
* GPUs per node: 8
* Memory per node: 1TB
* Inter-GPU communication solution: NVLink
* Inter-Node communication solution: InfiniBand (400/800 Gb/s)

## Software requirements

### OS Requirements

This package is supported for Linux. The package has been tested on the following systems:
Linux: Ubuntu 20.04

### Python Dependencies

`FLM-101B` mainly depends on: `requirements.txt` and [apex](https://github.com/NVIDIA/apex) package.


# Installation Guide
1. Install `apex`

```
git clone https://github.com/NVIDIA/apex
cd apex
pip install --global-option="--cpp_ext" --global-option="--cuda_ext" --no-cache -v --disable-pip-version-check .  2>&1 | tee build.log
```

2. Install `cofe-ai/FLM-101B`
```
git clone https://github.com/cofe-ai/FLM-101B.git
cd FLM-101B
pip install -r requirements.txt
```

# Important Methods

- **[Extrapolatable Position Embedding (xPos)](https://arxiv.org/pdf/2212.10554.pdf)**
- **[Flash Attention (In Training)](https://arxiv.org/pdf/2205.14135.pdf)**
- **[Model Growth](https://arxiv.org/pdf/2305.02869.pdf)**
- **[Loss Prediction](https://arxiv.org/abs/2304.06875)**
- **[FreeLM Model](https://arxiv.org/pdf/2305.01616.pdf)**


# Citation

If you find our work useful, please consider citing FLM-101B:
```
@article{flm-101b,
  author       = {Xiang Li and Yiqun Yao and Xin Jiang and Xuezhi Fang and Xuying Meng and
                  Siqi Fan and Peng Han and Jing Li and Li Du and Bowen Qin and Zheng Zhang and
                  Aixin Sun and Yequan Wang},
  title        = {FLM-101B: An Open LLM and How to Train It with \$100K Budget},
  year         = {2023}
}
```

You may also consider FreeLM's original work in your reference:
```
@article{freelm,
  author       = {Xiang Li and Xin Jiang and Xuying Meng and Aixin Sun and Yequan Wang},
  title        = {FreeLM: Fine-Tuning-Free Language Model},
  year         = {2023},
  url          = {https://doi.org/10.48550/arXiv.2305.01616}
}
```

# Contact

tshwangyequan@gmail.com

# License

This project is covered under the Apache 2.0 License.