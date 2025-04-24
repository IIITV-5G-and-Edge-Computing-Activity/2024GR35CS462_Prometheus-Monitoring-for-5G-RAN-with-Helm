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

![Network Overview(![image](https://github.com/user-attachments/assets/6707a267-12f2-4414-9435-0d57ce41e32a)
)

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
```
Build Open5GS for using Prometheus
Open5GS sets Prometheus metrics by default. Please refer to the following for building Open5GS.

https://open5gs.org/open5gs/docs/guide/02-building-open5gs-from-sources/

Please install the following to run Prometheus on Docker.

docker-ce

Run Prometheus
Please refer to the following for using Prometheus.

https://open5gs.org/open5gs/docs/tutorial/04-metrics-prometheus/

Web Access to Prometheus Dashboard
First, create the following prometheus.yml.
```yaml
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: open5gs-amfd
    static_configs:
      - targets: ["192.168.0.111:9090"]
  - job_name: open5gs-pcfd
    static_configs:
      - targets: ["192.168.0.111:9091"]
  - job_name: open5gs-smfd1
    static_configs:
      - targets: ["192.168.0.112:9090"]
  - job_name: open5gs-smfd2
    static_configs:
      - targets: ["192.168.0.113:9090"]
  - job_name: open5gs-upfd1
    static_configs:
      - targets: ["192.168.0.114:9090"]
  - job_name: open5gs-upfd2
    static_configs:
      - targets: ["192.168.0.115:9090"]
```
After starting Open5GS, run Prometheus as follows.
```yaml
    docker run -d -p 9090:9090 -v `pwd`/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```
You can access the following URL with web browser.


The list of targets is as follows.

Prometheus Targets
![WhatsApp Image 2025-04-23 at 23 26 16_1d53e4d0](https://github.com/user-attachments/assets/5330c80d-4c47-43d1-8e70-2aacc729baaf)


Metrics of Open5GS AMF
Following the Endpoint link of job_name=open5gs-amfd, the metrics will be displayed as follows.
```yaml
# HELP gnb gNodeBs
# TYPE gnb gauge
gnb 0

# HELP fivegs_amffunction_mm_confupdate Number of UE Configuration Update commands requested by the AMF
# TYPE fivegs_amffunction_mm_confupdate counter
fivegs_amffunction_mm_confupdate 0

# HELP fivegs_amffunction_rm_reginitreq Number of initial registration requests received by the AMF
# TYPE fivegs_amffunction_rm_reginitreq counter
fivegs_amffunction_rm_reginitreq 0

# HELP fivegs_amffunction_rm_regemergreq Number of emergency registration requests received by the AMF
# TYPE fivegs_amffunction_rm_regemergreq counter
fivegs_amffunction_rm_regemergreq 0

# HELP fivegs_amffunction_mm_paging5greq Number of 5G paging procedures initiated at the AMF
# TYPE fivegs_amffunction_mm_paging5greq counter
fivegs_amffunction_mm_paging5greq 0

# HELP fivegs_amffunction_rm_regperiodreq Number of periodic registration update requests received by the AMF
# TYPE fivegs_amffunction_rm_regperiodreq counter
fivegs_amffunction_rm_regperiodreq 0

# HELP fivegs_amffunction_mm_confupdatesucc Number of UE Configuration Update complete messages received by the AMF
# TYPE fivegs_amffunction_mm_confupdatesucc counter
fivegs_amffunction_mm_confupdatesucc 0

# HELP fivegs_amffunction_rm_reginitsucc Number of successful initial registrations at the AMF
# TYPE fivegs_amffunction_rm_reginitsucc counter
fivegs_amffunction_rm_reginitsucc 0

# HELP fivegs_amffunction_amf_authreject Number of authentication rejections sent by the AMF
# TYPE fivegs_amffunction_amf_authreject counter
fivegs_amffunction_amf_authreject 0

# HELP fivegs_amffunction_rm_regmobreq Number of mobility registration update requests received by the AMF
# TYPE fivegs_amffunction_rm_regmobreq counter
fivegs_amffunction_rm_regmobreq 0

# HELP amf_session AMF Sessions
# TYPE amf_session gauge
amf_session 0

# HELP fivegs_amffunction_rm_regmobsucc Number of successful mobility registration updates at the AMF
# TYPE fivegs_amffunction_rm_regmobsucc counter
fivegs_amffunction_rm_regmobsucc 0

# HELP fivegs_amffunction_amf_authreq Number of authentication requests sent by the AMF
# TYPE fivegs_amffunction_amf_authreq counter
fivegs_amffunction_amf_authreq 0

# HELP fivegs_amffunction_rm_regemergsucc Number of successful emergency registrations at the AMF
# TYPE fivegs_amffunction_rm_regemergsucc counter
fivegs_amffunction_rm_regemergsucc 0

# HELP fivegs_amffunction_mm_paging5gsucc Number of successful 5G paging procedures initiated at the AMF
# TYPE fivegs_amffunction_mm_paging5gsucc counter
fivegs_amffunction_mm_paging5gsucc 0

# HELP ran_ue RAN UEs
# TYPE ran_ue gauge
ran_ue 0

# HELP fivegs_amffunction_rm_regperiodsucc Number of successful periodic registration update requests at the AMF
# TYPE fivegs_amffunction_rm_regperiodsucc counter
fivegs_amffunction_rm_regperiodsucc 0

# HELP fivegs_amffunction_rm_registeredsubnbr Number of registered state subscribers per AMF
# TYPE fivegs_amffunction_rm_registeredsubnbr gauge

# HELP fivegs_amffunction_rm_reginitfail Number of failed initial registrations at the AMF
# TYPE fivegs_amffunction_rm_reginitfail counter

# HELP fivegs_amffunction_rm_regmobfail Number of failed mobility registration updates at the AMF
# TYPE fivegs_amffunction_rm_regmobfail counter

# HELP fivegs_amffunction_rm_regperiodfail Number of failed periodic registration update requests at the AMF
# TYPE fivegs_amffunction_rm_regperiodfail counter

# HELP fivegs_amffunction_rm_regemergfail Number of failed emergency registrations at the AMF
# TYPE fivegs_amffunction_rm_regemergfail counter

# HELP fivegs_amffunction_amf_authfail Number of authentication failure messages received by the AMF
# TYPE fivegs_amffunction_amf_authfail counter

# HELP process_max_fds Maximum number of open file descriptors.
# TYPE process_max_fds gauge
process_max_fds 1024

# HELP process_virtual_memory_max_bytes Maximum amount of virtual memory available in bytes.
# TYPE process_virtual_memory_max_bytes gauge
process_virtual_memory_max_bytes -1

# HELP process_cpu_seconds_total Total user and system CPU time spent in seconds.
# TYPE process_cpu_seconds_total gauge
process_cpu_seconds_total 0

# HELP process_virtual_memory_bytes Virtual memory size in bytes.
# TYPE process_virtual_memory_bytes gauge
process_virtual_memory_bytes 159555584

# HELP process_resident_memory_bytes Resident memory size in bytes.
# TYPE process_resident_memory_bytes gauge
process_resident_memory_bytes 17182720

# HELP process_start_time_seconds Start time of the process since unix epoch in seconds.
# TYPE process_start_time_seconds gauge
process_start_time_seconds 61727

# HELP process_open_fds Number of open file descriptors.
# TYPE process_open_fds gauge
process_open_fds 23
```
## Run Grafana
```markdown


I used the OSS version of Grafana.

## Web Access to Grafana Dashboard

After starting Open5GS and Prometheus, run Grafana-OSS as follows:

```bash
docker run -d -p 3001:3000 grafana/grafana-oss
```
##RUN GRAFANA

You can access the following URL with web browser. The default username and password are both `admin`.

[http://192.168.0.111:3001/](http://192.168.0.111:3000/)

## Prometheus data source

The data source name, URL, and access mode are as follows. I used the defaults for other than these as is.

| Name     | URL                      |
|----------|--------------------------|
| Open5GS  | http://192.168.0.111:9092/ |

Grafana Data Source
![image](https://github.com/user-attachments/assets/e7e61fc7-e411-4b7c-8441-0b3bcca68588)

## Example of setting visualization of metrics

1. Create your first dashboard
2. Add a panel for each metric you want to visualize
3. In the Metrics browser:
   - Select the job and its metrics from the available `job_names`
   - Set panel properties
   - Add panels
   - Create the dashboard

The following is a simple example of setting the panel for the AMF metric `amf_session`:

#Grafana Metric Visualization
![image](https://github.com/user-attachments/assets/89ec487a-c441-4934-8f08-9c965a044328)


Grafana allows you to freely design user-friendly dashboards by:
- Creating attractive panels for each metric
- Combining them in meaningful layouts
- Customizing visualizations to suit your monitoring needs
```
# Prometheus Monitoring for 5G RAN with Helm and Documentation

This project demonstrates a 5G network simulation using Open5GS, UERANSIM, Prometheus, and Grafana, deployed with Helm. It includes submodules linking to the official repositories.

## Components (Submodules)
- [Open5GS](./open5gs/): 5G Core network implementation.
- [UERANSIM](./ueransim/): UE and gNodeB simulation.
- [Prometheus](./prometheus/): Metrics collection tool.
- [Grafana](./grafana/): Dashboard visualizations.

## Setup
To clone with submodules, run:
```bash
git clone --recurse-submodules https://github.com/Codeblinders/Prometheus-Monitoring-for-5G-RAN-with-Helm-and-Documentation.git

```
##Acknowledgments
Thanks to the Open5GS, UERANSIM, Prometheus, and Grafana communities.
