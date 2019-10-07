---
title: Service Level Objectives
description: Service Level Objectives and Indicators 
weight: 25
keywords: [metrics, quality gates, sli, slo]
---

A service level objective (SLO) is a target value or range of values for a service level that is measured by an SLI. An SLI (service level indicator) is a carefully defined quantitative measure of some aspect of the level of service that is provided.

# Service Filter
By default, Keptn supports [Prometheus](https://prometheus.io) and [Dynatrace](https://dynatrace.com) as SLI providers. In future versions of Keptn additional providers can be added.

SLOs are defined per service. When querying SLIs from an SLI provider you can provide filter criteria to only get SLIs that are needed for an evaluation. For example, instead of retrieving the request latency for all services of an SLI provider, you can narrow down the result set to only the SLIs for the desired service. The filter criteria are key value pairs and depend on the SLI provider.

Supported filter criteria for Prometheus:

- namespace: 
- pod_name: 
- job: 

Supported filter criteria for Dynatrace:

- tags:
- service id:

In future versions of Keptn additional filter criteria can be added.

# Service Level Indicators
By default, Keptn supports the following SLIs, inspired by the Site Reliability Engineering book from Google (https://landing.google.com/sre/sre-book/chapters/service-level-objectives).

In future versions of Keptn additional SLIs can be added.

## Request Latency (p50,p90,p95)
The time it takes for a service to execute and complete a task or how long it takes to return a response to a request.

- `request_latency_p50`
- `request_latency_p90`
- `request_latency_p95`


## System Throughput (requests per second)
The number of request per second that have been processed.

- `throughput`

## Error Rate 
The fraction of all received requests that procuced an error.

- `error_rate`

# Service Level Objectives
An SLO consists of an SLI and evaluation success criteria that depends on the selected comparison strategy.

## Comparison Strategies
By default, Keptn supports comparing with previous SLIs for evaluation. This strategy allows you to define the number of previous results you want to evaluate against.

In future versions of Keptn additional comparison strategies can be added.


# Example

```yaml
service_filter:
- namespace: "project-$STAGE"
- pod_name: "~carts-primary-.*"
strategy: "compare_with_previous"
- number_of_previous_results_to_compare: 1
evaluation_time_frame:
- start: "2019-10-07T10:17:00"
- stop: "2019-10-07T10:22:00"
objectives:
- response_time_p90:
    - pass: "5%"
    - needs_approval: "10%"
- response_time_p95:
    - pass: "2%"
    - needs_approval: "5%"
- throughput:
    - pass: "-8%"
    - needs_approval: "-15%"
``` 
