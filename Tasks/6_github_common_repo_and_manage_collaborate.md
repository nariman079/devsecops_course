# Практическое задание: Создание общего репозитория и управление командой

## Задание 1: Создание общего репозитория

### 1.1 Создание репозитория на GitHub

```bash
# Создайте репозиторий через GitHub CLI
gh repo create team-project --public --description "Team project for collaborative development"

# Или создайте репозиторий через веб-интерфейс GitHub:
# 1. Зайдите на https://github.com/new
# 2. Введите имя репозитория: team-project
# 3. Выберите публичный тип
# 4. Добавьте описание
# 5. Нажмите "Create repository"
```

**Зачем это нужно знать:** Создание общего репозитория - первый шаг к командной разработке. Публичный репозиторий позволяет всем членам команды легко получить доступ к коду.

### 1.2 Инициализация локального репозитория

```bash
# Создайте локальный репозиторий
mkdir team-project
cd team-project
git init

# Создайте основные файлы проекта
echo "# Team Project" > README.md
echo "node_modules/" > .gitignore
mkdir src docs tests

# Сделайте первый коммит
git add .
git commit -m "Initial commit: add basic project structure"

# Свяжите с удаленным репозиторием
git remote add origin https://github.com/username/team-project.git

# Запушите в master ветку
git branch -M master
git push -u origin master
```

**Зачем это нужно знать:** Правильная инициализация локального репозитория и его связь с удаленным - основа для успешной командной работы.


### 2.2 Добавление участников через веб-интерфейс

```bash
# Через веб-интерфейс GitHub:
# 1. Зайдите в репозиторий
# 2. Нажмите "Settings" (Настройки)
# 3. В левом меню выберите "Collaborators and teams"
# 4. Нажмите "Add people"
# 5. Введите имя пользователя GitHub
# 6. Выберите роль (обычно "Write")
# 7. Нажмите "Add <username> to this repository"
```

**Зачем это нужно знать:** Умение добавлять участников в проект необходимо для организации командной разработки.

## Задание 3: Раздача ролей и прав доступа

### 3.1 Понимание ролей в GitHub

```bash
# Роли участников:
# - Read: может просматривать код и создавать issues
# - Write: может коммитить в ветки (кроме защищенных)
# - Maintain: может управлять ветками и релизами
# - Admin: полный доступ, включая настройки репозитория
# - Owner: владелец репозитория (только для организации)
```

**Зачем это нужно знать:** Правильное распределение ролей обеспечивает безопасность и эффективность командной работы.


### 4.2 Настройка ветвевых правил через веб-интерфейс

```bash
# Через веб-интерфейс GitHub:
# 1. Зайдите в репозиторий
# 2. Нажмите "Settings" (Настройки)
# 3. В левом меню выберите "Branches"
# 4. Нажмите "Add rule" (Добавить правило)
# 5. В поле "Branch name pattern" введите: master
# 6. Настройте правила:
#    - Require a pull request before merging
#    - Require approvals: 2
#    - Dismiss stale pull request approvals when new commits are pushed
#    - Require review from code owners
#    - Require status checks to pass before merging
#    - Require branches to be up to date before merging
# 7. Нажмите "Create"
```

### 4.3 Дополнительные настройки защиты (не делать)

```bash
# Запретить форсированный push
gh api repos/username/team-project/branches/master/protection --method PATCH \
  -f allow_force_pushes=false

# Запретить удаление ветки
gh api repos/username/team-project/branches/master/protection --method PATCH \
  -f allow_deletions=false

# Требовать signed commits
gh api repos/username/team-project/branches/master/protection --method PATCH \
  -f required_signatures=true
```

**Зачем это нужно знать:** Защита основной ветки предотвращает случайное повреждение кода и обеспечивает качество через code review.


## Задание 6: Практический сценарий командной настройки

### 6.1 Создание командного репозитория

1. **Создание репозитория:**
   ```bash
   # Владелец создает репозиторий
   gh repo create e-commerce-team --public --description "E-commerce platform team project"
   ```

2. **Инициализация проекта:**
   ```bash
   # Владелец инициализирует локальный репозиторий
   mkdir e-commerce-team
   cd e-commerce-team
   git init
   
   # Создает структуру проекта
   mkdir src docs tests
   echo "node_modules/" > .gitignore
   echo "# E-commerce Platform" > README.md
   
   # Первый коммит
   git add .
   git commit -m "Initial commit: basic project structure"
   git remote add origin https://github.com/owner/e-commerce-team.git
   git push -u origin master
   ```


### 6.2 Работа команды

1. **Клонирование репозитория каждым участником:**
   ```bash
   git clone https://github.com/owner/e-commerce-team.git
   cd e-commerce-team
   ```

2. **Настройка локальной среды:**
   ```bash
   # Каждый участник настраивает свои Git-параметры
   git config user.name "Имя Фамилия"
   git config user.email "имя@почта.com"
   ```

3. **Создание feature-веток:**
   ```bash
   # Разработчик 1: корзина покупок
   git checkout -b feature/shopping-cart
   # Разработка...
   git push -u origin feature/shopping-cart
   
   # Разработчик 2: оплата
   git checkout -b feature/payment
   # Разработка...
   git push -u origin feature/payment
   ```

4. **Создание Pull Request:**
   ```bash
   # Каждый разработчик создает PR через веб-интерфейс GitHub
   # 1. Зайдите в репозиторий
   # 2. Нажмите "Pull requests"
   # 3. Нажмите "New pull request"
   # 4. Выберите base: master, compare: feature/название-ветки
   # 5. Заполните описание и создайте PR
   ```

5. **Code review:**
   ```bash
   # Другие участники команды проверяют код
   # - Проверяют логику
   # - Ищут потенциальные баги
   # - Проверяют соответствие стандартам кодирования
   # - Дают одобрение или запрашивают изменения
   ```

## Контрольные вопросы

1. Какие существуют роли участников в GitHub и чем они отличаются?
2. Зачем нужно защищать основную ветку репозитория?
3. Какие элементы ветвевого правила вы считаете наиболее важными?
4. В чем преимущества использования GitHub Actions для CI/CD?
5. Как правильно организовать процесс code review в команде?

## Дополнительные задания

1. Настройте автоматическое назначение reviewers для Pull Request
2. Создайте template для Pull Request
3. Настройте автоматическое создание issues из Pull Request
***
5. Настройте уведомления о событиях в репозитории
4. Реализуйте автоматическое развертывание на staging-среду (задание только для тех, кто поверил в себя)
***

Это задание охватывает все аспекты создания и настройки общего репозитория для командной разработки, включая управление доступом и защиту кода.
