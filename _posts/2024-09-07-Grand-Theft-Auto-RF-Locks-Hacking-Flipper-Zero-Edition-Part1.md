
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

Below is a video demonstration of the car jamming attack with HackRF. As you can see in the video below how effective the attack is. Please ensure the car door is securely locked before you leave.

<iframe width="560" height="315" src="https://www.youtube.com/embed/iuRdLtz4pmY" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

