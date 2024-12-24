# Monitoring and Alerting System - Docker Compose Configuration

This document provides a detailed explanation of the Docker Compose configuration file for deploying a monitoring and alerting stack using Prometheus, Alertmanager, and related components.

---

## Overview

This stack consists of the following services:

1. **Prometheus**: A metrics collection and alerting tool.
2. **PVE Exporter**: Exposes metrics from Proxmox Virtual Environment (PVE) for Prometheus.
3. **Mailrise**: Converts Prometheus alerts into email notifications.
4. **Alertmanager**: Handles alerts sent by Prometheus.

The configuration uses Docker volumes for persistent storage and ensures service reliability with restart policies.

---

## Components and Configuration

### 1. **Volumes**
- **`prometheus-data`**:
  - Used to store Prometheus' data persistently.
  
- **`alertmanager-data`**:
  - Used to store Alertmanager's data persistently.

These volumes ensure that Prometheus and Alertmanager retain data across container restarts.

---

### 2. **Services**

#### **Prometheus**
- **Image**: `prom/prometheus:latest`
- **Container Name**: `prometheus`
- **Ports**: 
  - Exposes port `9090` for accessing the Prometheus web interface.
- **Volumes**:
  - `./prometheus/prometheus.yml`: Prometheus configuration file.
  - `prometheus-data`: Volume for storing Prometheus data.
  - `./prometheus/rules.yml`: Configuration for Prometheus rules.
- **Command**:
  - `--web.enable-lifecycle`: Enables HTTP endpoints for lifecycle management.
  - `--config.file=/etc/prometheus/prometheus.yml`: Specifies the Prometheus configuration file.
- **Restart Policy**: `unless-stopped`, ensuring the service restarts unless manually stopped.

#### **PVE Exporter**
- **Image**: `prompve/prometheus-pve-exporter:latest`
- **Container Name**: `pve-exporter`
- **Ports**:
  - Exposes port `9221` for Prometheus to scrape PVE metrics.
- **Volumes**:
  - `./pve/pve.yml`: Configuration file for the PVE Exporter.
- **Restart Policy**: `unless-stopped`.

#### **Mailrise**
- **Image**: `yoryan/mailrise:latest`
- **Container Name**: `mailrise`
- **Ports**:
  - Exposes port `8025` for SMTP email forwarding.
- **Volumes**:
  - `./mailrise/mailrise.conf`: Configuration file for Mailrise.
- **Restart Policy**: `unless-stopped`.

#### **Alertmanager**
- **Image**: `quay.io/prometheus/alertmanager:latest`
- **Container Name**: `alertmanager`
- **Ports**:
  - Exposes port `9093` for accessing the Alertmanager web interface.
- **Volumes**:
  - `./alertmanager/alertmanager.yml`: Alertmanager configuration file.
  - `alertmanager-data`: Volume for storing Alertmanager data.
- **Restart Policy**: `unless-stopped`.

---

### 3. **Networks**

The stack uses a default bridge network with custom options:
- **Driver**: `bridge`.
- **Driver Options**:
  - `com.docker.network.driver.mtu`: Sets the Maximum Transmission Unit (MTU) to `1400`.

---

## Usage Instructions

### **Prerequisites**
1. Install Docker and Docker Compose.
2. Ensure the configuration files (`prometheus.yml`, `rules.yml`, `pve.yml`, `mailrise.conf`, `alertmanager.yml`) are present in their respective directories.

### **Run the Stack**
1. Start the services:
   ```bash
   docker-compose up -d

