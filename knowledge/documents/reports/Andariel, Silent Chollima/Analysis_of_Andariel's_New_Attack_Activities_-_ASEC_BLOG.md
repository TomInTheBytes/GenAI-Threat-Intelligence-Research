# Reference for threat actor for "Andariel, Silent Chollima"

**Title**: Analysis of Andariel's New Attack Activities - ASEC BLOG

**Source**: https://asec.ahnlab.com/en/56405/

## Content















Analysis of Andariel's New Attack Activities - ASEC BLOG



































































 

Malware Information
AhnLab Detection
Statistics
Response Guide
AhnLab
 














Posted By Sanseo  , August 31, 2023 

Analysis of Andariel’s New Attack Activities 
Contents1. Past attack cases…. 1.1. Cases of Innorix Agent abuse…….. 1.1.1. NukeSped variant – Volgmer…….. 1.1.2. Andardoor…….. 1.1.3. 1th Troy Reverse Shell…. 1.2. Cases of attacks against Korean corporations…….. 1.2.1. TigerRat…….. 1.2.2. Black RAT…….. 1.2.3. NukeSped variants2. Cases of recent attacks…. 2.1. Cases of Innorix Agent abuse…….. 2.1.1. Goat RAT…. 2.2. Cases of attacks against Korean corporations…….. 2.2.1. AndarLoader…….. 2.2.2. DurianBeacon3. Connections to recent attack cases4. Connections to past attack cases of the Andariel group5. Conclusion
The Andariel threat group which usually targets Korean corporations and organizations is known to be affiliated with the Lazarus threat group or one of its subsidiaries. Attacks against Korean targets have been identified since 2008. Major target industries are those related to national security such as national defense, political organizations, shipbuilding, energy, and communications. Various other companies and institutes in Korea including universities, logistics, and ICT companies are also becoming attack targets. [1] (this report only supports the Korean version)
During the initial compromise stage, the Andariel threat group usually employs spear phishing, watering hole, and supply chain attacks. Additionally, there are cases where the group abuses central management solutions during the malware installation process. [2] A notable fact about the group is its creation and use of various malware types in its attacks. There are many backdoor types, including Andarat, Andaratm, Phandoor, and Rifdoor used in the past attacks, as well as TigerRAT [3] and MagicRAT [4] which have been detected for the past few years.
AhnLab Security Emergency response Center (ASEC) is continuously monitoring the attacks of the Andariel threat group. This blog post will cover details surrounding the recently identified attacks deemed to be perpetrated by the Andariel group. Note that because the malware strains and C&C servers identified in past attack cases were not used in the aforementioned attacks, there is no direct connection. Thus, in order to identify the connection between the recent attacks and the Andariel threat group, this post will first analyze the cases of attacks by the Andariel group in the first half of 2023. Then the analysis will be used to identify the possible link between the attacks and the threat group. Details confirmed in the past attack cases will be included if necessary.
One characteristic of the attacks identified in 2023 is that there are numerous malware strains developed in the Go language. In an attack case where Innorix Agent was used, a Reverse Shell developed in Go was used. Black RAT was used in attacks targeting Korean companies afterward. Such trends continued into the recent cases, where other malware strains developed in Go such as Goat RAT and DurianBeacon are being used in attacks. Besides the Go version, DurianBeacon has a version developed in the Rust language as well.

Figure 1. Source code information of DurianBeacon developed in Go
Because the initial distribution case could not be identified directly, this post will conduct an analysis based on the malware strains used in the attacks. Note that various malware types are being used in the attacks. When a name given by the malware creator can be confirmed, the said name will be used. If not, the names of similar malware types or AhnLab’s detection name will be used.
1. Past attack cases
1.1. Cases of Innorix Agent abuse
In February 2023, ASEC shared the case where the Andariel threat group distributed malware to users with a vulnerable version of Innorix Agent in the blog post “Distribution of Malware Exploiting Vulnerable Innorix: Andariel.” [5] The Innorix Agent program abused in distribution is a file transfer solution client program. According to the post regarding the vulnerability by the Korea Internet & Security Agency (KISA), the affected versions were found to be INNORIX Agent 9.2.18.450 or earlier, which were advised to be applied with the security update. [6] (this content only supports the Korean version)

Figure 2. Malware being distributed using Innorix Agent which had been vulnerable in the past
An investigation of the malware strains used in the attacks based on past attack cases revealed that multiple Korean universities were infected with malware strains. Most malware types used in the attacks were backdoors, and no previously identified type was present. However, because there is a connection with other malware strains used in the past or those used in subsequent attacks, a brief summary of their characteristics will be given.
1.1.1. NukeSped variant – Volgmer
As covered in the ASEC Blog before, this malware strain uses the following 0x10 byte key in the process of communicating with the C&C server to encrypt packets. The key value in question is the same as the one employed in Volgmer used by the Hidden Cobra (Lazarus) threat group, as stated in a report by the United States Cybersecurity & Infrastructure Security Agency (CISA). [7] (page currently unavailable)

Key: 74 61 51 04 77 32 54 45 89 95 12 52 12 02 32 73

