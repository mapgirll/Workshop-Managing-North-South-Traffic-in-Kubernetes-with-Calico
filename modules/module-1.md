# Module 1 - Getting Started with Calico Whisker

Calico Whisker is a recent addition to Calico Open Source, enabling you to view flow logs to verify network traffic and create network policies.

## Prerequisites
A Calico Open Source cluster, version 3.30+.

## Monitor network traffic in Calico Whisker

The Whisker web console deploys automatically, but it is not accessible from outside the cluster by default.
To view the web console, you need to allow access.

In this step, you will:

* **Set up port forwarding:** This allows you to access the Whisker web console from your browser.
* **Open the Whisker web console:** View the network traffic logs in real time.

1. Check that you have Calico Open Source 3.30 or greater installed, and that Calico Whisker is running in your cluster:

   ```bash
   kubectl get clusterinfo -o yaml
   ```

You are looking for the `calicoVersion` to be 3.30 or greater:

```bash,nocopy
  spec:
    calicoVersion: v3.30.2
```

2. Check that Calico Whisker is running:

```bash
kubectl get pod -A | grep whisker
```

```bash,nocopy
calico-system        whisker-7557ccb558-hvngj                       2/2     Running
```

```bash
kubectl get svc -A | grep whisker
```
```bash,nocopy
calico-system      whisker                           ClusterIP   10.96.153.8     <none>        8081/TCP
```

Calico Whisker should be running in the cluster on port 8081.

3. From your terminal, run the following command:

```bash
   kubectl port-forward -n calico-system service/whisker 8081:8081
```

```bash title="Expected output"
Forwarding from 127.0.0.1:8081 -> 8081
Forwarding from [::1]:8081 -> 8081
```

Keep this terminal open throughout the rest of this workshop. Open a new terminal window or tab to continue.
The Whisker web console needs the port forwarding to be active to receive logs.

4. To open Calico Whisker, open your browser and go to `localhost:8081`.
You won't see any flows at the beginning.
But in a few moments, as the console receives flow logs, you'll begin to see a list of connections.

<Screenshot src="/images/calico-whisker.png" alt="Whisker" />

The web console accumulates flow logs in real time.
Keep this window open through the rest of the tutorial to see logs of the connections your pods are making.

![Calico Whisker](<../images/Calico Whisker.gif>)

[:arrow_right: Module 2](module-2.md)  

[:leftwards_arrow_with_hook: Back to Main](../README.md)  