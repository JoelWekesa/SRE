# Runbook for Site Unreachable Alert

## 1️⃣ Runbook Overview

### **Alert Name**: Site Unreachable Alert

**Alert Trigger**: When a monitored site (web application or service) cannot be reached (e.g., HTTP request failure, timeout, or DNS resolution failure).

---

## 2️⃣ Objective

To ensure rapid identification and resolution of an unreachable site, minimizing downtime and ensuring continuous availability for users.

---

## 3️⃣ Tools Required

- **Prometheus** and **Grafana** (for monitoring and visualizing metrics)
- **Curl** or **wget** (for manual testing)
- **SSH access** to the affected servers
- **DNS configuration tools** (if DNS issues are suspected)

---

## 4️⃣ Alert Details

- **Threshold**: Site is unreachable for more than 2 minutes.
- **Duration**: 2 minutes of failed requests or timeouts.
- **Prometheus Query for Alert**:

  ```promql
  http_requests_total{status=~"5..", job="your_job_name"} > 0
  ```

- **Alert severity**: Extremely Critical

---

## 5️⃣ Actions to Take When Alert is Triggered

### **Step 1: Verify the Alert**

- **Check if the site is genuinely unreachable**:
  - Use **curl** or **wget** to manually check the site:
    ```bash
    curl -I http://your-site-url
    ```
    Or:
    ```bash
    wget --spider http://your-site-url
    ```

### **Step 2: Investigate the Cause**

- **Network Connectivity**:
  - Check if the server has internet connectivity (e.g., check DNS resolution, firewall rules, or routing issues).
  - Use `ping` to check if the server can resolve and reach external networks:
    ```bash
    ping google.com
    ```
- **Web Server Status**:

  - Check if the web server (Nginx) is running and healthy:
    ```bash
    systemctl status nginx
    ```

- **Check Logs**:
  - Review **web server logs** (`/var/log/nginx/error.log`, `/var/log/apache2/error.log`) for any errors.
  - Check **application logs** for issues that could have caused the service to go down.
- **DNS Issues**:
  - Check if DNS resolution is working correctly:
    ```bash
    nslookup your-site-url
    ```

### **Step 3: Take Immediate Actions**

- **Option 1: Restart the Web Server**  
  If the web server is not responding, restart it:

  ```bash
  systemctl restart nginx
  ```

- **Option 2: Check Firewall and Security Groups**  
  Ensure there are no firewall rules blocking HTTP/HTTPS traffic.

  - For **UFW**:
    ```bash
    ufw status
    ```
  - For **AWS Security Groups**, ensure the inbound rules allow HTTP/HTTPS traffic.

- **Option 3: Investigate DNS Issues**  
  If DNS resolution is failing:
  - Check if DNS is configured properly.
  - If the site’s domain name is using an external DNS provider, check their status and configuration.
- **Option 4: Check Database or Application Health**  
  If your site relies on databases or other services, ensure they are online and functioning correctly.
  - For database checks, verify service status:
    ```bash
    systemctl status mysql
    systemctl status postgresql
    ```

### **Step 4: Mitigate and Prevent Future Incidents**

- **Improve Availability**:
  - Set up **load balancing** to distribute traffic across multiple servers.
- **Setup Health Checks**:

  - Implement automated health checks with **Prometheus** or **Grafana** to monitor the health of critical services.
  - Configure **HTTP/HTTPS health checks** for your web servers to automatically restart if the service is unhealthy.

- **Enhance DNS Resilience**:
  - Consider using **DNS failover** services to automatically switch to a backup DNS server in case of issues with the primary DNS.
  - Use **CDN services** (e.g., Cloudflare) to ensure availability and reduce the risk of DNS issues.

---

## 6️⃣ Post-Incident Review

- **Root Cause Analysis**: Was the site unreachable due to a server failure, DNS issue, network problem, or application error?
- **Action Plan**: What measures will be put in place to prevent this from happening again? (e.g., DNS improvements, load balancing, application fixes)
- **Document Lessons Learned**: Update the runbook with any new insights or steps that need to be added.

---

## 7️⃣ Communication During the Incident

- **Notify the team** An alert is triggered to Telegram when the incident is detected and when it is resolved.
- **Post-Mortem**: Once resolved, communicate the incident status and root cause to all stakeholders.

---

## ✅ Next Steps

1. Test the **site unreachable alert** by simulating downtime using network throttling or web server failure.

