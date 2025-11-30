# Contents

* [Overview](#overview)
* [Download & Installation](#download--installation)
* [Dashboards](#dashboards)
    * [Ping Dashboard](#ping-dashboard)
    * [System Monitoring Dashboard](#system-monitoring-dashboard)
* [Test Alerts](#test-alerts)
* [Utility Scripts](#utility-scripts)
* [Security Considerations](#security-considerations)
   * [Production Security](#production-security)
* [Troubleshooting](#troubleshooting)
   * [Mac Users](#mac-users)

    
# Overview
The main services that compose this stack are:

| Service                                                                  | Port  | Description                                       | Notes                                                                                         |
|--------------------------------------------------------------------------|:-----:|---------------------------------------------------|-----------------------------------------------------------------------------------------------| 
| [Prometheus](http://prometheus.io/)                                      | :9090 | Data Aggregator                                   | Prometheus is an open source monitoring and alerting toolkit for microservices and containers that provides flexible queries and real-time notifications. | 
| [AlertManager](https://prometheus.io/docs/alerting/latest/alertmanager/) | :9093 | Handles alerts sent by the Prometheus server.     | |
| [Grafana](https://grafana.com/)                                          | :3000 | UI To Visualize Prometheus Data                   | Grafana is a multi-platform open source analytics and interactive visualization web application. |
| [NodeExporter](https://github.com/prometheus/node_exporter)              | :9100 | Data Collector for Computer Stats | The node_exporter is designed to monitor the host system. |
| [cAdvisor](https://github.com/google/cadvisor)                           | :8080 | Collects resource usage of the Docker containers. |                                                                                               |

# Download & Installation
By running the following commands the files are downloaded locally and the services are initialized and started.
```
git clone https://github.com/ArildoGjergji/Prometheus-Monitoring.git
cd Prometheus-Monitoring
sudo docker-compose up -d
```

# Dashboards
Included are two dashboards. Grafana dashboards can be found on [Grafana Dashboards Page](https://grafana.com/grafana/dashboards/).<br\>

## Alerting

There are 3 basic alerts that have been added to this stack.

| Alert | Time To Fire | Description |
| --- | :---: | --- |
| Site Down | 30 seconds | Fires if a website check is down |
| Service Down | 30 seconds | Fires if a service in this setup is down |
| High Load | 30 seconds | Fires if the CPU load is greater than 50% |

To get alerts sent to you, follow the directions in the [Alert Configuration Section](#alert-configuration).

### Test Alerts
A quick test for your alerts is to simulate high CPU load. Run the utility script `./util/high-load.sh` and about 30 seconds or so later you should notice the incident created in [PagerTree](https://pagertree.com) (assuming you followed the [Alert Configuration Section](#alert-configuration) and you'll also get notifications.

Then `Ctrl+C` to stop this command. The incident should auto resolve in PagerTree.

# Security Considerations
This project is intended to be a quick-start to get up and running with Docker and Prometheus. Security has not been implemented in this project. It is the users responsibility to implement Firewall/IpTables and SSL.

Since this is a template to get started Prometheus and Alerting services are exposing their ports to allow for easy troubleshooting and understanding of how the stack works.

## Production Security:
Here are just a couple security considerations for this stack to help you get started.
* Remove the published ports from Prometheus and Alerting services and only allow Grafana to be accessed
* Enable SSL for Grafana with a Proxy such as [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/) or [Traefik](https://traefik.io/) with Let's Encrypt
* Add user authentication via a Reverse Proxy [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/) or [Traefik](https://traefik.io/) for services cAdvisor, Prometheus, & Alerting as they don't support user authenticaiton
* Terminate all services/containers via HTTPS/SSL/TLS

# Troubleshooting
It appears some people have reported no data appearing in Grafana. If this is happening to you be sure to check the time range being queried within Grafana to ensure it is using Today's date with current time.

## Mac Users
Node-Exporter is not designed to run on Mac and in fact cannot collect metrics from the Mac OS. I recommend you comment out the node-exporter section in the [docker-compose.yml](docker-compose.yml) file and instead just use the cAdvisor.
