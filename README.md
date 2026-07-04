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

## LO4 - Accelerated delivery using Kubernetes

**Q1. Create Deployment imperatively and export YAML.**
> **Command:** `kubectl create deploy web --image=nginx:1.25 --replicas=3 --dry-run=client -o yaml > d.yaml`
> **Explanation:** Generates a clean starting manifest without actually deploying it to the cluster yet.

**Q4. Rolling update status.**
> **Command:** `kubectl set image deploy/web nginx=nginx:1.27` & `kubectl rollout status`
> **Explanation:** Gradually replaces old pods with new ones without downtime; tracked via rollout commands.

**Q6. Rolling Update: maxSurge 1, maxUnavailable 0.**
> **Code:** Define strategy in Deployment spec.
> **Explanation:** Guarantees 100% application capacity is maintained by forcing the new pod to be ready *before* killing an old one.

**Q7. Recreate strategy.**
> **Code:** `strategy: type: Recreate`
> **Explanation:** Kills all old pods before starting new ones. Essential for legacy apps requiring exclusive database locks.

**Q8. Requests vs Limits.**
> **Code:** Add `resources: requests: ... limits: ...`
> **Explanation:** `Requests` guarantee minimum node resources; `Limits` enforce a hard cap to prevent node starvation.

**Q10. ImagePullBackOff behavior.**
> **Action:** Deploy bad tag, observe.
> **Explanation:** The rollout automatically halts; old pods continue serving traffic seamlessly because the new ones failed to start.

**Q12. Kubectl expose.**
> **Command:** `kubectl expose deploy/web --port=80`
> **Explanation:** Creates a Service object that perfectly copies the Deployment's label selectors to route traffic.

**Q16. StatefulSet.**
> **Action:** Deploy StatefulSet with Headless Service.
> **Explanation:** Provides predictable, sticky pod identities (pod-0, pod-1) and stable internal DNS records.

**Q17. DaemonSet.**
> **Action:** Deploy DaemonSet.
> **Explanation:** Bypasses manual replicas to guarantee exactly one pod instance runs on *every* eligible cluster node.

**Q19. CronJob.**
> **Action:** Deploy CronJob.
> **Explanation:** Triggers isolated Job executions based on a time schedule; can be paused easily using the `suspend` flag.

**Q24. ConfigMap as Volume.**
> **Action:** Mount ConfigMap.
> **Explanation:** Dynamically injects each ConfigMap key as a distinct configuration file inside the pod's directory.

**Q26. Secret as Volume.**
> **Action:** Mount Secret.
> **Explanation:** Injects sensitive data as highly secure, in-memory `tmpfs` files with restrictive Linux permissions.

**Q34. Secret Volumes vs Env Vars.**
> **Explanation:** Volumes are safer; environment variables are easily exposed if the application crashes and dumps its memory to logs.

**Q38-40. Services (ClusterIP, NodePort, LoadBalancer).**
> **Action:** Create different service types.
> **Explanation:** `ClusterIP` is internal only. `NodePort` opens a port on all host IPs. `LoadBalancer` requests a cloud IP.

**Q41. Headless Service.**
> **Code:** `clusterIP: None`
> **Explanation:** Bypasses the proxy load balancer, returning the direct IP addresses of the backing pods via DNS.

**Q50-52. Probes (Liveness, Readiness, Startup).**
> **Action:** Define probes in container spec.
> **Explanation:** *Liveness* restarts stuck apps. *Readiness* stops traffic to busy apps. *Startup* gives slow apps time to boot.

---

## LO5 - Solve problems with application shipping

**Q1. `podman run -d -p 80:8080 nginx` -> Unreachable.**
> **Error:** Ports are reversed.
> **Fix:** `-p 8080:80` (HostPort:ContainerPort).

**Q2. `podman run -d mysql` -> Exits immediately.**
> **Error:** Missing required environment variables.
> **Fix:** Add `-e MYSQL_ROOT_PASSWORD=secret`.

**Q4. `podman run -d busybox` -> Exited(0).**
> **Error:** No long-running foreground process.
> **Fix:** Add a command like `sleep infinity`.

**Q6. `--memory 8m mysql` -> Crash.**
> **Error:** Memory limit too low (OOMKilled).
> **Fix:** Increase memory to at least `512m`.

**Q10. `FROM debian` -> `RUN apt-get install nginx` fails.**
> **Error:** Local package cache is empty.
> **Fix:** `RUN apt-get update && apt-get install -y nginx`.

**Q12. Node app `COPY . /app` then `npm install` is slow.**
> **Error:** Source code changes invalidate the cache for `npm install`.
> **Fix:** Copy `package.json` first, run `npm install`, *then* `COPY . /app`.

**Q21. Pod in ImagePullBackOff.**
> **Error:** Typo in image name, or missing authentication.
> **Fix:** Check image tag spelling, or provide `imagePullSecrets`.

**Q22. Pod in CrashLoopBackOff.**
> **Error:** Application is crashing constantly.
> **Fix:** Use `kubectl logs --previous` to read the stack trace of the dead container.

**Q24. Pods never become Ready.**
> **Error:** Failing readiness probe.
> **Fix:** Check the probe HTTP path/port; the app might be running on a different port than defined.

**Q25. Service returns nothing.**
> **Error:** Selector mismatch.
> **Fix:** Use `kubectl get endpoints`. If empty, the Service `selector` doesn't match the Pod `labels`.

**Q48. Diagnosing Service connectivity flow.**
> **Methodology:**
> 1. Check if Pod is **Running**.
> 2. Verify **Labels** match Service Selectors.
> 3. Verify **Endpoints** are populated.
> 4. Test internal **DNS** resolution.
