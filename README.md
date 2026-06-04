# Multi-Modal Whole-Body Control

> **M3imic: Learning a Versatile Whole-Body Controller for Multimodal Motion Mimicking**  
> Zuxing Lu, Ziang Zheng, Yao Lyu, Jingyu Liu, Feihong Zhang, Song Lu, Xin Yuan, Changyin Sun⁺, Xingxing Zuo⁺, Shengbo Eben Li⁺  

[![License: BSD-3-Clause](https://img.shields.io/badge/License-BSD--3--Clause-blue.svg)](./LICENSE)
![Python 3.10](https://img.shields.io/badge/python-3.10-blue.svg)
![OS Linux](https://img.shields.io/badge/OS-Linux-green.svg)
![Isaac Sim 4.5](https://img.shields.io/badge/IsaacSim-4.5.0-ff6f00.svg)
![Isaac Lab 2.1.1](https://img.shields.io/badge/IsaacLab-2.1.1-3b5cff.svg)
![RSL--RL](https://img.shields.io/badge/RSL--RL-integrated-orange.svg)
[![arXiv](https://img.shields.io/badge/arXiv-2606.04829-b31b1b.svg?logo=arxiv)](https://arxiv.org/abs/2606.04829)

<p align="center">
  <img src="docs/illustrations/mmwbc.png" alt="Multi-Modal Whole-Body Control" width="80%" />
</p>

**Multi-Modal Whole-Body Control** is a research-oriented framework for learning **whole-body control policies** for humanoid and general articulated robots from **heterogeneous motion signals**.  
Built on **NVIDIA Isaac Sim / Isaac Lab** and **RSL-RL**, the framework targets **large-scale parallel simulation** and **multi-modal imitation learning**, with a focus on *robust motion tracking* and *cross-modal embodiment*.

The core philosophy of this repository is to treat **whole-body control as a multi-modal sequence alignment problem**:  
a robot policy learns to coordinate its full-body dynamics by jointly conditioning on robot-centric states and external motion descriptors such as **human body pose (SMPL-X)** and **SE(3) keypoints**.

The framework currently focuses on **Unitree G1**, but is designed to be extensible to other humanoid or whole-body platforms.

## News
- (2026-06-03) Our work has been accepted by IEEE Robotics and Automation Letters (RA-L).

## TODO List

- [ ] mjlab version of M3imic.
- [ ] other optional modality versions of M3imic.
- [ ] enhanced version.

## Key Ideas

- **Whole-body control at scale**  
  Designed for thousands of parallel environments in Isaac Lab, enabling fast and stable on-policy learning.

- **Multi-modal motion supervision**  
  Policies can be conditioned on:
  - robot joint trajectories,
  - human SMPL-X full-body motion,
  - 3D / SE(3) keypoint trajectories.

- **Unified tracking & imitation**  
  Motion tracking, multi-motion training, and GAE-Mimic–style imitation are expressed within a single task framework.

### Demo

See `docs/illustrations/demo.mp4`

<p align="center">
  <video src="docs/illustrations/demo.mp4" controls width="640">
    Your browser does not support the video tag. You can download the video from
    <a href="docs/illustrations/demo.mp4">docs/illustrations/demo.mp4</a>.
  </video>
</p>


## Repository Structure

<details>
<summary><strong>Click to expand repository structure</strong></summary>

```

.
├── source/whole_body_control
│   ├── robots/              # Robot models and actuators (e.g., Unitree G1)
│   ├── tasks/               # Tracking / Multi-Tracking / GAEMimic tasks
│   ├── utils/               # Motion datasets, dataloaders, runners
│
├── scripts
│   ├── rsl_rl/
│   │   ├── train.py         # Training entry point
│   │   └── play.py          # Policy rollout (if available)
│   ├── tools/               # Env listing, random / zero agents
│   └── data/                # Dataset preprocessing utilities
│
├── third_party/rsl_rl       # Vendorized RSL-RL
├── docs/env_setup.md        # Detailed environment setup
└── README.md

```

</details>



## Tasks and Environments

<details>
<summary><strong>Click to expand tasks and environments</strong></summary>

All environments are implemented using **Isaac Lab task abstractions**.

### Motion Tracking

- **`TrackingEnvCfg`**
  - Single reference motion tracking
  - Joint position control
  - Dense rewards on pose, velocity, orientation, contacts
  - Privileged observations for the critic

### Multi-Motion Tracking

- **`MultiTracking_TrackingEnvCfg`**
  - Samples from multiple motion clips
  - Dataset-driven command interface
  - Performance-based curriculum over motion difficulty

### Multi-Modal Imitation (GAE-Mimic)

- **`GAEMimic_TrackingEnvCfg`**
  - Conditions policy on:
    - robot joint trajectories,
    - SMPL-X human body pose,
    - SE(3) keypoint trajectories
  - Designed for cross-modal imitation and embodiment transfer

</details>



## Robot Model

<details>
<summary><strong>Click to expand robot model</strong></summary>

### Unitree G1

Defined in `whole_body_control/robots/g1.py` using:

- `ArticulationCfg`
- `ImplicitActuatorCfg`

Includes:
- Full-body joint limits and initial posture
- Per-joint-group actuator stiffness and damping
- Action scaling for stable RL control

</details>


## Installation

<details>
<summary><strong>Click to expand installation steps</strong></summary>

```bash
# Install Isaac Lab
git clone https://github.com/isaac-sim/IsaacLab.git
cd IsaacLab
git checkout 90b79bb2d44feb8d833f260f2bf37da3487180ba
./isaaclab.sh -i

# (Optional) install RSL-RL
./isaaclab.sh -p -m pip install -e path/to/rsl_rl

# Install this repository
cd source/whole_body_control
pip install -e .
```

</details>


### Dataset Download

<details>
<summary><strong>Click to expand dataset download</strong></summary>

Preprocessed datasets (with SMPL-X and keypoints) are available at:

* [https://www.modelscope.cn/datasets/seulzx/gae_mimic_dataset](https://www.modelscope.cn/datasets/seulzx/gae_mimic_dataset)

Unzip under the `datasets/` directory.

</details>

## Quick Start

<details>
<summary><strong>Click to expand quick start</strong></summary>

### List Available Environments

```bash
python scripts/tools/list_envs.py
```

### Train a Policy

```bash
python scripts/rsl_rl/train.py \
  --headless \
  --task MultiTracking-Flat-G1-v0
```

Common options:

* `--num_envs`
* `--seed`
* `--max_iterations`
* `--video`, `--video_interval`
* `--distributed` (multi-GPU)

</details>

## License

* Isaac Lab components: **BSD-3-Clause**
* RSL-RL: see `third_party/rsl_rl`
* Robot assets (e.g., Unitree G1): subject to original licenses


## Citation

If you find this work useful for your research, please cite:

```bibtex
@article{lu2026m3imic,
    title={M3imic: Learning a Versatile Whole-Body Controller for Multimodal Motion Mimicking},
    author={Zuxing Lu and Ziang Zheng and Yao Lyu and Jingyu Liu and Feihong Zhang and Song Lu and Xin Yuan and Changyin Sun and Xingxing Zuo and Shengbo Eben Li},
    year={2026},
    journal={arXiv preprint arXiv:2606.04829}
}
```

## Star History

<a href="https://www.star-history.com/?repos=Renforce-Dynamics%2FMultiModalWBC&type=date&legend=top-left">
 <picture>
   <source media="(prefers-color-scheme: dark)" srcset="https://api.star-history.com/chart?repos=Renforce-Dynamics/MultiModalWBC&type=date&theme=dark&legend=top-left" />
   <source media="(prefers-color-scheme: light)" srcset="https://api.star-history.com/chart?repos=Renforce-Dynamics/MultiModalWBC&type=date&legend=top-left" />
   <img alt="Star History Chart" src="https://api.star-history.com/chart?repos=Renforce-Dynamics/MultiModalWBC&type=date&legend=top-left" />
 </picture>
</a>
