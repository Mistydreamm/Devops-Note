# Intro to DevOps - Complete Practice Questions & Answers

Ce document propose la correction exhaustive et structurée de toutes les questions de pratique du cours (LO1 à LO6).

---

## LO1 - Use of containers and container services

**Q1. Run an httpd container detached, publish port 80 to 8081, custom name/hostname.**
> **Command:** `podman run -d --name web --hostname webhost -p 8081:80 httpd`
> **Explanation:** `-d` runs it detached, `-p` maps ports, `podman inspect web` confirms identifiers.

**Q2. Run with --restart=always, kill main process, prove restart.**
> **Command:** `podman run -d --restart=always --name c1 alpine sleep inf` -> `podman kill c1`
> **Explanation:** The runtime detects the exit and automatically restarts the container.

**Q3. Run with --memory=256m --cpus=0.5 and verify limits.**
> **Command:** `podman run -d --memory=256m --cpus=0.5 nginx`
> **Explanation:** Cgroup limits are applied by the kernel, verifiable via `podman stats`.

**Q4. Start one with --env and another with --env-file.**
> **Command:** `podman run --env K=V alpine env` vs `podman run --env-file ./file.env alpine env`
> **Explanation:** `--env` is for single inline vars; `--env-file` loads multiple vars from a file safely.

**Q5. Create user-defined network, run container, find IP.**
> **Command:** `podman network create net1` -> `podman run --network net1 -d alpine`
> **Explanation:** Custom networks assign internal IPs, found via `podman network inspect net1`.

**Q6. Pause and unpause a container.**
> **Command:** `podman pause <c>` / `podman unpause <c>`
> **Explanation:** Paused processes are suspended by the cgroup freezer (0% CPU usage).

**Q7. Rename an existing container.**
> **Command:** `podman rename old_name new_name`
> **Explanation:** Changes the local reference without needing to restart the container.

**Q8. Use podman logs with --tail, --since, and -f.**
> **Command:** `podman logs -f --tail 10 --since 5m <c>`
> **Explanation:** `-f` streams live, `--tail` limits line count, `--since` filters by time.

**Q9. Extract single field using format.**
> **Command:** `podman inspect --format '{{.NetworkSettings.IPAddress}}' <c>`
> **Explanation:** Go-templates parse and extract specific JSON values natively.

**Q10. Copy a file from host into container and back.**
> **Command:** `podman cp local.txt <c>:/app/` / `podman cp <c>:/app/file.txt .`
> **Explanation:** Directly transfers data across the container isolation boundary.

**Q11. Exec interactive shell, install package, does it survive recreation?**
> **Command:** `podman exec -it <c> bash`
> **Explanation:** Changes do NOT survive recreation; they only exist in the ephemeral read-write layer.

**Q12. Show processes and live resource usage.**
> **Command:** `podman top <c>` and `podman stats <c>`
> **Explanation:** `top` shows internal container processes; `stats` shows live host resource usage.

**Q13. Run container with -rm.**
> **Command:** `podman run --rm alpine`
> **Explanation:** Auto-cleans the container on exit, but you lose logs for debugging.

**Q14. Bind-mount directory read-only.**
> **Command:** `podman run -v ./dir:/app:ro alpine`
> **Explanation:** `:ro` enforces read-only access, rejecting internal modification attempts.

**Q15. Add custom-labels, list matches.**
> **Command:** `podman run --label env=test alpine` -> `podman ps --filter label=env=test`
> **Explanation:** Labels add metadata; `--filter` isolates containers with those exact labels.

**Q16. Commit a modified container.**
> **Command:** `podman commit <c> new_image:v1`
> **Explanation:** Freezes the read-write layer into a permanent, reusable image.

**Q17. Show filesystem changes (A/C/D).**
> **Command:** `podman diff <c>`
> **Explanation:** A = Added, C = Changed, D = Deleted files relative to the base image.

**Q18. Run with --network none.**
> **Command:** `podman run --network none alpine`
> **Explanation:** Creates a container with only a loopback interface for isolated/secure processing.

**Q19. Publish port to specific host IP.**
> **Command:** `podman run -p 127.0.0.1:8082:80 nginx`
> **Explanation:** Restricts access to localhost only, blocking external network requests.

