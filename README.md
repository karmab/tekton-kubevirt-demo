# About

this is a simple tekton pipeline showcasing how to deploy vms and containers together from a pipeline, which will both serve basic http content

# Environment

sno openshift cluster with the following OLM operators:

- kubevirt-hyperconverged
- openshift-pipelines-operator-rh
- optionally odf-lvm-operator (to provide dynamic storage based on local disks)

# How to use

## Dedicated namespace

We optionally create a dedicated namespace and grant anyuid scc to the default service account (to ease running the httpd deployment part of our pipeline)

```
oc new-project demo
oc adm policy add-scc-to-user anyuid -z default
```

## Deployment

`oc apply -f pipeline.yml`

# Run the pipeline

You can run From the UI or using the following invocation

`oc create -f pipelinerun.yml`

The pipeline will run two tasks:

- one deploying an httpd deployment and creating a service and a route on top of it. The service targets any pod with the `production=ready` label
- one deploying the specified number of vms. Each of them will launch apache (through cloudinit)

# Testing the app

```
curl production-web-demo.apps.sno.accenture.com
```

When launching the curl command several times, we will get a different message depending on whether the httpd container or the vms answer


# Cleaning

```
oc delete all -l production=ready
oc delete pipelinerun --all
```
