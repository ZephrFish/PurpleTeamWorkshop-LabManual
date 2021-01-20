# PurpleTeamWorkshop-LabManual
Purple Team Workshop by @jorgeorchilles


In the hands-on portion of our workshop you will play the role of a Cyber Threat Intelligence analyst, the red team, and the blue team. We have set up an isolated environment for each attendee to go through a Purple Team Exercise. Attendees will be able to create adversary emulation campaigns with SCYTHE and run them in a small environment consisting of a domain controller and a member server. While the attendee is the red team operator, they will also play the role of the blue team looking for Indicators of Compromise and adversary behavior mapped to MITRE ATT&CK Tactics, Techniques, and Procedures (TTPs). Attendees will learn the basics of adversary emulation (powered by SCYTHE) and blue team tools such as Sysmon, WireShark, and others. It will be fun hands-on learning!

Systems available:

- Unicorn Windows Member server with console access  - 192.168.0.20
- UnicornDC1 - 192.168.0.10
- SCYTHE Attack Server - 192.168.0.40
- SANS Slingshot C2 Matrix Edition VM with console access - 192.168.0.100



INTRO TO C2 WITH SCYTHE


In this introduction to Command and Control (C2), we will use SCYTHE to:

- Define Command and Control (C2) for a campaign.
- Use modern endpoint defense evasion techniques for implant creation.
- Craft custom adversarial scenario utilizing action automation.
- Deployed implant to arbitrary endpoint and established C2 connection.
- Utilized C2 to conduct adversarial actions.
- Aligned adversary behavior with MITRE ATT&CK framework.
- Generate both coarse and fine-grained action reporting.
- Provided Campaign insights aligned to the cyber kill-chain.

SCYTHE provides an advanced attack emulation platform for the enterprise and cybersecurity consulting market. The SCYTHE platform enables Red, Blue, and Purple teams to build and emulate real-world adversarial campaigns in a matter of minutes. Customers are in turn enabled to validate the risk posture and exposure of their business and employees and the performance of enterprise security teams and existing security solutions.

SCENARIO 1: NEW CAMPAIGN


In order to best demonstrate the power and flexibility of SCYTHE’s implant creation and automation, this scenario will guide you on how to create an initial campaign, setup automation of actions, and detonate a SCYTHE executable payload on an endpoint.


STEP 1: DEFINE CAMPAIGN SETTINGS


First we must define the Campaign’s general settings which will persist throughout the duration of the Campaign and its implants.

Open Google Chrome and click the SCYTHE bookmark. Log in with the provided credentials:

- Username: unicorn
- Password: unicorn

Once logged in to SCYTHE:

1. Open the Campaign Manager dropdown located on the left-sidebar.
2. Select New Campaign.
3. Set the Campaign Name to a value, such as: FirstCampaign
4. Ensure the Target Operating System is set to Windows.
5. Leave Restrict Campaign set to None. This allows you to set Execution Guardrails via Environmental Keying (ATT&CK T1480)
6. Leave Start Date and End Date set to empty.
7. For Communication Modules:
- Ensure the selected Module is set to HTTPS.
- Under Parameters, you can set a custom profile, domain fronting, etc. For this case, just set the heartbeat:
	- Click heartbeat and type 10. This is a 10 second heartbeat that your implant will check in with the C2 server.
- Confirm the Parameters field is set to:
	- --cp scythe:443 --secure true --multipart 10240 --heartbeat 10
8. For Capability Modules:
- Ensure only the Controller and Loader Modules are selected.
- Do not select any other modules as we will select and configure those in the next window.
9. For Signature Avoidance:
- Leave the Program database path and Compilation timestamp set to their current non-empty values.
- You can click randomize to change them.
10. Click Next.

STEP 2: DEFINE CAMPAIGN AUTOMATION


Now we can use the SCYTHE Automation interface to define our own custom adversarial behaviors to be taken by an implant at runtime (aka automation!).

From the automation interface:

1. Under Load a module:
- Click ARP 
- Click PrintScr
- Click Run
2. Under Execute an action:
- Click printscr --window Desktop
- Click arp
- Click run whoami
3. Under Compound actions:
- Click MITRE ATT&CK Techniques.
- Under Discovery column:
	- Click T1124 / System Time Discovery.
	- Click System Time Discovery - PowerShell.
	- Click Add Steps
	- Click Proceed
4. At the bottom right of the window (under Finish step) click Save Steps as Threat.
- Name: “First Threat”.
- Description: “SCYTHE Example Threat”.
- Click Save.
5. At the bottom left of the window click Next.
6. On Deliver Campaign window:
- Ensure that Deliver via is set to Physical.
- Click Start Campaign.

STEP 3: DOWNLOAD, DEPLOY, AND DETONATE IMPLANT


	Now that your Campaign is created it is ready to be downloaded and deployed. While on the Campaign page:

1. Click More actions... menu.
2. Click Download 64-bit EXE.
3. This prompt the download of a file named Initial_Campaign_scythe_client64.exe
4. Once the executable has been downloaded, execute it. You may get a SmartScreen message, accept and run.