**Q20. Use podman port.**
> **Command:** `podman port <c>`
> **Explanation:** Neatly lists all port mappings without needing to parse the full `inspect` JSON.

**Q21. Run as non-root user.**
> **Command:** `podman run --user 1000 alpine id`
> **Explanation:** Drops root privileges inside the container, mapping processes to the specified UID.

**Q22. Generate systemd unit.**
> **Command:** `podman generate systemd --name <c>` (or use Quadlet)
> **Explanation:** Creates `.service` files so the host OS can auto-start containers on boot.

**Q23. Observe lifecycle events.**
> **Command:** `podman events`
> **Explanation:** A live listener that records discrete state changes (start, die, stop).

---

## LO2 - Managing and creating container images

**Q1. ARG for base-image tag.**
> **Command:** `ARG VER` / `FROM alpine:${VER}` -> `podman build --build-arg VER=3.18 .`
> **Explanation:** Injects variables dynamically at build time to change the base image.

**Q2. Multi-stage build.**
> **Command:** Build in one `FROM`, copy binary to second `FROM`.
> **Explanation:** Compiles code and copies only the result, drastically reducing final image size.

**Q3. Add .containerignore.**
> **Command:** Create `.containerignore` file.
> **Explanation:** Prevents unnecessary local files from bloating the build context sent to the daemon.

**Q4. Apply three tags at once.**
> **Command:** `podman build -t app:1.0 -t app:1 -t app:latest .`
> **Explanation:** Applies multiple aliases pointing to the same image digest.

**Q5. Inspect layer history.**
> **Command:** `podman history <image>`
> **Explanation:** Shows layer creation; combine `RUN` commands with `&&` to reduce layer count.

**Q6. Non-root USER and writable WORKDIR.**
> **Command:** `WORKDIR /app` / `USER 1000`
> **Explanation:** `USER` changes execution identity; `WORKDIR` sets the default execution directory.

**Q7. Add HEALTHCHECK.**
> **Command:** `HEALTHCHECK CMD curl -f http://localhost/ || exit 1`
> **Explanation:** Periodically tests app readiness, reporting status natively in `podman ps`.

**Q8. ENTRYPOINT vs CMD.**
> **Command:** `ENTRYPOINT ["ping"]` + `CMD ["localhost"]`
> **Explanation:** `ENTRYPOINT` is the fixed executable; `CMD` acts as default, overridable arguments.

**Q9. ADD vs COPY.**
> **Command:** `COPY file /` vs `ADD archive.tar /`
> **Explanation:** `ADD` can extract tarballs/URLs; `COPY` strictly copies local files securely.

**Q10. Save and restore image.**
> **Command:** `podman save -o img.tar app` / `podman load -i img.tar`
> **Explanation:** Converts images to files for offline/air-gapped transfers.

**Q11. Pull by digest.**
> **Command:** `podman pull app@sha256:...`
> **Explanation:** Guarantees pulling the exact same image bytes, avoiding overwritten tag issues.

**Q12. Build with --no-cache.**
> **Command:** `podman build --no-cache .`
> **Explanation:** Forces rebuilding all layers; useful for pulling the latest OS security updates.

**Q13. Build from non-default Containerfile.**
> **Command:** `podman build -f custom.file .`
> **Explanation:** Specifies a custom manifest filename while keeping the standard build context.

**Q14. Use COPY --chown.**
> **Command:** `COPY --chown=user:group ./src /app`
> **Explanation:** Sets permissions during the copy, avoiding a size-bloating `RUN chown` layer.

**Q15. ARG vs ENV.**
> **Command:** `ARG build_var` vs `ENV run_var`
> **Explanation:** `ARG` only exists during the build; `ENV` persists in the running container.

**Q16. Login and push.**
> **Command:** `podman login registry.com` -> `podman push image registry.com/image`
> **Explanation:** Generates an auth token stored in `~/.config/containers/auth.json`.

**Q17. Remove dangling images.**
> **Command:** `podman image prune`
> **Explanation:** Deletes untagged, orphaned layers to free up disk space.

**Q18. Python static file server.**
> **Command:** `COPY . /web` / `WORKDIR /web` / `CMD ["python", "-m", "http.server", "80"]`
> **Explanation:** Packages and serves a static HTML directory over HTTP.

