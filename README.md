# SpatialCrafter: Single Image World Modeling with Generative 3D Proxies


<div align="center" style="margin-top: 0; padding-top: 0; line-height: 1;">
    <a href="https://fangchuan.github.io/SpatialCrafter" target="_blank" style="margin: 2px;"><img alt="Project"
    src="https://img.shields.io/badge/🌐%20Project-SpatialCrafter-ffc107?color=42a5f5&logoColor=white" style="display: inline-block; vertical-align: middle;"/></a>
    <a href="https://arxiv.org/abs/2608.27073" target="_blank" style="margin: 2px;"><img alt="arXiv"
    src="https://img.shields.io/badge/arXiv-SpatialCrafter-b31b1b?logo=arxiv&logoColor=white" style="display: inline-block; vertical-align: middle;"/></a>
    <a href="https://github.com/manycore-research/SpatialCrafter" target="_blank" style="margin: 2px;"><img alt="GitHub"
    src="https://img.shields.io/badge/GitHub-SpatialCrafter-24292e?logo=github&logoColor=white" style="display: inline-block; vertical-align: middle;"/></a>
    <a href="https://huggingface.co/manycore-research/SpatialCrafter" target="_blank" style="margin: 2px;"><img alt="Hugging Face"
    src="https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-SpatialCrafter-ffc107?color=ffc107&logoColor=white" style="display: inline-block; vertical-align: middle;"/></a>
</div>

<div align="center">

