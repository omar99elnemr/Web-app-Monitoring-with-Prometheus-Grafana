# Web-app-Monitoring-with-Prometheus-Grafana

## Overview
This project demonstrates a comprehensive **DevOps monitoring solution** for applications and servers from scratch. It provides a step-by-step guide to set up a robust monitoring system using industry-standard tools like Prometheus, Grafana, Node Exporter, and Blackbox Exporter. The goal is to provide deep insights into how to monitor application availability, server health, and visualize key metrics effectively.

## Introduction
Monitoring is a critical aspect of DevOps, ensuring that applications and infrastructure are performing optimally and issues are identified and resolved quickly. This project focuses on implementing monitoring to understand if a website is up and reachable, if it has a valid SSL certificate, and to monitor server-level metrics such as CPU, RAM, and disk usage.

## Components to Monitor
The monitoring setup targets two core areas:

*   **Website Monitoring**:
    *   Checking if the **website is up or down**.
    *   Verifying if it is **successfully reachable**.
    *   Detecting the presence and validity of an **SSL certificate**.
*   **Server/Node Monitoring**:
    *   Monitoring **node metrics** of the server where the application is deployed or other services are running.
    *   Key metrics include **CPU usage**, **RAM usage**, and **disk usage**.

## Tools Used

This project leverages a powerful stack of open-source tools:

*   **Prometheus**:
    *   The **core component** that performs the actual monitoring.
    *   Acts as a **data source** for collecting metrics.
    *   Specialises in **Time Series Data (TSD)**, collecting metrics at regular intervals.
    *   Stores collected metrics in a custom **Time Series Database (TSDB)**.
    *   Allows querying of data using its specific language, **PromQL**.
    *   Prometheus accesses metrics exposed on specific endpoints, typically `/metrics`.

*   **Node Exporter**:
    *   An **exporter** responsible for collecting **node (server/virtual machine) metrics**.
    *   Collects information like **CPU usage, RAM usage, and disk usage**.
    *   **Must be installed on the server** (or worker node) that you intend to monitor for these metrics.
    *   Exposes metrics on a `/metrics` endpoint, typically on port `9100`.

*   **Blackbox Exporter**:
    *   An **exporter** designed to monitor **website reachability and status**.
    *   Checks if a website is **up, reachable, and has an SSL certificate**.
    *   **Does not need to be installed on the server** where the application is running; it can be installed anywhere and sends requests to the website URL.
    *   Used by Prometheus to send "probes" (requests) to target websites.

*   **Grafana**:
    *   A powerful **visualization tool**.
    *   Connects to Prometheus as a data source to **present collected metrics in an understandable format**.
    *   Allows creation of **tables, charts, and graphs** for easy analysis of data.
    *   Simplifies data analysis for human operators.

## Architecture and Flow

The monitoring setup follows this flow:

1.  **Metric Collection**:
    *   **Node Exporter** is installed on the **application server** to collect detailed server-level metrics (CPU, RAM, disk).
    *   **Blackbox Exporter** is installed on a **separate monitoring server** (or any location) and sends probes to the website URL to check its status.
    *   Both exporters make the collected metrics available on specific `/metrics` endpoints.

2.  **Data Ingestion and Storage**:
    *   **Prometheus** is installed on the **monitoring server**.
    *   Prometheus is configured to **scrape (collect) metrics** from the Node Exporter and Blackbox Exporter endpoints at regular intervals.
    *   These collected metrics are then stored in **Prometheus's Time Series Database (TSDB)**.

3.  **Data Visualization**:
    *   **Grafana** is installed on the **monitoring server** alongside Prometheus.
    *   Grafana is configured to use **Prometheus as its data source**.
    *   Dashboards are imported or created in Grafana to **visualize the metrics** collected by Prometheus in an easy-to-understand graphical format, allowing for real-time monitoring and analysis.

```
[Diagram Placeholder: A high-level architectural diagram showing Application Server, Monitoring Server, and the flow between Node Exporter, Blackbox Exporter, Prometheus, and Grafana.]
```

## Setup and Implementation Steps

This demo is implemented using **two AWS EC2 virtual machines** for clarity, though it can be adapted for Kubernetes environments.

### 1. Set Up Virtual Machines
*   **Application Server**: A T2 medium instance (e.g., Ubuntu 24.04) to host the application and Node Exporter.
*   **Monitoring Server**: A T2 large instance (e.g., Ubuntu 24.04) to host Prometheus, Blackbox Exporter, and Grafana.
*   Ensure **appropriate security group rules** are configured to allow necessary port access (e.g., 8080 for app, 9100 for Node Exporter, 9090 for Prometheus, 9115 for Blackbox Exporter, 3000 for Grafana).

