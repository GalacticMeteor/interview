# Jenkins Essential Course

## 1. Introduction to Jenkins

### What is Jenkins?
Jenkins is an **open-source automation server** used to implement **Continuous Integration (CI) and Continuous Delivery (CD)**. It helps automate tasks like:
- Building code
- Running tests
- Packaging applications
- Deploying software

It acts as the **central orchestrator** in DevOps pipelines.

### Why Jenkins?
- Free and open-source with a huge community
- Supports hundreds of plugins for integrations
- Platform-independent (runs on Linux, Windows, macOS)
- Enables building CI/CD pipelines for almost any language or platform

### Prerequisites
Before using Jenkins, you should know:
- Basic Linux commands and administration
- Git and version control concepts
- Build tools like Maven or Gradle
- Application frameworks like Java Spring Boot or Angular

---

## 2. Installing Jenkins

### Installation Methods
1. **On a VM**: Using Linux packages or Docker.
2. **Using Docker**: Pull Jenkins image from Docker Hub.
3. **Jenkins WAR file**: Run directly with Java (`java -jar jenkins.war`).

### Jenkins CLI
Jenkins provides a **Command-Line Interface** to perform tasks like:
- Creating jobs
- Managing plugins
- Triggering builds
- Checking build status

Example CLI command:
```bash
java -jar jenkins-cli.jar -s http://localhost:8080/ help
```

---

## 3. Jenkins Plugins and Integrations

### What are Plugins?
Plugins extend Jenkins functionality. They allow integration with:
- Source control (Git, GitHub, GitLab)
- Build tools (Maven, Gradle)
- Notification tools (Slack, Email)
- Container platforms (Docker, Kubernetes)

### Plugin Management
- **Search & Install Plugins**: Manage Jenkins > Manage Plugins > Available
- **Update or Remove Plugins**: Manage Plugins > Installed
- **Restart Jenkins** may be required for some plugin changes

### Example Useful Plugins
- **Git Plugin**: Integrate with Git repositories
- **Pipeline Plugin**: Write CI/CD pipelines as code
- **Blue Ocean**: Visual dashboard for pipelines
- **Maven Integration Plugin**: Run Maven builds

---

## 4. Exploring the Jenkins UI

- **Dashboard**: Shows all jobs, builds, and pipelines
- **Manage Jenkins**: Configure global settings, credentials, and tools
- **Users and Teams**: Add users, roles, and assign permissions
- **Build History**: Review past builds and logs
- **Global Tool Configurations**: Configure Java, Maven, Git, and other tools

---

## 5. Jenkins Administration

### Key Tasks
- **Backup & Restore**: Save Jenkins configuration and job data
- **Monitoring**: Track performance and uptime, integrate with Prometheus for metrics
- **Security**: Enable authentication, role-based access, and controller isolation

---

## 6. Jenkins Pipelines

### What is a Jenkinsfile?
A `Jenkinsfile` defines a **pipeline as code**. It can be **Declarative** or **Scripted**.

### Example CI/CD Pipeline: Java Spring Boot + Angular + Maven

```groovy
pipeline {
    agent any

    tools {
        jdk 'JDK11'
        maven 'Maven3'
        nodejs 'NodeJS'
    }

    environment {
        APP_NAME = 'my-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/username/my-app.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('Test') {
            steps {
                dir('backend') {
                    sh 'mvn test'
                }
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                // Deployment script here (e.g., Docker/Kubernetes)
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```

### Notes
- `agent any` lets Jenkins pick any available executor
- `tools` section installs required JDK, Maven, and NodeJS versions
- Stages are modular: checkout → build → test → deploy

---

## 7. Build Agents

- **What are Build Agents?** Servers or containers that run jobs
- Use **Ubuntu servers** or **Docker containers** as agents
- Agents allow scaling and distributing workload

Example agent declaration in pipeline:
```groovy
agent {
    label 'ubuntu-agent'
}
```

---

## 8. Blue Ocean

- A **modern Jenkins UI** for visual pipelines
- Shows pipelines as **graphs with stages, steps, and results**
- Allows easier tracking of multi-branch pipelines
- Install via Jenkins > Manage Plugins > Blue Ocean

---

## 9. Securing Jenkins

- Enable **security and authentication** (LDAP, GitHub OAuth, or Jenkins own database)
- Use **role-based access control** for jobs and builds
- **Controller Isolation**: Keep build agents isolated from Jenkins master
- **Pipeline Security Matrix**: Define which scripts can run in pipeline

---

### Summary
This course covers:
- Jenkins basics, installation, and UI
- Plugins and integrations
- Admin tasks like backup, restore, and monitoring
- Pipelines with a real-world CI/CD example (Spring Boot + Angular + Maven)
- Build agents and Blue Ocean dashboard
- Security best practices

