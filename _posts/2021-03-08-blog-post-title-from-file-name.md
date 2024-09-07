## Old Wine for New Bottle - Legacy Bug Exploitation

INTRODUCTION

For the past few quarters, I've been working on developing regulatory testing scripts framwork. Essentially, framework is a set of automated vehicle tests that check whether mitigation are implemented against common threats. This is aims to cover UNR155 Threats and Mitigation to provide a baseline test suite. We plans to expand coverage to include wireless, USB, cellular, and many other in-car
communication interfaces. That's where I come in, as a vulnerability researcher, I've always had a passion for wireless and embedded systems security. In the past, I've discovered various bugs, one of the highlights being Rolling-PWN, which affects all models of the Honda key fob system globally. Additionally, my colleague and I found a backdoor-like account in the firmware of an EV charging station from Schneider, which led to a full compromise of the EV charging network. We received acknowledgement from Schneider and later presented a talk at DEFCON.

![image](https://github.com/user-attachments/assets/1c8c7dc2-0936-409b-95ef-ee987d8f5723)

Although there are multiple attack vectors to consider when pen-testing vehicle-related targets, those bugs I've mentioned have one thing in common: they all relate to the upstream supply chain, whether the application involves a keyfob opening a car door, a charging station, or a T-box connecting a vehicle to a cloud server. They all require firmware and an OS, like QNX, Linux, or Android. If they have Bluetooth functionality, they likely use the BlueZ or Bluedroid stack. The same chipset may be used across all keyfobs to control the vehicles. Due to the nature of the vehicle ecosystem and the complexity of upstream suppliers, a protocol or chipset-level bug can have significant implications for every product relying on the same solution. In this article, I will share the research and script development process for some legacy Bluetooth vulnerabilities, discuss why they are necessary for Breakwater, and explore their impact on modern vehicles.

LEGACY BUG – BRAKTOOTH

In year 2021, a team of researchers at SUTD (Asset-Group) discovered and disclosed the family of vulnerabilities in the classic Bluetooth link manager layer. They've released a paper named "Braktooth: Causing Havoc on Bluetooth link manager". The paper is very detailed, highly recommend. The Braktooth is a codename for 16 classic Bluetooth vulnerabilities. It will cause affect BT devices continuously crash or deadlock. These bugs are present in various BT Chipsets across many manufacturers such as Intel, Mediatek, Qualcomm, TI, Infineon, etc. The whole testing setup is quite simple, we only need a ESP-WROVER-KIT flashed with Braktooth firmware and testing POC released on Github.

![Screenshot from 2024-08-07 16-40-06](https://github.com/user-attachments/assets/bd072e67-4e16-4427-bd08-1dcd1d802ac5)

