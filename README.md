# Intro to DevOps - Practice Questions & Answers

This repository contains the solutions for the Intro to DevOps practice questions (LO1 to LO5). Each answer includes the necessary command or explanation, followed by a brief English summary.

---

## [cite_start]LO1 - Use of containers and container services [cite: 13]

* **1. [cite_start]Run an httpd container detached, publish container port 80 to host port 8081, give it a custom --name and a custom-hostname, and confirm both with podman inspect.** [cite: 14]
  `podman run -d --name web --hostname webhost -p 8081:80 httpd`. The `-d` flag runs it detached, `-p` maps ports, and you use `podman inspect web` to confirm the identifiers.

* **2. [cite_start]Run a container with --restart=always, kill its main process, and prove podman restarts it (check podman ps and the restart behavior).** [cite: 15]
  `podman kill <container>`. The container runtime automatically detects the process exit and restarts it according to the policy.

* **3. [cite_start]Run a container with --memory=256m --cpus $z=0.5$ and verify the limits are applied using podman stats and podman inspect.** [cite: 16]
  `podman run --memory=256m --cpus=0.5 <image>`. Podman applies kernel cgroup limits preventing the container from exceeding these resources.

* **4. [cite_start]Start one container with --env KEY=VALUE and another with --env-file, then show the environment differences with podman exec <c> env.** [cite: 17]
  `--env` passes a single variable while `--env-file` loads multiple from a file, both visible using the `env` command inside the container.

* **5. [cite_start]Create a user-defined bridge network with podman network create, run a container attached to it, and inspect the network to find the container's IP.** [cite: 18]
  `podman network create mynet`. Custom bridge networks provide internal IPs discoverable via `podman network inspect`.

* **6. [cite_start]Pause and unpause a running container and explain what state its processes are in while paused.** [cite: 19]
  `podman pause <c>`. Paused processes are suspended by the Linux cgroup freezer and consume zero CPU cycles.

* **7. [cite_start]Rename an existing container with podman rename and confirm the change.** [cite: 20]
  `podman rename old new`. This safely changes the local reference name without restarting the container.

* **8. [cite_start]Use podman logs with --tail, --since, and -f, and explain what each flag does.** [cite: 21]
  `--tail` limits line count, `--since` filters by timestamp, and `-f` streams the logs continuously.

* **9. [cite_start]Extract a single field (e.g. the IP address or restart count) using podman inspect--format '{{ ... }}' Go- template syntax.** [cite: 22]
  `podman inspect --format '{{.NetworkSettings.IPAddress}}' <c>`. Go-templates parse and extract specific JSON values efficiently.

* **10. [cite_start]Copy a file from the host into a running container and back out again with podman cp.** [cite: 23]
  `podman cp file <c>:/path` and `podman cp <c>:/path file`. This copies data directly across the container boundary.

* **11. [cite_start]Exec an interactive shell into a running container, install a package, and explain whether that change survives the container being recreated.** [cite: 24]
  Interactive package installations do not survive recreation because they only exist in the ephemeral read-write layer.

* **12. [cite_start]Use podman top and podman stats to show the processes and live resource usage of a container.** [cite: 25]
  `podman top` displays running processes inside the container, while `podman stats` streams live CPU and memory usage.

* **13. [cite_start]Run a container with -rm, and explain when auto-removal is useful and what you lose by using it.** [cite: 26]
  `--rm` auto-cleans the container on exit, which is great for one-off tasks but destroys logs for post-mortem debugging.

* **14. [cite_start]Bind-mount a host directory into a container read-only (-v hostdir:/path:ro) and prove that writes from inside are rejected.** [cite: 27, 28]
  `-v hostdir:/path:ro` enforces read-only access, rejecting any internal modification attempts by the container.

* **15. [cite_start]Add custom-labels to two containers, then list only the ones matching a label using podman ps --filter.** [cite: 29]
  `podman run --label env=test`. Labels add metadata that can be easily queried using the `--filter` flag.

* **16. [cite_start]Commit a modified running container into a new image with podman commit, then run a container from that image.** [cite: 30]
  `podman commit <c> newimage`. This freezes the current read-write layer into a permanent, reusable image.

* **17. [cite_start]Show the filesystem changes made inside a running container with podman diff and interpret the A/C/D markers.** [cite: 31]
  `podman diff` shows file system changes where A is Added, C is Changed, and D is Deleted.

* **18. [cite_start]Run a container with --network none, demonstrate it has no external connectivity, and explain a use case.** [cite: 32]
  `--network none` creates a completely isolated loopback-only container, ideal for secure data processing.

* **19. [cite_start]Publish a port bound to a specific host IP only (-p 127.0.0.1:8082:80) and verify it is not reachable on the VM's other IPs.** [cite: 33]
  `-p 127.0.0.1:8082:80` restricts the exposed port to the host's localhost loopback, preventing external network access.

* **20. [cite_start]Use podman port to list a container's published ports and cross-check with podman inspect.** [cite: 34]
  `podman port` outputs port mappings quickly without needing to parse the full JSON from inspect.

