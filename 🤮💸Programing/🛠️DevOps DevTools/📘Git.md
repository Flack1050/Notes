---
tags: [git, devops]
---

# 📘 Git

Связано: [[🌿Git Workflow]] · [[🔄CI-CD Pipelines]]

Про ветвление, коммиты и командную работу — в [[🌿Git Workflow]]. Здесь — то, что обычно остаётся "за кадром": как git устроен внутри и базовая настройка.

## Три состояния файла

```
Working Directory  →  Staging Area (index)  →  Repository (.git)
   (правишь)           git add                   git commit
```

```bash
git status          # что изменено / что в staging
git diff             # diff working dir vs staging
git diff --staged     # diff staging vs последний коммит
```

## Объектная модель (упрощённо)

Git хранит не diff'ы, а снэпшоты. Внутри `.git/objects` всего 4 типа объектов, адресуемых SHA-1/SHA-256 хэшем содержимого:

| Объект | Что хранит |
|---|---|
| **blob** | содержимое файла |
| **tree** | снимок директории (список blob/tree + имена) |
| **commit** | ссылка на tree + родительский коммит + автор + сообщение |
| **tag** | именованная ссылка на конкретный коммит (annotated tag) |

```bash
git cat-file -p <hash>      # посмотреть содержимое любого объекта
git log --oneline            # commit — это просто цепочка через parent-ссылки
```

Ветка (`branch`) — это просто файл с хэшем коммита, на который она указывает (`.git/refs/heads/main`). `HEAD` — указатель на текущую ветку (или конкретный коммит в detached state).

## Настройка (один раз на машину)

```bash
git config --global user.name "Имя"
git config --global user.email "you@example.com"
git config --global init.defaultBranch main
git config --global core.editor "nvim"
git config --global pull.rebase true     # git pull = fetch + rebase, а не merge

git config --list                          # посмотреть всё
git config --global -e                      # отредактировать ~/.gitconfig напрямую
```

## Remotes

```bash
git remote -v                              # список remote'ов
git remote add origin git@github.com:user/repo.git
git remote set-url origin <new-url>
git fetch origin                            # скачать, не трогая рабочие файлы
git push -u origin main                      # -u запоминает upstream для будущих push/pull без аргументов
```

## Теги

```bash
git tag v1.0.0                       # lightweight tag
git tag -a v1.0.0 -m "Release 1.0"    # annotated tag (с автором/датой/сообщением — предпочтительно)
git push origin v1.0.0
git push origin --tags                 # запушить все теги разом
git tag -d v1.0.0                       # удалить локально
git push origin :refs/tags/v1.0.0        # удалить на remote
```

## Submodules

Подключение одного git-репозитория внутрь другого как подпапки с фиксированным коммитом:

```bash
git submodule add git@github.com:org/lib.git vendor/lib
git submodule update --init --recursive   # после клонирования родительского репо
git submodule update --remote               # подтянуть новый коммит в сабмодуле
```

> [!note] Альтернатива
> Submodules неудобны в работе (легко забыть обновить/закоммитить указатель). Для большинства DevOps-кейсов (например, шаринг Helm-чартов) чаще проще отдельный пакет/registry, чем submodule.

## Hooks

Скрипты в `.git/hooks/`, которые git запускает на определённые события (не коммитятся в репозиторий сами по себе — живут только локально, если не настроен отдельный механизм вроде `core.hooksPath` или Husky):

```bash
.git/hooks/pre-commit     # перед коммитом — например, запустить линтер
.git/hooks/pre-push        # перед push — например, прогнать тесты
.git/hooks/commit-msg       # проверить формат сообщения коммита
```

```bash
#!/bin/sh
# .git/hooks/pre-commit (должен быть executable: chmod +x)
npm run lint || exit 1
```

## Полезные алиасы

```bash
git config --global alias.st status
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.lg "log --oneline --graph --all --decorate"
```

## .gitattributes

Управляет тем, как git обрабатывает файлы (line endings, какие файлы считать бинарными, diff-стратегии):

```
* text=auto eol=lf
*.png binary
*.sh text eol=lf
```

#git #devops #version-control
