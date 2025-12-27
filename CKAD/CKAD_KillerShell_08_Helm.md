# Interactive Scenarios for Kubernetes Application Developers (8 of 12)

Reference: <https://killercoda.com/killer-shell-ckad>

## [Helm](https://killercoda.com/killer-shell-ckad/scenario/helm)

### Use the Helm package manager

Write the list of all Helm releases in the cluster into **`/root/releases`** .

#### Info

```text
Helm Chart: Kubernetes YAML template-files combined into a single package, Values allow customisation

Helm Release: Installed instance of a Chart

Helm Values: Allow to customise the YAML template-files in a Chart when creating a Release
```

#### Tip 1a

**`helm ls`**

#### Tip 1b

**`helm ls -A`**

#### Solution 1

Helm charts can be installed in any Namespaces, so here we have to look in all.

```bash
helm ls -A > /root/releases
```

#### Output 1

```bash
controlplane:~$ helm ls 
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
controlplane:~$ helm ls -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
apiserver       team-yellow     1               2025-12-27 02:27:11.873302303 +0000 UTC deployed        apache-11.4.29  2.4.65     
webserver       team-blue       1               2025-12-27 02:27:07.310170204 +0000 UTC deployed        apache-11.4.29  2.4.65     
controlplane:~$ helm ls -A > /root/releases
controlplane:~$ 
```

### Delete a Helm release

Delete the Helm release apiserver .

#### Tip 2

helm uninstall -h

#### Solution 2

```bash
helm ls -A
helm -n team-yellow uninstall apiserver
```

#### Output 2

```bash
controlplane:~$ helm ls -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
apiserver       team-yellow     1               2025-12-27 02:27:11.873302303 +0000 UTC deployed        apache-11.4.29  2.4.65     
webserver       team-blue       1               2025-12-27 02:27:07.310170204 +0000 UTC deployed        apache-11.4.29  2.4.65     
controlplane:~$ helm -n team-yellow uninstall apiserver
release "apiserver" uninstalled
controlplane:~$ helm ls -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
webserver       team-blue       1               2025-12-27 02:27:07.310170204 +0000 UTC deployed        apache-11.4.29  2.4.65     
controlplane:~$ 
```

### Install a Helm chart

Install the Helm chart falcosecurity/falco into Namespace team-yellow .

The release should've the name dev .

#### Tip 3

**`helm install -h`**

#### Solution 3

```bash
helm -n team-yellow install dev falcosecurity/falco
```

#### Output 3

```bash
controlplane:~$ helm -n team-yellow install dev falcosecurity/falco
NAME: dev
LAST DEPLOYED: Sat Dec 27 02:49:08 2025
NAMESPACE: team-yellow
STATUS: deployed
REVISION: 1
DESCRIPTION: Install complete
TEST SUITE: None
NOTES:
Falco agents are spinning up on each node in your cluster. After a few
seconds, they are going to start monitoring your containers looking for
security issues.

TIP:
You can easily forward Falco events to Slack, Kafka, AWS Lambda and more with falcosidekick.
Full list of outputs: https://github.com/falcosecurity/charts/tree/master/charts/falcosidekick.
You can enable its deployment with `--set falcosidekick.enabled=true` or in your values.yaml.
See: https://github.com/falcosecurity/charts/blob/master/charts/falcosidekick/values.yaml for configuration values.
controlplane:~$ 
controlplane:~$ helm ls -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
dev             team-yellow     1               2025-12-27 02:49:08.313893653 +0000 UTC deployed        falco-7.0.2     0.42.1     
webserver       team-blue       1               2025-12-27 02:27:07.310170204 +0000 UTC deployed        apache-11.4.29  2.4.65     
controlplane:~$ 
```
