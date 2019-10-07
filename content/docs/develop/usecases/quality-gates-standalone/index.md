---
title: Quality Gates Standalone
description: You can use Keptn Quality Gates as a standalone feature.
weight: 60
keywords: [quality gates]
aliases:
---

Let's say you deploy and test your applications and services with tools other than Keptn. Nevertheless, you can still use Keptn quality gates as a standalone feature. Here's how it goes.

# Prerequisites

- Running Keptn installation (_quality gates only_ installation is sufficient, see [here](../../installation/setup-keptn))
- *Service level objectives* for each service you want to evaluate
- Supported tool (currently either Prometheus or Dynatrace) that Keptn can query for service level indicators ([SLI](../../reference/slo/)s) for evaulation

# Configure Keptn

1. You need to create a Keptn project and let Keptn know about the used SLI provider by providing the endpoint and credentials, if needed. 

    ```console
    keptn create project PROJECTNAME 
    ```
    OR
    ```console
    keptn create project PROJECTNAME --sli-provider=prometheus --endpoint=https://1.2.3.4
    ```
    OR
    ```console
    keptn create sli-provider prometheus --project=PROJECTNAME --endpoint=https://1.2.3.4
    ```
    OR
    ```console
    keptn create sli-provider dynatrace --project PROJECTNAME --endpoint=http://a.b.c.d --api-token=abcde12345
    ```

1. Then you create a Keptn service for each service that you want to evaluate using Keptn quality gates. For each service you need to provide a `yaml` file that contains

    - Details on how to uniquely identify that service when querying the SLI provider and
    - Service level objectives of that service

    ```console
    keptn create service SERVICENAME --project PROJECTNAME --slo=slo.yaml
    ```

    An example of an `slo.yaml` file is shown below. You find more information on service identification, service level indicators and objectives [here](../../reference/slo/).

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

# Evaluate Keptn quality gates

The Keptn quality gates is a two step procedure that consists of starting the evaluation and polling for the results.

At a specific point in time, either after you've run your tests or you've waited for enough live traffic, you can either start the evaluation of the Keptn quality gates manually using the Keptn CLI, or automate it by either including the Keptn CLI calls in your automation scripts, or by directly accessing the Keptn API. 

1. Use the Keptn CLI to start the evaluation: 

    ```
    keptn start-evaluation --project PROJECTNAME --service SERVICENAME --reference test-run-A --timeframe 5m
    ```

The `start-evaluation` command will evaluate the service level objects of the specified service in the specified project over the last 5 minutes and will save the results under the reference `test-run-A`. This command will return a `KeptnContext` ID that you will need for querying the results of the evaluation.

2. Use the Keptn CLI to pull evaluation results: 
    
    ```console
    keptn get-evaluation-results --keptnContext 1234-5678-90
    ```
    ```console
    keptn get-evaluation-results --project PROJECTNAME --service SERVICENAME
    ```
    ```console
    keptn get-evaluation-results --project PROJECTNAME --service SERVICENAME --reference test-run-A
    ```

All of the above commands will return the results of the same Keptn qualiy gates evaluation. The results come in the form of the `evaluation-done` event, that is specified [here](https://github.com/keptn/keptn/blob/master/specification/cloudevents.md#evaluation-done).
