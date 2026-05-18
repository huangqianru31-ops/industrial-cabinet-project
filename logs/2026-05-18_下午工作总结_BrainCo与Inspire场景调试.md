# 2026-05-18 下午工作总结

## 日期
- 2026-05-18

## 工作目标
- 继续完善工业电柜仿真场景。
- 尝试将原本可运行的 Inspire 仿真链路切换到 BrainCo Revo2。
- 检查并修复 BrainCo 版本下的机器人、相机、手部挂接和 DDS 通信问题。
- 在问题无法快速收敛的情况下，恢复回原本较稳定的 Inspire 版本，便于重新开始采集。

## 今天下午实际做了什么

### 1. 先确认并扩展了 BrainCo 仿真支持链路
- 找到了当前工业柜任务使用的机器人配置、任务配置、相机配置和 DDS 创建逻辑。
- 新增或补齐了 BrainCo 仿真侧的一组最小支持：
  - BrainCo DDS 创建与接入。
  - `sim_main.py` 增加 `--enable_brainco_dds`。
  - 工业柜任务新增 BrainCo 任务入口：
    - `Isaac-Industrial-Cabinet-Panel-G129-Brainco-Joint`
- 同时保留了之前 Inspire 版本作为回退方案。

### 2. 尝试从“接口兼容”升级到“完整 G1 + BrainCo 资产”
- 不满足于只在接口层做 BrainCo 兼容，进一步尝试生成完整的：
  - `G1 + BrainCo URDF`
  - `G1 + BrainCo USD`
- 为此新增了拼装和转换脚本：
  - `tools/build_g1_brainco_urdf.py`
  - `tools/convert_g1_brainco.py`
- 生成了：
  - `assets/robots/g1-29dof-brainco-base-fix-urdf/g1_29dof_with_brainco_base_fix.urdf`
  - `assets/robots/g1-29dof-brainco-base-fix-usd/g1_29dof_with_brainco_base_fix.usd`

### 3. 检查并修复 BrainCo 手的挂接姿态
- 发现 BrainCo 手与 G1 wrist 的挂接姿态很可疑，怀疑是：
  - 手装歪了
  - 手挡住了相机
  - 整机姿态和第一视角一起变怪
- 对比了：
  - G1 原始手部挂接方式
  - BrainCo 手部 URDF 内部的 base/link/joint 结构
- 最终把 BrainCo 挂接关节的姿态从：
  - `rpy="1.57 3.14 0"`
  改回：
  - `rpy="0 0 0"`
- 修正后重新生成了 BrainCo 整机 URDF。

### 4. 修 BrainCo 相机挂点
- BrainCo 工业柜任务一开始创建环境时报错：
  - 找不到 Inspire 那套手腕相机 prim path
- 原因是 BrainCo 任务还在引用 Inspire 的手腕相机挂点：
  - `left_hand_camera_base_link`
  - `right_hand_camera_base_link`
- 新增了 BrainCo 手腕相机 preset，并改成 BrainCo 资产里实际存在的挂点：
  - `left_base_link`
  - `right_base_link`

### 5. 多次尝试修正 BrainCo 第一视角
- 出现过几种异常现象：
  - 视角贴到柜门上
  - 整机像“躺在地上”
  - 看不到按钮、旋钮、门把手
  - 只能看到局部白块或柜体大面
- 为此尝试了几轮方案：
  - 改成固定前视相机
  - 改成场景中的前方观察相机
  - 改回机器人身上的 `d435_link/front_cam`
  - 再次调整 BrainCo 任务里的 camera preset
- 这些尝试说明：
  - 视角异常不只是单纯的相机位置问题
  - 更深层原因仍然和 BrainCo 整机资产、手部挂接姿态、旧 USD 缓存/旧资产加载有关

### 6. 修 DDS 网络接口问题
- 之前仿真有时会直接退出，排查后发现跟 DDS 默认网卡选择有关。
- 新增了 `--network_interface` 参数，让仿真端可显式指定网卡。
- 根据机器实际网卡信息确认：
  - `eno1` 对应 `192.168.0.18`
  - `wlp133s0f0` 对应 `10.42.0.1`
- 因此仿真应使用：
  - `--network_interface eno1`

