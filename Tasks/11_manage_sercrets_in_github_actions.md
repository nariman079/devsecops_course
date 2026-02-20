# Практическая работа: Работа с GitHub Variables и Environments

## Цель работы
Научиться работать с переменными окружения и секретами в GitHub через веб-интерфейс, а также создавать и использовать environments.

## Задание 1: Создание репозитория

### Шаг 1: Создание нового репозитория
1. Перейдите на https://github.com
2. Нажмите на значок "+" в правом верхнем углу
3. Выберите "New repository"
4. Заполните поля:
   - Repository name: `github-variables-practice`
   - Description: `Practice repository for GitHub variables and environments`
   - Visibility: Public
   - Check "Add a README file"
5. Нажмите "Create repository"

### Шаг 2: Создание структуры проекта
1. В репозитории нажмите "Add file" → "Create new file"
2. Создайте файл `.github/workflows/variables-test.yml`
3. Создайте файл `app.js` с содержимым:
   ```javascript
   console.log("Hello from Node.js!");
   console.log("Environment:", process.env.NODE_ENV);
   console.log("API Key exists:", !!process.env.API_KEY);
   ```

## Задание 2: Работа с Repository Secrets

### Шаг 1: Добавление секретов
1. В репозитории перейдите в "Settings" (Настройки)
2. В левом меню найдите раздел "Security"
3. Выберите "Secrets and variables" → "Actions"
4. Нажмите "New repository secret"
5. Добавьте следующие секреты:
   - **SECRET_NAME**: `API_KEY`
   - **SECRET_VALUE**: `your-secret-api-key-12345`
6. Нажмите "Add secret"

### Шаг 2: Добавление переменных
1. В том же разделе найдите "Variables"
2. Нажмите "New repository variable"
3. Добавьте переменные:
   - **VARIABLE_NAME**: `NODE_ENV`
   - **VALUE**: `development`
4. Нажмите "Add variable"

## Задание 3: Создание Environments

### Шаг 1: Создание environments
1. В репозитории перейдите в "Settings" → "Environments"
2. Нажмите "New environment"
3. Создайте environment с именем `staging`
4. Нажмите "Configure environment"
5. В разделе "Environment secrets" добавьте:
   - **NAME**: `STAGING_DB_URL`
   - **VALUE**: `postgresql://user:pass@staging-db:5432/staging`
6. Нажмите "Add secret"
7. Повторите для environment `production` с секретом:
   - **NAME**: `PROD_DB_URL`
   - **VALUE**: `postgresql://user:pass@prod-db:5432/production`

## Задание 4: Создание CI Pipeline с Variables

### Шаг 1: Написание workflow
Откройте файл `.github/workflows/variables-test.yml` и добавьте следующий код:

```yaml
name: Variables Test Pipeline

on: [push, pull_request]

jobs:
  show-variables:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Show all environment variables
      run: |
        echo "=== SYSTEM VARIABLES ==="
        echo "GITHUB_REPOSITORY: $GITHUB_REPOSITORY"
        echo "GITHUB_REF: $GITHUB_REF"
        echo "GITHUB_SHA: $GITHUB_SHA"
        echo "GITHUB_WORKFLOW: $GITHUB_WORKFLOW"
        echo "GITHUB_RUN_ID: $GITHUB_RUN_ID"
        echo ""
        echo "=== CUSTOM VARIABLES ==="
        echo "NODE_ENV: $NODE_ENV"
        echo "CUSTOM_VAR: $CUSTOM_VAR"
        echo ""
        echo "=== SECRET VARIABLES ==="
        echo "API Key exists: ${{ secrets.API_KEY != '' }}"
        echo "Secret length: ${{ secrets.API_KEY }}"
        
    - name: Set custom variable
      run: echo "CUSTOM_VAR=hello-world" >> $GITHUB_ENV
      
    - name: Show custom variable
      run: echo "Custom variable: $CUSTOM_VAR"
      
    - name: Run Node.js app
      run: |
        echo "Running Node.js application..."
        node app.js
      env:
        NODE_ENV: ${{ vars.NODE_ENV }}
        API_KEY: ${{ secrets.API_KEY }}
        
    - name: Test staging environment
      run: |
        echo "Testing staging environment..."
        echo "Staging DB URL exists: ${{ secrets.STAGING_DB_URL != '' }}"
      environment: staging
      
    - name: Test production environment
      run: |
        echo "Testing production environment..."
        echo "Production DB URL exists: ${{ secrets.PROD_DB_URL != '' }}"
      environment: production
```

