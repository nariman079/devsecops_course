# Практическая работа: Docker Volumes, Networks и Environment Variables

## Цель работы
Научиться работать с томами (volumes), сетями (networks) и переменными окружения (environment variables) в Docker. Понимать, как сохранять данные между запусками контейнеров, как контейнеры общаются друг с другом и как передавать конфигурацию.

## Задание
Выполнить серию практических задач локально в терминале, затем настроить минимальный CI/CD пайплайн в GitHub Actions для проверки работы.

> **Важно:** Перед началом убедитесь, что:
> - [ ] Docker Desktop запущен
> - [ ] Вы выполнили предыдущие работы по основам Docker
> - [ ] У вас есть репозиторий на GitHub с правами на редактирование
>
> **Время выполнения:** ~2 часа

---

## Шаг 1: Работа с Volumes (Тома)

### [ ] 1.1 Что такое Volume
**Теория:** Volume — это хранилище данных, которое сохраняется после удаления контейнера.

**Зачем нужно:** 
- Сохранять данные базы данных
- Делиться файлами между хостом и контейнером
- Сохранять логи и конфигурации

### [ ] 1.2 Создайте именованный volume
```bash
# Создать volume
docker volume create my-data

# Посмотреть список volumes
docker volume ls

# Посмотреть информацию о volume
docker volume inspect my-data
```

**Проверка понимания:**
- [ ] Выполните все три команды
- [ ] Убедитесь, что `my-data` появился в списке
- [ ] Запомните путь `Mountpoint` из `inspect`

### [ ] 1.3 Запишите данные в volume через контейнер
```bash
# Запустите контейнер с volume и запишите файл
docker run --rm -v my-data:/data alpine sh -c "echo 'Hello Volume!' > /data/test.txt"

# Запустите другой контейнер с тем же volume и прочитайте файл
docker run --rm -v my-data:/data alpine cat /data/test.txt
```

**Что это:** Два разных контейнера используют один volume  
**Зачем нужно:** Данные сохраняются между запусками контейнеров  
**Пример:** База данных хранит файлы в volume, контейнер можно пересоздавать

**Проверка понимания:**
- [ ] Выполните обе команды
- [ ] Убедитесь, что во второй команде видно "Hello Volume!"
- [ ] Данные сохранились, хотя первый контейнер удалён (`--rm`)

### [ ] 1.4 Bind Mount (монтирование папки с хоста)
```bash
# Создайте папку локально
mkdir -p ./local-data
echo "From Host" > ./local-data/host.txt

# Запустите контейнер с bind mount
docker run --rm -v $(pwd)/local-data:/data alpine cat /data/host.txt
```

**Что это:** Прямое подключение папки с вашего компьютера в контейнер  
**Зачем нужно:** Быстрая разработка, редактирование файлов на хосте  
**Пример:** `docker run -v $(pwd):/app` — текущая папка в контейнере

**Проверка понимания:**
- [ ] Выполните команды
- [ ] Убедитесь, что видно "From Host"
- [ ] Проверьте, что файл появился в папке `./local-data` после записи из контейнера

### [ ] 1.5 Очистка volumes
```bash
# Удалить конкретный volume
docker volume rm my-data

# Удалить все неиспользуемые volumes
docker volume prune
```

**Проверка понимания:**
- [ ] Выполните `docker volume ls` до и после удаления
- [ ] Убедитесь, что `my-data` удалён

---

## Шаг 2: Работа с Networks (Сети)

### [ ] 2.1 Что такое Docker Network
**Теория:** Network позволяет контейнерам общаться друг с другом по именам.

**Зачем нужно:**
- Микросервисы общаются внутри сети
- Изоляция групп контейнеров
- Безопасность (контейнеры вне сети не видят друг друга)

### [ ] 2.2 Создайте сеть
```bash
# Создать сеть
docker network create my-network

# Посмотреть список сетей
docker network ls

# Посмотреть информацию о сети
docker network inspect my-network
```

**Проверка понимания:**
- [ ] Выполните все три команды
- [ ] Убедитесь, что `my-network` появился в списке
- [ ] В `inspect` найдите поле `Containers` (пока пустое)

