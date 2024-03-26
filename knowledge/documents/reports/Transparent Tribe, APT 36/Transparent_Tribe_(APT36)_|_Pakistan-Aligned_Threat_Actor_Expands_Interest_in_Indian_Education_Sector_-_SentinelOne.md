# Reference for threat actor for "Transparent Tribe, APT 36"

**Title**: Transparent Tribe (APT36) | Pakistan-Aligned Threat Actor Expands Interest in Indian Education Sector - SentinelOne

**Source**: https://www.sentinelone.com/labs/transparent-tribe-apt36-pakistan-aligned-threat-actor-expands-interest-in-indian-education-sector/

## Content

























Transparent Tribe (APT36) | Pakistan-Aligned Threat Actor Expands Interest in Indian Education Sector - SentinelOne











































































































ABOUT
CVE DATABASE
CONTACT
VISIT SENTINELONE.COM
 

en

English日本語DeutschEspañolFrançaisItalianoDutch한국어 



Get a Demo









 
Back
 



ABOUT
CVE DATABASE
CONTACT
VISIT SENTINELONE.COM
 


Get a Demo

































Advanced Persistent Threat 
Transparent Tribe (APT36) | Pakistan-Aligned Threat Actor Expands Interest in Indian Education Sector 


Aleksandar Milenkoski 
/


April 13, 2023





Executive Summary

SentinelLabs has been tracking a cluster of malicious documents that stage Crimson RAT, distributed by APT36 (Transparent Tribe).
We assess that this activity is part of the group’s previously reported targeting of the education sector in the Indian subcontinent.
We observed APT36 introducing OLE embedding to its typically used techniques for staging malware from lure documents and versioned changes to the implementation of Crimson RAT, indicating the ongoing evolution of APT36’s tactics and malware arsenal.

Overview
SentinelLabs has been tracking a recently disclosed cluster of malicious Office documents that distribute Crimson RAT, used by the APT36 group (also known as Transparent Tribe) targeting the education sector. This post summarizes our observations highlighting the group’s continuous change in used malware staging techniques and Crimson RAT implementations.
Transparent Tribe is a suspected Pakistan-based threat group active since at least 2013. The group is not very sophisticated; however, it is a highly persistent threat actor that continuously adapts its operational strategy. Transparent Tribe has previously focused mainly on Indian military and government personnel, but it has recently expanded its scope to include educational institutions and students in the Indian subcontinent. Crimson RAT is a consistent staple in the group’s malware arsenal the adversary uses in its campaigns.
The names and content of the lure documents, the associated domains, and the use of Crimson RAT suggest that the activities discussed in this post are part of a previously reported broader targeting of the education sector by Transparent Tribe.
Further, the PDB paths of some Crimson RAT samples we analyzed contain the word Wibemax, which is also contained in the PDB paths of Crimson RAT payloads observed in a previous Transparent Tribe campaign.
Wibemax matches the name of a Pakistani software development company, but at this time we have not identified a clear relationship to the adversary.
It is worth noting that there are high confidence assessments of Transparent Tribe leveraging third parties to support their operation, such as the Pakistani web hosting provider Zain Hosting.
Our analysis reinforces the assessment that closely monitoring the research endeavors of adversary nations has become an important objective for the adversary, underscoring the crucial role this activity plays in fulfilling the goals and aspirations of the authorities whose interests Transparent Tribe represents.
Malicious Documents
The documents that Transparent Tribe distributes have education-themed content and names such as assignment or Assignment-no-10, and indicate creation dates of July and August 2022. Based on known behavior of this group, we suspect that the documents have been distributed to targets as attachments to phishing emails. Consistent with known Transparent Tribe tactics, we observed that some of the documents have been hosted on file hosting services and attacker-created domains, such as s1.fileditch[.]ch, cloud-drive[.]store, and drive-phone[.]online.
It is important to note that cloud-drive[.]store and drive-phone[.]online have been previously linked to Transparent Tribe activities targeting the education sector and assessed as domains prepared for future use. Further, drive-phone[.]online closely resembles the phone-drive[.]online domain recently observed hosting Transparent Tribe malware targeting Indian and Pakistani Android users.
The malicious documents we analyzed stage Crimson RAT using Microsoft Office macros or OLE embedding.
The macro code executes when the documents are opened, and its functionality is consistent with known Transparent Tribe macro variants. The macros create and decompress an embedded archive file in the %ALLUSERSPROFILE% directory (C:\ProgramData) and execute the Crimson RAT payload within. Some macros insert text in the document, which is typically education-themed content relating to India.


