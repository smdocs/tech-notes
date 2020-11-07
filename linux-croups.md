# Linux Cgroups

## What is a Cgroup?

Cgroups are a facility built into the kernel that allow the administrator to set resource utilization limits on any process on the system. 
In general, cgroups control:
- The number of CPU shares per process.
- The limits on memory per process.
- Block Device I/O per process.
- Which network packets are identified as the same type so that another application can enforce network traffic rules.

## How do cgroups work?
Cgroups are a mechanism for controlling certain subsystems in the kernel. These subsystems, such as devices, CPU, RAM, network access, and so on, are called 
controllers in the cgroup terminology. Each type of controller (cpu, blkio, memory, etc.) is subdivided into a tree-like structure. Each branch or leaf has its 
own weights or limits. A control group has multiple processes associated with it, making resource utilization granular and easy to fine-tune.

![](https://www.redhat.com/sysadmin/sites/default/files/styles/embed_large/public/2020-09/CGroup_Diagram.png?itok=pbB1JLje)
NOTE: Each child inherits and is restricted by the limits set on the parent cgroup.

In the diagram above, it is possible to have PID 1 in memory, disk i/o, and cpu control groups. The cgroups are created per resource type and have no association
with each other.

## Resources
1. [A Linux sysadmin's introduction to cgroups](https://www.redhat.com/sysadmin/cgroups-part-one)
2. [Cgroup Internals](https://terenceli.github.io/%E6%8A%80%E6%9C%AF/2020/01/05/cgroup-internlas)
3. [Understanding cgroup APIs](https://lwn.net/Articles/679786/)
