# 分支与改动说明

## 1. `unitree_sim_isaaclab`

路径：
- `/home/zju/unitree_sim_isaaclab`

当前分支：
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
git switch feat/industrial-cabinet-inspire
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
