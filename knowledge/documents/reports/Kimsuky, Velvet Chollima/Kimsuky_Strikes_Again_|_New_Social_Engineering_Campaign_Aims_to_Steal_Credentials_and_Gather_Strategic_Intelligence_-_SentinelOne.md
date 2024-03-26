# Reference for threat actor for "Kimsuky, Velvet Chollima"

**Title**: Kimsuky Strikes Again | New Social Engineering Campaign Aims to Steal Credentials and Gather Strategic Intelligence - SentinelOne

**Source**: https://www.sentinelone.com/labs/kimsuky-new-social-engineering-campaign-aims-to-steal-credentials-and-gather-strategic-intelligence/

## Content

























Kimsuky Strikes Again | New Social Engineering Campaign Aims to Steal Credentials and Gather Strategic Intelligence - SentinelOne











































































































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
Kimsuky Strikes Again | New Social Engineering Campaign Aims to Steal Credentials and Gather Strategic Intelligence 


Aleksandar Milenkoski 
/


June 6, 2023





Executive Summary

SentinelLabs has been tracking a social engineering campaign by the North Korean APT group Kimsuky targeting experts in North Korean affairs, part of a broader campaign discussed in a recent NSA advisory.
The campaign has the objective of stealing Google and subscription credentials of a reputable news and analysis service focusing on North Korea, as well as delivering reconnaissance malware.
Kimsuky engages in extensive email correspondence and uses spoofed URLs, websites imitating legitimate web platforms, and Office documents weaponized with the ReconShark malware.
This activity indicates Kimsuky’s growing dedication to social engineering and highlights the group’s increasing interest in gathering strategic intelligence.

Overview
In collaboration with NK News, a leading subscription-based service that provides news and analyses about North Korea, SentinelLabs has been tracking a targeted social engineering campaign against experts in North Korean affairs from the non-government sector. The campaign focuses on theft of email credentials, delivery of reconnaissance malware, and theft of NK News subscription credentials. Based on the used malware, infrastructure, and tactics, we assess with high confidence that the campaign has been orchestrated by the Kimsuky threat actor.
The social engineering tactics and some infrastructure characteristics closely relate to a Kimsuky activity privately reported by PwC and discussed in an NSA advisory published during the writing of this article. We focus on the specific targeting of expert analysts of North Korean affairs by impersonating NK News and stealing NK News credentials, and provide details on used TTPs to support collaborative hunting and detection efforts.
Kimsuky, a suspected North Korean advanced persistent threat (APT) group whose activities align with the interests of the North Korean government, is known for its global targeting of organizations and individuals. Operating since at least 2012, the group often employs targeted phishing and social engineering tactics to gather intelligence and access sensitive information.
A hallmark of the activity we discuss in this post is Kimsuky’s focus on establishing initial contact and developing a rapport with their targets prior to initiating malicious activities. As part of their initial contact strategy, the group impersonated Chad O’Carroll, the founder of NK News and the associated holding company Korea Risk Group, using an attacker-created domain, nknews[.]pro, which closely resembles the legitimate NK News domain nknews.org. The initial email requests the review of a draft article analyzing the nuclear threat posed by North Korea.
If the target engages in the conversation, Kimsuky uses the opportunity to deliver a spoofed URL to a Google document, which redirects to a malicious website specifically crafted to capture Google credentials. Kimsuky may also deliver a weaponized Office document that executes the ReconShark reconnaissance malware.
Further, Kimsuky’s objective extends to the theft of subscription credentials from NK News. To achieve this, the group distributes emails that lure targeted individuals to log in on the malicious website nknews[.]pro, which masquerades as the authentic NK News site. The login form that is presented to the target is designed to capture entered credentials.
This Kimsuky activity indicates the group’s growing efforts to establish early communication and foster trust with their targets prior to initiating malicious operations, including the delivery of malware. Their approach highlights the group’s commitment to creating a sense of rapport with the individuals they target, potentially increasing the success rate of their subsequent malicious activities.
By actively targeting high-profile experts in North Korean affairs and stealing subscription credentials from prominent news and analysis outlets focussing on North Korea, Kimsuky demonstrates a heightened curiosity in understanding how the international community perceives developments concerning North Korea, such as the country’s military activities. These actions are probably part of their broader objective to gather strategic intelligence, contributing to North Korea’s decision-making processes.
Google Credential Theft
We observed Kimsuky distributing an HTML-formatted phishing email to selected individuals, which requests the review of a draft article analyzing the nuclear threat posed by North Korea. The email primarily aims to initiate a subsequent conversation and is intentionally designed to appear benign: It impersonates NK News leadership and lacks any malicious artifacts.


