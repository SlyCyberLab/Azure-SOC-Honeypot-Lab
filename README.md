# 🛡️ Azure SOC Lab - Cloud Security Operations Center
<!--
![GitHub repo size](https://img.shields.io/github/repo-size/SlyCyberLab/Azure-SOC-Lab)
![Last Commit](https://img.shields.io/github/last-commit/SlyCyberLab/Azure-SOC-Lab)
![License](https://img.shields.io/github/license/SlyCyberLab/Azure-SOC-Lab)
-->
> **A comprehensive cloud-based Security Operations Center demonstrating real-world threat detection, log analysis, and incident response using Microsoft Azure services.**

![Lab Status](https://img.shields.io/badge/Lab%20Status-Active-brightgreen)
![Azure](https://img.shields.io/badge/Microsoft-Azure-0078d4)
![Sentinel](https://img.shields.io/badge/Microsoft-Sentinel-198754)
![Security](https://img.shields.io/badge/Security-SOC-d63384)

## 📋 Overview

This home lab demonstrates the deployment and configuration of a complete Security Operations Center (SOC) environment using Microsoft Azure cloud services. The project showcases skills in cloud security, threat detection, log analysis, and security incident response through a deliberately exposed honeypot system that attracts real-world attacks.

### 🎯 Objectives Achieved
- ✅ Azure cloud infrastructure deployment and management
- ✅ Windows VM honeypot configuration for threat attraction
- ✅ Log Analytics Workspace setup and log forwarding
- ✅ Microsoft Sentinel SIEM integration and configuration
- ✅ Real-time security event monitoring and analysis
- ✅ Attack visualization and geolocation mapping
- ✅ KQL (Kusto Query Language) for security analytics
- ✅ Incident response workflow implementation

## 🏗️ Architecture

<p align="center">
<img src="[INSERT_NETWORK_DIAGRAM_LINK_HERE]" />
<br />
<strong>Azure SOC Lab Network Architecture - Honeypot with Centralized Logging & Security Monitoring</strong>
</p>

## 📖 Implementation Guide

### Phase 1: Azure Foundation Setup

**☁️ Azure Subscription & Resource Group**

#### 1. **Create Microsoft Azure Account**
1. Navigate to [Azure Portal](https://portal.azure.com)
2. Sign up for free Azure subscription
3. Complete account verification process

**📸 Screenshot Location:** `screenshots/01-azure-account-creation.png`

#### 2. **Create Resource Group**
1. In Azure Portal → **Resource Groups** → **Create**
2. **Resource Group Name:** `RG-SOC-Lab`
3. **Region:** Select closest region for optimal performance
4. Click **Review + Create** → **Create**

<p align="center">
<img src="[INSERT_SCREENSHOT_RG_CREATION]" height="80%" width="80%"/>
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
4. **Region:** Same as Resource Group
5. **Address Space:** `10.0.0.0/16` (default)
6. **Subnet:** `default (10.0.0.0/24)`
7. **Security:** Default settings
8. Click **Review + Create** → **Create**

<p align="center">
<img src="[INSERT_SCREENSHOT_VNET_CONFIG]" height="80%" width="80%"/>
<br />
<strong>Virtual Network Configuration</strong>
</p>

**📸 Screenshot Needed:** Virtual network creation process and final configuration

### Phase 3: Honeypot VM Deployment

**🍯 Windows VM Honeypot Setup**

#### 1. **Create Virtual Machine**

**Basic Configuration:**
1. Azure Portal → **Virtual Machines** → **Create** → **Azure virtual machine**
2. **Resource Group:** `RG-SOC-Lab`
3. **Virtual machine name:** `PROD-WEB-01` *(appears as production web server)*
4. **Region:** Same as previous resources
5. **Image:** `Windows 11 Pro`
6. **Size:** `Standard_B2s (2 vcpus, 4 GiB memory)` *(cost-effective for lab)*

<p align="center">
<img src="[INSERT_SCREENSHOT_VM_BASICS]" height="80%" width="80%"/>
<br />
<strong>VM Basic Configuration - Honeypot Disguised as Production Server</strong>
</p>

**Administrator Account:**
- **Username:** `sysadmin` *(attractive target for attackers)*
- **Password:** `1Password!` *(intentionally weak for honeypot)*
- ✅ **Confirm licensing requirements**

**📸 Screenshots Needed:**
- `screenshots/03-vm-basic-config.png`
- `screenshots/04-vm-admin-account.png`

#### 2. **Disk Configuration**
1. **Disk Type:** `Standard SSD` (sufficient for lab purposes)
2. Keep default settings for cost optimization
3. Click **Next: Networking**

#### 3. **Network Configuration**
1. **Virtual network:** `VNet-SOC-Lab`
2. **Subnet:** `default (10.0.0.0/24)`
3. **Public IP:** Create new
4. **NIC network security group:** `Basic`
5. **Public inbound ports:** `Allow selected ports`
6. **Select inbound ports:** `RDP (3389)`
7. ✅ **Delete public IP and NIC when VM is deleted**

<p align="center">
<img src="[INSERT_SCREENSHOT_VM_NETWORKING]" height="80%" width="80%"/>
<br />
<strong>VM Network Configuration - Exposing RDP to Internet</strong>
</p>

> ⚠️ **Security Note:** We're intentionally exposing RDP to the internet for honeypot purposes. Never do this in production!

#### 4. **Management & Monitoring**
1. **Boot diagnostics:** `Disable` *(reduces costs)*
2. **Enable OS guest diagnostics:** `Disable` *(we'll use custom logging)*
3. Keep other default settings
4. Click **Next: Advanced** → **Next: Tags** → **Next: Review + create**

#### 5. **Deploy VM**
1. Review all configurations
2. Click **Create**
3. Wait for deployment completion (5-10 minutes)

<p align="center">
<img src="[INSERT_SCREENSHOT_VM_DEPLOYMENT]" height="80%" width="80%"/>
<br />
<strong>VM Deployment in Progress</strong>
</p>

**📸 Screenshot Needed:** Successful VM deployment confirmation

### Phase 4: Honeypot Configuration

**🔓 Making the VM Vulnerable**

#### 1. **Connect to VM**
1. Navigate to your VM → **Connect** → **RDP**
2. Download RDP file and connect
3. Login with credentials: `sysadmin` / `1Password!`

<p align="center">
<img src="[INSERT_SCREENSHOT_RDP_CONNECTION]" height="80%" width="80%"/>
<br />
<strong>Initial RDP Connection to Honeypot VM</strong>
</p>

#### 2. **Disable Windows Firewall**
**⚠️ CRITICAL:** This makes the VM extremely vulnerable - only do this in isolated lab environments!

1. Open **Windows Security** → **Firewall & network protection**
2. **Domain network:** Turn off Windows Defender Firewall
3. **Private network:** Turn off Windows Defender Firewall  
4. **Public network:** Turn off Windows Defender Firewall
5. Confirm all three profiles show "Off"

<p align="center">
<img src="[INSERT_SCREENSHOT_FIREWALL_OFF]" height="80%" width="80%"/>
<br />
<strong>Windows Firewall Disabled - VM Now Vulnerable</strong>
</p>

**Alternative PowerShell Method:**
```powershell
# Run as Administrator
Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False
```

#### 3. **Create Network Security Group (NSG)**
**Purpose:** Cloud-level firewall that we'll configure to allow all traffic

1. Azure Portal → **Network security groups** → **Create**
2. **Resource group:** `RG-SOC-Lab`
3. **Name:** `NSG-SOC-Honeypot`
4. **Region:** Same as other resources
5. Click **Review + create** → **Create**

<p align="center">
<img src="[INSERT_SCREENSHOT_NSG_CREATION]" height="80%" width="80%"/>
<br />
<strong>Network Security Group Creation</strong>
</p>

#### 4. **Configure NSG Rules (Wide Open)**
1. Go to created NSG → **Inbound security rules**
2. **Add** new rule:
   - **Source:** `Any`
   - **Source port ranges:** `*`
   - **Destination:** `Any`
   - **Destination port ranges:** `*`
   - **Protocol:** `Any`
   - **Action:** `Allow`
   - **Priority:** `100`
   - **Name:** `ALLOW-ALL-INBOUND`
3. **Add** the rule

<p align="center">
<img src="[INSERT_SCREENSHOT_NSG_RULES]" height="80%" width="80%"/>
<br />
<strong>NSG Rule Configuration - Allowing All Traffic</strong>
</p>

#### 5. **Associate NSG with VM**
1. NSG → **Network interfaces** → **Associate**
2. Select your VM's network interface
3. Click **OK**

**Verification:**
- VM should now be accessible from internet on all ports
- Windows firewall disabled
- NSG allowing all traffic

**📸 Screenshots Needed:**
- `screenshots/08-firewall-disabled.png`
- `screenshots/09-nsg-rules-configured.png`
- `screenshots/10-nsg-associated.png`

### Phase 5: Log Analytics Workspace

**📊 Centralized Logging Infrastructure**

#### 1. **Create Log Analytics Workspace**
1. Azure Portal → **Log Analytics workspaces** → **Create**
2. **Resource group:** `RG-SOC-Lab`
3. **Name:** `LAW-SOC-Lab`
4. **Region:** Same as other resources
5. **Pricing tier:** `Pay-as-you-go` (free tier available)
6. Click **Review + Create** → **Create**

<p align="center">
<img src="[INSERT_SCREENSHOT_LAW_CREATION]" height="80%" width="80%"/>
<br />
<strong>Log Analytics Workspace Configuration</strong>
</p>

#### 2. **Configure Data Collection**
1. Navigate to Log Analytics Workspace → **Agents management**
2. Note the **Workspace ID** and **Primary Key** (needed for VM agent)
3. **Virtual machines** → Connect your honeypot VM
4. Click **Connect** to install monitoring agent

<p align="center">
<img src="[INSERT_SCREENSHOT_LAW_AGENT]" height="80%" width="80%"/>
<br />
<strong>Installing Log Analytics Agent on Honeypot VM</strong>
</p>

#### 3. **Enable Security Events Collection**
1. Log Analytics Workspace → **Data sources** → **Windows Event logs**
2. Add **Security** event log
3. Select **All Events** for comprehensive logging
4. **Save** configuration

**📸 Screenshots Needed:**
- `screenshots/11-law-creation.png`
- `screenshots/12-law-agent-installation.png`
- `screenshots/13-security-events-config.png`

### Phase 6: Microsoft Sentinel Integration

**🛡️ SIEM Configuration & Threat Detection**

#### 1. **Add Microsoft Sentinel**
1. Azure Portal → **Microsoft Sentinel** → **Create**
2. **Select workspace:** `LAW-SOC-Lab`
3. Click **Add Microsoft Sentinel**
4. Wait for Sentinel to be added to workspace

<p align="center">
<img src="[INSERT_SCREENSHOT_SENTINEL_SETUP]" height="80%" width="80%"/>
<br />
<strong>Microsoft Sentinel Added to Log Analytics Workspace</strong>
</p>

#### 2. **Configure Data Connectors**
1. Sentinel → **Data connectors**
2. **Security Events via Legacy Agent** → **Open connector page**
3. **Configuration:** Select **All Events**
4. **Apply Changes**

<p align="center">
<img src="[INSERT_SCREENSHOT_SENTINEL_CONNECTORS]" height="80%" width="80%"/>
<br />
<strong>Security Events Data Connector Configuration</strong>
</p>

#### 3. **Verify Data Flow**
1. Sentinel → **Logs**
2. Run basic KQL query to test:
```kql
SecurityEvent
| where TimeGenerated > ago(24h)
| take 10
```

**📸 Screenshots Needed:**
- `screenshots/14-sentinel-creation.png`
- `screenshots/15-data-connectors.png`
- `screenshots/16-initial-kql-query.png`

## 🎯 Expected Results & Analysis

### **Real-World Attack Detection**

After deployment, the honeypot will begin attracting real attacks within hours:

**Common Attack Patterns:**
- **RDP Brute Force:** Failed login attempts from global IP addresses
- **Port Scanning:** Automated discovery attempts
- **Credential Stuffing:** Dictionary-based password attacks
- **Geographic Distribution:** Attacks from multiple countries

### **KQL Queries for Analysis**

**Failed RDP Login Attempts:**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| summarize FailureCount = count() by IpAddress, Account
| sort by FailureCount desc
```

**Geographic Attack Mapping:**
```kql
SecurityEvent
| where EventID == 4625
| where TimeGenerated > ago(24h)
| extend GeoInfo = geo_info_from_ip_address(IpAddress)
| project TimeGenerated, IpAddress, Account, GeoInfo.country, GeoInfo.state, GeoInfo.city
```

**📸 Future Screenshots Needed:**
- `screenshots/17-attack-detection-dashboard.png`
- `screenshots/18-failed-login-analysis.png`
- `screenshots/19-geographic-attack-map.png`
- `screenshots/20-kql-query-results.png`

## 📊 Lab Capabilities

### 🔐 Security Monitoring
- Real-time failed login detection
- Geographic attack source mapping
- Automated threat intelligence correlation
- Custom alert rule configuration
- Incident response workflow automation

### 📈 Analytics & Reporting
- KQL-based security event analysis
- Attack pattern identification
- Threat actor profiling
- Security metrics dashboards
- Executive-level reporting

### 🔧 Cloud Security Tools
- Microsoft Sentinel SIEM
- Log Analytics Workspace
- Azure Security Center integration
- Threat intelligence feeds
- Automated response playbooks

## 🧪 Common Issues & Solutions

**🔍 Troubleshooting Guide**

| Issue | Symptom | Solution |
|-------|---------|----------|
| **No Log Data** | Empty query results | Verify Log Analytics agent installation |
| **Agent Not Installing** | Connection failures | Check NSG allows port 443 outbound |
| **Sentinel Errors** | Data connector issues | Verify workspace permissions |
| **VM Not Accessible** | RDP connection fails | Check public IP and NSG rules |
| **No Attack Data** | No failed logins | Wait 24-48 hours for discovery |

## 📁 Repository Structure

```
📦 Azure-SOC-Lab/
├── 📂 screenshots/
│   ├── 01-azure-account-creation.png
│   ├── 02-resource-group-creation.png
│   ├── 03-vm-basic-config.png
│   ├── 04-vm-admin-account.png
│   ├── 05-vm-networking.png
│   ├── 06-vm-deployment.png
│   ├── 07-rdp-connection.png
│   ├── 08-firewall-disabled.png
│   ├── 09-nsg-rules-configured.png
│   ├── 10-nsg-associated.png
│   ├── 11-law-creation.png
│   ├── 12-law-agent-installation.png
│   ├── 13-security-events-config.png
│   ├── 14-sentinel-creation.png
│   ├── 15-data-connectors.png
│   ├── 16-initial-kql-query.png
│   ├── 17-attack-detection-dashboard.png
│   ├── 18-failed-login-analysis.png
│   ├── 19-geographic-attack-map.png
│   └── 20-kql-query-results.png
├── 📂 kql-queries/
│   ├── failed-login-analysis.kql
│   ├── geographic-mapping.kql
│   └── attack-pattern-detection.kql
├── 📂 documentation/
│   ├── network-diagram.svg
│   └── architecture-overview.md
├── README.md
└── LICENSE
```

## 🎓 Skills Demonstrated

**Cloud Security:**
- Microsoft Azure platform administration
- Security Operations Center (SOC) design and implementation
- SIEM configuration and management
- Log aggregation and analysis
- Threat detection and incident response

**Security Analytics:**
- KQL (Kusto Query Language) development
- Security event correlation and analysis
- Attack pattern recognition
- Threat intelligence integration
- Security metrics and reporting

**Cloud Infrastructure:**
- Azure resource management and organization
- Virtual networking and security groups
- Virtual machine deployment and configuration
- Identity and access management
- Cost optimization and resource monitoring

**Incident Response:**
- Real-time threat detection
- Security event investigation
- Attack attribution and profiling
- Forensic analysis techniques
- Documentation and reporting

## 🚀 Future Enhancements

- [ ] Advanced KQL queries for sophisticated threat hunting
- [ ] Custom Sentinel analytics rules and playbooks
- [ ] Integration with threat intelligence feeds
- [ ] Automated incident response workflows
- [ ] PowerBI dashboard for executive reporting
- [ ] Multi-VM honeypot network expansion
- [ ] Advanced persistent threat (APT) simulation
- [ ] Integration with other Azure security services

## 📞 Connect

[![LinkedIn](https://img.shields.io/badge/LinkedIn-View%20Profile-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/emsly-s-482794196)  
📧 [slycyber7@gmail.com](mailto:slycyber7@gmail.com)  
<!--🌐 [slycyber.com](https://slycyber.com) -->

---
<p align="center">
  ⭐️ If this lab helped or inspired you, consider giving it a star.
</p>

> This SOC lab environment demonstrates enterprise-level security monitoring capabilities and can be extended for advanced threat hunting, incident response training, and security analyst skill development.
