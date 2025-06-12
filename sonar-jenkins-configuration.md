# 📘 SonarQube - Jenkins Integration Guide

This guide walks you through integrating **SonarQube** with **Jenkins** using Docker and pipeline jobs to enable static code analysis and enforce quality gates.

---

## 🛠️ Prerequisites

* **Jenkins** installed with the following plugins:

  * `SonarQube Scanner`
  * `SonarQube Quality Gates`
* **SonarQube** installed (recommended: using Docker Compose)

---

## ⚙️ SonarQube Configuration

1. **Create Project**:

   * Go to SonarQube UI → `Projects` → `Create Project`
   * Note the **Project Key**
   * Create a **project token** and save it

2. **Create User Token**:

   * Go to `My Account` → `Security` → Generate a **user token**
   * Save this token (used for Jenkins authentication)

3. **Configure Quality Gates**:

   * Go to `Quality Gates`
   * Use default (`Sonar way`) or create a new one as needed

4. **Create Webhook**:
this is used to communicate to the jenkins to send the status of the quality gate report
   * Navigate to the project → `Project Settings` → `Webhooks`
   * Create a new webhook:

     * **Name**: `Jenkins`
     * **URL**: `http://<jenkins-ip>:8080/sonarqube-webhook/`

---

## 🧰 Jenkins Configuration

### 1. Create Pipeline Project

* Create a new **Pipeline** job in Jenkins

### 2. Add Sonar Tokens to Credentials

* Go to `Manage Jenkins` → `Credentials` → `System` → `Global`
* Add two **Secret Text** credentials:

  * `sonar-project-token`
  * `sonar-user-token`

### 3. Configure SonarQube Server

* `Manage Jenkins` → `Configure System`
* Under **SonarQube Servers**:

  * Check **Environment variables**
  * **Name**: `sonarqube-server` *(Used in pipeline)*
  * **Server URL**: `http://localhost:9000`
  * **Authentication Token**: Select `sonar-user-token`

### 4. Configure SonarQube Quality Gates

* In the **Configure System** section under **Quality Gates**:

  * **Name**: `sonarqube-scanner`
  * **Server URL**: `http://localhost:9000`
  * **Login Credentials**: Provide username and password (ignore account token)

### 5. Configure SonarQube Scanner

* `Manage Jenkins` → `Global Tool Configuration`
* Under **SonarQube Scanner**:

  * **Name**: `SonarQube-scanner`
  * Check **Install automatically**
  * Save

---

## 🧪 Jenkins Pipeline Code

### 🔍 SonarQube Scan Stage

```groovy
stage('Sonar Scan') {
    withSonarQubeEnv('sonarqube-server') {
        sh 'sonar-scanner -Dsonar.projectKey=myProject'
    }
}
```

> `sonarqube-server` must match the server name set in Jenkins global config.

### ✅ Quality Gate Stage (Non-blocking)

This runs even if the quality gate fails:

```groovy
stage('Quality Gate') {
    timeout(time: 5, unit: 'MINUTES') {
        waitForQualityGate()
    }
}
```

### ⛔ Quality Gate Stage (Blocking)

This **aborts** the pipeline if the quality gate fails:

```groovy
stage('Quality Gate') {
    timeout(time: 2, unit: 'MINUTES') {
        waitForQualityGate abortPipeline: true
    }
}
```

---

## 🔗 Reference

* 📺 [SonarQube + Jenkins Integration Video](https://youtu.be/KsTMy0920go?si=ibmllDUCDcU4YiGk)
