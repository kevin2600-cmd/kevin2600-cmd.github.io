## Private Investigation - Kobo eReader - First Contact

### HOW IT BEGAN

<p align="justify">
Ever since I got involved in the financial sector, I've spent a lot of time trading stocks and reading the Wall-street Journal instead of bug bounty reports. As a result, my hacking skills got a bit rusty and needed a refresh. So, I decided to invest some time into evaluating how robust today’s embedded security really are against a somewhat capable and experienced hacker. But which one should I dive into first?
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/648157eb-f17e-4be0-bb95-ce5bb05f9252">

<p align="justify">
During Cansecwest last year, I was showing one of the speakers around Vancouver. While wandering through a local consumer electronics shop, we spotted a Kobo eReader on the shelf. With a grin, I joked, “we should hack it, do you think there’s a bug bounty for eReaders? we then laughed and left it behind. But maybe now it’s time to make a move. After a quick search, I found that Kobo eReader is a global e-reading company HQ in Toronto, Canada. It was acquired by the Japanese e-commerce giant Rakuten in 2011. Kobo is known for its diverse range of e-reader devices, offering a strong alternative to Amazon Kindle. Best of all, Kobo even has a dedicated webpage focused on products security.
</p>

<img width="780" height="446" alt="K2" src="https://github.com/user-attachments/assets/c6764f25-16db-42f8-9607-f1885853eb0a" />

<p align="justify">
However, I was somewhat disappointed to find that their website states, “Kobo does not offer monetary rewards for vulnerability disclosures. In addition, the website also state that Kobo does not use universal default or easily guessable passwords; I later discovered that this is not entirely true. 
</p>

<img width="580" height="76" alt="image" src="https://github.com/user-attachments/assets/16539894-d6cb-42a3-8277-e3f6d9d09904" />

<p align="justify">
Even though there’s no bug bounty, I still believe the whole journey will make a great series of articles for blog. By sharing my hacking experience with the Kobo eReader, I hope to inspire more people to take an interest in hardware hacking. So, fasten your seatbelt - let the exploration begin!
</p>

### MYSTERY BAUD-RATE

<p align="justify">
Kobo offers various versions of its eReaders, the one I have is the Kobo Clara BW. Since I love low-level hardware hacking, my first step with any device is to disassemble it. Using a blade, I was able to easily pop open the case. 
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/de8fb395-4717-46a3-a307-161cc4956bf6">

<p align="justify">
A closer inspection reveals that the Kobo Clara BW is powered by a MediaTek ARM processor and uses an eMMC chip for storage. 
An upgrade from earlier models that relied on SD cards for file storage.
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/1f9224cf-04d4-4083-86ac-dba2f6dc999f">

<p align="justify">
What really caught my attention was the presence of clearly labelled debug ports, with the TX/RX pins appearing to be UART connections. You’d think that once we connect to the ports, we could start messing around immediately right?
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/821c2d47-2782-44b4-99ec-881925883d8b">

<p align="justify">
Well, we’re not there yet. Here comes our first roadblock. If you’ve done similar project before, you know that configuring the correct baud rate is essential to receiving clear text. Normally, the default baud rate for most devices is either 9600 or 115200. Unfortunately, that’s not the case here, when using those settings, we only receive garbage text.
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/2d2a2033-09ff-422d-989b-91676160d709">

<p align="justify">
So, we needed to find the exact baud rate for the Kobo eReader first. About 10 years ago, a great security researcher named devttys0 released a python script to brute-force baud rates. This method usually works well, but unfortunately, it didn’t work out this time. By looking at the source code, we can see that the usual baud rates are hard-coded. So if our target device doesn’t use those rates, the script won’t be able to figure it out.
</p>

<img width="450" height="235" alt="image" src="https://github.com/user-attachments/assets/9cf943d4-989d-4329-b424-a6ee87510253" />
<img width="217" height="235" alt="image" src="https://github.com/user-attachments/assets/9834b596-eaaf-4fbc-a928-5d44a6213629" />

