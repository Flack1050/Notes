---
tags: [git, devops]
---

# 🌿 Git Workflow

Связано: [[🔄CI-CD Pipelines]]

## Ветвление — популярные модели

- **Trunk-based** — одна основная ветка (`main`), короткоживущие feature-ветки, частые мержи. Хорошо сочетается с CI/CD.
- **GitFlow** — `main` + `develop` + `feature/*` + `release/*` + `hotfix/*`. Тяжеловеснее, подходит для релизных циклов с версионированием.
- **GitHub Flow** — упрощённый trunk-based: ветка на фичу → PR → review → merge в `main` → деплой.

## Базовые команды

```bash
git status
git add -p                     # добавлять изменения по кускам (hunk)
git commit -m "feat: add auth middleware"
git log --oneline --graph --all

git branch feature/auth
git switch feature/auth        # современная замена checkout -b
git switch -c feature/auth     # создать и переключиться

git fetch origin
git rebase origin/main          # перенести коммиты поверх свежего main
git merge feature/auth           # альтернатива rebase

git stash
git stash pop
```

## Конвенции коммитов (Conventional Commits)

```
feat: новая фича
fix: исправление бага
chore: рутина (обновление зависимостей и т.п.)
docs: документация
refactor: рефакторинг без изменения поведения
test: добавление/правка тестов
ci: изменения в CI/CD конфигах
```
Такой формат позволяет автоматически генерировать changelog и версионировать по semver (semantic-release).

## Полезные приёмы

```bash
git commit --amend --no-edit          # добавить изменения в последний коммит
git rebase -i HEAD~3                  # интерактивный rebase — squash/reorder коммитов
git cherry-pick <hash>                # перенести один коммит в другую ветку
git bisect start                       # бинарный поиск бага по истории
git worktree add ../hotfix main        # отдельная рабочая директория для другой ветки
```

## .gitignore для DevOps-репозиториев

```
.env
*.tfstate
*.tfstate.backup
.terraform/
node_modules/
__pycache__/
*.pem
*.key
```

> [!warning] Секреты в git
> Если случайно закоммитил секрет — недостаточно просто удалить файл новым коммитом, он останется в истории. Нужно переписывать историю (`git filter-repo` / BFG Repo-Cleaner) и **ротировать сам секрет**, считая его скомпрометированным.

#git #devops #version-control
