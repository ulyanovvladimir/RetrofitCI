# Continuous Integration - пример конфигурации

## Обзор
Будем использовать связку GitHub + CircleCI для организации Continuous Integration

## Инструкции

### Создадим репозиторий с коммитом на GitHub
Проект должен собираться через Gradle


### Зарегистрируемся на CircleCI
http://circleci.com

Используем вход через GitHub.

### Добавление проекта
В навигационной панели слева выбираем Projects

Вверху справа нажимаем кнопку Add Project

Выбираем проект из списка проектов GitHub.

В настройках проекта в CircleCI выбираем

Build forked pull requests - ON

### Конфигурация CircleCI
Для того, чтобы приложение автоматически собиралось, необходимо добавить папку

.circleci

и в нее добавить файл config.yml следующего содержания:

```yml
# Java Gradle CircleCI 2.0 configuration file
#
# Check https://circleci.com/docs/2.0/language-java/ for more details
#
version: 2
jobs:
  build:
    working_directory: ~/code
    docker:
      - image: circleci/android:api-26-alpha
    environment:
      JVM_OPTS: -Xmx3200m
    steps:
      - checkout
      - restore_cache:
          key: jars-{{ checksum "build.gradle" }}-{{ checksum  "app/build.gradle" }}
#      - run:
#         name: Chmod permissions #if permission for Gradlew Dependencies fail, use this.
#         command: sudo chmod +x ./gradlew
      - run:
          name: chmod permissions
          command: chmod +x ./gradlew
      - run:
          name: Download Dependencies
          command: ./gradlew androidDependencies
      - save_cache:
          paths:
            - ~/.gradle
          key: jars-{{ checksum "build.gradle" }}-{{ checksum  "app/build.gradle" }}
      - run:
          name: Run Tests
          command: ./gradlew lint test
      - store_artifacts:
          path: app/build/reports
          destination: reports
      - store_test_results:
          path: app/build/test-results
```

Сделайте commit и push в репозиторий.

Теперь автоматически будет производиться сборка проекта в CircleCI при каждом коммите. 
Если включена настройка сборки при pull-request, то будут собираться и прогоняться тесты и для 
них тоже. Это позволит принять решение о мердже.

## Continuous Delivery
### Создание ключа
### Безопасное хранение ключа в шифрованном бакете
### Доставка релизов через Fabric 