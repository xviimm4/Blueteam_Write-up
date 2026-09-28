# SOC168 - Whoami Command Detected in Request Body

![image 1](../images/SOC168/image1.png)

## 1. Taking Ownership

A ticket was created to take ownership of the alert and start the investigation workflow.

![image 2](../images/SOC168/image2.png)

## 2. Understanding Why the Alert Was Triggered

The first step of the playbook is to collect the initial information from the alert itself.

![image 3](../images/SOC168/image3.png)

Key observations:

1. **Rule name:** "Whoami Command Detected in Request Body" indicates that the alert was triggered by the string `whoami` appearing in the body of an HTTP request. This is a typical sign of command injection / remote code execution, used to check the privileges of the web server process, and it maps to technique T1190 (Exploit Public-Facing Application).
2. **Traffic direction:**
   - **Source:** 61.177.172.87, a public IP address (external, on the Internet).
   - **Destination:** 172.16.17.16, a private IP address (WebServer1004).
   - **Protocol:** HTTP/HTTPS, using the POST method to the path `/video/`.

## 3. Data Collection

![image 4](../images/SOC168/image4.png)

![image 5](../images/SOC168/image5.png)

The source IP originates from China and is flagged as malicious by 2 of 91 vendors, so it is classified as **Malicious**.

![image 6](../images/SOC168/image6.png)

Next, Log Management was searched for the source IP `61.177.172.87`.

![image 7](../images/SOC168/image7.png)

Five firewall events with this source address were found. Each event was reviewed in turn.

![image 8](../images/SOC168/image8.png)

![image 9](../images/SOC168/image9.png)

![image 10](../images/SOC168/image10.png)

![image 11](../images/SOC168/image11.png)

![image 12](../images/SOC168/image12.png)

The POST parameters in these logs (`uname`, `whoami`, `ls`, `cat /etc/passwd`, `cat /etc/shadow`) are OS commands, and every response returned HTTP status 200. The attack type is therefore **Command Injection**.

## 4. Planned Test Check

![image 13](../images/SOC168/image13.png)

To rule out a penetration test or attack simulation, Email Security was searched for any message related to the suspicious IP. None was found, so the activity is **Not Planned**.

## 5. Traffic Direction

![image 14](../images/SOC168/image14.png)

The traffic comes from an external network into the internal one: **Internet -> Company Network**.

## 6. Attack Outcome

![image 15](../images/SOC168/image15.png)

Every request received an HTTP 200 response, so the attack is considered **successful**.

## 7. Containment

![image 16](../images/SOC168/image16.png)

The host **WebServer1004** (`172.16.17.16`) was isolated via **Endpoint Security** using **Request Containment**.

![image 17](../images/SOC168/image17.png)

## 8. Artifacts

![image 18](../images/SOC168/image18.png)

## 9. Escalation

![image 19](../images/SOC168/image19.png)

Since the attack was successful, the case was escalated to **Tier 2**.

## 10. Analyst Note

![image 20](../images/SOC168/image20.png)

## 11. Final Result

The final result of the investigation is **True Positive**.

![image 21](../images/SOC168/image21.png)

![image 22](../images/SOC168/image22.png)