### Шаг 2: Сохранение workflow
1. Прокрутите вниз до раздела "Commit changes"
2. В поле "Commit message" введите: "Add variables test pipeline"
3. Нажмите "Commit changes"

## Задание 5: Тестирование Pipeline

### Шаг 1: Запуск workflow
1. Внесите любое изменение в репозиторий (например, добавьте комментарий в README.md)
2. Зафиксируйте изменения: Commit changes
3. Это автоматически запустит workflow

### Шаг 2: Проверка результатов
1. Перейдите в репозитории на вкладку "Actions"
2. Вы увидите запущенный workflow "Variables Test Pipeline"
3. Нажмите на него для просмотра деталей
4. Разверните каждый шаг и посмотрите логи

### Шаг 3: Анализ логов
Обратите внимание на:
- Как отображаются системные переменные
- Как работают кастомные переменные
- Как используются секреты
- Разницу между `vars` и `secrets`

## Задание 6: Практические эксперименты

### Эксперимент 1: Добавление новых переменных
1. Вернитесь в "Settings" → "Secrets and variables" → "Actions"
2. Добавьте новую переменную:
   - **NAME**: `APP_VERSION`
   - **VALUE**: `1.0.0`
3. Обновите workflow, добавив шаг:
   ```yaml
   - name: Show app version
     run: echo "App version: $APP_VERSION"
   ```
4. Запустите workflow и проверьте результат

### Эксперимент 2: Работа с разными environments
1. Создайте новый workflow для staging:
   ```yaml
   name: Staging Pipeline
   
   on:
     push:
       branches: [ develop ]
   
   jobs:
     deploy:
       runs-on: ubuntu-latest
       environment: staging
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Show staging variables
         run: |
           echo "Staging environment"
           echo "DB URL: ${{ secrets.STAGING_DB_URL }}"
   ```
2. Создайте workflow для production:
   ```yaml
   name: Production Pipeline
   
   on:
     push:
       branches: [ main ]
   
   jobs:
     deploy:
       runs-on: ubuntu-latest
       environment: production
       
       steps:
       - name: Checkout code
         uses: actions/checkout@v3
         
       - name: Show production variables
         run: |
           echo "Production environment"
           echo "DB URL: ${{ secrets.PROD_DB_URL }}"
   ```

## Задание 7: Понимание различий

### Разница между Variables и Secrets
- **Variables** - публичные переменные, видимые в логах
- **Secrets** - приватные переменные, скрытые в логах

### Разница между Repository и Environment secrets
- **Repository secrets** - доступны во всех workflow
- **Environment secrets** - доступны только при использовании конкретного environment

### Системные переменные GitHub
- `GITHUB_REPOSITORY` - имя репозитория
- `GITHUB_REF` - ветка или тег
- `GITHUB_SHA` - хеш коммита
- `GITHUB_WORKFLOW` - имя workflow
- `GITHUB_RUN_ID` - ID запуска

## Контрольные вопросы

1. Где находятся настройки variables и secrets в GitHub?
2. В чем разница между variables и secrets?
3. Как использовать environment в workflow?
4. Какие системные переменные предоставляет GitHub?
5. Как создать environment и добавить в него секреты?

## Дополнительные задания

1. Создайте workflow, который использует разные secrets для разных веток
2. Добавьте в workflow шаг, который создает новую переменную во время выполнения
3. Создайте workflow, который проверяет наличие всех необходимых secrets
4. Настройте уведомления о результатах workflow
5. Создайте workflow с условными шагами в зависимости от переменных

## Советы

- Секреты никогда не отображаются в логах
- Environment secrets более безопасны для production
- Используйте meaningful имена для переменных
- Тестируйте workflow на разных ветках
- Всегда проверяйте логи выполнения
