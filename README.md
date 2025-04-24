# Prometheus-Monitoring-for-5G-RAN-with-Helm-and-Documentation
Use Helm to deploy a 5G RAN simulator and Prometheus sidecar for  metrics scraping. Write a comprehensive technical paper describing  the deployment steps, key metrics tracked, and how the monitored  data can guide network optimization.
Table of Contents
Overview of Open5GS 5GC Simulation Mobile Network
Additional changes in configuration files of Open5GS 5GC C-Plane and U-Plane
Build Open5GS for using Prometheus
Run Prometheus
Web Access to Prometheus Dashboard
Metrics of Open5GS AMF
Metrics of Open5GS PCF
Metrics of Open5GS SMF
Metrics of Open5GS UPF
Run Grafana
Web Access to Grafana Dashboard
Prometheus data source
Example of setting visualization of metrics
Changelog (summary)

Overview of Open5GS 5GC Simulation Mobile Network
This is an additional setting example when monitoring the metrics with Prometheus for the following sample configuration.

Open5GS 5GC & UERANSIM UE / RAN Sample Configuration - Select UPF based on S-NSSAI
This example configuration for monitoring metrics with Prometheus is shown in the figure below. NFs without metrics are not drawn in the figure.

./images/network-overview.png

The 5GC / UE / RAN used are as follows. Also, I started Prometheus and Grafana using Docker.

5GC - Open5GS v2.6.1 (2023.03.10) - https://github.com/open5gs/open5gs
UE / RAN - UERANSIM v3.2.6 - https://github.com/aligungr/UERANSIM
The IP address and port of the monitored NFs are as follows.

NF	IP address	port	Prometheus job_name
AMF	192.168.0.111	9090/tcp	open5gs-amfd
PCF	192.168.0.111	9091/tcp	open5gs-pcfd
SMF1	192.168.0.112	9090/tcp	open5gs-smfd1
SMF2	192.168.0.113	9090/tcp	open5gs-smfd2
UPF1	192.168.0.114	9090/tcp	open5gs-upfd1
UPF2	192.168.0.115	9090/tcp	open5gs-upfd2
The exposed IP address and port of Prometheus and Grafana-OSS are as follows.

Server	IP address	port
Prometheus	192.168.0.111	9092/tcp
Grafana-OSS	192.168.0.111	3001/tcp

Additional changes in configuration files of Open5GS 5GC C-Plane and U-Plane
In this case, the following configuration is further changed for monitoring the metrics with Prometheus.

Open5GS 5GC & UERANSIM UE / RAN Sample Configuration - Select UPF based on S-NSSAI
I will explain what to set additionally here. When monitoring the metrics with Prometheus, change the following configuration files for each NF of C-Plane and U-Plane.

open5gs/install/etc/open5gs/amf.yaml
...
metrics:
  - addr: 192.168.0.111
    port: 9090
open5gs/install/etc/open5gs/pcf.yaml
...
metrics:
  - addr: 192.168.0.111
    port: 9091
open5gs/install/etc/open5gs/smf1.yaml
...
metrics:
  - addr: 192.168.0.112
    port: 9090
open5gs/install/etc/open5gs/smf2.yaml
...
metrics:
  - addr: 192.168.0.113
    port: 9090
open5gs/install/etc/open5gs/upf.yaml for UPF1
...
metrics:
  - addr: 192.168.0.114
    port: 9090
open5gs/install/etc/open5gs/upf.yaml for UPF2
...
metrics:
  - addr: 192.168.0.115
    port: 9090

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
After starting Open5GS, run Prometheus as follows.

docker run -d -p 9092:9090 -v `pwd`/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
You can access the following URL with web browser.

http://192.168.0.111:9092/
./images/prometheus-top.png

The list of targets is as follows.

./images/prometheus-targets.png


Metrics of Open5GS AMF
Following the Endpoint link of job_name=open5gs-amfd, the metrics will be displayed as follows.

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
