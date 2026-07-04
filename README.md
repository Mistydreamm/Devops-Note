# Intro to DevOps - Practice Questions & Answers



## LO1 - Use of containers and container services

**Q1. Run an httpd container detached, publish container port 80 to host port 8081, give it a custom --name and a custom-hostname, and confirm both with podman inspect.**
> **Command:** `podman run -d --name web --hostname webhost -p 8081:80 httpd`
> **Explanation:** The `-d` flag runs it detached, `-p` maps ports, and you use `podman inspect web` to confirm the identifiers.

**Q2. Run a container with --restart=always, kill its main process, and prove podman restarts it.**
> **Action:** `podman kill <container>`
> **Explanation:** The container runtime automatically detects the process exit and restarts it according to the policy.

**Q3. Run a container with --memory=256m --cpus=0.5 and verify the limits.**
> **Command:** `podman run --memory=256m --cpus=0.5 <image>`
> **Explanation:** Podman applies kernel cgroup limits preventing the container from exceeding these specific resources.

**Q4. Start one container with --env KEY=VALUE and another with --env-file.**
> **Action:** Compare with `podman exec <c> env`
> **Explanation:** `--env` passes a single variable while `--env-file` loads multiple from a file, both visible inside the container.

**Q5. Create a user-defined bridge network, run a container attached to it, and inspect to find IP.**
> **Command:** `podman network create mynet`
> **Explanation:** Custom bridge networks provide internal IPs easily discoverable via `podman network inspect`.

**Q6. Pause and unpause a running container.**
> **Command:** `podman pause <c>` / `podman unpause <c>`
> **Explanation:** Paused processes are suspended by the Linux cgroup freezer and consume zero CPU cycles.

**Q7. Rename an existing container.**
> **Command:** `podman rename old_name new_name`
> **Explanation:** Safely changes the local reference name without restarting the container.

**Q8. Use podman logs with --tail, --since, and -f.**
> **Action:** Use flags to filter logs.
> **Explanation:** `--tail` limits line count, `--since` filters by timestamp, and `-f` streams the logs continuously.

**Q9. Extract a single field using Go-template syntax.**
> **Command:** `podman inspect --format '{{.NetworkSettings.IPAddress}}' <c>`
> **Explanation:** Go-templates parse and extract specific JSON values efficiently without manual searching.

**Q10. Copy a file from the host into a running container and back out.**
> **Command:** `podman cp file <c>:/path` & `podman cp <c>:/path file`
> **Explanation:** Directly transfers data across the container isolation boundary.

**Q11. Exec an interactive shell, install a package, does it survive recreation?**
> **Action:** `podman exec -it <c> bash`
> **Explanation:** Interactive installations do **not** survive recreation because they only exist in the ephemeral read-write layer.

**Q12. Use podman top and podman stats.**
> **Action:** Observe running metrics.
> **Explanation:** `top` displays internal processes, while `stats` streams live CPU/memory host usage.

**Q13. Run a container with -rm.**
> **Command:** `podman run --rm <image>`
> **Explanation:** Auto-cleans the container on exit (good for one-off tasks), but you lose logs for post-mortem debugging.

**Q14. Bind-mount a host directory read-only.**
> **Command:** `podman run -v hostdir:/path:ro <image>`
> **Explanation:** The `:ro` flag enforces read-only access, rejecting internal modifications.

**Q15. Add custom-labels to two containers, list matches.**
> **Command:** `podman run --label env=test` then `podman ps --filter label=env=test`
> **Explanation:** Labels add metadata easily queried using the `--filter` flag.

**Q16. Commit a modified running container into a new image.**
> **Command:** `podman commit <c> newimage`
> **Explanation:** Freezes the current read-write layer into a permanent, reusable image structure.

**Q17. Show the filesystem changes made inside a running container.**
> **Command:** `podman diff <c>`
> **Explanation:** Shows file system changes: **A** (Added), **C** (Changed), **D** (Deleted).

**Q18. Run a container with --network none.**
> **Command:** `podman run --network none <image>`
> **Explanation:** Creates an isolated loopback-only container, ideal for secure offline data processing.

**Q19. Publish a port bound to a specific host IP only.**
> **Command:** `podman run -p 127.0.0.1:8082:80 <image>`
> **Explanation:** Restricts exposure to localhost only, preventing outside network access.

**Q20. Use podman port to list published ports.**
> **Command:** `podman port <c>`
> **Explanation:** Outputs port mappings directly, skipping the need to parse full JSON inspect outputs.

**Q21. Run a container as a non-root user.**
> **Command:** `podman run --user 1000 <image>`
> **Explanation:** Drops root privileges inside the container for enhanced security.

**Q22. Generate a systemd unit.**
> **Command:** `podman generate systemd <c>` (or Quadlet)
> **Explanation:** Creates unit files allowing the host Linux OS to manage and auto-start containers on boot.

**Q23. Run podman events in one terminal.**
> **Command:** `podman events`
> **Explanation:** Acts as a live listener recording discrete state changes like *start*, *die*, and *stop*.

