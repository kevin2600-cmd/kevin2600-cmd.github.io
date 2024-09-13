## Grand Theft Auto - A peek of BLE relay attack

### HOW IT BEGIN

<p align="justify">
About 7 years ago, security researcher Stawomir Jasek released a tool called Gattacker. It was designed to function as a Man-in-the-Middle and analysis tool for BLE devices. At that time, IoT hacking was very popular, but car hackers were still waiting for their moment. In May 2022, independent security researcher Martin Herfurt published a project called TEMPA, which revealed several vulnerabilities he discovered in Tesla vehicles. One of his methods for hacking the Tesla PhoneKey system involved a BLE relay attack, and the tool he used was Gattacker.
</p>

![1](https://github.com/user-attachments/assets/731cf701-38b3-4438-b547-2c5e30a4127e)

<p align="justify">
This immediately caught my attention. In Martin’s article, he mentioned that by using software like Gattacker, it is possible to relay information between the PhoneKey and the Tesla vehicle at the protocol level. Furthermore, the advantage of a protocol-based relay attack is that the distance between the victim and the vehicle is not constrained by physical proximity. This sounds very exciting because it means that not only could we potentially drive away the Tesla, but the victim could be anywhere in the world, as long as there is Internet access. However, this raises questions about how reliable this method is. More importantly, does it work on other modern vehicles that also implement the PhoneKey function?
</p>

<img width="715" alt="2" src="https://github.com/user-attachments/assets/96ec40f6-d97f-4bf7-8179-879f5c736d32">


<p align="justify">
Before we jump into tampering with cars, let's first understand some background on the BLE relay attack. The Tesla Model 3 and Model Y use a BLE-based passive entry system called PhoneKey. As the name suggests, Tesla owners can use an authorized mobile phone to unlock and control the vehicle within a certain proximity. Additionally, all cryptographic challenge-response operations are conducted over BLE. However, there’s a catch: although the Tesla PhoneKey uses challenge-response as an authentication mechanism, it does not enable BLE link-layer pairing or encryption. This makes it an ideal target for Gattacker.
</p>

### THE SUCCESS

<p align="justify">
For the first attempt, we had Gattacker running on two laptops, relaying the Tesla Model 3's traffic through a local Wi-Fi connection. It turns out Martin was right—despite being 7 years old, Gattacker still works like a charm.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/1Vsf6irr4Sc" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

Next, we tested it on a Tesla Model Y. As expected, it successfully unlocked the car doors.

![3](https://github.com/user-attachments/assets/21b7c8cb-4b6c-473c-9aeb-de1e28c1fabd)

<p align="justify">
What about the long-distance relay attack? To achieve this, we set up a publicly accessible VPN server and ran Gattacker on Raspberry Pis instead of PCs.
</p>

![4](https://github.com/user-attachments/assets/e1d5e77a-9251-4ec6-bb42-759f8f05bbbd)

<p align="justify">
The result was quite amazing. Not only did we relay the traffic over a long distance, but we also crossed city boundaries. As demonstrated in the test, we successfully unlocked a Tesla located in a completely different area.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ijio9_WJpH4" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
So, one question remains: does this method work on other modern vehicles that also implement the PhoneKey function? In recent years, the smart car industry has been booming in China, and nearly everyone wants a piece of it. I have to admit, some of these are quite good cars. However, when it comes to security, there's still room for improvement. Below is an example of one of the most popular smart cars in China XiaoPeng P7. We easily connected to it using a BLE application, with no pairing required. This is either a good—or perhaps a bad—sign, depending on how you look at it.
</p>

![5](https://github.com/user-attachments/assets/1c5b114a-1091-4dde-96be-1631594c1336)


<p align="justify">
Once again, the Gattacker work very well. And notice that in the demo video, the attacker purposely keeps a distance from the victim, to emulate the real attack.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/syj-rghnQRk" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>


<p align="justify">
Here's a trick: to check if a vehicle is vulnerable to a BLE relay attack, you can use any BLE app to attempt a connection. If no pairing is required, it may indicate a potential security issue.
</p>

![6](https://github.com/user-attachments/assets/2cc7f617-b18f-41d3-a5c8-9280604a3b83)
![Screenshot from 2024-09-13 15-23-40](https://github.com/user-attachments/assets/e6940323-1969-4464-9d3b-cc05fa1ecc85)
![8](https://github.com/user-attachments/assets/42da7ae7-b436-443f-b2f4-fa2e60e31e8a)
![9](https://github.com/user-attachments/assets/d107dc4a-69e3-42f7-83a0-eda74d3175e7)

### THE FAILURE

<p align="justify">
Like any scientific experiment, success was not always guaranteed. During the tests, we were unable to perform the BLE relay attack on some targets using Gattacker. One clear indicator is that these vendors have enabled PIN code requirements during the initial pairing stage. This is beyond the capabilities of Gattacker, which only works on targets that do not implement BLE link-layer pairing or encryption.
</p>

<img width="524" alt="10" src="https://github.com/user-attachments/assets/4d4657d9-5e67-4231-998d-fbe6275325a1">

<p align="justify">
On May 15, 2022, security researcher Sultan Qasim Khan from the NCC Group published a series of articles detailing his research. He developed a tool for BLE relay attacks capable of relaying encrypted link-layer communications. This tool was successfully tested on the Tesla Model 3 and other IoT products. However, he did not release this tool to the public. Instead, he released another tool called Sniffle, which is a sniffer for BLE 5 and 4, using the TI CC1352/CC26x2 board. Interestingly, he used the same board to conduct the link-layer BLE relay attack.
</p>

<img width="945" alt="11" src="https://github.com/user-attachments/assets/feb6bc10-1a2a-417d-8bd4-371d7b3c7f2e">

<p align="justify">
Around October 2022, a group of researchers from Team XWZ claimed to have reproduced Sultan's link-layer relay attack and successfully demonstrated it on the Tesla Model 3. However, as of the time of writing, they also encountered difficulties attacking certain car models. One possibility is that these vendors may have implemented additional security mechanisms to prevent such BLE relay attacks.
</p>

### ONE MORE THING

<p align="justify">
In August 2019, security researcher Martin Herfurt published a project called Tesla Radar. He discovered that Tesla vehicles continuously broadcast a signal that anyone can detect. Furthermore, owners cannot disable these unique hashed iBeacon IDs sent from the vehicles. This could be a privacy concern, as it allows anyone to track Tesla vehicles using this signal.
</p>

<img width="522" alt="12" src="https://github.com/user-attachments/assets/0ee25b94-6448-40a5-96e6-77e80233aa94">

<p align="justify">
During the research, we discovered that some vendors have a similar issue. Instead of broadcasting a hashed ID, they broadcast the vehicle’s VIN number in plaintext. While it is true that VIN numbers are public information, broadcasting them in plaintext makes it easier to locate target vehicles quickly in parking lots.
</p>

![13](https://github.com/user-attachments/assets/8b7dddf5-2529-47bf-924e-78e1d73bbc63)


### FINAL THOUGHT

<p align="justify">
Our findings have been reported to the respective vendors. However, we have experienced that, due to dependencies on multiple downstream vendors, it can be very challenging for some vendors to produce a patch. This highlights the complexity of addressing vehicle vulnerabilities. Fingers crossed for a resolution.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/de2-YcfEqmI" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
In August 2020, I presented my research on NFC relay attacks on the Tesla Model 3 at DEFCON. Similar to the response regarding BLE relay attacks, Tesla acknowledged that relay attacks are a known limitation of their passive entry system and recommended using the PIN to Drive feature. However, years ago, we discovered a bug that could bypass the PIN2Drive, which earned us a spot in the Tesla Hall of Fame. Later, researcher Martin Herfurt identified another bug that also bypassed PIN2Drive. This underscores the fact that the security of such products is indeed a long-term endeavor, and vendors must remain vigilant.
</p>


### REFERENCES

https://bugcrowd.com/Kevin_2600

https://rollingpwn.github.io/rolling-pwn/

https://trifinite.org/stuff/tempa_relay_attack/

https://research.nccgroup.com/2022/05/15/technical-advisory-tesla-ble-phone-as-a-key-passive-entry-vulnerable-to-relay-attacks/





































