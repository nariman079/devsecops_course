# Практическое задание: Работа с файлами, коммитами и ветками Git

## Задание 1: Работа с файлами

### 1.1 Создание и управление файлами

```bash
# Создайте новый репозиторий
mkdir team-project
cd team-project
git init

# Создайте структуру проекта
mkdir src docs tests
echo "console.log('Main application');" > src/index.js
echo "# Project Documentation" > docs/README.md
echo "const test = require('./src/index.js');" > tests/test.js

# Проверьте статус файлов
git status

# Добавьте файлы в индекс
git add src/index.js
git add docs/README.md
git add tests/test.js

# Или добавьте все файлы сразу
git add .

# Просмотрите изменения, которые будут закоммичены
git diff --staged
```

**Зачем это нужно знать:** Понимание жизненного цикла файлов в Git (untracked → staged → committed) позволяет эффективно контролировать, какие изменения попадут в коммит.

### 1.2 Работа с изменениями в файлах

```bash
# Внесите изменения в файл
echo "console.log('Updated application');" >> src/index.js

# Просмотрите незакоммиченные изменения
git diff

# Добавьте изменения в индекс
git add src/index.js

# Просмотрите изменения, готовые к коммиту
git diff --staged

# Отмените добавление файла в индекс (не трогая изменения в файле)
git reset HEAD src/index.js

# Или отмените изменения в файле полностью
git checkout -- src/index.js
```

**Зачем это нужно знать:** Умение управлять изменениями позволяет точно контролировать, какие правки попадут в историю проекта.

## Задание 2: Работа с коммитами

### 2.1 Создание коммитов

```bash
# Сделайте первый коммит
git commit -m "Initial commit: add basic project structure"

# Создайте изменения и закоммитьте их
echo "console.log('New feature');" > src/feature.js
git add src/feature.js
git commit -m "Add new feature module"

# Создайте коммит со всеми изменениями сразу
git commit -am "Update all modified files"

# Просмотрите историю коммитов
git log --oneline
git log --graph --oneline --all
```

**Зачем это нужно знать:** Правильное создание коммитов с понятными сообщениями помогает отслеживать историю изменений и находить нужные правки.

### 2.2 Работа с последним коммитом

```bash
# Измените последний коммит (добавьте файлы или измените сообщение)
echo "console.log('Additional change');" >> src/index.js
git add src/index.js
git commit --amend -m "Initial commit: add basic project structure and additional changes"

# Просмотрите изменения в последнем коммите
git show HEAD

# Отмените последний коммит, оставив изменения в рабочей директории
git reset --soft HEAD~1

# Отмените последний коммит и все изменения
git reset --hard HEAD~1
```

**Зачем это нужно знать:** Умение редактировать и отменять коммиты позволяет поддерживать чистую историю разработки.

## Задание 3: Работа с ветками

### 3.1 Создание и переключение веток

```bash
# Просмотрите существующие ветки
git branch

# Создайте новую ветку
git branch feature-auth

# Переключитесь на ветку
git checkout feature-auth
# или в новых версиях Git:
git switch feature-auth

# Создайте и переключитесь на новую ветку за один шаг
git checkout -b feature-payment
# или:
git switch -c feature-payment

# Проверьте текущую ветку
git branch --show-current
```

**Зачем это нужно знать:** Ветвление позволяет работать над разными функциями параллельно, не мешая основной разработке.

### 3.2 Работа с несколькими ветками

```bash
# Создайте несколько веток для разных функций
git checkout -b feature-user-management
git checkout -b feature-inventory
git checkout -b feature-analytics

# Переключайтесь между ветками
git checkout feature-user-management
git checkout feature-inventory
git checkout feature-analytics

# Просмотрите все ветки
git branch -a

# Просмотрите ветки с их последними коммитами
git branch -v
```

**Зачем это нужно знать:** Умение управлять несколькими ветками необходимо для параллельной разработки различных компонентов проекта.

### 3.3 Push веток на удаленный репозиторий

```bash
# Добавьте удаленный репозиторий
git remote add origin https://github.com/username/team-project.git

# Запушите ветку на удаленный репозиторий
git push -u origin feature-auth

# Запушите все локальные ветки на удаленный репозиторий
git push --all origin

# Просмотрите удаленные ветки
git branch -r
git branch -a
```

