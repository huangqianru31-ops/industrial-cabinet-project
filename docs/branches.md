# 分支与改动说明

## 1. `unitree_sim_isaaclab`

路径：
- `/home/zju/unitree_sim_isaaclab`

当前分支：
- `feat/revo2-integration`
  - 当前 BrainCo/Revo2 工业柜主调试分支
  - 包含 G1 + Revo2 URDF/USD 模型资产
  - 包含工业柜按钮、旋钮、门把手模型和场景参数
  - 包含 BrainCo DDS 到 Revo2 仿真手关节的映射
  - 最新提交：`6fe6653 Tune BrainCo Revo2 cabinet simulation`
- `feat/industrial-cabinet-inspire`
  - 稳定 Inspire 版本
  - 包含工业电柜场景、按钮场景、网络接口支持
- `feat/brainco-experiments`
  - BrainCo 仿真实验分支
  - 和稳定 Inspire 分支分开保存

最常用命令：

```bash
cd ~/unitree_sim_isaaclab
git branch
git switch feat/revo2-integration
git status
git log --oneline -n 5
git diff
```

## 2. `xr_teleoperate`

路径：
- `/home/zju/xr_teleoperate`

当前分支：
- `feat/teleop-data-tools`
  - 数据完整性检查脚本
  - `.gitignore` 中忽略 `teleop/utils/data/`
  - BrainCo teleop 低通参数
  - G1 手臂 `--arm-reach-scale`
  - 2026-05-20 BrainCo/Revo2 调参记录和总结文档
  - 最新提交：`5e8978f Document and tune BrainCo Revo2 teleop`

最常用命令：

```bash
cd ~/xr_teleoperate
git branch
git switch feat/teleop-data-tools
git status
git log --oneline -n 5
git diff
```

## 3. `unitree_lerobot`

路径：
- `/home/zju/unitree_lerobot`

当前分支：
- `feat/sim-robot-configs`
  - `Unitree_G1_Dex3_Sim`
  - `Unitree_G1_Inspire_Sim`
  - `Unitree_G1_Brainco_Sim`

最常用命令：

```bash
cd ~/unitree_lerobot
git branch
git switch feat/sim-robot-configs
git status
git log --oneline -n 5
git diff
```

## 4. 总控仓库

路径：
- `/home/zju/industrial-cabinet-project`

用途：
- 放文档
- 放日志
- 放 git 使用说明

最常用命令：

```bash
cd ~/industrial-cabinet-project
git status
git log --oneline -n 5
```

## 5. 2026-05-20 最新改动怎么看

GitHub 上不要只看默认 `main` 分支，要切到对应 feature 分支：

- `xr_teleoperate`
  - `https://github.com/huangqianru31-ops/xr_teleoperate/tree/feat/teleop-data-tools`
  - 重点看：
    - `docs/2026-05-20-brainco-revo2-work-summary.md`
    - `assets/brainco_hand/brainco.yml`
    - `teleop/teleop_hand_and_arm.py`
    - `teleop/robot_control/robot_arm_ik.py`
- `unitree_sim_isaaclab`
  - `https://github.com/huangqianru31-ops/unitree_sim_isaaclab/tree/feat/revo2-integration`
  - 重点看：
    - `assets/robots/g1-29dof-revo2-base-fix-urdf/`
    - `assets/robots/g1-29dof-revo2-base-fix-usd/`
    - `assets/objects/industrial_cabinet_simple/`
    - `action_provider/action_provider_dds.py`
    - `robots/revo2.py`
    - `tasks/common_scene/base_scene_industrial_cabinet_simple.py`
