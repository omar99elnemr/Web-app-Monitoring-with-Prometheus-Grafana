# Taskmaster Application Monitoring

The application specifically used as the target for monitoring in this demonstration is a **Java-based web application**. This application is referred to as the **Taskmaster application**.

Here's a detailed breakdown of the application and its role in the monitoring setup:

## Nature and Purpose

The Taskmaster application is a **Java-based web application** that serves as the "website" or "application" to be monitored. The primary goal is to demonstrate how to track its health and status.

## Deployment and Accessibility

- It is deployed and **runs on the first virtual machine**, which is designated as the **"application server"**.
- The application is accessible via a web browser on **Port 880** using the IP address of the application server.
- To prepare the environment for this application, Java (specifically OpenJDK 17 headless) and Maven are installed on the application server, and the application's repository is cloned, built, and then run as a `.jar` file in the background.
- {Screenshot placeholder: showing the application running in a browser at IP:880}

## Specific Monitoring Objectives

For this web application, the monitoring focuses on key aspects of its availability and security:

- **Up/Down Status**: Whether the website is operational.
- **Reachability**: If it can be successfully accessed.
- **SSL Certificate Status**: Whether it possesses an SSL certificate.

## Monitoring Tool – Blackbox Exporter

- The **Blackbox Exporter** is the specific tool used to gather metrics for the Taskmaster web application.
- It operates by sending "probes" or **HTTP requests** to the website to check its reachability and **HTTP status** (e.g., looking for an HTTP 200 response for success). It also checks for the presence of an SSL certificate.
- Notably, the Blackbox Exporter **does not need to be installed on the same server where the Taskmaster application is running**. It can be installed anywhere, typically on the "monitoring server", as it simply sends requests to the target website's URL to gather information.
- {Screenshot placeholder: showing Blackbox Exporter running and listening on Port 9115}

## Prometheus Configuration

- The URL of the Taskmaster application (the IP address of the application server on Port 880) is explicitly added as a target within the `prometheus.yaml` configuration file for the Blackbox Exporter to monitor.
- Prometheus, the core monitoring component and data source, then scrapes these metrics collected by the Blackbox Exporter.
- {Screenshot placeholder: showing the `prometheus.yaml` configuration with the Taskmaster application's URL}
- {Screenshot placeholder: showing the Prometheus UI with the Taskmaster application's URL listed as an "up" target}

## Grafana Visualisation

- A dedicated Grafana dashboard for Blackbox Exporter is imported and configured to display the monitoring data for the Taskmaster application.
- This dashboard visually presents the application's status (e.g., "up"), HTTP status code (e.g., "200"), and whether an SSL certificate is present. It also shows probe and HTTP duration, indicating the time taken for requests.
- This makes the collected metrics easily understandable for human analysis.
- {Screenshot placeholder: showing the Grafana Blackbox Exporter dashboard displaying the Taskmaster application's status}

## Summary

In summary, the **Taskmaster application** is the key **web application** whose performance and availability are continuously monitored throughout the demonstration, leveraging Blackbox Exporter for metric collection, Prometheus for storage and scraping, and Grafana for insightful visualisation.
