# 🚀 CI/CD Pipeline with Jenkins, Maven, SonarQube, and Artifactory

This project outlines the setup and configuration of a robust CI/CD pipeline utilizing Jenkins for automation, Maven for build management, SonarQube for code quality analysis, and Artifactory for artifact storage. The infrastructure is deployed on AWS EC2 instances, ensuring scalability and reliability.

---

## 🗂️ Project Overview

- **Version Control**: GitLab (Self-Hosted)
- **Build Automation**: Jenkins
- **Build Tool**: Maven
- **Code Quality Analysis**: SonarQube
- **Artifact Repository**: JFrog Artifactory
- **Containerization**: Docker & Docker Compose
- **Infrastructure**: AWS EC2 Instances

---

## 🧱 Infrastructure Components

### 1. **GitLab (Self-Hosted)**

- Hosts the source code repositories.
- Configured with webhooks to trigger Jenkins pipelines on code changes.

### 2. **Jenkins**

- Manages the CI/CD pipeline.
- Installed with necessary plugins:
  - gitLab plugin
  - Maven Integration
  - Artifactory Plugin
  - SonarQube Scanner
  - Multi-Branch Pipeline
  - Email Extension Plugin

### 3. **Jenkins Agent**

- Executes the pipeline stages.
- Runs Docker containers for:
  - SonarQube
  - PostgreSQL (SonarQube Database)
  - Maven (Build Environment)

### 4. **SonarQube**

- Analyzes code for bugs, vulnerabilities, and code smells.
- Accessed via `http://<agent-ip>:9000`.

### 5. **JFrog Artifactory**

- Stores build artifacts.
- Accessed via `http://<artifactory-ip>:8081/artifactory`.

---

## 🔐 Security Groups Configuration

To ensure secure communication between services, the following AWS Security Groups are configured:

- **Jenkins-SG**:

  - Inbound: Port 8080 (HTTP), Port 22 (SSH)
  - Outbound: All traffic

- **Artifactory-SG**:

  - Inbound: Port 8081 (HTTP), Port 22 (SSH)
  - Outbound: All traffic

- **SonarQube-SG**:

  - Inbound: Port 9000 (HTTP), Port 22 (SSH)
  - Outbound: All traffic

- **GitLab-SG**:
  - Inbound: Port 80 (HTTP), Port 22 (SSH)
  - Outbound: All traffic

Ensure that the security groups allow necessary inter-service communication while restricting unauthorized access.

---

## ⚙️ Jenkins Pipeline Overview

The Jenkins pipeline is defined using a declarative syntax and includes the following stages:

1. **Environment**: Verifies the environment vars, path and Maven installation.
2. **Checkout**: Retrieves the latest code from the GitLab repository (git-flow branch strategy strategy).
3. **SonarQube Analysis**: Performs static code analysis to ensure code quality.
4. **Build Artifact with Maven**: Compiles the source code and packages it into a JAR file.
5. **Push to Artifactory**: Deploys the build artifact to JFrog Artifactory.
6. **Post-Build Actions**:
   - Cleans up the workspace.
   - Sends email notifications based on the build result.

---

## 📝 Maven Configuration (`settings.xml`)

To authenticate with Artifactory, the Maven `settings.xml` is configured with the following server credentials:

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0">
  <servers>
    <server>
      <id>artifactory-releases</id>
      <username>admin</username>
      <password>your_password</password>
    </server>
    <server>
      <id>artifactory-snapshots</id>
      <username>admin</username>
      <password>your_password</password>
    </server>
  </servers>
</settings>
```

Replace `your_password` with the actual password. Ensure this file is placed in the appropriate directory (e.g., `/home/ubuntu/.m2/settings.xml`) on the Jenkins agent.

---

## 🧪 SonarQube Project Configuration

The `sonar-project.properties` file at the root of the project defines the SonarQube analysis parameters:

```properties
sonar.projectKey=project-key
sonar.projectName=Project Name
sonar.projectVersion=1.0
sonar.sources=src
sonar.host.url=http://<agent-ip>:9000
```

Replace `<agent-ip>` with the actual IP address of the Jenkins agent hosting SonarQube.

---

## 📦 Artifact Deployment

Artifacts are deployed to Artifactory using the Maven Deploy Plugin. The `pom.xml` includes the distribution management configuration:

```xml
<distributionManagement>
    <repository>
        <id>artifactory-releases</id>
        <url>http://<artifactory-ip>:8081/artifactory/calc-app-repo</url>
    </repository>
    <snapshotRepository>
        <id>artifactory-snapshots</id>
        <url>http://<artifactory-ip>:8081/artifactory/calc-app-repo</url>
    </snapshotRepository>
</distributionManagement>
```

Replace `<artifactory-ip>` with the actual IP address of the Artifactory server.

---

## 📬 Email Notifications

Post-build email notifications are configured using the Email Extension Plugin. Notifications are sent to `your-group-email@gmail.com` with the build status.

---

## 🛠️ Additional Configurations

- **Webhooks**: Ensure that GitLab webhooks are updated with the public IP addresses, as they may change with each bot deployment.
- **Docker Compose**: The SonarQube and PostgreSQL containers are managed using Docker Compose on the Jenkins agent.
- **Credentials Management**: Store sensitive information, such as tokens and passwords, securely within Jenkins Credentials Manager.

---

## 📌 Conclusion

By completing this project, I gained real-world experience in designing and deploying scalable CI/CD pipelines, integrating key DevOps tools like Jenkins, Maven, SonarQube, and Artifactory on AWS infrastructure.

---
