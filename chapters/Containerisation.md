# Containerisation
## Background 
### What is Containerisation?
#### Key Concepts of Containerisation?
1. Isolation:
Containers run in isolated environments, ensuring that the application's processes, files, and network resources are separated from other containers and the host system. This isolation is achieved through kernel features like namespaces and cgroups.
2. Portability:
Containers package an application and all its dependencies, such as libraries and configuration files, into a single image. This image can be run consistently on any system that supports the container runtime, making it easy to move applications between development, testing, and production environments.
3. Efficiency:
Unlike traditional virtual machines (VMs), containers share the host operating system's kernel, which reduces overhead and allows for more efficient use of system resources. This leads to faster startup times and lower memory and CPU usage.
4. Consistency:
By encapsulating the entire runtime environment, containers ensure that applications run the same way across different environments. This eliminates the "works on my machine" problem often encountered in software development.

# How does the Concept of isolation work?
### Key Technologies for Container Isolation:
1. Namespaces:
Namespaces provide isolation for various system resources, ensuring that containers have their own unique view of the system. Each namespace isolates a specific aspect of the operating environment.
Types of Namespaces:
- PID Namespace: Isolates process IDs, ensuring that processes inside a container cannot see or interact with processes outside the container.
- Mount Namespace: Isolates the filesystem mount points, allowing containers to have their own filesystem hierarchy.
- UTS Namespace: Isolates hostname and domain name, allowing containers to have their own hostname.
- IPC Namespace: Isolates inter-process communication resources like shared memory and message queues.
- Network Namespace: Isolates network interfaces and IP addresses, allowing containers to have their own network stack.
- User Namespace: Isolates user and group IDs, allowing containers to have their own user and group mappings.
2. Control Groups (cgroups):
Cgroups provide resource limitation and management, ensuring that containers use only their allocated share of system resources such as CPU, memory, and I/O.
- Resource Management:
- CPU Limitation: Control the CPU usage for each container, preventing any container from monopolizing the CPU.
- Memory Limitation: Set memory limits to ensure that containers do not exceed their allocated memory, protecting the host system from being exhausted.
- I/O Limitation: Control the read/write access to disk resources to manage the I/O performance of containers.
3. Security Features:
Additional security mechanisms ensure that containers run securely and limit the potential impact of compromised containers.
- Seccomp: Provides system call filtering, restricting the system calls that a container can make to reduce the attack surface.
- SELinux (Security-Enhanced Linux): Provides mandatory access control policies to enforce security policies on containers.
- AppArmor: Similar to SELinux, it provides security profiles that restrict the capabilities of container processes.

# How is a Container Runtime deploy a container?
1. Container Creation:
When a container is created, the container runtime (e.g., Docker, containerd) sets up the necessary namespaces, cgroups, and security profiles for the container.
The runtime creates a new PID namespace, mount namespace, network namespace, etc., ensuring that the container has its own isolated view of these resources.
2. Resource Allocation:
The runtime configures cgroups to limit the resources available to the container. For example, it sets CPU and memory limits based on the container's configuration.
3. Running the Container:
The container runs in its isolated environment, with its processes, filesystems, network interfaces, and users isolated from other containers and the host system.
Security mechanisms like seccomp and AppArmor enforce additional restrictions on the container's capabilities.