---

## LO2 - Managing and creating container images

**Q1. Write a Containerfile that uses an ARG to choose the base-image.**
> **Code:** `ARG base` / `FROM $base`
> **Explanation:** `--build-arg` injects variables to dynamically change the base image during the build process.

**Q2. Write a multi-stage Containerfile.**
> **Action:** Build in one `FROM`, copy to second minimal `FROM`.
> **Explanation:** Compiles code in one stage and copies only the final binary, drastically reducing image size.

**Q3. Add a .containerignore file.**
> **Action:** Create `.containerignore`.
> **Explanation:** Prevents unnecessary or sensitive host files from bloating the build context sent to the daemon.

**Q4. Build an image and apply three tags at once.**
> **Command:** `podman build -t img:1.0 -t img:1 -t img:latest .`
> **Explanation:** Applies multiple aliases to the same underlying image digest.

**Q5. Inspect layer history.**
> **Command:** `podman history <image>`
> **Explanation:** Shows how each command adds a layer; chaining `RUN` commands with `&&` reduces this bloat.

**Q6. Set a non-root USER and a writable WORKDIR.**
> **Code:** `WORKDIR /app` / `USER 1000`
> **Explanation:** `USER` changes the execution identity, `WORKDIR` sets the default directory for the process.

**Q7. Add a HEALTHCHECK to a Containerfile.**
> **Code:** `HEALTHCHECK CMD curl -f http://localhost/ || exit 1`
> **Explanation:** Periodically tests application readiness, reporting status directly in `podman ps`.

**Q8. ENTRYPOINT vs CMD difference.**
> **Action:** `ENTRYPOINT ["ping"] CMD ["localhost"]`
> **Explanation:** `ENTRYPOINT` is the fixed executable, while `CMD` acts as default arguments easily overridden at runtime.

**Q9. Difference between ADD and COPY.**
> **Action:** Use `COPY` for files, `ADD` for archives.
> **Explanation:** `ADD` automatically extracts tarballs and fetches URLs; `COPY` securely transfers local files only.

**Q10. Save an image to a tarball and restore it.**
> **Command:** `podman save -o img.tar <image>` / `podman load -i img.tar`
> **Explanation:** Converts images to files, essential for offline, air-gapped environment deployments.

**Q11. Pull an image by digest.**
> **Command:** `podman pull name@sha256:...`
> **Explanation:** Guarantees pulling the exact same image bytes, avoiding issues if a tag (like `latest`) is overwritten.

**Q12. Build an image with --no-cache.**
> **Command:** `podman build --no-cache .`
> **Explanation:** Forces all layers to rebuild, vital when fetching the latest upstream OS security patches.

**Q13. Build from a non-default Containerfile.**
> **Command:** `podman build -f custom.file .`
> **Explanation:** `-f` targets a specific filename while keeping the standard build context directory.

**Q14. Use COPY --chown.**
> **Code:** `COPY --chown=appuser:appgroup file /app`
> **Explanation:** Sets file permissions natively during the copy phase, avoiding the bloat of an extra `RUN chown` layer.

**Q15. ARG vs ENV difference.**
> **Action:** Define both in Containerfile.
> **Explanation:** `ARG` only exists during the build phase; `ENV` persists inside the running container at runtime.

**Q16. Login and push to a private registry.**
> **Command:** `podman login <registry>` then `podman push`
> **Explanation:** Auth tokens are securely stored locally in `~/.config/containers/auth.json`.

**Q17. Remove dangling images.**
> **Command:** `podman image prune`
> **Explanation:** Deletes untagged, orphaned layers ("dangling") to free up disk space.

**Q18. Containerfile for a python static file server.**
> **Code:** `COPY . /web` -> `CMD ["python", "-m", "http.server", "80"]`
> **Explanation:** Easily packages and serves a static directory over HTTP.

**Q19. Pin a specific package version.**
> **Code:** `RUN apk add --no-cache curl=8.5.0-r0`
> **Explanation:** Prevents unexpected upstream software updates from breaking your build reproducibility.

**Q20. Inspect an unknown image.**
> **Command:** `podman image inspect <image>`
> **Explanation:** Reveals internal metadata like entrypoints and exposed ports to help you run it correctly.

**Q21. Multi-line RUN cleanup.**
> **Code:** `RUN apt install -y pkg && apt clean`
> **Explanation:** Cleaning caches in the *same* `RUN` step prevents the junk from being permanently saved in the image layer.

**Q22. Image mirroring.**
> **Action:** Tag and push to Docker Hub AND Quay.
> **Explanation:** Ensures high availability of your application if one external registry goes offline.

**Q23. Security value of minimal base image.**
> **Action:** Use Alpine or Distroless.
> **Explanation:** Minimal images contain fewer utilities (like shells), drastically reducing the attack surface.

---

## LO3 - Application delivery, networking & security

