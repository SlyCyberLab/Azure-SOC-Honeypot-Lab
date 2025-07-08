# 🛡️ Azure SOC Lab - Cloud Security Operations Center

> **A comprehensive cloud-based Security Operations Center demonstrating real-world threat detection, log analysis, and incident response using Microsoft Azure services.**

![Lab Status](https://img.shields.io/badge/Lab%20Status-Active-brightgreen)
![Azure](https://img.shields.io/badge/Microsoft-Azure-0078d4)
![Sentinel](https://img.shields.io/badge/Microsoft-Sentinel-198754)
![Security](https://img.shields.io/badge/Security-SOC-d63384)

## 📋 Overview

This home lab demonstrates the deployment and configuration of a complete Security Operations Center (SOC) environment using Microsoft Azure cloud services. The project showcases skills in cloud security, threat detection, log analysis, and security incident response through a deliberately exposed honeypot system that attracts and analyzes real-world attacks from global threat actors.

**Lab Results**: Over **6,000 attack attempts** recorded in just a couple of hours, demonstrating the immediate threat landscape facing exposed systems on the internet.

### 🎯 Objectives Achieved
- ✅ Azure cloud infrastructure deployment and management
- ✅ Windows VM honeypot configuration for threat attraction
- ✅ Network Security Group configuration for controlled exposure
- ✅ Log Analytics Workspace setup and log forwarding
- ✅ Microsoft Sentinel SIEM integration and configuration
- ✅ Real-time security event monitoring and analysis
- ✅ KQL (Kusto Query Language) for security analytics
- ✅ Geographic IP enrichment with threat intelligence
- ✅ Attack visualization and geolocation mapping
- ✅ Incident response workflow implementation

## 🏗️ Architecture

<p align="center">
<img src="https://i.imgur.com/dqCBk4H.png" />
<br />
<strong>Azure SOC Lab Network Architecture - Honeypot with Centralized Logging & Security Monitoring</strong>
</p>

## 📖 Implementation Guide

### Phase 1: Azure Foundation Setup

**☁️ Azure Subscription & Resource Group**

#### 1. **Create Microsoft Azure Account**
1. Navigate to [Azure Portal](https://portal.azure.com)
2. Sign up for free Azure subscription ($200 credit for 30 days)
3. Complete account verification process

#### 2. **Create Resource Group**
1. In Azure Portal → **Resource Groups** → **Create**
2. **Resource Group Name:** `RG-SOC-Lab`
3. **Region:** `East US` (or closest region for optimal performance)
4. Click **Review + Create** → **Create**

<p align="center">
  <img src="https://i.imgur.com/ZKHQTmc.png" alt="Resource Group Creation" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Resource Group Creation - Organizing Cloud Resources</strong>
</p>

> 💡 **Best Practice:** Resource Groups act as logical containers for Azure resources, enabling organized management and cost tracking.

### Phase 2: Network Infrastructure

**🌐 Virtual Network Configuration**

#### 1. **Create Virtual Network**
1. Navigate to **Virtual Networks** → **Create**
2. **Resource Group:** `RG-SOC-Lab`
3. **Name:** `VNet-SOC-Lab`
4. **Region:** `East US 2` (same as Resource Group)
5. **Address Space:** `10.0.0.0/16` (default)
6. **Subnet:** `default (10.0.0.0/24)`
7. **Security:** Default settings
8. Click **Review + Create** → **Create**

<p align="center">
  <img src="https://i.imgur.com/ZOEKTIl.png" alt="Virtual Network Configuration" style="max-width: 500px; height: auto;" />
  <br><br>
  <img src="https://i.imgur.com/nOo0mip.png" alt="Network Foundation Setup" style="max-width: 500px; height: auto;" />
</p>
<p align="center">
  <strong>Virtual Network Configuration - Network Foundation</strong>
</p>


### Phase 3: Honeypot VM Deployment

**🍯 Windows VM Honeypot Setup**

#### 1. **Create Virtual Machine**
1. Azure Portal → **Virtual Machines** → **Create** → **Azure virtual machine**
2. **Resource Group:** `RG-SOC-Lab`
3. **Virtual machine name:** `PROD-WEB-01` *(disguised as production web server)*
4. **Region:** `East US 2`
5. **Image:** `Windows 10 Pro`
6. **Size:** `Standard_B2s (1 vcpu, 1 GiB memory)` *(cost-effective for lab)*
7. **Username:** `sysadmin` / **Password:** `12Password!@`
8. **Public inbound ports:** Allow RDP (3389)
9. **Review + Create** → **Create**

<p align="center">
  <img src="https://i.imgur.com/ewQrv0J.png" alt="VM Basic Configuration" style="max-width: 70%; height: auto;" />
  <br />
  <strong>VM Basic Configuration - Honeypot Disguised as Production Server</strong>
</p>

#### 2. **Network Configuration**
1. **Virtual network:** `VNet-SOC-Lab`
2. **Subnet:** `default (10.0.0.0/24)`
3. **Public IP:** Create new
4. **NIC network security group:** `Basic`
5. **Public inbound ports:** `Allow selected ports`
6. **Select inbound ports:** `RDP (3389)`

<p align="center">
  <img src="https://i.imgur.com/B6M6hhp.png" alt="VM Network Configuration" style="max-width: 70%; height: auto;" />
  <br />
  <strong>VM Network Configuration</strong>
</p>

### Phase 4: Honeypot Configuration & Vulnerability

**🔓 Making the VM Intentionally Vulnerable**

#### 1. **Configure Network Security Group (Wide Open)**
1. Azure Portal → **Network Security Groups** → Select your VM's NSG
2. **Inbound security rules** → **Add**
3. **Source:** `Any (*)` | **Destination:** `Any (*)`
4. **Protocol:** `Any` | **Action:** `Allow` | **Priority:** `100`
5. **Name:** `ALLOW-ALL-INBOUND` → **Add**

<p align="center">
  <img src="https://i.imgur.com/bRKicjK.png" alt="NSG Rule Configuration" style="max-width: 70%; height: auto;" />
  <br />
  <strong>NSG Rule Configuration - Allowing All Inbound Traffic</strong>
</p>

#### 2. **Disable Windows Firewall**
1. RDP to VM using `sysadmin` / `12Password!@`
2. **Windows Security** → **Firewall & network protection**
3. Turn off firewall for **all three profiles** (Domain, Private, Public)

<p align="center">
  <img src="https://i.imgur.com/DHn5csa.png" alt="Windows Firewall Disabled" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Windows Firewall Completely Disabled - VM Now Vulnerable</strong>
</p>

> ⚠️ **Security Warning:** This configuration makes the VM extremely vulnerable. Only use in isolated lab environments.

### Phase 5: Initial Attack Observation

**🔍 Local Event Log Analysis**

#### 1. **Examine Windows Event Logs**
1. On the VM: **Start** → **Event Viewer**
2. **Windows Logs** → **Security**
3. Look for **Event ID 4625** (Failed logon attempts)
4. Note the failed attempts from external IP addresses


<p align="center">
  <img src="https://i.imgur.com/wbLEwOD.png" alt="Event Viewer Failed Logins" style="max-width: 500px; height: auto;" />
  <br><br>
  <img src="https://i.imgur.com/BaklKZV.png" alt="Event ID 4625 Details" style="max-width: 500px; height: auto;" />
</p>
<p align="center">
  <strong>Event Viewer Showing Failed Login Attempts (Event ID 4625)</strong>
</p>


> 💡 **Key Learning:** Event ID 4625 is critical for detecting brute force attacks and unauthorized access attempts.

### Phase 6: Log Analytics Workspace

**📊 Centralized Logging Infrastructure**

#### 1. **Create Log Analytics Workspace**
1. Azure Portal → **Log Analytics workspaces** → **Create**
2. **Resource group:** `RG-SOC-Lab`
3. **Name:** `LAW-SOC-Lab`
4. **Region:** `East US` (same as other resources)
5. **Pricing tier:** `Pay-as-you-go` (includes free tier)
6. Click **Review + Create** → **Create**

<p align="center">
  <img src="https://i.imgur.com/Ni61JHY.png" alt="Log Analytics Workspace Configuration" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Log Analytics Workspace Configuration</strong>
</p>

### Phase 7: Microsoft Sentinel Integration

**🛡️ SIEM Configuration & Setup**

#### 1. **Add Microsoft Sentinel**
1. Azure Portal → **Microsoft Sentinel** → **Create**
2. **Select workspace:** `LAW-SOC-Lab`
3. Click **Add Microsoft Sentinel**
4. Wait for Sentinel to be added (includes free trial)

<p align="center">
  <img src="https://i.imgur.com/1J8DvFz.png" alt="Microsoft Sentinel Added to Log Analytics Workspace" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Microsoft Sentinel Added to Log Analytics Workspace</strong>
</p>

#### 2. **Configure Security Events Data Connector**
1. Sentinel → **Data connectors**
2. Search for **Windows Security Events via AMA**
3. **Open connector page** → **Create data collection rule**
4. Select your honeypot VM → **All Security Events** → **Create**

<p align="center">
  <img src="https://i.imgur.com/PlpeN73.png" alt="Windows Security Events Configuration" style="max-width: 500px; height: auto;" />
  <br><br>
  <img src="https://i.imgur.com/xwsbwfS.png" alt="AMA Data Connector Setup" style="max-width: 500px; height: auto;" />
</p>
<p align="center">
  <strong>Windows Security Events via AMA Data Connector Configuration</strong>
</p>


### Phase 8: KQL Query Development & Log Analysis

**🔍 Security Event Analysis with KQL**

#### 1. **Initial Log Verification**
1. Sentinel → **Logs**
2. Run basic query to verify data ingestion:
```kql
SecurityEvent
| take 10
```

#### 2. **Failed Login Analysis**
**Basic Failed Login Query:**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(5d)
| project TimeGenerated, IpAddress, Account, Computer
| order by TimeGenerated desc
```

<p align="center">
  <img src="https://i.imgur.com/IWtVuJp.png" alt="Basic KQL Query Results - Failed Login Attempts" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Basic KQL Query Results - Failed Login Attempts</strong>
</p>

#### 3. **Real-Time Attack Monitoring**

**Attack Frequency Analysis:**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(6h)
| summarize AttackCount = count() by IpAddress
| where AttackCount > 5
| order by AttackCount desc
```

<p align="center">
  <img src="https://i.imgur.com/w2x0UiT.png" alt="KQL Attack Frequency Analysis" style="max-width: 70%; height: auto;" />
  <br />
  <strong>KQL Attack Frequency Analysis - 5,000+ Attacks Detected</strong>
</p>

> 🎯 **Lab Results:** Within a few hours of exposure, over **5,000 failed login attempts** were recorded from various global IP addresses.

### Phase 9: Geographic IP Enrichment

**🌍 Threat Intelligence Integration**

#### 1. **Create Sentinel Watchlist**
1. Download [geoip-summarized.csv](https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv)
2. Sentinel → **Watchlists** → **Create new**
3. **Name:** `geoip` | **Alias:** `geoip`
4. **Source type:** `Local file` | **Upload file:** geoip-summarized.csv
5. **Search key:** `network` → **Create**

<p align="center">
  <img src="https://i.imgur.com/o2dtyj4.png" alt="Geographic IP Watchlist Creation" style="max-width: 500px; height: auto;" />
  <br><br>
  <img src="https://i.imgur.com/3n7Y2Iy.png" alt="54,000 IP Records Imported" style="max-width: 500px; height: auto;" />
</p>
<p align="center">
  <strong>Geographic IP Watchlist Creation - 54,000 IP Records Imported</strong>
</p>

#### 2. **Geographic Enrichment Query**
```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
| project TimeGenerated, IpAddress, Account, city, country, latitude, longitude
| order by TimeGenerated desc
```

### Phase 10: Attack Map Visualization

**🗺️ Real-Time Attack Mapping**

#### 1. **Create Sentinel Workbook**
1. Sentinel → **Workbooks** → **Add workbook**
2. **Edit** → Delete all pre-populated elements
3. **Add** → **Add query**

#### 2. **Configure Map Visualization**
**Attack Map KQL Query:**
```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(1h)
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
| summarize AttackCount = count() by IpAddress, city, country, latitude, longitude
| where AttackCount > 0 and isnotempty(latitude) and isnotempty(longitude)
```

<p align="center">
  <img src="https://i.imgur.com/cqHUSNb.png" alt="Attack Map Workbook Configuration" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Attack Map Workbook Configuration</strong>
</p>

#### 3. **Live Attack Map**
1. **Visualization:** Map
2. **Location info using:** Latitude/Longitude
3. **Size by:** AttackCount
4. **Save** workbook as `Attack-Map-Honeypot`

<p align="center">
  <img src="https://i.imgur.com/R6t0Vki.png" alt="Live Attack Map Showing Global Threat Distribution" style="max-width: 70%; height: auto;" />
  <br />
  <strong>Live Attack Map Showing Global Threat Distribution</strong>
</p>

## 📊 Key KQL Queries for SOC Analysis

### **Failed Login Monitoring**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| summarize FailureCount = count() by IpAddress, Account
| where FailureCount > 5
| order by FailureCount desc
```

### **Geographic Attack Analysis**
```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network)
| summarize AttackCount = count() by country
| order by AttackCount desc
```

### **Hourly Attack Trends**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| summarize AttackCount = count() by bin(TimeGenerated, 1h)
| render timechart
```

## 🧪 Common Issues & Solutions

**🔍 Troubleshooting Guide**

| Issue | Symptom | Solution |
|-------|---------|----------|
| **No Log Data** | Empty query results | Verify Azure Monitoring Agent installation |
| **Agent Not Installing** | Connection failures | Check NSG allows port 443 outbound |
| **Sentinel Errors** | Data connector issues | Verify workspace permissions |
| **VM Not Accessible** | RDP connection fails | Check public IP and NSG rules |
| **No Attack Data** | No failed logins | Wait 24-48 hours for discovery |
| **High Azure Costs** | Unexpected charges | Shut down VM when not in use; monitor usage |
| **KQL Query Timeout** | Query exceeds limits | Reduce time range; add more specific filters |


## 💡 Key Learnings

**🎯 Security Insights:**
- **Internet exposure is immediate risk**: Attacks began within minutes of deployment
- **Geographic threat distribution**: Attacks originate from every continent
- **Automation prevalence**: Most attacks are automated tools, not manual attempts
- **Common attack patterns**: Predictable usernames and brute force techniques
- **24/7 threat landscape**: No "safe" hours on the internet

**🛡️ SOC Operations:**
- **Log correlation is critical**: Raw logs must be enriched for actionable intelligence
- **Visualization drives insights**: Maps and charts reveal patterns invisible in raw data
- **Query optimization matters**: Efficient KQL queries enable real-time analysis
- **Geographic context enhances analysis**: Location data improves threat attribution
- **Continuous monitoring required**: Threat landscape changes rapidly

## 📞 Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-View%20Profile-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/emsly-s-482794196)  
📧 [slycyber7@gmail.com](mailto:slycyber7@gmail.com)  
<!--🌐 [slycyber.com](https://slycyber.com) -->

---
<p align="center">
  ⭐️ If this lab helped or inspired you, consider giving it a star.
</p>

> This SOC lab environment demonstrates enterprise-level security monitoring capabilities and can be extended for advanced threat hunting, incident response training, and security analyst skill development. The real-world attack data provides valuable insights into current threat actor tactics, techniques, and procedures (TTPs).
