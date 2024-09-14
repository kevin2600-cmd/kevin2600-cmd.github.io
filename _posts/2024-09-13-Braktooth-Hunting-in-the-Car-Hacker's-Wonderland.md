## Braktooth Hunting in the Car Hacker's Wonderland

## INTRODUCTION

<p align="justify">
Years ago, a team of researchers at SUTD (Asset-Group) discovered and disclosed a family of vulnerabilities in the classic Bluetooth link manager layer. They released a paper and proof-of-concept (POC) named "Braktooth: Causing Havoc on Bluetooth Link Manager". The paper is very detailed and enjoyable to read, and I highly recommend it to anyone interested in Bluetooth security. Braktooth is the codename for a set of 16 classic Bluetooth vulnerabilities that can cause devices to continuously crash or deadlock. In at least one case, attackers can remotely execute arbitrary code and erase all data on the targeted devices. These vulnerabilities are present in various Bluetooth chipsets across many manufacturers, including Intel, Qualcomm, TI, Infineon, and others.
</p>

![Braktooth101](https://github.com/user-attachments/assets/3e764bee-c4cd-468d-b24d-4bf22a10ca6b)

<p align="justify">
Since my primary focus is on vehicle security, one aspect of the Braktooth paper immediately caught my attention. The paper notes that Braktooth not only affects laptops and smartphones but also impacts infotainment units in automobiles and even audio systems in airplanes. 
</p>

![Volvo Technology](https://github.com/user-attachments/assets/747c29aa-3b74-44dc-8d34-abc27267ad3e)


<p align="justify">
For in-vehicle infotainment (IVI) systems, the paper specifically mentions the Volvo FH as an example. This led me to wonder whether other popular cars are also affected by Braktooth.
</p>

## FIRST-BLOOD

<p align="justify">
Before diving into testing with cars, we need to familiarize ourselves with Braktooth. Setting up the actual environment is quite straightforward. According to the paper, certain chipsets from MediaTek are affected. My Lenovo L14 laptop uses a Ralink chipset for Bluetooth communication. Since Ralink is part of the MediaTek group, it is an ideal candidate for testing.
</p>

![Volvo Technology](https://github.com/user-attachments/assets/3f014378-90e4-4451-9c7b-4ba5b73813ef)


<p align="justify">
Since there are 16 proof-of-concepts (POCs) for the Braktooth vulnerabilities, we need to test each one. We found that one particular vulnerability, called Invalid-Timing-Accuracy, almost always works. Vulnerable chipsets fail to properly handle the reception of a malformed LMP timing accuracy response, especially when followed by multiple reconnections to the target link slave.
</p>


![LMP-Timing-Attack-Ralink](https://github.com/user-attachments/assets/dea6063f-9060-427c-8dbc-f976655c283c)


<p align="justify">
This vulnerability allows attackers to exhaust the Bluetooth resources of the target device. The attacker can trigger a crash or disrupt other Bluetooth devices connected to the target chipset. The best part is that the attacker only needs to know the BDAddress of the target device—no authentication is required to launch the attack. As demonstrated in the video below, the Bluetooth connection between the Thinkpad laptop and the speaker stops functioning and eventually disconnects.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/_dbYnJjrbwo" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

## INVESTIGATION

<p align="justify">
The easiest place to start is to have the car components on the test bench. For example, we have a second-hand Nissan IVI in the lab. After running the Invalid-Timing-Accuracy POC, we observed that the Nissan IVI froze and could no longer detect any nearby Bluetooth devices.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/11zfyoa90m4" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
Another place to look for potential targets is the parking lot. If approached politely, most people are often interested in seeing if their cars can be hacked. At that time, we had access to two Tesla cars and one Changan Uni-T for testing. For the Tesla Model 3 and Model X, only the Invalid-Setup-Complete POC was successful; all other POCs failed.
</p>








