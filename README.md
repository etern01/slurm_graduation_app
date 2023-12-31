# Репозиторий приложения YELB.

Это репозиторий с кодом учебного приложения Yelb.

Код, необходимый для создания и настройки инфраструктуры, необходимой для запуска этого приложения, находится в в отдельном репозитории https://s015605.gitlab.yandexcloud.net/etern0/tf . 
Используются Gitlab CI, Terraform и Yandex cloud.

Приложение состоит из следующих сервисов:

* yelb-appserver
* yelb-ui
* база данных Postgresql (расположена в yandex cloud)
* сервер Redis

Построенный в данном репозитории пайплайн собирает, проверяет и деплоит yelb-appserver и yelb-ui, redis из helm chart. База данных деплоятся в пайплайне IaC-репозитория в рамках подготовки инфраструктуры.

После установки приложение будет доступно по адресу https://s015605.site/

Для запуска приложения в production среде создайте необходимые переменны
* DB_SERVER - адрес postgress
* DB_SERVER_USER - имя пользователя postgress
* DB_SERVER_PASSWORD - пароль пользователя postgress
* DB_NAME - имя базы данных postgress

Для запуска приложения в develop среде создайте необходимые переменны
* DB_SERVER - адрес postgress
* DB_SERVER_USER_DEV - имя пользователя postgress
* DB_SERVER_PASSWORD_DEV - пароль пользователя postgress
* DB_NAME_DEV - имя базы данных postgress

Содзайте в кластере kuberneters serviceaccount.
Создайте токен для serviceaccount и добавьте его в ci.
Пример: 
k create token --namespace kube-system gitlab-admin --duration=999999h

Создайте два namespace
Пример:
kubectl create namespace yelb-development
kubectl create namespace yelb-production

Создайте clusterissuer из корня проекта



# Документация по переменным

В предоставленной конфигурации YAML файл содержит набор переменных и их использование в CI/CD. Переменные используются для определения и управления динамическими значениями, которые могут меняться в зависимости от различных условий или окружений. Ниже приведено описание переменных и их использование:


## 1. Глобальные переменные:
Это постоянные значения, которые остаются неизменными на протяжении всего процесса CI/CD.

### K8S_API_URL:
- Описание: URL API Kubernetes.
- Значение: https://158.160.109.92
- Использование: Эта переменная содержит URL API Kubernetes для общения с кластером Kubernetes.

### FRONTEND_NAME:
- Описание: Имя сервиса фронтенда.
- Значение: yelb-ui
- Использование: Эта переменная представляет собой имя сервиса фронтенда.

### BACKEND_NAME:
- Описание: Имя сервиса бэкенда.
- Значение: yelb-appserver
- Использование: Эта переменная представляет собой имя сервиса бэкенда.

### APP_NAME:
- Описание: Имя приложения.
- Значение: yelb
- Использование: Эта переменная содержит имя приложения.

## 2. Правила и условные переменные:
Эти переменные определяются условно в зависимости от ветки (`main` или другой), используемой в конвейере CI/CD.

### rules:
- Описание: Правила для определения переменных в зависимости от ветки.
- Использование: Эти правила определяют, какой набор переменных должен использоваться в зависимости от ветки, которая сейчас собирается. Если ветка - это `main`, устанавливаются переменные, связанные с продакшеном. В противном случае, для любой другой ветки устанавливаются переменные, связанные с разработкой.

## 3. Переменные для каждого этапа (Job-Specific Variables):
Эти переменные используются на различных этапах конвейера CI/CD.

### YELB_DB_SERVER_ENDPOINT:
- Описание: Конечная точка сервера базы данных.
- Использование: Эта переменная содержит конечную точку сервера базы данных.

### YELB_DB_SERVER_USER:
- Описание: Имя пользователя сервера базы данных.
- Использование: Эта переменная содержит имя пользователя для подключения к серверу базы данных.

### YELB_DB_SERVER_PASSWORD:
- Описание: Пароль сервера базы данных.
- Использование: Эта переменная содержит пароль для аутентификации на сервере базы данных.

### YELB_DB_NAME:
- Описание: Имя базы данных.
- Использование: Эта переменная содержит имя базы данных.

### DEPLOY_VARIABLE:
- Описание: Переменная окружения для развертывания.
- Использование: Эта переменная содержит либо "production", либо "develop" в зависимости от ветки, которая сейчас собирается.

### DEPLOY_URL:
- Описание: URL развертывания.
- Использование: Эта переменная содержит URL развертывания в зависимости от ветки, которая сейчас собирается.

## 4. Этапы конвейера (Stages):
Это различные этапы, определенные для конвейера CI/CD.

- lint: Проверка синтаксиса YAML-файлов и Helm-чартов.
- build:backend: Сборка Docker-образа для сервиса бэкенда.
- build:frontend: Сборка Docker-образа для сервиса фронтенда.
- test: Запуск тестов с использованием Docker Compose.
- cleanup: Очистка контейнеров Docker.
- push: Публикация Docker-образов в реестр.
- deploy: Развертывание приложения с использованием Kubernetes и Helm.

## 5. Определения задач (Job Definitions):
Это конкретные определения задач для каждого этапа конвейера CI/CD.

- lint-yaml: Проверка синтаксиса YAML-файлов с помощью Docker-образа `cytopia/yamllint`.
- lint-helm: Проверка синтаксиса Helm-чартов с помощью Docker-образа `alpine/k8s:1.24.15`.
- build:backend: Сборка Docker-образа сервиса бэкенда и его публикация в реестр.
- build:frontend: Сборка Docker-образа сервиса фронтенда и его публикация в реестр.
- test: Запуск тестов с использованием Docker Compose.
- cleanup: Очистка контейнеров Docker после завершения тестов.
- push: Публикация собранных Docker-образов в реестр Docker.
- deploy: Развертывание приложения в Kubernetes с помощью Helm.