**Q1. Pod with two containers sharing localhost.**
> **Command:** `podman pod create -p 8080:80`
> **Explanation:** Pods share the same network namespace, meaning containers inside talk to each other via `127.0.0.1`.

**Q2. Custom network DNS resolution.**
> **Action:** `podman network create net` -> run containers on it.
> **Explanation:** Custom networks have built-in DNS, allowing apps to reach databases just by using the container name.

**Q3-4. Named volumes for persistence.**
> **Command:** `podman run -v db_data:/var/lib/postgresql/data ...`
> **Explanation:** Named volumes store data on the host filesystem independently, surviving container destruction.

**Q5. Use podman secret.**
> **Command:** `podman secret create db_pass ...`
> **Explanation:** Injects passwords securely, keeping them completely hidden from `podman inspect` and plain text logs.

**Q6-7. Podman compose internal database.**
> **Code:** Do not specify `ports:` for the DB in `compose.yaml`.
> **Explanation:** Without port mappings, the DB remains strictly isolated on the internal bridge network.

**Q8. Compose healthcheck and depends_on.**
> **Code:** `depends_on: db: condition: service_healthy`
> **Explanation:** Forces the application container to pause startup until the database is fully initialized and healthy.

**Q10. Scale a compose service.**
> **Command:** `podman compose up --scale app=3`
> **Explanation:** Spins up identical replicas; the compose network automatically load-balances requests across them.

**Q11. Bind mount vs Named volume.**
> **Action:** Bind mount for `./config.conf`, Volume for DB data.
> **Explanation:** Bind mounts easily map local editable config files; named volumes are optimized for heavy, persistent data.

**Q13. Reverse proxy (nginx).**
> **Action:** Proxy container exposes port 80, forwards to internal app name.
> **Explanation:** Creates a single entry point handling host traffic and securely routing it to isolated backend containers.

**Q15-16. Generate and Play Kubernetes YAML.**
> **Command:** `podman generate kube <pod>` / `podman kube play file.yaml`
> **Explanation:** Natively translates local pod setups into declarative Kubernetes manifests for easier migration.

**Q18. Pod namespaces.**
> **Command:** `podman pod inspect`
> **Explanation:** Pods share Network and IPC namespaces, but by default maintain completely isolated PID (process) namespaces.

**Q22. Risk of shared read-write storage.**
> **Action:** Two app replicas mounting the same data volume.
> **Explanation:** Concurrent writes risk major data corruption if the application is not built to handle file locking properly.

---
## LO4 - Accelerated delivery of multilayer applications using containers

**Q1. Create a Deployment named web running nginx: 1.25 with 3 replicas using a single imperative kubectl create deployment command, then export its generated YAML to a file.**
> **Command:** `kubectl create deployment web --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml > dep.yaml`
> **Explanation:** This generates the exact Kubernetes manifest structure without sending it to the API server, allowing you to save it.

**Q2. Enable pulling images from DockerHub as an authenticated user to lower the chance of hitting the pull limit. What kind of resource do you need to create?**
> **Action:** Create a `Secret` of type `kubernetes.io/dockerconfigjson` (commonly called an image pull secret).
> **Explanation:** This securely stores your registry credentials in the cluster so the kubelet can authenticate when pulling images.

**Q3. Scale web from 3 to 5 replicas two different ways (once with kubectl scale, once by editing the manifest) and show the ReplicaSet that results.**
> **Command:** `kubectl scale deployment web --replicas=5` OR edit `dep.yaml` and `kubectl apply -f dep.yaml`.
> **Explanation:** Both methods update the Deployment spec, which then commands its underlying ReplicaSet to spin up 2 additional pods.

**Q4. Perform a rolling update of web from nginx: 1.25 to nginx:1.27 and watch it with kubectl rollout status.**
> **Command:** `kubectl set image deployment/web nginx=nginx:1.27` then `kubectl rollout status deployment/web`.
> **Explanation:** The rollout automatically updates pods incrementally, ensuring zero downtime.

**Q5. View a Deployment's rollout history and roll back to the previous revision. Explain what the CHANGE-CAUSE column shows and how to populate it.**
> **Command:** `kubectl rollout history deployment/web` / `kubectl rollout undo deployment/web`.
> **Explanation:** `CHANGE-CAUSE` shows the command or annotation that triggered the update. It is populated by using the `--kubernetes.io/change-cause` annotation.

**Q6. Set the update strategy to Rolling Update with maxSurge: 1 and maxUnavailable: 0, and explain the effect on availability during an update.**
> **Action:** Define these under `spec.strategy.rollingUpdate` in the YAML.
> **Explanation:** This guarantees 100% capacity is maintained at all times, as Kubernetes will force a new pod to start and become ready before terminating any old pod.

**Q7. Switch a Deployment's strategy to Recreate and describe a concrete scenario where this is required instead of Rolling Update.**
> **Code:** `strategy: type: Recreate`
> **Explanation:** All old pods are killed before new ones are created. This is mandatory for legacy applications or databases that require exclusive locks on a storage volume.

