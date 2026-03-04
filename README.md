<h1 align="center">
OmniCT: Towards a Unified Slice-Volume LVLM for Comprehensive CT Analysis
</h1>

<div align="center">

Tianwei Lin<sup>1,2*</sup>, Zhongwei Qiu<sup>2,3,1*</sup>, Wenqiao Zhang<sup>1†</sup>, Jiang Liu<sup>1</sup>, Yihan Xie<sup>1</sup>, <br>
Mingjian Gao<sup>1</sup>, Zhenxuan Fan<sup>1</sup>, Zhaocheng Li<sup>1</sup>, Sijing Li<sup>1,2</sup>, Zhongle Xie<sup>1</sup>, <br>
Peng Lu<sup>1</sup>, Yueting Zhuang<sup>1</sup>, Ling Zhang<sup>2</sup>, Beng Chin Ooi<sup>1</sup>, Yingda Xia<sup>2</sup> <br>

<sup>1</sup>Zhejiang University <sup>2</sup>DAMO Academy, Alibaba Group <sup>3</sup>Hupan Lab
<br>

<a href='https://arxiv.org/abs/2602.16110'><img src='https://img.shields.io/badge/Paper-Arxiv-red'></a>
<a href='https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-3B'><img src='https://img.shields.io/badge/Model(3b)-Huggingface-yellow'></a>
<a href='https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-7B'><img src='https://img.shields.io/badge/Model(7b)-Huggingface-yellow'></a>
<a href='https://github.com/alibaba-damo-academy/OmniCT'><img src='https://img.shields.io/badge/Code-GitHub-green'></a>

</div>

## 🌟 Overview

**OmniCT** is a unified **Slice-Volume Large Vision-Language Model (LVLM)** designed for medical Computed Tomography (CT) scenarios. It supports both 2D slice-based inputs and 3D volume-based data, enabling comprehensive CT understanding and analysis within a unified framework.

<p align="center">
  <img src="assets/framework.png" alt="OmniCT Framework" width="90%">
</p>

This project provides:

- 🧠 **Models**: `OmniCT-3B`, `OmniCT-7B`
- 📦 **Dataset**: `MedEval-CT-Dataset` (1.7M carefully curated samples)

