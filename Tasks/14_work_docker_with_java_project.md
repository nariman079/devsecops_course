# Практическая работа: Работа с файлами и Java-проектами в Docker

## Цель работы
Научиться копировать файлы в Docker-образы через `COPY`, упаковывать и запускать Java-приложения в контейнерах локально и в GitHub Actions.

## Задание
Создать Java-проект, написать Dockerfile с копированием файлов, настроить сборку и запуск в контейнере.

> **Важно:** Перед началом убедитесь, что:
> - [ ] Java JDK установлен локально (версия 11 или выше)
> - [ ] Docker Desktop запущен
> - [ ] Вы выполнили предыдущие работы по основам Docker

---

## Шаг 1: Копирование файлов в образ (COPY)

### [ ] 1.1 Создайте тестовые файлы
- [ ] Создайте папку `docker-copy-lab`
- [ ] Внутри создайте файл `app.txt` с содержимым:
```
Hello from host!
```
- [ ] Создайте файл `Dockerfile`:
```dockerfile
FROM alpine:latest

# Копируем файл из хоста в образ
COPY app.txt /app/app.txt

# Устанавливаем рабочую директорию
WORKDIR /app

# Команда запуска
CMD ["cat", "app.txt"]
```

**Что это:** Инструкция `COPY` копирует файлы с вашего компьютера в образ  
**Зачем нужно:** Чтобы файлы приложения были внутри контейнера  
**Пример:** `COPY . /app` — скопировать всё содержимое папки

**Проверка понимания:**
- [ ] Соберите образ: `docker build -t copy-test .`
- [ ] Запустите: `docker run --rm copy-test`
- [ ] Убедитесь, что в выводе видно "Hello from host!"

### [ ] 1.2 Рабочая директория (WORKDIR)
```dockerfile
WORKDIR /app
COPY . .
RUN ls -la
```

**Что это:** Установка текущей директории внутри контейнера  
**Зачем нужно:** Чтобы не писать полные пути в каждой команде  
**Пример:** Все последующие `RUN`, `CMD`, `COPY` будут относительно `/app`

**Проверка понимания:**
- [ ] Добавьте `WORKDIR /app` в ваш Dockerfile
- [ ] Убедитесь, что `COPY app.txt /app/app.txt` можно заменить на `COPY app.txt .`

### [ ] 1.3 Файл .dockerignore
- [ ] Создайте файл `.dockerignore` в корне папки:
```
.git
*.md
*.log
```

**Что это:** Список файлов, которые НЕ будут копироваться в образ  
**Зачем нужно:** Уменьшает размер образа и ускоряет сборку  
**Пример:** `.git` папка может весить много, она не нужна в контейнере

**Проверка понимания:**
- [ ] Добавьте `.dockerignore` в папку проекта
- [ ] Пересоберите образ и сравните размер

---

## Шаг 2: Подготовка Java-проекта

### [ ] 2.1 Создайте простой Java-проект
- [ ] Создайте папку `java-docker-app`
- [ ] Внутри создайте файл `src/Main.java`:
```java
public class Main {
    public static void main(String[] args) {
        System.out.println("Hello from Java in Docker!");
        System.out.println("Java version: " + System.getProperty("java.version"));
    }
}
```

### [ ] 2.2 Соберите JAR локально
```bash
cd java-docker-app
mkdir -p build
javac -d build src/Main.java
jar cfe app.jar Main -C build .
```

**Что это:** Компиляция Java-кода и упаковка в JAR  
**Зачем нужно:** Контейнер будет запускать готовый JAR-файл  
**Пример:** `jar cfe app.jar Main -C build .` — создаёт исполняемый JAR

**Проверка понимания:**
- [ ] Выполните команды сборки
- [ ] Запустите локально: `java -jar app.jar`
- [ ] Убедитесь, что вывод корректный

---

## Шаг 3: Dockerfile для Java-приложения

### [ ] 3.1 Создайте Dockerfile для Java
```dockerfile
# Базовый образ с Java (JRE для запуска)
FROM eclipse-temurin:17-jre-alpine

# Устанавливаем рабочую директорию
WORKDIR /app

# Копируем JAR-файл в образ
COPY app.jar /app/app.jar

# Команда запуска
CMD ["java", "-jar", "app.jar"]
```

**Что это:** Простой Dockerfile для Java-приложения  
**Зачем нужно:** `eclipse-temurin` — современный OpenJDK образ  
**Пример:** `-jre` версия меньше, чем full JDK (только для запуска)

**Проверка понимания:**
- [ ] Соберите образ: `docker build -t java-app .`
- [ ] Запустите: `docker run --rm java-app`
- [ ] Убедитесь, что видно "Hello from Java in Docker!" и версию Java

### [ ] 3.2 Копирование конфигурационного файла
- [ ] Создайте файл `config.properties` в папке проекта:
```
app.name=MyJavaApp
app.version=1.0
```

- [ ] Обновите `Main.java` для чтения конфига:
```java
import java.io.*;
import java.util.*;

public class Main {
    public static void main(String[] args) throws Exception {
        Properties props = new Properties();
        props.load(new FileInputStream("config.properties"));
        System.out.println("App: " + props.getProperty("app.name"));
        System.out.println("Version: " + props.getProperty("app.version"));
    }
}
```

- [ ] Обновите `Dockerfile`:
```dockerfile
FROM eclipse-temurin:17-jre-alpine
WORKDIR /app
COPY app.jar /app/app.jar
COPY config.properties /app/config.properties
CMD ["java", "-jar", "app.jar"]
```

