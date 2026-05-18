# 仓库关系说明

## 1. 为什么拆成 4 个仓库

这次任务同时有 4 类内容：

- 仿真场景和机器人配置
- teleop 与录制
- LeRobot 转换
- 文档和工作记录

所以拆成：

- `~/unitree_sim_isaaclab`
- `~/xr_teleoperate`
- `~/unitree_lerobot`
- `~/industrial-cabinet-project`

## 2. 每个仓库分别管什么

### `unitree_sim_isaaclab`

负责：
- 工业电柜场景
- 按钮、旋钮、手柄
- 机器人配置
- 相机配置
- DDS / 仿真侧支持

### `xr_teleoperate`

负责：
- teleop 启动
- 录制
- 数据检查脚本

### `unitree_lerobot`

负责：
- 原始 episode 转 LeRobot
- sim 机器人配置

### `industrial-cabinet-project`

负责：
- 文档
- 日志
- 分支说明
- git 使用说明

## 3. 哪些东西不进 git

不要直接提交这些大数据目录：

- `~/xr_teleoperate/teleop/utils/data/`
- `~/.cache/huggingface/lerobot/`

这些数据单独打包保存。

## 4. 当前最常用的查看方式

看某个仓库改了什么：

```bash
cd 仓库路径
git status
git diff
```

看当前在哪个分支：

```bash
git branch --show-current
```
