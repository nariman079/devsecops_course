# Практическое задание: Базовый CI Pipeline с GitHub Actions (только через веб-интерфейс)

## Задание 1: Создание репозитория через веб-интерфейс

### 1.1 Создание нового репозитория

1. **Перейдите на GitHub:**
   - Откройте браузер и перейдите на https://github.com
   - Авторизуйтесь в своей учетной записи

2. **Создайте новый репозиторий:**
   - Нажмите на значок "+" в правом верхнем углу
   - Выберите "New repository"
   - Заполните поля:
     - Repository name: `ci-pipeline-practice`
     - Description: `Practice repository for CI pipeline`
     - Visibility: Public
     - Check "Add a README file"
     - Check "Add .gitignore" → выберите "Node"
     - Check "Choose a license" → выберите "MIT License"
   - Нажмите "Create repository"

**Зачем это нужно знать:** Создание репозитория через веб-интерфейс - это основной способ для большинства пользователей. Понимание всех опций при создании помогает правильно настроить проект с самого начала.

### 1.2 Создание структуры проекта

1. **Создайте директорию для GitHub Actions:**
   - В репозитории нажмите "Add file" → "Create new file"
   - В поле имени файла введите: `.github/workflows/main.yml`
   - Нажмите "Commit new file" внизу страницы

2. **Создайте основные файлы проекта:**
   - Создайте файл `app.js`:
     ```javascript
     console.log("Hello CI Pipeline!");
     ```
   - Создайте файл `script.sh`:
     ```bash
     #!/bin/bash
     echo "This is a test script"
     echo "Current directory: $(pwd)"
     echo "Files in directory: $(ls -la)"
     ```
   - Создайте файл `test.java`:
     ```java
     public class Test {
         public static void main(String[] args) {
             System.out.println("Hello from Java!");
         }
     }
     ```

**Зачем это нужно знать:** Работа с файлами через веб-интерфейс GitHub позволяет быстро создавать и редактировать файлы без необходимости локальной разработки.

## Задание 2: Написание базового CI pipeline с 3-4 этапами

### 2.1 Создание простого workflow с echo командами

1. **Откройте файл `.github/workflows/main.yml`**
   - В репозитории перейдите в папку `.github/workflows/`
   - Нажмите на файл `main.yml`
   - Нажмите на значок карандаша (Edit this file) в правом верхнем углу

2. **Напишите базовый workflow:**
   ```yaml
   name: Basic CI Pipeline
   
   on: [push, pull_request]
   
   jobs:
     build:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Step 1 - Echo Hello
         run: echo "Hello from Step 1"
         
       - name: Step 2 - Echo World
         run: echo "World from Step 2"
         
       - name: Step 3 - Echo CI
         run: echo "CI from Step 3"
         
       - name: Step 4 - Echo Pipeline
         run: echo "Pipeline from Step 4"
   ```

3. **Сохраните изменения:**
   - Прокрутите вниз до раздела "Commit changes"
   - В поле "Commit message" введите: "Add basic CI pipeline with echo steps"
   - Нажмите "Commit changes"

**Зачем это нужно знать:** Понимание структуры workflow (jobs, steps, run) является основой для создания любых CI/CD pipeline в GitHub Actions.

### 2.2 Создание workflow с несколькими jobs

1. **Создайте файл `.github/workflows/multi-job.yml`:**
   ```yaml
   name: Multi-Job Pipeline
   
   on: [push, pull_request]
   
   jobs:
     job1:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Job 1 Step 1
         run: echo "Job 1, Step 1"
         
       - name: Job 1 Step 2
         run: echo "Job 1, Step 2"
         
     job2:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Job 2 Step 1
         run: echo "Job 2, Step 1"
         
       - name: Job 2 Step 2
         run: echo "Job 2, Step 2"
   ```

**Зачем это нужно знать:** Многозадачные workflow позволяют выполнять независимые операции параллельно, что ускоряет процесс CI/CD.

## Задание 3: CI pipeline с bash операциями

### 3.1 Создание workflow для работы с файлами

1. **Создайте файл `.github/workflows/file-operations.yml`:**
   ```yaml
   name: File Operations Pipeline
   
   on: [push, pull_request]
   
   jobs:
     file-operations:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: List files in repository
         run: ls -la
         
       - name: Create new file
         run: echo "This file was created by CI pipeline" > created_by_ci.txt
         
       - name: Show created file
         run: cat created_by_ci.txt
         
       - name: Create directory structure
         run: |
           mkdir -p test_dir/subdir
           echo "Directory created" > test_dir/subdir/test.txt
           ls -la test_dir/
   ```

**Зачем это нужно знать:** Умение выполнять bash операции в CI pipeline необходимо для автоматизации различных задач, таких как создание файлов, запуск скриптов и управление файловой системой.

### 3.2 Создание workflow для запуска Java кода

