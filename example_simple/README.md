# Create Simple GitHub File Count Pipeline

In this folder we create a simple Tekton pipeline to count the number of files in a GitHub repo. Specifically, we create a pipeline that will take any git repository as a `PipelineResource` and then count the number of files, using `Tasks`, `Pipelines`, and `Pipeline Resources`. 

# Installation

See README.md in parent repository for code dependencies. 

Run the following commands on your OpenShift cluster to run this repository. 

```
oc apply -f example_simple/task.yaml 
oc apply -f example_simple/pipeline.yaml 
oc apply -f example_simple/pipeline_resource.yaml 
tkn pipeline start count --showlog
```

### Create Task

<b>Definition</b>: A `Task` is an individual job and defines a set of build steps such as compiling code, running tests, and building and deploying images.

Firstly, we start by creating a Task that counts the number of files in a GitHub repo: 

```
apiVersion: tekton.dev/v1beta1
kind: Task
metadata:
  name: count-files
spec:
  resources:
    inputs:
      - name: repo
        type: git
        targetPath: code
  steps:
    - name: count
      image: registry.access.redhat.com/ubi8/ubi
      command:
        - /bin/bash
      args: ['-c', 'echo $(find ./ -type f | wc -l) files in repo']
```

You can apply the above Task to the OpenShift cluster by running the following command: 
```
oc apply -f example_simple/task.yaml 
```
### Create Pipeline

<b>Definition</b>: `Pipeline` describes a list of tasks that compose a pipeline.

Next, you can create a pipeline that will also have an input resource. This Pipeline will have a single task, which will be the `count-files` task you've just defined.

```
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: count
spec:
  resources:
    - name: git-repo
      type: git
  tasks:
    - name: count-task
      taskRef:
        name: count-files
      resources:
        inputs:
          - name: repo
            resource: git-repo
```

You can apply the above Pipeline to the OpenShift cluster by running the following command: 
```
oc apply -f example_simple/pipeline.yaml 
```

### Create Pipeline Resource

<b>Definition</b>: The `PipelineResource` defines an object that is an input (such as a Git repository) or an output (such as a Docker image) of the pipeline.

Finally, you can create a PipelineResource. This resource is of type `git`, and you can put in the link of a Github repository in the `url` parameter. You can use the repo for this project.

```
apiVersion: tekton.dev/v1alpha1
kind: PipelineResource
metadata:
  name: git-repo
spec:
  type: git  
  params:
    - name: url
      value: https://github.com/miyapniel/tekton-pipelines
```

You can apply the above Pipeline to the OpenShift cluster by running the following command: 
```
oc apply -f example_simple/pipeline_resource.yaml
```

When you begin the Pipeline with the CLI, you will be prompted on the git resource to use. You can either use the resource you've just created or create your own. You could also use the `--resource` parameter with the CLI to specify which resources to use.