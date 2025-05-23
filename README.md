# SonarQube Setup with CNES Report Plugin

This repository contains a Docker-based setup to run **SonarQube** with a **PostgreSQL** database, and instructions to install the **CNES Report plugin**.

## 📁 Directory Structure

```
.
├── docker-compose.yaml
├── Readme.txt
├── sonar-cnes-report-4.3.0.jar
├── sonar-cnes-report-4.3.0.tar.gz
└── raw-instructions.txt
```

---

## 🐳 Prerequisites

* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)

---

## 🚀 Steps to Deploy SonarQube

### 1. Start SonarQube and PostgreSQL

Run the following command to start the containers:

```bash
docker-compose up -d
```

This will:

* Launch SonarQube on `http://localhost:9000`
* Create a PostgreSQL database for SonarQube

---

### 2. Verify SonarQube Access

Open your browser and navigate to:

```
http://localhost:9000
```

Login with the default credentials:

* **Username**: `admin`
* **Password**: `admin` (you’ll be prompted to change this on first login)

---

### 3. Check for CNES Report Plugin

Once logged in:

* Click on the top-right menu
* Go to **"More" → "CNES Report"**

If the **CNES Report** page is available, the plugin is already installed.

---

## 🥉 CNES Report Plugin Installation (If Not Present)

If CNES Report is not listed:

### 1. Ensure `sonar-cnes-report-4.3.0.jar` is Available

If the file is missing, download it from the [official GitHub releases](https://github.com/cnescatlab/sonar-cnes-report/releases) or use the version mentioned:

* Version: `sonar-cnes-report-4.3.0.jar`

### 2. Copy Plugin to Running Container

Use the following command to copy the plugin into the running SonarQube container:

```bash
docker cp sonar-cnes-report-4.3.0.jar sonarqube:/opt/sonarqube/extensions/plugins/
```

### 3. Restart the SonarQube Container

```bash
docker restart sonarqube
```

After restart, the plugin should appear under the **"More"** section.

---

## 📛 Stop Services

To stop the containers:

```bash
docker-compose down
```

---

## 📝 Notes

* SonarQube runs on port `9000`.
* PostgreSQL database credentials are hardcoded as `sonar/sonar`. Update in `docker-compose.yaml` as needed.
* You can manage logs, data, and extensions using the mounted volumes declared in the Compose file.

---

## 📄 License

MIT