* **21. [cite_start]Run a container as a non-root user with --user, and verify the effective UID inside with id / whoami.** [cite: 35]
  `--user` drops root privileges inside the container, mapping processes to the specified UID.

* **22. [cite_start]Generate a systemd unit (or a Quadlet.container file) for a container and explain how it makes the container start on boot.** [cite: 36, 37]
  Quadlet or `podman generate systemd` creates unit files allowing the host OS to start containers automatically on boot.

* **23. [cite_start]Run podman events in one terminal while you start/stop a container in another, and describe the lifecycle events you observe.** [cite: 38]
  `podman events` acts as a live listener, recording discrete state changes like start, die, and stop.

---

## [cite_start]LO2 - Managing and creating container images [cite: 41]

* **1. [cite_start]Write a Containerfile that uses an ARG to choose the base-image tag at build time (--build-arg), and build it twice with different values.** [cite: 42]
  Use `ARG base` and `FROM $base`; `--build-arg` injects variables to change the image foundation dynamically at build time.

* **2. [cite_start]Write a multi-stage Containerfile that builds an artifact in one stage and copies only the result into a minimal final image; compare the final image size to a single-stage build.** [cite: 43, 44]
  Multi-stage builds compile code in one stage and copy only the binary to the final stage, drastically reducing image size.

* **3. [cite_start]Add a .containerignore file and demonstrate that ignored files are excluded from the build context / not copied.** [cite: 45]
  A `.containerignore` file prevents unnecessary or sensitive host files from bloating the build context.

* **4. [cite_start]Build an image and apply three tags at once (name: 1.0, name: 1, name:latest); explain the registry/namespace/name:tag convention.** [cite: 46, 47]
  Multiple `-t` flags apply multiple aliases to the same image digest, following the standard registry taxonomy.

* **5. [cite_start]Inspect an image's layer history with podman history and explain how to reduce the number of layers.** [cite: 48]
  `podman history` shows how each command adds a layer; consolidating `RUN` commands with `&&` reduces this layer count.

* **6. [cite_start]Write a Containerfile that sets a non-root USER and a writable WORKDIR; run it and confirm whoami is not root.** [cite: 49, 50]
  `USER` changes the execution identity, while `WORKDIR` sets the default directory for subsequent commands.

* **7. [cite_start]Add a HEALTHCHECK to a Containerfile, run the container, and show the health status in podman ps / via podman healthcheck run.** [cite: 51]
  `HEALTHCHECK` periodically tests application readiness, reporting the status in the container list natively.

* **8. [cite_start]Demonstrate the difference between ENTRYPOINT and CMD in one Containerfile that uses both, then override CMD at podman run time.** [cite: 52]
  `ENTRYPOINT` is the unchangeable executable, while `CMD` acts as default arguments that are easily overridden during `podman run`.

* **9. [cite_start]Explain the difference between ADD and COPY and write a Containerfile that justifies using each.** [cite: 53]
  `ADD` can extract tarballs and download URLs automatically, whereas `COPY` securely transfers local files only.

* **10. [cite_start]Save an image to a tarball with podman save, remove it, then restore it with podman load; explain when you'd do this (air-gapped transfer).** [cite: 54, 55]
  `save` and `load` convert images to tarballs, which is necessary for moving images to offline air-gapped environments.

* **11. [cite_start]Pull an image by digest (name@sha256:...) and explain why it's more reproducible than a tag.** [cite: 56]
  Pulling by `sha256` digest guarantees the exact same image bytes, avoiding unexpected changes if a tag is overwritten.

* **12. [cite_start]Build an image with --no-cache and explain layer caching and when disabling it helps or hurts.** [cite: 57]
  `--no-cache` forces all layers to rebuild, useful when fetching the latest upstream security patches.

* **13. [cite_start]Build from a non-default Containerfile name using -f, and explain the build-context argument.** [cite: 58]
  `-f` specifies a custom manifest filename, while the build context path defines the directory sent to the daemon.

* **14. [cite_start]Use COPY --chown to set file ownership in the image and verify with Is -I inside the container.** [cite: 59]
  `COPY --chown` sets permissions natively during the copy, saving space compared to an extra `RUN chown` layer.

* **15. [cite_start]Write a Containerfile combining ARG and ENV (build-time vs run-time variables) and demonstrate the difference.** [cite: 60, 61]
  `ARG` disappears after the build finishes, while `ENV` persists as environment variables inside the running container.

* **16. podman login to a registry and push an image to a private repository; explain where/how the credentials are stored.** [cite: 62]
  Authentication creates a token stored locally in `~/.config/containers/auth.json` to authorize pushes.

* **17. [cite_start]Remove dangling images with podman image prune and a specific image with podman rmi; explain what "dangling" means.** [cite: 63]
  Dangling images are untagged, orphaned layers; `prune` deletes them to free up disk space.

* **18. [cite_start]Write a Containerfile for a static file server using python -m http.server over a directory you COPY in, expose the port, and run it.** [cite: 64]
  `COPY . /web`, then `CMD ["python", "-m", "http.server", "80"]` serves the static directory.

* **19. [cite_start]Build an image that installs a specific pinned version of a package (apt or apk) and explain why pinning matters for reproducibility.** [cite: 65]
  Pinning package versions prevents upstream updates from breaking the build unexpectedly.

