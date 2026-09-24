# Spring PetClinic — CI/CD Pipeline Practice

🇬🇧 [English](#english) | 🇩🇪 [Deutsch](#deutsch) | 🇷🇺 [Русский](#русский)

---

## English

A learning project built on top of the official [Spring PetClinic](https://github.com/spring-projects/spring-petclinic) 
application, used as a base to practice setting up a full CI/CD pipeline: containerizing 
a Java application with Docker, automating build & test with GitLab CI, and deploying 
to a cloud platform.

> This repository is based on Spring PetClinic (Apache License 2.0). The application 
> logic itself is from the original project — my work here is the containerization, 
> CI/CD pipeline, and deployment setup described below.

### Tech Stack

- **Java 17**, **Spring Boot**, **Maven**
- **Docker** — multi-stage build
- **Podman** — local container testing
- **GitLab CI/CD** — automated build & test pipeline
- **Render** — cloud deployment

### What I set up

- **Multi-stage Dockerfile** — separates the build environment (Maven + JDK) from 
  the runtime environment (JRE only), reducing the final image size and removing 
  unnecessary build tools from production.
- **GitLab CI/CD pipeline** (`.gitlab-ci.yml`) — runs automatically on every push to 
  `main`:
  - `build-job`: compiles the project
  - `test-job`: runs the test suite
- **Automatic deployment to Render** — connected to the repository, builds the Docker 
  image and deploys the app on every push.

### Pipeline status

![pipeline status](LINK_TO_GITLAB_BADGE)

### CI/CD configuration (`.gitlab-ci.yml`)

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean compile
  only:
    - main

test-job:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn test
  only:
    - main
```

### Challenges I ran into (and solved)

- Diagnosed why the deployment platform's auto-detection picked up Gradle instead 
  of Maven, and switched to an explicit Docker-based build to remove the ambiguity.
- Set up a working Docker/Podman environment inside a Distrobox container on an 
  immutable Linux OS (Bazzite), which doesn't run systemd by default.
- Debugged a YAML syntax error and a GitLab identity verification requirement 
  that were blocking the pipeline from running.

### Run locally

```bash
git clone https://github.com/KorvusBl/spring-petclinic.git
cd spring-petclinic
podman build -t petclinic:1.0 .
podman run -p 8080:8080 petclinic:1.0
```

Then open `http://localhost:8080`.

### Dockerfile overview

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

---

## Deutsch

Ein Lernprojekt auf Basis der offiziellen [Spring PetClinic](https://github.com/spring-projects/spring-petclinic)-Anwendung, 
das als Grundlage diente, um eine vollständige CI/CD-Pipeline einzurichten: Containerisierung 
einer Java-Anwendung mit Docker, Automatisierung von Build und Tests mit GitLab CI sowie 
Deployment auf eine Cloud-Plattform.

> Dieses Repository basiert auf Spring PetClinic (Apache License 2.0). Die eigentliche 
> Anwendungslogik stammt aus dem Originalprojekt — meine eigene Arbeit besteht in der 
> Containerisierung sowie der unten beschriebenen CI/CD-Pipeline und dem Deployment.

### Tech-Stack

- **Java 17**, **Spring Boot**, **Maven**
- **Docker** — Multi-Stage-Build
- **Podman** — lokales Testen der Container
- **GitLab CI/CD** — automatisierte Build- & Test-Pipeline
- **Render** — Cloud-Deployment

### Was ich eingerichtet habe

- **Multi-Stage-Dockerfile** — trennt die Build-Umgebung (Maven + JDK) von der 
  Laufzeitumgebung (nur JRE), wodurch das finale Image kleiner wird und unnötige 
  Build-Werkzeuge nicht in die Produktion gelangen.
- **GitLab-CI/CD-Pipeline** (`.gitlab-ci.yml`) — läuft automatisch bei jedem Push 
  auf `main`:
  - `build-job`: kompiliert das Projekt
  - `test-job`: führt die Tests aus
- **Automatisches Deployment auf Render** — mit dem Repository verbunden, baut das 
  Docker-Image und deployt die Anwendung bei jedem Push.

### Pipeline-Status

![pipeline status](LINK_ZUM_GITLAB_BADGE)

### CI/CD-Konfiguration (`.gitlab-ci.yml`)

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean compile
  only:
    - main

test-job:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn test
  only:
    - main
```

### Herausforderungen (und wie ich sie gelöst habe)

- Herausgefunden, warum die automatische Erkennung der Deployment-Plattform Gradle 
  statt Maven ausgewählt hat, und auf einen expliziten Docker-basierten Build 
  umgestellt, um die Mehrdeutigkeit zu beseitigen.
- Eine funktionierende Docker/Podman-Umgebung innerhalb eines Distrobox-Containers 
  auf einem unveränderlichen Linux-System (Bazzite) eingerichtet, das standardmäßig 
  kein systemd ausführt.
- Einen YAML-Syntaxfehler behoben und die Identitätsverifizierung bei GitLab 
  durchlaufen, die den Start der Pipeline blockiert hatten.

### Lokal ausführen

```bash
git clone https://github.com/KorvusBl/spring-petclinic.git
cd spring-petclinic
podman build -t petclinic:1.0 .
podman run -p 8080:8080 petclinic:1.0
```

Danach `http://localhost:8080` öffnen.

### Dockerfile im Überblick

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```

---

## Русский

Учебный проект на основе официального приложения [Spring PetClinic](https://github.com/spring-projects/spring-petclinic), 
использованного как база для практики настройки полного CI/CD-пайплайна: контейнеризация 
Java-приложения через Docker, автоматизация сборки и тестов через GitLab CI, 
деплой на облачную платформу.

> Этот репозиторий основан на Spring PetClinic (лицензия Apache 2.0). Бизнес-логика 
> приложения взята из оригинального проекта — моя работа здесь заключается в 
> контейнеризации, настройке CI/CD-пайплайна и деплоя, описанных ниже.

### Стек технологий

- **Java 17**, **Spring Boot**, **Maven**
- **Docker** — multi-stage сборка
- **Podman** — локальное тестирование контейнеров
- **GitLab CI/CD** — автоматизированный пайплайн сборки и тестов
- **Render** — деплой в облако

### Что я настроил

- **Multi-stage Dockerfile** — разделяет среду сборки (Maven + JDK) и среду выполнения 
  (только JRE), что уменьшает размер финального образа и убирает лишние 
  инструменты сборки из продакшена.
- **Пайплайн GitLab CI/CD** (`.gitlab-ci.yml`) — запускается автоматически при каждом 
  пуше в `main`:
  - `build-job`: компилирует проект
  - `test-job`: запускает тесты
- **Автоматический деплой на Render** — подключён к репозиторию, собирает Docker-образ 
  и деплоит приложение при каждом пуше.

### Статус пайплайна

![pipeline status](ССЫЛКА_НА_BADGE_ИЗ_GITLAB)

### Конфигурация CI/CD (`.gitlab-ci.yml`)

```yaml
stages:
  - build
  - test

build-job:
  stage: build
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn clean compile
  only:
    - main

test-job:
  stage: test
  image: maven:3.9-eclipse-temurin-17
  script:
    - mvn test
  only:
    - main
```

### С какими проблемами столкнулся (и как решил)

- Разобрался, почему автодетект платформы деплоя выбирал Gradle вместо Maven, 
  и перешёл на явную сборку через Docker, чтобы убрать двусмысленность.
- Настроил рабочую среду Docker/Podman внутри Distrobox-контейнера на неизменяемой 
  Linux-системе (Bazzite), которая по умолчанию не запускает systemd.
- Отладил синтаксическую ошибку в YAML и прошёл верификацию личности в GitLab, 
  которые блокировали запуск пайплайна.

### Как запустить локально

```bash
git clone https://github.com/KorvusBl/spring-petclinic.git
cd spring-petclinic
podman build -t petclinic:1.0 .
podman run -p 8080:8080 petclinic:1.0
```

Затем открой `http://localhost:8080`.

### Обзор Dockerfile

```dockerfile
FROM maven:3.9-eclipse-temurin-17 AS builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM eclipse-temurin:17-jre-jammy
WORKDIR /app
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
CMD ["java", "-jar", "app.jar"]
```
