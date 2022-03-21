# Final-Project
This is a private github repository meant to submit all of my findings and answers to the activities related to the Final Project.

# Blue Team: Summary of Operations

## Table of Contents
- Network Topology
- Description of Targets
- Monitoring the Targets
- Patterns of Traffic & Behavior
- Suggestions for Going Further

### Network Topology
_TODO: Fill out the information below._

The following machines were identified on the network:
- TARGET1
  - **Operating System**: Linux
  - **Purpose**: Runs Apache webserver 
  - **IP Address**:192.168.1.110
- TARGET2
  - **Operating System**: Linux
  - **Purpose**: Runs Apache webserver
  - **IP Address**:192.168.1.115
- Kali
  - **Operating System**: Linux
  - **Purpose**: Attacking Machine
  - **IP Address**: 192.168.1.90
- Capstone
  - **Operating System**: Linux
  - **Purpose**: Runs Apache webserver
  - **IP Address**: 192.168.1.105
- ELK
  - **Operating System**: Linux
  - **Purpose**: Runs Elasticsearch on the target machines.
  - **IP Address**: 19.168.1.100

### Description of Targets
_TODO: Answer the questions below._

The target of this attack was: `Target 1` 192.168.1.110.

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:
- **Excessive HTTP Errors**: WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes
- **HTTP Request Size Monitor**: WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute
- **CPU Usage Monitor**: WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors


Excessive HTTP Errors is implemented as follows:
  - **Metric**: Number of HTTP error codes (400 and up).
  - **Threshold**: Anything number of attempts that resulted in a 400 error code or above.
  - **Vulnerability Mitigated**: Brute Force Vulnerabilities could be mitigated with this alert. Any excessive amount of failed logins could clearly be brute force attempts into the network.
  - **Reliability**: This could cause some false negatives due to the error being set to trigger even one bad login attempt. However, it could prove valuable in monitoring overall failed attempts therefor making it difficult for successful brute force logins later on. I would rate this as medium reliability due to the sheer number of false alarms that would be created, however, it also does its job flawlessly in regards to alerting everytime a bad login is made, and in the process alerting for potential threat actor activity.

#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
  - **Metric**: The amount of HTTP requests in a set amount of time.
  - **Threshold**: Anything above 3500 events in the last 1 minute would trigger the alert
  - **Vulnerability Mitigated**: If there was a significant number of traffic that is directed towards a specific file on the webserver, that would alert the blue team of a potential malicious threat actor snooping around our file system trying to access confidential data.
  - **Reliability**: TODO: Does this alert generate lots of false positives/false negatives? Rate as low, medium, or high reliability.

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
  - **Metric**: CPU percentage use 
  - **Threshold**: TODO
  - **Vulnerability Mitigated**: TODO
  - **Reliability**: TODO: Does this alert generate lots of false positives/false negatives? Rate as low, medium, or high reliability.



# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
_TODO: Fill out the information below._

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap -sV 192.168.1.110
  # TODO: Insert scan output
```

This scan identifies the services below as potential points of entry:
- Target 1
  - List of
  - Exposed Services

_TODO: Fill out the list below. Include severity, and CVE numbers, if possible._

The following vulnerabilities were identified on each target:
- Target 1
  - List of
  - Critical
  - Vulnerabilities

_TODO: Include vulnerability scan results to prove the identified vulnerabilities._

### Exploitation
_TODO: Fill out the details below. Include screenshots where possible._

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33e11b80be759c4e844862482d
    - **Exploit Used**
      - **Exploits**:
        - Brute forcing weak password credentials
        - Reading confidential files
      - **Commands**:
        - ssh michael@192.168.1.110
        - cat /var/www/html/service.html
  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a36e581c
    - **Exploit Used**
      - _TODO: Brute forcing weak password credentials
      - _TODO: Reading confidential file
  - `flag3.txt`: afc01ab56b50591e7dccf93122770cd2
    - **Exploit Used**
      - todo
      - todo
  - `flag4.txt`: 715dea6c055b9fe3337544932f2941ce
    - **Exploit Used**
      - todo
      - todo