* **20. [cite_start]Inspect an unknown image with podman image inspect to discover its default Entrypoint/Cmd, exposed ports, and env, then run it accordingly.** [cite: 66]
  Inspecting reveals entrypoints and ports, providing the necessary details to formulate the correct run command.

* **21. [cite_start]Write a Containerfile with a multi-line RUN... && ... that installs packages and cleans the package cache in the same layer; explain why cleanup must share the layer.** [cite: 67, 68]
  Cleaning package caches in the same `RUN` layer prevents the cache debris from being permanently baked into the image.

* **22. [cite_start]Tag and push the same image to two different registries (e.g. Docker Hub and Quay) and explain when image mirroring is useful.** [cite: 69]
  Pushing to multiple registries ensures high availability if one registry experiences an outage.

* **23. [cite_start]Run an image and list the OS packages installed inside it; explain the security/size value of choosing a minimal base image.** [cite: 70, 71]
  Minimal base images contain fewer utilities, significantly reducing the attack surface and download times.

---

## LO3 - Application delivery using containers, network architecture, and component security [cite: 73]

* **1. [cite_start]Create a pod with podman pod create (publishing a port), add an app container and a database container to it, and show they reach each other over localhost (shared network namespace).** [cite: 75]
  Pods share a network namespace, allowing containers within them to communicate directly via localhost.

* **2. [cite_start]Create a user-defined network, run an app container and a postgres container on it, and prove the app resolves the database by container name (podman DNS on custom networks).** [cite: 76]
  Custom bridge networks include an internal DNS resolver, allowing containers to connect using their names.

* **3. [cite_start]Deploy a two-tier stack of your choice that is not Drupal/Joomla/WordPress (e.g. Ghost + MySQL, Gitea + PostgreSQL, or Redmine + PostgreSQL) and complete its first-run setup.** [cite: 77]
  Deploying an app with a backend requires placing both on a shared custom network to facilitate communication.

* **4. [cite_start]Persist the database's data in a named volume so it survives podman rm + re-creation of the DB container; prove the data is still there afterward.** [cite: 78, 79]
  Named volumes store data on the host filesystem, entirely bypassing container deletion.

* **5. [cite_start]Use a podman secret (podman secret create + --secret) to pass the database password instead of --env; explain the security benefit.** [cite: 80, 81]
  Secrets inject sensitive data securely, preventing passwords from appearing in plain text in inspect logs.

* **6. [cite_start]Write a podman compose file (compose.yaml) for an app + database, bring it up with podman compose up d, and show both services running.** [cite: 82]
  `podman compose up -d` declaratively provisions all interconnected services defined in the YAML file in the background.

* **7. [cite_start]In that compose file, keep the database on an internal network with no published port and expose only the app; prove the DB isn't reachable from the host.** [cite: 83, 84]
  Omitting the `ports:` mapping keeps the database strictly isolated on the internal network.

* **8. [cite_start]Add a compose healthcheck plus depends_on: condition: service_healthy so the app waits for the database; demonstrate the startup ordering.** [cite: 85]
  `depends_on` delays the app's startup sequence until the database healthcheck reports as healthy.

* **9. [cite_start]Use an env file: in compose for the database credentials instead of inline values.** [cite: 86]
  `env_file:` centralizes environment variables externally, keeping the compose file clean and secure.

* **10. [cite_start]Scale one compose service to multiple replicas (podman compose up --scale app=3) and explain how requests are distributed.** [cite: 87]
  Scaling spins up identical container replicas, with requests load-balanced automatically across them.

* **11. [cite_start]Mount a bind mount for app configuration and a named volume for data in the same container, and explain why you'd use each.** [cite: 88]
  Bind mounts easily inject local config files, while named volumes offer optimized, persistent data storage.

* **12. [cite_start]Back up a named volume to a tarball using a helper alpine/busybox container that mounts the volume, then restore it into a fresh volume.** [cite: 89]
  A temporary container can mount the volume and host directory to `tar` the volume data into an archive.

* **13. [cite_start]Put an nginx reverse-proxy container in front of an app container on a shared network and route host traffic through nginx to the app.** [cite: 90]
  The proxy container maps the host port and forwards traffic to the internal app using its DNS name.

* **14. [cite_start]Add a database-admin container (e.g. adminer or pgadmin) to the network and use it to inspect the database.** [cite: 91]
  Adding an admin container to the same network grants UI access to the otherwise isolated database.

* **15. [cite_start]Generate Kubernetes YAML from a running pod with podman generate kube and explain how it bridges LO3 to LO4.** [cite: 92]
  `podman generate kube` translates local pod setups into Kubernetes manifests for cluster deployment.

* **16. [cite_start]Run a pod from a Kubernetes YAML with podman kube play, then tear it down with podman kube play -- down.** [cite: 93]
  `podman kube play` natively interprets and runs Kubernetes YAML manifests without needing a full cluster.

* **17. [cite_start]Set per-service CPU/memory limits in a compose file and verify them with podman stats.** [cite: 94]
  `deploy.resources` restricts CPU and memory natively in compose, visible in `podman stats`.