### [ ] 2.3 Запустите два контейнера в одной сети
```bash
# Запустите первый контейнер (сервер)
docker run -d --name server --network my-network alpine sleep 300

# Запустите второй контейнер (клиент)
docker run --rm --network my-network alpine ping -c 3 server
```

**Что это:** Контейнеры видят друг друга по имени в одной сети  
**Зачем нужно:** Микросервисная архитектура, базы данных + приложения  
**Пример:** `app` контейнер подключается к `db` по имени `db`

**Проверка понимания:**
- [ ] Выполните обе команды
- [ ] Убедитесь, что `ping` успешен (видите ответы от `server`)
- [ ] Это работает потому что оба в `my-network`

### [ ] 2.4 Проверка изоляции сетей
```bash
# Запустите контейнер в сети по умолчанию
docker run --rm alpine ping -c 1 server
```

**Что это:** Контейнер вне сети не видит другие контейнеры  
**Зачем нужно:** Безопасность и изоляция  

**Проверка понимания:**
- [ ] Выполните команду
- [ ] Должна быть ошибка (контейнер не находит `server`)
- [ ] Это доказывает изоляцию сетей

### [ ] 2.5 Очистка networks
```bash
# Остановите контейнеры
docker stop server
docker rm server

# Удалите сеть
docker network rm my-network

# Или удалите все неиспользуемые сети
docker network prune
```

**Проверка понимания:**
- [ ] Выполните очистку
- [ ] Убедитесь через `docker network ls`, что сеть удалена

---

## Шаг 3: Работа с Environment Variables (Переменные окружения)

### [ ] 3.1 Передача переменных через -e
```bash
# Запустите контейнер с переменной
docker run --rm -e MY_VAR="Hello" alpine sh -c "echo $MY_VAR"

# Несколько переменных
docker run --rm -e VAR1="First" -e VAR2="Second" alpine sh -c "echo $VAR1 $VAR2"
```

**Что это:** Передача конфигурации в контейнер при запуске  
**Зачем нужно:** Пароли, URL баз данных, флаги окружения (dev/prod)  
**Пример:** `docker run -e DB_PASSWORD=secret my-app`

**Проверка понимания:**
- [ ] Выполните обе команды
- [ ] Убедитесь, что переменные выводятся правильно

### [ ] 3.2 Передача переменных из файла (.env)
- [ ] Создайте файл `.env` в текущей папке:
```
APP_NAME=MyApp
APP_VERSION=1.0
DB_HOST=localhost
```

- [ ] Запустите контейнер с чтением из файла:
```bash
docker run --rm --env-file .env alpine sh -c "echo $APP_NAME v$APP_VERSION"
```

**Что это:** Загрузка переменных из файла  
**Зачем нужно:** Удобное управление множеством переменных  
**Пример:** Разные `.env` для dev, test, production

**Проверка понимания:**
- [ ] Создайте файл `.env`
- [ ] Выполните команду
- [ ] Убедитесь, что видно "MyApp v1.0"

### [ ] 3.3 Переменные в Dockerfile (ARG vs ENV)
- [ ] Создайте `Dockerfile`:
```dockerfile
FROM alpine:latest

# ARG доступен только при сборке
ARG BUILD_VERSION=1.0

# ENV доступен в запущенном контейнере
ENV APP_NAME=DefaultApp

RUN echo "Build version: $BUILD_VERSION" > /build_info.txt

CMD ["sh", "-c", "echo 'App: $APP_NAME' && cat /build_info.txt"]
```

- [ ] Соберите и запустите:
```bash
docker build --build-arg BUILD_VERSION=2.0 -t env-test .
docker run --rm -e APP_NAME=CustomApp env-test
```

**Что это:** `ARG` для сборки, `ENV` для запуска  
**Зачем нужно:** Разные значения на разных этапах  
**Пример:** `ARG` — версия сборки, `ENV` — режим работы (dev/prod)

**Проверка понимания:**
- [ ] Соберите образ с `--build-arg`
- [ ] Запустите с `-e APP_NAME`
- [ ] Убедитесь, что видно "Build version: 2.0" и "App: CustomApp"

---

## Шаг 4: Практический кейс (Объединяем всё)

