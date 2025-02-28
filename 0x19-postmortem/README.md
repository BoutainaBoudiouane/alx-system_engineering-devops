# Postmortem: Outage Due to Misconfigured Load Balancer

## Issue Summary
* **Duration of Outage**: 2023-10-20 09:00 UTC to 2023-10-20 10:30 UTC (1.5 hours)
* **Impact**: The website was completely inaccessible for 100% of users. Users received a "502 Bad Gateway" error when trying to access the site.
* **Root Cause**: A misconfigured load balancer caused it to route traffic to an invalid backend server.

## Timeline
* **09:00 UTC**: Issue detected when monitoring alerts flagged a sudden drop in traffic to zero.
* **09:05 UTC**: Engineers noticed the load balancer was returning "502 Bad Gateway" errors.
* **09:10 UTC**: Investigation focused on the backend servers, assuming one of them had crashed.
* **09:20 UTC**: Misleading path: Engineers checked the health of all backend servers, but all were running normally.
* **09:30 UTC**: Incident escalated to the Networking and DevOps teams.
* **09:45 UTC**: Root cause identified as a misconfigured load balancer rule that pointed to a decommissioned server.
* **10:00 UTC**: Load balancer configuration was corrected to point to the correct backend servers.
* **10:30 UTC**: Full service restored.

## Root Cause and Resolution
* **Root Cause**: During a routine server decommissioning process, the load balancer configuration was not updated to remove the decommissioned server from its backend pool. This caused the load balancer to route traffic to a non-existent server.
* **Resolution**: The load balancer configuration was updated to remove the decommissioned server and point only to active backend servers. A script was also added to automatically update the load balancer configuration during server decommissioning.

## Corrective and Preventative Measures
* **Improvements**:
   1. Implement automated checks to ensure load balancer configurations are updated during server decommissioning.
   2. Add monitoring for load balancer backend server health.
   3. Conduct regular audits of load balancer configurations.
   4. Improve documentation for server decommissioning procedures.
* **TODO List**:
   * Write a script to automatically update load balancer configurations during server decommissioning.
   * Add monitoring for load balancer backend server health.
   * Conduct a full audit of all load balancer configurations.
   * Update server decommissioning documentation to include load balancer updates.
   * Train the team on the new procedures.

## Attractive Elements
To make this postmortem more engaging, here's a simple diagram to illustrate the issue:

```
[Users] --> [Load Balancer] --> [Backend Server 1]
                              --> [Backend Server 2]
                              --> [Decommissioned Server (Invalid)]
```

When the load balancer tried to route traffic to the decommissioned server:

```
[Users] --> [Load Balancer] --> [ERROR: 502 Bad Gateway] 
```
