# Firewall Implementation and Exploration

## Introduction
This project explores the implementation and configuration of firewalls using `iptables` and Netfilter in Linux. The objectives include developing stateless and stateful firewall rules, analyzing connection tracking mechanisms, implementing packet filtering, and experimenting with load balancing techniques. The project follows the SEED Labs – Firewall Exploration Lab and is structured into multiple tasks.

## Setup
The setup files from SEED Labs were downloaded, and the Docker containers were built and executed to create a controlled environment for firewall experimentation.

---
## Task 1: Implementing a Simple Firewall
The goal of this task was to implement a packet-filtering firewall that inspects incoming and outgoing packets, enforcing administrator-defined policies within the Linux kernel.

### 1.1 Implementing a Simple Kernel Module
- **Makefile Creation**: A Makefile was developed to compile the Loadable Kernel Module (LKM).
- **Compilation and Loading**: The kernel module `hello.ko` was compiled and loaded.
- **Observations**:
  - Upon loading, "Hello World!" was printed in system logs.
  - Upon removal, "Bye-bye World!" was printed.
  - Verified module presence with `lsmod | grep hello`.
  - Module details checked using `modinfo hello.ko`.

### 1.2 Implementing a Simple Firewall Using Netfilter
- **Netfilter Hook Implementation**:
  - Implemented five hook functions (`NF_INET_PRE_ROUTING`, `NF_INET_LOCAL_IN`, `NF_INET_FORWARD`, `NF_INET_LOCAL_OUT`, `NF_INET_POST_ROUTING`).
  - Built and loaded `seedFilter.ko` into the kernel.
  - Verified the working of hooks using `dig 8.8.8.8 example.com`.
- **Packet Filtering**:
  - Implemented `seedBlock.c` to block packets.
  - Built `seedBlock.ko` and loaded it into the kernel.
  - Verified that pings from `Host A` to `VM` were blocked.

---
## Task 2: Experimenting with Stateless Firewall Rules
### 2.1 Protecting the Router
- Verified that pings and telnet connections to the router were successful.
- Applied firewall rules to block access.
- Observed that pings and telnet connections were dropped.

### 2.2 Protecting the Internal Network
- Restarted Docker containers.
- Applied `iptables` rules to drop ICMP echo requests.
- Verified that pings to `192.168.60.5` were blocked while those to `10.9.0.11` were allowed.

### 2.3 Protecting Internal Servers
- Applied rules to allow telnet access only to `192.168.60.5`.
- Verified that telnet connections to `192.168.60.5` were successful while others were blocked.

---
## Task 3: Connection Tracking and Stateful Firewall
### 3.1 Connection Tracking Experiments
#### ICMP Experiment
- Sent ICMP packets and monitored with `conntrack -L`.
- Observed that the state persisted for ~8 seconds after termination.

#### UDP Experiment
- Established a UDP connection using Netcat.
- Observed that `conntrack` retained the state for ~8-10 seconds after termination.

#### TCP Experiment
- Established a TCP connection using Netcat.
- Observed `ESTABLISHED` state in `conntrack`.
- After termination, the state changed to `TIME_WAIT` for ~113 seconds before being discarded.

### 3.2 Setting Up a Stateful Firewall
- Implemented stateful filtering using `iptables`.
- Verified that only established connections were allowed.

---
## Task 4: Limiting Network Traffic
- Applied `iptables` rules to limit traffic from `10.9.0.5`.
- Observed that packets were initially not limited due to missing drop rules.
- Added a secondary rule to drop excess packets.
- Verified that traffic was successfully limited with ~73.47% packet loss.

---
## Task 5: Load Balancing
- Started servers on `192.168.60.5`, `192.168.60.6`, and `192.168.60.7`.
- Configured `iptables` for round-robin load balancing.
- Verified that UDP packets were evenly distributed among hosts.
- Observed that random mode was inconsistent for short packets but followed statistical distribution over time.

---
## Conclusion
This project provided hands-on experience in implementing and configuring firewalls using `iptables` and Netfilter. Key takeaways include:
- Understanding kernel module implementation for packet filtering.
- Configuring stateless and stateful firewall rules.
- Experimenting with connection tracking and observing state retention.
- Applying packet rate limiting techniques.
- Implementing load balancing using firewall rules.
