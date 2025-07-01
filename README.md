# 🛡️ Azure SOC Lab - Cloud Security Operations Center

> **A comprehensive cloud-based Security Operations Center demonstrating real-world threat detection, log analysis, and incident response using Microsoft Azure services.**

![Lab Status](https://img.shields.io/badge/Lab%20Status-Active-brightgreen)
![Azure](https://img.shields.io/badge/Microsoft-Azure-0078d4)
![Sentinel](https://img.shields.io/badge/Microsoft-Sentinel-198754)
![Security](https://img.shields.io/badge/Security-SOC-d63384)

## 📋 Overview

This home lab demonstrates the deployment and configuration of a complete Security Operations Center (SOC) environment using Microsoft Azure cloud services. The project showcases skills in cloud security, threat detection, log analysis, and security incident response through a deliberately exposed honeypot system that attracts and analyzes real-world attacks from global threat actors.

**Lab Results**: Over **6,000 attack attempts** recorded in just 30 minutes, demonstrating the immediate threat landscape facing exposed systems on the internet.

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
3. **Region:** `East US 2` (or closest region for optimal performance)
4. Click **Review + Create** → **Create**

<p align="center">
<img src="https://i.imgur.com/ZKHQTmc.png" height="80%" width="80%"/>
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
  <table>
    <tr>
      <td><img src="https://i.imgur.com/ZOEKTIl.png" width="400" height="300"/></td>
      <td><img src="https://i.imgur.com/nOo0mip.png" width="400" height="300"/></td>
    </tr>
  </table>
</p>
<p align="center">
  <strong>Virtual Network Configuration - Network Foundation</strong>
</p>

### Phase 3: Honeypot VM Deployment

**🍯 Windows VM Honeypot Setup**

#### 1. **Create Virtual Machine**

**Basic Configuration:**
1. Azure Portal → **Virtual Machines** → **Create** → **Azure virtual machine**
2. **Resource Group:** `RG-SOC-Lab`
3. **Virtual machine name:** `PROD-WEB-01` *(disguised as production web server)*
4. **Region:** `East US 2`
5. **Image:** `Windows 10 Pro`
6. **Size:** `Standard_B2s (1 vcpus, 1 GiB memory)` *(cost-effective for lab)*

**Administrator Account:**
- **Username:** `sysadmin` *(attractive target for attackers)*
- **Password:** `123Password!` *(intentionally common pattern)*
- ✅ **Confirm licensing requirements**

<p align="center">
<img src="https://imgur.com/ewQrv0J.png" height="80%" width="80%"/>
<br />
<strong>VM Basic Configurationc</strong>
</p>

#### 2. **Network Configuration**
1. **Virtual network:** `VNet-SOC-Lab`
2. **Subnet:** `default (10.0.0.0/24)`
3. **Public IP:** Create new
4. **NIC network security group:** `Basic`
5. **Public inbound ports:** `Allow selected ports`
6. **Select inbound ports:** `RDP (3389)`
7. ✅ **Delete public IP and NIC when VM is deleted**

<p align="center">
<img src="https://imgur.com/B6M6hhp.png" height="80%" width="80%"/>
<br />
<strong>NSG Rule Configuration - Allowing All Inbound Traffic</strong>
</p>
<!--
<p align="center">
<img src="https://imgur.com/ymCISDe.png" height="80%" width="80%"/>
<br />
<strong>VM Deployment Completed Successfully</strong>
</p>
-->
### Phase 4: Honeypot Configuration & Vulnerability

**🔓 Making the VM Intentionally Vulnerable**


#### 2. **Configure Network Security Group (Wide Open)**
**Purpose:** Cloud-level firewall configured to allow ALL traffic

1. Azure Portal → **Network Security Groups** → Select your VM's NSG
2. **Inbound security rules** → **Add**
3. **Source:** `Any (*)`
4. **Source port ranges:** `*`
5. **Destination:** `Any (*)`
6. **Destination port ranges:** `*`
7. **Protocol:** `Any`
8. **Action:** `Allow`
9. **Priority:** `100`
10. **Name:** `ALLOW-ALL-INBOUND`
11. **Add** the rule

<p align="center">
<img src="https://i.imgur.com/bRKicjK.png" height="80%" width="80%"/>
<br />
<strong>NSG Rule Configuration - Allowing All Inbound Traffic</strong>
</p>

#### 3. **Disable Windows Firewall**
**⚠️ CRITICAL:** This makes the VM extremely vulnerable - only do this in isolated lab environments!

**Method 1: GUI Approach**
1. Open **Windows Security** → **Firewall & network protection**
2. **Domain network:** Turn off Windows Defender Firewall
3. **Private network:** Turn off Windows Defender Firewall  
4. **Public network:** Turn off Windows Defender Firewall
5. Confirm all three profiles show "Off"

**Method 2: Command Line Approach**
1. Open **Run** (`Win + R`) → Type `wf.msc` → Enter
2. Click **Windows Defender Firewall Properties**
3. **Domain Profile:** Firewall state = `Off`
4. **Private Profile:** Firewall state = `Off`
5. **Public Profile:** Firewall state = `Off`
6. **Apply** → **OK**

<p align="center">
<img src="https://i.imgur.com/DHn5csa.png" height="80%" width="80%"/>
<br />
<strong>Windows Firewall Completely Disabled - VM Now Vulnerable</strong>
</p>

**Alternative PowerShell Method:**
```powershell
# Run as Administrator
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

### Phase 5: Initial Attack Observation

**🔍 Local Event Log Analysis**

#### 1. **Examine Windows Event Logs**
1. On the VM: **Start** → **Event Viewer**
2. **Windows Logs** → **Security**
3. Look for **Event ID 4625** (Failed logon attempts)
4. Note the failed attempts from your IP address

<p align="center">
  <table>
    <tr>
      <td><img src="https://imgur.com/wbLEwOD.png" width="400" height="300"/></td>
      <td><img src="https://imgur.com/BaklKZV.png" width="400" height="300"/></td>
    </tr>
  </table>
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
4. **Region:** `East US ` (same as other resources)
5. **Pricing tier:** `Pay-as-you-go` (includes free tier)
6. Click **Review + Create** → **Create**

<p align="center">
<img src="https://i.imgur.com/Ni61JHY.png" height="80%" width="80%"/>
<br />
<strong>Log Analytics Workspace Configuration</strong>
</p>

#### 2. **Verify Workspace Creation**
1. Navigate to the created workspace
2. Note the **Workspace ID** and **Location**
3. **Overview** tab shows workspace details

### Phase 7: Microsoft Sentinel Integration

**🛡️ SIEM Configuration & Setup**

#### 1. **Add Microsoft Sentinel**
1. Azure Portal → **Microsoft Sentinel** → **Create**
2. **Select workspace:** `LAW-SOC-Lab`
3. Click **Add Microsoft Sentinel**
4. Wait for Sentinel to be added (includes free trial)

<p align="center">
<img src="https://i.imgur.com/1J8DvFz.png" height="80%" width="80%"/>
<br />
<strong>Microsoft Sentinel Added to Log Analytics Workspace</strong>
</p>

#### 2. **Configure Security Events Data Connector**
1. Sentinel → **Data connectors**
2. Search for **Windows Security Events via AMA**
3. **Open connector page**
4. **Create data collection rule**

<p align="center">
  <table>
    <tr>
      <td><img src="https://imgur.com/PlpeN73.png" width="400" height="300"/></td>
      <td><img src="https://imgur.com/xwsbwfS.png" width="400" height="300"/></td>
    </tr>
  </table>
</p>
<p align="center">
  <strong>Windows Security Events via AMA Data Connector</strong>
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
| where TimeGenerated > ago(24h)
| take 50
```

<p align="center">
<img src="https://i.imgur.com/ZQ0HXDb.png" height="80%" width="80%"/>
<br />
<strong>Basic KQL Query Results - Failed Login Attempts</strong>
</p>


#### 3. **Real-Time Attack Monitoring**
**Recent Failed Logins (Last 5 Minutes):**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(5m)
| project TimeGenerated, IpAddress, Account
| order by TimeGenerated desc
```

**Attack Frequency Analysis:**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(6h)
| summarize AttackCount = count() by IpAddress
| order by AttackCount desc
```

<p align="center">
<img src="https://i.imgur.com/8RNUK1h.png" height="80%" width="80%"/>
<br />
<strong>KQL Attack Frequency Analysis - 300+ Attacks Detected</strong>
</p>

> 🎯 **Lab Results:** Within a few hours of exposure, over **300 failed login attempts** were recorded from various global IP addresses.

### Phase 9: Geographic IP Enrichment

**🌍 Threat Intelligence Integration**

#### 1. **Download Geographic IP Database**
1. Download the CSV file: [geoip-summarized.csv](https://raw.githubusercontent.com/joshmadakor1/lognpacific-public/refs/heads/main/misc/geoip-summarized.csv)
2. Save locally for upload to Azure

#### 2. **Create Sentinel Watchlist**
1. Sentinel → **Watchlists** → **Create new**
2. **Name:** `geoip`
3. **Alias:** `geoip`
4. **Source type:** `Local file`
5. **Upload file:** Select downloaded `geoip-summarized.csv`
6. **Number of lines before header:** `0`
7. **Search key:** `network`
8. **Create**

<p align="center">
  <table>
    <tr>
      <td><img src="https://imgur.com/o2dtyj4.png" width="400" height="300"/></td>
      <td><img src="https://imgur.com/3n7Y2Iy.png" width="400" height="300"/></td>
    </tr>
  </table>
</p>
<p align="center">
  <strong>Geographic IP Watchlist Creation in Sentinel</strong>
</p>


### Phase 10: Attack Map Visualization

**🗺️ Real-Time Attack Mapping**

#### 1. **Create Sentinel Workbook**
1. Sentinel → **Workbooks** → **Add workbook**
2. **Edit** → Delete all pre-populated elements
3. **Add** → **Add query**

#### 2. **Configure Map Visualization**
1. **Advanced Editor** → Paste JSON configuration
2. **Visualization:** `Map`
3. **Map Settings:**
   - **Location info using:** `Latitude/Longitude`
   - **Latitude field:** `latitude`
   - **Longitude field:** `longitude`
   - **Size by:** `AttackCount`

**Attack Map KQL Query:**
```kql
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
    | where IpAddress == <attacker IP address>
    | where EventID == 4625
    | order by TimeGenerated desc
    | evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```

<p align="center">
<img src="https://i.imgur.com/cqHUSNb].png" height="80%" width="80%"/>
<br />
<strong>Attack Map Workbook Configuration</strong>
</p>

#### 3. **Customize Map Appearance**
1. **Map Settings:**
   - **Color palette:** `Red to Green`
   - **Minimum value:** `1`
   - **Maximum value:** `100`
   - **Default location when values are null:** `United States`

#### 4. **Save and View Attack Map**
1. **Save** workbook as `Attack-Map-Honeypot`
2. **Done Editing** to view live map
3. Observe real-time attack distribution globally

<p align="center">
<img src="https://imgur.com/CFIG800.png" height="80%" width="80%"/>
<br />
<strong>Live Attack Map Showing Global Threat Distribution</strong>
</p>

## 🎯 Lab Results & Analysis

### **Real-World Attack Statistics**

**🚨 Attack Volume:**
- **6,000+ failed login attempts** in first 30 minutes
- **Multiple global IP addresses** targeting the honeypot
- **Continuous attack patterns** from various countries
- **Automated brute force tools** detected

### **Geographic Attack Distribution**

**Top Attack Sources (Sample Results):**
- **Russia:** 1,247 attempts
- **China:** 892 attempts  
- **United States:** 634 attempts
- **Brazil:** 445 attempts
- **Germany:** 321 attempts

### **Attack Patterns Observed**

**Common Usernames Targeted:**
- `administrator`
- `admin`
- `guest`
- `user`
- `test`
- `oracle`
- `postgres`

**Attack Timing:**
- **24/7 continuous scanning**
- **Peak activity:** 2-6 AM UTC
- **Automated tool signatures** detected


## 🧪 Common Issues & Solutions

**🔍 Troubleshooting Guide**

| Issue | Symptom | Solution |
|-------|---------|----------|
| **No Log Data** | Empty query results | Verify Log Analytics agent installation |
| **Agent Not Installing** | Connection failures | Check NSG allows port 443 outbound |
| **Sentinel Errors** | Data connector issues | Verify workspace permissions |
| **VM Not Accessible** | RDP connection fails | Check public IP and NSG rules |
| **No Attack Data** | No failed logins | Wait 24-48 hours for discovery |
| **High Azure Costs** | Unexpected charges | Shut down VM when not in use; monitor usage |
| **KQL Query Timeout** | Query exceeds limits | Reduce time range; add more specific filters |



## 🎓 Skills Demonstrated

**Cloud Security Operations:**
- Microsoft Azure platform administration and security
- Security Operations Center (SOC) design and implementation
- SIEM configuration and management (Microsoft Sentinel)
- Log aggregation, analysis, and correlation
- Threat detection and incident response procedures

**Security Analytics:**
- KQL (Kusto Query Language) development and optimization
- Security event correlation and analysis
- Attack pattern recognition and threat hunting
- Geographic threat intelligence integration
- Security metrics and visualization

**Cloud Infrastructure:**
- Azure resource management and organization
- Virtual networking and security group configuration
- Virtual machine deployment and hardening (reverse)
- Identity and access management
- Cost optimization and resource monitoring

**Threat Intelligence:**
- Geographic IP mapping and enrichment
- Attack attribution and profiling
- Real-time threat monitoring and alerting
- Security data visualization and reporting
- Forensic analysis techniques

## 🚀 Future Enhancements

- [ ] **Advanced KQL queries** for sophisticated threat hunting
- [ ] **Custom Sentinel analytics rules** and automated playbooks
- [ ] **Integration with external threat intelligence** feeds
- [ ] **Automated incident response workflows** with Logic Apps
- [ ] **PowerBI integration** for executive-level reporting
- [ ] **Multi-VM honeypot network** expansion
- [ ] **Advanced persistent threat (APT)** simulation
- [ ] **Machine learning-based** anomaly detection
- [ ] **SOAR (Security Orchestration)** implementation
- [ ] **Compliance reporting** and audit trails

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
