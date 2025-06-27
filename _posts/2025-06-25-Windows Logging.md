---
layout: post
title: "Windows Logging For SOC Room"
date: 2025-06-25
categories: [TryHackMe]
tags: [THM,Windows,Log analysis,SOC]
---

Welcome to the Windows Logging for SOC Room on Try Hack Me! 

So, it is good to know about different types of Winodws logs and where to find them before starting this room, as Log analysis is the most important thing that a blue teamer will focus on be it for incident response, threat hunting or simply triaging .

Alright, let's get started!

**TASK 1: Introduction ---> Self-Explanatory**
---


**TASK 2: What Is Logged**
---
This task provides with a short introduction to get familiar with the layout of the event viewer.

We simply need to navigate to the Windows Event Viewer

**1. Looking at the last screenshot, which event ID describes a successful login?**

There are some important Event IDs that one should know and this is one of them. The answer is easy and you can figure it our by yourself.

**TASK 3: Security Log: Authentication**
---

This task deals with the two important event IDs for Authentication i.e `4624 (Successful Logon)` and `4625 (Failed Logon)`.

The can be hand to find out potential Brute Force and password spraying attacks along with the malicious Hostname or IPs used for the same.

In order to complete this task we need to open `Practice-Security.evtx`.

**1. Which IP performed a brute force of the THM-PC?**

Here having a eye on the timestamp really matters. I started by inspecting logs using the `4625` filter to check the failed attempts along with the Hostname and IPs asscoiated with it.

![Task-3](/assets/img/THM_SS/Win-log/T3.png)

Here on inspecting every single log I found out that it was actually a `Password Spraying` attempt as it has multiple usernames and each had atmost 4 failed attempts.

Another thing to note is it starts from `Timestamp: 10:53:26 to 10:53:30`.

Now, on inspecting the Successful logons we see that we have an entry of a successful logon exactly at `10:53:30` and it was on `THM-PC` which is a result of a successful Brute Force attack.

![Task-3](/assets/img/THM_SS/Win-log/T3-1.png)

You can find the malicious IP asscoiated with it by reverting back to 4625 filter and in log details section.

**2. Which user has been breached as a result of the attack?**

This can be found in the EventData --> TargetUserName 

**3. What was the Logon ID of the malicious RDP login?**

So for RDP the Logon type to look for is `10`.

Unfortunately, I was not able to filter by logon type even using XML , hence I had to manually look for it in the 4624 filtered logs and I was able to find one with the RDP.

The answer can be found in the EventData --> TargetLogonID

**TASK 4: User Management**
---

This section provides information on how important Event IDs in context to user management are useful to analyze logs in order to find potential backdoor users.

**1. Which user was created by the attacker soon after the RDP login?**

From the previous task we know the time stamp of the malicious RDP logon i.e `10:53:41`

I tried to analyze logs after this timeframe , if you want you can also apply filter `4720` to identify the new user but i did not do that because I wanted the whole context.

So, while analyzing logs I came across a log with Event ID `4720` and the subject username was the same that we had for RDP login.

We know that the new user created can be found in the `general` tab for that log. 

**2. Which two privileged groups was the backdoor user added to?**

You will now understand why I did not apply any filters, if you simple scroll up , you can easily find three `4732` Event IDs which indicate that a user has been added to a security group. All three add the same backdoor user to groups with diffrernt privilege level and you can easily find this by the group name.


**3. Does the Logon ID field match what you saw in the previous task?**

I found this question a bit tricky because the backdoor user, if you examine the logs closely, never actually logged in afterward. As a result, there’s no associated Logon ID for that user in the subsequent events. However, we do have the Member SID, which matches the Target SID assigned to the user at the time of account creation. This correlation confirms the identity of the user involved, leading to the answer.


**TASK 5: Sysmon: Process Monitoring**
---

Here, we learn about process monitoring where we generally search with Event Code as 1 and use sysmon for the same.

We will be using `Practice-Sysmon.evtx` file.

**1. Which web browser does Sarah use to browse the web?**

I started by filtering the log where Event ID is 1 to get all the process creation.

