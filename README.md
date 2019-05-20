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

Logging in requires the correct login parameters. Those can be set via environment variables or during the deployment. Assuming we want to use `user`:`secretpassword` as user:pw credentials, do the following:

* chain username and password together
* sha256 hash them
* use the username as username, the hash as password

Under linux, this could be done by issuing `echo -n usersecretpassword | sha256sum`

For our example this would then look like `user` with the hash `60355f3abbfb4def49ac8b645b5ec3394040795c33bdfd6a841eccdc32869fe7`. Create the app as `oc new-app --template=hivemq4-cluster -p HIVEMQ_CONTROL_CENTER_USER=user -p HIVEMQ_CONTROL_CENTER_PASSWORD=60355f3abbfb4def49ac8b645b5ec3394040795c33bdfd6a841eccdc32869fe7`.

If you want to change the data later on, you can simply modify the environment variables in the Deployment Configuration.

## Labels
Each item of the template has an `app` label, which defaults to `hivemq4-example`. This can be overwritten by using the `NAME` parameter:
```
oc new-app --template=hivemq4-cluster -p NAME=mqtt-cluster
```

## Removal
To remove all items created by this template, use
`oc delete all -l app=hivemq4-example`

Change the value of the label to whatever you specified for the `NAME` variable during deployment. Remove the configmap afterwards, it won't be covered by the `all` statement. You can ask for a list of configmaps via `oc get cm`.