[Chuan Fang](https://fangchuan.github.io/), [Lingteng Qiu](https://lingtengqiu.github.io/), [Yixun Liang](https://yixunliang.github.io/), [Rui Chen](https://aruichen.github.io/), [Kunming Luo](https://coolbeam.github.io/), [Zhaohua Zheng](https://zhaohuazheng.github.io/), [Tongyuan Bai](https://tongyuanbai.github.io/), [Feipeng Tian](https://feipengtian.github.io/), [Zilong Dong](https://baike.baidu.com/item/%E8%91%A3%E5%AD%90%E9%BE%99/62931048), [Zihan Zhou](https://zihan-z.github.io), [Ping Tan](https://pingtan.people.ust.hk/index.html)

**[HKUST Spatial Artificial Intelligence Lab](https://github.com/HKUST-SAIL)**; **[Manycore Tech Inc](https://manycoretech.com/)**; **[Alibaba Group](https://www.alibabagroup.com/)**; **[Jilin University](https://www.jlu.edu.cn/)**;

</div>

<div align="center">

<video src="./assets/fast-forward-720p.mp4" width="100%" controls autoplay loop muted playsinline></video>

<p>TL;DR: Given a single image and a consecutive camera trajectory, SpatialCrafter builds generative 3D proxies from a single image to enable consistent, controllable video generation.</p>
</div>

## ✨ News

- [Oct, 2026] Initial release of SpatialCrafter!
- [Aug, 2026] Paper is released on arXiv!
- [Jul, 2026] SpatialCrafter is accepted to SIGGRAPH-Asia 2026!

## 📋 Release Plan

- [x] Inference code && Pre-trained model weights
- [x] Data-Engine code
- [ ] Training code

## SpatialCrafter Models

<div align="center">

|     **Model**      | **Download**                                                             |
| :----------------: | ------------------------------------------------------------------------ |
| SpatialCrafter-1.0 | [🤗 HuggingFace](https://huggingface.co/manycore-research/SpatialCrafter) |

</div>

## Usage

### 🔧 Installation

Tested with the following environment:
* Python 3.10
* PyTorch 2.5.1
* CUDA Version 12.4
* NVIDIA A100 and H20

```bash
# clone the repository
git clone https://github.com/fangchuan/SpatialCrafter.git
cd SpatialCrafter

python -m venv .venv
source .venv/bin/activate

pip install torch==2.5.1 torchvision==0.20.1 --index-url https://download.pytorch.org/whl/cu124
pip install --no-build-isolation -e .

```

### High quality 3D Scene Data Engine
Please refer to the [Data Engine documentation](./data_engine/README_en.md) for more details on how to produce the high quality 3D scene data.

### Inference

```bash
# download the weights of Wan2.1, DepthAnything V3
huggingface-cli download alibaba-pai/Wan2.1-Fun-V1.1-14B-Control --local-dir ./checkpoints/PAI/Wan2.1-Fun-V1.1-14B-Control
huggingface-cli download depth-anything/DA3NESTED-GIANT-LARGE-1.1 --local-dir ./checkpoints/DA3NESTED-GIANT-LARGE-1.1
export DIFFSYNTH_MODEL_BASE_PATH=$PWD/checkpoints
export DA3NESTED_MODEL_BASE_PATH=$PWD/checkpoints/DA3NESTED-GIANT-LARGE-1.1
# download the weights of TRELLIS-Scene model and SpatialCrafter
huggingface-cli download manycore-research/SpatialCrafter --local-dir ./checkpoints/SpatialCrafter

# TODO: Single image-to-RGBD Video generation
python scripts/infer_rgbd_video.py \
       --test_split_file test_data/tests.csv    \
       --ss_vae_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_ss_vae_64/config.json \
       --ss_dit_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_ss_dit_64/config.json \
       --slat_vae_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_slat_vae_64/config.json \
       --slat_dit_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_slat_dit_64/config.json \
       --wanvideo_ckpt_path ./checkpoints/SpatialCrafter/deferred_geo_refiner_rgbd.safetensors  \
       --depth_concat width_wise     \
       --video_size 512  \
       --output_path ./outputs_rgbd

# TODO: Single image-to-RGB-SCM (scene corrdinate map) Video generation
python scripts/infer_rgb_scm_video.py \
       --test_split_file test_data/tests.csv    \
       --ss_vae_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_ss_vae_64/config.json \
       --ss_dit_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_ss_dit_64/config.json \
       --slat_vae_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_slat_vae_64/config.json \
       --slat_dit_config_file ./checkpoints/SpatialCrafter/TRELLIS_Scene/sft_slat_dit_64/config.json \
       --wanvideo_ckpt_path ./checkpoints/SpatialCrafter/deferred_geo_refiner_rgbscm.safetensors  \
       --depth_concat width_wise     \
       --video_size 512  \
       --output_path ./outputs_rgb_scm
```

## License

This project is released under the [Apache License 2.0](LICENSE).


## Citation

```bibtex
@article{fang2026spatialcrafter,
  title={SpatialCrafter: Single Image World Modeling with Generative 3D Proxies},
  author={Fang, Chuan and Qiu, Lingteng and Liang, Yixun and Chen, Rui and Luo, Kunming and Zheng, Zhaohua and Bai, Tongyuan and Tian, Feipeng and Dong, Zilong and Zhou, Zihan and Tan, Ping},
  journal={arXiv preprint arXiv:2608.27073},
  year={2026}
}
```

## Announcement on Revision of TRELLIS and DiffSynth Codebase

The TRELLIS codebase used in this project have been revised from their original versions to better suit the 3D scene dataset, and DiffSynth-Studio has been revised for single image-to-RGBD and single image-to-RGB-SCM video generation tasks. These revisions include modifications to the model configurations and training codes to ensure compatibility and improved performance within the SpatialCrafter framework.

Please note that these revised models are intended for research purposes within the context of the SpatialCrafter project and may not reflect the original performance characteristics of the unmodified TRELLIS and WanVideo models.

## Acknowledgements

We thank the teams behind [TRELLIS](https://github.com/microsoft/TRELLIS) and [DiffSynth-Studio](https://github.com/modelscope/diffsynth-studio) for open-sourcing their models, and the authors of [SpatialGen](https://manycore-research.github.io/SpatialGen/) for the website template.