Volgmer was also used in comparatively recent attacks. It runs by reading the configuration data saved in the registry key “HKLM\SYSTEM\CurrentControlSet\Control\WMI\Security” and uses the HTTP protocol to communicate with the C&C server. Such characteristics are highly similar to the type mentioned in the CISA report in the past, which means that the malware continues to be used in attacks with no significant variants being released. While the same key value was used in both the malware mentioned in this post and Volgmer, there is a difference: the malware used in the current attack cases uses the key value to encrypt the packets used to communicate with the C&C server. Meanwhile, Volgmer uses the value to decrypt the encrypted configuration data saved in the registry.
Accordingly, it is not entirely accurate to categorize the above malware strain as a type of Volgmer, so it was categorized as a variant of NukeSped instead. The malware is a comparatively simple backdoor that only provides basic features. Notably, the Batch script used in the self-deletion process is similar to the one used in NukeSped types in the past.


Figure 3. Batch script used in the self-deletion process
1.1.2. Andardoor
Developed in .NET, this malware is a backdoor that uses the name TestProgram. Based on AhnLab’s detection name, it is classified as Andardoor. It is notable for being obfuscated using the Dotfuscator tool. It offers various features for controlling the infected system, such as file and process tasks, executing commands, and capturing screenshots. SSL encryption is used for communication with the C&C server. For the server name, it designated the “clientName” string.

Figure 4. SSL communications routine with the C&C server
1.1.3. 1th Troy Reverse Shell
1th Troy is a Reverse Shell malware developed in Go. The following string included in the binary shows that the malware has the simple name of “Reverse_Base64_Pipe” and the malware’s creator classified the malware as “1th Troy”.
G:/Code/01__1th Troy/Go/Reverse_Base64_Pipe/Client/client.go
Being a Reverse Shell that only provides basic commands, the commands supported include “cmd”, “exit”, and “self delete”. They support the command execution, process termination, and self-deletion features respectively.

Figure 5. Reverse Shell with a simple structure
1.2. Cases of attacks against Korean corporations
The Andariel group also distributed malware in March 2023 in its attacks against the Korean defense industry and an electronics device manufacturer. The method of initial compromise has not yet been identified, but logs of the mshta.exe process installing TigerRat and the mshta.exe process being terminated were confirmed through the AhnLab Smart Defense (ASD) infrastructure. This means that the malware strains were installed through a script-type malware with the spear phishing attack method.

Figure 6. Mshta process installing TigerRat
Malware strains used in attacks were generally backdoor types. TigerRat, which has been used by the Andariel group since the past, was also included.
1.2.1. TigerRat
Tiger Rat is a RAT-type malware with its name given by KISA [8] and has been consistently employed by the Andariel threat group since 2020. It is known to be generally distributed through malicious document files containing macros that are attached to spear phishing emails, or through watering hole attacks. [9] There are also cases where the Andariel group targeted Korean corporations that use vulnerable versions of VMware Horizon and launched Log4Shell vulnerability attacks to install TigerRat. [10]
Besides offering basic features such as file tasks and executing commands, TigerRat is a backdoor that supports other various features such as collecting information, keylogging, capturing screenshots, and port forwarding. One of its characteristics is that there is an authentication process upon the first communication session with the C&C server. In past versions, the string shown below disguised as SSL communications was used in the authentication process. Depending on the malware version, either the string “HTTP 1.1 /member.php SSL3.4” or “HTTP 1.1 /index.php?member=sbi2009 SSL3.3.7” must be sent to the C&C server, and the string “HTTP 1.1 200 OK SSL2.1” should be sent in return for authentication to be successful.

Figure 7. String used in the authentication process for the C&C server – past version
However, in the recently identified TigerRat type, the following random strings 0x20 in size are used. These strings are thought to be the MD5 hash for “fool” (dd7b696b96434d2bf07b34f9c125d51d) and “iwan” (01ccce480c60fcdb67b54f4509ffdb56). It seems that the threat actor used random strings in the authentication process to evade network detection.

Figure 8. String used in authentication to the C&C server – latest version

C&C request string: dd7b696b96434d2bf07b34f9c125d51d
C&C response string: 01ccce480c60fcdb67b54f4509ffdb56

1.2.2. Black RAT
Black Rat is a backdoor-type malware that is likely created by the threat actor. Like other malware strains, it was developed in Go. While the 1th Troy Reverse Shell identified in the previous case only supports a basic command execution feature, Black Rat provides many additional features such as downloading files and capturing screenshots.

Figure 9. Features supported by Black RAT
Examining the following string included in the binary shows that the malware creator classified the malware as a RAT type and named it Black.
I:/01___Tools/02__RAT/Black/Client_Go/Client.go
1.2.3. NukeSped variants
A typical NukeSped-type backdoor was also used in this attack. Supported features include network scanning, process and file lookup, file upload/download, and command execution. The names of the APIs to be used are encrypted as shown below. These are decrypted and the API names are taken from somewhere else. A key with a size of 0x26 is used for decryption.

Figure 10. Obfuscated API string

Key value used for decryption: i<6fu>-0|HSLRCqd.xHqMB]4H#axZ%5!5!?SQ&

This NukeSped variant also uses a Batch script for self-deletion, but it is slightly different from the one used in the previous attacks.

