# Taking Java Memory Dump from Containerized App running in AKS/K8S

Special tricks to take Java memory dump from [Alpine](https://hub.docker.com/_/alpine) (e.g. [OpenJDK-Alpine](https://hub.docker.com/_/openjdk)) based container which running in [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes). By adding [Tini](https://github.com/krallin/tini) as init of container, otherwise Java process in Alpine would be using PID 1 and would make "Unable to get pid of LinuxThreads manager thread" error when taking Java memory dump (same to [jcmd](https://docs.oracle.com/javase/8/docs/technotes/guides/troubleshoot/tooldescr006.html), [jstack](https://docs.oracle.com/en/java/javase/13/docs/specs/man/jstack.html)).

* Dockerfile <-- Sample Dockerfile with Tini, it's using openjdk:8-jdk-alpine.
* [Containerized App sample in Java Springboot](https://github.com/easonlai/serving-web-content-demo)

Get list of POD running in AKS.
```shell
kubectl get pod
```

Run interactive terminal from container POD.
```shell
kubectl exec -it your-java-app-pod-name /bin/sh
```

Check Process ID (PID) running Java.
PID not equal to 1 should be our target to take memory dump.
```shell
top
```

Start to take memory dump.
```shell
cd /tmp
jmap -dump:format=b,file=snapshot.jmap 1
exit
```

Start to take memory dump.
```shell
kubectl cp your-java-app-pod-name:tmp/snapshot.jmap snapshot.jmap -n your-pod-namespace
```