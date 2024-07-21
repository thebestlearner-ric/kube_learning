# Docker Engine
## Dockerd (Docker Daemon):
1. Role: The core component of the Docker Engine.
2. Function: Manages Docker containers, images, networks, and volumes. It listens for API requests from the Docker CLI or other tools and performs the requested operations.
3. Interaction: Interfaces with the underlying operating system and container runtime to execute container-related tasks.

## Docker CLI (Command Line Interface):
1. Role: The user-facing component that allows users to interact with Docker.
2. Function: Provides commands to manage Docker resources. It communicates with the Docker daemon via the Docker REST API to perform operations.
3. Usage: Commands like docker run, docker build, docker ps are executed via the CLI.
## Containerd:
1. Role: A container runtime used by dockerd.
2. Function: Manages the lifecycle of containers, including creation, execution, and destruction. Containerd adheres to the OCI (Open Container Initiative) specifications.
3. Integration: Used by dockerd to run containers and manage lower-level container functions.

## Runc:
1. Role: A low-level container runtime.
2. Function: Responsible for running containers according to the OCI runtime specification. It is called by Containerd to actually start and stop containers.

## Summary:
1. Dockerd: The primary daemon process in the Docker Engine, responsible for managing containers and other Docker objects.
2. Docker Engine: The complete suite of tools and components, including dockerd, Docker CLI, Containerd, and Runc, that together provide the full functionality to build, ship, and run containers.