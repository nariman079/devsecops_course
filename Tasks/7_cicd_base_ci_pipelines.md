# Практическое задание: Базовый CI с GitHub Actions

## Задание 1: Создание структуры GitHub Actions

### 1.1 Создание директории workflows

```bash
# Создайте репозиторий для практики
mkdir ci-practice-project
cd ci-practice-project
git init

# Создайте основную структуру проекта
mkdir src tests docs
echo "console.log('Hello CI!');" > src/index.js
echo "node_modules/" > .gitignore
echo "# CI Practice Project" > README.md

# Создайте директорию для GitHub Actions
mkdir -p .github/workflows

# Сделайте первый коммит
git add .
git commit -m "Initial commit: project structure and GitHub Actions setup"
```

**Зачем это нужно знать:** Директория `.github/workflows/` является стандартным местом для хранения файлов GitHub Actions. Все YAML-файлы в этой директории автоматически распознаются GitHub как workflow-конфигурации.

### 1.2 Создание базовых workflow файлов

```bash
# Создайте файл main.yml
cat > .github/workflows/main.yml << 'EOF'
name: Main Pipeline

on: [push]
jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
EOF

# Создайте файл ci.yml
cat > .github/workflows/ci.yml << 'EOF'
name: CI Pipeline

on:
  push:
    branches: [ develop ]
  pull_request:
    branches: [ develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
EOF

# Создайте файл dev.yml
cat > .github/workflows/dev.yml << 'EOF'
name: Development Pipeline

on:
  push:
    branches: [ develop ]

jobs:
  deploy-dev:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
EOF

# Создайте файл prod.yml
cat > .github/workflows/prod.yml << 'EOF'
name: Production Pipeline

on:
  push:
    branches: [ main, master ]

jobs:
  deploy-prod:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
EOF
```

**Зачем это нужно знать:** Разделение workflow на разные файлы позволяет настраивать разные процессы для разных веток и окружений, что является лучшей практикой CI/CD.

## Задание 2: Создание базового pipeline

### 2.1 Написание простого workflow

```yml
# Создайте простой workflow (gihub/workflows/test.yml) для проверки `.
name: Simple Test Workflow

on: [push, pull_request]

jobs:
  hello-world:
    runs-on: ubuntu-latest
    
    steps:
    - name: Hello World
      run: echo "Hello, GitHub Actions!"
      
    - name: check java lint
      run: echo "Потипу какойта этап который проверяет код"
      
    - name: Test java
      run: echo "Потипу  еще один этап, который тестировает ваш код "
```

**Зачем это нужно знать:** Простой workflow помогает понять базовую структуру GitHub Actions и проверить, что система работает корректно.

### 2.2 Создание workflow с ошибкой для тестирования

```yaml
# Создайте workflow (.github/workflows/error-test.yml) с намеренной ошибкой
name: Error Test Workflow

on: [push]

jobs:
  test-error:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Create test file
      run: echo "test content" > test.txt
      
    - name: Read non-existent file (will fail)
      run: cat nonexistent.txt
      
    - name: This step will not run
      run: echo "This will not execute"
```

**Зачем это нужно знать:** Тестирование workflow с ошибками помогает понять, как GitHub Actions обрабатывает сбои и как выглядит отчет об ошибках.

## Задание 3: Проверка работы pipeline в GitHub Actions
 

### 3.2 Проверка запуска workflow

```bash
# 1. Зайдите в репозиторий на GitHub
# 2. Нажмите на вкладку "Actions"
# 3. Вы увидите список запущенных workflow
# 4. Кликните на любой workflow для просмотра деталей
```

**Зачем это нужно знать:** Понимание того, как проверять статус и результаты workflow, необходимо для отладки и мониторинга CI/CD процессов.

### 3.3 Анализ результатов workflow

```bash
# Просмотрите логи конкретного workflow 

# 1. В разделе Actions выберите нужный workflow
# 2. Нажмите на него для просмотра деталей
# 3. Разверните каждый шаг для просмотра логов
# 4. Обратите внимание на статус (зеленый - успех, красный - ошибка)
```

**Зачем это нужно знать:** Анализ логов workflow помогает находить и исправлять ошибки в CI/CD процессах.

## Задание 4: Практический сценарий


## Контрольные вопросы

1. Зачем нужно разделять workflow на разные файлы?
2. Какие события могут запускать workflow в GitHub Actions?
3. В чем разница между `push` и `pull_request` событиями?
4. Зачем нужно кэшировать зависимости в workflow?
5. Как проверить статус выполнения workflow?

## Дополнительные задания (Только для ярых кодеров)

1. Создайте workflow, который запускается по расписанию (cron)
2. Настройте workflow для автоматического создания релизов
3. Создайте workflow для проверки безопасности кода
4. Настройте уведомления о результатах workflow в Slack
5. Создайте workflow для автоматического деплоя на GitHub Pages

Это задание охватывает все основные аспекты создания и настройки базового CI с GitHub Actions, включая создание workflow, их тестирование и отладку.
