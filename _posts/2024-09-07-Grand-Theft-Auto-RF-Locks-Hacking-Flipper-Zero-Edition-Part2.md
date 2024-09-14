## Grand Theft Auto – RF Locks Hacking Flipper-Zero Edition Part 2

### 0x00. INTRODUCTION

<p align="justify">
In Part 1, we explored common methods for hacking fixed-code RF locks, including signal jamming, replay attacks, and brute-force attacks. In Part 2, we will delve into more advanced techniques for hacking the rolling codes of RF locks. This series of articles is designed as a beginner’s guide to RF lock system hacking, utilizing tools such as the Flipper Zero and other RF hacking devices. I hope that each case study will provide valuable insights into the requirements for successfully hacking RF locks.
</p>

![image](https://github.com/user-attachments/assets/e9ff4f39-513e-471e-8d5e-db830fe35659)

### 0x01. PROPRIETARY ENCRYPTION 

<p align="justify">
In Part 1, we focused on RF locks that use fixed codes. These types of locks are inherently vulnerable to replay attacks. One way to mitigate this vulnerability is by implementing a rolling codes mechanism. For example, both the key fob and the car synchronize using the same rolling codes algorithm. As the codes change with each use, predicting the next code in the sequence becomes difficult. The car will only accept commands with valid rolling codes, rendering simple replay attacks ineffective.
</p>

![image](https://github.com/user-attachments/assets/ed961471-d07d-4bba-8ce4-d09b6aa9a0ea)

<p align="justify">
When designing a secure cryptosystem, it is generally advised to avoid using untested proprietary encryption algorithms in products. However, there are always some "innovative" individuals who challenge this guideline. For example, consider this commercial RF remote control lock. It boasts advertising claims such as using a "US military grade rolling code" chip. But what could possibly go wrong?
</p>

![image](https://github.com/user-attachments/assets/1e37e3d6-259c-4f25-a286-e3a55cba9b38)

<p align="justify">
As always, we can use URH with HackRF to capture unlock signals and collect sample data for reverse engineering. To confirm the presence of a rolling codes mechanism, we can start by attempting a replay attack. However, the lock did not respond, confirming that a rolling codes mechanism is indeed in place.
</p>

![image](https://github.com/user-attachments/assets/92a1e242-cca7-48e7-8922-c3b4d7145e56)

<p align="justify">
We need to revisit and analyze the unlock samples. For easier comparison, we view the data in Hex mode and decode it using Manchester encoding. We soon discovered some intriguing flaws in the rolling code mechanism: every unlock command only changes randomly at bytes 15, 31, and 47, while all other bytes remain constant. Additionally, the rolling codes revert to an expired command value every 5 to 10 rounds. This suggests that the keyspace of this proprietary rolling code is extremely small, making it unnecessary to use brute-force methods to unlock it.
</p>

![image](https://github.com/user-attachments/assets/9cdff0a2-cc55-42ce-98cb-b47fa7d9a1c0)

As demonstrated in the video below, we only need to capture the unlocking command once. Afterward, we can replay it repeatedly until the lock is eventually unlocked.

<iframe width="560" height="315" src="https://www.youtube.com/embed/EruaGuE-cWI" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
Additionally, the Flipper Zero allows for manual addition of well-known key fob protocols. For instance, the LiftMaster_315 protocol uses a rolling code mechanism. However, as shown in the pictures below, the counter value and key are predictable. This predictability makes it relatively easy to capture and spoof an unlock command for this system.
</p>

![image](https://github.com/user-attachments/assets/183ef6fa-989a-4a88-9bee-d527f1162bed)

![image](https://github.com/user-attachments/assets/b658008d-f0b5-456e-854f-289732a86fd1)

### 0x02. ROLLJAM ATTACK

<p align="justify">
Fortunately, most car manufacturers avoid using such simplistic designs for car door locks. Instead, they tend to rely on proprietary encryption algorithms. For example, as shown in the picture below, a 2014 Jetta key fob uses Volkswagen’s proprietary algorithms, which remain undisclosed. As a result, the Flipper Zero is unable to recognize or decode it.
</p>

![image](https://github.com/user-attachments/assets/6be85cf0-30b2-45dd-8d41-0a5fedb8dbf1)

<p align="justify">
Once again, we used URH to analyze the Jetta key fob packets. A helpful feature of URH is the ability to highlight packet sequences in color, which greatly simplifies the analysis process. Each valid command sequence begins with 10101000, highlighted in green as the sync word.
</p>

![image](https://github.com/user-attachments/assets/9ddbe92e-87ca-4fa3-bbff-3c5f605b001b)

<p align="justify">
From the 305th to the 312th bit, highlighted in red, are the operation commands. For example, 00011100 is the command to unlock the door, while 00101010 is the command to lock it. The blue section represents the rolling codes, which change with each use.
</p>

![image](https://github.com/user-attachments/assets/10366914-a6bf-4d4c-a917-f3e74d75ab26)

URH includes a comparison feature, which allows for easy analysis and comparison of the rolling code segments between different commands.

![image](https://github.com/user-attachments/assets/19ba2a38-b315-4415-b96c-6338a59b6985)

As demonstrated in the video below, because modern cars now implement proprietary rolling code mechanisms, we can only replay the unlock command a single time.

<iframe width="560" height="315" src="https://www.youtube.com/embed/kNzH_on9HlM" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>
<br>

<p align="justify">
Years ago, security researcher Samy Kamkar developed an idea to manipulate certain rolling code systems, known as the Rolljam Attack. The mechanics of this attack are well-explained in the slides below. Rolljam works by jamming the target's radio signal at a slightly shifted frequency while simultaneously recording the signal using a receiver with a narrow filter bandwidth from the keyfob. Since the first unlock signal is blocked, the car door doesn’t unlock, and the car owner will likely try again. The attacker then records and blocks the second signal, but this time, they also replay the first code to unlock the car door.
</p>

![image](https://github.com/user-attachments/assets/115d2625-dfde-410d-81a8-bb9a036a9bbd)

As demonstrated in the Rolljam attack video below, HackRF is used as the signal jammer, while Yardstick 1 acts as both the recorder and transmitter.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Yesp2NRBiCM" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>
<br>

Since the Rolljam attack requires both a jammer and a recorder to work simultaneously, we can use HackRF and Flipper-Zero as a combination of tools for this attack.

![image](https://github.com/user-attachments/assets/21ffeec6-14fe-4ae9-bb89-841ea52f616c)

(Source from @takeapart)

### 0x03. ROLLING-PWN 

<p align="justify">
In 2021, I discovered a highly concerning car lock vulnerability that affected all Honda vehicles on the global market from 2012 to 2023. This flaw allows the replay of previously expired commands in sequence, enabling the car door to be unlocked permanently.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/BtYhD1X_L3A" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>
<br>

<p align="justify">
The vulnerability was assigned CVE-2021-46145, and I wrote a dedicated article on this issue, which can be found at https://rollingpwn.github.io/rolling-pwn. Special thanks to researcher Rob Stumpf, who helped verify the bug using his 2021 Honda Accord from the US.
</p>

![image](https://github.com/user-attachments/assets/5ca2d08c-81a8-4a96-b723-c989bcf366b8)

<p align="justify">
Moreover, Honda officially acknowledged the bug. However, Honda concluded that it poses a low risk to customers and that they regularly improve security features with new models to address this and similar vulnerabilities. Fingers crossed.
</p>

![image](https://github.com/user-attachments/assets/cda6a119-5d9a-439c-8eb3-9f410dff98d6)

<p align="justify">
In late August 2022, a group of researchers from Singapore presented a talk at Black Hat USA addressing the same issue and discovered that the bug also affected many other car brands. As mentioned in the Rolling-Pwn article, we were aware of this but kept information about other affected brands confidential at the time. We were busy preparing for Geekpwn, a Pwn2Own-style hacker competition, where one of our modern smart car targets was vulnerable to the bug. Fortunately, we secured second runner-up in the competition. As shown in the picture below, we used a high-gain antenna to compromise two different car brands from a considerable distance.
</p>

![image](https://github.com/user-attachments/assets/357a6943-0d0c-4451-ade9-93f07746e50d)

![image](https://github.com/user-attachments/assets/0c3cf651-0430-4613-a4e2-4a3ccddf7160)

### 0x04. KEELOQ DECRYPTION

<p align="justify">
In the world of cryptography, the well-known Kerckhoff's principle states that a cryptosystem should remain secure even if the attacker knows all the details about the system, except for the secret key. However, have you ever wondered what happens if that secret key is leaked by the manufacturer or if a default manufacturer key from the datasheet is used in the final products? Such incidents are not uncommon—remember the MIFARE Crypto1 default key hacks? Let’s take a widely used rolling code algorithm called Keeloq as an example.
</p>

![image](https://github.com/user-attachments/assets/fe53e119-fbf7-4a8c-abbe-9cad0edf9f59)

<p align="justify">
KeeLoq is a proprietary cipher owned by Microchip and is widely used in keyfob systems by car manufacturers such as Honda, Toyota, Volvo, Volkswagen Group, and others. If we find an HCS200 or HCS300 series chipset inside the keyfob, it indicates the use of the KeeLoq cipher. In March 2008, researchers from Ruhr University Bochum managed to break the KeeLoq-based cipher using side-channel analysis. By measuring the power consumption of a device during encryption, they were able to extract the manufacturer key from both the receiver and the remote control.
</p>

![image](https://github.com/user-attachments/assets/9b73f89a-d684-45b1-bd94-822674436a4d)

<p align="justify">
When connecting the HCS201 chip to an oscilloscope or logic analyzer, we can observe KeeLoq's signal waveform. Initially, a preamble appears at the start of the signal, followed by the rolling code, serial number, function code, and status code.
</p>

![image](https://github.com/user-attachments/assets/c0b350ad-0824-42ea-9387-13ef09ce3dd1)

![image](https://github.com/user-attachments/assets/a0c47e1f-1999-4541-8ffe-dc34e68b5a00)

The KeeLoq data structure consists of a total of 66 bits: 32 bits for the rolling code, 28 bits for the serial number, 4 bits for the button function code, and 2 bits for the status code. The encoding method used is Pulse Width Modulation (PWM).

![image](https://github.com/user-attachments/assets/e50a59fa-6ab0-4a71-910d-a357a1661b84)

<p align="justify">
In terms of encryption key generation, Keeloq has three modes: simple, standard and secure. The manufacturer's secret key for simple encryption is the same. For standard encryption, the manufacturer's secret key for each keyfob is unique. Taking standard encryption as an example, assumes that the serial number is 0x1234567. First add 2 as prefix for serial numbers, it become 0x21234567, then encrypted using the manufacturer's secret key to get a 32-bit LSB of 0x89074278. Second add 6 as prefix for serial numbers, it become 0x61234567, again encrypted using the manufacturer's secret key to get the 32 bit MSB 0x0516FBE9. The encryption key to this is 0x0516FBE989074278.
</p>

![image](https://github.com/user-attachments/assets/5b3dc722-5b36-4912-8b6d-c257d2cb8ab0)

We can use the simulator to demonstrate this in practice. In this example, we set the manufacturer's secret key to 0123456789ABCDEF, the serial number to 4141410, and the counter starts from 2600.

![image](https://github.com/user-attachments/assets/4eb6ef89-7b68-463d-ad62-ab9747fb2b2c)

After analyzing the packets with URH, we can see that the 28-bit serial number is indeed 4141410.

![image](https://github.com/user-attachments/assets/5311fe21-ac52-4bdd-a1ae-579392ac96c9)

The 32-bits rolling code contains key information such as counters to prevent a replay attack.

![image](https://github.com/user-attachments/assets/f4059ac6-9cef-476c-a5c2-53542a57372a)

<p align="justify">
As mentioned earlier, if the default manufacturer key is used, we can decrypt the 32-bit rolling codes with the program. You will see the rolling codes in sequence in the decrypted message, which matches the starting value of 2600 that we set earlier.
</p>

![image](https://github.com/user-attachments/assets/2736aebb-8ae4-4265-90bf-ac454d0c14ac)

As you can see the video below, we can spoof a new command to turn on the light.

<iframe width="560" height="315" src="https://www.youtube.com/embed/uyk0xOtnUew" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

### 0x05. SUMMARY

<p align="justify">
In Part 2, we explored some advanced techniques, such as RollJam, Rolling-PWN, and Keeloq decryption. However, there are many other types of encryption and rolling code algorithms to investigate. Stay tuned.
</p>

![image](https://github.com/user-attachments/assets/7b6393cb-1021-4fbc-8b60-0bda191ecaa8)



