**Q8. Add CPU/memory requests and limits to a Deployment's container and verify they appear in the running pod spec.**
> **Code:** Add a `resources:` block with `requests` and `limits` under the container spec.
> **Explanation:** Requests guarantee the pod gets minimum node resources to schedule, while limits cap usage to prevent it from starving the node.

**Q9. Set revision HistoryLimit: 3 and explain how it affects your ability to roll back and how many old Replica Sets are kept.**
> **Code:** `revisionHistoryLimit: 3`
> **Explanation:** It retains exactly 3 old ReplicaSets. You will only be able to roll back to these 3 previous states, keeping the cluster clean of obsolete resources.

**Q10. Set a Deployment's image to a non-existent tag, observe the rollout get stuck, and explain why the old pods keep serving traffic.**
> **Action:** Update image to `nginx:fake-tag`.
> **Explanation:** The rollout halts in `ImagePullBackOff`. Old pods continue serving traffic because the rolling update strategy waits for new pods to become 'Ready' before terminating old ones.

**Q11. Use a label selector to list only the pods belonging to one Deployment, and explain the Deployment → ReplicaSet Pod selector relationship.**
> **Command:** `kubectl get pods -l app=web`
> **Explanation:** A Deployment manages a ReplicaSet using a label selector, and the ReplicaSet uses that exact same selector to manage and identify its Pods.

**Q12. Expose a Deployment with kubectl expose, then explain what object was created and how its selector was derived.**
> **Command:** `kubectl expose deployment web --port=80`
> **Explanation:** A Service object is created. Its `selector` is automatically copied directly from the Deployment's pod template labels.

**Q13. Add a sidecar (second) container to a Deployment's pod template and explain how the two containers share the pod network and volumes.**
> **Action:** Add a second item under `containers:` in the pod spec.
> **Explanation:** Containers in the same pod share the same network namespace (can communicate via localhost) and can mount the exact same storage volumes.

**Q14. Write a Deployment manifest for httpd:2.4 with 2 replicas, a named container port, and a nodeSelector; explain why it stays Pending if no node matches.**
> **Code:** Add `nodeSelector: disktype: ssd`.
> **Explanation:** The scheduler strictly requires a node with that exact label. If none exists, the pod cannot be placed and remains Pending.

**Q15. Create a bare Pod (no controller) running busybox that sleeps, then explain what happens when you delete it versus a Deployment-managed pod.**
> **Command:** `kubectl run bare --image=busybox -- sleep 3600` then `kubectl delete pod bare`.
> **Explanation:** A bare pod is permanently gone when deleted. A Deployment-managed pod would be instantly recreated by its ReplicaSet to maintain the desired count.

**Q16. Write a StatefulSet for redis:7 with 3 replicas plus a headless Service, and show the stable pod names and per-pod DNS records.**
> **Action:** Deploy the YAML and run `kubectl get pods`.
> **Explanation:** StatefulSets create pods with predictable, sticky names (e.g., `redis-0`, `redis-1`) and unique network identities via the headless service.

**Q17. Create a DaemonSet running a busybox agent and explain why exactly one pod runs per node.**
> **Action:** Deploy a DaemonSet manifest.
> **Explanation:** DaemonSets bypass the concept of "replicas" and interact directly with the scheduler to ensure every eligible node runs exactly one instance of the pod.

**Q18. Create a Job using busybox/perl that computes something once and completes; show COMPLETIONS and read the result from logs.**
> **Action:** Deploy a Job manifest calculating pi, wait, then `kubectl logs <job-pod>`.
> **Explanation:** Jobs run a finite task until successful completion, rather than keeping a server running indefinitely.

**Q19. Create a CronJob that prints the date every minute; show how to suspend it and how to list the Jobs it spawns.**
> **Command:** `kubectl create cronjob date-job --schedule="* * * * *" --image=busybox -- date`. Suspend via `kubectl patch cronjob date-job -p '{"spec":{"suspend":true}}'`.
> **Explanation:** CronJobs act like standard Linux cron, automatically spawning standard Kubernetes Jobs on a schedule.

**Q20. Manually run a Job from an existing CronJob (kubectl create job--from=cronjob/...) to test it on demand.**
> **Command:** `kubectl create job test-run --from=cronjob/date-job`
> **Explanation:** This allows you to immediately test the CronJob's execution logic without waiting for the next scheduled minute.

**Q21. Show that a StatefulSet creates and terminates pods in order, and contrast this with a Deployment.**
> **Action:** Watch StatefulSet creation (`kubectl get pods -w`).
> **Explanation:** StatefulSets strictly wait for `pod-0` to be Ready before starting `pod-1`. Deployments spin up all replicas in parallel.

**Q22. Create a multi-container Pod where two containers share an emptyDir one writes a file, the other reads it. Prove it's shared.**
> **Action:** Mount the same `emptyDir` volume to both containers. Container A runs `echo "hello" > /data/msg`, Container B runs `cat /data/msg`.
> **Explanation:** `emptyDir` creates a temporary directory on the node that is shared across all containers in the pod.

