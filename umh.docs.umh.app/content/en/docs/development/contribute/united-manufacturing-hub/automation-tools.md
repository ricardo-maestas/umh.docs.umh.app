---
title: Automation Tools
description:  | 
    This section contains the description of the automation tools used in the
    United Manufacturing Hub project.
weight: 10
aliases:
    - /docs/development/contribute/united-manufacturing-hub/ci-cd/
---

Automation tools are an essential part of the United Manufacturing Hub project.
They automate the building and testing of the project's code, ensuring that it
remains of high quality and stays reliable.

We rely on GitHub Actions for running the pipelines, which are defined in the
`.github/workflows` directory of the project's repository.

Here's a brief overview of each workflow:

## Build Docker Images

This pipeline builds and pushes all the Docker images for the project, tagging
them using the branch name or the git tag. This way there is always a tagged
version for the latest release of the UMH, as well as specific version for each
branch to use for testing.

It runs on push events only when relevant files have been changed, such as the
Dockerfiles or the source code.

## Codecov

This pipeline executes `go test` and uploads the results to `codecov`, to make
sure that unit tests are passing and they cover most of the code.

It runs on both push and pull request events.

## GitGuardian Scan

This pipeline scans the code for security vulnerabilities, such as exposed secrets.

It runs on both push and pull request events.

## Helm Checks

This pipeline runs checks on Helm configuration. It executes `helm lint` for the
default `values.yaml` and for any custom values used for testing. Additionally,
runs `checkov` to prevent misconfigurations and vulnerabilities, along with best
practices checks.

It runs on push events only when Helm template files have been changed.

## Test Deployment

This pipeline group verifies that the current changes can be successfully
installed and that data flows correctly. There are two pipelines: a "tiny"
version with the minimum amount of services needed to run the stack, and a "full"
version with as many services as possible.

Each pipeline has two jobs. The first job installs the stacks with the current
changes, and the second job tries to upgrade from the latest stable version
to the current changes.

A test is run in each workflow to verify that simulated data flows through MQTT,
NodeRed, Kafka, and TimescaleDB. In the full version, an additional test for
sensorconnect is run, using a mocked sensor to verify the data flow.

It runs on pull request events when the Helm configuration or the source code
changes.
