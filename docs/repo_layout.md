# 仓库关系说明

## 一、为什么要这样管理

这次任务同时涉及：

- 仿真场景开发
- teleop 录制
- LeRobot 数据转换
- 日志与汇报材料

如果把这些都混在一个仓库里，会很乱。  
因此建议拆成：

### 1. 代码仓
- `~/unitree_sim_isaaclab`
- `~/xr_teleoperate`
- `~/unitree_lerobot`

### 2. 总控仓
- `~/industrial-cabinet-project`

总控仓只放：
- markdown 文档
- 项目说明
- 每日工作记录
- 启动命令

## 二、三个代码仓分别管什么

### `unitree_sim_isaaclab`
主要负责：
- 工业电柜场景
- 按钮 / 旋钮 / 手柄建模
- 机器人配置
- 相机配置
- DDS / sim 侧支持

### `xr_teleoperate`
主要负责：
- teleop 启动
- 录制
- episode 数据目录
- 数据完整性检查脚本

### `unitree_lerobot`
主要负责：
- 原始 episode 转 LeRobot
- robot type 配置
- 数据集标准化

## 三、GitHub 要不要用

### 可以不用 GitHub
如果你只是自己本地管理，直接用本地 git 就可以。

### 更推荐用你自己的 GitHub
如果你想：
- 备份
- 和老师/师兄协作
- 保留阶段性版本

那就推荐：

1. 你自己的 GitHub 新建一个总控仓库  
2. 三个代码仓如果后面也要同步，可以 fork 到你自己的 GitHub

## 四、最稳的推进顺序

1. 先在本地把文档和代码整理干净
2. 各代码仓单独 commit
3. 再决定是否推送到 GitHub

## 五、当前状态

当前已经建立总控仓库：

- `/home/zju/industrial-cabinet-project`

其中：
- `README.md`：项目入口
- `docs/`：说明文档
- `logs/`：日志