STEP 4: REVIEW ACTIONS & REPORTS


Now that your ENDPOINT has a running SCYTHE implant, you can get updates from the machine in real-time via the Event Log in the user interface. SCYTHE generates reports at the current point in time, even if the campaign is still running. You can download various reports in various formats:

- CSV and HTML - provide the entire Red Team Operator log. This is great for providing the Blue Team exactly what was done and at what time.
- Executive Summary - provide a high-level summary including how far down the cyber kill-chain the Campaign was able to progress to, the amount of C2 traffic, and the statistics for the most active devices.
- MITRE ATT&CK - Heat Map of what was executed and if it was successful or not from the Red Team perspective
- ATT&CK Navigator - JSON file that can be imported into ATT&CK Navigator

From within the SCYTHE web interface:

1. Select Reports located on the left-sidebar.
2. Locate your Campaign (Initial_Campaign) from the list.
3. Click CSV to download the report in CSV format for importing into VECTR later.
4. Feel free to download other report formats.

DETECTION WITH WIRESHARK


In this section, we will leverage wireshark to see the packets being sent to and from the implant to the command and control server. Wireshark has been installed already so you can just double click the shortcut on the desktop.

USING WIRESHARK


When you open Wireshark you will be prompted for which interface to capture traffic.

- Double click: Ethernet0

You will begin to see traffic going back and forth. Since you are using Unicorn to connect to the management interface of SCYTHE, you should see traffic to 192.168.0.40 over port 8443. You may also see traffic on 445.

Wireshark allows filtering and this will help limit the scope of what you want to focus on.

- In the Apply a display filter type: ip.addr == 192.168.0.40

Now you are only seeing traffic to the SCYTHE Command and Control server. This will allow you to observe each packet.

- Right click on a packet, click Follow - TCP Stream.

CYBER THREAT INTELLIGENCE - ORANGEWORM


Let's consume some Cyber Threat Intelligence to create an adversary emulation plan. Given the current threat against hospitals, let's look at a threat actor targeting healthcare: Orangeworm.

A great place to start is MITRE ATT&CK site: https://attack.mitre.org/groups/G0071/

The mapping to MITRE ATT&CK is pretty weak, only 2 TTPs. Continue to read through the page and notice all the software they have used. Each of that software has TTPs. 

There is a reference to a vendor Cyber Threat Intelligence report which has more information: https://symantec-enterprise-blogs.security.com/blogs/threat-intelligence/orangeworm-targets-healthcare-us-europe-asia 

Read through the report and see if you can extract the adversary behaviors or tactics, techniques, and procedures. In this particular report, like many others, they did not map it to MITRE ATT&CK.

Here is a walkthrough of the extraction process to create an adversary emulation plan: https://www.scythe.io/library/threatthursday-orangeworm

You can view the mapping here: 

- https://mitre-attack.github.io/attack-navigator/#layerURL=https://raw.githubusercontent.com/scythe-io/community-threats/master/Orangeworm/orangeworm_layer.json

 

SCENARIO 2: EMULATE ORANGEWORM


Now that you have a basic understanding of setting up a C2 listener (SCYTHE), creating a payload, and manually interacting with a shell, let's emulate Orangeworm.

Orangeworm performs a significant amount of Discovery by leveraging built in tools such as arp, cmd, ipconfig, net, netstat, route, and systeminfo. We will do the same with our adversary emulation plan, conscience that most of these tools will run without being blocked. In the industry, we call leveraging built in tools: “Living off the Land”. 

You have 2 options:

1. Create the Orangeworm plan yourself following what we have shown (create a new campaign, automate the steps from the Cyber Threat Intelligence, launch the campaign, and execute it).

2. Use the adversary emulation plan we have created and shared in SCYTHE's Community Threat GitHub; we have already downloaded the Orangeworm threat from and it is in your downloads folder.

If you go with our walkthrough:

1. In SCYTHE, click Migrate Threats under Threat Manager.
2. Click “Choose File” to select the JSON file you wish to import, it is in your Downloads folder.
3. Click Import.

Now create a new campaign:

1. Click New Campaign under Campaign Manager
2. Call the campaign Orangeworm
3. Leave everything as is and click Next in the bottom
4. On the Automate Campaign window, click Existing Threats
5. Select Orangeworm and click Add Steps
6. You will see all the steps added to the right side of the window.
7. Click Next
8. Click Start Campaign

For this threat, we are going to execute the dropper leveraging the RunDLL sub-technique (T1218.011 - Rundll32) under technique T1218 - Signed Binary Proxy Execution. To do this:

1. Download the 32-bit DLL from your SCYTHE campaign onto the target system; make sure to save as a .dll (we will use ServiceLogin.dll as the example)
2. Open a Command Prompt
3. Change directory to where the DLL was downloaded
4. Execute with: rundll32.exe ServiceLogin.dll,PlatformClientMain

Now you can observe all the Orangeworm steps as you did in Scenario 1.

DETECTION WITH SYSMON


