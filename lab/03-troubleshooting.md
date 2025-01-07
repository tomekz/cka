# Trouble Shooting

## Troubleshooting cluster components

In case the apiserver is not running, check the logs of the apiserver pod:

First find the failing apiserver pod. It will be probably in the `exited` state so you need to check all the pods:
```
crictl ps --all
```

```shell
crictl logs -f <apiserver-pod-name>
```
