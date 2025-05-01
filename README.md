## Incident Response Simulation  


**Description: In this lab project, I will simulate a basic script execution attack by running an Atomic-Red-script called AutoIt Script Execution in my Azure Windows VM.**


_**Inception State:**_ the organisation has no coorect firewall, IDS/IPS in place, and I have allowed all the inbound traffic to come in from NSG.

“Script execution attacks” are when a bad actor infects your endpoint with malware that uses a “script interpreter” (in this case, AutoIt.exe) to automatically launch malicious programs within the target machine, silently.

---


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

### Step 1 - Onbording the VM to teh Microsoft MDE

In this step, we’ll ensure that Microsoft Defender for Endpoint (MDE) is correctly configured to detect the simulated attack triggered by the Atomic Red Team test.
And, we will create the following MDE detection rules using KQL query language to alert us when any of these steps are executed on our VM:


<img width="874" alt="image" src="https://github.com/user-attachments/assets/ba394244-250d-4f34-bee4-e3d11408da64" />



Rule 1: Alert when AutoIt.exe is launched from a User, Temp or Downloads folder AND the command line runs the malicious calc.au3 script file:

----



<img width="852" alt="image" src="https://github.com/user-attachments/assets/b4843470-36a0-4ef7-b605-82adffddbeb6" />



Rule 2: Alert when Autolt.exe launches calc.exe (this is an abnormal parent-child process relationship)


------



<img width="875" alt="image" src="https://github.com/user-attachments/assets/43c6a452-d7fb-4f21-8eae-d136bf98955b" />

Rule 3: Alert when PowerShell is used to download something from the internet via the “Invoke-WebRequest” command

---------



<img width="872" alt="image" src="https://github.com/user-attachments/assets/78537767-9c1c-44a6-a06f-07b34199aff4" />

Rule 4: Alert when Powershell is being used to install Autolt.exe (Powershell is not typically used to install programs like these in a normal enterprise environment)

-----


<img width="618" alt="image" src="https://github.com/user-attachments/assets/8fe2f5a4-3588-4d75-82d7-be72dd2045f1" />

Overall, here is the screenshot of all the rules setup in MDE


-----

### Step 2 - Execute the Atomic Red Team Attack (T1059 - Command and Scripting Interpreter)

With Microsoft Defender for Endpoint (MDE) and your VM environment ready, we will now execute the Atomic Red Team attack that simulates PowerShell-based script execution (MITRE ATT&CK T1059). 

Clone Atomic Red Team Repository:

<pre>
powershell:
git clone https://github.com/redcanaryco/atomic-red-team.git
</pre>

This downloads the full library of Atomic Red 
---


<pre>
powershell:
cd C:\Users\adminuser\atomic-red-team
</pre>

This changed directory to the folder where the scripts are loaded
---

<pre>
powershell:
$env:PathToAtomicsFolder = "C:\Users\YourUser\atomic-red-team\atomics\"
</pre>

This makes sure that we are pulling the atomic script from the correct folder (Atomics) that was created when we cloned the Atomic Red database of attacks
---

<pre>
powershell:
Install-Module -Name Invoke-AtomicRedTeam -Force -AllowClobber
</pre>

This preps our VM for running the attacks by downloading the right module to do so. “-AllowClobber” also allows us to override any existing modules that could get in the way.
---

<pre>
powershell
Install-Module -Name Invoke-AtomicRedTeam -Force -AllowClobber
</pre>

This installs the PowerShell module required to run Atomic Red Team tests.
---

<pre>
 powershell
Import-Module Invoke-AtomicRedTeam
</pre>
This imports the module into your current PowerShell session
----

<pre>
powershell
Set-ExecutionPolicy Bypass -Scope Process -Force
</pre>

Temporarily bypasses PowerShell script execution restrictions to allow the test to run.
----

<pre>
powershell
Invoke-AtomicTest T1059 -GetPrereqs -PathToAtomicsFolder "C:\Users\Ali-win-10\atomic-red-team\atomics\"
</pre>