### 7. 最后恢复回 Inspire 版本
- 由于 BrainCo 这条线在今天下午始终没有稳定到可直接采集的状态，最后决定先恢复到原本可工作的 Inspire 版本。
- 已从备份恢复这些文件：
  - `robots/unitree.py`
  - `tasks/common_config/robot_configs.py`
  - `tasks/g1_tasks/industrial_cabinet_panel_g1_29dof_dex3/industrial_cabinet_panel_g1_29dof_dex3_joint_env_cfg.py`
  - `tasks/g1_tasks/industrial_cabinet_panel_g1_29dof_dex3/__init__.py`
- 当前已确认回到任务名：
  - `Isaac-Industrial-Cabinet-Panel-G129-Inspire-Joint`

## 今天下午做过的主要尝试

### 已尝试的方向
- 给 `sim_main.py` 增加 BrainCo DDS 开关。
- 给 DDS 创建逻辑增加 BrainCo 分支。
- 给动作/状态发布链路增加 BrainCo 支持。
- 生成完整 G1 + BrainCo URDF。
- 尝试将完整 G1 + BrainCo URDF 转换成 USD。
- 修正 BrainCo 手腕相机挂点。
- 修正 BrainCo 手与腕部的固定挂接姿态。
- 多次调整 BrainCo 相机视角和观察位置。
- 回退到 Inspire 稳定版本。

### 已做的备份
- BrainCo 切换前备份：
  - `/home/zju/unitree_sim_isaaclab/backups/brainco_switch_20260518_1520`
- 工业柜场景备份：
  - `/home/zju/unitree_sim_isaaclab/backups/industrial_cabinet_20260515_0026`

## 今天下午遇到的主要问题

### 1. BrainCo 仿真不是简单改一个参数就能切过去
- BrainCo 真机链路和 teleop 端已有一定支持，但 Isaac 仿真侧原本并没有完整的 BrainCo 整机资产。
- 因此需要：
  - 新整机 URDF
  - 新整机 USD
  - 新 DDS 分支
  - 新任务配置
  - 新相机挂点

### 2. BrainCo 手挂接姿态最初是错的
- 会直接导致：
  - 手的位置和朝向异常
  - 第一视角异常
  - 整机姿态看起来怪异

### 3. 相机问题和挂接问题互相叠加
- 一开始看起来像是“相机放错了”
- 后来发现不仅是相机位置问题，还有：
  - 手挡住相机
  - 整机 BrainCo USD 可能仍然是旧姿态

### 4. USD 重生成环境不稳定
- 在当前环境下重新转 BrainCo USD 时，Isaac/Omniverse 图形初始化会报大量 GPU/NVML/GLFW 相关警告。
- 虽然 URDF 可以成功生成，但新的 USD 是否完整、是否被当前仿真真正加载，今天下午没有完全收敛到稳定状态。

### 5. 用户侧可见结果不稳定
- 典型现象包括：
  - 视角贴脸
  - 柜门占满画面
  - 画面空白
  - 整机像倒在地上
  - 看不到按钮、旋钮、手柄

## 当前结论
- **BrainCo 这条线今天下午没有达到“稳定可采集”的程度。**
- **Inspire 版本已经恢复，可以重新回到那条较稳定的采集链路。**
- 如果后续继续做 BrainCo，建议单独安排一轮：
  - 先在干净环境里只解决 `G1 + BrainCo USD` 生成与加载问题；
  - 再回到工业柜任务和采集。

## 当前建议的下一步
- 先使用 Inspire 版本重新开始仿真与采集，确保任务继续推进。
- 等采集链路稳定后，再单独处理 BrainCo 完整仿真支持。

## 当前 Inspire 启动方式

### 仿真
```bash
source ~/miniconda3/etc/profile.d/conda.sh
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab
python sim_main.py --headless --enable_cameras --device cpu --task Isaac-Industrial-Cabinet-Panel-G129-Inspire-Joint --enable_inspire_dds --robot_type g129 --public_ip 192.168.0.18 --network_interface eno1
```

### Teleop
```bash
source ~/miniconda3/etc/profile.d/conda.sh
conda activate tv
cd ~/xr_teleoperate/teleop
python teleop_hand_and_arm.py --ee=inspire_dfx --sim --record --headless --img-server-ip 192.168.0.18
```

