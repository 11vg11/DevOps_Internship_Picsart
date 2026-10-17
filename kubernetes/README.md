# Kubernetes Fundamentals

## 1. Introduction

Kubernetes orchestrates containerized workloads across a cluster. It continuously reconciles actual resources toward the desired state described by API objects. Docker builds and runs containers; Kubernetes schedules and manages workloads across machines and is not a replacement for an image builder.

## 2. Core concepts

- A **cluster** consists of a control plane and worker **nodes**.
- A **Pod** is the smallest schedulable unit, containing one or more closely coupled containers.
- A **Deployment** manages replicated Pods and rollout history.
- A **Service** provides a stable network endpoint for selected Pods.
- **Namespace** groups namespaced objects; **ConfigMap** stores non-sensitive configuration; **Secret** is intended for sensitive data but requires secure cluster configuration.
- YAML manifests express desired resources through the Kubernetes API.

## 3. How it works

`kubectl` authenticates to the API server and submits a manifest. Controllers compare desired and current state, schedule Pods, and update resources. Services select matching Pod labels; rolling updates replace replicas progressively. Cluster networking and storage depend on installed plugins/providers.

## 4. Commands and examples

```sh
kubectl config current-context       # Confirm target cluster before changes
kubectl get nodes
kubectl get pods -A
kubectl apply -f deployment.yaml
kubectl get deployments,services
kubectl scale deployment/web --replicas=3
kubectl rollout status deployment/web
kubectl rollout undo deployment/web  # Review the effect and compatibility first
kubectl logs -l app=web --all-containers=true
kubectl describe pod POD_NAME
kubectl delete -f deployment.yaml    # Removes objects defined in this file
```

Commands that mutate a cluster should be run only against a lab context you own. `kubectl delete` removes resources; verify the context first.

## 5. Configuration example

Save as `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
    spec:
      containers:
        - name: nginx
          image: nginx:stable-alpine
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: web
spec:
  selector:
    app: web
  ports:
    - port: 80
      targetPort: 80
  type: ClusterIP
```

For reproducible or production use, select a currently supported, reviewed image version or digest and define resource requests/limits and health probes based on workload needs.

## 6. Hands-on exercises

**Prerequisites:** local disposable cluster (kind/minikube) and `kubectl`; confirm context with `kubectl config current-context`. **Expected outcome:** two nginx Pods behind an in-cluster Service.

1. Apply the manifest above and inspect Pods, Deployment, and Service.
2. Scale to three replicas and observe the controller reconcile.
3. Read Pod logs and describe one Pod. Change the image to an invalid tag only in the local lab; inspect the resulting pull error, then restore it.
4. Delete the lab manifest and confirm its resources are gone. No cloud cluster is required.

## 7. Real-world use cases

Deploying stateless services, managing rolling releases, scaling workloads, separating team resources with namespaces, and coordinating service discovery.

## 8. Best practices

- Use least-privilege RBAC, namespaces, network policies, and controlled API access.
- Store secrets securely; Kubernetes Secret objects are base64-encoded by default, not automatically encrypted at rest.
- Define resource requests/limits and readiness/liveness probes intentionally.
- Pin and scan images; avoid privileged containers and root where possible.
- Use declarative manifests, reviews, backups, and rollout/rollback plans. Avoid applying unreviewed manifests to production.

## 9. Common errors and troubleshooting

| Symptom | Checks |
|---|---|
| Pending Pod | `kubectl describe pod`: capacity, scheduling constraints, storage, and events. |
| `ImagePullBackOff` | Image reference, registry authentication, tag/digest, and node connectivity. |
| Service has no endpoints | Service selector vs. Pod labels, Pod readiness, and namespace. |
| CrashLoopBackOff | `kubectl logs --previous`, events, command/args, configuration, and dependencies. |
| Cannot connect | Current context, credentials, API endpoint, and network access. |

## 10. Interview questions

1. **What is a Pod?** The smallest schedulable unit, wrapping one or more cooperating containers.
2. **Deployment vs. Service?** A Deployment manages Pod replicas/rollouts; a Service provides stable connectivity to matching Pods.
3. **What is reconciliation?** Controllers repeatedly compare desired and observed state and act to reduce the difference.
4. **Are Kubernetes Secrets encrypted?** Not necessarily by default; base64 is encoding, so configure encryption at rest and access controls.
5. **What differs between Docker and Kubernetes?** Docker tooling builds/runs containers; Kubernetes orchestrates workloads across a cluster.

## 11. Further learning

- [Kubernetes documentation](https://kubernetes.io/docs/)
- [kubectl reference](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes security checklist](https://kubernetes.io/docs/concepts/security/)

## 12. Progress checklist

- [ ] Inspect cluster context, nodes, namespaces, and events
- [ ] Apply and explain a Deployment and Service manifest
- [ ] Scale, roll out, inspect logs, and troubleshoot a Pod
- [ ] Explain ConfigMaps, Secrets, and access-control practices