**Q19. Install specific pinned version.**
> **Command:** `RUN apk add --no-cache curl=8.5.0-r0`
> **Explanation:** Pinning prevents unexpected upstream updates from breaking the build.

**Q20. Inspect unknown image.**
> **Command:** `podman image inspect <image>`
> **Explanation:** Reveals internal metadata (Entrypoint, Ports) to help you run it correctly.

**Q21. Multi-line RUN cleanup.**
> **Command:** `RUN apt install -y pkg && apt clean`
> **Explanation:** Cleaning caches in the *same* layer prevents junk from being saved in the image.

**Q22. Tag and push to two registries.**
> **Command:** `podman push <image> docker.io/...` / `podman push <image> quay.io/...`
> **Explanation:** Image mirroring ensures high availability if one registry goes down.

**Q23. Security value of minimal base image.**
> **Command:** Use `alpine` or `distroless`.
> **Explanation:** Minimal images have fewer tools (like shells), reducing the attack surface.

---

## LO3 - Application delivery, network architecture & security

**Q1. Pod with 2 containers.**
> **Command:** `podman pod create -p 8080:80` -> add containers to pod.
> **Explanation:** Pods share a network namespace; containers inside communicate via `localhost`.

**Q2. Custom network DNS.**
> **Command:** `podman network create net1`
> **Explanation:** Custom networks have a DNS resolver; apps can ping databases by container name.

**Q3. Deploy two-tier stack.**
> **Command:** Use `compose.yaml` with an App and a DB.
> **Explanation:** Orchestrates multiple containers connected to the same shared network.

**Q4. Persist data in named volume.**
> **Command:** `podman run -v db_data:/var/lib/postgresql/data ...`
> **Explanation:** Named volumes store data on the host, surviving container deletion.

**Q5. Use podman secret.**
> **Command:** `podman secret create db_pass ...` -> `podman run --secret db_pass ...`
> **Explanation:** Injects passwords securely, keeping them out of plain-text inspect logs.

**Q6. podman compose up.**
> **Command:** `podman compose up -d`
> **Explanation:** Declaratively provisions networks, volumes, and services in the background.

**Q7. Internal DB network in compose.**
> **Command:** Omit `ports:` for the DB service in yaml.
> **Explanation:** Keeps the database strictly isolated on the internal bridge network.

**Q8. Compose healthcheck and depends_on.**
> **Command:** `depends_on: db: condition: service_healthy`
> **Explanation:** App container waits until the DB is fully initialized and reporting healthy.

**Q9. env_file in compose.**
> **Command:** `env_file: - ./db.env`
> **Explanation:** Centralizes variables externally, keeping the compose file clean and secure.

**Q10. Scale compose service.**
> **Command:** `podman compose up --scale app=3`
> **Explanation:** Spins up identical replicas; compose network load-balances requests across them.

**Q11. Bind mount vs Named volume.**
> **Command:** Bind for `./config.conf`, Volume for `db_data`.
> **Explanation:** Bind mounts inject local config files; volumes provide optimized persistent data storage.

**Q12. Backup named volume to tarball.**
> **Command:** Run a temp container mounting the volume and host dir, run `tar cvf /host/bkp.tar /vol`.
> **Explanation:** Archives persistent data for migration or backup.

**Q13. Nginx reverse-proxy container.**
> **Command:** Proxy exposes 80, forwards to internal app name.
> **Explanation:** A single entry point routes host traffic to isolated internal containers.

**Q14. Database-admin container.**
> **Command:** Add `adminer` to the same network.
> **Explanation:** Provides a UI to interact with the database without exposing the DB port publicly.

**Q15. Generate Kubernetes YAML.**
> **Command:** `podman generate kube <pod>`
> **Explanation:** Translates local pod configurations into standard K8s manifests.

**Q16. Run pod from K8s YAML.**
> **Command:** `podman kube play file.yaml`
> **Explanation:** Natively interprets and runs K8s manifests locally without a cluster.

**Q17. CPU/memory limits in compose.**
> **Command:** Use `deploy: resources: limits:` in compose.yaml.
> **Explanation:** Restricts resources natively, visible via `podman stats`.

