# Installation

## Install Openshift CLI: 

Install the [CLI for OpenShift](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html) in your environment. 

## Install Tekton via CLI:

Make sure you have [Tekton](https://tekton.dev/docs/cli/) installed in your environment. 
 
## Install Tekton on your OpenShift cluster: 

To install Tekton Pipelines on OpenShift, you must first apply the anyuid security context constraint to the tekton-pipelines-controller service account. This is required to run the webhook Pod.

1. Log on as a user with cluster-admin privileges. The following example uses the kubeadmin password provided when the cluster is created: 
```
oc login --server=https://api.$cluster_name.scp.labs.automationlogic.com:6443 -u kubeadmin
```

2. Use a dedicated namespace (project) to install the Tekton Operators in:
```
oc new-project tekton-pipelines
oc adm policy add-scc-to-user anyuid -z tekton-pipelines-controller
oc adm policy add-scc-to-user anyuid -z tekton-pipelines-webhook
```

3. Install Tekton Pipelines:
```
oc apply --filename https://storage.googleapis.com/tekton-releases/pipeline/latest/release.notags.yaml
```


4. Monitor the installation using the following command until all components show a Running status:
```
oc get pods --namespace tekton-pipelines --watch

```
Congratulations! You have successfully installed Tekton Pipelines on your OpenShift environment. 