## 🔥 News
- **[2026.03.04]** We have released the full weights and projection-layer weights for [OmniCT-3B](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-3B) and [OmniCT-7B](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-7B).
- **[2026.01.26]** 🎉🎉🎉 [OmniCT](https://arxiv.org/abs/2602.16110) has been accepted by **ICLR 2026 as Poster** presentation.

**TODO:**
- [x] Release environment setup instructions  
- [x] Release training and inference code  
- [x] Release MedEval-CT (including `MedEval-CT-Dataset`)  
- [x] Release pre-trained projection weights and full OmniCT model checkpoints  

## 🛠️ Getting Started

This section provides instructions for environment setup, inference, and training.

> This repository serves as a demo homepage; the full code is available in [GitHub](https://github.com/alibaba-damo-academy/OmniCT).

### Step 1: Environment Setup

First, clone the repository:

```bash
git clone https://github.com/alibaba-damo-academy/OmniCT.git
cd OmniCT
```

Using uv for environment installation (Recommended)

```bash
uv python install 3.11
uv venv --python 3.11
uv sync
uv pip install -e . --no-deps
```

Or using Conda

```bash
conda create -n omnict python=3.11 -y
conda activate omnict
pip install -r requirements.txt
pip install -e . --no-deps
```

> 💡 We strongly recommend installing **Flash Attention** for optimal training and inference performance.

### Step 2. Model Weights Preparation

**2.1 Inference: download OmniCT weights from huggingface**

| Model | Link |
| - | - |
| `OmniCT-3B` | [Download](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-3B) |
| `OmniCT-7B` | [Download](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-7B) |

**2.2 Training from scratch: download initialization weights (ViT + LLM)**

| Component | Link |
| - | - |
| `google/siglip-so400m-patch14-384` | [Download](https://huggingface.co/google/siglip-so400m-patch14-384) |
| `Qwen/Qwen2.5-3B-Instruct` | [Download](https://huggingface.co/Qwen/Qwen2.5-3B-Instruct) |
| `Qwen/Qwen2.5-7B-Instruct` | [Download](https://huggingface.co/Qwen/Qwen2.5-7B-Instruct) |

**2.3 Training from pre-train stage (Optional)**

To bypass the pre-train stage, you may directly use our released projection weights:

| Model | Link |
| - | - |
| `OmniCT-3B-projection-weights` | [Download](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-3B/blob/main/projection-weights/model.safetensors) |
| `OmniCT-7B-projection-weights` | [Download](https://huggingface.co/Alibaba-DAMO-Academy/OmniCT-7B/blob/main/projection-weights/model.safetensors) |

## 🤖 Inference

After completing installation and weight preparation, run the following command for inference:

```bash
uv run python evaluation/infer.py \
  --model_name_or_path "/path/to/OmniCT_Weights/" \
  --vision_tower_name_or_path "google/siglip-so400m-patch14-384" \
  --training_stage "eval" \
  --bf16 "true" \
  --modality "2d" \  # Optional: 2d or 3d
  --question "Describe the CT image." \
  --image_path "/path/to/input_ct/"
```

Alternatively, configure parameters in `evaluation/infer.sh` and run:

```bash
uv run bash evaluation/infer.sh
```

## 📚 Training

Training consists of two stages:
- PT (Pre-train Stage)
- SFT (Supervised Fine-tuning Stage)

### 4.1 Data Preparation

We provide the following in [MedEval-CT-Dataset](https://huggingface.co/datasets/lintw/MedEval-CT):
- Pre-train data
- SFT data

After downloading, please configure image paths or perform preprocessing according to [`dataset_info.json`](datasets/dataset_info.json).

**Data Format Examples**

2D Data:

```json
{
    "messages": [
        {
            "content": "<|image2d_start|><|image2d|><|image2d_end|>\n<|organ_start|><|mask2d|><|organ_end|>\nCan you describe the CT image?",
            "role": "user"
        },
        {
            "content": "The CT image shows ...",
            "role": "assistant"
        }
    ],
    "image": "/path/to/image"
}
```

3D Data:

```json
{
    "messages": [
        {
            "content": "<|image3d_start|><|image3d|><|image3d_end|>\n<|organ_start|><|mask3d|><|organ_end|>\nCan you describe the CT image?",
            "role": "user"
        },
        {
            "content": "The CT image shows ...",
            "role": "assistant"
        }
    ],
    "image": "/path/to/image"
}
```

### 4.2 Single-Node Training

**Configuration files:**
- `configs/training_config/pt.json`
- `configs/training_config/sft.json`

**PT:**

```bash
uv run bash scripts/pt_single_node.sh
# or
uv run deepspeed \
src/omnict/train.py \
--config configs/training_config/pt.json \
--deepspeed \
--deepspeed_config configs/ds_config/zero2.json
```

**SFT:**

```bash
uv run bash scripts/sft_single_node.sh
# or
uv run deepspeed \
src/omnict/train.py \
--config configs/training_config/sft.json \
--deepspeed \
--deepspeed_config configs/ds_config/zero2.json
```

### 4.3 Multi-Node Training

**PT:**

```bash
uv run bash scripts/pt_multi_nodes.sh
# or
uv run torchrun \
--master_addr=$MASTER_ADDR \
--master_port=$MASTER_PORT \
--nproc_per_node=$NPROC_PER_NODE \
--nnodes=$WORLD_SIZE \
--node_rank=$RANK \
src/omnict/train.py \
--config configs/training_config/pt.json \
--deepspeed \
--deepspeed_config configs/ds_config/zero2.json
```

**SFT:**

```bash
uv run bash scripts/sft_multi_nodes.sh
# or
uv run torchrun \
--master_addr=$MASTER_ADDR \
--master_port=$MASTER_PORT \
--nproc_per_node=$NPROC_PER_NODE \
--nnodes=$WORLD_SIZE \
--node_rank=$RANK \
src/omnict/train.py \
--config configs/training_config/sft.json \
--deepspeed \
--deepspeed_config configs/ds_config/zero2.json
```

By default, checkpoints are saved to:

```bash
./checkpoint/<project>/<run_name>/
```

## 📑 Citation

If OmniCT is helpful to your research or work, please consider citing:

```bibtex
@article{lin2026omnict,
  title={OmniCT: Towards a Unified Slice-Volume LVLM for Comprehensive CT Analysis},
  author={Lin, Tianwei and Qiu, Zhongwei and Zhang, Wenqiao and Liu, Jiang and Xie, Yihan and Gao, Mingjian and Fan, Zhenxuan and Li, Zhaocheng and Li, Sijing and Xie, Zhongle and others},
  journal={arXiv preprint arXiv:2602.16110},
  year={2026}
}
```

## ⭐ Acknowledgement

We sincerely thank all researchers and open-source contributors advancing the field of medical multimodal understanding.

If you find our work useful, please consider giving us a ⭐ Star!