### [ ] 4.1 Сценарий: Веб-приложение + База данных
**Задача:** Создать два контейнера, которые:
- Общаются через сеть
- База данных хранит данные в volume
- Конфигурация передаётся через environment variables

### [ ] 4.2 Создайте сеть и volume
```bash
docker network create app-network
docker volume create db-data
```

**Проверка понимания:**
- [ ] Выполните команды
- [ ] Проверьте через `docker network ls` и `docker volume ls`

### [ ] 4.3 Запустите "базу данных" (эмуляция)
```bash
docker run -d \
  --name db \
  --network app-network \
  -v db-data:/var/lib/data \
  -e DB_NAME=mydb \
  alpine sh -c "echo 'DB: $DB_NAME' > /var/lib/data/db.info && sleep 300"
```

**Что это:** Контейнер БД с volume и переменной  
**Зачем нужно:** Данные сохраняются в volume, конфиг через ENV

### [ ] 4.4 Запустите "приложение" (эмуляция)
```bash
docker run --rm \
  --network app-network \
  -e DB_HOST=db \
  -e DB_NAME=mydb \
  alpine sh -c "echo 'App connecting to $DB_HOST' && cat /var/lib/data/db.info"
```

**Что это:** Приложение подключается к БД по имени в сети  
**Зачем нужно:** Реальная архитектура микросервисов

**Проверка понимания:**
- [ ] Выполните команду
- [ ] Убедитесь, что видно "App connecting to db"
- [ ] Убедитесь, что видно содержимое из volume БД

### [ ] 4.5 Проверьте сохранность данных
```bash
# Удалите контейнер БД
docker stop db
docker rm db

# Запустите новый контейнер с тем же volume
docker run --rm -v db-data:/var/lib/data alpine cat /var/lib/data/db.info
```

**Проверка понимания:**
- [ ] Выполните команды
- [ ] Убедитесь, что файл `db.info` сохранился после удаления контейнера
- [ ] Это доказывает работу volume

### [ ] 4.6 Очистка
```bash
docker network rm app-network
docker volume rm db-data
```

---

## Шаг 5: GitHub Actions CI/CD (Минимальная проверка)

### [ ] 5.1 Создайте Dockerfile для теста
- [ ] В корне репозитория создайте `Dockerfile`:
```dockerfile
FROM alpine:latest

ENV TEST_MODE=ci

RUN echo "default" > /data.txt

CMD ["sh", "-c", "echo 'Mode: $TEST_MODE' && cat /data.txt"]
```

### [ ] 5.2 Создайте workflow 
Пожалуйста пишите все шаги "steps" вручную, не копируйте. Чтобы вы могли увидеть, как работает каждый этап в github actions нужно после каждого push
- [ ] Создайте `.github/workflows/docker-env-test.yml`:
```yaml
name: Docker Env Network Volume Test

on: [push]

jobs:
  docker-test:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3

    - name: Create Network
      run: docker network create test-network

    - name: Create Volume
      run: docker volume create test-volume

    - name: Build Image
      run: docker build -t env-test .

    - name: Run with Environment
      run: docker run --rm -e TEST_MODE=github-actions env-test

    - name: Run with Volume
      run: |
        docker run --rm -v test-volume:/data alpine sh -c "echo 'CI Data' > /data/ci.txt"
        docker run --rm -v test-volume:/data alpine cat /data/ci.txt

    - name: Run with Network
      run: |
        docker run -d --name svc1 --network test-network alpine sleep 60
        docker run --rm --network test-network alpine ping -c 2 svc1
        docker stop svc1
        docker rm svc1

    - name: Cleanup
      run: |
        docker network rm test-network
        docker volume rm test-volume
```

**Что это:** Минимальный CI/CD проверяет все три компонента  
**Зачем нужно:** Автоматическая проверка работы Docker в pipeline  

**Проверка понимания:**
- [ ] Запушите все файлы в репозиторий
- [ ] Перейдите в Actions
- [ ] Убедитесь, что все шаги зелёные
- [ ] В логах `Run with Environment` видно "Mode: github-actions"
- [ ] В логах `Run with Volume` видно "CI Data"
- [ ] В логах `Run with Network` ping успешен

---

## Шаг 6: Самостоятельное задание

