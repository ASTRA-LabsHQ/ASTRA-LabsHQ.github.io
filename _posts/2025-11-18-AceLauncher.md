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

![Mention of Being Authorized AV Dealer](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/NOTMalware.png)



## AceLauncher.exe ([Installer](https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/)) - First Stage

The first file we'll analyze is the installer. Admittedly I was not able to make it very far in the analysis of this sample. A majority of the information gathered on this campaign comes from the second sample we'll analyze later in the post. If you enjoy malware analysis feel free to download the [sample](https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/) and let me know what you find!

Using DIE (Detect It Easy) we can see that the installer is written in Delphi.
 ![DIE Output](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/DIE%20results.png)

Executing the installer spawns the following installation screen
![Ace Launcher Installer](/assets/pictures/Blogs/What'sCookin-TamperedChefAceLauncher/AceLauncherInstallerOne.png)





## AceLauncher.exe - Second Stage













## Indicators of Compromise

## Links

https://www.virustotal.com/gui/file/c8030eabf633a2e60e79c2b9eaa46a34bdac7a1298d520d28cca02f22e657956

Hash: c8030eabf633a2e60e79c2b9eaa46a34bdac7a1298d520d28cca02f22e657956

https://bazaar.abuse.ch/sample/ca53fabc32fc7b9d0441806ccf239b16644a75c5ad7104db640e2ec2338c29c8/