* **18. [cite_start]Inspect a pod with podman pod inspect and show which Linux namespaces the containers share (network, IPC) and which they don't (PID by default).** [cite: 95]
  Containers in a pod share network and IPC namespaces by default, but maintain isolated PID namespaces.

* **19. [cite_start]Demonstrate name-based discovery failing when two containers are on different networks, then fix it by attaching them to a common network.** [cite: 96]
  The default network lacks DNS; connecting containers to a user-defined network enables name resolution.

* **20. [cite_start]Attach a single container to two networks (a frontend and a backend) and explain the segmentation benefit.** [cite: 97]
  Dual-attachment allows a single container to bridge two isolated networks securely.

* **21. [cite_start]Use podman compose logs and podman compose ps to observe and troubleshoot a multi-service stack.** [cite: 98]
  `podman compose logs` aggregates stdout from all services, making stack-wide troubleshooting easier.

* **22. [cite_start]Share configuration across two replicas of the same app via a shared named volume, and explain a risk of shared read-write storage.** [cite: 99]
  Sharing read-write volumes between replicas risks data corruption if the application isn't concurrency-safe.

* **23. [cite_start]Tear down a stack with podman compose down, then explain what happens to named volumes and how volumes changes that.** [cite: 100]
  `podman compose down` cleanly removes containers and networks but preserves volumes by default to protect data.

---

## [cite_start]LO4 - Accelerated delivery of multilayer applications using containers [cite: 102]

* **1. [cite_start]Create a Deployment named web running nginx: 1.25 with 3 replicas using a single imperative kubectl create deployment command, then export its generated YAML to a file.** [cite: 104]
  `kubectl create deployment web --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml > dep.yaml` generates the required manifest.

* **2. Enable pulling images from DockerHub as an authenticated user to lower the chance of hitting the pull limit. [cite_start]What kind of resource do you need to create?** [cite: 105, 106]
  You must create a Secret of type `kubernetes.io/dockerconfigjson` (ImagePullSecret) to store registry credentials.

* **3. [cite_start]Scale web from 3 to 5 replicas two different ways and show the ReplicaSet that results. once with kubectl scale, once by editing the manifest** [cite: 107, 108, 109]
  You can scale using `kubectl scale` or by editing the manifest; both update the underlying ReplicaSet identically.

* **4. [cite_start]Perform a rolling update of web from nginx: 1.25 to nginx:1.27 and watch it with kubectl rollout status.** [cite: 110]
  `kubectl set image` updates the pod template, triggering a rolling update tracked by `rollout status`.

* **5. View a Deployment's rollout history and roll back to the previous revision. [cite_start]Explain what the CHANGE- CAUSE column shows and how to populate it.** [cite: 111, 112]
  `CHANGE-CAUSE` is populated by Kubernetes annotations to document the specific reason for a rollout revision.

* **6. [cite_start]Set the update strategy to Rolling Update with maxSurge: 1 and maxUnavailable: 0, and explain the effect on availability during an update.** [cite: 113]
  `maxUnavailable: 0` ensures 100% capacity is maintained by forcing new pods to start before old ones terminate.

* **7. [cite_start]Switch a Deployment's strategy to Recreate and describe a concrete scenario where this is required instead of Rolling Update.** [cite: 114]
  `Recreate` strictly terminates old pods first, necessary for applications with exclusive database locks.

* **8. [cite_start]Add CPU/memory requests and limits to a Deployment's container and verify they appear in the running pod spec.** [cite: 115]
  `requests` guarantee node capacity, while `limits` cap maximum resource usage.

* **9. [cite_start]Set revision HistoryLimit: 3 and explain how it affects your ability to roll back and how many old Replica Sets are kept.** [cite: 116]
  `revisionHistoryLimit` determines how many old ReplicaSets are retained to allow rollbacks.

* **10. [cite_start]Set a Deployment's image to a non-existent tag, observe the rollout get stuck, and explain why the old pods keep serving traffic.** [cite: 117]
  The old pods keep serving traffic because Kubernetes halts the rollout when the new pods enter `ImagePullBackOff`.

* **11. [cite_start]Use a label selector to list only the pods belonging to one Deployment, and explain the Deployment → ReplicaSet Pod selector relationship.** [cite: 118, 119]
  The Deployment uses label selectors to control its ReplicaSet, which in turn uses selectors to manage the Pods.

* **12. [cite_start]Expose a Deployment with kubectl expose, then explain what object was created and how its selector was derived.** [cite: 120]
  `kubectl expose` creates a Service matching the Deployment's identical label selectors.

* **13. [cite_start]Add a sidecar (second) container to a Deployment's pod template and explain how the two containers share the pod network and volumes.** [cite: 121]
  Sidecars run within the same pod, seamlessly sharing localhost and any mounted storage volumes.

* **14. [cite_start]Write a Deployment manifest for httpd:2.4 with 2 replicas, a named container port, and a nodeSelector; explain why it stays Pending if no node matches.** [cite: 122, 123]
  `nodeSelector` strictly requires node labels; it stays Pending because the scheduler finds no exact match.

