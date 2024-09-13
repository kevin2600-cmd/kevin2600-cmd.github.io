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
