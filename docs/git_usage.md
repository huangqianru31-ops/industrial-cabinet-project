# Git 使用说明

## 1. 怎么看我改了什么

进入对应仓库后先看：

```bash
git status
```

它会告诉你：
- 当前在哪个分支
- 哪些文件改了
- 哪些文件还没提交

如果要看具体改了什么：

```bash
git diff
```

如果只看某个文件：

```bash
git diff 路径/文件名
```

例子：

```bash
cd ~/unitree_sim_isaaclab
git diff tasks/common_scene/base_scene_industrial_cabinet_simple.py
```

## 2. 怎么看当前在哪个分支

```bash
git branch --show-current
```

## 3. 怎么切换分支

```bash
git switch 分支名
```

例子：

```bash
git switch feat/industrial-cabinet-inspire
```

## 4. 怎么看最近提交过什么

```bash
git log --oneline -n 10
```

## 5. 怎么提交自己的改动

```bash
git add .
git commit -m "写清楚这次改了什么"
```

如果只提交一个文件：

```bash
git add 路径/文件名
git commit -m "Update one file"
```

## 6. 怎么推到 GitHub

```bash
git push
```

如果是第一次推一个新分支：

```bash
git push -u origin 分支名
```

## 7. 这几个仓库平时最常用的命令

### `unitree_sim_isaaclab`

```bash
cd ~/unitree_sim_isaaclab
git branch --show-current
git status
git diff
```

### `xr_teleoperate`

```bash
cd ~/xr_teleoperate
git branch --show-current
git status
git diff
```

### `unitree_lerobot`

```bash
cd ~/unitree_lerobot
git branch --show-current
git status
git diff
```

## 8. 什么时候不要直接 `git add .`

下面这些一般不要直接进 git：

- `teleop/utils/data/`
- `.cache/huggingface/lerobot/`
- 大 zip
- 临时截图
- 本地缓存

先 `git status` 看一眼，再决定要不要提交。