Initial email
If the target engages in the conversation, Kimsuky eventually follows up with an email that contains an URL to a Google document.
Follow-up email
If the target is not responsive, Kimsuky follows up with a reminder email in an attempt to engage the target in conversation.
Reminder email
The URL’s destination is manipulated through the spoofing technique of setting the href HTML property to direct to a website created by Kimsuky. This method, commonly employed in phishing attacks, creates a discrepancy between the perceived legitimacy of the link (a genuine Google document) and the actual website visited upon clicking the URL.
The displayed URL to a Google document points to an actual article hosted on Google Docs, delving into the topic of the North Korean nuclear threat. The article contains visible edits to give the impression of a genuine draft article, aligning with Kimsuky’s luring tactic.
Google document
The spoofed destination of the URL redirects the target to an attacker-created website that masquerades as a legitimate Google Docs site for requesting document access, such as
https[://]drive-google[.]shanumedia[.]com/pdf/ul/ji78fghJHKtgfLKJIO/s2.php?menu=ZGFu[...]vbQ==
The Base-64 encoded segment, that is, the value of the menu URL query parameter, resolves to the target’s email address.
This serves as a means of transporting the target’s address to the fake Google Docs site, which enables the site to dynamically display the address, creating a personalized and convincing appearance of legitimacy. The design and functionality of this site suggest its potential for reuse in targeting different individuals.
Malicious Google Docs site
We were unable to analyze the functionality behind the Request access web element as the group has taken down the site. However, given the theme of the site, we suspect that it has been designed to capture entered Google credentials.
During conversations with targeted individuals, Kimsuky also seizes any available opportunity to distribute password-protected weaponized Office documents that deploy the ReconShark reconnaissance malware. ReconShark exfiltrates information relevant for conducting subsequent precision attacks, such as deployed detection mechanisms and hardware information. The implementation of the ReconShark variant we observed in this activity remains the same as the one covered in our previous post on Kimsuky activity, with the main distinction being the use of a different C2 server: staradvertiser[.]store. This domain resolves to the IP address 162.0.209[.]27, which has hosted domains that have been attributed to Kimsuky in previous research, such as sesorin[.]lol and rfa[.]ink. Kimsuky’s use of ReconShark as part of this activity underscores the malware’s central role within the group’s current operational playbook.
NK News Credential Theft
We also observed Kimsuky attempting to steal credentials for the subscription service of NK News, which is known for its comprehensive expert analyses and news reports. Gaining access to such reports would provide Kimsuky with valuable insights into how the international community assesses and interprets developments related to North Korea, contributing to their broader strategic intelligence-gathering initiatives.
In order to accomplish this, Kimsuky distributes an email that lure targeted individuals to log in to a spoofed NK News subscription service. The emails prompt the recipients to confirm their NK News accounts under the pretext of recent security updates.
Phishing Email
The fake login site, hosted at https[://]www.nknews[.]pro/ip/register/, features a login form with the standard web elements, such as Sign In, Sign Up, and Forgot Password? buttons. When clicked, the Sign In button executes the loginAct JavaScript function, whereas the rest of the buttons do not conduct any activities.
Fake NK News login site
The JavaScript code captures entered credentials by issuing an HTTP POST request to https[://]www.nknews[.]pro/ip/register/login[.]php and then redirects the user to the legitimate NK News site.
JavaScript code
The main website hosted at https[://]www.nknews[.]pro redirects to the legitimate NK News site, https://nknews.org, and uses a certificate issued by Sectigo:

Thumbprint: a1597d197e9b084a043ada5c7dac1f9b6d7f7af3
Serial number: 00f342582c9a299acf2452aaf5115c5be0

The domain nknews[.]pro, registered through Namecheap, also resolves to the Kimsuky-linked IP address 162.0.209[.]27. The URL https[://]www.nknews[.]pro/config[.]php hosts a password-protected remote management site, which is likely an implementation of the b374k tool, based on the implementation of the login site and the presence of the config.php file. The Kimsuky group is known to use this tool for remote management of its infrastructure.
b374k login site
Conclusion
SentinelLabs remains actively engaged in monitoring the activities conducted by Kimsuky. The findings presented in this post highlight the group’s persistent commitment to targeted social engineering attacks and underscore the need for increased awareness and understanding of Kimsuky’s tactics among potential targets. Maintaining vigilance and implementing effective security measures are imperative to mitigate the risks posed by this persistent threat actor.
Indicators of Compromise



Indicator
Description


nknews[.]pro
Phishing email sender domain


chad.ocarroll@nknews[.]pro
Phishing email sender address


membership@nknews[.]pro
Phishing email sender address


https[://]www.nknews[.]pro
Website impersonating NK News


https[://]www.nknews[.]pro/config[.]php
Website impersonating NK News: b374k login site


https[://]www.nknews[.]pro/ip/register/
Website impersonating NK News: Fake NK News login site


https[://]www.nknews[.]pro/ip/register/login[.]php
Website impersonating NK News: NK News credential theft endpoint


https[://]staradvertiser.store/piece/ca[.]php
ReconShark payload hosting endpoint


https[://]staradvertiser.store/piece/r[.]php
ReconShark C2 server endpoint


162.0.209[.]27
Website impersonating NK News, ReconShark C2 server: IP address


4150B40C00D8AB2E960AA059159149AF3F9ADA09
Malicious document (password-protected): SHA1 hash


7514FD9E5667FC5085373704FE2EA959258C7595
Malicious document: SHA1 hash


41E39162AE3A6370B1100BE2B35BB09E2CBE9782
ReconShark: SHA1 hash







adversary




Share

PDF












Aleksandar Milenkoski



Aleksandar Milenkoski is a Senior Threat Researcher at SentinelLabs. With expertise in malware research and focus on targeted attacks, he brings a blend of practical and deep insights to the forefront of cyber threat intelligence. Aleksandar has a PhD in system security and is the author of numerous reports on cyberespionage and high-impact cybercriminal operations, conference talks, and peer-reviewed research papers. His research has won awards from SPEC, the Bavarian Foundation for Science, and the University of Würzburg.


















PrevRadare2 Power Ups | Delivering Faster macOS Malware Analysis With r2 CustomizationNextLABScon Replay | Star-Gazing: Using a Full Galaxy of YARA Methods to Pursue an Apex Actor 



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




























 