**Q18. Pod namespaces (network, IPC, PID).**
> **Command:** `podman pod inspect <pod>`
> **Explanation:** Pods share Network/IPC, but maintain isolated PID namespaces by default.

**Q19. Name-based discovery failure on default network.**
> **Command:** Create custom network and attach both containers.
> **Explanation:** The default bridge network lacks DNS; custom networks resolve names automatically.

**Q20. Attach single container to two networks.**
> **Command:** `podman network connect net2 <c>`
> **Explanation:** Allows a container to act as a bridge between an isolated backend and frontend.

**Q21. Compose logs.**
> **Command:** `podman compose logs -f`
> **Explanation:** Aggregates stdout from all services for multi-stack troubleshooting.

**Q22. Shared read-write storage risk.**
> **Command:** Two replicas mounting the same volume.
> **Explanation:** Risks severe data corruption if the application doesn't handle file locking.

**Q23. podman compose down.**
> **Command:** `podman compose down`
> **Explanation:** Removes containers and networks, but preserves volumes by default to protect data.

---

## LO4 - Accelerated delivery of multilayer applications (K8s)

**Q1. Imperative Deployment creation + export YAML.**
> **Command:** `kubectl create deploy web --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml > d.yaml`
> **Explanation:** Generates a base manifest locally without sending it to the API.

**Q2. DockerHub auth secret.**
> **Command:** Create a `kubernetes.io/dockerconfigjson` Secret.
> **Explanation:** Stores registry credentials so the kubelet can pull private images.

**Q3. Scale Deployment.**
> **Command:** `kubectl scale deploy web --replicas=5`
> **Explanation:** Updates the Deployment, which updates the underlying ReplicaSet.

**Q4. Rolling update + rollout status.**
> **Command:** `kubectl set image deploy/web nginx=nginx:1.27` -> `kubectl rollout status deploy/web`
> **Explanation:** Gradually replaces old pods with new ones, ensuring zero downtime.

**Q5. Rollout history and undo.**
> **Command:** `kubectl rollout history deploy/web` / `kubectl rollout undo deploy/web`
> **Explanation:** Reverts to a previous revision. `CHANGE-CAUSE` tracks the command used.

**Q6. maxSurge: 1 and maxUnavailable: 0.**
> **Command:** Define in strategy spec.
> **Explanation:** Guarantees 100% capacity; new pods must be ready before old ones terminate.

**Q7. Recreate strategy.**
> **Command:** `strategy: type: Recreate`
> **Explanation:** Kills all old pods first. Required for legacy apps requiring exclusive DB locks.

**Q8. Requests and limits.**
> **Command:** Define `resources: requests/limits` in container spec.
> **Explanation:** Requests guarantee node capacity; limits cap max usage.

**Q9. revisionHistoryLimit.**
> **Command:** `revisionHistoryLimit: 3`
> **Explanation:** Retains only 3 old ReplicaSets to allow rollbacks without cluttering etcd.

**Q10. Bad image tag rollout.**
> **Command:** Set image to non-existent tag.
> **Explanation:** Rollout halts in ImagePullBackOff. Old pods keep serving traffic safely.

**Q11. Label selectors Deployment -> Pod.**
> **Command:** `kubectl get pods -l app=web`
> **Explanation:** Deployments and ReplicaSets strictly manage Pods based on matching labels.

**Q12. kubectl expose.**
> **Command:** `kubectl expose deploy web --port=80`
> **Explanation:** Creates a Service matching the exact pod selectors of the Deployment.

**Q13. Sidecar container.**
> **Command:** Add second container to pod spec.
> **Explanation:** Both containers share the same localhost network and can mount the same volumes.

**Q14. nodeSelector.**
> **Command:** `nodeSelector: disktype: ssd`
> **Explanation:** Pod stays Pending if no cluster node matches the required label.

**Q15. Bare pod vs Deployment pod.**
> **Command:** `kubectl delete pod <bare-pod>`
> **Explanation:** Bare pods disappear forever. Deployment pods are instantly recreated.

**Q16. StatefulSet + Headless Service.**
> **Command:** Deploy StatefulSet.
> **Explanation:** Provides sticky pod identities (pod-0, pod-1) and stable internal DNS records.

**Q17. DaemonSet.**
> **Command:** Deploy DaemonSet.
> **Explanation:** Ensures exactly one pod instance runs on *every* eligible node.

