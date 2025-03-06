
# Endpoint Security using FleetDM and Osquery

**Project Overview**

This project sets up FleetDM as a centralized server on a Linux environment and configures Osquery on Windows endpoints to collect and monitor system information. The FleetDM server will act as a management and logging tool for Osquery, allowing remote querying and monitoring of endpoint security.

**Prerequisites**

**Linux Server (FleetDM host):**

* Ubuntu 20.04+ or CentOS 7+
* MySQL or MariaDB database
* FleetDM binaries (without Docker)
* Golang


**Windows Endpoint:**
* Windows 10/11 or Windows Server 2016+
* Windows Firewall configured to allow communication with FleetDM
* Osquery MSI package


## Step 1: Install FleetDM on Linux Server








**1.1 Update System and Install Dependencies**
```bash
  sudo apt update && sudo apt upgrade -y  //To update & upgrade Linux package

  sudo apt install -y unzip curl mysql-server
```


**1.2 Install Golang (Required for FleetDM)**
```bash
wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.20.5.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

**1.3 Install FleetDM Server**
```bash
curl -LO https://github.com/fleetdm/fleet/releases/latest/download/fleet.zip
unzip fleet.zip -d fleet-server
cd fleet-server
chmod +x fleet
```


**1.4 Configure MySQL Database**
```bash
sudo systemctl start mysql
sudo mysql -u root -p
CREATE DATABASE fleet;
CREATE USER 'fleet'@'localhost' IDENTIFIED BY 'strongpassword';
GRANT ALL PRIVILEGES ON fleet.* TO 'fleet'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
**1.5 Run FleetDM Server**
```bash
./fleet serve --config fleet.yml
```

## Step 2: Install and Configure Osquery on Windows Endpoint

**2.1 Download and Install Osquery**

* Download Osquery MSI from Osquery official site.
* Install using default settings.

**2.2 Configure Osquery to Communicate with FleetDM**
* Edit ```bash osquery.flags``` file located in ```bash C:\Program Files\osquery```.
```bash
--tls_hostname=<Fleet_Server_IP>
--tls_server_certs=C:\path\to\fleet.pem
--enroll_secret_path=C:\Program Files\osquery\secret.txt
--host_identifier=hostname
--enable_windows_events=true
```


* Add the enroll_secret obtained from FleetDM.
* Restart Osquery service:
```bash 
Restart-Service osqueryd
```

## Step 3: Verify Connection
* Log in to FleetDM UI.
* Check if the Windows endpoint appears in the dashboard. 
* Run test queries using the FleetDM web interface. 


## Step 4: Running Queries on Windows Endpoint
Example query to check running processes:

```bash
SELECT name, path, pid FROM processes WHERE name LIKE 'chrome%';
```



# Troubleshooting
If occurred
* Ensure FleetDM server is running (ps aux | grep fleet).
* Check firewall rules and open necessary ports (default: 443 or 8080).
* Validate Windows Osquery logs (C:\Program Files\osquery\log\osqueryd.results.log).




# Conclusion
This project, built with FleetDM and OSQuery, brings a cutting-edge approach to endpoint security, offering organizations the ability to strengthen their defenses with real-time, granular visibility and proactive threat detection. By combining FleetDM’s efficient agent management with OSQuery’s powerful query engine, this solution provides a seamless way to monitor, secure, and respond to threats across all endpoints in an organization.

In today’s rapidly evolving threat landscape, securing endpoints is crucial, as they are often the first line of defense against cyberattacks. This project empowers security teams by offering real-time insights into endpoint health, configuration, and security posture, making it easier to identify and mitigate vulnerabilities before they can be exploited. With the ability to execute custom queries across thousands of endpoints simultaneously, organizations can rapidly detect abnormal activity and ensure continuous compliance with security policies.

By integrating this solution into their environment, companies can gain a competitive advantage in their security operations, improving efficiency and reducing the time to detect and respond to incidents. The ability to perform continuous monitoring at scale, paired with detailed visibility into endpoint data, ensures that security teams are not only reactive but also proactive in managing risks.

Ultimately, this project adds significant value by not only securing endpoints but also enhancing the operational effectiveness of SOC teams, fostering a culture of vigilance and resilience. As cybersecurity threats become more sophisticated, this solution provides organizations with the tools needed to stay ahead of potential risks and protect their critical assets.













    
