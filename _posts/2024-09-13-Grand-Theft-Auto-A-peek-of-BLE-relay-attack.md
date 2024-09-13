## Grand Theft Auto A peek of BLE relay attack

### HOW IT BEGIN

<p align="justify">
About 7 years ago, security researcher Stawomir Jasek released a tool called Gattacker. It aims to use as a Man-in-the-Middle and analysis tool for BLE devices. However, IOT Hacking was very popular at the time, but car hackers are still waiting for their moment. In 2022 MAY, an independent security researcher Martin Herfurt published a project called TEMPA, which presented several vulnerabilities he found on Tesla. One of his tricks to hack the Tesla PhoneKey was BLE Relay, and the tool he used was Gattacker.
</p>

![1](https://github.com/user-attachments/assets/731cf701-38b3-4438-b547-2c5e30a4127e)

<p align="justify">
This immediately attracted my attention; in Martin’s article, he mentioned by using software like gattacker, it is possible to relay the information between the PhoneKey and the Tesla vehicle on a protocol level. Furthermore, the advantage of the protocol-based relay attack is the distance between the victim and the vehicle is not limited by physical constraints. This is sounds very exciting because that means we are not only able to drive away the Tesla, but the victim can be in any part of the world, as long as there is Internet access. However, it also brought up the questions like how reliable this method is. More importantly, does it work on other modern vehicles that also implement the Phonekey function?
</p>

<img width="715" alt="2" src="https://github.com/user-attachments/assets/96ec40f6-d97f-4bf7-8179-879f5c736d32">


<p align="justify">
Before we jump on to messing with all the cars, let us first understand some background knowledge of the BLE relay attack. The Tesla Model 3 and Y implement a BLE-based passive entry system called PhoneKey. As the name suggests, Tesla owners can use an authorized mobile phone to unlock and control the vehicle within a proximity range. In addition, all the cryptographic challenge-response operations were conducted over BLE. But here is a catch, even the Tesla PhoneKey has implemented the challenge-response as authenticate mechanism. They did not enable the BLE link-layer pairing/encryption, which make it the perfect target of Gattacker.
</p>

### THE SUCCESS

<p align="justify">
For the first attempt, we have Gattacker running on two laptops; relay the Tesla Model 3 traffic through a local WIFI connection. Turns out Martin was right. The 7 years old, Gattacker still works as a charm.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/1Vsf6irr4Sc" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

Then we tested it on Tesla Model Y. No surprise here, it open the car doors respectively.

![3](https://github.com/user-attachments/assets/21b7c8cb-4b6c-473c-9aeb-de1e28c1fabd)

<p align="justify">
What about the long-distance relay attack? To achieve this goal, we have set up a publicly accessible VPN server. And running Gattacker on Raspberry PIs instead of the PCs.
</p>

![4](https://github.com/user-attachments/assets/e1d5e77a-9251-4ec6-bb42-759f8f05bbbd)

<p align="justify">
The result is quite amazing; we have not just relayed the traffic over a long distance but crossed the city. As shown in the demo, we have successfully opened the door for Tesla in a completely different place.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/ijio9_WJpH4" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

<p align="justify">
So, one question remains, does it work on other modern vehicles that also implement the Phonekey function? In recent years, the smart-car business is booming in China, and almost everyone like to have part in it. I have to say some of them are quite good cars. But when come to security, they still have room to improve. Below is one of the most popular smart car in China. We are easily connected to it with a BLE application, no pairing is required. This is a good, ok maybe a bad sign, depending on how you see it.
</p>

![5](https://github.com/user-attachments/assets/1c5b114a-1091-4dde-96be-1631594c1336)

<p align="justify">
Once again, the Gattacker work very well. And notice that in the demo video, the attacker purposely keeps a distance from the victim, to emulate the real attack.
</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/syj-rghnQRk" 
title="YouTube video player" frameborder="0" 
allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" 
allowfullscreen></iframe>

So here is a trick, when we like to see if the vehicle is vulnerable to the BLE Relay attack, we can always use any BLE App to connect to it, if no pairing is needed, it may be a sign of a problem.

![6](https://github.com/user-attachments/assets/2cc7f617-b18f-41d3-a5c8-9280604a3b83)
![Screenshot from 2024-09-13 15-23-40](https://github.com/user-attachments/assets/e6940323-1969-4464-9d3b-cc05fa1ecc85)
![8](https://github.com/user-attachments/assets/42da7ae7-b436-443f-b2f4-fa2e60e31e8a)
![9](https://github.com/user-attachments/assets/d107dc4a-69e3-42f7-83a0-eda74d3175e7)


THE FAILURE

Just like any science experiment, success was not always the case. During the test, we failed to perform the BLE Relay attack on some of the targets with Gattacker. One clear sign is these vendors have enabled the PIN codes requirement during the beginning of the pairing stage, which is out of the capability of Gattacker since Gattacker only works for the target do not implement the BLE link-layer pairing/encryption.


On 2022 May 15, a security researcher Sultan Qasim Khan from NCC group published a series of articles regarding his research. He has developed a tool for BLE relay attacks capable of relaying encrypted link layer communications. He has tested successfully on the Tesla Model 3 and other IOT products. However, he did not release the tool to the public. But he is kind enough to release another tool called Sniffle. Which is a sniffer for BLE 5 and 4 using TI CC1352/CC26x2 board. Moreover, he happened to use the same board to conduct the link layer BLE relay attack.


Around 2022 Oct, a group of researchers from Team XWZ claimed they reproduced Sultan's link layer relay attack, and successfully demonstrated it on Tesla Model 3. However, at the time of writing, they also failed to attack certain models of cars. One possibility is those vendors may implement an extra security mechanism to prevent the BLE relay attack like this.


ONE MORE THING

On 2019 August, security researcher Martin Herfurt published a project called Tesla Radar. He found that Tesla vehicles constantly broadcast a signal that anyone can detect. In addition, the owner cannot disable such unique hashed iBeacon IDs sent from Tesla vehicles. Anyone concerned about their privacy will likely think this an issue, since anyone can track any Tesla vehicles in this way.



During the research we came across some vendors has a similar issue, and instead of broadcast a hashed ID, they were broadcast the vehicle VIN number in plaintext. Now some people may argue VIN number is a public information. This is true. However, because vendors chose to broadcast the VIN, does helped us to locate the target cars in the parking lots very quick.


FINAL THOUGHT

Our findings have already been reported to the respective vendors. However, we experienced that sometimes due to the downstream dependencies on multiple vendors; it seems very difficult for some vendor to produce a patch. This clearly shows the complexity of resolving vehicle vulnerabilities, fingers crossed.

On 2020 AUG, I presented my research NFC Relay attack on Tesla Model 3 at DEFCON. Just like for BLE Relay attack, response from Tesla are the same, that relay attacks are a known limitation of the passive entry system. Users should be encouraged to use the PIN to Drive feature. However, years ago we found a bug that can bypass the PIN2Drive. And this made us into the Tesla Hall of Fame. Year later researcher Martin Herfurt found another bug, which can bypass the PIN2Drive too. Therefore, all the vendors should fully aware that the security of the products is indeed a long-term game.


REFERENCES

. https://bugcrowd.com/Kevin_2600
· https://www.teslaradar.com/faq/
· https://github.com/nccgroup/Sniffle
· https://rollingpwn.github.io/rolling-pwn/
· https://securepositioning.com/ghost-peak/
· https://trifinite.org/stuff/tempa_relay_attack/
· https://www.youtube.com/watch?v=nn-_3AbtEkI (NFC Relay on Tesla)/
· https://research.nccgroup.com/2022/05/15/technical-advisory-tesla-ble-phone-as-a-key-passive-entry-vulnerable-to-relay-attacks/







































