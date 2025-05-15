# k8s_hardening

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secure-app
spec:
  securityContext:
    runAsUser: 1000                  # Run as non-root user
    runAsGroup: 3000                 # Assign a group
    fsGroup: 2000                    # For volumes, gives group ownership
    runAsNonRoot: true               # Enforce non-root execution
    seccompProfile:                  # Seccomp (short for Secure Computing Mode) is a Linux kernel feature that restricts the system calls (syscalls) a process can make.
      type: RuntimeDefault           # Use default seccomp profile
  containers:
  - name: app-container
    image: myapp:latest
    securityContext:
      allowPrivilegeEscalation: false   # Prevent privilege escalation
      capabilities:
        drop:
          - ALL                         # Drop all Linux capabilities
        add:
          - NET_ADMIN              # Adds capability to manage networking
      readOnlyRootFilesystem: true
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: restrict-backend-traffic
  namespace: my-namespace
spec:
  podSelector:
    matchLabels:
      app: backend
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: frontend
  egress: []
```
