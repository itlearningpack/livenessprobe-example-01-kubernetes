# Define a liveness command

Many applications running for long periods of time eventually transition to broken states, and cannot recover except by being restarted. Kubernetes provides liveness probes to detect and remedy such situations.

In this exercise, you create a Pod that runs a container based on the registry.k8s.io/busybox:1.27.2 image. Here is the configuration file for the Pod:

### Pod Configuration for Liveness EXEC-COMMAND

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: v1.0.0
  name: healthcheck1
spec:
  containers:
  - name: busybox
    image: busybox:latest
    args:
    - '/bin/sh'
    - '-c'
    - 'touch /tmp/healthy; sleep 10; rm -f /tmp/healthy; sleep 10'
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 10
      periodSeconds: 10
```
In the configuration file, you can see that the Pod has a single Container. The periodSeconds field specifies that the kubelet should perform a liveness probe every 5 seconds. The initialDelaySeconds field tells the kubelet that it should wait 5 seconds before performing the first probe. To perform a probe, the kubelet executes the command cat /tmp/healthy in the target container. If the command succeeds, it returns 0, and the kubelet considers the container to be alive and healthy. If the command returns a non-zero value, the kubelet kills the container and restarts it.

When the container starts, it executes this command:

```
/bin/sh -c "touch /tmp/healthy; sleep 10; rm -f /tmp/healthy; sleep 10"
```
For the first 10 seconds of the container's life, there is a /tmp/healthy file. So during the first 10 seconds, the command cat /tmp/healthy returns a success code. After 10 seconds, cat /tmp/healthy returns a failure code.

Create the Pod:
```
kubectl apply -f healthcheck1.yaml
```

