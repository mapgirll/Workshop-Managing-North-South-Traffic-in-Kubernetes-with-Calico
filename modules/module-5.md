# Module 5 - Egress Gateway

### What are egress gateways?

An egress gateway is a means of assigning fixed IP addresses to Kubernetes traffic by having it leave a cluster through a defined gateway.
It also offers a method of managing egress routing, by controlling which gateways egress traffic is routed through or by integrating with external firewalls.

Traditional, non-Kubernetes deployments are more static and long-lived, making it easier to manage and secure egress traffic from VMs, for example, through firewalls.
Kubernetes’ dynamic, scalable, and ephemeral nature makes it difficult to assign static egress identities to workloads, namespaces and deployments.
It's possible to do this without an egress gateway, by configuring the `natOutgoing` behaviour with Calico CNI.
This will either preserve a pod’s IP as traffic leaves a cluster, or be translated to the node’s IP.
Both of these options require allowing a larger CIDR range in any security rules, and the egress IP address is not necessarily ascribed to a particular namespace, deployment or pod.

### When to use egress gateways

#### Integrate Kubernetes with external firewalls

Giving egress traffic a static IP allows an external firewall to manage traffic coming from your Kubernetes cluster.
You may do this to use centralized, existing firewall rules and security measures as an extra line of defense.

![Egress gateway universal firewall](https://docs.tigera.io/assets/images/Universal-Firewall-Integration-diagram-76fe37102fbc1f4570c184f10666ee58.png)

#### Provide a smaller range of IP addresses for other applications or services to allow requests from

Security teams or other applications may restrict access to a small range of static IP addresses for external service or applications.
Limiting the range of IP addresses that are allowed reduces the attack surface.
Without an egress gateway, this could be a dynamic, sizable, ever-changing IP block.
You can route workloads through a specific egress gateway depending on the traffic destination, ensuring that the source is consistent with the allowed CIDR.

![Egress gateway destination routing](https://docs.tigera.io/assets/images/Destination-Based-Routing-diagram-3b7d1faf05040a89c91c54b4e7fe2b55.png)

#### Avoid IP address exhaustion

It’s not unusual for people to try to  solve the issue without egress gateway by giving all pods a publicly routable IP address. 
This can lead to IP exhaustion, becauseIPv4 addresses are a limited, expensive resource.
Assigning a smaller quantity of publicly routable IP addresses to egress gateways allows you to use private IP addresses behind the gateway.

#### Audit, log, and track the source of egress traffic

If you need to trace egress traffic back to a source namespace, application, or tenant for any reason (compliance, costing, auditing, etc) this becomes difficult or impossible without an egress gateway.
Using network security software such as Calico also logs egress traffic which is valuable for compliance, auditing, and troubleshooting.
Setting up routing rules so that specific resources use a specific gateway makes it very easy to audit traffic flows and determine the source of egress flows.

#### Restrict egress access

Egress gateway can work in conjunction with egress access controls to manage or restrict outgoing communications for a deeper line of defense when used with an external firewall.

Using egress gateway policy allows you to control routing based on the destination of egress traffic.

## Egress Gateways with Calico Enterprise and Calico Cloud

Egress gateways are supported in both Calico Enterprise and Calico Cloud.
When an egress gateway is deployed, it creates a transit pod for configured outbound application traffic so that the original source IP is changed to that of the egress gateway pod.

You can create IP pools for the egress gateway pods, giving you control over the outgoing source IPs.
You specify which pods or namespaces use which egress gateways using either annotations, egress gateway policy, or a combination of the two.
Using egress gateway policy allows you to control routing based on the destination of egress traffic.

![Egress gateway](https://docs.tigera.io/assets/images/egress-gateway-9dbf88dca38f5f3b971314137b207079.png)

A pod-based egress gateway architecture offers a cost-effective and scalable approach, as egress gateway pods likely fit on existing nodes.
Without Calico’s pod-based egress gateway you would need to provision additional nodes to perform SNAT on egress traffic, which would increase infrastructure costs as the number of gateways increases.
Ensuring that an egress gateway pod is present on each node that needs one saves an additional hop, improving request time, which may be crucial for low-latency services.
It also makes it easy to protect egress gateway pods using network policies, the same way you protect and secure your other workloads.

## Egress IPs with Calico Open Source

Calico Open Source does not support egress gateways.
If you are a Calico Open Source user, the closest you can get to similar functionality of an egress gateway is to allow either the pod or node CIDR in external firewall rules, or external application, etc.

![natOutgoing true](https://docs.tigera.io/assets/images/natoutgoing-true-4dea97d9b7c5538513b2d2893b2c4ced.png)

This would be the default Kubernetes behavior, where a pod’s source IP is translated  to the node IP, unless natOutgoing: false, where it would retain its pod IP.

![natOutgoing false](https://docs.tigera.io/assets/images/natoutgoing-false-b328316e7464801959e6bc37316cbd10.png)

[You can watch an overview video of Egress Gateway here](https://app.arcade.software/share/uaSF22l4ImQejXnqX2EA)

[:arrow_left: Module 4](module-4.md) 

[:leftwards_arrow_with_hook: Back to Main](../readme.md)