# Containers Dev Notes

## Kubernetes CLI (kubectl)

## Multiple `port-forward` in a single command

```sh
kubectl port-forward svc/my-service-1 8081 & \
  kubectl port-forward svc/my-service-2 8082 & \
  kubectl port-forward svc/my-service-3 9093
```

This will run port forward processes in the background.  

Note: Port forwarding process will still remain running in the background if command did not exit properly (e.g. CTRL + C). To kill the background processes, use the `fg` command to bring the process to foreground and then exit it (e.g CTRL + C).
