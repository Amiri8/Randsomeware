# Randsomeware

Description: In this lab project, I will simulate a basic script execution attack by running an Atomic-Red-script called AutoIt Script Execution in my Azure Windows VM. 


_**Inception State:**_ the organisation has no coorect firewall, IDS/IPS in place, 

Completion State: “Script execution attacks” are when a bad actor infects your endpoint with malware that uses a “script interpreter” (in this case, AutoIt.exe) to automatically launch malicious programs within the target machine, silently.
---

<img width="1000" alt="image" src="https://github.com/user-attachments/assets/cfc5dbcf-3fcb-4a71-9c13-2a49f8bab3e6">

# Tools and Frameworks used for this lab:
- Azure Virtual Machines
- Microsoft Defender for Endpoint
- Microsoft Sentinel
- Wireshark (For network packet collection)
- Powershell
- Atomic Red Scripts
- Optional: DeepBlueCLI
- NIST 800-61 Incident Response


---


# Table of Contents

- [Vulnerability Management Policy Draft Creation](#vulnerability-management-policy-draft-creation)
- [Mock Meeting: Policy Buy-In (Stakeholders)](#step-2-mock-meeting-policy-buy-in-stakeholders)
- [Policy Finalization and Senior Leadership Sign-Off](#step-3-policy-finalization-and-senior-leadership-sign-off)
- [Mock Meeting: Initial Scan Permission (Server Team)](#step-4-mock-meeting-initial-scan-permission-server-team)
- [Initial Scan of Server Team Assets](#step-5-initial-scan-of-server-team-assets)
- [Vulnerability Assessment and Prioritization](#step-6-vulnerability-assessment-and-prioritization)
- [Distributing Remediations to Remediation Teams](#step-7-distributing-remediations-to-remediation-teams)
- [Mock Meeting: Post-Initial Discovery Scan (Server Team)](#step-8-mock-meeting-post-initial-discovery-scan-server-team)
- [Mock CAB Meeting: Implementing Remediations](#step-9-mock-cab-meeting-implementing-remediations)
- [Remediation Round 1: Outdated Wireshark Removal](#remediation-round-1-outdated-wireshark-removal)
- [Remediation Round 2: Insecure Protocols & Ciphers](#remediation-round-2-insecure-protocols--ciphers)
- [Remediation Round 3: Guest Account Group Membership](#remediation-round-3-guest-account-group-membership)
- [Remediation Round 4: Windows OS Updates](#remediation-round-4-windows-os-updates)
- [First Cycle Remediation Effort Summary](#first-cycle-remediation-effort-summary)

---

### Step 1 - Onbording the VM to teh Microsoft MDE

In this step, we’ll ensure that Microsoft Defender for Endpoint (MDE) is correctly configured to detect the simulated attack triggered by the Atomic Red Team test.
And, we will create the following MDE detection rules using KQL query language to alert us when any of these steps are executed on our VM:


This phase I focused on creating a Vulnerability Management Policy as a starting point for stakeholder engagement. The initial draft outlines scope, responsibilities, and remediation timelines, and may be adjusted based on feedback from relevant departments to ensure practical implementation before final approval by upper management.  
[Draft Policy](https://docs.google.com/document/d/1vjTiAw22sGOtd1_bBgbrbfSeEDZNOSQynUS3v78_CT4/edit?tab=t.0)

---

### Step 2) Mock Meeting: Policy Buy-In (Stakeholders)
## Vulnerability Management Policy Discussion

In this phase, a meeting with the server team introduces the draft Vulnerability Management Policy and assesses their capability to meet remediation timelines. Feedback leads to adjustments, like extending the critical remediation window from 48 hours to one week, ensuring collaborative implementation.


---
# Vulnerability Remediation Policy Discussion (sample) 

## Participants  
- Ali (Policy Owner)  
- Jimmy (IT Operations Lead)  
---

### Ali:  
Morning, Jimmy. How’s everything been? I know it’s been pretty busy these last few weeks.

### Jimmy:  
Morning, Ali. Yeah, it’s definitely been hectic, but we’re hanging in there — appreciate you asking. I had a chance to go through the policy draft. Overall, it makes sense, but with our current staffing, we won’t be able to meet the aggressive remediation timelines — especially the 48-hour window for critical vulnerabilities.

### Ali:  
Totally understand — that is pretty aggressive, especially starting out. Maybe we can extend the critical remediation window to one week for now? We could reserve the 48-hour requirement for truly severe cases like zero-day vulnerabilities.

### Jimmy:  
That sounds reasonable. We appreciate the flexibility. Would it be possible to have a bit of leeway in the first few months while we get used to the new remediation and patching process?

### Ali:  
Absolutely. Once the policy is finalised, we’ll officially start the program, but we’re planning to give all departments about six months to adjust and get comfortable with the new process. Does that sound fair?

### Jimmy:  
That works for us. Thanks, Ali — we really appreciate being included in the decision-making process. It helps us feel like we’re part of the solution.

### Ali:  
Of course — we’re all in this together. Thanks for working with us.

### Jimmy:  
No problem — and thanks for keeping this meeting short.

### Ali:  
Yeah, my favorite kind. Take care.

### Jimmy:  
See you later.

---

*End of Discussion*

---

### Step 3) Policy Finalisation and Senior Leadership Sign-Off

After gathering feedback from the server team, the policy is revised, addressing aggressive remediation timelines. With final approval from upper management, the policy now guides the program, ensuring compliance and reference for pushback resolution.  
[Finalised Policy](https://docs.google.com/document/d/1vqqb7dO2m3143XFWeNqmsVKV6wB7HpW-Cv-5WuHvBnk/edit?tab=t.0)
<div style="text-align: center;">
<img width="1161" alt="image" src="https://github.com/user-attachments/assets/ac8e4763-83ef-4400-a051-320969d8c854" />

---

### Step 4) Mock Meeting: Initial Scan Permission (Server Team)

The team collaborates with the server team to initiate scheduled credential scans. A compromise is reached to scan a single server first, monitoring resource impact, and using just-in-time Active Directory credentials for secure, controlled access.  
 

## Participants  
- Ali (Security Analyst)  
- Jimmy (IT Operations Lead)




### Ali:  
Morning, Jimmy.

### Jimmy:  
Good morning, Ali. I heard you're ready to start running some scans?

### Ali:  
Yep! Now that our Vulnerability Management Policy is in place, I wanted to begin scheduling some credentialed scans of your environment.

### Jimmy:  
Sounds good. What’s involved, and how can we help?

### Ali:  
We’re planning to run weekly scans on the server infrastructure. We estimate it will take about 4 to 6 hours to scan all 200 assets. We’ll need you to provide us with some administrative credentials so the scan engine can log into the systems and perform a more thorough assessment.

### Jimmy:  
Whoa, hold on. What exactly does scanning involve? I'm a little concerned about resource usage. And you’re asking for admin credentials to 200 machines — is that safe?

### Ali:  
Good questions — and fair concerns. The scan engine sends specific traffic to the servers to check for vulnerabilities — things like outdated software, insecure protocols, or weak cipher suites. That’s why we need credentials — it allows us to check inside the system for these issues rather than just doing surface-level checks.

### Jimmy:  
I see. As long as the scans don’t impact server performance or bring anything down, we should be okay.

### Ali:  
Absolutely. To start, we can scan a single server and monitor resource utilisation to make sure everything runs smoothly.

### Jimmy:  
That sounds like a good plan.

### Ali:  
Also, for the credentials — would you be able to create a dedicated Active Directory account for scanning? We can leave the account disabled until we’re ready to run the scan, then enable it just for the duration of the scan, and disable or deprovision it afterwards — a kind of just-in-time access approach.

### Jimmy:  
Sounds good. I’ll ask Susan to start working on the automation for account provisioning.

### Ali:  
Perfect — talk soon.

### Jimmy:  
No problem. I’ll let you know once the credentials are set up.

### Ali:  
Sounds good. See you later.

### Jimmy:  
See you later.

---

*End of Discussion*
---

### Step 5) Initial Scan of Server Team Assets

In this phase, an insecure Windows Server is provisioned to simulate the server team's environment. After creating vulnerabilities, an authenticated scan is performed, and the results are exported for future remediation steps.  

<img width="756" alt="image" src="https://github.com/user-attachments/assets/2b9f52f3-f671-4c24-ae88-dabcd5ff17b9" />



---

### Step 6) Vulnerability Assessment and Prioritisation

We assessed vulnerabilities and established a remediation prioritisation strategy based on ease of remediation and impact. The following priorities were set:

1. Third Party Software Removal (Wireshark)
2. Windows OS Secure Configuration (Protocols & Ciphers)
3. Windows OS Secure Configuration (Guest Account Group Membership)
4. Windows OS Updates

---

### Step 7) Distributing Remediations to Remediation Teams

The server team received remediation scripts and scan reports to address key vulnerabilities. This streamlined their efforts and prepared them for a follow-up review.  

<img width="635" alt="image" src="https://github.com/user-attachments/assets/bbf9478f-e1d1-4898-846e-b510ec8c6f72">

[Remediation Email](<img width="1494" alt="image" src="https://github.com/user-attachments/assets/c5f20413-b24f-4550-85f8-3e23d1268325" />
)

---

### Step 8) Mock Meeting: Post-Initial Discovery Scan (Server Team)

The server team reviewed vulnerability scan results, identifying outdated software, insecure accounts, and deprecated protocols. The remediation packages were prepared for submission to the Change Control Board (CAB). 


## Participants  
- Ali (Security Analyst)  
- Jimmy (IT Operations Lead)

### Ali:  
Morning, Jimmy. How are you doing?

### Jimmy:  
Not bad for a Monday — and yourself?

### Ali:  
Still alive, so I can't complain! Before we jump into the vulnerability findings — how did the scan go on your end? Any outages or performance issues?

### Jimmy:  
The scan went well. We were monitoring the servers and, aside from noticing some open connections, we wouldn’t have even known a scan was taking place.

### Ali:  
That's great to hear — exactly what I was hoping for. We'll keep monitoring, but I don’t expect resource utilization to be an issue going forward. Mind if I dive into the vulnerability findings?

### Jimmy:  
Absolutely — go ahead.

### Ali:  
Let me share my screen real quick. So, most of the vulnerabilities are related to outdated versions of Wireshark being installed. It’s pretty old and needs to be updated or removed.

Another interesting finding — the local Guest account on the servers is a member of the local Administrators group. That’s definitely something we should address.

Some other vulnerabilities, like the Microsoft Edge Chromium one, may resolve automatically through Windows Updates. Same with a few others — I’ll need to verify that.

We don’t need to worry about the self-signed certificate finding — that's normal for internal machines.

However, the medium-strength cipher suites and deprecated protocols like TLS 1.0 and 1.1 are worth addressing. These are outdated and should be removed.

### Jimmy:  
Very interesting. The good news is I suspect most of our servers will have the same vulnerabilities, so remediation should be easier across the board.

### Ali:  
Exactly — a uniform environment makes the process smoother. Do you foresee any issues with remediating the cipher suites or disabling those insecure protocols?

### Jimmy:  
I doubt it. We'll run everything through the next Change Control Board. Uninstalling Wireshark and fixing the Guest account shouldn't be a problem either — those things shouldn’t have been on the servers in the first place. I’ll check in with our sysadmins to confirm.

### Ali:  
Sounds good. I’ll go ahead and start building out some remediation packages to make the process easier for you.

### Jimmy:  
Appreciate that. Also — do we have patch management in place to handle the Windows Update-related vulnerabilities?

### Ali:  
Good question — I’m not too worried about that. Our patch management process should handle those automatically by next week.

### Jimmy:  
Excellent.

### Ali:  
Alright, I’ll get started on researching the best approach for remediation and get back to you before the next Change Control Board.

### Jimmy:  
Sounds good — talk to you soon.

### Ali:  
Talk to you soon.


---

### Step 9) Mock CAB Meeting: Implementing Remediations

The Change Control Board (CAB) reviewed and approved the plan to remove insecure protocols and cipher suites. The plan included a rollback script and a tiered deployment approach.  

# Insecure Protocols & Cipher Suites Remediation Discussion  

## Participants  
- Ali (Security Analyst from Risk Department)  
- Jimmy (IT Operations Lead)  
- Meeting Attendees (Change Advisory Board - CAB)


### Ali:  
Next up on the list are a couple of vulnerability remediations for the server team:  
1. Removal of insecure protocols  
2. Removal of insecure cipher suites  

It looks like I’ve been working with Jimmy from the infrastructure team on this. Ali, do you want to walk us through the technical details of the change?

### Ali:  
Sure, I can explain.  

Basically, the existence of insecure protocols and cipher suites means the system is capable of negotiating outdated or deprecated algorithms when connecting to other systems. If a server only supports these weak protocols, our systems could end up using them — which is a security risk.

These settings are controlled via the Windows registry. The fix is straightforward — we’ve developed a PowerShell script that disables all insecure protocols and cipher suites while enabling only the current secure standards.

### Meeting Attendee:  
That sounds good, but what if something goes wrong? Do we have a rollback plan?

### Ali:  
Absolutely. We’ve got a tiered deployment plan:  
- We'll start with a small pilot group of non-critical systems.  
- Then move to pre-production.  
- And finally deploy to production.

In addition, we’ve built automated rollback scripts for each change. If any unexpected issues arise, these scripts will restore the original registry settings — including the previously enabled protocols and cipher suites.

### Jimmy:  
That sounds good. Since the changes are just registry updates, I’m not too concerned.

### Ali:  
Exactly — it’s a simple but effective fix.

### Meeting Attendee:  
Any more questions from anyone?

### Ali:  
Great — that wraps things up for this week’s CAB meeting. See you all next week.

### Everyone:  
See you later.


---
### Step 10 ) Remediation Effort

#### Remediation Round 1: Outdated Wireshark Removal

The server team used a PowerShell script to remove outdated Wireshark. A follow-up scan confirmed successful remediation.  

<img width="891" alt="image" src="https://github.com/user-attachments/assets/96305b20-cf2f-415f-9c21-dd9424f0c3dd" />


[Scan 2 - Third Party Software Removal](https://drive.google.com/file/d/15et0hRx1Wgk0gt3qRo-5vFjjx85E0PU4/view?usp=sharing)


#### Remediation Round 2: Insecure Protocols & Ciphers

The server team used PowerShell scripts to remediate insecure protocols and cipher suites. A follow-up scan verified successful remediation, and the results were saved for reference.  
[PowerShell: Insecure Protocols Remediation](https://github.com/joshmadakor1/lognpacific-public/blob/main/automation/toggle-protocols.ps1)
[PowerShell: Insecure Ciphers Remediation](https://github.com/joshmadakor1/lognpacific-public/blob/main/automation/toggle-cipher-suites.ps1)

<img width="926" alt="image" src="https://github.com/user-attachments/assets/1178f2a0-9c72-4737-a300-30555e1e18c6" />

[Scan 3 - Ciphersuites and Protocols](https://drive.google.com/file/d/15et0hRx1Wgk0gt3qRo-5vFjjx85E0PU4/view?usp=drive_link)


#### Remediation Round 3: Guest Account Group Membership

The server team removed the guest account from the administrator group. A new scan confirmed remediation, and the results were exported for comparison.  
[PowerShell: Guest Account Group Membership Remediation](https://github.com/joshmadakor1/lognpacific-public/blob/main/automation/toggle-guest-local-administrators.ps1)  

<img width="899" alt="image" src="https://github.com/user-attachments/assets/7f0fc3b2-8e74-4ef5-b0a4-32911fdc2ff5" />

[Scan 4 - Guest Account Group Removal](https://drive.google.com/file/d/1ul2m_zgVO1j6F9mll2ijbGMWtwZq7km7/view?usp=drive_link)


#### Remediation Round 4: Windows OS Updates

Windows updates were re-enabled and applied until the system was fully up to date. A final scan verified the changes  

<img width="920" alt="image" src="https://github.com/user-attachments/assets/bc0f90d2-38e5-4566-af56-c66eb2df32e1" />

[Scan 5 - Post Windows Updates](https://drive.google.com/file/d/1-zOOFcozGEzFzqwc8b4eSJiM5PRb643i/view?usp=drive_link)

---

### First Cycle Remediation Effort Summary

The remediation process reduced total vulnerabilities by 80%, from 30 to 6. Critical vulnerabilities were resolved by the second scan (100%), and high vulnerabilities dropped by 90%. Mediums were reduced by 76%. In an actual production environment, asset criticality would further guide future remediation efforts.  

<img width="599" alt="image" src="https://github.com/user-attachments/assets/2bb54df3-ddb6-4f0d-a341-08c8f93995ef" />


[Remediation Data](https://docs.google.com/spreadsheets/d/18UrwBZlysmslSI4yhW-CI4CwJ_b32gGEL6KK4LxIC4k/edit?gid=0#gid=0)

---

### On-going Vulnerability Management (Maintenance Mode)

After completing the initial remediation cycle, the vulnerability management program transitions into **Maintenance Mode**. This phase ensures that vulnerabilities continue to be managed proactively, keeping systems secure over time. Regular scans, continuous monitoring, and timely remediation are crucial components of this phase. (See [Finalised Policy](https://docs.google.com/document/d/1PXUc5GCLQuqTnumlW89rBc-3KV9GZq_PwjvSfU_pPD4/edit?tab=t.0) for scanning and remediation cadence requirements.)

Key activities in Maintenance Mode include:
- **Scheduled Vulnerability Scans**: Perform regular scans (e.g., weekly or monthly) to detect new vulnerabilities as systems evolve.
- **Patch Management**: Continuously apply security patches and updates, ensuring no critical vulnerabilities remain unpatched.
- **Remediation Follow-ups**: Address newly identified vulnerabilities promptly, prioritising based on risk and impact.
- **Policy Review and Updates**: Periodically review the Vulnerability Management Policy to ensure it aligns with the latest security best practices and organisational needs.
- **Audit and Compliance**: Conduct internal audits to ensure compliance with the vulnerability management policy and external regulations.
- **Ongoing Communication with Stakeholders**: Maintain open communication with teams responsible for remediation, ensuring efficient coordination.

By maintaining an active vulnerability management process, organisations can stay ahead of emerging threats and ensure long-term security resilience.