* **15. [cite_start]Create a bare Pod (no controller) running busybox that sleeps, then explain what happens when you delete it versus a Deployment-managed pod.** [cite: 124]
  Bare pods are unmanaged and disappear upon deletion, whereas controllers instantly replace deleted managed pods.

* **16. [cite_start]Write a StatefulSet for redis:7 with 3 replicas plus a headless Service, and show the stable pod names and per-pod DNS records.** [cite: 125]
  StatefulSets provide sticky, sequential identities and unique DNS records via headless services.

* **17. [cite_start]Create a DaemonSet running a busybox agent and explain why exactly one pod runs per node.** [cite: 126]
  DaemonSets bypass standard replica counts to guarantee one instance runs on every eligible cluster node.

* **18. [cite_start]Create a Job using busybox/perl that computes something once and completes; show COMPLETIONS and read the result from logs.** [cite: 127]
  Jobs execute a finite task to completion and terminate, leaving logs available for review.

* **19. [cite_start]Create a CronJob that prints the date every minute; show how to suspend it and how to list the Jobs it spawns.** [cite: 128, 129]
  CronJobs execute Jobs on a schedule and can be paused by toggling the `suspend` field.

* **20. [cite_start]Manually run a Job from an existing CronJob (kubectl create job--from=cronjob/...) to test it on demand.** [cite: 130]
  Manually triggering a Job from a CronJob bypasses the schedule for immediate testing.

* **21. [cite_start]Show that a StatefulSet creates and terminates pods in order, and contrast this with a Deployment.** [cite: 131]
  StatefulSets scale incrementally and sequentially, unlike Deployments which process pods in parallel.

* **22. Create a multi-container Pod where two containers share an emptyDir one writes a file, the other reads it. [cite_start]Prove it's shared.** [cite: 132, 133]
  An `emptyDir` mount acts as a shared scratchpad directory between containers in the same pod.

* **23. [cite_start]List the Storage Classes, PVs and PVCs.** [cite: 134]
  `kubectl get sc,pv,pvc` provides a complete view of storage classes, persistent volumes, and their claims.

* **24. [cite_start]Mount a ConfigMap as a volume so each key becomes a file, and verify the contents inside the pod.** [cite: 136]
  ConfigMaps mounted as volumes dynamically inject each key as a separate configuration file.

* **25. [cite_start]Mount a single ConfigMap key to a specific path using subPath; explain when it's needed and its update caveat.** [cite: 137]
  `subPath` mounts specific files without clearing the target directory, but loses automatic live updates.

* **26. [cite_start]Mount a Secret as a volume and verify the files contain decoded values with restrictive permissions.** [cite: 138]
  Secrets are injected safely as in-memory `tmpfs` files with restrictive default permissions.

* **27. [cite_start]Show that scaling a StatefulSet creates one PVC per replica, then explain what happens to those PVCs when the StatefulSet is deleted.** [cite: 139]
  StatefulSets generate unique PVCs per pod that intentionally persist after the StatefulSet is deleted to prevent data loss.

* **28. [cite_start]Use an initContainer to pre-populate data into a shared volume before the main container reads it.** [cite: 140]
  InitContainers execute completely before application containers, making them perfect for data pre-population.

* **29. [cite_start]Set readOnly: true on a volume mount and prove writes are rejected.** [cite: 141]
  `readOnly: true` enforces immutability at the mount level, blocking any write operations.

* **30. [cite_start]Set a sizeLimit on an emptyDir and explain what happens if the container exceeds it.** [cite: 142]
  If an `emptyDir` exceeds its `sizeLimit`, the kubelet evicts the pod to protect node stability.

* **31. [cite_start]Create a generic Secret from literals for a DB username/password, then show the stored values are base64- encoded, not encrypted.** [cite: 143]
  Standard Secrets offer no encryption at rest by default; they are merely base64-encoded strings.

* **32. [cite_start]Create a Secret from files (--from-file) holding a certificate and key, and identify the resulting keys.** [cite: 144]
  Using `--from-file` maps the exact filename to the Secret key and the file's contents to the value.

* **33. [cite_start]Create a kubernetes.io/tls typed Secret from a cert/key pair and explain where it's consumed.** [cite: 148]
  `kubernetes.io/tls` dictates `tls.crt` and `tls.key` formatting, directly consumed by Ingress controllers.

* **34. [cite_start]Mount a Secret as a volume and explain the security trade-offs versus env vars.** [cite: 149]
  Volume mounts are safer than environment variables because env vars easily leak into crash logs.

* **35. [cite_start]Use envFrom with a secretRef to load every key of a Secret as env vars at once.** [cite: 150]
  `envFrom` bulk-injects all keys from a Secret into the container's environment simultaneously.

* **36. [cite_start]Create a docker-registry (image-pull) Secret and reference it via imagePullSecrets; explain when it's required.** [cite: 151]
  `imagePullSecrets` supply the specific credentials the kubelet needs to pull from private registries.

* **37. [cite_start]Create a Secret with several keys and selectively mount only one of them into a pod.** [cite: 152]
  Defining `items` in the volume mount projection allows you to isolate and mount single keys.

