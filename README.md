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
  - `feat/revo2-integration`
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
- [2026-05-20 BrainCo/Revo2 工业柜调试总结](./logs/2026-05-20_brainco_revo2_industrial_cabinet_summary.md)
- [2026-05-18 下午工作总结](./logs/2026-05-18_下午工作总结_BrainCo与Inspire场景调试.md)

## 最新入口

2026-05-20 的 BrainCo/Revo2 工业柜调参主要分布在两个代码仓：

- `xr_teleoperate`
  - GitHub: `https://github.com/huangqianru31-ops/xr_teleoperate/tree/feat/teleop-data-tools`
  - 最新提交：`5e8978f Document and tune BrainCo Revo2 teleop`
- `unitree_sim_isaaclab`
  - GitHub: `https://github.com/huangqianru31-ops/unitree_sim_isaaclab/tree/feat/revo2-integration`
  - 最新提交：`6fe6653 Tune BrainCo Revo2 cabinet simulation`

给师姐看的模型与调参文件包在服务器本地：

```text
/home/zju/xr_teleoperate/share_packages/g1_brainco_revo2_industrial_cabinet_models_20260520.zip
```

## 数据说明

下面这些目录不要直接进普通 git：

- `~/xr_teleoperate/teleop/utils/data/`
- `~/.cache/huggingface/lerobot/`

原始数据和转换后的 LeRobot 数据，单独打包保存。