Figure 11. Batch script used in the self-deletion process
There are two types of identified NukeSped variants: Reverse Shell and Bind Shell types. Both listen to port number 10443. This NukeSped variant has an authentication process before communicating with the C&C server like TigerRat. Yet whereas TigerRat disguised the process as SSL communications, NukeSped disguised it as HTTP communications. Thus, after sending the following POST request, an accurately matching HTTP response must be received for the malware to commence communications with the C&C server.

Figure 12. HTTP packet used in authentication
2. Cases of Recent Attacks
ASEC is monitoring attacks of the Andariel group and has recently identified cases of Innorix Agent being abused to install malware. Unlike past cases where Innorix Agent was downloading malware strains, the recent case directly creates the malware file, so it is not certain whether the attacks are vulnerability attacks or if Innorix Agent was simply abused.
Malware strains identified in these attacks are not those that had been used by the Andariel group in the past, but aside from the fact that Innorix was used in the attacks, the current attack is similar to past attack cases in that the attack targets are Korean universities. While the attack was being perpetrated, attack cases against Korean ICT companies, electronic device manufacturers, the shipbuilding industry, and the manufacturing industry were identified as well. Analysis showed that there was a connection with the malware strains used in attack cases where Innorix was abused.
This part will analyze each attack case and malware strains used in the attacks. Afterward, a summary will be given of the conclusion that the same threat actor is behind these attacks and the basis behind the claim, as well as the relationship between the current attacks and past attack cases of the Andariel threat group.
2.1. Cases of Innorix Agent abuse
2.1.1. Goat RAT
In recent attacks against Korean universities, there were cases where Innorix Agent installed malware strains. Innorix Agent installed the malware strains under the name “iexplorer.exe”. This is one of the names that has been often used by the Andariel group.

Figure 13. Using Innorix Agent to install Goat RAT
E:/Projects/Malware/6_Goat_23/Goat/Goat.goE:/Projects/Malware/6_Goat_23/Goat/define.goE:/Projects/Malware/6_Goat_23/Goat/anti-vaccine.goE:/Projects/Malware/6_Goat_23/Goat/command.go
Although the recent version is obfuscated unlike the Go-based backdoor-type malware used in past attacks, basic file tasks, self-deletion features, etc. can be identified. There are also logs where the following commands were executed.
> cmd /c tasklist> cmd /c ipconfig /all

Figure 14. Obfuscated function name
2.2. Cases of attacks against Korean corporations
2.2.1. AndarLoader
Aside from the attack cases where Innorix Agent was abused, ASEC identified another type of attack in a similar period of time. While the initial distribution route has not yet been ascertained, the malware strains used in the attacks were obfuscated with a tool called Dotfuscator like the .NET malware strains classified as Andardoor. Another common trait is that both types use SSL communications with the C&C server. Unlike Andardoor which used “clientName” when connecting to the C&C server, this attack case used the string “sslClient”.

Figure 15. SSL connection process with the C&C server
Whereas Andardoor had most of its features already implemented, this malware strain only has a downloader feature to download and execute executable data such as .NET assemblies from external sources. Out of the commands sent from the C&C server, the commands shown below can be used to execute or terminate the received code. Behaviors performed by the threat actor using AndarLoader include installing Mimikatz in the infected system, which has been confirmed through a recorded log.
At the time of analysis, the C&C server was unavailable for access and the part in charge of the actual features could not be investigated, so no direct similarity with Andardoor could be confirmed. However, the use of the same obfuscation tool or the similarities in the communication process with the C&C server led AhnLab to categorize this malware as the AndarLoader type.
CommandFeaturealibabaExecute the downloaded .NET assembliesfacebookExecute the downloaded .NET methodexitTerminatevanishSelf-delete and terminateTable 1. List of commands that can be executed
Among the commands given by the treat actor that AndarLoader executes, there is a command to terminate the mshta.exe process. The fact that AndarLoader was installed via PowerShell and the mshta.exe process was involved leaves the possibility that this is a spear phishing attack like the cases of attacks covered above.

Figure 16. Commands executed by AndarLoader
Additionally, logs of the mshta.exe process connecting to the C&C server can be found in systems infected with AndarLoader.

Figure 17. Network communications log
The domain kro.kr was used as the C&C and download URLs. This is a domain generally used by the Kimsuky threat group. Also, the fact that Ngrok was installed for RDP connection during the attack process shows how the case is similar to the attack pattern of the Kimsuky group.

Figure 18. Log showing the installed Ngrok being executed
2.2.2. DurianBeacon
While investigating the AndarLoader malware, AhnLab identified that a malware strain named DurianBeacon was also used in the attack process. There are two versions of DurianBeacon, one developed in Go and the other developed in Rust. Both are backdoors that can perform malicious behaviors by receiving the threat actor’s commands from the C&C server.
A. Go Version
Examining the following strings included in the binary indicates that the malware creator named this malware strain DurianBeacon.
G:/Dev/Go/DurianBeacon/Command.goG:/Dev/Go/DurianBeacon/SSL.goG:/Dev/Go/DurianBeacon/Utils.goG:/Dev/Go/DurianBeacon/main.go
The Go version of DurianBeacon uses the SSL protocol to communicate with the C&C server. After initial access, it sends the infected system’s IP information, user name, desktop name, architecture, and file names before awaiting commands. When a command is sent, it returns a result. Supported features besides collecting basic information about the infected system include file download/upload, lookup, and command execution features.

