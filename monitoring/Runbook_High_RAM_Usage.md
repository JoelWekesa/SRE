# Runbook for High RAM Usage Alert

## 1️⃣ Runbook Overview

### **Alert Name**: High RAM Usage Alert

**Alert Trigger**: When memory usage exceeds the defined threshold for more than 2 minutes.

---

## 2️⃣ Objective

To reduce the impact of high memory usage by identifying the root cause and taking corrective actions to restore the system to normal operating conditions.

---

## 3️⃣ Tools Required

- **Prometheus** and **Grafana** (for monitoring and visualizing metrics)
- **Docker** (for container management)
- **System logs** and **application logs** (for deeper insights)
- **SSH access** to the affected servers

---

## 4️⃣ Alert Details

- **Threshold**: 90% RAM usage
- **Duration**: 2 minutes

- **Alert severity**: Extremely Critical

---

## 5️⃣ Actions to Take When Alert is Triggered

### **Step 1: Verify the Alert**

- **Check if the alert is genuine**:
  - Open **Grafana** and confirm the memory usage of the container/instance.

### **Step 2: Analyze the Root Cause**

- **Identify the container causing the issue**:

  - Review the **container memory usage** in Grafana.
  - Isolate which container is using high memory.

- **Check application logs** for any memory-related issues like memory leaks or inefficient memory usage.
- **Inspect system logs** (`/var/log/syslog`, `/var/log/dmesg`) for any errors or warnings.
- **Check for resource constraints**:
  - Sometimes, high memory usage is due to system-wide limits. Check if the container is nearing its memory limits.

### **Step 3: Take Immediate Actions**

- **Option 1: Kill or Restart the Container**  
  If you identify the container causing the issue:

  ```bash
  docker container restart your-container-name
  ```

  This can temporarily resolve the issue if high memory usage is due to a transient process or application bug.

- **Option 2: Scale the Application**  
  If high traffic or processing is causing the memory usage:
  - **Docker Compose**: Increase the number of replicas in your `docker-compose.yml`.

### **Step 4: Mitigate and Prevent Future Incidents**

- **Review container resource limits** (memory and CPU):

  - Set **memory limits** and **requests** to prevent containers from consuming excessive memory.
  - Example Docker Compose:
    ```yaml
    services:
     app:
      image: your-image
      deploy:
       resources:
        limits:
         memory: '2Gi'
        reservations:
         memory: '1Gi'
    ```

- **Optimize application performance**:

  - Check for memory leaks and inefficient memory management in the application code.
  - Consider optimizing algorithms or using a more memory-efficient approach.

- **Enable Swap** (if needed):
  - In case memory usage is close to limits, enabling swap can provide temporary relief, but it should not be a long-term solution.

---

## 6️⃣ Post-Incident Review

- **Root Cause Analysis**: Was the high memory usage caused by an application bug, traffic spike, or resource mismanagement?
- **Action Plan**: What measures will be put in place to prevent this from happening again? (e.g., scaling, optimization, alert tuning)
- **Document Lessons Learned**: Update the runbook with any new insights or steps that need to be added.

---

## 7️⃣ Communication During the Incident

- **Notify the team** An alert is triggered to Telegram when the incident is detected and when it is resolved.
- **Post-Mortem**: Once resolved, communicate the incident status and root cause to all stakeholders.

---

## ✅ Next Steps

1. Test the **memory usage alert** in **Prometheus** by artificially increasing the load (e.g., stress-testing a container).
