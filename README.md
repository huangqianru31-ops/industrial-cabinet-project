# Industrial Cabinet Project

这个仓库只放 3 类东西：

- 项目说明
- 工作记录
- Git 使用说明

不放大体量源码，也不放采集数据。

## 当前代码仓

- `~/unitree_sim_isaaclab`
  - 仿真场景、机器人配置、DDS、工业电柜任务
- `~/xr_teleoperate`
  - teleop、录制、数据检查
- `~/unitree_lerobot`
  - LeRobot 转换与后处理

## 当前分支

- `unitree_sim_isaaclab`
  - `feat/industrial-cabinet-inspire`
  - `feat/brainco-experiments`
- `xr_teleoperate`
  - `feat/teleop-data-tools`
- `unitree_lerobot`
  - `feat/sim-robot-configs`

## 文档入口

- [仓库关系说明](./docs/repo_layout.md)
- [分支与改动说明](./docs/branches.md)
- [Git 使用说明](./docs/git_usage.md)
- [2026-05-18 下午工作总结](./logs/2026-05-18_下午工作总结_BrainCo与Inspire场景调试.md)

## 数据说明

下面这些目录不要直接进普通 git：

- `~/xr_teleoperate/teleop/utils/data/`
- `~/.cache/huggingface/lerobot/`

原始数据和转换后的 LeRobot 数据，单独打包保存。
