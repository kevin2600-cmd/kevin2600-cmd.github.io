## Grand Theft Auto – RF Locks Hacking Flipper-Zero Edition Part 2

### 0x00. INTRODUCTION

<p align="justify">
Previously from part1, we have looked at some common methods for hacking fixed-code RF Locks, such as signal jamming, replay, and brute-force attack. In part2, we are going to look at more advanced techniques to hack the rolling codes of RF Locks. This series of articles serves as a beginner’s RF lock system hacking journey, performing lock hacking with Flipper-Zero and other RF hacking tools. I hope each case study will help people get a better idea of what they would need for hacking RF locks.
</p>

![image](https://github.com/user-attachments/assets/e9ff4f39-513e-471e-8d5e-db830fe35659)

### 0x01. PROPRIETARY ENCRYPTION 

<p align="justify">
In part1 we mainly target RF locks that rely on fixed-code. Fixed-code locks are vulnerable to replay attack by design. One solution to prevent replay attacks is to implement a so-called rolling codes mechanism. For example, the keyfob and the car synchronized with the same rolling codes algorithm. As the codes change with each use, we will not be able to predict the next sequence of rolling code. The car will not accept the command until the rolling codes is valid, so a simple replay attack will not work.
</p>

![image](https://github.com/user-attachments/assets/ed961471-d07d-4bba-8ce4-d09b6aa9a0ea)

<p align="justify">
When it comes to designing a secure cryptosystem, people always say we should never use untested proprietary encryption algorithms in our products. However, there are always some “smart” people trying to challenge this statement. Let's take this commercial RF remote control lock as an example. What makes it very eye-catching is the advertising terms, such as the lock is use a “US military grade of rolling code” chip. What could possibly go wrong then?
</p>

![image](https://github.com/user-attachments/assets/1e37e3d6-259c-4f25-a286-e3a55cba9b38)

<p align="justify">
As always, we can fire up our URH with HackRF and capture some unlock signals as sample data to reverse engineer. Of course, just to be sure we can try the replay attack first. However, the lock did not respond, indicating that a rolling codes mechanism was indeed applied.
</p>

![image](https://github.com/user-attachments/assets/92a1e242-cca7-48e7-8922-c3b4d7145e56)

<p align="justify">
We need to go back and analyze those unlock samples. For the sake of easy comparison, we view the data as Hex mode and decoding the data as Manchester. We soon found some interesting rolling code flaws; every unlock command only randomly changes at bytes 15th, 31st, and 47th, all other bytes remain the same. Not only have that, the rolling codes return to an expired command value every 5 to 10 rounds. This indicates that the keyspace of such proprietary rolling codes is extremely small and we do not even need bruteforce to unlock it.
</p>

![image](https://github.com/user-attachments/assets/9cdff0a2-cc55-42ce-98cb-b47fa7d9a1c0)

As you can see in the demo video below, we only need to capture the unlocking command once, and then replay it repeatedly, until the lock is finally unlocked. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/EruaGuE-cWI" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>


<p align="justify">
In addition, Flipper-Zero can add some well-known keyfobs protocols manually. Take LiftMaster_315 as an example. It implemented a rolling code mechanism. However, as you can see in the pics below, the counter value is predictable, so does the key. Therefore, it is very easy to capture then spoof an unlock command to such system.
</p>

![image](https://github.com/user-attachments/assets/183ef6fa-989a-4a88-9bee-d527f1162bed)

![image](https://github.com/user-attachments/assets/b658008d-f0b5-456e-854f-289732a86fd1)

### 0x02. ROLLJAM ATTACK

<p align="justify">
Fortunately, most car manufacturers will not use such naive designs for the car door locks. They prefer to use their own proprietary encryption algorithms instead. As you can see in the picture below is a 2014 Jetta keyfob. Since Volkswagen keeps the algorithms secret, the flipper-zero is not able to recognize it. 
</p>

![image](https://github.com/user-attachments/assets/6be85cf0-30b2-45dd-8d41-0a5fedb8dbf1)

<p align="justify">
Again, we use URH for the analyzing Jetta keyfob packets. One nice feature of URH is that we can highlight the sequence of packets in color; this makes analyzing job much easier. Each valid sequence of commands will start with 10101000 in green as the sync-word. 
</p>

![image](https://github.com/user-attachments/assets/9ddbe92e-87ca-4fa3-bbff-3c5f605b001b)

<p align="justify">
From the 305th to the 312th in red are operation commands. For example, 00011100 is the command to open the door, and 00101010 is the command to close the door. The blue part is the rolling codes that change every time. 
</p>

![image](https://github.com/user-attachments/assets/10366914-a6bf-4d4c-a917-f3e74d75ab26)

URH comes with a comparison function that can be used to compare and analyze the rolling code parts between different commands.

![image](https://github.com/user-attachments/assets/19ba2a38-b315-4415-b96c-6338a59b6985)

As you can see in the demo video below, since modern cars nowadays have implemented the proprietary rolling code mechanism, we can only replay the unlock command once.

<iframe width="560" height="315" src="https://www.youtube.com/embed/kNzH_on9HlM" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
Years ago, a security researcher, Samy Kamkar, came up with an idea that can manipulate certain rolling code system. He called it the Rolljam Attack. How Rolljam attack works is well explained in the slides below. The Rolljam attack works by jamming the target radio signal at slightly deviated frequency and recording the signal at tight receiver filter bandwidth from the keyfob at the same time. Since the first unlock signal is blocked, the car door will not unlock and the car owner will likely try again. Attacker can again record and block the second signal, but this time the attackers also replays the first code to unlock the car door.
</p>

![image](https://github.com/user-attachments/assets/115d2625-dfde-410d-81a8-bb9a036a9bbd)

As you can see in the demo video of rolljam attack below, the HackRF has been used as a signal jammer, and Yardstick 1 as the recorder and transmitter.

<iframe width="560" height="315" src="https://www.youtube.com/embed/Yesp2NRBiCM" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

Since Rolljam attack needs a jammer and recorder work at the same time, we can use HackRF and Flipper-Zero as combo tools for this attack.

![image](https://github.com/user-attachments/assets/21ffeec6-14fe-4ae9-bb89-841ea52f616c)

(Source from @takeapart)

### 0x03. ROLLING-PWN 

<p align="justify">
In year 2021, I have found a very interesting yet scary car lock vulnerability, which affected all Honda vehicles currently existing on the market globally from year 2012 up to year 2023. All Honda vehicles allow a replay of the already expired commands in a consecutive sequence to unlock the car door permanently. 
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/BtYhD1X_L3A" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
The CVE-2021-46145 has assigned to this bug, and I have written an article dedicated to this bug (https://rollingpwn.github.io/rolling-pwn). Special thanks to researcher, Rob Stumpf helped us to verify the bug with his own 2021 Honda Accord from the US.
</p>

![image](https://github.com/user-attachments/assets/5ca2d08c-81a8-4a96-b723-c989bcf366b8)

<p align="justify">
Moreover, Honda officially acknowledge the bug. However, Honda concluded this is a low risk to customers, and Honda regularly improves security features as new models are introduced that would thwart this and similar approaches. Fingers crossed.
</p>

![image](https://github.com/user-attachments/assets/cda6a119-5d9a-439c-8eb3-9f410dff98d6)

<p align="justify">
In later Aug 2022, group of researchers from Singapore presented a talk at Blackhat USA addressing the same issue, and they found out the bug also affected many other brands of cars. As we mentioned in the Rolling-Pwn article, we knew about this. However, we were keeping what other brands were also affected by the bug quiet at that time. Because we are busy preparing for a Pwn2own style hacker competition called Geekpwn, and one of our modern smart car targets for the competition is vulnerable to the bug. Luckily, we won second runner-up in the end. As you can see in the pic below, we were using a high gain antenna to pwned those two different brands of cars in a fairly long distance.
</p>

![image](https://github.com/user-attachments/assets/357a6943-0d0c-4451-ade9-93f07746e50d)

![image](https://github.com/user-attachments/assets/0c3cf651-0430-4613-a4e2-4a3ccddf7160)

### 0x04. KEELOQ DECRYPTION

<p align="justify">
In the world of crypto, there is a well-known Kerckhoff principle; a crypto system should be secure even if the attacker knows all the details about the system, except the secret key. However, have you ever wondered what happen if that secret key leaked from manufacturer or a default manufacturer key from the datasheet used in the final products? These kinds of incidents are not uncommon; remember the mifare crypto1 default key hacks, anyone? Let’s take a widely used rolling code algorithm called Keeloq as an example.
</p>

![image](https://github.com/user-attachments/assets/fe53e119-fbf7-4a8c-abbe-9cad0edf9f59)

<p align="justify">
KeeLoq is a proprietary cipher owned by Microchip. It is widely used in keyfob systems by car companies such as Honda, Toyota, Volvo, Volkswagen Group and so on. If we found HCS200 or HCS300 series chipset inside the keyfob, which means we are facing the Keeloq cipher based system. In March 2008, researchers from the Ruhr University, Bochum, broke the KeeLoq-based cipher with side-channel analysis. By measuring the power consumption of a device during encryption, the researchers can extract the manufacturer key from the receivers and the remote control.
</p>

![image](https://github.com/user-attachments/assets/9b73f89a-d684-45b1-bd94-822674436a4d)

<p align="justify">
When we connect the HCS201 chip to an oscilloscope or logic analyzer, we can see Keeloq's signal waveform. First, there will be a preamble at the start of the signal, followed by the rolling codes, serial number, function code and status code.
</p>

![image](https://github.com/user-attachments/assets/c0b350ad-0824-42ea-9387-13ef09ce3dd1)

![image](https://github.com/user-attachments/assets/a0c47e1f-1999-4541-8ffe-dc34e68b5a00)

Keeloq data structure has a total of 66 bits, including 32 bits of rolling code, 28 bits of serial number, and 4 bits of button function code and 2 bits of status code. The encoding is PWM.

![image](https://github.com/user-attachments/assets/e50a59fa-6ab0-4a71-910d-a357a1661b84)

<p align="justify">
In terms of encryption key generation, Keeloq has three modes: simple, standard and secure. The manufacturer's secret key for simple encryption is the same. For standard encryption, the manufacturer's secret key for each keyfob is unique. Taking standard encryption as an example, assumes that the serial number is 0x1234567. First add 2 as prefix for serial numbers, it become 0x21234567, then encrypted using the manufacturer's secret key to get a 32-bit LSB of 0x89074278. Second add 6 as prefix for serial numbers, it become 0x61234567, again encrypted using the manufacturer's secret key to get the 32 bit MSB 0x0516FBE9. The encryption key to this is 0x0516FBE989074278.
</p>

![image](https://github.com/user-attachments/assets/5b3dc722-5b36-4912-8b6d-c257d2cb8ab0)

We can use the simulator to demonstrate it in practice. Here we set the manufacturer's secret key to 0123456789ABCDEF, the serial number to 4141410, and the counter starts from 2600.

![image](https://github.com/user-attachments/assets/4eb6ef89-7b68-463d-ad62-ab9747fb2b2c)

After analyzing the packets with URH, we can see that the 28-bit serial number is indeed 4141410.

![image](https://github.com/user-attachments/assets/5311fe21-ac52-4bdd-a1ae-579392ac96c9)

The 32-bits rolling code contains key information such as counters to prevent a replay attack.

![image](https://github.com/user-attachments/assets/f4059ac6-9cef-476c-a5c2-53542a57372a)

<p align="justify">
As mentioned earlier, if the default manufacturer key has been used. We can decrypt the 32-bits rolling codes with the program, you can see the rolling code in sequence in the decrypted message, which matches the starting value of 2600 we set earlier.
</p>

![image](https://github.com/user-attachments/assets/2736aebb-8ae4-4265-90bf-ac454d0c14ac)

As you can see the video below, we can spoof a new command to turn on the light.

<iframe width="560" height="315" src="https://www.youtube.com/embed/uyk0xOtnUew" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

### 0x05. SUMMARY

<p align="justify">
In Part 2, we have looked at some of the advanced techniques, such as RollJAM; Rolling-PWN and Keeloq Decryption. However, there are many other types of encryption and rolling code algorithms to play with. Stay tuned.
</p>

![image](https://github.com/user-attachments/assets/7b6393cb-1021-4fbc-8b60-0bda191ecaa8)



























