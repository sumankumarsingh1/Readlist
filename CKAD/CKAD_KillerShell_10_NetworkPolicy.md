# Interactive Scenarios for Kubernetes Application Developers (3 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [NetworkPolicy Namespace Selector](https://killercoda.com/killer-shell-ckad/scenario/networkpolicy-namespace-communication)

Allow communication between two Namespaces

### Create new NPs

There are existing Pods in Namespace **`space1`** and **`space2`** .

We need a new NetworkPolicy named **`np`** that restricts all Pods in Namespace **`space1`** to only have outgoing traffic to Pods in Namespace **`space2`** . Incoming traffic not affected.

The NetworkPolicy should still allow all outgoing DNS traffic on port **`53`** TCP and UDP.


#### Template

You can use this template:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
  # TODO add the namespaceSelector section here
```

#### Tip

For learning you can check the [NetworkPolicy Editor](https://editor.cilium.io/)

The namespaceSelector from NPs works with Namespace labels, so first we check existing labels for Namespaces

**`k get ns --show-labels`**

#### Solution

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: np
  namespace: space1
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - ports:
    - port: 53
      protocol: TCP
    - port: 53
      protocol: UDP
  - to:
     - namespaceSelector:
        matchLabels:
         kubernetes.io/metadata.name: space2
```

#### Verify

```bash
# these should work
k -n space1 exec app1-0 -- curl -m 1 microservice1.space2.svc.cluster.local
k -n space1 exec app1-0 -- curl -m 1 microservice2.space2.svc.cluster.local
k -n space1 exec app1-0 -- nslookup tester.default.svc.cluster.local
k -n space1 exec app1-0 -- nslookup killercoda.com

# these should not work
k -n space1 exec app1-0 -- curl -m 1 tester.default.svc.cluster.local
k -n space1 exec app1-0 -- curl -m 1 killercoda.com
```

#### Output