### 2. Deploy Application (on Application Server)
*   Update system packages: `sudo apt update`.
*   Install Java (e.g., OpenJDK 17 headless) and Maven: `sudo apt install openjdk-17-jre-headless maven`.
*   Clone the demo Java application repository.
*   Build the application using Maven: `mvn package`.
*   Run the application in the background: `java -jar target/<app-jar-file>.jar &`.
*   Verify application accessibility on `http://<Application_Server_IP>:8080`.

```
[Screenshot Placeholder: Application running in browser, e.g., Taskmaster app.]
```

### 3. Install and Run Node Exporter (on Application Server)
*   Download Node Exporter from Prometheus downloads page.
*   Extract the downloaded tar file: `tar -xvf node_exporter-<version>.tar.gz`.
*   Rename the extracted directory to `node_exporter` for simplicity.
*   Run Node Exporter in the background: `./node_exporter &`.
*   Verify Node Exporter metrics endpoint accessibility on `http://<Application_Server_IP>:9100/metrics`.

```
[Screenshot Placeholder: Node Exporter metrics endpoint in browser.]
```

### 4. Install and Configure Prometheus (on Monitoring Server)
*   Download Prometheus from Prometheus downloads page.
*   Extract the downloaded tar file: `tar -xvf prometheus-<version>.tar.gz`.
*   Rename the extracted directory to `prometheus`.
*   **Edit `prometheus.yaml` configuration file**:
    *   Add a `scrape_config` for **Node Exporter**, specifying its job name and the target URL (`<Application_Server_IP>:9100`).
    *   Add a `scrape_config` for **Blackbox Exporter**, configuring `module: 2xx` (for HTTP 200 responses) and listing the target URLs (e.g., your application URL: `http://<Application_Server_IP>:8080`, and other URLs like `https://github.com`). Ensure correct indentation.
*   Run Prometheus in the background: `./prometheus &`.
*   Access Prometheus UI on `http://<Monitoring_Server_IP>:9090`.
*   Go to **Status > Targets** to confirm Node Exporter and Blackbox Exporter targets are `UP`.

```
[Screenshot Placeholder: Prometheus UI showing targets health.]
```

### 5. Install and Run Blackbox Exporter (on Monitoring Server)
*   Download Blackbox Exporter from Prometheus downloads page.
*   Extract the downloaded tar file: `tar -xvf blackbox_exporter-<version>.tar.gz`.
*   Rename the extracted directory to `blackbox_exporter`.
*   Run Blackbox Exporter in the background: `./blackbox_exporter &`.
*   Verify Blackbox Exporter accessibility on `http://<Monitoring_Server_IP>:9115`.

### 6. Install and Configure Grafana (on Monitoring Server)
*   Download and install Grafana using `wget`, `dpkg` commands.
*   Start Grafana service: `sudo systemctl daemon-reload`, `sudo systemctl enable grafana-server`, `sudo systemctl start grafana-server`.
*   Access Grafana UI on `http://<Monitoring_Server_IP>:3000`.
*   Default login: `admin`/`admin`. Change password upon first login.
*   **Add Prometheus as a Data Source**:
    *   Navigate to **Connections > Data Sources**.
    *   Search for and select `Prometheus`.
    *   Enter the Prometheus URL: `http://localhost:9090` (or `http://<Monitoring_Server_IP>:9090`).
    *   Click **Save & Test**.

```
[Screenshot Placeholder: Grafana UI after adding Prometheus data source.]
```

### 7. Import Grafana Dashboards
*   **Import Node Exporter Dashboard**:
    *   In Grafana, go to **Dashboards > New > Import**.
    *   Enter the official Node Exporter Dashboard ID: **`1860`**.
    *   Select your Prometheus data source and click **Import**.
    *   Adjust refresh rate (e.g., 5 seconds) and time range (e.g., Last 5 minutes).

```
[Screenshot Placeholder: Grafana Node Exporter Dashboard showing CPU, RAM, Disk usage.]
```

*   **Import Blackbox Exporter Dashboard**:
    *   In Grafana, go to **Dashboards > New > Import**.
    *   Search for "blackbox grafana dashboard" to find an ID (e.g., from Grafana Labs official dashboards).
    *   Enter the Blackbox Exporter Dashboard ID.
    *   Select your Prometheus data source and click **Import**.
    *   Adjust refresh rate and time range.

```
[Screenshot Placeholder: Grafana Blackbox Exporter Dashboard showing website status (up/down, HTTP status, SSL status, probe duration).]
```

## Conclusion
By following these steps, you will have a functional DevOps monitoring setup capable of providing real-time insights into your application's availability and server's health. This project provides a solid foundation for further exploration into advanced monitoring concepts.