Macro implementation
Macro-inserted document text
In addition to macros, we observed that Transparent Tribe have adopted OLE embedding as a technique to stage Crimson RAT. Malicious documents that implement this technique require users to double-click a document element. The documents distributed by Transparent Tribe typically display an image (a “View Document” graphic) indicating that the document content is locked. This lures users to double-click the graphic to view the content, which activates an OLE package that stores and executes Crimson RAT masquerading as an update process (MicrosoftUpdate.exe).
The “View Document” graphic
OLE stream that stores Crimson RAT
Transparent Tribe is known to experiment with different malware staging techniques, which include distributing executables with embedded documents or documents that execute designated Crimson RAT loaders. The adoption of OLE embedding further highlights the group’s continuous experimentation with malware staging techniques.
Crimson RAT Implementations
We observed a variety of Crimson RAT .NET implementations, with compilation timestamps between July and September 2022. The Crimson RAT payloads we analyzed use the richa-sharma.ddns[.]net domain for C2 purposes and support either 40 or 65 commands, most of which have been documented in previous research. Features of Crimson RAT include exfiltrating system information, capturing screenshots, starting and stopping processes, and enumerating files and drives.
A Crimson RAT command dispatch routine
Some Crimson RAT variants are stripped of debug information, whereas others have PDB paths that contain a date stamp, the word Richa, which relates to the configured C2 domain, and the word Wibemax. Portions of these PDB paths overlap those of Crimson RAT payloads observed in a previous Transparent Tribe campaign, such as D:\Projects\Wibemax\WinP\WinP\obj\Debug\WinP.pdb and D:\Projects\Wibemax\Windows RAT\1 Windows 10 Client\Win8P-Sunny\2022-04-15-Win8P Sunny\obj\Debug\FUJIKBattery.pdb.
Crimson RAT PDB paths
We observed different Crimson RAT version identifiers: R.S.8.8., R.S.8.9, R.S.8.1, and R.S.8.6. We speculate that the R.S. components of the identifiers may relate to the configured C2 domain (richa-sharma.ddns[.]net) and the numerical components may specify a version (build) number. This aligns with a documented Crimson RAT variant with the identifier S.L.2.2., which has used the sunnyleone.hopto[.]org domain for C2 purposes.
As an anti-analysis measure, Crimson RAT variants delay their execution for a given time period, for example, 61, 180, or 241 seconds. Most of the Crimson RAT variants we analyzed evaluate whether they execute at a machine named G551JW or DESKTOP-B83U7C5 and establish persistence by creating a registry key under \SOFTWARE\Microsoft\Windows\CurrentVersion\Run only if the victim’s machine name differs. G551JW or DESKTOP-B83U7C5 may be the names of the machines where Crimson RAT developers have been running test executions.
Crimson RAT variants implement different obfuscation techniques of varying intensities, for example, simple function name malformation and dynamic string resolution. We observed the use of the Eazfuscator obfuscator in a Crimson RAT sample named NewOrleans. Evidence suggests that the Crimson RAT developers have patched the routine that evaluates the trial period of Eazfuscator to enable the execution of the malware after the trial period expires.
Eazfuscator trial period evaluation in NewOrleans
Eazfuscator trial expiry message
With previous variants of Crimson RAT obfuscated using Crypto Obfuscator, the addition of Eazfuscator to the obfuscation techniques used by Transparent Tribe highlights the continuous maintenance and development of the RAT.
Conclusion
Transparent Tribe is a highly motivated and persistent threat actor that regularly updates its malware arsenal, operational playbook, and targets. Our analysis further demonstrates this characteristic of the group by spotlighting the adoption of OLE embedding as a technique for staging malware from lure documents and the Eazfuscator obfuscator to protect Crimson RAT implementations. Transparent Tribe’s constantly changing operational and targeting strategies require constant vigilance to mitigate the threat posed by the group.
Indicators of Compromise



