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

As always, we can fire up our URH with HackRF and capture some unlock signals as sample data to reverse engineer. Of course, just to be sure we can try the replay attack first. However, the lock did not respond, indicating that a rolling codes mechanism was indeed applied.

![image](https://github.com/user-attachments/assets/92a1e242-cca7-48e7-8922-c3b4d7145e56)

We need to go back and analyze those unlock samples. For the sake of easy comparison, we view the data as Hex mode and decoding the data as Manchester. We soon found some interesting rolling code flaws; every unlock command only randomly changes at bytes 15th, 31st, and 47th, all other bytes remain the same. Not only have that, the rolling codes return to an expired command value every 5 to 10 rounds. This indicates that the keyspace of such proprietary rolling codes is extremely small and we do not even need bruteforce to unlock it.

![image](https://github.com/user-attachments/assets/9cdff0a2-cc55-42ce-98cb-b47fa7d9a1c0)

As you can see in the demo video below, we only need to capture the unlocking command once, and then replay it repeatedly, until the lock is finally unlocked. 

<iframe width="560" height="315" src="https://www.youtube.com/embed/EruaGuE-cWI" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

In addition, Flipper-Zero can add some well-known keyfobs protocols manually. Take LiftMaster_315 as an example. It implemented a rolling code mechanism. However, as you can see in the pics below, the counter value is predictable, so does the key. Therefore, it is very easy to capture then spoof an unlock command to such system.

![image](https://github.com/user-attachments/assets/183ef6fa-989a-4a88-9bee-d527f1162bed)

![image](https://github.com/user-attachments/assets/b658008d-f0b5-456e-854f-289732a86fd1)

### 0x02. ROLLJAM ATTACK

Fortunately, most car manufacturers will not use such naive designs for the car door locks. They prefer to use their own proprietary encryption algorithms instead. As you can see in the picture below is a 2014 Jetta keyfob. Since Volkswagen keeps the algorithms secret, the flipper-zero is not able to recognize it. 


