**Что это:** Копирование нескольких файлов в образ  
**Зачем нужно:** Конфигурация приложения должна быть внутри контейнера  
**Пример:** Можно копировать несколько файлов разными `COPY` или одним `COPY . .`

**Проверка понимания:**
- [ ] Пересоберите JAR: `javac -d build src/Main.java && jar cfe app.jar Main -C build .`
- [ ] Пересоберите образ: `docker build -t java-app .`
- [ ] Запустите: `docker run --rm java-app`
- [ ] Убедитесь, что видно "App: MyJavaApp" и "Version: 1.0"

---

## Шаг 4: Java + Docker в GitHub Actions

### [ ] 4.1 Создайте workflow для Java
- [ ] В репозитории создайте `.github/workflows/java-docker.yml`
```yaml
name: Java Docker CI

on: [push]

jobs:
  build-and-run:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3

    - name: Set up Java
      uses: actions/setup-java@v3
      with:
        java-version: '17'
        distribution: 'temurin'

    - name: Build Java application
      run: |
        mkdir -p build
        javac -d build src/Main.java
        jar cfe app.jar Main -C build .

    - name: Build Docker image
      run: docker build -t java-ci-app .

    - name: Run Docker container
      run: docker run --rm java-ci-app

    - name: Show images
      run: docker images
```

**Что это:** Полный пайплайн сборки Java + Docker  
**Зачем нужно:** `setup-java` устанавливает JDK на раннере перед сборкой  
**Пример:** Можно добавить тесты перед сборкой JAR

**Проверка понимания:**
- [ ] Запушите все файлы в репозиторий (Dockerfile, src/, config.properties, workflow)
- [ ] Перейдите в Actions и запустите workflow
- [ ] Убедитесь, что все шаги зелёные
- [ ] В логах `Run Docker container` видно вывод Java-приложения

---

## Шаг 5: Практика (Самостоятельное задание)

### [ ] 5.1 Создайте Java-проект с конфигурацией
Напишите проект, который:
- [ ] Имеет класс `Main.java` в папке `src/`
- [ ] Читает конфигурацию из файла `config.properties` (лежит в корне)
- [ ] Выводит значение ключа `app.name` из конфигурации
- [ ] Выводит текущее время при запуске (подсказка: `new Date()`)

### [ ] 5.2 Напишите Dockerfile
Ваш `Dockerfile` должен:
- [ ] Использовать образ `eclipse-temurin:17-jre-alpine`
- [ ] Копировать `app.jar` и `config.properties` через `COPY`
- [ ] Устанавливать `WORKDIR /app`
- [ ] Запускать приложение через `CMD ["java", "-jar", "app.jar"]`

### [ ] 5.3 Настройте GitHub Actions
Создайте workflow `java-task.yml`, который:
- [ ] Запускается при push в ветку `main`
- [ ] Собирает Java-приложение через `setup-java`
- [ ] Собирает Docker-образ с именем `student-java-app`
- [ ] Запускает контейнер и проверяет вывод
- [ ] Выводит размер финального образа командой `docker images`
- [ ] Имеет шаг с `docker ps -a` для проверки отсутствия «мусора»

### [ ] 5.4 Требования к сдаче
- [ ] Workflow выполняется успешно (зелёная галочка)
- [ ] В логах видно значение из `config.properties`
- [ ] В логах видно текущее время
- [ ] После выполнения нет остановленных контейнеров

---

## Контрольные вопросы

### [ ] 6.1 Простые вопросы
- [ ] Что делает инструкция `COPY` в Dockerfile?
- [ ] Зачем нужна инструкция `WORKDIR`?
- [ ] Почему для запуска лучше использовать JRE, а не JDK?

### [ ] 6.2 Понимание различий
- [ ] Что произойдет, если изменить файл на хосте после `docker build`?
- [ ] Зачем нужен файл `.dockerignore`?
- [ ] Как передать внешний config-файл в уже собранный образ?

### [ ] 6.3 Практические задания
- [ ] Попробуйте запустить контейнер с внешним volume: `docker run -v $(pwd)/config.properties:/app/config.properties java-app`
- [ ] Добавьте в Dockerfile проверку наличия файла через `RUN test -f /app/config.properties`
- [ ] Попробуйте собрать образ без `.dockerignore` и сравните время сборки



---

## Советы
- [ ] Используйте `.dockerignore` чтобы не копировать лишнее (`.git`, `build/`, `*.log`)
- [ ] Для Java всегда указывайте конкретную версию образа (`17-jre-alpine`, не `latest`)
- [ ] Конфигурационные файлы можно монтировать через volume вместо копирования в образ
- [ ] Для больших проектов используйте Maven/Gradle вместо ручной компиляции
- [ ] Всегда проверяйте, что JAR-файл собран перед `docker build`

---

## Пример .dockerignore
```
.git
.gitignore
*.md
target/
build/
*.log
.idea/
.vscode/
```

**Что это:** Файл исключений для Docker build  
**Зачем нужно:** Ускоряет сборку и уменьшает размер контекста  
**Пример:** Поместите в корень репозитория рядом с `Dockerfile`

---

## Что вы должны понять
После этой работы вы должны:
- [ ] Уметь копировать файлы в образ через `COPY`
- [ ] Знать, как настроить `WORKDIR` для удобства
- [ ] Уметь собирать и запускать Java-приложения в Docker
- [ ] Понимать, зачем нужен `.dockerignore`
- [ ] Уметь настроить CI/CD пайплайн для Java + Docker


