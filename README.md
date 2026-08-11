# Agents Skills

本仓库并排维护两套 skill 目录：

- `skills/`：由本仓库跟踪的本地/自定义 skills。
- `skills-main/`：上游 Matt Pocock skills，以 Git 子模块形式跟踪，来源为 `https://github.com/mattpocock/skills.git`。

## 更新 `skills/`

在任意目录下执行：

```bash
git -C /Users/chasewu/.agents pull
```

若修改了本地 skills 并需要保存：

```bash
git -C /Users/chasewu/.agents add skills
git -C /Users/chasewu/.agents commit -m "Update local skills"
git -C /Users/chasewu/.agents push
```

## 更新 `skills-main/`

拉取上游子模块的最新提交：

```bash
git -C /Users/chasewu/.agents submodule update --remote skills-main
```

在本仓库中记录新的子模块指针：

```bash
git -C /Users/chasewu/.agents add .gitmodules skills-main README.md
git -C /Users/chasewu/.agents commit -m "Update skills-main submodule"
git -C /Users/chasewu/.agents push
```

## 新机器克隆后的初始化

克隆本仓库后，初始化子模块：

```bash
git submodule update --init --recursive
```

## 备份说明

在改为子模块之前，原先的纯目录副本已移至 `skills-main.bak/`。
