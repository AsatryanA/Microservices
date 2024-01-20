# Table of Content

 - [What to do](#what-to-do)
 - [Sub-task 1: Adding logging](#sub-task-1-adding-logging)
 - [Sub-task 2: Monitoring](#sub-task-2-monitoring)
 - [Sub-task 3: Tracing](#sub-task-3-tracing)
 - [Example](#example)

## What to do

In this task it is needed to provide exist infrastructure with a Logging system.

## Sub-task 1: Adding logging

1) Update your infrastructure with any data storage ([influxdb](https://hub.docker.com/_/influxdb), [prometheus](https://hub.docker.com/r/prom/prometheus/), [elasticsearch](https://hub.docker.com/_/elasticsearch)). 
2) Configure logging in applications to send logs to the new added log storage. 
3) The logs should be gathered from the services, transferred and persisted into data storage.

## Sub-task 2: Monitoring

1) Provide a specific route to any visualization tool ([Grafana](https://hub.docker.com/r/grafana/grafana), [Kibana](https://hub.docker.com/_/kibana), etc.) in API Gateway service. Add it to docker-compose file and configure. 
2) Make several dashboards (for instance, based on the metrics from JVMs, performance and Latency of the API Gateway etc.)
 
## Sub-task 3: Tracing

1) Add trace ID header to uploaded file request and propagate it via HTTP calls (if rest template used, possible to do it via thread-locals and http interceptors) and via queue (adding and extracting additional attribute to the message). 
2) Extract Trace ID and find appropriate logs in your Visualization tool. Make sure you can find logs from all the involved services only by Trace ID.

## Example

Please, find below an example on how to add monitoring with prometheus and grafana and log aggregation with ELK stack:

 - [Monitoring with prometheus and grafana and log aggregation using ELK stack: Part 1](https://medium.com/nerd-for-tech/creating-spring-boot-microservices-monitoring-with-prometheus-and-grafana-and-log-aggregation-ba4f20496942)
 - [Monitoring with prometheus and grafana and log aggregation using ELK stack: Part 2](https://medium.com/nerd-for-tech/building-spring-boot-microservices-monitoring-with-prometheus-and-grafana-and-log-aggregation-5ed9ca7dda36)

**Note**

All additional components should be launched as containers via docker-compose files.