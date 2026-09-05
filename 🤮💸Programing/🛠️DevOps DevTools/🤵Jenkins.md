---
tags: [jenkins, cicd, devops]
---

# 🤵 Jenkins

Связано: [[🔄CI-CD Pipelines]] · [[☸️Kubernetes]] · [[🐳Docker]] · [[⚙️Ansible]]

Self-hosted сервер автоматизации. В отличие от GitHub Actions/GitLab CI, разворачивается сам (обычно в докере) и полностью управляется вручную — гибче, но требует администрирования.

## Ключевые понятия

- **Job / Project** — единица работы (Freestyle или Pipeline).
- **Pipeline** — сценарий на Groovy-DSL, описывающий стадии (Declarative или Scripted).
- **Jenkinsfile** — файл с pipeline, хранится в репозитории (Pipeline as Code).
- **Agent / Node** — машина, где исполняется job (master сам обычно не билдит).
- **Executor** — слот на agent для параллельного запуска jobs.
- **Plugin** — расширение функциональности (Docker, Git, Slack notify, Blue Ocean UI и т.д.)

## Запуск Jenkins в Docker

```bash
docker run -d --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts
```

Пароль администратора для первого входа:

```bash
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

> [!note]
> Монтирование `docker.sock` даёт Jenkins возможность запускать docker-команды прямо из pipeline (docker-in-docker подход), но открывает контейнеру доступ к докер-демону хоста — учитывай это с точки зрения безопасности.

## Declarative Jenkinsfile — пример

```groovy
pipeline {
    agent any

    environment {
        IMAGE = "ghcr.io/org/myapp"
    }

    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Lint & Test') {
            steps {
                sh 'npm ci'
                sh 'npm run lint'
                sh 'npm test'
            }
        }

        stage('Build image') {
            steps {
                sh "docker build -t ${IMAGE}:${env.GIT_COMMIT} ."
            }
        }

        stage('Push image') {
            when { branch 'main' }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'ghcr-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh "echo $PASS | docker login ghcr.io -u $USER --password-stdin"
                    sh "docker push ${IMAGE}:${env.GIT_COMMIT}"
                }
            }
        }

        stage('Deploy') {
            when { branch 'main' }
            steps {
                sh "ansible-playbook -i inventory/prod.ini deploy.yml --extra-vars tag=${env.GIT_COMMIT}"
            }
        }
    }

    post {
        always { cleanWs() }
        failure {
            slackSend(color: 'danger', message: "Build ${env.BUILD_NUMBER} failed: ${env.BUILD_URL}")
        }
    }
}
```

## Scripted vs Declarative

| | Declarative | Scripted |
|---|---|---|
| Синтаксис | строгий, структурированный (`pipeline { stages { ... } }`) | произвольный Groovy-код |
| Читаемость | выше | ниже, но гибче |
| Когда выбирать | почти всегда по умолчанию | сложная нестандартная логика, которую не выразить декларативно |

## Полезные шаги (steps)

```groovy
sh 'команда'                       // выполнить shell-команду
bat 'команда'                      // то же для Windows-агентов
input message: 'Deploy to prod?'   // ручное подтверждение
parallel(                          // параллельные стадии
    unit: { sh 'npm run test:unit' },
    e2e:  { sh 'npm run test:e2e' }
)
retry(3) { sh './flaky-script.sh' }
timeout(time: 5, unit: 'MINUTES') { sh './long-task.sh' }
```

## Credentials

Секреты хранятся в **Jenkins Credentials Store** (Manage Jenkins → Credentials), а не в Jenkinsfile. Достаются через `withCredentials` или `credentials()`:

```groovy
environment {
    DB_PASS = credentials('db-password-id')
}
```

## Агенты / масштабирование

- **Static agents** — постоянные машины, подключённые к master по SSH или JNLP.
- **Dynamic agents** — поднимаются по требованию (Docker, Kubernetes plugin) и удаляются после job — экономит ресурсы, чище окружение.

```groovy
pipeline {
    agent {
        docker { image 'node:20-alpine' }
    }
    ...
}
```

## Jenkins vs GitHub Actions / GitLab CI

| | Jenkins | GitHub Actions / GitLab CI |
|---|---|---|
| Хостинг | self-hosted (сам администрируешь) | SaaS (или self-hosted runner) |
| Гибкость | максимальная (тысячи плагинов) | ограничена возможностями платформы |
| Порог входа | выше (нужно поддерживать сервер, плагины, апдейты) | ниже, всё из коробки |
| Когда выбирать | сложные legacy-инфры, on-prem требования, специфичные интеграции | большинство современных проектов на GitHub/GitLab |

#jenkins #cicd #devops #pipelines