Now it is time to enable some defenses. In this section, we will install Sysmon, enable an industry-leading configuration, and confirm logging is working on the local workstation. We will then execute various campaigns again to see what visibility we have enabled.

INSTALL SYSMON


We downloaded and extracted Sysmon and two configurations to C:\Sysmon

1. Open a command prompt with administrator privileges
- Right click on the command prompt icon in the taskbar
- Right click on Command Prompt
- Click Run as Administrator
- Click Yes on User Access Control prompt
2. Change directory to Sysmon folder
- cd C:\Sysmon
3. Start Sysmon64 with sysmonconfig.xml (This is Olaf Hartong’s configuration called sysmon-modular: https://github.com/olafhartong/sysmon-modular)
- Sysmon64.exe -i sysmonconfig.xml

EXECUTE CAMPAIGNS


You can now execute Orangeworm again or either of the two campaigns that have been pre-downloaded to your Downloads folder

1. Navigate to C:\Users\student\Downloads
2. Double click either APT33_scythe_client64.exe or WastedLocker_scythe_client64.exe

VIEW SYSMON LOGS THROUGH EVENT VIEWER

1. Click Start and then Event Viewer
2. On the left navigation pane, navigate to: Applications and Services - Microsoft - Windows - Sysmon - Operational
3. You can refresh the logs as campaigns are executed:
- Right Click Operational
- Click Refresh
4. You may want to clear logs between execution to limit the scrolling and searching:
- Right Click Operational
- Click Clear Log…
- Click Clear

SCENARIO 3: EMULATE RYUK


Now that you have a basic understanding of emulating an adversary, let's emulate a more sophisticated adversary: Ryuk

You have 2 options:

1. Create the Ryuk plan yourself following what we have shown. Threat Intelligence available here: 

- https://www.scythe.io/library/threatthursday-ryuk

2. Use the adversary emulation plan we have created and shared in SCYTHE's Community Threat GitHub; we have already uploaded the threat and it is in the SCYTHE Threat Catalog.

If you go with our walkthrough:

1. Click New Campaign under Campaign Manager
2. Call the campaign Ryuk
3. Leave everything as is and click Next in the bottom
4. On the Automate Campaign window, click Existing Threats
5. Select Ryuk and click Add Steps
6. You will see all the steps added to the right side of the window.
7. Click Next
8. Click Start Campaign

Download the 64-bit executable by clicking More Action in the Ryuk campaign. Execute it. Now you can observe all the Ryuk steps as you did in previous scenarios.

**NOTE* This adversary emulation plan has a lot of steps. It takes about 15 minutes to run. It is still much quicker than the manual attack: https://thedfirreport.com/2020/11/05/ryuk-speed-run-2-hours-to-ransom/

Does Sysmon identify all these behaviors?

TRACKING WITH VECTR


The latest version of VECTR is running on the SANS Slingshot C2 Matrix Edition VM. You can access this host through the console or access VECTR from the Unicorn system, via Chrome, the bookmark is on the bookmark bar.

IMPORT SCYTHE LOG INTO VECTR


After logging in, you will be in the Assessments page.

1. Click SCYTHE-DEMO
2. Click Assessment Actions on the top right and then Import Log
3. Click where it says drag and drop
4. Select the CSV that you downloaded for the campaign you want to import. If you did not do this already, go back to SCYTHE - Reports - Click CSV next to report you want to import
5. Click Open
6. Click Submit after it uploads.
7. Now click on the new Campaign

UPDATE TTPS


In the Campaign page, you have various views to select and edit each TTP. 

Click on a TTP to see the details. On the left, you have the Red Team details and on the right the Blue Team details. Since this was imported from a SCYTHE campaign, you have all off the Red Team items filled out. This is a big convenience for performing these exercises.

Now you need to fill out the Blue Team side of each TTP based on the defenses. Was the TTP logged locally? Was there an alert that fired off? What tool logged and alerted? At what time? All of that can be tracked for each TTP.

With each TTP filled out, you can generate reports that show where you stand, where you need to improve, and historical trending of those improvements.


SANS SLINGSHOT C2 MATRIX EDITION


Within this environment, we have another attack machine. It is the SANS Slingshot C2 Matrix Edition which comes with a number of free, open-source C2 frameworks you can use.

Open the console for Slingshot and login with slingshot:slingshot.

The Slingshot CS Matrix Edition was made in collaboration with SANS, Ryan O'Grady, Jorge Orchilles, and C2 Matrix contributors. The goal is to lower the learning curve of installing each C2 framework and getting you straight to testing which C2s work against your organization. Slingshot C2 Matrix Edition is ideal for red team, blue team, and purple team functions.

Slingshot C2 Matrix Edition brings the following C2s pre-installed:

- Covenant
- Empire3 with Starkiller
- Faction
- Koadic
- Merlin
- Metasploit
- SilentTrinity
- Sliver

Slingshot - C2 Matrix Edition also includes a number of other tools that red teamers and penetration testers will find useful such as VECTR for tracking red and purple team exercises.

Click on the C2 you want to play with and follow the howto: https://howto.thec2matrix.com/slingshot-c2-matrix-edition 