1. **Создайте файл `.github/workflows/java-pipeline.yml`:**
   ```yaml
   name: Java Pipeline
   
   on: [push, pull_request]
   
   jobs:
     java-build:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Set up JDK 11
         uses: actions/setup-java@v3
         with:
           java-version: '11'
           distribution: 'temurin'
           
       - name: Compile Java code
         run: javac Test.java
         
       - name: Run Java program
         run: java Test
         
       - name: List compiled files
         run: ls -la *.class
   ```

**Зачем это нужно знать:** Запуск Java кода в CI pipeline позволяет автоматически компилировать и тестировать Java приложения при каждом изменении кода.

### 3.3 Создание workflow для запуска тестов

1. **Создайте файл `.github/workflows/test-pipeline.yml`:**
   ```yaml
   name: Test Pipeline
   
   on: [push, pull_request]
   
   jobs:
     tests:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Create test files
         run: |
           echo "console.log('Test 1 passed');" > test1.js
           echo "console.log('Test 2 passed');" > test2.js
           echo "console.log('Test 3 passed');" > test3.js
           
       - name: Run JavaScript tests
         run: |
           node test1.js
           node test2.js
           node test3.js
           
       - name: Create test summary
         run: |
           echo "## Test Results" > test_results.md
           echo "- Test 1: ✅ PASSED" >> test_results.md
           echo "- Test 2: ✅ PASSED" >> test_results.md
           echo "- Test 3: ✅ PASSED" >> test_results.md
           cat test_results.md
   ```

**Зачем это нужно знать:** Автоматический запуск тестов в CI pipeline обеспечивает качество кода и помогает выявлять проблемы на ранних этапах разработки.

## Задание 4: Проверка работы pipeline

### 4.1 Запуск workflow

1. **Инициируйте запуск workflow:**
   - Внесите любое изменение в репозиторий (например, добавьте комментарий в любой файл)
   - Зафиксируйте изменения: Commit changes
   - Это автоматически запустит workflow, так как он настроен на срабатывание при push

2. **Проверка статуса workflow:**
   - Перейдите в репозитории на вкладку "Actions"
   - Вы увидите список запущенных workflow
   - Нажмите на любой workflow для просмотра деталей выполнения

### 4.2 Анализ результатов

1. **Просмотр логов выполнения:**
   - В разделе Actions выберите нужный workflow
   - Нажмите на него для просмотра деталей
   - Разверните каждый шаг для просмотра логов
   - Обратите внимание на статус каждого шага (зеленый - успех, красный - ошибка)

2. **Проверка созданных файлов:**
   - Вернитесь в основной репозиторий
   - Проверьте, были ли созданы файлы, указанные в workflow
   - Обратите внимание, что файлы, созданные в workflow, не сохраняются в репозитории (это временные файлы)

**Зачем это нужно знать:** Понимание того, как проверять и анализировать результаты workflow, необходимо для отладки и мониторинга CI/CD процессов.

## Задание 5: Практический сценарий

### 5.1 Создание комплексного workflow

1. **Создайте файл `.github/workflows/complete-pipeline.yml`:**
   ```yaml
   name: Complete CI Pipeline
   
   on: [push, pull_request]
   
   jobs:
     setup:
       runs-on: ubuntu-latest
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Setup environment
         run: |
           echo "Setting up environment..."
           echo "OS: $(uname -s)"
           echo "Architecture: $(uname -m)"
           echo "Current time: $(date)"
           
     build:
       runs-on: ubuntu-latest
       needs: setup
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Build application
         run: |
           echo "Building application..."
           echo "Creating build artifacts..."
           mkdir -p build
           echo "Build completed successfully"
           
     test:
       runs-on: ubuntu-latest
       needs: build
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Run tests
         run: |
           echo "Running tests..."
           echo "Test 1: Checking file structure"
           ls -la
           echo "Test 2: Checking build directory"
           ls -la build/
           echo "All tests passed!"
           
     deploy:
       runs-on: ubuntu-latest
       needs: test
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Deploy application
         run: |
           echo "Deploying application..."
           echo "Deployment completed successfully"
   ```

**Зачем это нужно знать:** Комплексный workflow с несколькими зависимыми jobs демонстрирует реальный CI/CD процесс с этапами setup, build, test и deploy.

## Контрольные вопросы

1. В чем разница между job, step и run в GitHub Actions?
2. Какие события могут запускать workflow?
3. Зачем нужно использовать `needs` в workflow?
4. Почему файлы, созданные в workflow, не сохраняются в репозитории?
5. Как проверить статус выполнения workflow?

## Дополнительные задания

1. Создайте workflow, который запускается только при push в ветку main
2. Добавьте в workflow шаг, который создает архив с файлами проекта
3. Создайте workflow, который проверяет наличие определенных файлов в репозитории
4. Добавьте в workflow шаг, который выводит информацию о коммите
5. Создайте workflow с условными шагами (if conditions)

Это задание охватывает все основные аспекты создания CI pipeline с GitHub Actions через веб-интерфейс, включая работу с файлами, bash операции и запуск различных типов кода.