Downloads and installs any required tools or scripts needed to run the Atomic test.
-----



<pre>
powershell
Invoke-AtomicTest T1059 -PathToAtomicsFolder "C:\Users\YourUser\atomic-red-team\atomics\"
</pre>

Runs and detonates our malicious script in the VM, we should see the calculator app open after few seconds
---




Once all the scripts successfully run, we can stop the wireshark and save the recorded activity as we can alanayse this later. 
-----



### Step 3 - Review MDE Alerts (Post-Attack)
Now that we have detonated the attack script, let’s check if the attack has triggered any of the detection rules we setup in MDE!

<img width="764" alt="image" src="https://github.com/user-attachments/assets/bfccd43f-3988-4e0a-9703-a5eed04606ae" />



As you can see, one of our detection rules were triggered!!!
Ideally, the orhers should have aslo been triggered, but it seems like we have to refine the KQL queries a bit more to improve their detection. We also seem to have randsomeware attack, probably due to disabiling our firewalls and NSG within Azure.
----





### Step 4 - Conduct Incident Response Investigation 
We will be conducting our incident response investifgation in accordance with **NIST 800-61** Guidelines, which puts us in the **Detection and Alalysis Phase**

<img width="677" alt="image" src="https://github.com/user-attachments/assets/cf09e8b9-7463-475d-bb97-14bbe7cdfb57" />


As per **NIST 800-61 Guildline** We need to perform the following tasks for our investigation in order to successfully determine if the alert is a False or True positive.

1. Fund the attack vector used to initiate the attack.
2. Findings or indicators of security incidents.
3. Analyse the potential security incident and determine if it is a ture or false positive.
4. Document all findings and activities of investigations if it is a true positive.
5. Report the confirmed security incidents to the management team.

   ----

1. ### Whar was the attack-vector that was used?
Under the typical circumstance, our investigations would involve iddenrifying the **Attack-Vector** used to initiate the attack. This could be: clicking on a phishing email. However since this is a simulated attack and I have run the attack, there won't be "attack-Vector" to identify. 


2. ### What are the Precursos and/or Indicator of the Attack?
Again since I have simulated the attack, there wont be a real precursors since I intentionally disabled my VM's firewall and allowed all inbound traffic to occur without restrictions (NSG).
However we have many **indivators of compromise (IOCs)** of our security incidnet via MDE and Sentinel logs. So we can now seach through these logs to see if we can find any strong IOCs. 

----

### Step 4 - Conduct Incident Response Investigation


Let’s Start with looking at the timeline of logs generated in MDE for that specific alert by going into the “Alerts” tab and clicking on the alert itself:


<img width="765" alt="image" src="https://github.com/user-attachments/assets/bb19afde-7515-4b77-b8ed-4afbf6bb5910" />



As per the screenshot, we can see some familiar commands that we initiated when setting up our attack. We can also see the malware scripts that were run:


<img width="763" alt="image" src="https://github.com/user-attachments/assets/0529a410-b9ab-4ac7-acc1-f6156f1855fa" />



Clearly there are alot of IOC's here!! At this point we have enough evidence to contain and **isolate this endpoint**. Let's see if we can find the steps involved in our AutoIt Script Execution attack. 

Upon analysing the MDE timeline of the events, a few things stand out that tells us more about this attack:

If we scroll down to **event ID 8408**, we can see a PowerShell command that is initiating a **Invoke-WebRequest** command and downloaded **AutoIt-v3-setup.exe**


<img width="760" alt="image" src="https://github.com/user-attachments/assets/c83f7ee3-8bd2-477c-9f6f-e5f538dc50af" />

This is clearly the script downloading and installing the required AutoIt.exe program it needs to run the malicious script!


And as a result we see that **AutoIt.exe** was ran and installed, which eventually lead to the execution of the **calc.au3** malicious script.


<img width="755" alt="image" src="https://github.com/user-attachments/assets/c00a1f15-87df-4741-b28b-2b0fe94bd1f1" />


