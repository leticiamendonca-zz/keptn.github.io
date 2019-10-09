---
title: Service Level Objectives
description: Service Level Objectives and Indicators 
weight: 25
keywords: [metrics, quality gates, sli, slo]
---

A service level objective (SLO) is a target value or range of values for a service level that is measured by an SLI. An SLI (service level indicator) is a carefully defined quantitative measure of some aspect of the level of service that is provided.

# Service Level Indicators
By default, Keptn supports the following SLIs, inspired by the Site Reliability Engineering book from Google (https://landing.google.com/sre/sre-book/chapters/service-level-objectives).

## Request Latency
The time it takes for a service to execute and complete a task or how long it takes to return a response to a request.

- `request_latency_p50` (median)
- `request_latency_p90` (90th percentile)
- `request_latency_p95` (95th percentile)


## System Throughput (requests per second)
The number of request per second that have been processed.

- `throughput`

## Error Rate 
The fraction of all received requests that procuced an error.

- `error_rate`

# Service Level Objectives
An SLO consists of an SLI and evaluation success criteria that depends on the selected comparison strategy.

## Service Filter
SLOs are defined per service. When querying SLIs from an SLI provider you can provide filter criteria to only get SLIs that are needed for an evaluation. For example, instead of retrieving the request latency for all services of an SLI provider, you can narrow down the result set to only the SLIs for the desired service. The filter criteria are key value pairs and depend on the SLI provider.

By default, Keptn supports [Prometheus](https://prometheus.io) and [Dynatrace](https://dynatrace.com) as SLI providers.

### Supported filter criteria for Prometheus:
You can either filter by passing a combination of the namespace and the pod name, in which the service is deployed:

 ```yaml
service_filter:
  namespace: "project-production"
  pod_name: "~carts-primary-.*"
 ```

 Alternatively, if you can pass the name of the scrape job config you have set up in Prometheus:

 ```yaml
service_filter:
  job: "carts-sockshop-production"
 ```

### Supported filter criteria for Dynatrace:

You can either pass a list of tags:

 ```yaml
service_filter:
  tags:
    - name: app
      value: carts
    - name: stage
      value: production
    - name: project
      value: sockshop 
 ```

Or you can address a specific Dynatrace Service Entity

```yaml
service_filter:
  service_id: <DT_SERVICE_ENTITY_ID> 
 ```

## Comparison Strategies
By default, Keptn supports comparing with previous SLIs for evaluation. This strategy allows you to define the number of previous results you want to evaluate against. For example, if you specify

```yaml
strategy: "compare_with_previous"
- number_of_previous_results_to_compare: 1
```

the current results is only compared to the previous result. Whereas if you specify

```yaml
strategy: "compare_with_previous"
- number_of_previous_results_to_compare: 3
```

the current result is compared to the average of the three previous results.

## Objectives

An objective consists of an SLI and `pass` and `needs_approval` values that can be positive or negative. `pass` represents the upper limit up to which an evaluation is successful. `needs_approval` describes the border where an approval is needed to pass the evaluation. Both values must be stated in percent. All values beyond `pass` and `needs_approval` result in failed SLOs.

# Example

Assume we have a service with the following SLOs.

```yaml
service_filter:
- namespace: "project-production"
- pod_name: "~carts-primary-.*"
strategy: "compare_with_previous"
- number_of_previous_results_to_compare: 1
objectives:
- request_latency_p90:
    - pass: "<5%"
    - needs_approval: "<10%"
- request_latency_p95:
    - pass: "2%"
    - needs_approval: "5%"
- throughput:
    - pass: "-8%"
    - needs_approval: "-15%"
``` 

The previous evaluation returned the following SLI values:

```console
request_latency_p90 = 450ms
request_latency_p95 = 670ms
throughput = 150
```

## All pass
Assume the current evaluation returned the following SLI values:

```console
request_latency_p90 = 470ms (increase of 4.4%)
request_latency_p95 = 680ms (increase of 1.5%)
throughput = 140 (decrease of 6.7%)
```

# Current Limitations

In future versions Keptn will support additional SLI providers, filter criteria, SLIs, and comparison strategies.