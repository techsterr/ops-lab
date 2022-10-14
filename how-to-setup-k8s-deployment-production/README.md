# Requirements

Health checks
- Readiness probes
- Liveness probe

Fault tolerance
- Replicas
- Affinity
- PodDisruptionBudget

Resources utilisation
- Limit CPU, Mem

Graceful shutdown

Rolling update

Tagging resources

Node
- Selector
- Taints and Tolerations

# Health checks

## Readiness probes and Liveness probe
```yml
readinessProbe:
  httpGet:
    path: '/health'
    port: 8081
  initialDelaySeconds: 60
  periodSeconds: 10
  timeoutSeconds: 10
  failureThreshold: 10
  successThreshold: 
livenessProbe:
  httpGet:
    path: '/health'
    port: 8081
  initialDelaySeconds: 60
  periodSeconds: 30
  timeoutSeconds: 10
  failureThreshold: 10
  successThreshold: 
```

# Fault tolerance

## Replicas

```yml
spec:
  replicas: 3
```


## Affinity

- `requiredDuringSchedulingIgnoredDuringExecution`: The scheduler can't schedule the Pod unless the rule is met. This functions like nodeSelector, but with a more expressive syntax.
- `preferredDuringSchedulingIgnoredDuringExecution`: The scheduler tries to find a node that meets the rule. If a matching node is not available, the scheduler still schedules the Pod.


```yml
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: topology.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: topology.kubernetes.io/zone
```
```yml
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/os
            operator: In
            values:
            - linux
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: label-1
            operator: In
            values:
            - key-1
      - weight: 50
        preference:
          matchExpressions:
          - key: label-2
            operator: In
            values:
            - key-2
```

## AntiAffinity

```yml
spec:
  ...
  spec:
    affinity: |
      podAntiAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchLabels:
                app:  {{ template "template.name" . }}
                release: "{{ .Release.Name }}"
              matchExpressions:
                - key: role
                  operator: NotIn
                  values:
                    - test
            topologyKey: kubernetes.io/hostname
        preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 100
            podAffinityTerm:
              labelSelector:
                matchLabels:
                  app:  {{ template "template.name" . }}
                  release: "{{ .Release.Name }}"
                matchExpressions:
                  - key: role
                    operator: NotIn
                    values:
                      - test
              topologyKey: failure-domain.beta.kubernetes.io/zone
```

```yml
      affinity: |
        podAntiAffinity:
          preferredDuringSchedulingIgnoredDuringExecution:
          - weight: 1
            podAffinityTerm:
              topologyKey: kubernetes.io/hostname
              labelSelector:
                matchLabels:
                  app: "{{ template "template.name" . }}"
                  release: {{ .Release.Name | quote }}
```

# PodDisruptionBudget

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
 name: example
spec:
 minAvailable: 4
 selector:
   matchLabels:
     app: my-app
```


# Resources utilisation

```yml
  resources:
    requests:
      cpu: 50m
      memory: 100Mi
    limits:
      cpu: 100m
      memory: 100Mi
```

# Graceful shutdown

```yml
spec:
    spec:
      terminationGracePeriodSeconds: 60
      containers:
        ...
```

# Rolling update

```yml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 100%
      maxUnavailable: 0
```

# Tagging resources

```yml
spec:
  template:
    metadata:
      labels:
        app: app-name
```

```yml
  template:
    metadata:
      labels:
        app: "app-name"
        release: "app-release-v2"
```

# Node

## Taint Node
```sh
kubectl taint nodes node1 node-type=preemptible:NoSchedule
```

## nodeSelector & tolerations
```yml
    nodeSelector:
      node-type: preemptible
    tolerations:
    - key: "node-type"
      value: "preemptible"
      operator: "Equal"
```