We get 7 process in total and I inspected them turn by turn and found that browser that Sarah uses. (Do not get confused by Explorer)

**2. Which file did Sarah download from the browser?**

This is evident and ig no explanation is needed , we need the path.

**3. Which URL was the file downloaded from?**

For this I had to do a bit of research, like what all other events does Sysmon have that can give us some info about the URL.

I found that `Event ID 15` does the job where we can find the URL in contents field.

**TASK 6: Sysmon: Files and Network**
---

**1. Which file was created by the downloaded malware to persist on the host?**

These questions are simple just use relevent filters, like here we use Event ID `11` which indicates file creation and this what we need.

**2. What is the Command & Control server malware connected to?**

Here, we need the C2C IP and the port as well, we can find it using Event ID `3` which provides us with info about network connections.

**3. Finally, which domain does the malicious IP correspond to?**

Since, we are asked about the domain we need to filter by Event ID `22` which provides us with details about DNS Queries.

**TASK 7: PowerShell: Logging Commands**
---

When we run powershell it shows up in the log as a single process and we have no idea what command someone is executing in the powershell.

In order to get those logs we have something called ConsoleHost_history.txt, which is automatically defined per user and it stores all the commands executed.

A sample path to get there : 

`C:\Users\<USER>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt`

**1. Which PowerShell command was executed first?**

Simply have a look at the ConsoleHost_history.txt file for the current user.

To read that file use command

```bash
type ConsoleHost_history.txt
```


**2. When did the Administrator run the first PS command?**

Go to file properties and we get the date from there.   

**3. Can you find the flag stored in the PowerShell history?**

I just performed a normal file search that would fetch me ConsoleHost_history.txt for each user. I inspected those files and got the flag.


### It was a very simple room, I must say. Congratulations on completing this room! &#127881;
<br>

# Professional Analysis

### Detection & Impact

<div style="text-align: justify;">
Log analysis is a critical component of attack detection. Even a minor oversight by an attacker can leave traces in system logs, making it possible to uncover their activity. For instance, Windows Security logs are valuable for identifying credential-based attacks such as password spraying or brute force attempts. They also provide insight into lateral movement within the network.
<br><br>
A weak understanding of log analysis can significantly weaken an organization's security posture. Failing to detect early-stage attacks may allow adversaries to compromise user accounts, escalate privileges to administrative levels, and establish persistence through backdoors. In some cases, attackers may even reset passwords across the environment, severely delaying the incident response process. 
</div>

### Real-World Application

<div style="text-align: justify;">
In addition to log analysis, log correlation is essential for any SOC analyst. It enables the linking of related events across different systems, playing a key role not only in the detection and analysis phases of incident response but also in identifying the root cause of an attack.
<br><br>
Industry-standard tools like Splunk and Endpoint Detection and Response (EDR) solutions are widely adopted across organizations and contribute significantly to overall security. However, to fully leverage these tools, a solid understanding of log correlation is crucial. It enhances alert tuning, helps configure effective detection rules, and improves the ability to identify and filter out false positives. 
</div>

### Security Implications

<div style="text-align: justify;">
From a business perspective, investing in a robust Endpoint Detection and Response (EDR) solution alongside a Security Information and Event Management (SIEM) platform is crucial. These tools are not only central to the detection of threats, but also play a critical role in the containment and mitigation of compromised systems.
<br><br>
In alignment with this, NIST SP 800-53 Rev. 5 (AU-11) requires organizations to retain audit records (i.e., logs) for a defined period that meets their legal, regulatory, and operational requirements. While the exact duration is organization-specific, a one-year retention period is widely recommended in related NIST guidance (such as SP 800-92) and industry standards to support incident investigations, forensic analysis, and compliance audits.
<br><br>
Failure to comply with these requirements can lead to severe consequences, including regulatory penalties, delayed incident response, loss of forensic evidence, and damage to business reputation and stakeholder trust. Maintaining proper log retention and leveraging EDR and SIEM solutions are therefore not just technical necessities, but strategic investments in the organization's overall risk management and resilience.
</div>