**Q23. List the Storage Classes, PVs and PVCs.**
> **Command:** `kubectl get sc,pv,pvc`
> **Explanation:** This provides a complete overview of the cluster's storage architecture, from available provisioners (sc) to claims (pvc).

**Q24. Mount a ConfigMap as a volume so each key becomes a file, and verify the contents inside the pod.**
> **Action:** Reference the ConfigMap in `volumes:` and mount it in `volumeMounts:`.
> **Explanation:** Kubernetes dynamically injects the ConfigMap's keys as individual text files into the mounted directory.

**Q25. Mount a single ConfigMap key to a specific path using subPath; explain when it's needed and its update caveat.**
> **Code:** Use `subPath: mykey.conf` in the `volumeMount`.
> **Explanation:** `subPath` is needed to inject a single file without overwriting the entire destination directory. Caveat: `subPath` mounts do not receive automatic live updates.

**Q26. Mount a Secret as a volume and verify the files contain decoded values with restrictive permissions.**
> **Action:** Mount the Secret and `kubectl exec` to check permissions (`ls -l`).
> **Explanation:** Secrets are securely mounted as in-memory `tmpfs` files with restrictive default permissions (often `0420` or `0400`) to prevent unauthorized read access.

**Q27. Show that scaling a StatefulSet creates one PVC per replica, then explain what happens to those PVCs when the StatefulSet is deleted.**
> **Action:** Scale the StatefulSet, check `kubectl get pvc`. Then delete it and check again.
> **Explanation:** Each pod gets its own unique PVC based on the `volumeClaimTemplate`. When the StatefulSet is deleted, the PVCs are intentionally left behind to prevent accidental data loss.

**Q28. Use an initContainer to pre-populate data into a shared volume before the main container reads it.**
> **Code:** Add `initContainers:` block that writes to an `emptyDir` mounted by the main container.
> **Explanation:** InitContainers run strictly sequentially and must complete successfully before any main application container starts, making them ideal for setup tasks.

**Q29. Set readOnly: true on a volume mount and prove writes are rejected.**
> **Code:** `readOnly: true` in the `volumeMounts` section.
> **Explanation:** This enforces immutability at the container filesystem level, throwing a "Read-only file system" error if the app tries to write.

**Q30. Set a sizeLimit on an emptyDir and explain what happens if the container exceeds it.**
> **Code:** `emptyDir: sizeLimit: 100Mi`
> **Explanation:** If the container writes more than 100Mi, the kubelet will detect the violation and evict the pod to protect the node's disk.

**Q31. Create a generic Secret from literals for a DB username/password, then show the stored values are base64-encoded, not encrypted.**
> **Command:** `kubectl create secret generic db-auth --from-literal=user=admin` then `kubectl get secret db-auth -o yaml`.
> **Explanation:** The values in the YAML are visibly base64 encoded strings, meaning anyone with read access to the Secret object can decode them instantly.

**Q32. Create a Secret from files (--from-file) holding a certificate and key, and identify the resulting keys.**
> **Command:** `kubectl create secret generic tls-cert --from-file=cert.pem --from-file=key.pem`
> **Explanation:** The resulting Secret will have two keys exactly matching the filenames: `cert.pem` and `key.pem`.

**Q33. Create a kubernetes.io/tls typed Secret from a cert/key pair and explain where it's consumed.**
> **Command:** `kubectl create secret tls my-tls --cert=cert.pem --key=key.pem`
> **Explanation:** This specialized Secret format enforces the presence of `tls.crt` and `tls.key` keys, and is directly consumed by Ingress controllers for HTTPS termination.

**Q34. Mount a Secret as a volume and explain the security trade-offs versus env vars.**
> **Explanation:** Volumes are much safer. Environment variables can easily be leaked if the application crashes and dumps its environment block to the logs, or via `docker inspect`.

**Q35. Use envFrom with a secretRef to load every key of a Secret as env vars at once.**
> **Code:** `envFrom: - secretRef: name: my-secret`
> **Explanation:** This bulk-injects all key-value pairs from the Secret directly into the container's environment simultaneously, saving manifest space.

**Q36. Create a docker-registry (image-pull) Secret and reference it via imagePullSecrets; explain when it's required.**
> **Code:** Add `imagePullSecrets: - name: my-registry-key` to the pod spec.
> **Explanation:** Required when pulling images from private, authenticated registries like a private Docker Hub repository or private Quay repo.

**Q37. Create a Secret with several keys and selectively mount only one of them into a pod.**
> **Code:** Use the `items:` array under `secret:` in the volume definition to specify which key maps to which path.
> **Explanation:** This follows the principle of least privilege, exposing only the specific credential the application actually needs.

**Q38. Create a ClusterIP Service for a Deployment and resolve it by DNS from another pod (nslookup <svc>.<ns>.svc.cluster.local).**
> **Command:** `kubectl expose deployment web` then `nslookup web.default.svc.cluster.local` from a debug pod.
> **Explanation:** The internal DNS server (CoreDNS) automatically creates an A record for the Service using this predictable namespace structure.

