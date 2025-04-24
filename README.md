# Prometheus-Monitoring-for-5G-RAN-with-Helm-and-Documentation
Use Helm to deploy a 5G RAN simulator and Prometheus sidecar for  metrics scraping. Write a comprehensive technical paper describing  the deployment steps, key metrics tracked, and how the monitored  data can guide network optimization.

## Table of Contents
- [Overview of Open5GS 5GC Simulation Mobile Network](#overview-of-open5gs-5gc-simulation-mobile-network)
- [Additional changes in configuration files](#additional-changes-in-configuration-files-of-open5gs-5gc-c-plane-and-u-plane)
- [Build Open5GS for using Prometheus](#build-open5gs-for-using-prometheus)
- [Run Prometheus](#run-prometheus)
- [Web Access to Prometheus Dashboard](#web-access-to-prometheus-dashboard)
- [Metrics of Open5GS AMF](#metrics-of-open5gs-amf)
- [Metrics of Open5GS PCF](#metrics-of-open5gs-pcf)
- [Metrics of Open5GS SMF](#metrics-of-open5gs-smf)
- [Metrics of Open5GS UPF](#metrics-of-open5gs-upf)
- [Run Grafana](#run-grafana)
- [Web Access to Grafana Dashboard](#web-access-to-grafana-dashboard)
- [Prometheus data source](#prometheus-data-source)
- [Example of setting visualization of metrics](#example-of-setting-visualization-of-metrics)
- [Changelog](#changelog-summary)

## Overview of Open5GS 5GC Simulation Mobile Network

This is an additional setting example when monitoring the metrics with Prometheus for the following sample configuration.

[Open5GS 5GC & UERANSIM UE / RAN Sample Configuration - Select UPF based on S-NSSAI]()

This example configuration for monitoring metrics with Prometheus is shown in the figure below. NFs without metrics are not drawn in the figure.

![Network Overview](./images/network-overview.png)

The 5GC / UE / RAN used are as follows. Also, I started Prometheus and Grafana using Docker.

- 5GC - Open5GS v2.6.1 (2023.03.10) - [https://github.com/open5gs/open5gs](https://github.com/open5gs/open5gs)
- UE / RAN - UERANSIM v3.2.6 - [https://github.com/aligungr/UERANSIM](https://github.com/aligungr/UERANSIM)

The IP address and port of the monitored NFs are as follows.

| NF    | IP address     | port      | Prometheus job_name    |
|-------|----------------|-----------|------------------------|
| AMF   | 192.168.0.111  | 9090/tcp  | open5gs-amfd           |
| PCF   | 192.168.0.111  | 9091/tcp  | open5gs-pcfd           |
| SMF1  | 192.168.0.112  | 9090/tcp  | open5gs-smfd1          |
| SMF2  | 192.168.0.113  | 9090/tcp  | open5gs-smfd2          |
| UPF1  | 192.168.0.114  | 9090/tcp  | open5gs-upfd1          |
| UPF2  | 192.168.0.115  | 9090/tcp  | open5gs-upfd2          |

The exposed IP address and port of Prometheus and Grafana-OSS are as follows.

| Server       | IP address     | port       |
|--------------|----------------|------------|
| Prometheus   | 192.168.0.111  | 9092/tcp   |
| Grafana-OSS  | 192.168.0.111  | 3001/tcp   |

## Additional changes in configuration files of Open5GS 5GC C-Plane and U-Plane

In this case, the following configuration is further changed for monitoring the metrics with Prometheus.

[Open5GS 5GC & UERANSIM UE / RAN Sample Configuration - Select UPF based on S-NSSAI]()

I will explain what to set additionally here. When monitoring the metrics with Prometheus, change the following configuration files for each NF of C-Plane and U-Plane.

### AMF Configuration
`open5gs/install/etc/open5gs/amf.yaml`
```yaml
metrics:
  - addr: 192.168.0.111
    port: 9090