**Q18. Job.**
> **Command:** Deploy Job.
> **Explanation:** Executes a finite task to completion, then stops.

**Q19. CronJob.**
> **Command:** Deploy CronJob.
> **Explanation:** Triggers Jobs on a schedule. Can be paused with `suspend: true`.

**Q20. Run Job from CronJob.**
> **Command:** `kubectl create job --from=cronjob/my-cron test-run`
> **Explanation:** Triggers the task immediately for testing.

**Q21. StatefulSet startup order.**
> **Command:** Observe pod creation.
> **Explanation:** Starts sequentially (pod-0 -> pod-1), unlike Deployments which spin up in parallel.

**Q22. emptyDir.**
> **Command:** Mount `emptyDir` on two containers in one pod.
> **Explanation:** Creates a shared scratchpad directory on the node.

**Q23. List SC, PV, PVC.**
> **Command:** `kubectl get sc,pv,pvc`
> **Explanation:** Displays storage classes, physical volumes, and volume claims.

**Q24. ConfigMap as Volume.**
> **Command:** Mount ConfigMap.
> **Explanation:** Injects each ConfigMap key as a distinct configuration file.

**Q25. ConfigMap subPath.**
> **Command:** Use `subPath: file.conf`.
> **Explanation:** Mounts a single file without erasing the target folder, but loses live updates.

**Q26. Secret as Volume.**
> **Command:** Mount Secret.
> **Explanation:** Securely mounts decoded data as in-memory `tmpfs` files with restrictive permissions.

**Q27. StatefulSet PVCs.**
> **Command:** Delete StatefulSet.
> **Explanation:** Generates unique PVCs per pod that persist after deletion to prevent data loss.

**Q28. initContainer.**
> **Command:** Define `initContainers`.
> **Explanation:** Runs completely before the main app starts, ideal for data pre-population.

**Q29. readOnly volume mount.**
> **Command:** `readOnly: true`
> **Explanation:** Enforces filesystem immutability; writes are rejected by the kernel.

**Q30. emptyDir sizeLimit.**
> **Command:** `emptyDir: sizeLimit: 100Mi`
> **Explanation:** If exceeded, the kubelet evicts the pod to protect node disk space.

**Q31. Generic Secret.**
> **Command:** `kubectl create secret generic auth --from-literal=u=admin`
> **Explanation:** Secrets are just base64-encoded strings, they are NOT encrypted by default.

**Q32. Secret from file.**
> **Command:** `kubectl create secret generic certs --from-file=cert.pem`
> **Explanation:** The filename becomes the secret key.

**Q33. TLS Secret.**
> **Command:** `kubectl create secret tls cert --cert=c.pem --key=k.pem`
> **Explanation:** Enforces `tls.crt`/`tls.key` format, directly consumed by Ingress controllers.

**Q34. Secret Volume vs Env Var.**
> **Command:** N/A
> **Explanation:** Volumes are safer; environment variables easily leak into crash logs.

**Q35. envFrom secretRef.**
> **Command:** `envFrom: - secretRef: name: my-secret`
> **Explanation:** Bulk-injects all keys of a secret as environment variables.

**Q36. imagePullSecrets.**
> **Command:** `imagePullSecrets: - name: registry-key`
> **Explanation:** Supplies credentials required to pull from private registries.

**Q37. Selective Secret mount.**
> **Command:** Use `items:` in volume projection.
> **Explanation:** Exposes only specific keys to the pod, enforcing least privilege.

**Q38. ClusterIP DNS.**
> **Command:** `nslookup <svc>.<ns>.svc.cluster.local`
> **Explanation:** Internal DNS resolves the service name across the cluster.

**Q39. NodePort Service.**
> **Command:** `kubectl expose deploy web --type=NodePort`
> **Explanation:** Opens a static high port on every node's IP.

**Q40. LoadBalancer Service (Minikube).**
> **Command:** `minikube tunnel`
> **Explanation:** Minikube lacks a real cloud LB, the tunnel simulates the external IP locally.

**Q41. Headless Service.**
> **Command:** `clusterIP: None`
> **Explanation:** Bypasses proxy VIP, returning direct A records for backing pods.