**Q39. Create a NodePort Service and reach it via minikube service <svc> --url and $(minikube ip): <nodePort>.**
> **Command:** `kubectl expose deploy web --type=NodePort --port=80`
> **Explanation:** NodePort opens a static port (usually 30000-32767) on every node's IP in the cluster to allow external access.

**Q40. Create a Load Balancer Service, explain why EXTERNAL-IP is <pending> on minikube, and obtain access with minikube tunnel.**
> **Action:** Expose with `--type=LoadBalancer`. Run `minikube tunnel`.
> **Explanation:** Bare-metal clusters and minikube lack cloud provider API integrations to provision real LoadBalancers. The tunnel simulates one locally.

**Q41. Create a headless Service (clusterIP: None) for a StatefulSet and show it returns per-pod A records instead of a single VIP.**
> **Code:** `clusterIP: None`
> **Explanation:** Bypasses the kube-proxy load balancer. DNS queries return the direct IP addresses of the backing pods, required for clustered databases.

**Q42. Inspect a Service's Endpoints/EndpointSlice and explain how they're populated from the pod selector.**
> **Command:** `kubectl get endpoints <svc>`
> **Explanation:** The endpoints controller continuously watches for Pods that match the Service's selector and are in a 'Ready' state, dynamically syncing their IPs to the Endpoints object.

**Q43. Demonstrate cross-namespace access using the FQDN, and show the short name fails from another namespace.**
> **Action:** Run a pod in `ns2` and try to curl `web` vs `web.ns1.svc.cluster.local`.
> **Explanation:** Short names only search the pod's local namespace domain. Cross-namespace communication requires the Fully Qualified Domain Name (FQDN).

**Q44. Compare kubectl port-forward to a Service versus to a Pod - explain the difference and when each fits.**
> **Explanation:** Forwarding to a Service load-balances your local requests across any of the backend pods. Forwarding to a Pod forces connection to that specific instance (best for debugging a specific failing pod).

**Q45. Explain the difference between a Service's port, targetPort, and nodePort.**
> **Explanation:** `port` is the internal cluster IP port. `targetPort` is the actual port the container is listening on. `nodePort` is the externally accessible host port on the nodes.

**Q46. Verify connectivity to a ClusterIP Service from a throwaway debug pod (kubectl run tmp --rm-it -- image=busybox -- sh) with wget/nc.**
> **Command:** `kubectl run tmp --rm -it --image=busybox -- sh` -> `wget -O- http://web`
> **Explanation:** A throwaway interactive pod provides an isolated environment inside the cluster network to execute raw connectivity tests.

**Q47. Create two Deployments and one Service that load-balances across both by sharing a common label; prove requests hit both.**
> **Action:** Give Deployment A and B the label `app=shared`. Create a Service selecting `app=shared`.
> **Explanation:** Services are entirely decoupled from Deployments; they purely route traffic to *any* pod matching their label selector.

**Q48. Systematically diagnose why a pod can't reach a Service: DNS endpoints selector labels readiness -> port.**
> **Explanation:** Diagnosis flow: 1. Is the Pod ready? 2. Do the Pod labels match the Service selector? 3. Are the Endpoints populated? 4. Does the Service DNS resolve? 5. Is the target port correct?

**Q49. Expose a service with a NodePort and verify it works.**
> **Command:** `kubectl expose deployment web --type=NodePort --port=80`
> **Explanation:** Creates a cluster-wide high port mapping for immediate external testing without needing an Ingress.

**Q50. Add an HTTP liveness Probe hitting / on port 80 to an nginx Deployment and verify via kubectl describe pod.**
> **Action:** Define `livenessProbe:` with an `httpGet` action.
> **Explanation:** The kubelet periodically pings the endpoint. If it returns an error code (or times out), the kubelet forcefully restarts the container.

**Q51. Add a tcpSocket readiness probe to a redis container on port 6379 and verify it.**
> **Action:** Define `readinessProbe:` with a `tcpSocket` action.
> **Explanation:** Readiness probes block the pod from receiving Service traffic until the network socket successfully accepts TCP connections.

**Q52. Configure a startup probe with a failure Threshold periodSeconds budget for a ~2-minute boot and justify the numbers.**
> **Code:** `startupProbe: failureThreshold: 12, periodSeconds: 10`.
> **Explanation:** `12 * 10s = 120 seconds`. This disables liveness checks for 2 minutes, granting a slow legacy application ample time to boot without being falsely restarted.

**Q53. Explain the default behavior when no probes are defined at all.**
> **Explanation:** Without probes, Kubernetes blindly assumes a container is fully healthy and ready to serve traffic the exact millisecond its primary process (PID 1) starts.
## LO5 - Solve problems with application shipping by using containers

