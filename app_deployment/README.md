# A Tekton pipeline to deploy an app to Openshift

### run the pipeline via the Tekton CLI
tkn pipeline start get-config --showlog

tkn taskrun start get-config --showlog

### delete all the tasks and pipelines
oc delete tasks --all 
oc delete taskruns --all 
oc delete pipelines --all 
oc delete pipelineruns --all 