SHA1
Description


738d31ceca78ffd053403d3b2bc15847682899a0
Malicious document


9ed39c6a3faab057e6c962f0b2aaab07728c5555
Malicious document


af6608755e2708335dc80961a9e634f870aecf3c
Malicious document


e000596ad65b2427d7af3313e5748c2e7f37fba7
Malicious document


fd46411b315beb36926877e4b021721fcd111d7a
Malicious document


516db7998e3bf46858352697c1f103ef456f2e8e
Crimson RAT


842f55579db786e46b20f7a7053861170e1c0c5e
Crimson RAT


87e0ea08713a746d53bef7fb04632bfcd6717fa9
Crimson RAT


911226d78918b303df5110704a8c8bb599bcd403
Crimson RAT


973cb3afc7eb47801ff5d2487d2734ada6b4056f
Crimson RAT






Domain
Description


richa-sharma.ddns[.]net
C2 server


cloud-drive[.]store
Malware hosting location


drive-phone[.]online
Malware hosting location


s1.fileditch[.]ch
Malware hosting location







apt




Share

PDF












Aleksandar Milenkoski



Aleksandar Milenkoski is a Senior Threat Researcher at SentinelLabs. With expertise in malware research and focus on targeted attacks, he brings a blend of practical and deep insights to the forefront of cyber threat intelligence. Aleksandar has a PhD in system security and is the author of numerous reports on cyberespionage and high-impact cybercriminal operations, conference talks, and peer-reviewed research papers. His research has won awards from SPEC, the Bavarian Foundation for Science, and the University of Würzburg.


















PrevDissecting AlienFox | The Cloud Spammer’s Swiss Army KnifeNextKimsuky Evolves Reconnaissance Capabilities in New Global Campaign 



Related Posts






ScarCruft | Attackers Gather Strategic Intelligence and Target Cybersecurity Professionals 

January 22 2024







Sandman APT | China-Based Adversaries Embrace Lua 

December 11 2023







Elephant Hunting | Inside an Indian Hack-For-Hire Group 

November 16 2023







Search

Search ...




Sign Up


Get notified when we post new content.



Thanks! Keep an eye out for new content!



Recent PostsSNS Sender | Active Campaigns Unleash Messaging Spam Through the CloudFebruary 15, 2024China’s Cyber Revenge | Why the PRC Fails to Back Its Claims of Western EspionageFebruary 12, 2024ScarCruft | Attackers Gather Strategic Intelligence and Target Cybersecurity ProfessionalsJanuary 22, 2024Labs Categories Crimeware

Security Research

Advanced Persistent Threat

Adversary

LABScon

Security & Intelligence

 









SentinelLabs In the era of interconnectivity, when markets, geographies, and jurisdictions merge in the melting pot of the digital domain, the perils of the threat ecosystem become unparalleled. Crimeware families achieve an unparalleled level of technical sophistication, APT groups are competing in fully-fledged cyber warfare, while once decentralized and scattered threat actors are forming adamant alliances of operating as elite corporate espionage teams.

Recent PostsSNS Sender | Active Campaigns Unleash Messaging Spam Through the CloudFebruary 15, 2024China’s Cyber Revenge | Why the PRC Fails to Back Its Claims of Western EspionageFebruary 12, 2024ScarCruft | Attackers Gather Strategic Intelligence and Target Cybersecurity ProfessionalsJanuary 22, 2024Sign Up


Get notified when we post new content.



Thanks! Keep an eye out for new content!



 







Twitter




LinkedIn









©2024 SentinelOne, All Rights Reserved. 




























 



