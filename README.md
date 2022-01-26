# Security Research: Malware HoneyPot

## Overview:

This project is what's known as a [honeypot]. Essentially, it is a fake server used to bait for cyber attacks. We intentionally leave it vulnerable in order to gain an understanding of how one conducts their attacks. In my particular honeypot, I have it set up specifically to try to detect and collect malware samples for further research and reverse engineering practice.
> Services\ Frameworks used:
> * [Google Cloud Platform] 
> * [Modern Honey Network]
> * [Dionaea]
> * [FLAREVM]

## Setup:

Due to my intentions with the honeypot, I did not see the need to beef it up and make it look particularly enticing for an attacker to try to infiltrate and escalate privileges. Mine was to serve as more of a monitor of HTTP traffic to see what is simply floating around on the internet and where it comes from. 

In essence, I hosted two virtual servers on the Google Cloud Platform. One for monitoring ingress traffic and another to serve as the honeypot itself. There were virtually no firewall rules on the honeypot, so most traffic would be free range. Both were simple ubuntu servers with MHN and Dionaea configured. When setup correctly, the MHN framework allows us to simply `http` into the admin server to view the user interface in which we can see some analytics about the incoming attacks.
> There are even some neat features integrated such as HoneyMap, which maps out the incoming attacks in real time.

### Admin UI
![image](https://user-images.githubusercontent.com/66766340/150939244-6b87e92f-8efe-4327-a853-8e4b5c22f04e.png)

### HoneyMap
<img src="https://github.com/colton-gabertan/xcjg-honeypot/blob/Index/honeymap1.gif">

From here we have a clean UI to view some interesting stats; however, my end goal with the honeypot was to collect malware samples. To do so, in the honeypot server, I used `screen` to run a continuous `tcpdump` that would write to a `.pcap` file. After letting it run and collect some traffic, I `scp`ed the pcap and viewed the data with `wireshark` in my FLAREVM. We also have the option to export the traffic logs into a `.json` from the honeypot.

### Continuous `tcpdump` as a Detached Process
<img src="https://github.com/colton-gabertan/xcjg-honeypot/blob/Index/tcpdump.gif">

Thanks to `wireshark`, we can actually download the intercepted data. This feature is what allows us to pull any binaries or malicious traffic containing code into our malware analysis environment. We can hash files to cross-check samples with databases such as [VirusTotal], check to see if any antivirus scans (like malwarebytes, bitdefender, etc.) will pick up on our samples, and even get into reverse engineering live malware.

## Malware HoneyPot Findings:

### Attempted FTP Exploits



[honeypot]: https://blog.malwarebytes.com/101/2021/05/what-is-a-honeypot-how-they-are-used-in-cybersecurity/
[Modern Honey Network]: https://github.com/pwnlandia/mhn
[Google Cloud Platform]: https://cloud.google.com/free/
[Dionaea]: https://github.com/DinoTools/dionaea\
[FLAREVM]: https://github.com/mandiant/flare-vm
[VirusTotal]: https://www.virustotal.com/gui/home/url
