# CI/CD Final Project — Hit Counter Service

A Python Flask **Hit Counter Service** demonstrating continuous integration and continuous delivery (CI/CD) with GitHub Actions, Tekton, Buildah, and Red Hat OpenShift.

## Project Overview

This project automates code-quality checks, unit testing, container image building, and deployment. The service exposes a JSON API, including the `/counters` endpoint.

## Technology Stack

- **Python / Flask** — application and API
- **Flake8** — Python linting
- **Nose** — unit tests
- **GitHub Actions** — continuous integration workflow
- **Tekton Pipelines** — pipeline orchestration in OpenShift
- **Buildah** — container image build and push
- **Red Hat OpenShift** — container deployment, Service, and Route
- **Gunicorn** — WSGI application server

## CI/CD Workflow

The Tekton build-and-test pipeline runs these stages in order:

1. **Cleanup** — prepares the shared workspace.
2. **Git clone** — checks out the configured repository and branch.
3. **Lint** — runs Flake8.
4. **Tests** — runs the Nose test suite.
5. **Build and push** — builds the container image from the `Dockerfile` and pushes it to the OpenShift internal image registry.
6. **Deploy** — applies the Deployment and Service configuration and waits for the rollout.

The pipeline is configured in `.tekton/pipeline-build-test.yml`. The PipelineRun configuration is in `.tekton/pipeline-build-test-run.yml`. Supporting test pipeline files are also stored under `.tekton/`.

## Test Results

A successful run in the OpenShift lab reported:

- Flake8: **0 violations**
- Nose: **9 tests passed**
- Test coverage: **99%**

Results may vary if the source code or dependencies change.

## Run in the IBM Skills Network OpenShift Lab

### Prerequisites

- Access to the IBM Skills Network OpenShift lab
- `oc` (OpenShift CLI) and `tkn` (Tekton CLI)
- A project namespace and a persistent volume claim named `oc-lab-pvc`
- The repository's configured Tekton Tasks and the Buildah and OpenShift Client ClusterTasks available in the lab

### Inspect the resources

```bash
oc get pipelines
oc get tasks
tkn clustertask list
oc get pvc oc-lab-pvc
```

### Apply and run the build-and-test pipeline

```bash
oc apply -f .tekton/pipeline-build-test.yml
oc create -f .tekton/pipeline-build-test-run.yml
tkn pipelinerun list
```

To view a run's logs, replace `<PIPELINERUN_NAME>` with the actual PipelineRun name:

```bash
tkn pipelinerun logs <PIPELINERUN_NAME> -f
```

### Verify the OpenShift deployment

```bash
oc rollout status deployment/ci-cd-final-project
oc get pods
oc get service ci-cd-final-project
oc get route ci-cd-final-project
oc logs deployment/ci-cd-final-project
```

The service listens on port `8000`. Use the route hostname printed by `oc get route ci-cd-final-project`; route availability depends on the lab environment.

## Repository Structure

```text
.
├── .github/
│   └── workflows/       # GitHub Actions CI workflow
├── .tekton/
│   ├── flake8-task.yml
│   ├── pipeline-build-test.yml
│   ├── pipeline-build-test-run.yml
│   ├── pipeline-test.yml
│   └── pipeline-test-run.yml
├── bin/
│   └── setup.sh         # Lab setup script
├── Dockerfile
├── service.py           # Flask service
├── requirements.txt
└── README.md
```

Other project files and task definitions may be present in the repository.

## Submission Evidence

For course assessment, capture the requested evidence from your own lab session:

- GitHub Actions run details showing lint and unit-test steps
- OpenShift PVC details for `oc-lab-pvc`
- OpenShift Pipeline and successful PipelineRun details
- Application logs from `oc logs deployment/ci-cd-final-project`

Do not commit credentials, access tokens, or other secrets to the repository.

## License

Licensed under the Apache License. See [LICENSE](LICENSE).

---

© IBM Corporation. All rights reserved.