**Q42. Endpoints / EndpointSlice.**
> **Command:** `kubectl get endpoints`
> **Explanation:** Dynamically tracks the IPs of 'Ready' pods matching the service selector.

**Q43. Cross-namespace access.**
> **Command:** Use FQDN `web.ns1.svc.cluster.local`.
> **Explanation:** Short names only search the local namespace domain.

**Q44. port-forward Pod vs Service.**
> **Command:** `kubectl port-forward svc/web 8080:80`
> **Explanation:** Service load-balances; Pod connects strictly to that exact instance.

**Q45. port vs targetPort vs nodePort.**
> **Command:** N/A
> **Explanation:** port = Service IP port, targetPort = Pod port, nodePort = Host node port.

**Q46. Debug pod for connectivity.**
> **Command:** `kubectl run tmp --rm -it --image=busybox -- sh`
> **Explanation:** Creates an isolated environment to execute `wget`/`nc` tests.

**Q47. Service across two Deployments.**
> **Command:** Give both deployments the same label.
> **Explanation:** Services route to *any* pod matching the selector, regardless of the controller.

**Q48. Service connectivity diagnosis flow.**
> **Command:** N/A
> **Explanation:** Check: Pod Ready? -> Labels match? -> Endpoints populated? -> DNS resolves? -> TargetPort correct?

**Q49. Expose NodePort.**
> **Command:** `kubectl expose deploy web --type=NodePort`
> **Explanation:** Opens a cluster-wide port for immediate external testing.

**Q50. Liveness Probe.**
> **Command:** Define `livenessProbe`.
> **Explanation:** Pings an endpoint; restarts the pod automatically upon failure.

**Q51. Readiness Probe.**
> **Command:** Define `readinessProbe: tcpSocket`.
> **Explanation:** Blocks service traffic routing until the TCP socket connects.

**Q52. Startup Probe.**
> **Command:** Define `startupProbe`.
> **Explanation:** Delays liveness checks to give slow legacy applications time to boot.

**Q53. Default probe behavior.**
> **Command:** N/A
> **Explanation:** Without probes, Kubernetes assumes a container is fully ready the moment PID 1 starts.

---

## LO5 - Solve problems with application shipping

**Q1. `podman run -p 80:8080 nginx` -> Unreachable.**
> **Error:** Port mapping reversed.
> **Fix:** Use `-p 8080:80` (HostPort:ContainerPort).

**Q2. `podman run -d mysql` -> Exits during init.**
> **Error:** Missing required environment variables.
> **Fix:** Add `-e MYSQL_ROOT_PASSWORD=secret`.

**Q3. `podman run --network host -p 8080:80` -> -p ignored.**
> **Error:** `--network host` overtakes the network stack.
> **Fix:** Isolated port mappings are ineffective and ignored.

**Q4. `podman run -d busybox` -> Exited (0).**
> **Error:** No long-running command.
> **Fix:** Add a persistent command like `sleep infinity`.

**Q5. `podman run --rm -d alpine echo hello` -> logs fail.**
> **Error:** `--rm` deletes the container immediately upon exit.
> **Fix:** Remove `--rm` if you need to read logs after completion.

**Q6. `--memory 8m mysql` -> Unhealthy.**
> **Error:** Memory limit too low (OOMKill).
> **Fix:** Increase to at least `--memory 512m`.

**Q7. Name resolution fails between 'a' and 'b'.**
> **Error:** Default bridge network lacks DNS.
> **Fix:** Create and attach to a user-defined network.

**Q8. Volume mount empty / SELinux denies access.**
> **Error:** Missing SELinux context.
> **Fix:** Add `:Z` to the mount (`-v ./dir:/dir:Z`).

**Q9-10. `RUN apt-get install nginx` fails.**
> **Error:** Empty local package cache.
> **Fix:** Chain commands: `RUN apt-get update && apt-get install -y nginx`.

**Q11. `CMD python app.py` -> Doesn't handle signals.**
> **Error:** Shell form blocks signals.
> **Fix:** Use exec array form: `CMD ["python", "app.py"]`.

**Q12. `COPY . .` then `npm install` -> Slow builds.**
> **Error:** Source changes invalidate the install cache.
> **Fix:** `COPY package.json .` -> `RUN npm install` -> `COPY . .`.