**Зачем это нужно знать:** Push веток на удаленный репозиторий позволяет команде работать над одними и теми же ветками.

### 3.4 GitFlow ветки

```bash
# Создайте основные ветки GitFlow
git checkout -b master
git checkout -b develop

# Создайте feature-ветку от develop
git checkout develop
git checkout -b feature/shopping-cart

# После завершения работы над feature
git checkout develop
git merge feature/shopping-cart
git branch -d feature/shopping-cart

# Создайте release-ветку
git checkout -b release/v1.0.0 develop

# После тестирования и фиксации багов
git checkout master
git merge release/v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

git checkout develop
git merge release/v1.0.0
git branch -d release/v1.0.0
```

**Зачем это нужно знать:** GitFlow - это стандартная модель ветвления, используемая во многих командах для организации разработки.

### 3.5 Удаление веток

```bash
# Удалите локальную ветку
git branch -d feature-old  # безопасное удаление
git branch -D feature-old  # принудительное удаление

# Удалите ветку на удаленном репозитории
git push origin --delete feature-old

# Просмотрите ветки, которые уже не существуют на удаленном репозитории
git remote prune origin

# Удалите все локальные ветки, соответствующие удаленным веткам, которые уже не существуют
git fetch --prune
```

**Зачем это нужно знать:** Регулярная очистка неиспользуемых веток помогает поддерживать порядок в репозитории.

## Задание 4: Практический сценарий

### Создание командного проекта

1. **Инициализация проекта:**
   ```bash
   mkdir team-e-commerce
   cd team-e-commerce
   git init
   git remote add origin https://github.com/username/team-e-commerce.git
   ```

2. **Создание основных веток:**
   ```bash
   # Создайте и настройте основные ветки
   git checkout -b master
   git checkout -b develop
   
   # Запушите основные ветки
   git push -u origin master
   git push -u origin develop
   ```

3. **Разработка функций разными разработчиками:**
   ```bash
   # Разработчик 1: корзина покупок
   git checkout develop
   git checkout -b feature/shopping-cart
   mkdir src/cart
   echo "export class ShoppingCart {}" > src/cart/index.js
   git add .
   git commit -m "Add shopping cart basic structure"
   git push -u origin feature/shopping-cart
   
   # Разработчик 2: оплата
   git checkout develop
   git checkout -b feature/payment
   mkdir src/payment
   echo "export class PaymentProcessor {}" > src/payment/index.js
   git add .
   git commit -m "Add payment processing module"
   git push -u origin feature/payment
   
   # Разработчик 3: пользователи
   git checkout develop
   git checkout -b feature/users
   mkdir src/users
   echo "export class User {}" > src/users/index.js
   git add .
   git commit -m "Add user management module"
   git push -u origin feature/users
   ```

4. **Объединение функций:**
   ```bash
   # Каждый разработчик объединяет свою ветку в develop
   git checkout develop
   git merge feature/shopping-cart
   git merge feature/payment
   git merge feature/users
   
   # Удалите feature-ветки
   git branch -d feature/shopping-cart
   git branch -d feature/payment
   git branch -d feature/users
   
   # Запушите изменения в develop
   git push origin develop
   ```

5. **Создание релиза:**
   ```bash
   # Создание релизной ветки
   git checkout -b release/v1.0.0 develop
   
   # Тестирование и фиксация багов
   echo "Bug fixes and testing" >> README.md
   git add README.md
   git commit -m "Fix bugs and update documentation"
   
   # Релиз
   git checkout master
   git merge release/v1.0.0
   git tag -a v1.0.0 -m "First stable release"
   git push origin master --tags
   
   git checkout develop
   git merge release/v1.0.0
   git push origin develop
   
   # Очистка
   git branch -d release/v1.0.0
   git push origin --delete release/v1.0.0
   ```

## Контрольные вопросы

1. В чем разница между `git add .` и `git add <filename>`?
2. Какие преимущества дает использование веток?
3. Зачем нужно удалять ветки после их слияния?
4. Как правильно организовать работу над несколькими функциями одновременно?
5. В чем разница между `git reset --soft` и `git reset --hard`?

## Дополнительные задания

1. Создайте `.gitignore` для проекта
2. Настройте pre-commit хук для проверки кода
3. Создайте alias для часто используемых команд
4. Настройте автоматическое обновление ветки перед началом работы
5. Реализуйте защиту основных веток на GitHub

