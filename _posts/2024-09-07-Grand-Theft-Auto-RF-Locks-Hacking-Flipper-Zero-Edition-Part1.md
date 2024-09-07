## Grand Theft Auto – RF Locks Hacking Flipper-Zero Edition Part 1

### 0x00. INTRODUCTION
<p align="justify">
I believe when people are new to hacking RF locks system. One of the most common questions they will ask is which tools they will need. Usually I would say HackRF is a must to have tool. Up recently, the Flipper-Zero became one of the most popular gadgets among RF hackers. To be honest, the first time I heard about Flipper-Zero, I was not that impressed. I already owned a bunch of wireless hacking tools, such as Proxmark3, Yardstick1 and HackRF-Portapack. Why should we get another toy that provides similar functions? Soon I learned that I was wrong about it. For instance, other than its rich wireless capability, Flipper-Zero has also provided extendable external modules through the GPIO ports. This feature made the Flipper-Zero become a LEGO of hackers ;)
</p>

![image](https://github.com/user-attachments/assets/65e86b65-731b-4413-ae90-589388dc41bf)

(After 2-hours long wait in the queue, I finally got one at DEFCON31)

<p align="justify">
This article serves as a beginner’s RF lock system hacking journey, performing lock hacking with Flipper-Zero and other RF hacking tools. I hope each case study will help people get a better idea of what they would need for hacking RF locks.
</p>

### 0x01. SIGNAL JAMMING  

<p align="justify">
One of the easiest yet effective RF attack techniques called signal jamming. The attacker is constantly sending noise signals at that same frequency channel as the target, hoping to jam the original signal to achieve interference. Jamming essentially disrupts communication between two or more devices by shouting louder. It does not matter what to shout, as long as others cannot hear you.
</p>

![image](https://github.com/user-attachments/assets/55f9632e-b953-4a81-a4c5-71b20967c033)

(Jamming signals on the spectrum diagram)

<p align="justify">
Jamming attacks against car locks are also one of thieves' favorites. Criminals use hidden jamming equipment to prevent car owners from locking their cars. Once car owners leave, they can easily open the car door and steal the belongings left inside the car. Although signal jamming is not a new concept, it continues to grow in popularity among criminals, as the technology used becomes more sophisticated and easily accessible.
</p>

![image](https://github.com/user-attachments/assets/8c9662b4-b116-47ca-a4f5-a9ad65ccb36b)

<p align="justify">
Below is a video demonstration of the car jamming attack with HackRF. As you can see in the video below how effective the attack is. Please ensure the car door is securely locked before you leave.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/iuRdLtz4pmY" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>


<p align="justify">
Although Flipper Zero comes with certain regional and frequency restrictions, by installing a custom firmware (Xtreme) can bypassed this limit. This made Flipper-Zero perfect for experimenting with the jamming attack. 
</p>

![image](https://github.com/user-attachments/assets/0cdc0fd5-2175-44ac-836c-c10ca3b09f89)

(Source from @McSHUR1KEN)

### 0x02. SIGNAL REPLAY BLINDLY 

<p align="justify">
Of course, it would be cooler if we can control the target. The replay attack is one of the oldest tricks from the book that can achieve exactly that. Since most RF lock system operating frequency is at 315 MHz or 433 MHz range, and some of them still use fixed-code mechanism. Meaning we can just leave the capture device near the target and wait patiently. If lucky enough we will be able to catch the unlock signal for a later replay.
</p>

![image](https://github.com/user-attachments/assets/f7265af0-25e7-4cba-912c-2c16a826dd8c)

<p align="justify">
This is a lot like fishing, but the reward is unlock signal instead. Below is a video demonstration of the replay attack with HackRF. As you can see in the video below, it works as a charm.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/jUGePOmJzac" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
Interestingly for Tesla’s charging port, it still uses a fixed-code mechanism. People can download the pre-recorded Tesla Charge Port files to the Flipper-Zero, and messing with other Tesla vehicle owners, yet not knowing what is really going on behind the scenes. 
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SDuiAK10B3U" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

(Source from @takeapart)

### 0x03. SIGNAL REPLAY ANALYZE

<p align="justify">
Only blindly replaying the signal is not going to satisfy the Hackers. Unlike the jamming attacks, if we like to get to know our target better, we need to find out the target frequency, encoding method, chip model, etc. For example, we can learn the operating frequency by using the Frequency Analyzer application provided by Flipper-Zero.
</p>

![image](https://github.com/user-attachments/assets/d407f262-2f78-45b1-96e5-b40bf870aa28)

<p align="justify">
Alternatively, do an old school way by disassemble the keyfob and investigating the PCB. Here we see the IC chip model (CMT2150L) and a component crystal (26.250M) on an E-Scooter Keyfob. By checking the datasheet, we can found the operating frequency. The encoder is 1527. Most importantly the pinout diagram of the CMT2150L chip.
</p>

![image](https://github.com/user-attachments/assets/266445a4-341a-4953-b9f0-27c4dcca02ea)

![image](https://github.com/user-attachments/assets/401ea712-7950-4fac-8bc1-997bbe84830a)

<p align="justify">
Once we connect the correct pins on IC to an oscilloscope and press the unlock button on the keyfob, the data pulse of the unlock signal will be present in front of us. To make analyzing jobs easier, we can use a software called RTL_433 to capture the raw data generated by this E-Bike keyfob by wireless.
</p>

![image](https://github.com/user-attachments/assets/681e3d74-b93c-46bc-9862-85b7da318cbb)

![image](https://github.com/user-attachments/assets/0fa720f2-aefe-428b-9bb8-2bf2f6aa03fd)

<p align="justify">
Since the lock system of this E-Scooter again relies on the fixed-code, we can simply use Flipper-Zero to unlock it as you can see in the video below.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/jkHhvKER4ZE" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>


### 0x04. SIGNAL REPLAY BRUTEFORCE

<p align="justify">
Have you ever wondered if those fixed-code lock systems are bruteforceable? Here is an interesting lock; it comes with 8 DIP switches on both the lock and keyfob side, we can switch up, center and down to have different combinations. 
</p>

![image](https://github.com/user-attachments/assets/88116426-41bf-4c76-81d1-36727b221792)

![image](https://github.com/user-attachments/assets/8bb2b402-1bdf-439f-84f6-06b0bccd58e6)

<p align="justify">
By using the Flipper-Zero we are able to tell this lock is based on Princeton. We also can use app Pulse Plotter from Flipper-Zero to analyzing the signal. However, I would like to recommend a software designed for reversing wireless signal called Universe Radio Hacker to do the job. URH is a complete suite for wireless protocol investigation. URH allows easy demodulation of signals combined with an automatic detection of modulation parameters making it a breeze to identify the bits and bytes that fly over the air.
</p>

![image](https://github.com/user-attachments/assets/3681da85-c93e-426d-bb62-39051e5c5bbf)

![image](https://github.com/user-attachments/assets/a3f45f80-c321-433c-984a-67f5c6d876f5)

<p align="justify">
After capturing the unlock signal from the keyfob, the fixed code contents can be easily analyzed with URH. Since the DIP switch only 3^8 combination. It is very easy to brute force all 6561 combinations through the Fuzzing function of URH. As you can see in the video below.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/wgWMDBtQYrw" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
A person called Hong5489 has implemented the brute force sub files for Flipper-Zero. You can get sub files from his github. One thing need to be careful though is that when he tries to brute force his own gate, accidentally opens the neighbors gate.
</p>

![image](https://github.com/user-attachments/assets/2ae11535-9942-4ecd-a6d2-970e1adf8223)

### 0x05. SUMMARY

<p align="justify">
In this article, we have looked at the common methods of RF Locks hacking. In Part2, we are going to look at more advanced and interesting RF Locks hacking techniques. Stay tuned.
</p>
















