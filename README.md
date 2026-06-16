# Agents Skills

This repository keeps two skill directories side by side:

- `skills/`: your local/custom skills tracked by this repository.
- `skills-main/`: upstream Matt Pocock skills, tracked as a Git submodule from `https://github.com/mattpocock/skills.git`.

## Update `skills/`

From anywhere:

```bash
git -C /Users/chasewu/.agents pull
```

If you changed local skills and want to save them:

```bash
git -C /Users/chasewu/.agents add skills
git -C /Users/chasewu/.agents commit -m "Update local skills"
git -C /Users/chasewu/.agents push
```

## Update `skills-main/`

Fetch the latest upstream submodule commit:

```bash
git -C /Users/chasewu/.agents submodule update --remote skills-main
```

Record that new submodule pointer in this repository:

```bash
git -C /Users/chasewu/.agents add .gitmodules skills-main README.md
git -C /Users/chasewu/.agents commit -m "Update skills-main submodule"
git -C /Users/chasewu/.agents push
```

## Fresh clone setup

After cloning this repository on a new machine, initialize submodules:

```bash
git submodule update --init --recursive
```

## Backup

The previous plain-directory copy was moved to `skills-main.bak/` during the submodule conversion.