Figure 19. Features supported by DurianBeacon
Because the SSL protocol is used, communications packets are encrypted. The following packet structure is used internally.
OffsetSizeDescription0x000x04Command number0x040x04Size of the command argument0x08VariableCommand argumentTable 2. Command packet structure of DurianBeacon
The features corresponding to each command code are as follows.
CommandFeature0x00Hibernate0x01Interval0x02Execute commands (return results)0x03Look up directories0x04Drive information0x05, 0x06, 0x07, 0x08Upload files0x09, 0x0A, 0x0BDownload files0x0CCreate directories0x0DDelete files0x0ERun commands0x0FTerminateTable 3. List of DurianBeacon commands
After executing commands, the malware sends the success status or the command execution results to the threat actor’s C&C server. The response is also similar to the command packet.
OffsetSizeDescription0x000x04Response number0x040x04Size of the command execution results0x08VariableCommand execution resultsTable 4. Structure of the DurianBeacon response packet
ResponseDescription0x00Return command results0x01, 0x02, 0x03Look up directories (start, terminate, etc.)0x04Drive information0x05, 0x06, 0x07Upload files (error, success, etc.)0x08, 0x09, 0x0ADownload files (error, success, etc.)0x0B, 0x0CCreate directories (failure or success)0x0D, 0x0EDelete files (failure or success)0x0F, 0x10Run commands (failure or success)Table 5. DurianBeacon’s response list
B. Rust Version
Investigation of related files revealed that the Rust version of DurianBeacon was also used in attacks.

PDB information: C:\Users\Anna\Documents\DurianBeacon\target\x86_64-pc-windows-msvc\release\deps\DurianBeacon.pdb

DurianBeacon supports packet encryption using XOR aside from SSL to communicate with the C&C server, using the key 0x57.

Figure 20. Rust version of DurianBeacon supporting XOR encryption
The packet structure and commands are also the same as the Go version. The Rust version of DurianBeacon sends the keyword “durian2023” alongside the infected system’s IP information, user name, desktop name, architecture, and file names before awaiting command. When a command is sent, it returns the results.

Figure 21. Communications packet of the Rust version – test
3. Connections to recent attack cases
The above section covered the recently identified two cases where universities in Korea were attacked through abusing Innorix Agent and where malware strains were installed in Korean corporations through presumably spear phishing attacks. This part will explain why AhnLab considers the same threat actor to be behind both types of attacks.
First, there are cases in AhnLab’s ASD logs where Durian, Goat RAT, and AndarLoader were collected together in a similar period. The system in question is thought to be the threat actor’s test PC because the path name of AndarLoader was as follows.

AndarLoader collection path: d:\01__developing\99__c#_obfuscated\runtime broker.exe

There are also cases where the C&C servers of backdoor-type malware strains were the same. When the threat actor used Innorix Agent to install malware, Goat RAT was mainly employed, but there is a significant portion where other malware strains were installed. While such malware samples could not be collected, there are recorded communications logs with the C&C server. Also, the URL in question was the same as the DurianBeacon C&C server URL used in other attacks.

Figure 22. C&C communications log of the malware installed through Innorix Agent
Finally, there was a log where DurianBeacon installed AndarLoader. This means that these attacks happened around a similar time period, and the attacks might be related to each other as the installation processes and the C&C server URLs used tend to be similar.

Figure 23. Log showing DurianBeacon creating AndarLoader
4. Connections to past attack cases of the Andariel group
The recently identified two attack cases are likely done by the same threat actor. This section will cover the relationship between these attacks and the Andariel threat group.
A. Attack Targets

Attacked universities, the national defense industry, electronic device manufacturers, ICT companies, etc. in Korea.

B. Attack Methods

Abused Innorix Agent like in past cases
Probably employed spear phishing method like in past cases
Similarities between the path and file names used when installing the malware strains

C. Malware Types Used

Malware strains developed in Go were used
Similarities between Andardoor and AndarLoader
Malware types similar to the Infostealer used in previous attacks were identified

First, there are the facts that the industries and sectors that became attack targets were the same as the targets identified in past attack cases and the same attack methods used in previous attacks were employed in recent cases. AhnLab identified cases where Innorix Agent was used. While not confirmed, many logs showed circumstances of spear phishing attacks.
The file name “iexplorer.exe” used to install malware has been identified from Andariel’s past attack cases to the present. Besides “iexplorer.exe”, names including the “svc” keyword such as “authsvc.exe” and “creditsvc.exe” has been continuously used. Also, aside from “mainsvc.exe” and “certsvc.exe”, there are cases where similar names such as “netsvc.exe” and “srvcredit.exe” were used.
As covered in the corresponding section, AndarLoader was obfuscated with the trial version of Dotfuscator, the tool used in Andardoor in previous attacks. It also uses SSL encryption to communicate with the C&C server, again showing similarities with past attack cases. Two other malware strains developed in Go were used as well. These align with the trend of malware strains developed in Go such as 1th Troy Reverse Shell and Black RAT continuously being used since the early part of this year.
Finally, there is also the system thought to be the threat actor’s test PC and Infostealer strains presumably created by the threat actor during the attack process. In fact, the Andariel group in the past installed malware strains responsible for stealing account credentials during the attack process, exfiltrating account credentials saved in Internet Explorer, Chrome, and Firefox web browsers. Such malware strains are command line tools that output the extracted account credentials via command lines. It seems that the threat actor used a backdoor to send the results to the C&C server.

