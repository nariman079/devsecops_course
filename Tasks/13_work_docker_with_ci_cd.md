



# Практическая работа: Docker в GitHub Actions (CI/CD)

## Цель работы
Научиться запускать Docker-команды внутри пайплайна GitHub Actions, понимать особенности работы с образами на CI-раннерах и автоматизировать сборку приложений.

## Задание
Перенести локальные Docker-команды в workflow файл, настроить автоматическую сборку, запуск тестов и очистку ресурсов.

> **Важно:** Перед началом убедитесь, что:
> - [ ] Вы выполнили предыдущую работу (локальный Docker)
> - [ ] У вас есть репозиторий на GitHub с правами на редактирование
> - [ ] Dockerfile из прошлой работы сохранён или создан заново

---

## Шаг 1: Подготовка репозитория

### [ ] 1.1 Создайте структуру файлов
- [ ] В корне репозитория создайте `Dockerfile` (если нет):
```dockerfile
FROM alpine:latest
RUN echo "Build: config ready" > /config.txt
CMD ["sh", "-c", "echo 'App started' && cat /config.txt"]
```
- [ ] Создайте папку `.github/workflows/`
- [ ] Внутри создайте файл `docker-ci.yml`

**Что это:** Базовая структура проекта для CI/CD  
**Зачем нужно:** GitHub Actions ищет workflow-файлы только в `.github/workflows/`  
**Пример:** Имя файла может быть любым, расширение `.yml` или `.yaml`

**Проверка понимания:**
- [ ] Запушите файлы в репозиторий
- [ ] Убедитесь, что `Dockerfile` в корне, а `docker-ci.yml` в правильной папке

---

## Шаг 2: Первый запуск Docker в CI

### [ ] 2.1 Напишите минимальный workflow
```yaml
name: Docker CI Basic

on: [push]

jobs:
  docker-test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Build Docker image
      run: docker build -t my-app .

    - name: Run Docker container
      run: docker run --rm my-app
```

**Что это:** Аналог локальных команд `docker build` и `docker run` в облаке  
**Зачем нужно:** Автоматическая сборка и тест при каждом push  
**Пример:** `ubuntu-latest` — это виртуальная машина GitHub с предустановленным Docker

**Проверка понимания:**
- [ ] Запушите изменения
- [ ] Перейдите во вкладку **Actions** на GitHub
- [ ] Откройте запущенный workflow
- [ ] Убедитесь, что в логах шага `Run Docker container` видно:
  ```text
  App started
  Build: config ready
  ```

---

## Шаг 3: Управление образами и контейнерами в CI

### [ ] 3.1 Добавьте шаги для инспекции
```yaml
    - name: Show available images
      run: docker images

    - name: Run container in background
      run: docker run -d --name bg-test alpine sleep 60

    - name: List running containers
      run: docker ps

    - name: Stop and remove container
      run: |
        docker stop bg-test
        docker rm bg-test
```

**Что это:** Команды управления в среде CI  
**Зачем нужно:** Понимать, что происходит на раннере, и не оставлять «мусор»  
**Пример:** `|` в YAML позволяет писать многострочные команды

**Проверка понимания:**
- [ ] Запушите изменения
- [ ] В логах `List running containers` должен быть виден `bg-test`
- [ ] Убедитесь, что после шага `Stop and remove` контейнер удалён

---

## Шаг 4: Передача переменных окружения

### [ ] 4.1 Модифицируйте Dockerfile для работы с ENV
```dockerfile
FROM alpine:latest
RUN echo "Config loaded" > /app.log
CMD ["sh", "-c", "echo 'App: $APP_ENV' && cat /app.log"]
```

### [ ] 4.2 Передайте переменную в docker run
```yaml
    - name: Run with environment variable
      run: docker run --rm -e APP_ENV=production my-app
```

**Что это:** Передача конфигурации в контейнер через `-e`  
**Зачем нужно:** В CI/CD так передают секреты, флаги, настройки окружения  
**Пример:** `-e DB_URL=postgres://...` — подключение к БД

**Проверка понимания:**
- [ ] Запушите изменения
- [ ] В логах должно быть: `App: production` и `Config loaded`
- [ ] Попробуйте запустить без `-e` — что выведется вместо значения?

---

## Шаг 5: Обработка ошибок и статусов

