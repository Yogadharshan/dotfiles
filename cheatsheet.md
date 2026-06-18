# fedora workflow cheatsheet

for repodanta, monkeymind, ai/ml, and ai automation.

> ship first. optimize later.

## setup

-   zsh + oh-my-zsh
-   starship
-   ptyxis
-   direnv
-   atuin
-   zoxide
-   eza
-   ripgrep
-   fzf
-   docker
-   docker compose
-   claude code
-   ollama
-   playwright
-   tmux
-   git + gh

## navigation

``` bash
pwd
ls -la
eza --tree -a -L 2
xdg-open .
cd -
z repodanta
rg "PlayerController"
```

## git essentials

``` bash
git status
git branch
git remote -v
git log --oneline --graph --all -n 10

git pull --rebase origin main
git push -u origin main
```

## dotfiles

track:

-   .zshrc
-   .gitconfig
-   CLAUDE.md
-   settings.json
-   starship.toml
-   ptyxis.conf

never track:

-   .env
-   api keys
-   sessions
-   cache
-   downloads

## tmux

create session:

``` bash
tmux new -s repodanta
```

attach:

``` bash
tmux attach -t repodanta
```

list:

``` bash
tmux ls
```

shortcuts:

-   ctrl+b d → detach
-   ctrl+b % → vertical split
-   ctrl+b " → horizontal split
-   ctrl+b arrow → move panes

## gnome backup & restore

backup:

```bash
dconf dump / > ~/dotfiles/gnome/dconf-backup.ini
dconf dump /org/gnome/shell/extensions/ > ~/dotfiles/gnome/extensions.dconf
```

restore:

```bash
dconf load / < ~/dotfiles/gnome/dconf-backup.ini
dconf load /org/gnome/shell/extensions/ < ~/dotfiles/gnome/extensions.dconf
```

if you see:

``` text
sessions should be nested with care
```

you are already inside tmux.

## claude code workflow

``` bash
claude .
```

good prompt:

``` text
analyze the architecture.
map dependencies.
do not write code yet.
propose the smallest safe change.
```

## docker

``` bash
docker ps
docker compose up -d
docker compose logs -f
docker exec -it <container> bash
```

## ollama

``` bash
ollama serve
ollama pull qwen2.5
```

## ai/ml

``` bash
uv sync
uv run python train.py
jupyter lab
mlflow ui
dvc init
```

principles:

-   simple baselines first
-   measure before optimizing
-   version datasets
-   keep experiments reproducible

## playwright

``` bash
npx playwright test
npx playwright install-deps
```

## game dev

-   build one playable loop first
-   avoid feature creep
-   map scene dependencies
-   test systems independently

## repodanta roadmap

1.  refactor architecture
2.  improve dependency graph
3.  add impact analysis
4.  generate architecture docs
5.  validate on real repositories

## daily workflow

``` text
inspect
→ plan
→ implement
→ test
→ commit
→ ship
```

## rules

-   build products before infrastructure
-   architecture before ai
-   prove usefulness before scale
-   avoid setup churn
-   ship consistently