Figure 24. Infostealer identified in past attack cases
The Infostealer used in the recent attacks has a similar format. The only difference is that it only targets web browsers and steals account credentials and histories. Additionally, unlike the past cases where results were outputted by command lines, the recent version saves the stolen information in the same path under the file name “error.log”.

Figure 25. Infostealer identified in recent attack cases
5. Conclusion
The Andariel group is one of the highly active threat groups targeting Korea along with Kimsuky and Lazarus. The group launched attacks to gain information related to national security in the early days but now carries out attacks for financial gains. [11] The group is known to employ spear phishing attacks, watering hole attacks, and vulnerability exploits for their initial infiltration process. There have also been cases where it used other vulnerabilities in the attack process to distribute malware strains.
Users must be particularly cautious about attachments to emails with unknown sources or executable files downloaded from websites. Users should also apply the latest patch for OS and programs such as internet browsers and update V3 to the latest version to prevent malware infection in advance.
File Detection– Backdoor/Win.Agent.R562183 (2023.03.14.00)– Backdoor/Win.Andardoor.C5381120 (2023.02.16.01)– Backdoor/Win.Andardoor.R558252 (2023.02.16.01)– Backdoor/Win.AndarGodoor.C5405584 (2023.04.05.03)– Backdoor/Win.DurianBeacon.C5472659 (2023.08.18.02)– Backdoor/Win.DurianBeacon.C5472662 (2023.08.18.02)– Backdoor/Win.DurianBeacon.C5472665 (2023.08.18.03)– Backdoor/Win.Goat.C5472627 (2023.08.18.02)– Backdoor/Win.Goat.C5472628 (2023.08.18.02)– Backdoor/Win.Goat.C5472629 (2023.08.18.02)– Backdoor/Win.NukeSped.C5404471 (2023.04.03.02)– Backdoor/Win.NukeSped.C5409470 (2023.04.12.00)– Backdoor/Win.NukeSped.C5409543 (2023.04.12.00)– Infostealer/Win.Agent.C5472631 (2023.08.18.02)– Trojan/Win.Agent.C5393280 (2023.03.11.00)– Trojan/Win.Agent.C5451550 (2023.07.11.00)– Trojan/Win.Andarinodoor.C5382101 (2023.02.16.01)– Trojan/Win.Andarinodoor.C5382103 (2023.02.16.01)– Trojan/Win32.RL_Mimikatz.R366782 (2021.02.18.01)
Behavior Detection– Suspicious/MDP.Download.M1004– Infostealer/MDP.Behavior.M1965
IOCMD5– 0a09b7f2317b3d5f057180be6b6d0755: NukeSped variant – Volgmer (%SystemRoot%\mstc.exe.irx)– 1ffccc23fef2964e9b1747098c19d956: NukeSped Variant – Volgmer (%SystemRoot%\msnox.exe.irx)– 9112efb49cae021abebd3e9a564e6ca4: NukeSped variant – Volgmer (%SystemRoot%\system32\mscert.exe)– bcac28919fa33704a01d7a9e5e3ddf3f: NukeSped variant – Volgmer (%SystemRoot%\msnoxe.exe.irx)– ac0ada011f1544aa3a1cf27a26f2e288: Andardoor (%SystemDrive%\users\%ASD%\msdes.exe.irx)– c892c60817e6399f939987bd2bf5dee0: Andardoor (%SystemDrive%\users\%ASD%\msdes.exe.irx)– 0211a3160cc5871cbcd4e5514449162b: Andardoor (%SystemDrive%\users\%ASD%\msdes.exe.irx)– e5410abaaac69c88db84ab3d0e9485ac: 1st Troy Reverse Shell (%SystemRoot%\msnox.exe.irx)– 88a7c84ac7f7ed310b5ee791ec8bd6c5: 1st Troy Reverse Shell (%SystemRoot%\msnox.exe.irx)– eb35b75369805e7a6371577b1d2c4531: TigerRat (%SystemRoot%\system32\hl_cl.exe)– 5a3f3f75048b9cec177838fb8b40b945: TigerRat (%SystemDrive%\users\%ASD%\larabar.exe, %SystemDrive%\users\%ASD%\mainsvc.exe, %SystemDrive%\users\%ASD%\certsvc.exe)– 9d7bd0caed10cc002670faff7ca130f5: Black RAT (%SystemRoot%\syswow64\mbcbuilder.exe, %SystemRoot%\syswow64\msinfo.exe)– 8434cdd34425916be234b19f933ad7ea: Black RAT (%SystemRoot%\system32\shamon.exe)– bbaee4fe73ccff1097d635422fdc0483: NukeSped Variant (%SystemDrive%\users\%ASD%\update.exe)– 79e474e056b4798e0a3e7c60dd67fd28: NukeSped variant (%SystemRoot%\hl_cl.exe)– 3ec3c9e9a1ad0e6a6bd75d00d616936bc: Goat RAT (%SystemDrive%\users\%ASD%\downloads\iexplore.exe)– 95c276215dcc1bd7606c0cb2be06bf70: Goat RAT (%SystemDrive%\users\%ASD%\downloads\iexplore.exe)– 426bb55531e8e3055c942a1a035e46b9: Goat RAT (%SystemDrive%\users\%ASD%\downloads\iexplore.exe)– cfae52529468034dbbb40c9a985fa504: Goat RAT (%SystemDrive%\users\%ASD%\downloads\iexplore.exe)– deae4be61c90ad6d499f5bdac5dad242: Goat RAT (%SystemDrive%\users\%ASD%\downloads\iexplore.exe)– 6ab4eb4c23c9e419fbba85884ea141f4: AndarLoader ((SystemDrive%\users\%ASD%\pictures\runtime broker.exe, %SystemRoot%\system32\authsvc.exe, %SystemRoot%\system32\creditsvc.exe, %ProgramFiles%\smartplant\svchost.exe)– bda0686d02a8b7685adf937cbcd35f46: DurianBeacon Go (a.exe)– 6de6c27ca8f4e00f0b3e8ff5185a59d1: DurianBeacon Go (%SystemDrive%\users\%ASD%\pictures\xxx.exe)– c61a8c4f6f6870c7ca0013e084b893d2: DurianBeacon Rust (%SystemDrive%\users\%ASD%\documents\d.exe)– 5291aed100cc48415636c4875592f70c: Mimikatz (%SystemDrive%\users\%ASD%\mimi.exe)– f4795f7aec4389c8323f7f40b50ae46f: malware collecting account credentials (%SystemDrive%\users\%ASD%\documents\mshelp.exe)
Download URLs– hxxp://27.102.113[.]88/client.exe: NukeSped variant – Volgmer– hxxp://27.102.107[.]230/mstcs.exe: NukeSped variant – Volgmer– hxxp://27.102.107[.]233/update.exe: NukeSped variant – Volgmer– hxxp://27.102.107[.]233/client.exe: NukeSped variant – Volgmer– hxxp://27.102.107[.]234/update.exe: NukeSped variant – Volgmer– hxxp://27.102.107[.]235/mstcs.exe: NukeSped variant – Volgmer– hxxp://139.177.190[.]243/update.exe: Andardoor– hxxp://4.246.144[.]112/update.exe: Andardoor– hxxp://27.102.113[.]88/update.exe: 1st Troy Reverse Shell– hxxp://27.102.107[.]224/update.exe: 1st Troy Reverse Shell– hxxp://27.102.107[.]230/update.exe: 1st Troy Reverse Shell– hxxp://www.ipservice.kro[.]kr/dataSeq.exe: AndarLoader– hxxp://www.ipservice.kro[.]kr/creditsvc.exe: AndarLoader
C&C URLs– 27.102.113[.]88:5443: NukeSped variant – Volgmer– 27.102.107[.]234:8443: NukeSped variant – Volgmer– 27.102.107[.]224:5443: NukeSped variant – Volgmer– 109.248.150[.]179:443: NukeSped variant – Volgmer– 139.177.190[.]243:443: Andardoor– 4.246.144[.]112:443: Andardoor– 27.102.113[.]88:21: 1st Troy Reverse Shell– 27.102.107[.]224:8443: 1st Troy Reverse Shell– 4.246.149[.]227:8080: TigerRat– 13.76.133[.]68:8080: TigerRat– 217.195.153[.]233:443: TigerRat– bbs.topigsnorsvin.com[.]ec:8080: Black RAT– 27.102.129[.]196:8088: Black RAT– 13.76.133[.]68:10443: NukeSped variant– 46.183.223[.]21:8080: Goat RAT– chinesekungfu[.]org:443: AndarLoader– privatemake.bounceme[.]net:443: AndarLoader– 8.213.128[.]76:1012: DurianBeacon Go
Subscribe to AhnLab’s next-generation threat intelligence platform ‘AhnLab TIP’ to check related IOC and detailed analysis information.


Categories:Malware Information 
Tagged as:Andardoor,Andariel,BlackRAT,DurianBeacon,GoatRAT,Lazarus,TigerRat,Volgmer 




Analysis of MS-SQL Server Proxyjacking Cases 

Tracking Fileless Malware Distributed Through Spam Mails 







5
1
vote
Article Rating

 





 Subscribe




 Login 




Notify of 


new follow-up comments
new replies to my comments








 







 


Label












{}
[+]

 















Name*





Email





Website






[Important] Consent to Collection and Use of Personal Information


Purpose: Identify the contact information of user who left a comment and respond to inquiries on our blog posts.
Personal Information We Collect: Name of user and organization, Email address
Period of Retention: We store personal information for 3 months. Then, we delete and destroy personal information without delay.

 



I agree to AhnLab’s collection and use of personal information.




















Δ 










 


Label












{}
[+]

 















Name*





Email





Website






[Important] Consent to Collection and Use of Personal Information


Purpose: Identify the contact information of user who left a comment and respond to inquiries on our blog posts.
Personal Information We Collect: Name of user and organization, Email address
Period of Retention: We store personal information for 3 months. Then, we delete and destroy personal information without delay.

 



I agree to AhnLab’s collection and use of personal information.




















Δ 






29 Comments                    









 Inline Feedbacks                    
View all comments











Analysis of Andariel’s New Attack Activities – BU-CERT



    5 months ago













[…] Read more… […]






0






Reply













Andariel – Yet Another News Aggregator Channel



    5 months ago













[…] FORRÁS […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group’s Andariel Cluster – Cyber Social Hub



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group’s Andariel Cluster – F1TYM1



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Andariel, una branca del gruppo Lazarus contro Corea del Sud



    5 months ago













[…] nel 2023 è che sono presenti numerose varianti di malware sviluppate nel linguaggio Go“, ha dichiarato il Centro di Risposta d’Emergenza della Sicurezza AhnLab (ASEC) in un’analisi approfondita […]






0






Reply













Andariel, una branca del gruppo Lazarus attacca la Corea del Sud | PlayTalk.it



    5 months ago













[…] nel 2023 è che sono presenti numerose varianti di malware sviluppate nel linguaggio Go“, ha dichiarato il Centro di Risposta d’Emergenza della Sicurezza AhnLab (ASEC) in un’analisi approfondita […]






0






Reply













Researchers Warn of Cyber Weapons Utilized by Lazarus Group’s Andariel Cluster – informatify.net



    5 months ago













[…] strains developed within the Go language,” the AhnLab Safety Emergency Response Heart (ASEC) said in a deep dive launched final […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group’s Andariel Cluster



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group's Andariel Cluster – Cloud Karamchari



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group’s Andariel Cluster – Securitydone



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Investigadores advierten sobre armas cibernéticas utilizadas por el grupo Andariel del Grupo Lazarus - Teknomers Noticias



    5 months ago













[…] en el lenguaje Go», afirma el Centro de Respuesta a Emergencias de Seguridad de AhnLab (ASEC) dicho en un análisis profundo publicado la semana […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group’s Andariel Cluster - The Kilguard



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Des chercheurs mettent en garde contre les cyberarmes utilisées par le cluster Andariel du groupe Lazarus - Teknomers Nouvelles



    5 months ago













[…] dans le langage Go », indique l’AhnLab Security Emergency Response Center (ASEC). dit dans une étude approfondie publiée la semaine […]






0






Reply













Lazarus sub-cluster bolsters cyber arsenal – F1TYM1



    5 months ago













[…] intrusions against South Korean companies and organizations across different sectors, according to The Hacker News. Andariel, also known as Silent Chollima and Nice, has utilized supply chain attacks, […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group's Andariel Cluster – Solar Kat



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group's Andariel Cluster - xWebfingers Blog



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Researchers Warn of Cyber Guns Utilized by Lazarus Team's Andariel Cluster -



    5 months ago













[…] traces advanced within the Move language,” the AhnLab Safety Emergency Reaction Heart (ASEC) stated in a deep dive launched remaining […]






0






Reply













Researchers Warn of Cyber Weapons Utilized by Lazarus Group's Andariel Cluster - superpcparts.com



    5 months ago













[…] strains developed within the Go language,” the AhnLab Safety Emergency Response Middle (ASEC) mentioned in a deep dive launched final […]






0






Reply













【資安日報】9月6日，瀏覽器的開發工具元件成駭客竊取資料管道，研究人員揭露惡意軟體Chaes新一波攻擊行動 – AI 資訊



    5 months ago













[…] 北韓駭客Lazarus旗下組織Andariel鎖定韓國國防工業而來 […]






0






Reply













【資安日報】9月8日，網釣工具包W3LL挾持微軟帳號，並繞過雙因素驗證，暗中運作6年才曝光 – AI 資訊



    5 months ago













[…] 1. https://asec.ahnlab.com/en/48198/ 2. https://asec.ahnlab.com/en/56405/ […]






0






Reply













Araştırmacılar Lazarus Grubunun Andariel Kümesinin Kullandığı Siber Silahlar Konusunda Uyardı - Dünyadan Güncel Teknoloji Haberleri | Teknomers



    5 months ago













[…] Go dilinde geliştirilmiş çok sayıda kötü amaçlı yazılım türünün bulunmasıdır.” söz konusu geçen hafta yayınlanan derin bir […]






0






Reply













BlueShell Used in APT Attacks Against Korean and Thai Targets - ASEC BLOG



    5 months ago













[…] One of the main characteristics of BlueShell is that it was developed in Go. Because of the many advantages of the Go language including the fact that it is easy to develop with and offers cross-platform support, it is used often to not only develop applications but also create malware. SparkRAT included within a Korean VPN installer [1] and Sliver C2 used in the attack campaign exploiting the vulnerability in Sunlogin, a Chinese remote control utility [2] are both malware developed in Go and published on GitHub. Besides these, there have been a growing number of cases where APT threat groups used Go to create malware; the Kimsuky threat group developed a downloader that installs Meterpreter, [3] the RedEyes (APT37) threat group developed a backdoor by abusing the Ably service, [4] and the Andariel threat group developed a variety of malware including 1th Troy reverse shell, Black RAT, Goat RAT, and Durian Beacon. [5] […]






0






Reply













Researchers Warn of Cyber Weapons Used by Lazarus Group's Andariel Cluster - SwapUpdate



    5 months ago













[…] strains developed in the Go language,” the AhnLab Security Emergency Response Center (ASEC) said in a deep dive released last […]






0






Reply













Analysis Report on Lazarus Threat Group's Volgmer and Scout Malwares - ASEC BLOG



    4 months ago













[…] Because Volgmer is a DLL-type backdoor, it needs malware that installs it. A dropper was identified alongside the initial version of Volgmer, and this dropper installs Volgmer by creating a password-protected compressed version in the resource area before registering it as a service. The dropper also checks the number of arguments, recognizes Korean operating environments, and even checks the version of Windows operating environments, and if these do not match pre-configured conditions, it either displays a message box or deletes itself. A batch file is used for self-deletion, and its use of the file name “pdm.bat” is notable. The encrypted configuration data is decrypted during execution. This contains the registry key which will include the configuration data with the C&C server addresses, the string used to register the malware as a service, and the file “pdm.bate” to be used for self-deletion. The 0x10 byte-sized key used for decryption is still used by the malware from the Andariel group, a subsidiary group of Lazarus. [11] […]






0






Reply













Analysis Report on Lazarus Threat Group’s Volgmer and Scout Malwares - Ciberdefensa



    4 months ago













[…] Because Volgmer is a DLL-type backdoor, it needs malware that installs it. A dropper was identified alongside the initial version of Volgmer, and this dropper installs Volgmer by creating a password-protected compressed version in the resource area before registering it as a service. The dropper also checks the number of arguments, recognizes Korean operating environments, and even checks the version of Windows operating environments, and if these do not match pre-configured conditions, it either displays a message box or deletes itself. A batch file is used for self-deletion, and its use of the file name “pdm.bat” is notable. The encrypted configuration data is decrypted during execution. This contains the registry key which will include the configuration data with the C&C server addresses, the string used to register the malware as a service, and the file “pdm.bate” to be used for self-deletion. The 0x10 byte-sized key used for decryption is still used by the malware from the Andariel group, a subsidiary group of Lazarus. [11] […]






0






Reply













Circumstances of an Attack Exploiting an Asset Management Program (Andariel Group) - ASEC BLOG



    2 months ago













[…] The Andariel group usually launches spear phishing, watering hole, or supply chain attacks for initial penetration. There is also a case where the group exploited a central management solution during the malware installation process. Recently, the Andariel group has been exploiting vulnerabilities in many programs such as Log4Shell and Innorix Agent to attack targets in various corporate sectors in South Korea. [1] […]






0






Reply













Circumstances of the Andariel Group Exploiting an Apache ActiveMQ Vulnerability (CVE-2023-46604) - ASEC BLOG



    2 months ago













[…] group has been employing spear phishing, watering hole, and supply chain attacks from the past [2]. Recently, cases have been identified where the group exploits a Log4Shell vulnerability [3], […]






0






Reply













Circumstances of the Andariel Group Exploiting an Apache ActiveMQ Vulnerability (CVE-2023-46604) - F1TYM1



    2 months ago













[…] group has been employing spear phishing, watering hole, and supply chain attacks from the past [2]. Recently, cases have been identified where the group exploits a Log4Shell vulnerability [3], […]






0






Reply













Account Credentials Stealing Malware Detected by AhnLab MDS (Web Browsers, Email, FTP) - ASEC BLOG



    14 days ago













[…] where the group exploits centralized management solutions during the malware installation process [3]. This post will cover the Infostealer that was installed in the past by the Andariel group using […]






0






Reply




 













Archives Archives

Select Month
 February 2024 
 January 2024 
 December 2023 
 November 2023 
 October 2023 
 September 2023 
 August 2023 
 July 2023 
 June 2023 
 May 2023 
 April 2023 
 March 2023 
 February 2023 
 January 2023 
 December 2022 
 November 2022 
 October 2022 
 September 2022 
 August 2022 
 July 2022 
 June 2022 
 May 2022 
 April 2022 
 March 2022 
 February 2022 
 January 2022 
 December 2021 
 November 2021 
 October 2021 
 September 2021 
 August 2021 
 July 2021 
 June 2021 
 May 2021 
 April 2021 
 March 2021 
 February 2021 
 January 2021 
 December 2020 
 November 2020 
 September 2020 
 August 2020 
 July 2020 
 June 2020 
 May 2020 
 April 2020 
 March 2020 
 February 2020 
 December 2019 
 November 2019 
 October 2019 
 September 2019 
 August 2019 
 June 2019 
 May 2019 
 April 2019 
 March 2019 
 February 2019 
 January 2019 
 November 2018 
 July 2018 
 April 2018 
 February 2018 


FOLLOW US


LinkedIn   


X   


RSS Feed   









footer(en) 220, Pangyoyeok-ro, Bundang-gu, Seongnam-si, Gyeonggi-do, Korea | Privacy & Security© AhnLab, Inc. All rights reserved.family site


한국 (한국어)
Global (English)
日本 (日本語)
 




wpDiscuzInsert 



















































































































































































































