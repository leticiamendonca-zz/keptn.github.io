---
title: Quality Gates Standalone
description: You can use Keptn quality gates as a standalone feature.
weight: 60
keywords: [quality gates]
aliases:
---

Let's say you deploy and test your applications and services with tools other than Keptn. You can still use Keptn quality gates as a standalone feature. Here's how it goes.

# Missing
- Explanation of Evaluation service
- List of supported monitoring tools
- Explanation, how the identification parameters are used in the queries of the plugins of the Evaluation service

## Prerequisites

- Running Keptn installation (_quality gates_ installation is sufficient, see [here](../../installation/setup-keptn))
- [Supported tool]() that can provide the [predefined Keptn metrics](../../reference/metrics/) for evaulation
- Service level objectives for either individual services or an entire application (depends on your app and on the nature of tests)

## Configure Keptn

You need to create a Keptn project and let Keptn know about the used metrics provider tool (endpoint and, if needed, credentials). Then you create a Keptn service for each service that you want to evaluate using Keptn Quality Gates. For each service you need to provide a yaml file that describes the service level objectives of that service and details on how to identify that service when querying the metrics provider.

```
keptn create project <name>
keptn create service <servicename> --project <name> --slo=<servicename>-slo.yaml
```

Example of an slo.yaml file. Find more information about identification
```
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

## Evaluate Keptn Quality Gates

At a specific point in time, either after you've run your tests or you've waited for enough live traffic, you can either start the evaluation of the Keptn Quality Gates manually using the Keptn CLI, or automate it by either including the Keptn CLI calls in your automation scripts, or by directly accessing the Keptn API. The Keptn Quality Gates is a two step procedure that consists of starting the evaluation and polling for the results.

```
keptn start-evaluation --project <name> --service <servicename> --reference testrun7 --timeframe 5m
```

The `start-evaluation` command will evaluate the service level objects of the specified service in the specified project over the last 5 minutes and will save the results under the reference `testrun7`. This command will return a `KeptnContext` ID that you will need for querying the results of the evaluation.

```
keptn get-evaluation-results --keptnContext 1234-5678-90
keptn get-evaluation-results --project <name> --service <servicename>
keptn get-evaluation-results --project <name> --service <servicename> --reference testrun7
```

All of the above commands will return the results of the same Keptn Qualiy Gates evaluation. The results come in the form of the `evaluation-done` event, that is specified [here](https://github.com/keptn/keptn/blob/master/specification/cloudevents.md#evaluation-done).
