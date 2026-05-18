# Industrial Cabinet Project

这个仓库是这次“工业电柜仿真采集任务”的**总控仓库**，不直接承载大体量源码和数据，而是用来管理：

- 项目说明
- 每日工作记录
- 启动命令
- 代码仓之间的关系
- 数据与 LeRobot 转换说明

## 当前相关代码仓

- `~/unitree_sim_isaaclab`
  - 仿真场景、机器人配置、DDS、工业电柜任务
- `~/xr_teleoperate`
  - teleop、录制、数据检查
- `~/unitree_lerobot`
  - LeRobot 转换与后处理

## 当前建议的 git 管理方式

### 1. 本仓库管理“项目级内容”
- 文档
- 日志
- 关键命令
- 进度记录

### 2. 三个代码仓各自管理“代码改动”
- `unitree_sim_isaaclab`
- `xr_teleoperate`
- `unitree_lerobot`

### 3. 采集数据不要直接大量进 git
以下目录不建议直接纳入普通 git：

- `~/xr_teleoperate/teleop/utils/data/`
- `~/.cache/huggingface/lerobot/`

建议：
- 本地磁盘保存
- 打包 zip
- 需要长期版本化时再考虑 `git-lfs` 或 `dvc`

## 当前已整理的文档

- [仓库关系说明](./docs/repo_layout.md)
- [2026-05-18 下午工作总结](./logs/2026-05-18_下午工作总结_BrainCo与Inspire场景调试.md)

## 后续你最常做的事

### 记录今天做了什么
把每日总结放进：

- `logs/`

### 记录怎么启动
把长期稳定命令写进：

- `docs/`

### 提交代码改动
分别进入三个代码仓单独 commit。