Lets vertify this **Invoke-WebRequest** with **Wireshark** and see if the downloaded file actually took place?


----


### Step 5 - Conduct Incident Response Investigation 


Once we oopen the PCAP file, we see all of the network traffic packets that were captured during our attack simulation 


<img width="614" alt="image" src="https://github.com/user-attachments/assets/83c4eb20-ddda-4ca9-a703-b03e81a99ab4" />

In order to verify that the **Invoke-WebRequest** happened, we need to filter down these results. Based on the MDE logs, we know that the protocol occurred by **HTTPS** protocol at 3:42:12 PM to actually download the AutoIt-v3-setup.exe file. We also know that our VM's private IP address is **(10.1.0.64)**. 

Based on these facts,  we will construct the following display filter to see if we can find this malicious download request




<pre>

 tls && ip.src == 10.1.0.64 && frame contains "autoitscript.com"

</pre>




<img width="766" alt="image" src="https://github.com/user-attachments/assets/190f1c07-66e4-4639-8321-03288d5d8a30" />

When we run this filter, we get back one packet that shows an initial connection request with the destination IP of **212.227.91.231**


-----

Now that we have teh destination IP address, we can run anotehr filter display to see the full exchange communication between our VM and the website


<img width="767" alt="image" src="https://github.com/user-attachments/assets/da7d444e-e48d-4feb-ad2b-ee51f2b3a108" />


The result shows  that the there was indeed an exchange of an encrypted payload!

----

Next lets check the networking logs in Sentinel by using KQL:

So I used this command




<pre>
DeviceNetworkEvents
| where Timestamp > ago(24h)
| where DeviceName == "atomictest-02"
| project Timestamp, DeviceName, RemoteIP, RemotePort, Protocol, ActionType, InitiatingProcessFileName, ReportId
| order by Timestamp desc
</pre>



<img width="765" alt="image" src="https://github.com/user-attachments/assets/fbd71b5a-f929-4d21-91e5-d8ce0cd74104" />


So at this point, we should have enough evidence to escalate this incidnet and move into the **Containment, Eradication, and Recovery** phase of NIST-800-6. 


----


### Step 5 - Containment, Eradication, and Recovery

At this point we have conducted our investigation in accordance with the Detection and Analysis phase of NIST 800-61. Now would be time to collect our evidence, write a report of our findings and inform the appropriate managements. 

Once we have taken the above steps, we can now enter the **Containment, Eradication, and Recovery** phase of NIST 800-61



<img width="530" alt="Screenshot 2025-05-01 at 6 23 18 pm" src="https://github.com/user-attachments/assets/912bd4d3-7d8b-490d-88f1-8c5b2136425c" />


According to NIST, this phase involves completing the following tasks in order to remediate this security breach:

1. Choose a containment strategy
2. Gather any evidence, artifacts, IOCs for potential legal proceedings.
3. Identify the attacking host(s)
4. Eradicate the components of the breach and Recover the impacted assets.


**Choosing a containment strategy:** In this scenarios the most appropriate containment strategy is to **isolate** the VM from MDE to prevent any further communication with malicious server. 

**Gather evidence:** Here, we would gather all IP addresses, PCAP files, logs, hash values of suspected files (calc.au3.exe, etc.) and any indicators of compromise that will help build a legal case.

**Identify the attacking host(s):** Since this was a simulated attack, this won’t apply.

**Eradicate the components of the breach and Recover the impacted assets:** In this case, this involves running an anti-malware or antivitus scans, enabling our firewall and NSG, and deleting any malcious files from the host.

----


**Post-Incident Activities**
At this point, 

we are now in the final **Post-Incident Activity phase**. In this phase we review all of the lessons learned from our incident and use that to build better detection rules in future. 



<img width="640" alt="Screenshot 2025-05-01 at 6 34 42 pm" src="https://github.com/user-attachments/assets/d8381741-17f9-441f-849e-a756a72047ff" />