<p align="justify">
Lucky for us, we have a hardware hacker’s best friend the logic analyzer. I used the Saleae Logic2, the best part is that Saleae provides an extension called Baud Rate Estimate. With it, we were able to easily find the correct secret baud rate.
</p>

<img width="380" height="433" alt="K10" src="https://github.com/user-attachments/assets/a6901543-6c80-461e-abd4-6e7d546682ef" />

<p align="justify">
With the correct baud rate configured, we successfully received meaningful text and were greeted by the login prompt from the Kobo eReader.
</p>

<img width="659" height="147" alt="K10" src="https://github.com/user-attachments/assets/d774c8d7-0bf0-4f2a-a6d0-b7a329ba04cd" />

<p align="justify">
A fun fact: sometimes, the order in which you hack really matters. If I had chosen to root the device first, I probably would have found the correct baud rate clearly written inside a cmdline configuration file first, which would have saved me all the hassle! :p
</p>

<img width="519" height="200" alt="image" src="https://github.com/user-attachments/assets/10f2cc70-d8d2-4983-be9f-0ea313ae81da" />

<p align="justify">
In addition, the U-Boot configuration parameter “bootdelay” specifies a 2 seconds wait during the boot process. This
gives us a 2 seconds window to manually interrupt the boot and access the U-Boot prompt of Kobo eReader.
</p>

<img width="1075" height="403" alt="image" src="https://github.com/user-attachments/assets/81217ead-673a-4b0e-959d-c19546c9beaa" />

### GOT ROOT? DOUBLE KILL!

<p align="justify">
When the Kobo eReader is powered on for the first time, it automatically contacts the remote server to check for any available firmware updates. we can verify the installed firmware and kernel versions in the Device Information section.
</p>

<img width="616" height="376" alt="K18" src="https://github.com/user-attachments/assets/d32039d2-72d5-466d-97e4-c2dfc8b4aa7a" />

<p align="justify">
In addition, the device also supports firmware updates via USB. Specifically, if a file named KoboRoot.tgz is placed in the .kobo folder of the public partition, the Kobo will extract the contents of this archive into the root directory and then reboot automatically.
</p>

<img width="550" height="123" alt="image" src="https://github.com/user-attachments/assets/5c517daa-3dd1-465f-896e-153c937356f6" />

<p align="justify">
This mechanism can be leveraged to modify system files to enable additional services. For example, Telnet access can be enabled by including the appropriate configuration within the inetd.conf.user  file. 
</p>

<img width="550" height="112" alt="image" src="https://github.com/user-attachments/assets/07e0acb9-f498-45ff-a2c0-6cfb3c348441" />

<img width="496" height="62" alt="image" src="https://github.com/user-attachments/assets/cc77d3a1-7d93-4ea2-b83d-a281a014b0d9" />

<p align="justify">
Interestingly, up until Kobo firmware version 4.41.23145, the device’s /etc/passwd file contained an admin account with a password that could be easily guessed or cracked.
</p>

<img width="933" height="269" alt="image" src="https://github.com/user-attachments/assets/4f1b8ac9-91f0-4364-844d-18ea73c8f7ae" />

<p align="justify">
This finding stands in contrast to Kobo’s stated position that they do not use universal default or easily guessable passwords.
</p>

<img width="656" height="162" alt="image" src="https://github.com/user-attachments/assets/e34669ca-992b-437d-b49f-af21511488e0" />


<p align="justify">
We were then able to login to the Kobo eReader via Telnet or Serial port using those credentials, which granted us root privileges. 
</p>

<img width="622" height="241" alt="image" src="https://github.com/user-attachments/assets/653be067-0570-4fc6-824b-ded50600bb38" />

<p align="justify">
Finally, a firmware patch 4.42.23296 released by Kobo in May 2025 appears to have addressed this issue. 
</p>

