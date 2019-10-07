---
title: Service Level Objectives
description: Service Level Objectives and Indicators 
weight: 25
keywords: [metrics, quality gates, sli, slo]
---

Lorem ipsum dolor sit.

# Service Identification

# Service Level Indicators
Google SRE Book: https://landing.google.com/sre/sre-book/chapters/service-level-objectives/

## Request Latency (p50,p90,p95)
<description> of response time and percentiles
The time it takes for a service to execute and complete a task.
how long it takes to return a response to a request

## System Throughput (requests per second)
<description> of throughput

## Error Rate (error fraction of all requests received)
<description> of failure rate

# Service Level Objectives


## Comparison Strategies



# Example

```yaml
identification:
- namespace: "project-$STAGE"
- pod_name: "~carts-primary-.*"
strategy: compare_with_latest
- n: 1
objectives:
- response_time_p90:
    - pass: 5%
    - needs_approval: 10%
- response_time_p95:
    - pass: 2%
    - needs_approval: 5%
``` 
