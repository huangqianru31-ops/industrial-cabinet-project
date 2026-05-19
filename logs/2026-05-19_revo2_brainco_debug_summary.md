# 2026-05-19 Revo2 / BrainCo 调试总结

## 今日目标

在 `unitree_sim_isaaclab` 中完成 `G1 + Revo2/BrainCo` 手的整机集成，并让工业柜任务至少满足以下条件：

- Revo2 手能够正确挂到 G1 wrist 上
- 机器人在工业柜场景里能稳定站住，不再明显摆臂
- 前视画面可以正常看到柜门和操作区域
- BrainCo/Revo2 手指命令能够从 teleop 侧传到 sim 侧

## 今天完成的工作

### 1. 接入官方 Revo2 描述包

本地引入了官方 `revo2_description` 资源：

- 来源目录：
  - `/home/zju/unitree_sim_isaaclab/third_party/Revo2_URDF Description_ROS2/revo2_description`
- 关键文件：
  - `urdf/revo2_left_hand.urdf`
  - `urdf/revo2_right_hand.urdf`
  - `meshes/revo2_left_hand/`
  - `meshes/revo2_right_hand/`

检查结果：

- 官方左右手 URDF 中 mesh 引用完整
- 合并后的整机 URDF 中 mesh 路径也能正确解析
- 当前“手显示出来但姿态不对”的问题，不是 mesh 丢失导致的

### 2. 新增 G1 + Revo2 整机资产生成流程

新增脚本：

- `tools/build_g1_revo2_urdf.py`
- `tools/convert_g1_revo2.py`

用途：

- 将 G1 本体和官方 Revo2 左右手拼接为整机 URDF
- 再从整机 URDF 转 Isaac Sim / IsaacLab 可用 USD

已生成的本地资产：

- `assets/robots/g1-29dof-revo2-base-fix-urdf/g1_29dof_with_revo2_base_fix.urdf`
- `assets/robots/g1-29dof-revo2-base-fix-usd/g1_29dof_with_revo2_base_fix.usd`

说明：

- 这些生成资产目前保留在本地
- Git 中主要保留“生成脚本”和“仿真接入代码”
- 后续可按文末命令重新生成

### 3. 完成 Revo2 / BrainCo 在 sim 侧的代码接入

新增文件：

- `robots/revo2.py`
- `dds/brainco_dds.py`
- `tasks/common_observations/brainco_state.py`

修改文件：

- `action_provider/action_provider_dds.py`
- `dds/dds_create.py`
- `sim_main.py`
- `tasks/common_config/camera_configs.py`
- `tasks/common_config/robot_configs.py`
- `tasks/g1_tasks/industrial_cabinet_panel_g1_29dof_dex3/__init__.py`
- `tasks/g1_tasks/industrial_cabinet_panel_g1_29dof_dex3/industrial_cabinet_panel_g1_29dof_dex3_joint_env_cfg.py`

主要内容：

- 新增 Revo2 机器人配置
- 新增 BrainCo DDS 命令解析
- 新增 Revo2 手关节观测
- 将工业柜任务注册为 `Isaac-Industrial-Cabinet-Panel-G129-Revo2-Joint`
- 为调试加入 arm / hand joint 的状态打印

### 4. 修复整机稳定性问题

已处理的问题：

- 机器人最初会倒地
  - 原因：生成 USD 时 `fix_base` 没正确固定
  - 处理：Revo2 转换流程改为显式启用 `fix_base`

- 机器人前臂和 wrist 曾经明显摆动
  - 原因：初始姿态和 actuator 配置不适配新末端负载
  - 处理：
    - 为 Revo2 线单独保留 default joint pose
    - 给 `left/right_wrist_pitch_joint` 添加固定偏置
    - 提高 Revo2 线 arm/wrist actuator 的 stiffness / damping / effort_limit

调试结论：

- 经过 `--debug_arm_joints` 检查后，后期 `wrist_pitch` target 已稳定
- 不再支持“DDS 目标乱跳导致摆臂”的判断
- 当前手臂大幅摆动问题已经基本收住

### 5. 修复左右手挂接姿态

今天左手挂接做了多轮调试，最后收敛到左右手分别使用不同的 mount 旋转。

当前结果：

- 右手姿态基本正常
- 左手最后已经调到“不会消失、不会明显上下反”的可用状态

说明：

- 左手最终姿态还建议后续再做一次小范围精修
- 但现在已经脱离“完全翻转/完全侧翻/手消失”的阶段

### 6. 修复 teleop 侧 websocket 断连刷异常

问题：

- 网页关闭或 websocket 断开后，`televuer` 一直反复报：
  - `Websocket session is missing`

处理：

- 在 `xr_teleoperate/teleop/televuer/src/televuer/televuer.py` 中增加断线保护
- 当前做法是：检测到 websocket session 不存在后退出 monocular webrtc 更新循环

说明：

- 这个改动目前只保留在本地 `televuer` 子仓工作区
- 我已额外保存 patch 文件，见：
  - `docs/patches/televuer_websocket_session_fix_2026-05-19.patch`

### 7. 定位并修复 BrainCo/Revo2 手指“能收到命令但闭合方向反了”的问题

这是今天最关键的后期问题之一。

现象：

- 一开始 `raw cmds` 为空，手指当然不动
- 后来 `raw cmds` 有数据了，但手会一上来直接打到接近最大闭合

证据：

- `sim_main.py --debug_brainco_hands` 输出显示：
  - `raw cmds` 最终已成功收到
  - 关节 `tgt` 和 `pos` 也在同步变化
- 说明：
  - 命令通路已经通了
  - 问题不是 actuator 没配
  - 问题是 sim 侧把开合方向解释反了