**Q1. `podman run -d --name web -p 80:8080 docker.io/library/nginx`**
> **Error:** Nginx listens on 80, but the site isn't reachable on the host. The port mapping is reversed.
> **Fix:** Use `-p 8080:80` (HostPort:ContainerPort).

**Q2. `podman run-d--name db -e MYSQL_ROOT_PASSWORD docker.io/library/mysql`**
> **Error:** The container exits during init because the environment variable is declared but empty.
> **Fix:** Provide a value: `-e MYSQL_ROOT_PASSWORD=secret`.

**Q3. `podman run-d--name app --network host -p 8080:80 docker.io/library/nginx`**
> **Error:** The `-p` flag is ignored.
> **Explanation:** `--network host` uses the host's exact network stack, rendering isolated port mappings ineffective.

**Q4. `podman run-d--name c1 docker.io/library/busybox`**
> **Error:** The container goes straight to Exited (0).
> **Fix:** Busybox has no default long-running command. Add one like `sleep infinity` to keep it alive.

**Q5. `podman run --rm-d-name job docker.io/library/alpine echo hello`**
> **Error:** `podman logs job` fails.
> **Explanation:** The `--rm` flag deletes the container (and its logs) the exact moment it finishes echoing "hello".

**Q6. `podman run -d -p 8080:80 --memory 8m docker.io/library/mysql`**
> **Error:** The database never becomes healthy.
> **Explanation:** An 8MB memory limit is far too small for MySQL, causing the kernel to instantly OOMKill it.

**Q7. Name resolution fails when trying to ping container 'b' from container 'a'.**
> **Error:** The containers are on the default bridge network, which lacks DNS.
> **Fix:** Create a custom user-defined network and attach both containers to it.

**Q8. `podman run -d --name web -v./html:/usr/share/nginx/html docker.io/library/nginx`**
> **Error:** Files aren't visible or SELinux denies access.
> **Fix:** Add the `:Z` flag (`-v ./html:...:Z`) to apply the correct SELinux context to the volume.

**Q9. (Instruction to identify Dockerfile mistakes - covered in subsequent questions).**
> **Action:** Analyze typical Containerfile errors.

**Q10. `FROM debian:12 \n RUN apt-get install -y nginx`**
> **Error:** The build fails to find the package because the local package cache is empty.
> **Fix:** Prepend the update command: `RUN apt-get update && apt-get install -y nginx`.

**Q11. `CMD python app.py` (Shell vs Exec form).**
> **Error:** The app starts but doesn't handle stop signals cleanly.
> **Fix:** Use the JSON array exec form: `CMD ["python", "app.py"]` to avoid wrapping the process in a shell.

**Q12. `COPY ./app \n RUN npm install`**
> **Error:** Every source code change triggers a full, slow `npm install`.
> **Fix:** Reorder for caching: `COPY package.json .`, then `RUN npm install`, and finally `COPY . .`.

**Q13. `ENV PATH=/app/bin \n RUN apk add --no-cache curl`**
> **Error:** `curl` and `sh` aren't found. Overwriting `PATH` completely deletes system paths.
> **Fix:** Append to the path instead: `ENV PATH=$PATH:/app/bin`.

**Q14 & Q15. `EXPOSE 8080` but app runs on `3000`.**
> **Error:** Nothing answers on 8080. 
> **Explanation:** `EXPOSE` is merely documentation. It does not actively forward traffic. The app is actually listening on 3000.

**Q16. `RUN apt-get update && apt-get install -y build-essential`**
> **Error:** The image size is huge because cache lists are saved.
> **Fix:** Clean up in the same layer: append `&& apt-get clean && rm -rf /var/lib/apt/lists/*`.

**Q17. `USER appuser \n COPY requirements.txt /app/requirements.txt`**
> **Error:** Permission denied during `pip install`. `USER` makes subsequent `COPY` commands run as root by default.
> **Fix:** Use `COPY --chown=appuser:appgroup requirements.txt ...`.

**Q18. `RUN go build -o app` resulting in a 1GB image.**
> **Error:** The final image contains the entire Go compiler toolchain.
> **Fix:** Use a multi-stage build. Compile in stage one, copy *only* the final binary into a minimal stage two.

**Q19. A pod is stuck Pending.**
> **Action:** Walk through `kubectl describe pod`.
> **Explanation:** Usually caused by scheduling constraints: insufficient CPU/memory on nodes, missing NodeSelectors, or unbound PVCs.

**Q20. Deployment fails after removing a couple of spaces.**
> **Error:** YAML parsing error.
> **Fix:** YAML relies strictly on indentation. Restore the spaces to fix the nested schema structure.

**Q21. A pod is in ImagePullBackOff.**
> **Action:** Diagnose the cause.
> **Fix:** Verify image name spelling, tag existence, or add `imagePullSecrets` if the registry is private.

**Q22. A pod is in CrashLoopBackOff.**
> **Action:** Use `kubectl logs --previous <pod>`.
> **Explanation:** This command retrieves the stack trace of the *crashed* container to identify the application bug.

