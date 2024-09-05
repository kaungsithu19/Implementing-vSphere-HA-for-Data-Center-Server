# Implementing-vSphere-HA-for-Data-Center-Server

## Objective

The objective of "Implementing vSphere HA for Data Center Server" is to enhance the reliability and availability of virtualized server environments by configuring VMware vSphere High Availability (HA). This ensures automatic detection and recovery of virtual machines in the event of host failures, minimizing downtime and maintaining business continuity within the data center.

### Skills Learned

- VMware vSphere management: Proficiency in configuring and managing vSphere environments.
- High availability (HA) setup: Understanding how to configure and maintain HA clusters to ensure continuous server operation.
- Virtual machine recovery: Skills in automating the detection and recovery of virtual machines during host failures.
- Data center management: Knowledge of optimizing server resources and minimizing downtime in a virtualized data center.
- Troubleshooting: Ability to diagnose and resolve issues related to vSphere HA and system failures.

### Tools Used

- VMware vSphere for creating and managing virtualized environments and setting up High Availability (HA) clusters.
- VMware vCenter Server for centralized management of vSphere environments, including HA configurations.
- ESXi Hosts (Hypervisor) used to deploy and run virtual machines in the data center.
- vSphere Web Client for managing vSphere HA and configuring settings through a graphical interface.
- Shared Storage (e.g., SAN, NAS) for ensuring access to virtual machine files across hosts in an HA cluster.
- Mikrotik Router for changing the internal connection internel protocols.

## System Desgin

<img src="https://github.com/user-attachments/assets/404781cb-5056-4840-8ade-59a3f97bc918" width="650" height="340" />

Fig 1: Proposed System Design

### System Design of vSphere HA for Data Center Server

This diagram represents the design and architecture of a **vSphere High Availability (HA)** setup. The goal is to ensure continuous availability of virtual machines (VMs) in a data center by providing automated recovery during hardware failures. Here's a breakdown of the system design components and how they interact:

### Key Components:

1. **Mikrotik Router Board (172.20.10.10)**:
   - **Role**: The central networking component responsible for routing traffic between different network segments.
   - **Function**: Ensures communication between the hosts, VMs, shared storage, and vCenter appliance. It connects the entire network infrastructure within the data center.

2. **ESXi Hosts**:
   These are the servers hosting virtual machines and are key to the HA setup.
   
   - **ESXi 1 (172.20.10.12)**:
     - Hosts a virtual machine with IP **172.20.10.6**.
     - Can access shared storage through the network and participate in the vSphere HA cluster.
   
   - **ESXi 2 (172.20.10.3)**:
     - Hosts a virtual machine with IP **172.20.10.7**.
     - Also part of the vSphere HA cluster, allowing for load balancing and failover.

   **Purpose of ESXi Hosts**:
   - ESXi hosts are connected to the shared storage via iSCSI. Both hosts participate in a vSphere HA cluster. If one host fails, the other can automatically take over the VMs, minimizing downtime.

3. **Shared Storage (172.20.10.5)**:
   - **Role**: Acts as a centralized storage system accessible by all ESXi hosts.
   - **Access via iSCSI**: A virtual machine with IP **172.20.10.2** is linked to the shared storage, using iSCSI to make it accessible across the network.
   - **Function**: It stores virtual machine data, ensuring data redundancy and availability. Shared storage is crucial in an HA environment since it allows VMs to be migrated between hosts without data loss.

4. **vCenter Appliance (172.20.10.8)**:
   - **Role**: Provides centralized management for the virtualized environment.
   - **Function**: Manages and monitors the ESXi hosts and the VMs. It is essential for configuring vSphere HA, which ensures that if one ESXi host goes down, the VMs are restarted on another host in the cluster.
   - **vCenter also manages load balancing**: This ensures optimal resource utilization across hosts.

5. **vSphere Client (172.20.10.13)**:
   - **Role**: Provides a user interface for administrators to interact with the vCenter appliance.
   - **Function**: Allows management and configuration of the vSphere environment, including HA, VM creation, and performance monitoring.

6. **iSCSI (172.20.10.2)**:
   - **Role**: Implements iSCSI to connect the shared storage to the ESXi hosts.
   - **Function**: Facilitates the transfer of storage data over the network. It allows the shared storage to be used by all ESXi hosts, enabling VM migration and HA operations.

### System Design Features:

1. **High Availability (HA)**:
   - **Failover and Recovery**: If one of the ESXi hosts fails, the vSphere HA will detect this failure and automatically restart the VMs on the other available host. The shared storage ensures that the VMs’ data is preserved, allowing for seamless recovery.
   - **Centralized Management**: The vCenter appliance ensures that the entire HA cluster is monitored and controlled from one location, simplifying administration.

2. **Redundant and Shared Resources**:
   - **Shared Storage**: Both ESXi hosts use a shared storage system, meaning the data is available even if one host fails. This is critical for HA setups where VM migrations and failover processes depend on uninterrupted access to data.
   - **Multiple Hosts**: The ESXi hosts are both part of the same HA cluster, meaning they work together to handle workloads and ensure continuous operation, even if one host goes offline.

3. **Network Communication**:
   - **Router Connectivity**: The Mikrotik router board ensures that all components in the system are properly connected, including the ESXi hosts, vCenter appliance, and shared storage. This provides a reliable communication path for data transfers, heartbeat signals, and management traffic.

### How the System Works:

1. **Normal Operations**:
   - Both ESXi hosts run virtual machines using the resources of the shared storage.
   - The vCenter appliance continuously monitors the health of the hosts and VMs, ensuring they are running smoothly.

2. **Failure Scenario**:
   - If one ESXi host fails (e.g., ESXi 2), vSphere HA, managed by vCenter, detects the failure through heartbeats.
   - VMs on the failed host are automatically restarted on the remaining active ESXi host (ESXi 1).
   - Since the shared storage is centralized, there is no data loss during this failover.

3. **Restoration**:
   - After recovery, the failed ESXi host can be re-added to the HA cluster, allowing the workloads to be balanced again across both hosts.

<img src="https://github.com/user-attachments/assets/777c1a00-df84-49b4-ba23-35073a1c898b" width="800" height="220" />

Fig 2: Normal state of being connected between ESXI and Share-storage

<img src="https://github.com/user-attachments/assets/bd8a14ba-893e-4b45-bbab-1077ead39b1e" width="800" height="220" />

Fig 3: Operation state after ESXI 1 failover is occured


