---
title: "What’s Cookin’ in TamperedChef’s Kitchen? A Peek Into the Campaign’s Latest Activity"
date: 2025-11-18 3:54:00 -0500
categories: [Malware Analysis, TamperedChef, AceLauncher]
tags: [malware analysis, guide, static analysis, TamperedChef, AceLauncher]
description: "A Technical Analysis of Tampered Chef and AceLauncher.exe"
---
![ASTRA Labs Logo](/assets/pictures/WideNoAcronym.png)
![Ace Launcher Overview](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AceLauncherGraphic.png)

## Tampered Chef

A malware campaign known as Tampered Chef/EvilAI started to be tracked in June of this year but information such as the timestamp on the compiled executables indicates that preparation for this campaign could have started as early as March. The AceLauncher malware can be attributed to TamperedChef/EvilAI with high confidence because of similar TTPs with previous TamperedChef activity. The malware campaign typically uses themes such as enhanced browser productivity tools (the sample we'll be viewing today), AI tools, PDF Readers, and Recipe applications (We'll see artifacts of a recipe app in today's sample as well). The use of AI to quickly put together legitimate looking websites, the tools downloaded providing their claimed utility,  and code signing their applications make these malware campaigns particularly effective. The observed end goal of these campaigns is to exfiltrate browser data but other activity such as deploying a JavaScript backdoor have also been reported. Other motivations behind the campaign could include gaining initial access to sell for ransomware staging and opportunistic espionage.

### Alleged TamperChef/EvilAI Campaigns
- All Manuals Reader
- Master Chess
- Manual Reader Pro
- Manual Finder
- JustAskJacky
- Total User Manual
- Any Product Manual
- App Suite
- One Start
- PDF Editor
- Recipe Lister

## AceLauncher

Using [Any.Run](https://any.run/) we can navigate to AceLauncher[.]com and view the landing page along with some other pages on the website.

![AceLauncher Landing Page](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/LandingPage.png)

In short, AceLauncher is a claiming to be a browser productivity tool. They will download a chromium based browser, give you a toolbar that isn't very helpful, and all they ask for in exchange is your browser credentials! What a great deal!

I've also included their "Features" page. While I don't agree with their actions I think we can all agree that the website is put together very well and can absolutely be convincing to someone that is less experienced with computers. 

![AceLauncher Features Page](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AceLauncherFeatures.png)

But hey, they mention that they're an authorized distributor of Norton and McAfee antivirus software and under their FAQ section they state they're NOT malware. So it must be safe!

![FAQ - We're not Malware!](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/NOTMalware.png)

I was highly suspicious that this website was quickly put together with the use of AI whether that be vibe coding the website and using an LLM to generate all of the text on the website. I copied two paragraphs of text from their website and input them into zerogpt. zerogpt returned a 94.7% chance it was written using an LLM. That confirms that suspicion and makes it undeniable that threat actors are levering LLMs to stand up infrastructure faster and create more convincing landing pages. 

![zerogpt result](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AIGPT.png)

Now, lets get into the analysis!


## AceLauncher.exe ([Installer](https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/)) - First Stage

The first file we'll analyze is the installer. Admittedly, I was not able to make it very far in the analysis of this sample. A majority of the information gathered on this campaign comes from the second sample we'll analyze later in the post. If you enjoy malware analysis feel free to download the [sample](https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/) and let me know what you find!

After downloading the installer from the main landing page of the website, we're greeted with an error message. This error message spawns because the certificate the malware was signed with was revoked. If you're interested in learning more about how software certificates can be abused, check out [Cert Central](https://certcentral.org/training).

![Landing Page Download](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/DownloadFromLandingPage.png)

I was not able to find a workaround to download the file because the certificate was revoked, but I was able to pull a sample from Malware Bazaar. That is the sample we'll be analyzing moving forward. It's worth noting that if the signature was still valid a victim would be able to download and execute the binary from the website. 

Viewing the signature details we can see that the executable was signed by Sunstream Labs (Capital Intellect Inc.).

![Digital Signature that was revoked](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/Signature.png)



Using DIE (Detect It Easy) we can see that the installer is written in Delphi.
 ![DIE Output](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/DIE%20results.png)

#### Executing the installer
![Ace Launcher Installer](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AceLauncherInstallerOne.png)

Although there were errors while working through the installation of the malware as shown below, the malware still successfully installed and those errors could be used to extract IOCs from the sample. 

The first error indicated a CreateProcess failure in the Temp directory. This mini_installer.exe file will be included as an IOC.

![Mini_Installer.exe IOC](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/Error1.png)

The second error is a bit more telling and indicates the existence of an autoupdater for the executable. This will also be logged as an IOC.

![AutoUpdate.exe IOC](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/Error2.png)

After installation two .lnk files were dropped onto the desktop. 

![Two .lnk files dropped on Desktop](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/TwoLNKFiles.png)

#### AceLauncher.lnk properties:
![AceLauncher.lnk Properties](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AceLauncherLNKProperties.png)

Target: 
`C:\Users\{username}\AppData\Local\AceLauncherDock\current\AceLauncher.exe AceLauncher`

#### Web.lnk properties:
![AceLauncher.lnk Properties](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/WebLNKProperties.png)

Target: 
`C:\Users\{username}\AppData\Local\AceLauncher\Application\AceLauncher.exe`

### Following the crumbs..
If we follow the path referenced in the targets of the two .lnk files dropped onto the desktop we come across something very interesting. Within the \AppData\Local\AceLauncher directory we can see a "User Data" directory. The User Data directory contains a Default folder, a "fresh test" file, and a "Local State" file. Navigating to the Default folder we can begin to figure out the objective of this malware campaign. 

![Folder Contents](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/FolderContents.png)

All of the files listed are in a SQLite format. The "History" file contains all of the victims browswer history, downloaded files, and much more. The names of the other files explain their purpose.

That was as far as I could get with my current skillset, so now onto the second file!








## AceLauncher.exe - Second Stage













## Indicators of Compromise

## Links

https://www.virustotal.com/gui/file/c8030eabf633a2e60e79c2b9eaa46a34bdac7a1298d520d28cca02f22e657956

Hash: c8030eabf633a2e60e79c2b9eaa46a34bdac7a1298d520d28cca02f22e657956

https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/

