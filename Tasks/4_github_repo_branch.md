# Практическое задание: Работа с репозиторием и ветками Git

## Задание 1: Настройка репозитория

### 1.1 Создание и настройка репозитория

```bash
# Создайте новый репозиторий
mkdir my-project
cd my-project
git init

# Настройте глобальные параметры пользователя
git config --global user.name "Ваше Имя"
git config --global user.email "ваш@email.com"

# Настройте параметры для текущего репозитория
git config user.name "Ваше Имя"
git config user.email "ваш@email.com"

# Проверьте настройки
git config --list
```

**Зачем это нужно знать:** Правильная настройка пользователя позволяет идентифицировать автора коммитов. Глобальные настройки применяются ко всем репозиториям, а локальные - только к конкретному проекту.

### 1.2 Управление видимостью репозитория

```bash
# Создайте репозиторий на GitHub через веб-интерфейс
# или используйте GitHub CLI:
gh repo create my-project --private

# Сделайте репозиторий публичным
gh api repos/username/my-project --method PATCH -f visibility=public

# Сделайте репозиторий приватным
gh api repos/username/my-project --method PATCH -f visibility=private

# Удаление репозитория
gh api repos/username/my-project --method DELETE
```

**Зачем это нужно знать:** Контроль над видимостью репозитория важен для защиты коммерческой тайны и управления доступом к коду.

## Задание 2: Работа с файлами и коммитами

### 2.1 Создание и управление файлами

```bash
# Создайте файлы
echo "console.log('Hello, World!');" > app.js
echo "body { font-family: Arial; }" > style.css
mkdir src
echo "const config = {};" > src/config.js

# Проверьте статус
git status

# Добавьте файлы в индекс
git add app.js style.css
git add src/  # добавить всю папку

# Или добавьте все файлы
git add .

# Сделайте коммит
git commit -m "Initial commit: add basic project structure"

# Просмотр истории коммитов
git log --oneline
```

**Зачем это нужно знать:** Понимание жизненного цикла файлов в Git (untracked → staged → committed) необходимо для эффективной работы с версиями кода.

### 2.2 Работа с коммитами

```bash
# Создайте изменения в файлах
echo "console.log('Updated!');" >> app.js

# Просмотр изменений
git diff
git diff --staged

# Добавьте изменения
git add app.js

# Измените последний коммит
git commit --amend -m "Initial commit: add basic project structure and update app.js"

# Создайте новый коммит
git commit -am "Update app.js with new functionality"

# Отмена последнего коммита (оставляя изменения в рабочей директории)
git reset --soft HEAD~1

# Полная отмена последнего коммита
git reset --hard HEAD~1
```

**Зачем это нужно знать:** Умение управлять коммитами позволяет поддерживать чистую историю изменений и исправлять ошибки.

## Задание 3: Работа с ветками

### 3.1 Создание и управление ветками

```bash
# Просмотр существующих веток
git branch

# Создание новой ветки
git branch feature-login
git branch feature-auth

# Переход на ветку
git checkout feature-login
# или в новых версиях Git:
git switch feature-login

# Создание и переход на новую ветку за один шаг
git checkout -b feature-payment
# или:
git switch -c feature-payment

# Просмотр текущей ветки
git branch --show-current
```

**Зачем это нужно знать:** Ветвление позволяет работать над разными функциями параллельно, не мешая основной разработке.

### 3.2 GitFlow ветки

```bash
# Создание основных веток GitFlow
git checkout -b master
git checkout -b develop
git checkout -b release/v1.0.0

# Работа с feature-ветками
git checkout develop
git checkout -b feature/user-management

# После завершения feature
git checkout develop
git merge feature/user-management
git branch -d feature/user-management  # удаление ветки

# Работа с release-веткой
git checkout release/v1.0.0
git merge develop

# После тестирования и фиксации багов
git checkout master
git merge release/v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

git checkout develop
git merge release/v1.0.0
git branch -d release/v1.0.0
```

**Зачем это нужно знать:** GitFlow - это стандартная модель ветвления, используемая во многих командах для организации разработки.

### 3.3 Удаление веток

```bash
# Удаление локальной ветки
git branch -d feature-old  # безопасное удаление
git branch -D feature-old  # принудительное удаление

# Удаление удаленной ветки
git push origin --delete feature-old

# Просмотр удаленных веток (уже не существующих на удаленном репозитории)
git remote prune origin
```

**Зачем это нужно знать:** Регулярная очистка неиспользуемых веток помогает поддерживать порядок в репозитории.

## Задание 4: Практический сценарий

### Создание полноценного проекта с GitFlow

1. **Инициализация проекта:**
   ```bash
   mkdir e-commerce-store
   cd e-commerce-store
   git init
   git remote add origin https://github.com/username/e-commerce-store.git
   ```

2. **Создание основных веток:**
   ```bash
   # Создайте и настройте основные ветки
   git checkout -b master
   git checkout -b develop
   ```

3. **Разработка функций:**
   ```bash
   # Разработка корзины покупок
   git checkout develop
   git checkout -b feature/shopping-cart
   
   # Создайте файлы корзины
   mkdir src/cart
   echo "export class ShoppingCart {}" > src/cart/index.js
   
   git add .
   git commit -m "Add shopping cart basic structure"
   
   # Объединение в develop
   git checkout develop
   git merge feature/shopping-cart
   git branch -d feature/shopping-cart
   
   # Разработка оплаты
   git checkout -b feature/payment
   mkdir src/payment
   echo "export class PaymentProcessor {}" > src/payment/index.js
   
   git add .
   git commit -m "Add payment processing module"
   ```

4. **Создание релиза:**
   ```bash
   # Создание релизной ветки
   git checkout -b release/v1.0.0
   
   # Тестирование и фиксация багов
   echo "Bug fixes and testing" >> README.md
   git add README.md
   git commit -m "Fix bugs and update documentation"
   
   # Релиз
   git checkout master
   git merge release/v1.0.0
   git tag -a v1.0.0 -m "First stable release"
   
   git checkout develop
   git merge release/v1.0.0
   
   # Очистка
   git branch -d release/v1.0.0
   ```

5. **Работа с hotfix:**
   ```bash
   # Если найдена критическая ошибка
   git checkout -b hotfix/critical-bug master
   
   # Внесение исправления
   echo "Critical bug fix" >> src/app.js
   git add src/app.js
   git commit -m "Fix critical security vulnerability"
   
   # Объединение в master и develop
   git checkout master
   git merge hotfix/critical-bug
   git tag -a v1.0.1 -m "Hotfix release"
   
   git checkout develop
   git merge hotfix/critical-bug
   
   git branch -d hotfix/critical-bug
   ```

## Контрольные вопросы

1. Какие преимущества дает использование GitFlow?
2. В чем разница между `git reset --soft` и `git reset --hard`?
3. Зачем нужно удалять ветки после их слияния?
4. Как правильно организовать работу над несколькими функциями одновременно?
5. Когда использовать `git add .` и когда `git add <filename>`?

## Дополнительные задания

1. Настройте `.gitignore` для проекта