**Q13. `ENV PATH=/app/bin` -> curl not found.**
> **Error:** Overwrote system path.
> **Fix:** Append instead: `ENV PATH=$PATH:/app/bin`.

**Q14-15. `EXPOSE 8080` but app on 3000.**
> **Error:** EXPOSE is just documentation.
> **Fix:** The app listens on 3000, map ports accordingly.

**Q16. `apt-get install` -> Huge image.**
> **Error:** Apt cache left in layer.
> **Fix:** Append `&& apt-get clean && rm -rf /var/lib/apt/lists/*`.

**Q17. `USER appuser` -> `COPY` permission denied.**
> **Error:** Copies run as root.
> **Fix:** Use `COPY --chown=appuser:appuser`.

**Q18. `go build` -> 1GB image.**
> **Error:** Toolchain left in image.
> **Fix:** Use a multi-stage build.

**Q19. Pod stuck Pending.**
> **Error:** Unmet scheduling constraints.
> **Fix:** Check `describe pod` for insufficient resources or missing PVCs.

**Q20. YAML deploy fails after removing spaces.**
> **Error:** YAML syntax error.
> **Fix:** Restore strict indentation.

**Q21. ImagePullBackOff.**
> **Error:** Kubelet can't pull image.
> **Fix:** Check spelling, tag, or add `imagePullSecrets`.

**Q22. CrashLoopBackOff.**
> **Error:** App crashes continuously.
> **Fix:** Read logs of dead container: `kubectl logs --previous <pod>`.

**Q23. OOMKilled.**
> **Error:** Exceeded memory limit.
> **Fix:** Increase `resources.limits.memory`.

**Q24. Pods never Ready.**
> **Error:** Readiness probe fails.
> **Fix:** Correct the probe path/port.

**Q25. Service returns nothing.**
> **Error:** Endpoints empty due to selector mismatch.
> **Fix:** Match Service `selector` to Pod `labels`.

**Q26. matchLabels vs metadata.labels mismatch.**
> **Error:** Deployment API validation fails.
> **Fix:** Ensure both label blocks match exactly.

**Q27. requests exceed node capacity.**
> **Error:** Insufficient CPU/Memory.
> **Fix:** Lower requests or add a larger node.

**Q28. Pod mounts non-existent PVC.**
> **Error:** FailedMount / Pending.
> **Fix:** Create the corresponding PVC.

**Q29. Ubuntu container in CrashLoopBackOff.**
> **Error:** Base image exits naturally.
> **Fix:** Add `command: ["sleep", "infinity"]`.

**Q30. Triage pod events.**
> **Command:** `kubectl get events --sort-by=.lastTimestamp`
> **Explanation:** Sorts cluster events chronologically.

**Q31. Debug DNS in pod.**
> **Command:** `kubectl exec -it <pod> -- cat /etc/resolv.conf`
> **Explanation:** Inspects internal DNS configuration.

**Q32. Debug distroless container.**
> **Command:** `kubectl debug -it <pod> --image=busybox --target=<c>`
> **Explanation:** Injects debugging tools into an ephemeral container.

**Q33. Throwaway debug pod.**
> **Command:** `kubectl run tmp --rm -it --image=busybox -- sh`
> **Explanation:** Isolated pod for cluster network testing.

**Q34. Node NotReady.**
> **Command:** `kubectl describe node`
> **Explanation:** Check for DiskPressure or inspect host kubelet logs.

**Q35. Pod stuck Terminating.**
> **Command:** `kubectl delete pod <pod> --grace-period=0 --force`
> **Explanation:** Bypasses finalizers (Risk: leaves orphaned resources).

**Q36. Wrong apiVersion/kind.**
> **Error:** Schema validation error.
> **Fix:** e.g., Pod must use `v1`, not `apps/v1`.

**Q37. CreateContainerConfigError.**
> **Error:** Missing ConfigMap key.
> **Fix:** Add the required key to the referenced ConfigMap.

**Q38. Pod mounts Secret from different namespace.**
> **Error:** Secrets are namespace-scoped.
> **Fix:** Duplicate the Secret into the Pod's namespace.

**Q39. Progress Deadline Exceeded.**
> **Error:** Rollout timed out.
> **Fix:** Use `describe deploy` to find why pods aren't becoming Ready.