<img width="614" height="201" alt="image" src="https://github.com/user-attachments/assets/7b139daf-6f7d-4201-9e42-4ddc6d08c011" />

<p align="justify">
We have confirmed that the admin account has indeed been removed.
</p>
<img width="561" height="133" alt="image" src="https://github.com/user-attachments/assets/933dfc2e-2701-4ce2-a8e3-e0b897fce07a" />

<p align="justify">
However, the root cause of the issue is that the Kobo eReader does not implement any key signing to harden the firmware.
</p>
<img width="565" height="139" alt="image" src="https://github.com/user-attachments/assets/4a732d15-e829-457a-a413-edcb16edfe61" />

<p align="justify">
As a result, it is possible to remove the password for the root account and regain access easily with root privilege.
</p>

<img width="795" height="182" alt="image" src="https://github.com/user-attachments/assets/fdfd6011-e25a-41d7-bb42-8688f899b9e3" />

### POST-EXPLOITATION 

<p align="justify">
Once we gained root access on the eReader, uncovering more details about the device became effortless. A simple command revealed that the Kobo Clara BW is powered by an ARMv7 processor built on the MediaTek MT8180 platform.
</p>

<img width="912" height="234" alt="image" src="https://github.com/user-attachments/assets/e4cc2ab9-8b6b-4331-81da-1f4d9845ae88" />

<p align="justify">
As with any post-exploitation phase, it’s advisable to enumerate and analyze all accessible files, as they may contain sensitive information. Configuration files are often a valuable starting point, as they can reveal credentials, API keys, or other security-critical data.
</p>

<img width="448" height="512" alt="image" src="https://github.com/user-attachments/assets/5ed1a0f5-3542-4522-ac48-cb9e641a9fee" />

<p align="justify">
For example, a bt_config.bak file located in the bluedroid directory contains Bluetooth encryption keys in plain-text.
</p>
<img width="475" height="319" alt="image" src="https://github.com/user-attachments/assets/208a15db-abb1-47d8-94b8-0992969e40f5" />

<p align="justify">
The wpa_supplicant.conf file, stored in /etc/wpa_supplicant/, holds the encryption keys for all Wi-Fi networks you’ve ever connected to, making it a critical target for attackers.
</p>
<img width="709" height="271" alt="image" src="https://github.com/user-attachments/assets/68d801f3-9387-4332-8d03-4a0df526d671" />

<p align="justify">
Analysis of the files system revealed that, similar to many other embedded devices, Kobo eReader relies heavily on BusyBox, which may introduce more potential attack vectors.
</p>
<img width="790" height="385" alt="image" src="https://github.com/user-attachments/assets/c19033ba-b15c-4bea-af2e-2180b5aa2243" />

<p align="justify">
A common action attackers take after gaining root access to a target system is to install a hidden backdoor for reverse shell to maintain persistent access. This can be easily achieved using BusyBox’s nc command or more stealthily by leveraging openssl, as the latter encrypts all traffic
</p>
<img width="965" height="615" alt="image" src="https://github.com/user-attachments/assets/d04585ea-0ab7-4b21-a047-5c284ee9cdfe" />

### SUMMARY

<p align="justify">
In the first part of this article, we explored how to identify the actual baud rate and successfully gain root access on the Kobo eReader. In the upcoming article, we’ll dive deeper, demonstrating how to setup hidden backdoor for persistent access and how to perform fuzzing techniques to uncover vulnerabilities. Stay tuned for “Private Investigation – Kobo eReader – Deep Dive.”
</p>

### REFERENCE 

<p align="justify">
https://github.com/devttys0/baudrate
</p>

<p align="justify">
https://github.com/kobolabs/Kobo-Reader
</p>

<p align="justify">
https://pgaskin.net/KoboStuff/kobofirmware.html 
</p>

<p align="justify">
https://help.kobo.com/hc/en-us/articles/22713672676119-Kobo-s-Policy-relative-to-the-Security-of-our-Connected-Products
</p>














