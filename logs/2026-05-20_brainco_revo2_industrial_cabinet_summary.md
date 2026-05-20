# 2026-05-20 BrainCo/Revo2 工业柜调试总结

## 背景

今天主要调试 G1 + 强脑手输入 + Revo2 仿真手在工业柜任务中的效果。问题集中在手指不同步、抓不住手柄、按不到按钮、按钮/手柄会被压进柜子，以及 Isaac Sim 启动时报错。

## 主要问题

- 强脑手手指能动，但仿真 Revo2 手反应慢、不够灵活。
- 人手在头显里已经接近按钮/手柄，但仿真机械手实际接触点仍然够不到。
- 抓握手柄时容易滑开，手指闭合不足。
- 工业柜按钮和门把手会被压进柜体，不符合现实交互。
- 一次场景修改后，Isaac Sim 启动失败，报错为 `UsdFileCfg.__init__() got an unexpected keyword argument 'physics_material'`。

## 根因判断

- 强脑手输入是 `0..1` 归一化值，而 Revo2 仿真关节需要弧度目标，直接使用会导致手指闭合范围不足。
- 仿真侧滤波和 deadband 会增加手指延迟，并吞掉小动作。
- Revo2 手指执行器参数偏保守，接触抓握时力量和速度不够。
- XR 手腕位置到 G1 IK 的映射需要适当放大，才能解决实际接触点够不到的问题。
- 工业柜按钮和手柄的几何尺寸、位置、关节限位需要根据仿真接触重新调。
- 当前 IsaacLab 版本不支持在 `UsdFileCfg` 上直接加 `physics_material` 参数。

## 已完成修改

### `xr_teleoperate`

分支：

```text
feat/teleop-data-tools
```

最新提交：

```text
5e8978f Document and tune BrainCo Revo2 teleop
```

主要改动：

- `assets/brainco_hand/brainco.yml`
  - 将左右手 `low_pass_alpha` 调为 `0.9`，减少延迟同时保留少量平滑。
- `teleop/teleop_hand_and_arm.py`
  - 增加 `--arm-reach-scale` 参数。
  - BrainCo 仿真默认使用 `1.12` 的 reach scale。
- `teleop/robot_control/robot_arm_ik.py`
  - 在 G1 IK 前对 XR 手腕位移做缩放。
- `docs/2026-05-20-brainco-revo2-work-summary.md`
  - 记录今天完整排查、调参、数据转换和打包情况。

GitHub 查看：

```text
https://github.com/huangqianru31-ops/xr_teleoperate/tree/feat/teleop-data-tools
```

### `unitree_sim_isaaclab`

分支：

```text
feat/revo2-integration
```

最新提交：

```text
6fe6653 Tune BrainCo Revo2 cabinet simulation
```

主要改动：

- `action_provider/action_provider_dds.py`
  - 将强脑手 `0..1` 命令映射到 Revo2 关节弧度范围。
  - 关闭仿真侧额外低通延迟和 deadband。
- `tasks/common_observations/brainco_state.py`
  - 将 Revo2 弧度状态归一化回强脑手状态格式。
- `robots/revo2.py`
  - 提高手指执行器的力矩、速度、刚度和阻尼。
- `assets/objects/industrial_cabinet_simple/`
  - 修改工业柜按钮、旋钮、门把手 URDF/USD。
  - 调整按钮和手柄位置、尺寸、碰撞几何和关节限位。
- `tasks/common_scene/base_scene_industrial_cabinet_simple.py`
  - 调整工业柜目标点和执行器参数。
  - 移除不兼容的 `physics_material` 参数。
- `assets/robots/g1-29dof-revo2-base-fix-urdf/`
  - 新增 G1 + Revo2 组合 URDF 和 STL 网格。
- `assets/robots/g1-29dof-revo2-base-fix-usd/`
  - 新增 G1 + Revo2 Isaac Sim USD 模型。

GitHub 查看：

```text
https://github.com/huangqianru31-ops/unitree_sim_isaaclab/tree/feat/revo2-integration
```

## 数据和导出

最新有效录制：

```text
/home/zju/xr_teleoperate/teleop/utils/data/pick cube/episode_0007
```

检查结果：

- 503 帧
- 三路相机 `color_0/color_1/color_2` 都完整
- `colors/` 中共有 1509 张图

LeRobot 转换结果：

```text
/home/zju/xr_teleoperate/converted_lerobot/local/pick_cube_brainco_revo2_episode_0007
```

LeRobot 压缩包：

```text
/home/zju/xr_teleoperate/converted_lerobot/local/pick_cube_brainco_revo2_episode_0007.zip
```

## 给师姐的模型包

模型与调参文件包：

```text
/home/zju/xr_teleoperate/share_packages/g1_brainco_revo2_industrial_cabinet_models_20260520.zip
```

包内已包含中文 `README.md` 和 `MANIFEST.md`，说明每个文件夹用途、主要调参内容、如何放回原仓库、如何重新生成 USD。

## 后续工作

- 继续在头显里测试手指同步、抓握稳定性和按钮触达。
- 根据实测继续微调：
  - 强脑手低通参数
  - Revo2 手指执行器参数
  - `--arm-reach-scale`
  - 工业柜按钮/手柄位置和碰撞体
- 如果要让总仓库直接“连上”三个代码仓库，可以后续考虑用 git submodule，但目前总仓库只作为项目入口和日志仓库使用。
