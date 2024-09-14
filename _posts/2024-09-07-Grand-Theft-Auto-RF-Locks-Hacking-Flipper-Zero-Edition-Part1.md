## Grand Theft Auto – RF Locks Hacking Flipper-Zero Edition Part 1

### 0x00. INTRODUCTION
<p align="justify">
I believe that when people are new to hacking RF lock systems, one of the most common questions they ask is which tools they need. Usually, I would say that HackRF is a must-have tool. Recently, the Flipper-Zero has become one of the most popular gadgets among RF hackers. To be honest, the first time I heard about Flipper-Zero, I wasn't very impressed. I already owned a bunch of wireless hacking tools, such as Proxmark3, Yardstick1, and HackRF-Portapack. Why should I get another tool that provides similar functions? Soon, I realized I was wrong. For instance, besides its rich wireless capabilities, the Flipper-Zero also offers extendable external modules through GPIO ports. This feature makes the Flipper-Zero a kind of LEGO for hackers.
</p>

![image](https://github.com/user-attachments/assets/65e86b65-731b-4413-ae90-589388dc41bf)

(After 2-hours long wait in the queue, I finally got one at DEFCON31)

<p align="justify">
This article serves as a beginner’s guide to RF lock system hacking, using Flipper-Zero and other RF hacking tools. I hope each case study will help readers gain a better understanding of the tools and techniques needed for hacking RF locks.
</p>

### 0x01. SIGNAL JAMMING  

<p align="justify">
One of the easiest yet most effective RF attack techniques is signal jamming. In this method, the attacker continuously sends noise signals on the same frequency channel as the target, aiming to interfere with the original signal. Jamming disrupts communication between devices by overpowering the intended signals. The key is not what is being transmitted, but simply that the noise prevents the legitimate signals from being heard.
</p>

![image](https://github.com/user-attachments/assets/55f9632e-b953-4a81-a4c5-71b20967c033)

(Jamming signals on the spectrum diagram)

<p align="justify">
Jamming attacks are also a popular method among thieves targeting car locks. Criminals use hidden jamming equipment to prevent car owners from locking their vehicles. Once the owners leave, the thieves can easily open the car door and steal any belongings left inside. While signal jamming is not a new concept, its popularity among criminals continues to grow as the technology becomes more sophisticated and accessible.
</p>

![image](https://github.com/user-attachments/assets/8c9662b4-b116-47ca-a4f5-a9ad65ccb36b)

<p align="justify">
Below is a video demonstration of a car jamming attack using HackRF. The video illustrates how effective the attack can be. Please ensure that your car door is securely locked before you leave.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/iuRdLtz4pmY" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>


<p align="justify">
Although Flipper Zero comes with regional and frequency restrictions, installing a custom firmware (Xtreme) can bypass these limitations. This makes Flipper Zero ideal for experimenting with jamming attacks.
</p>

![image](https://github.com/user-attachments/assets/0cdc0fd5-2175-44ac-836c-c10ca3b09f89)

(Source from @McSHUR1KEN)

### 0x02. SIGNAL REPLAY BLINDLY 

<p align="justify">
Of course, it would be even cooler if we could control the target. The replay attack is one of the oldest tricks that can achieve exactly that. Since most RF lock systems operate at 315 MHz or 433 MHz and some still use fixed-code mechanisms, we can simply leave a capture device near the target and wait patiently. If we're lucky, we might catch the unlock signal for later replay.
</p>

![image](https://github.com/user-attachments/assets/f7265af0-25e7-4cba-912c-2c16a826dd8c)

<p align="justify">
This is a lot like fishing, but the reward is an unlock signal instead. Below is a video demonstration of the replay attack with HackRF. As you can see in the video, it works like a charm.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/jUGePOmJzac" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
Interestingly, Tesla’s charging port still uses a fixed-code mechanism. People can download pre-recorded Tesla charge port files to the Flipper-Zero and mess with other Tesla vehicle owners without knowing what is really going on behind the scenes.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/SDuiAK10B3U" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

(Source from @takeapart)

### 0x03. SIGNAL REPLAY ANALYZE

<p align="justify">
Simply replaying the signal blindly won't satisfy hackers. Unlike jamming attacks, if we want to understand our target better, we need to identify details like the target frequency, encoding method, chip model, etc. For example, we can determine the operating frequency using the Frequency Analyzer application provided by the Flipper-Zero.
</p>

![image](https://github.com/user-attachments/assets/d407f262-2f78-45b1-96e5-b40bf870aa28)

<p align="justify">
Alternatively, you can use the old-school method of disassembling the keyfob and examining the PCB. For instance, in an E-Scooter keyfob, we see the IC chip model (CMT2150L) and a component crystal (26.250M). By checking the datasheet, we can find the operating frequency and determine that the encoder is 1527. Most importantly, the datasheet provides the pinout diagram for the CMT2150L chip.
</p>

![image](https://github.com/user-attachments/assets/266445a4-341a-4953-b9f0-27c4dcca02ea)

![image](https://github.com/user-attachments/assets/401ea712-7950-4fac-8bc1-997bbe84830a)

<p align="justify">
Once we connect the correct pins on the IC to an oscilloscope and press the unlock button on the keyfob, the data pulse of the unlock signal will appear. To simplify the analysis, we can use a software called RTL_433 to capture the raw data transmitted by the E-Bike keyfob wirelessly.
</p>

![image](https://github.com/user-attachments/assets/681e3d74-b93c-46bc-9862-85b7da318cbb)

![image](https://github.com/user-attachments/assets/0fa720f2-aefe-428b-9bb8-2bf2f6aa03fd)

<p align="justify">
Since the lock system of this E-Scooter relies on a fixed code, we can easily unlock it using the Flipper-Zero, as demonstrated in the video below.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/jkHhvKER4ZE" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>
<br>

### 0x04. SIGNAL REPLAY BRUTEFORCE

<p align="justify">
Have you ever wondered if fixed-code lock systems are brute-forceable? Here's an interesting example: a lock with 8 DIP switches on both the lock and keyfob. You can set each switch to one of three positions—up, center, or down—creating different combinations.
</p>

![image](https://github.com/user-attachments/assets/88116426-41bf-4c76-81d1-36727b221792)

![image](https://github.com/user-attachments/assets/8bb2b402-1bdf-439f-84f6-06b0bccd58e6)

<p align="justify">
By using the Flipper-Zero, we can determine that this lock is based on Princeton. We can also use the Pulse Plotter app from Flipper-Zero to analyze the signal. However, I recommend using a software specifically designed for reversing wireless signals called Universe Radio Hacker (URH). URH is a comprehensive suite for wireless protocol investigation. It allows for easy demodulation of signals and automatic detection of modulation parameters, making it straightforward to identify the bits and bytes transmitted over the air.
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
A person named Hong5489 has implemented brute-force sub-files for the Flipper-Zero, which you can find on his GitHub. However, be cautious: when he used the brute-force tool on his own gate, he accidentally opened his neighbor’s gate.
</p>

![image](https://github.com/user-attachments/assets/2ae11535-9942-4ecd-a6d2-970e1adf8223)

### 0x05. SUMMARY

<p align="justify">
In this article, we’ve explored common methods for hacking RF locks. In Part 2, we’ll delve into more advanced and intriguing RF lock hacking techniques. Stay tuned.
</p>
















