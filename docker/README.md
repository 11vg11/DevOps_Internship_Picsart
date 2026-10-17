# Docker and Containerization

## 1. Introduction

Docker packages an application and its runtime dependencies into images that can be run as isolated containers. Repeatable images reduce differences between development, test, and deployment environments.

## 2. Core concepts

- **Image:** immutable, layered template; **container:** a running instance of an image.
- **Dockerfile:** instructions for building an image. Each instruction can contribute a cached image layer.
- **Port publishing:** maps a host port to a container port; it does not make the application listen on the host automatically.
- **Volume:** persistent or shared data; **network:** container connectivity; **environment variable:** runtime configuration.
- **Registry:** stores and distributes images, for example Docker Hub or a private registry.
- **Compose:** describes a local multi-container application in YAML.

## 3. How it works

The Docker client sends build/run requests to the Docker daemon. The daemon downloads or builds an image, creates a writable container layer, configures networking and mounts, then starts the process. Container logs normally capture the process's stdout and stderr.

## 4. Commands and examples

```sh
docker pull nginx:alpine                 # Download an image
docker image ls                          # List local images
docker build -t web-lab:1.0 .            # Build from the Dockerfile in this directory
docker run -d --name web -p 8080:80 nginx:alpine
docker ps                                # List running containers
docker inspect web                       # Inspect configuration and runtime state
docker logs --tail 50 -f web             # Follow recent container logs
docker stop web                          # Gracefully stop the container
docker rm web                            # Remove the stopped container
docker image rm web-lab:1.0              # Remove an image when no longer needed
```

`-p 8080:80` maps host port 8080 to container port 80. Avoid publishing databases or admin interfaces publicly.

## 5. Configuration example

Example `Dockerfile` (a small static site build context):

```dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

Example `compose.yaml`:

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "127.0.0.1:8080:80"
    volumes:
      - ./site:/usr/share/nginx/html:ro
```

The loopback bind keeps this development service reachable only from the local machine. Start and stop with `docker compose up -d` and `docker compose down`.

## 6. Hands-on exercises

**Prerequisites:** Docker installed and running; a terminal in a disposable directory. **Expected outcome:** a local web response on `http://127.0.0.1:8080`.

1. Create `site/index.html` with a short message and the Compose file above.
2. Run `docker compose up -d`, inspect with `docker compose ps`, and view `docker compose logs`.
3. Edit the page and refresh the browser; explain the bind mount behavior.
4. Run `docker compose down`, then confirm the test container stopped. Record actual results; no cloud resources are involved.

## 7. Real-world use cases

Reproducible developer environments, CI build/test jobs, packaging microservices, and running local dependencies such as databases for integration tests.

## 8. Best practices

- Use small, trusted, pinned base images; rebuild regularly for security updates.
- Run as a non-root user when the image supports it; avoid embedding credentials in layers.
- Use `.dockerignore`, multi-stage builds, health checks where useful, resource limits, and read-only mounts for immutable data.
- Store durable data in volumes, not the container writable layer. Avoid privileged mode and unnecessary host mounts.
- Scan images and use a registry with access controls; consider image digests for reproducibility.

## 9. Common errors and troubleshooting

| Symptom | Checks |
|---|---|
| Cannot connect to daemon | Is Docker running? Does the user have permission to access its socket? |
| Port already allocated | Use `docker ps`/`ss -ltn` to find the listener or choose another host port. |
| Container exits | Inspect `docker ps -a` and `docker logs`; the container's main process may have finished or failed. |
| Changes absent | Check build context, `COPY` paths, bind mounts, and whether the image/container was rebuilt/recreated. |
| Permission denied on files | Check UID/GID ownership and mount permissions; do not solve it by running everything privileged. |

## 10. Interview questions

1. **Image vs. container?** An image is a layered template; a container is an isolated runtime instance with a writable layer.
2. **Why are image layers useful?** They enable caching and reuse, reducing rebuild and transfer work.
3. **What does `-p 8080:80` do?** It forwards host port 8080 to container port 80.
4. **Where should persistent container data live?** In a managed volume or deliberate bind mount, not only in the ephemeral container layer.
5. **How is Compose different from Docker?** Compose is a tool and file format for defining and operating related containers.

## 11. Further learning

- [Docker documentation](https://docs.docker.com/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose documentation](https://docs.docker.com/compose/)

## 12. Progress checklist

- [ ] Build, tag, pull, and inspect an image
- [ ] Run, inspect, log, stop, and remove a container
- [ ] Explain ports, layers, volumes, networks, and environment variables
- [ ] Use Compose and describe registry/image security practices