* **38. [cite_start]Create a ClusterIP Service for a Deployment and resolve it by DNS from another pod (nslookup <svc>.<ns>.svc.cluster.local).** [cite: 153, 154]
  Internal DNS uses the `<svc>.<ns>.svc.cluster.local` structure to reliably resolve services across namespaces.

* **39. [cite_start]Create a NodePort Service and reach it via minikube service <svc> --url and $(minikube ip): <nodePort>.** [cite: 155]
  NodePort services open a specific, static port across every node's IP in the cluster.

* **40. [cite_start]Create a Load Balancer Service, explain why EXTERNAL-IP is <pending> on minikube, and obtain access with minikube tunnel.** [cite: 156]
  Minikube lacks a cloud load balancer, so `minikube tunnel` simulates external IPs locally.

* **41. [cite_start]Create a headless Service (clusterIP: None) for a StatefulSet and show it returns per-pod A records instead of a single VIP.** [cite: 157]
  Headless services (ClusterIP: None) return direct pod IPs rather than routing through a proxy VIP.

* **42. [cite_start]Inspect a Service's Endpoints/EndpointSlice and explain how they're populated from the pod selector.** [cite: 158]
  EndpointSlices dynamically sync the active IPs of Ready pods matching the service selector.

* **43. [cite_start]Demonstrate cross-namespace access using the FQDN, and show the short name fails from another namespace.** [cite: 159]
  Short DNS names only search the pod's local namespace; cross-namespace queries require the FQDN.

* **44. [cite_start]Compare kubectl port-forward to a Service versus to a Pod - explain the difference and when each fits.** [cite: 160]
  Forwarding to a Service load-balances across backends, while forwarding to a Pod connects to that exact instance.

* **45. [cite_start]Explain the difference between a Service's port, targetPort, and nodePort.** [cite: 161]
  `port` is the internal Service IP port, `targetPort` is the container port, and `nodePort` is the host's exposed port.

* **46. [cite_start]Verify connectivity to a ClusterIP Service from a throwaway debug pod (kubectl run tmp --rm-it -- image=busybox -- sh) with wget/nc.** [cite: 162, 163]
  A temporary debug pod provides an isolated environment to execute connectivity tests.

* **47. [cite_start]Create two Deployments and one Service that load-balances across both by sharing a common label; prove requests hit both.** [cite: 164]
  A Service load-balances across any pods sharing the defined label, regardless of the underlying Deployment.

* **48. [cite_start]Systematically diagnose why a pod can't reach a Service: DNS endpoints selector labels readiness → port.** [cite: 165]
  Diagnosis flows sequentially: verify Pod health, confirm Selector labels, check Endpoints, and validate DNS.

* **49. [cite_start]Expose a service with a NodePort and verify it works.** [cite: 166]
  `kubectl expose --type=NodePort` creates a cluster-wide high port mapping for immediate external testing.

* **50. [cite_start]Add an HTTP liveness Probe hitting / on port 80 to an nginx Deployment and verify via kubectl describe pod.** [cite: 167]
  Liveness probes periodically ping endpoints, restarting the pod automatically upon failure.

* **51. [cite_start]Add a tcpSocket readiness probe to a redis container on port 6379 and verify it.** [cite: 168]
  Readiness probes block traffic routing until the socket successfully accepts connections.

* **52. [cite_start]Configure a startup probe with a failure Threshold periodSeconds budget for a ~2-minute boot and justify the numbers.** [cite: 169]
  Startup probes provide extra leeway for slow-booting applications before standard liveness checks begin.

* **53. [cite_start]Explain the default behavior when no probes are defined at all.** [cite: 170]
  Without probes, Kubernetes blindly assumes a container is fully ready the moment its primary process starts.

---

## [cite_start]LO5 - Solve problems with application shipping by using containers [cite: 172]

* [cite_start]**1. podman run -d --name web -p 80:8080 docker.io/library/nginx (nginx listens on 80; the site isn't reachable on the host port you expected what's reversed?)** [cite: 173, 174]
  The port mapping is reversed; it should be `-p 8080:80` to map host 8080 to container 80.

* **2. podman run-d--name db -e MYSQL_ROOT_PASSWORD docker.io/library/mysql (the container exits during init inspect podman logs.)** [cite: 175]
  The database requires a password value; fix it by specifying `-e MYSQL_ROOT_PASSWORD=secret`.

* [cite_start]**3. podman run-d--name app --network host -p 8080:80 docker.io/library/nginx (why is the -p flag effectively ignored here?)** [cite: 176]
  `--network host` completely overtakes the network stack, rendering `-p` port mappings ineffective and ignored.

* [cite_start]**4. podman run-d--name c1 docker.io/library/busybox (the container goes straight to Exited (0) - why, and how do you keep it running?)** [cite: 177, 178]
  Busybox exits immediately because it has no default long-running command; fix by running `sleep infinity`.

* **5. podman run --rm-d-name job docker.io/library/alpine echo hello (then podman logs job fails explain the -rm + detached gotcha.)** [cite: 179, 180]
  The `--rm` flag deletes the container upon exit, erasing crucial logs needed to diagnose the run.