**Q40. Catch typos before applying.**
> **Command:** `kubectl apply -f file.yaml --dry-run=server`
> **Explanation:** Validates syntax against the API safely.

**Q41. App can't reach DB Service.**
> **Action:** Verify flow: Pod Ready -> Labels match -> Endpoints populated -> DNS resolves.

**Q42. Read container exit code.**
> **Command:** `kubectl get pod <pod> -o jsonpath='{.status.containerStatuses[0].lastState.terminated.exitCode}'`
> **Explanation:** Extracts the exact crash code for debugging.

**Q43. OpenShift Route vs Ingress.**
> **Explanation:** Routes natively provide edge routing and TLS out-of-the-box compared to basic Ingress.

---

## LO6 - Evaluate the use of selected container orchestration systems

**Q1. K8s vs Docker network.**
> **Explanation:** K8s enforces a flat network where every pod gets a unique IP and can talk to any other pod. Docker defaults to isolated bridge networks.

**Q2. K8s storage vs Podman.**
> **Explanation:** K8s (CSI, dynamic provisioning) is built for distributed, stateful workloads across multiple nodes. Podman relies on local plugins.

**Q3. Operator pattern vs manifests.**
> **Explanation:** Operators automate Day-2 tasks (backups, scaling) for stateful apps but require coding effort; manifests are simple but static.

**Q4. Plain K8s vs OpenShift S2I.**
> **Explanation:** S2I optimizes for developer speed (source code straight to image). Plain K8s requires manual Dockerfile builds and CI pipelines.

**Q5. Migrating OpenShift to K8s.**
> **Explanation:** Requires replacing OpenShift-specific `Routes` with `Ingresses`, and replacing strict SCCs (Security Context Constraints) with PodSecurity.

**Q6. CI/CD in K8s vs VMs.**
> **Explanation:** K8s offers ephemeral, auto-scaling runners but risks "noisy neighbor" resource starvation. VMs offer hard isolation but scale slowly.

**Q7. K8s Cloud integration.**
> **Explanation:** The Cloud Controller Manager dynamically interacts with AWS/GCP to provision real LoadBalancers and block storage (EBS).

**Q8. OpenShift security vs vanilla K8s.**
> **Explanation:** OpenShift enforces strict SCCs by default (blocking root containers). Enterprises choose it for this out-of-the-box compliance.

**Q9. OpenShift learning curve.**
> **Explanation:** OpenShift's added abstractions (Routes, ImageStreams) can confuse K8s purists, but simplify workflows for developers.

**Q10. Full K8s vs k3s.**
> **Explanation:** k3s is a lightweight, single-binary distribution (using SQLite) ideal for IoT/Edge. Full K8s is overkill for small on-prem setups.

**Q11. Docker Compose vs K8s.**
> **Explanation:** Compose is perfect for simple, single-node apps. K8s is needed for multi-node scaling, self-healing, and zero-downtime rollouts.

**Q12. Vendor lock-in.**
> **Explanation:** K8s is open-source (CNCF) and portable across clouds. OpenShift is Red Hat specific, trading portability for enterprise support.

**Q13. Why recommend OpenShift?**
> **Explanation:** For companies needing an integrated PaaS with built-in monitoring, registry, CI/CD, and strict security without building it themselves.

**Q14. Storage K8s vs VMs.**
> **Explanation:** K8s dynamically provisions storage via PVCs and StorageClasses. VMs usually require manual LUN assignment by admins.

**Q15. Startup with 2 engineers.**
> **Explanation:** Recommend Managed K8s (EKS/GKE) or PaaS (Render/Heroku) to minimize operational overhead and focus on product.

**Q16. Docker vs Podman architecture.**
> **Explanation:** Podman is daemonless and rootless by default, eliminating a central point of failure and drastically improving security.

**Q17. Self-managed vs Managed K8s.**
> **Explanation:** Managed K8s abstracts Control Plane upgrades and etcd backups. Self-managed requires a dedicated operations team.

**Q18. Media streaming solution.**
> **Explanation:** Recommend K8s due to HPA (Horizontal Pod Autoscaler) handling spiky traffic dynamically across cloud nodes.

**Q19. Migrating from Compose
