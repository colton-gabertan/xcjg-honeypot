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

## HoneyPot Findings:

These findings are accumulated after just two days of up-time. Out of curiosity, I pulled the pcap that the tcpdump has been writing to and found a pretty staggering amount of network traffic. Most of the traffic consisted of port-scans; however, it was relatively easy to see some areas where intrusion attempts had been made.

### Attempted FTP Exploits
![image](https://user-images.githubusercontent.com/66766340/151256295-151edc01-b39d-446e-8fb7-38d7dff7eaee.png)

This is just one of the [many examples] of FTP-based intrusions that I was able to see in my honeypot's network traffic. In this attempt, the attacker was trying to poke around in the filesystem, which was just empty. Neat to see, but it is not quite what I'm looking for. However, I will be sure to include more things I find interesting.

### SSH Brute Forcing
```
Jan 23 20:45:10 honeypot-1 sshguard[788]: Blocking 31.7.57.130 for 1680 secs (4 attacks in 38 secs, after 2 abuses over 14978 secs)
Jan 23 22:01:14 honeypot-1 sshguard[788]: Blocking 104.248.43.70 for 840 secs (4 attacks in 264 secs, after 1 abuses over 264 secs)
Jan 23 23:08:09 honeypot-1 sshguard[788]: Blocking 31.7.57.130 for 3360 secs (4 attacks in 38 secs, after 3 abuses over 23557 secs)
Jan 24 06:43:55 honeypot-1 sshguard[788]: Blocking 104.248.141.51 for 840 secs (4 attacks in 61 secs, after 1 abuses over 61 secs)
Jan 24 10:54:50 honeypot-1 sshguard[788]: Blocking 104.248.141.51 for 1680 secs (4 attacks in 63 secs, after 2 abuses over 15116 secs)
Jan 24 22:58:03 honeypot-1 sshguard[788]: Blocking 36.110.228.254 for 840 secs (4 attacks in 17 secs, after 1 abuses over 17 secs)
Jan 24 23:02:31 honeypot-1 sshguard[788]: Blocking 171.22.76.74 for 840 secs (4 attacks in 17 secs, after 1 abuses over 17 secs)
Jan 25 00:45:25 honeypot-1 sshguard[788]: Blocking 155.93.160.203 for 840 secs (4 attacks in 85 secs, after 1 abuses over 85 secs)
Jan 25 07:41:23 honeypot-1 sshguard[788]: Blocking 31.7.57.130 for 6720 secs (4 attacks in 38 secs, after 4 abuses over 140751 secs)
Jan 26 08:25:46 honeypot-1 sshguard[788]: Blocking 207.180.238.8 for 840 secs (4 attacks in 90 secs, after 1 abuses over 90 secs)
Jan 26 09:23:51 honeypot-1 sshguard[788]: Blocking 31.7.57.130 for 13440 secs (4 attacks in 39 secs, after 5 abuses over 233299 secs)
Jan 26 13:55:12 honeypot-1 sshguard[788]: Blocking 120.224.50.233 for 840 secs (4 attacks in 32 secs, after 1 abuses over 32 secs)
Jan 26 17:08:12 honeypot-1 sshguard[788]: Blocking 121.5.9.52 for 840 secs (4 attacks in 280 secs, after 1 abuses over 280 secs)
```

These alerts are from the auth.log indicating attempts of ssh brute-forcing. After only three days of being up, my honeypot was already getting tested by loud tactics. By what sshguard could catch, there were 12 definite attempts; however, looking at the full log, there are actually a handful of persistent ip's that test periodically. 



[honeypot]: https://blog.malwarebytes.com/101/2021/05/what-is-a-honeypot-how-they-are-used-in-cybersecurity/
[Modern Honey Network]: https://github.com/pwnlandia/mhn
[Google Cloud Platform]: https://cloud.google.com/free/
[Dionaea]: https://github.com/DinoTools/dionaea\
[FLAREVM]: https://github.com/mandiant/flare-vm
[VirusTotal]: https://www.virustotal.com/gui/home/url
[many examples]: https://github.com/colton-gabertan/xcjg-honeypot/blob/Index/honeypotFindings/ftp.pcap
