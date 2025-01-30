
# Runbook for High CPU Usage Alert

## 1️⃣ Runbook Overview

### **Alert Name**: High CPU Usage Alert  
**Alert Trigger**: When CPU usage exceeds the defined threshold for more than 2 minutes.

---

## 2️⃣ Objective

To reduce the impact of high CPU usage by identifying the root cause and taking corrective actions to restore the system to normal operating conditions.

---

## 3️⃣ Tools Required

- **Prometheus** and **Grafana** (for monitoring and visualizing metrics)
- **Docker** (for container management)
- **System logs** and **application logs** (for deeper insights)
- **SSH access** to the affected servers

---

## 4️⃣ Alert Details

- **Threshold**: 90% CPU usage
- **Duration**: 2 minutes
- **Prometheus Query for Alert**:  

- **Alert severity**: Critical

---

## 5️⃣ Actions to Take When Alert is Triggered

### **Step 1: Verify the Alert**
- **Check if the alert is genuine**:
  - Open **Grafana** and confirm the CPU usage of the container/instance.

### **Step 2: Analyze the Root Cause**
- **Identify the container causing the issue**:
  - Review the **container CPU usage** in Grafana.
  - Isolate which container is using high CPU.

- **Check application logs** for any anomalies or resource-heavy processes.
- **Inspect system logs** (`/var/log/syslog`, `/var/log/dmesg`) for any errors or unusual behavior.
- **Check for memory constraints**:
  - Sometimes high CPU usage is tied to memory constraints, leading to thrashing. Check if the container is also near memory limits.

### **Step 3: Take Immediate Actions**
- **Option 1: Kill or Restart the Container**  
  If you identify the container causing the issue:
  ```bash
  docker container restart your-container-name
  ```
  This can temporarily resolve the issue if the high CPU usage is due to a transient process or application bug.

- **Option 2: Scale the Application**  
  If high traffic is causing the CPU usage:
  - **Docker Compose**: Increase the number of replicas in your `docker-compose.yml`.

### **Step 4: Mitigate and Prevent Future Incidents**
- **Review container resource limits** (CPU and memory):
  - Set **CPU limits** and **requests** to prevent containers from consuming excessive resources.
  - Example Docker Compose:
    ```yaml
    services:
      app:
        image: your-image
        deploy:
          resources:
            limits:
              cpus: "1.0"
            reservations:
              cpus: "0.5"
    ```

- **Optimize application performance**:
  - Check for inefficient algorithms or memory leaks that could be consuming too much CPU.
  - Consider using **async processing** or **caching** strategies to reduce load.

---

## 6️⃣ Post-Incident Review
- **Root Cause Analysis**: Was the high CPU usage due to an application bug, traffic spike, or resource mismanagement?
- **Action Plan**: What measures will be put in place to prevent this from happening again? (e.g., scaling, optimization, alert tuning)
- **Document Lessons Learned**: Update the runbook with any new insights or steps that need to be added.

---

## 7️⃣ Communication During the Incident
- **Notify the team** An alert is triggered to Telegram when the incident is detected and when it is resolved.
- **Post-Mortem**: Once resolved, communicate the incident status and root cause to all stakeholders.

---

## ✅ Next Steps
1. Test the **CPU usage alert** in **Prometheus** by artificially increasing the load (e.g., stress-testing a container).