**Q23. A pod's last state shows OOMKilled.**
> **Action:** Confirm via `describe pod` and fix it.
> **Fix:** The container exceeded its hard memory limit. Raise `resources.limits.memory` in the manifest.

**Q24. A Deployment's pods never become Ready.**
> **Action:** Trace it to a failing readiness probe.
> **Fix:** Verify the probe's HTTP path, port, and authentication. If the app is slow to start, add a `startupProbe`.

**Q25. A Service returns nothing (kubectl get endpoints is empty).**
> **Error:** Service to Pod label mismatch.
> **Fix:** Ensure the Service's `selector` exactly matches the `labels` defined in the Pod template.

**Q26. `spec.selector.matchLabels` doesn't match `spec.template.metadata.labels`.**
> **Error:** `kubectl apply` returns a validation error.
> **Fix:** Deployments mandate label parity. Make sure both blocks contain the exact same key-value pairs.

**Q27. `resources.requests` exceed any node's capacity.**
> **Error:** The pod stays Pending due to "Insufficient cpu/memory".
> **Fix:** Lower the resource requests, or add a larger node to the cluster.

**Q28. A pod mounts a PVC that doesn't exist.**
> **Error:** Pod stuck in ContainerCreating / FailedMount.
> **Fix:** Create the PersistentVolumeClaim with the exact name referenced in the Pod spec.

**Q29. An Ubuntu container with no long-running command shows CrashLoopBackOff.**
> **Error:** Base OS images exit naturally, and Kubernetes tries to restart them endlessly.
> **Fix:** Add a persistent command like `command: ["sleep", "infinity"]`.

**Q30. Triage everything that happened to a pod over time.**
> **Command:** `kubectl get events --sort-by=.lastTimestamp`
> **Explanation:** Sorts cluster events chronologically to easily visualize the exact sequence of failures.

**Q31. Debug DNS from inside a pod.**
> **Command:** `kubectl exec -it <pod> -- sh`
> **Action:** Run `nslookup kubernetes.default` and inspect `cat /etc/resolv.conf`.

**Q32. Troubleshoot a distroless/no-shell container.**
> **Command:** `kubectl debug -it <pod> --image=busybox --target=<container>`
> **Explanation:** Ephemeral containers inject debugging utilities (like shell/curl) directly into a running, locked-down pod.

**Q33. Test connectivity to a Service from inside the cluster.**
> **Command:** `kubectl run tmp --rm -it --image=busybox -- sh`
> **Explanation:** Spins up a throwaway pod perfectly isolated for running internal network tests (`wget`, `nc`).

**Q34. A node shows NotReady.**
> **Action:** List what you'd check.
> **Explanation:** Inspect `kubectl describe node` for DiskPressure/MemoryPressure, or check host-level `kubelet` logs.

**Q35. A pod is stuck Terminating.**
> **Error:** Finalizers are blocking deletion, or graceful shutdown is hanging.
> **Fix:** `kubectl delete pod <pod> --grace-period=0 --force`. Risk: May leave orphaned resources or corrupt state.

**Q36. Wrong apiVersion/kind pairing (e.g., apps/v1 + Pod).**
> **Error:** Kubernetes schema validation error.
> **Fix:** A Pod must use `apiVersion: v1`, whereas Deployments/StatefulSets use `apiVersion: apps/v1`.

**Q37. Pod fails with CreateContainerConfigError.**
> **Error:** A `configMapKeyRef` or `secretKeyRef` is referencing a key that does not exist in the ConfigMap/Secret.
> **Fix:** Add the missing key to the ConfigMap, or correct the typo in the Pod spec.

**Q38. A pod can't mount a Secret from a different namespace.**
> **Error:** Secrets are strictly namespace-scoped for security.
> **Fix:** Duplicate the Secret into the Pod's namespace.

**Q39. Rollout is stuck with Progress Deadline Exceeded.**
> **Action:** Use `kubectl describe deployment` to find the cause.
> **Explanation:** Usually means the new ReplicaSet failed to spin up Ready pods within the default 600-second timeout.

**Q40. Catch typos before applying manifests.**
> **Command:** `kubectl apply -f file.yaml --dry-run=server`
> **Explanation:** Uses the cluster's API to safely validate manifest syntax and fields without making actual changes.

**Q41. App logs say it can't reach its database Service.**
> **Action:** Systematically verify the chain.
> **Flow:** 1. Pod running? -> 2. Service exists? -> 3. Endpoints populated (Labels match)? -> 4. DNS resolves? -> 5. Port correct?

**Q42. Determine the root cause of repeated restarts automatically.**
> **Command:** `kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[0].lastState.terminated.exitCode}'`
> **Explanation:** Extracts the exact exit code (e.g., 137 for OOM, 1 for App Crash) crucial for debugging.

**Q43. Compare OpenShift Route to Ingress.**
> **Explanation:** OpenShift Routes are a native concept providing built-in edge routing and TLS termination out-of-the-box, whereas vanilla Kubernetes Ingress requires deploying a separate controller (like Nginx).
