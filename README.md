# Spring PetClinic — CI/CD Pipeline Practice

A learning project built on top of the official [Spring PetClinic](https://github.com/spring-projects/spring-petclinic) 
application, used as a base to practice setting up a full CI/CD pipeline: containerizing 
a Java application with Docker, automating build & test with GitLab CI, and deploying 
to a cloud platform.

> This repository is based on Spring PetClinic (Apache License 2.0). The application 
> logic itself is from the original project — my work here is the containerization, 
> CI/CD pipeline, and deployment setup described below.

## Tech Stack

- **Java 17**, **Spring Boot**, **Maven**
- **Docker** — multi-stage build
- **Podman** — local container testing
- **GitLab CI/CD** — automated build & test pipeline
- **Render** — cloud deployment

## What I set up

- **Multi-stage Dockerfile** — separates the build environment (Maven + JDK) from 
  the runtime environment (JRE only), reducing the final image size and removing 
  unnecessary build tools from production.
- **GitLab CI/CD pipeline** (`.gitlab-ci.yml`) — runs automatically on every push to 
  `main`:
  - `build-job`: compiles the project
  - `test-job`: runs the test suite
- **Automatic deployment to Render** — connected to the repository, builds the Docker 
  image and deploys the app on every push.

## Pipeline status

![pipeline status](ССЫЛКА_НА_BADGE_ИЗ_GITLAB)

## Challenges I ran into (and solved)

- Diagnosed why the deployment platform's auto-detection picked up Gradle instead 
  of Maven, and switched to an explicit Docker-based build to remove the ambiguity.
- Set up a working Docker/Podman environment inside a Distrobox container on an 
  immutable Linux OS (Bazzite), which doesn't run systemd by default.
- Debugged a YAML syntax error and a GitLab identity verification requirement 
  that were blocking the pipeline from running.

## Run locally

```bash
git clone https://github.com/KorvusBl/spring-petclinic.git
cd spring-petclinic
podman build -t petclinic:1.0 .
podman run -p 8080:8080 petclinic:1.0
```

Then open `http://localhost:8080`.

## Dockerfile overview

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
