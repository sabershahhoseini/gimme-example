## Taints and Tolerations

From what the [official documentation](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration) of Kubernetes says:

*Node affinity is a property of Pods that attracts them to a set of nodes (either as a preference or a hard requirement). Taints are the opposite -- they allow a node to repel a set of pods.*

Tolerations are applied to pods. Tolerations allow the scheduler to schedule pods with matching taints. Tolerations allow scheduling but don't guarantee scheduling: the scheduler also evaluates other parameters as part of its function.

Taints and tolerations work together to ensure that pods are not scheduled onto inappropriate nodes. One or more taints are applied to a node; this marks that the node should not accept any pods that do not tolerate the taints.*


- To list taints of nodes: `kubectl get nodes -o json | jq '.items[].spec.taints'`
- To taint a node: `kubectl taint nodes node1 key1=value1:NoSchedule`
- To remove a taint: `kubectl taint nodes node1 key1=value1:NoSchedule-`

You specify a toleration for a pod in the PodSpec. Both of the following tolerations "match" the taint created by the kubectl taint line above, and thus a pod with either toleration would be able to schedule onto node1:

```
tolerations:
- key: "key1"
  operator: "Exists"
  effect: "NoSchedule"
```
```
tolerations:
- key: "key1"
  operator: "Equal"
  value: "value1"
  effect: "NoSchedule"
```
Here's an example of a pod that uses tolerations:
```
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "key1"
    operator: "Exists"
    effect: "NoSchedule"
```

The default value for operator is Equal.

A toleration "matches" a taint if the keys are the same and the effects are the same, and:

* The operator is Exists (in which case no value should be specified), or
* The operator is Equal and the values are equal.