* [cite_start]**6. podman run -d -p 8080:80 --memory 8m docker.io/library/mysql (the database never becomes healthy what limit is the problem?)** [cite: 181]
  MySQL crashes instantly because an 8MB memory limit triggers an Out-Of-Memory kill; increase `--memory`.

* [cite_start]**7. podman run -d --name a docker.io/library/alpine sleep 1d podman run -d --name b docker.io/library/alpine sleep 1d podman exec a ping b (name resolution fails why, on the default network, and how do you fix it?)** [cite: 182, 183]
  Name resolution fails on the default bridge network; fix by creating and attaching to a custom network.

* **8. podman run -d --name web -v./html:/usr/share/nginx/html docker.io/library/nginx (the files aren't visible in the container, or SELinux denies access what mount flag is missing?)** [cite: 184, 186]
  SELinux denies directory access; fix by adding the `:Z` flag (`-v ./html:...:Z`) to apply correct contexts.

* **9. [cite_start]For each Containerfile/Dockerfile: identify the mistake, fix it, and explain what was wrong.** [cite: 187]
  (Debugging intro).

* **10. [cite_start]FROM debian:12 RUN apt-get install -y nginx (the build fails to find the package what's missing before install?)** [cite: 188, 189, 190]
  The package manager cannot find the package; prepend `apt-get update &&` before installation.

* **11. [cite_start]FROM python:3.11 COPY app.py/app/app.py WORKDIR /app CMD python app.py (the app starts but doesn't handle signals / can't be stopped cleanly shell vs exec form?)** [cite: 191, 192, 193, 194, 195]
  Shell form `CMD` blocks signal handling; fix by using the exec array form `CMD ["python", "app.py"]`.

* **12. [cite_start]FROM node:20 COPY./app WORKDIR /app RUN npm install (every source change triggers a full npm install reorder for layer caching.)** [cite: 196, 197, 198, 199, 200]
  Copying everything first invalidates the cache; copy `package.json` and install dependencies before copying source code.

* **13. [cite_start]FROM alpine:3.20 ENV PATH=/app/bin RUN apk add --no-cache curl (after this, curl/sh aren't found what did setting PATH break?)** [cite: 201, 202, 203, 204]
  Overwriting `PATH` deletes system paths; append instead by using `ENV PATH=$PATH:/app/bin`.

* **14. [cite_start]FROM ubuntu:24.04 EXPOSE 8080 CMD ["python3", "-m", "http.server", "3000"]** [cite: 205, 206, 207]
  `EXPOSE` is merely documentation; the app serves on 3000 but the exposed mapping states 8080.

* **15. (you map -p 8080:8080 but nothing answers what mismatch is there, and what does EXPOSE actually do?)[cite_start]** [cite: 210, 211]
  (Covered in previous point).

* **16. [cite_start]FROM debian:12 RUN apt-get update && apt-get install -y build-essential (the image is huge how do you cut the size in the same layer?)** [cite: 212, 213]
  The image retains cache files; chain `&& apt-get clean && rm -rf /var/lib/apt/lists/*` to reduce size.

* **17. [cite_start]FROM python:3.11 USER appuser COPY requirements.txt /app/requirements.txt RUN pip install -r/app/requirements.txt (permission denied errors what's wrong with the USER/COPY ordering and ownership?)** [cite: 214, 215, 217, 218, 219]
  Setting `USER` early copies files as root; fix by using `COPY --chown=appuser`.

* **18. FROM golang:1.22 COPY./src WORKDIR /src RUN go build -o app. [cite_start]CMD ["/src/app"] (the final image is ~1 GB how would a multi-stage build fix it?)** [cite: 220, 221, 222, 223, 224, 225, 227]
  The image contains the heavy Go toolchain; use a multi-stage build to copy only the binary to a smaller base.

* **19. A pod is stuck Pending. [cite_start]Walk through the diagnosis with kubectl describe pod and identify the reason from the events (scheduling / resources / PVC/nodeSelector).** [cite: 228, 229]
  Pending usually signifies unmet scheduling constraints like lacking node resources or unbound volumes.

* **20. Find a deployment from older tasks, remove a couple of spaces and try to deploy it. [cite_start]Identify the cause in the error messages and fix it.** [cite: 230, 231]
  YAML relies strictly on spaces; fixing the indentation resolves the schema parsing errors.

* **21. A pod is in ImagePullBackOff. [cite_start]Diagnose the cause (image typo, missing tag, private registry without pull secret) and fix it.** [cite: 232]
  The kubelet cannot pull the image; verify the tag spelling or add missing `imagePullSecrets`.

* **22. A pod is in CrashLoopBackOff. [cite_start]Use kubectl logs -previous to read the last crash and fix the root cause.** [cite: 233]
  The application repeatedly crashes; use `kubectl logs --previous` to identify code or configuration errors.

* **23. A pod's last state shows OOMKilled. [cite_start]Confirm it from kubectl get pod -o yaml / describe, then fix it (raise the memory limit or fix the app).** [cite: 234, 235]
  The container exceeded its memory limit; increase `resources.limits.memory` in the pod spec.

* **24. A Deployment's pods never become Ready. [cite_start]Trace it to a failing readiness probe and correct the probe or the app.** [cite: 236]
  Unready pods fail their readiness probe; verify the probe path/port aligns with the application's true state.

* **25. A Service returns nothing. [cite_start]Diagnose a Service/pod label mismatch using kubectl get endpoints.** [cite: 237]
  Empty Endpoints mean the Service `selector` labels completely fail to match the target Pod's labels.

* **26. [cite_start]Given a manifest where spec.selector.matchLabels doesn't match spec.template.metadata.labels, explain the error kubectl apply returns and fix it.** [cite: 238]
  Deployments mandate label parity; ensure `matchLabels` exactly equals the pod template's metadata labels.

* **27. [cite_start]Given a manifest whose resources.requests exceed any node's capacity, explain why the pod is Pending (Insufficient cpu/memory) and fix it.** [cite: 239]
  Requested resources exceed node capacity; lower `resources.requests` to fit within cluster constraints.

* **28. [cite_start]Given a pod mounting a PVC that doesn't exist, diagnose the FailedMount/Pending and create the PVC.** [cite: 240]
  The pod awaits an unbound volume; create the corresponding PVC to fulfill the claim.

* **29. A container based on ubuntu with no long-running command shows Completed/CrashLoopBackOff. [cite_start]Explain why and add a proper command.** [cite: 241]
  Base OS images exit naturally; add a persistent `command: ["sleep", "infinity"]` to keep it running.

* **30. [cite_start]Use kubectl get events -sort-by=.lastTimestamp (or kubectl events) to triage everything that happened to a pod over time.** [cite: 242]
  Sorting events chronologically visualizes the exact sequence of scheduling and lifecycle failures.

* **31. [cite_start]Use kubectl exec -it to get a shell in a pod and debug DNS with nslookup and cat /etc/resolv.conf.** [cite: 243]
  `kubectl exec` allows internal inspection of `/etc/resolv.conf` to diagnose cluster DNS misconfigurations.

* **32. [cite_start]Use an ephemeral debug container (kubectl debug -it <pod> --image busybox-target=<container>) to troubleshoot a no-shell/distroless container.** [cite: 244]
  Ephemeral containers inject debugging utilities directly into running, minimal containers.

* **33. [cite_start]Spin up a throwaway kubectl run tmp -rm-it-image busybox pod to test connectivity to a Service from inside the cluster.** [cite: 245]
  Launching a throwaway pod isolates network troubleshooting from the complexities of the main application.

* **34. A node shows NotReady. [cite_start]List what you'd check (kubelet, disk pressure, CNI) and inspect it on minikube with kubectl describe node and sudo minikube logs.** [cite: 246]
  A NotReady node suffers from infrastructure issues; inspect kubelet logs or node disk pressure.

* **35. A pod is stuck Terminating. [cite_start]Explain finalizers and the grace period, then force-delete it (--grace-period=0 -- force) and state the risks.** [cite: 247]
  Finalizers prevent deletion until cleanup completes; forcing deletion circumvents this but risks resource leaks.

* **36. [cite_start]Given a manifest with the wrong apiVersion/kind pairing (e.g. apps/v1 + Pod), explain the validation error and correct the group/version.** [cite: 248]
  Kubernetes rejects invalid API versions; change `apps/v1` to `v1` to correct the schema error for a Pod.

* **37. A pod fails with CreateContainerConfigError because a configMapKeyRef key is missing. [cite_start]Diagnose and fix.** [cite: 249]
  The pod halts because an environment variable explicitly demands a missing ConfigMap key.

* **38. A pod can't mount a Secret that lives in a different namespace. [cite_start]Explain namespace scoping and fix it.** [cite: 250]
  Secrets are namespace-isolated; recreate the Secret in the identical namespace as the requesting pod.

* **39. A rollout is stuck with Progress Deadline Exceeded. [cite_start]Use kubectl describe deployment / rollout status to find the cause and remediate.** [cite: 253]
  A timed-out rollout usually indicates pods failing to become ready; inspect replica sets to find the blockage.

* **40. [cite_start]Catch indentation/field typos (e.g. imagePullpolicy, misnested ports) before applying with kubectl apply -- dry-run server/--validate true, then fix them.** [cite: 254]
  `--dry-run=server` uses the cluster's API to validate manifest syntax and fields safely without applying them.

* **41. An app's logs say it can't reach its database Service. [cite_start]Verify in order: pod running Service exists → endpoints populated DNS resolves correct port and identify the broken link.** [cite: 255, 256]
  Systematically step through Pod, Service, Endpoints, and DNS to pinpoint exactly where connectivity breaks.

* **42. [cite_start]Read a container's state/lastState and restartCount with kubectl get pod -o jsonpath to determine the root cause of repeated restarts.** [cite: 257]
  Parsing `lastState` JSON reveals specific exit codes, critical for debugging CrashLoopBackOff loops.

* **43. [cite_start]Compare OpenShift Route to Ingress.** [cite: 258]
  OpenShift Routes natively provide built-in edge routing and TLS termination, contrasting with standard Ingress objects.