根因：

- teleop 侧 `robot_hand_brainco.py` 约定：
  - `0.0 = fully open`
  - `1.0 = fully closed`
- 但 sim 侧 `dds/brainco_dds.py` 原先将归一化 / 反归一化方向写反了

修复：

- 将 sim 侧开合语义改为与 teleop 一致：
  - `0 -> open`
  - `1 -> closed`

当前状态：

- BrainCo/Revo2 手指命令链路已确认打通
- 但“手指 still 动不太了”需要在修复后再做一次完整实机验证

## 今天遇到的主要问题和解决方案

### 问题 1：相机画面异常，前视看到天花板、柜背面、机器人倒地等

原因：

- 初期同时叠加了三个问题：
  - front camera 链没有对齐
  - 机器人 base 没固定
  - 手挂接姿态本身错误

解决：

- 先收住机器人固定底座
- 再将画面切回正式机器人前视
- 再用手挂接姿态修正把左右手拉回合理位置

### 问题 2：Revo2 左右手姿态不对，左手尤其容易上下反 / 侧翻 / 看不见

原因：

- Revo2 左右手 base frame 是镜像关系
- 不能左右手共用同一组 mount `rpy`

解决：

- 在 `build_g1_revo2_urdf.py` 中分开调左右手 mount 外参
- 多轮验证后拿到当前可用姿态

### 问题 3：整条手臂明显摆动

原因：

- 起初怀疑 wrist target 在乱跳
- 后续加了 debug 后发现 target 稳定
- 真正问题更偏向：
  - Revo2 末端负载与原 arm/wrist 参数不匹配
  - 初始姿态不适合

解决：

- 调整 Revo2 线 default pose
- 提高 arm/wrist actuator stiffness / damping / effort_limit
- 用 `--debug_arm_joints` 验证 `tgt / pos / vel`

### 问题 4：网页断开后 vuer 连续刷异常

原因：

- websocket 会话已经断开，UI 更新循环仍在继续 `session.upsert(...)`

解决：

- 在 `televuer.py` 中增加断线保护，检测到 session 缺失时退出循环

### 问题 5：手指看起来不动，后续又发现其实一收到命令就直接闭合到极限

原因：

- BrainCo DDS 侧的归一化方向和 teleop 定义不一致

解决：

- 修复 `dds/brainco_dds.py` 中归一化与反归一化逻辑

## 当前还剩的问题

### 1. 手指需要做最终运行验证

虽然已经确认：

- raw hand commands 能到 sim
- hand joint target 也在变化
- sim 侧方向 bug 已修

但还需要下一轮运行验证：

- 手指是否终于能按预期开合
- 是否还存在“动作幅度偏小”或“默认就过度闭合”的问题

### 2. 左手姿态建议再做一次小范围精修

当前左手已经从完全错误状态收回来，但还建议后续继续检查：

- 手掌法向是否与右手一致
- 拇指朝向是否与真实使用姿态一致

### 3. 距离柜子仍可能需要再微调

当前 Revo2 任务里已经把机器人往柜子方向挪近了几次，但还需要根据实际 teleop 操作体验再微调：

- `init_pos` 的 `y`
- 必要时再微调相机里操作区域的构图

### 4. 手外观仍偏白

这个今天没有优先修。

原因大概率是：

- 官方 Revo2 URDF visual 材质本来就是纯白
- 场景光照较强

后续如果要做更真实的展示，可以单独补：

- Revo2 hand 的中性材质
- 或 scene light / exposure 微调

## 建议的下一步

下次继续时，建议按这个顺序：

1. 重新运行一轮 Revo2 任务，验证手指是否能正常开合
2. 如果手指 still 不自然，再检查 teleop 侧 hand command 的归一化范围
3. 对左手 mount 姿态做最后一次小角度精修
4. 按真实抓取任务需要，再继续微调机器人与柜子的距离

## 关键运行命令

### 重新生成 Revo2 整机资产

```bash
source ~/miniconda3/etc/profile.d/conda.sh
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab
python tools/build_g1_revo2_urdf.py
python tools/convert_g1_revo2.py --headless --device cpu --fix-base
```

### 启动 Revo2 仿真

```bash
source ~/miniconda3/etc/profile.d/conda.sh
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab
python sim_main.py --headless --enable_cameras --device cpu --task Isaac-Industrial-Cabinet-Panel-G129-Revo2-Joint --enable_brainco_dds --robot_type g129 --public_ip 192.168.0.18 --network_interface eno1
```

### 启动 teleop

```bash
source ~/miniconda3/etc/profile.d/conda.sh
conda activate tv
cd ~/xr_teleoperate/teleop
python teleop_hand_and_arm.py --ee=brainco --sim --record --headless --img-server-ip 192.168.0.18
```

### 调试手臂关节

```bash
python sim_main.py \
  --headless \
  --enable_cameras \
  --device cpu \
  --task Isaac-Industrial-Cabinet-Panel-G129-Revo2-Joint \
  --enable_brainco_dds \
  --robot_type g129 \
  --public_ip 192.168.0.18 \
  --network_interface eno1 \
  --debug_arm_joints \
  --debug_arm_interval 50
```

### 调试手指命令

```bash
python sim_main.py \
  --headless \
  --enable_cameras \
  --device cpu \
  --task Isaac-Industrial-Cabinet-Panel-G129-Revo2-Joint \
  --enable_brainco_dds \
  --robot_type g129 \
  --public_ip 192.168.0.18 \
  --network_interface eno1 \
  --debug_brainco_hands \
  --debug_brainco_interval 50
```