### [ ] 5.1 Создайте Dockerfile с ошибкой
```dockerfile
# Файл: Dockerfile.fail
FROM alpine:latest
CMD ["sh", "-c", "echo 'Starting...' && exit 1"]
```

### [ ] 5.2 Добавьте шаг, который должен «упасть»
```yaml
    - name: Build fail image
      run: docker build -t fail-app -f Dockerfile.fail .

    - name: Run fail container (expected to fail)
      run: docker run --rm fail-app
      continue-on-error: true
```

**Что это:** Эмуляция падения приложения внутри контейнера  
**Зачем нужно:** Учиться обрабатывать ошибки в пайплайне  
**Пример:** `continue-on-error: true` позволяет продолжить workflow даже после ошибки

**Проверка понимания:**
- [ ] Запушите изменения
- [ ] Убедитесь, что шаг `Run fail container` помечен желтым (warning), а не красным
- [ ] Проверьте, что следующие шаги (если есть) выполняются

---

## Шаг 6: Практика (Самостоятельное задание)

### [ ] 6.1 Создайте workflow `docker-task.yml`
Напишите пайплайн, который:
- [ ] Называется "Docker Student Task"
- [ ] Запускается при push в ветку `main`
- [ ] Имеет job с именем `build-and-test`
- [ ] Собирает образ с именем `student-ci-app` из `Dockerfile` в корне
- [ ] Запускает контейнер с переменной `STUDENT_NAME=ВашеИмя`
- [ ] Выводит список всех образов командой `docker images`
- [ ] Запускает контейнер в фоне (`sleep 120`), проверяет `docker ps`, затем останавливает и удаляет его
- [ ] Добавляет финальный шаг с `echo "Pipeline completed"`

### [ ] 6.2 Требования к Dockerfile
Ваш `Dockerfile` должен:
- [ ] Использовать `alpine:latest`
- [ ] Создавать файл `/student.txt` с текстом "CI/CD Lab" через `RUN`
- [ ] В `CMD` выводить значение переменной `STUDENT_NAME` и содержимое `/student.txt`

### [ ] 6.3 Проверка сдачи
- [ ] Workflow выполняется успешно (зелёная галочка)
- [ ] В логах запуска видна ваша переменная `STUDENT_NAME`
- [ ] В логах `docker images` виден образ `student-ci-app`
- [ ] После выполнения нет «висячих» контейнеров (проверить через `docker ps -a` в последнем шаге)

---

## Контрольные вопросы

### [ ] 7.1 Простые вопросы
- [ ] Чем отличается запуск `docker run` локально и в GitHub Actions?
- [ ] Зачем нужен `actions/checkout@v3` перед `docker build`?
- [ ] Что делает флаг `--rm` и почему он важен в CI?

### [ ] 7.2 Понимание различий
- [ ] Почему в CI нужно явно удалять контейнеры, а локально можно забыть?
- [ ] Как передать секрет (пароль) в контейнер через GitHub Actions?
- [ ] Что произойдет, если `docker build` упадёт с ошибкой в workflow?

### [ ] 7.3 Практические задания
- [ ] Попробуйте запустить два `docker run` параллельно в одном job — что нужно изменить?
- [ ] Добавьте кэширование образов между запусками (подсказка: `actions/cache`)
- [ ] Настройте запуск workflow только при изменении файлов `Dockerfile` или `*.go`

---

## Сроки выполнения
- [ ] Начало: [дата]
- [ ] Сдача: [дата]

---

## Советы
- [ ] Всегда используйте `--rm` для тестовых запусков в CI
- [ ] Давайте контейнерам уникальные имена через `${{ github.run_id }}`, если запускаете несколько
- [ ] Для отладки добавляйте шаги с `docker ps -a` и `docker images` в конец workflow
- [ ] Секреты передавайте через `env:` в step или через GitHub Secrets (`${{ secrets.NAME }}`)
- [ ] Если workflow «висит» — возможно, контейнер не завершается, проверьте команду в `CMD`

---

## Что вы должны понять
После этой работы вы должны:
- [ ] Уметь запускать `docker build` и `docker run` внутри GitHub Actions
- [ ] Понимать, что каждый шаг workflow — это новый shell-сеанс
- [ ] Знать, как передавать переменные окружения в контейнер в CI
- [ ] Уметь обрабатывать ошибки запуска контейнеров в пайплайне
- [ ] Понимать важность очистки ресурсов на CI-раннерах

---
