# Certified Argo Project Associate (CAPA) Certification Study Guide

> 📌 Last updated: May 29, 2025 – CAPA exam passed on May 29

<p align="center">
  <img src="./../../img/argo_associate.png" alt="CAPA" width="250"/>
</p>

## Who This Is For

## Details About the Exam


- **Type:** Online, proctored, multiple-choice test
- **Duration:** 90 minutes
- **Validity:** 2 years
- **Retake Policy:** One free retake included
- **Eligibility Period:** 12 months from purchase to schedule and take the exam
- **Environment:** Browser-based exam platform (secure, ID required)

The exam is broken down into the following domains:

| Domain               | Weight |
|----------------------|--------|
| Argo Workflows       | 36%    |
| ArgoCD               | 34%    |
| Argo Rollouts        | 18%    |
| Argo Events          | 12%    |

> Curriculum PDF: [CAPA Curriculum (CNCF)](https://github.com/cncf/curriculum/blob/master/CAPA_Curriculum.pdf)

To pass, you will need a score above 75%.
## Study Guide

### Argo Workflows (30%)

Argo Workflows is a spectialized Kubernetes/container-native workflow engine (as Kubernetes CRD) for orchestrating parallel jobs on Kubernetes.

Example use-cases

* Machine Learning pipelines
* Data and batch processing
* Infrastructure automation
* CI/CD

Reasons why to use Argo Workflows:

* Cheaper than an enterprise service.
* Familiarity with containerized applications.
* Kubernetes fits with the rest of the company’s infrastructure, making collaboration easier.


**Understand Argo Workflow Fundementals**

**Argo Workflow Controller** is responsible for reconciling the desired state of the Workflow.

**Argo Server** is responsible for serving the API requests

The following diagram shows the components of the Argo Workflows architecture.  

<p align="center">
  <img src="https://argo-workflows.readthedocs.io/en/latest/assets/diagram.png" alt="Argo Workflow architecture" width="500"/>
</p>

📚 *Source:* [Argo Workflows architecture ](https://argo-workflows.readthedocs.io/en/latest/architecture/)

**Workflow** is the most important resource (CRD) in Argo Workflows and serves two important functions:

1. It defines the workflow to be executed.
2. It stores the state of the workflow.

Each Workflow spec has

* Kubernetes header including meta‐data
* Spec body
  * Entrypoint invocation with optional arguments
  * List of template definitions

* For each template definition:
  * Name of the template
  * Optionally a list of inputs
  * Optionally a list of outputs
  * Container invocation (leaf template) or a list of steps
    * For each step, a template invocation

En example workflow object;

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Workflow
metadata:
  generateName: steps-
spec:
  entrypoint: hello           # We reference our first "template" here

  templates:
  - name: hello               # The first "template" in this Workflow, it is referenced by "entrypoint"
    retryStrategy:
      limit: "10"
    steps:                    # The type of this "template" is "steps"
    - - name: hello
        template: print-message # We reference our second "template" here
        arguments:
          parameters: [{name: message, value: "hello1"}]

  - name: print-message       # The second "template" in this Workflow, it is referenced by "hello"
    inputs:
      parameters:
      - name: message
    container:                # The type of this "template" is "container"
      image: busybox
      command: [echo]
      args: ["{{inputs.parameters.message}}"]
```

The Workflow spec contains the following key attributes:

* **entrypoint** is the name of the template that will be executed first
* **templates** is a list of templates that define the tasks in the workflow

**template** in Argo Workflows can be loosely thought of as "functions":
 it is not exactly a set of tasks, but rather define instructions to be executed. 

> These templates define work to be done, usually in a Container.

 The entrypoint field defines what the "main" function will be – that is, the template that will be executed first.

> ⚠️ **Warning:** `template` and `WorkflowTemplate` are NOT the same thing.
>
> A `template` defines a task or set of tasks within a single workflow.
>
> A `WorkflowTemplate` is a higher-level, reusable workflow definition to share common workflow steps across multiple workflows


There are 9 types of templates, divided into two different categories.

* Template Definitions
  * **Container** is perhaps the most common template type, it will simply schedule a container.
  * **Script** is a wrapper around `container` where the spec is the same as a container but adds a filed `source` which allows you do run ad-hoc scripts.
  * **Resource** allows you to get,create,apply,delete,replace or patch resources (e.g, `ConfigMap`) on your clusters.
  * **Suspend** allows you to suspend executions for a duration. Suspend templates can be **resumed** from the CLI, API or the UI.
  * **Plugin**
  * **Container Set**
  * **HTTP**
* Template Invocators
  * Steps
  * DAGs

**Generating and Consuming Artifacts**

There is built-in support for git, HTTP, GCS, and S3 artifacts.

**Understand Argo Workflow Templates**

**Understand the Argo Workflow Spec**

**Work with DAG (Directed-Acyclic Graphs)**

**Run Data Processing Jobs with Argo Workflows**

### Argo CD (34%)

**Understand Argo CD Fundementals**



## Official & Community Resources


https://argo-workflows.readthedocs.io/en/latest/workflow-concepts/#template-definitions

https://github.com/Al-HusseinHameedJasim/certified-argo-project-associate

https://paulyu.dev/article/capa-study-guide/