### [ ] 6.1 Создайте свой проект
Напишите конфигурацию, которая:
- [ ] Имеет `Dockerfile` с `ENV` переменной `APP_ENV`
- [ ] При запуске выводит значение `APP_ENV` и текущую дату
- [ ] Создаёт файл в volume с текстом "Student Work"

### [ ] 6.2 Настройте workflow
Создайте `docker-final.yml`, который:
- [ ] Запускается при push
- [ ] Создаёт сеть `student-network`
- [ ] Создаёт volume `student-volume`
- [ ] Собирает образ `student-final-app`
- [ ] Запускает контейнер с `-e APP_ENV=production`
- [ ] Записывает данные в volume
- [ ] Проверяет чтение из volume вторым контейнером
- [ ] Запускает второй контейнер в сети для проверки связи
- [ ] Очищает сеть и volume в конце

### [ ] 6.3 Требования к сдаче
- [ ] Workflow выполняется успешно (зелёная галочка)
- [ ] В логах видно значение `APP_ENV=production`
- [ ] В логах видно данные из volume
- [ ] В логах виден успешный ping между контейнерами
- [ ] После выполнения сеть и volume удалены

---

## Контрольные вопросы

### [ ] 7.1 Простые вопросы
- [ ] В чем разница между `docker volume create` и bind mount?
- [ ] Зачем нужна Docker сеть?
- [ ] Как передать переменную окружения в контейнер?

### [ ] 7.2 Понимание различий
- [ ] Что произойдет с данными в volume после удаления контейнера?
- [ ] Почему контейнеры в разных сетях не видят друг друга?
- [ ] В чем разница между `ARG` и `ENV` в Dockerfile?

### [ ] 7.3 Практические задания
- [ ] Попробуйте запустить контейнер с `--env-file` в GitHub Actions
- [ ] Создайте две сети и попробуйте соединить контейнеры из разных сетей
- [ ] Добавьте в workflow шаг с `docker volume inspect` для отладки

---

## Сроки выполнения
- [ ] Начало: [дата]
- [ ] Сдача: [дата]

---

## Шпаргалка команд

### Volumes
```bash
docker volume create <name>      # Создать
docker volume ls                 # Список
docker volume inspect <name>     # Информация
docker volume rm <name>          # Удалить
docker volume prune              # Удалить все неиспользуемые
```

### Networks
```bash
docker network create <name>     # Создать
docker network ls                # Список
docker network inspect <name>    # Информация
docker network rm <name>         # Удалить
docker network prune             # Удалить все неиспользуемые
```

### Environment
```bash
docker run -e VAR=value          # Одна переменная
docker run -e VAR1=v1 -e VAR2=v2 # Несколько переменных
docker run --env-file .env       # Из файла
docker build --build-arg VAR=v   # ARG при сборке
```

### Run с комбинацией
```bash
docker run -d \
  --name <name> \
  --network <network> \
  -v <volume>:/path \
  -e VAR=value \
  <image>
```

---

## Советы
- [ ] Всегда очищайте networks и volumes после тестов в CI/CD
- [ ] Используйте именованные volumes для важных данных
- [ ] Давайте контейнерам имена через `--name` для удобства
- [ ] В GitHub Actions runner'ы чистые — создавайте сети/volumes в каждом запуске
- [ ] Для отладки используйте `docker inspect` для сетей и volumes

---

## Что вы должны понять
После этой работы вы должны:
- [ ] Уметь создавать и использовать Docker volumes
- [ ] Понимать разницу между volume и bind mount
- [ ] Уметь создавать сети и соединять контейнеры
- [ ] Понимать изоляцию контейнеров в разных сетях
- [ ] Уметь передавать переменные окружения через `-e` и `--env-file`
- [ ] Понимать разницу между `ARG` и `ENV` в Dockerfile
- [ ] Уметь настроить минимальный CI/CD с проверкой volumes, networks, environments

---

> **Домашнее задание на следующую пару:**  
> Подумайте над улучшением:
> 1. Как пушить собранный образ в Docker Hub?
> 2. Как использовать GitHub Secrets для переменных окружения?
> 3. Как запустить реальные тесты внутри контейнера?
>
> Следующая тема: **Docker Registry и публикация образов**.