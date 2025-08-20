# Git Workflow и Версионирование

Полное руководство по организации работы с Git, ветвлению и версионированию проектов.

## 📋 Содержание

- [Структура веток](#структура-веток)
- [Версионирование](#версионирование)
- [Работа с Pull Request](#работа-с-pull-request)
- [Примеры команд](#примеры-команд)
- [Лучшие практики](#лучшие-практики)

## 🌳 Структура веток

### Основные ветки

#### `main` / `master`
- **Назначение**: Стабильная версия, готовая к релизу
- **Особенности**: 
  - Всегда в рабочем состоянии
  - Только проверенный код
  - Защищена от прямых коммитов

#### `develop`
- **Назначение**: Основная ветка разработки
- **Особенности**:
  - Интеграция всех новых фич
  - Может быть нестабильной
  - Не обязательна для маленьких проектов

### Рабочие ветки

#### `feature/имя-функции`
- **Создание**: От `develop` или `main`
- **Назначение**: Разработка новых функций
- **Жизненный цикл**: Создание → Разработка → Pull Request → Merge → Удаление

```bash
# Создание
git checkout develop
git pull origin develop
git checkout -b feature/user-authentication

# После завершения
git checkout develop
git merge feature/user-authentication
git branch -d feature/user-authentication
```

#### `bugfix/описание-бага`
- **Создание**: От `develop` или `main`
- **Назначение**: Исправление обычных багов
- **Примеры названий**: 
  - `bugfix/login-validation`
  - `bugfix/memory-leak-fix`

#### `hotfix/критический-баг`
- **Создание**: Напрямую от `main`
- **Назначение**: Критические исправления в продакшене
- **Особенности**: Мержится сразу в `main` и `develop`

```bash
# Создание hotfix
git checkout main
git pull origin main
git checkout -b hotfix/security-vulnerability

# После исправления
git checkout main
git merge hotfix/security-vulnerability
git checkout develop
git merge hotfix/security-vulnerability
```

#### `release/x.y.z`
- **Создание**: От `develop`
- **Назначение**: Подготовка к релизу
- **Содержимое**: Только багфиксы и мелкие правки
- **Завершение**: Merge в `main` + создание тега

## 🏷️ Версионирование

### Semantic Versioning (SemVer)

Формат: `MAJOR.MINOR.PATCH`

#### MAJOR (X.0.0)
- **Когда увеличивать**: Ломающие изменения API
- **Примеры**:
  - Удаление публичных методов
  - Изменение структуры ответа API
  - Изменение поведения существующих функций

#### MINOR (0.X.0)
- **Когда увеличивать**: Новые функции без поломки совместимости
- **Примеры**:
  - Добавление новых API методов
  - Новые опциональные параметры
  - Улучшения производительности

#### PATCH (0.0.X)
- **Когда увеличивать**: Багфиксы и мелкие изменения
- **Примеры**:
  - Исправление ошибок
  - Обновления документации
  - Рефакторинг без изменения API

### Примеры версионирования

```
1.0.0   → Первый стабильный релиз
1.0.1   → Исправлен баг авторизации
1.1.0   → Добавлена система уведомлений
1.1.1   → Исправлена ошибка в уведомлениях
1.2.0   → Добавлен экспорт в PDF
2.0.0   → Новый API, несовместимый с v1
2.0.1   → Исправлены миграционные скрипты
```

### Pre-release версии

```
1.0.0-alpha.1    → Альфа-версия
1.0.0-beta.1     → Бета-версия  
1.0.0-rc.1       → Release Candidate
```

## 🔄 Работа с Pull Request

### Создание PR

1. **Название**: Четко отражает суть изменений
   ```
   ✅ Хорошо: "Добавить аутентификацию через OAuth2"
   ❌ Плохо: "Фиксы", "Обновления"
   ```

2. **Описание**: Включает контекст и детали
   ```markdown
   ## Описание
   Реализована система аутентификации через OAuth2
   
   ## Изменения
   - Добавлен OAuth2 провайдер
   - Обновлена страница логина
   - Добавлены тесты
   
   ## Тестирование
   - [ ] Проверена авторизация через Google
   - [ ] Проверена авторизация через GitHub
   ```

3. **Один PR = Одна задача**: Не смешивайте разные изменения

### Code Review чеклист

#### Для автора PR:
- [ ] Код покрыт тестами
- [ ] Обновлена документация
- [ ] Проверена работоспособность
- [ ] Удалены debug-комментарии
- [ ] Соблюден стиль кодирования

#### Для ревьюера:
- [ ] Логика понятна и корректна
- [ ] Нет дублирования кода
- [ ] Соблюдены принципы безопасности
- [ ] Производительность не пострадала
- [ ] Тесты покрывают основные сценарии

## 💻 Примеры команд

### Настройка Git Flow

```bash
# Инициализация Git Flow
git flow init

# Или настройка вручную
git checkout -b develop main
git push -u origin develop
```

### Работа с фичами

```bash
# Начать новую фичу
git flow feature start user-profile

# Или вручную
git checkout develop
git pull origin develop
git checkout -b feature/user-profile

# Завершить фичу
git flow feature finish user-profile

# Или вручную
git checkout develop
git merge feature/user-profile
git branch -d feature/user-profile
git push origin develop
```

### Создание релиза

```bash
# Начать релиз
git flow release start 1.2.0

# Вручную
git checkout develop
git pull origin develop
git checkout -b release/1.2.0

# Завершить релиз
git flow release finish 1.2.0

# Вручную
git checkout main
git merge release/1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"
git checkout develop
git merge release/1.2.0
git push origin main develop --tags
```

### Работа с тегами

```bash
# Создание аннотированного тега
git tag -a v1.2.0 -m "Release version 1.2.0"

# Отправка тегов на сервер
git push origin --tags

# Просмотр всех тегов
git tag -l

# Создание тега с подписью
git tag -s v1.2.0 -m "Signed release 1.2.0"
```

## ✨ Лучшие практики

### Именование веток

```bash
# Используйте описательные имена
feature/add-payment-gateway
bugfix/fix-memory-leak
hotfix/security-patch-cve-2023-1234
release/2.1.0

# Избегайте
feature/stuff
bugfix/fix
hotfix/urgent
```

### Сообщения коммитов

Используйте [Conventional Commits](https://www.conventionalcommits.org/):

```bash
feat: добавить OAuth2 аутентификацию
fix: исправить утечку памяти в кеше
docs: обновить README с примерами API
style: исправить форматирование кода
refactor: переписать модуль аутентификации
test: добавить тесты для payment gateway
chore: обновить зависимости
```

### Защита веток

В настройках репозитория настройте:
- Защиту ветки `main`
- Обязательный code review
- Прохождение CI/CD тестов
- Обновленность с базовой веткой

### Автоматизация

```yaml
# .github/workflows/release.yml
name: Create Release
on:
  push:
    tags:
      - 'v*'
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Create Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref }}
          release_name: Release ${{ github.ref }}
```

### Полезные алиасы Git

```bash
# Добавить в ~/.gitconfig
[alias]
    co = checkout
    br = branch
    ci = commit
    st = status
    unstage = reset HEAD --
    last = log -1 HEAD
    visual = !gitk
    tree = log --oneline --decorate --graph --all
    amend = commit --amend --no-edit
```
