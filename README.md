# HiveMQ

## About
This is a modification of the templates provided on Docker Hub for K8s:

* [HiveMQ3](https://hub.docker.com/r/hivemq/hivemq3/)
* [HiveMQ4](https://hub.docker.com/r/hivemq/hivemq4/)

I took care of a few clean ups to ensure only the correct ports will be exposed, also
aplied some magic to get the Namespace name for service discovery automatically. If you
want to know how, take a look at `DeploymentConfig.spec.template.spec.containers.[0].env`.

## Usage

Apply the templates to your namespace by running
```
oc create -f <template_file>
```
After that, the template for an HiveMQ 4 cluster can be launched via
```
oc new-app --template=hivemq4-cluster
```

## Accessing the Control Interface
To get access to the WebUI, expose the service which is responsible for the Control Center (determine by name by running `oc get svc`) with `oc expose svc/<servicename>`

**Note** Currently there appears to be a bug for both images where login via custom credentials is not possible. I highly suggest NOT using the Control Interface until it's fixed. For more info take a look at the [Github Bug Report](https://github.com/hivemq/hivemq4-docker-images/issues/5)

## Labels
Each item of the template has an `app` label, which defaults to `hivemq4-example`. This can be overwritten by using the `NAME` parameter:
```
oc new-app --template=hivemq4-cluster -p NAME=mqtt-cluster
```

## Removal
To remove all items created by this template, use
`oc delete all -l app=hivemq4-example`

Change the value of the label to whatever you specified for the `NAME` variable during deployment.
