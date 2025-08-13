## Private Investigation - Kobo eReader - First Contact

### HOW IT BEGAN

<p align="justify">
Ever since I got involved in the financial sector, I've spent a lot of time trading stocks and reading the Wall-street Journal instead of bug bounty reports. As a result, my hacking skills got a bit rusty and needed a refresh. So, I decided to invest some time into evaluating how robust today’s embedded security really are against a somewhat capable and experienced hacker. But which one should I dive into first?
</p>

<img width="580" alt="2" src="https://github.com/user-attachments/assets/648157eb-f17e-4be0-bb95-ce5bb05f9252">

<p align="justify">
During Cansecwest last year, I was showing one of the speakers around Vancouver. While wandering through a local consumer electronics shop, we spotted a Kobo eReader on the shelf. With a grin, I joked, “we should hack it, do you think there’s a bug bounty for eReaders? we then laughed and left it behind. But maybe now it’s time to make a move. After a quick search, I found that Kobo eReader is a global e-reading company HQ in Toronto, Canada. It was acquired by the Japanese e-commerce giant Rakuten in 2011. Kobo is known for its diverse range of e-reader devices, offering a strong alternative to Amazon Kindle. Best of all, Kobo even has a dedicated website focused on products security.
</p>

<img width="580" height="446" alt="K2" src="https://github.com/user-attachments/assets/c6764f25-16db-42f8-9607-f1885853eb0a" />

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
A closer inspection reveals that the Kobo Clara BW is powered by a MediaTek ARM processor and uses an eMMC chip for storage. An upgrade from earlier models that relied on SD cards for file storage.
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

<img width="410" height="433" alt="K10" src="https://github.com/user-attachments/assets/a6901543-6c80-461e-abd4-6e7d546682ef" />

<p align="justify">
With the correct baud rate configured, we successfully received meaningful text and were greeted by the login prompt from the Kobo eReader.
</p>

<img width="659" height="147" alt="K10" src="https://github.com/user-attachments/assets/d774c8d7-0bf0-4f2a-a6d0-b7a329ba04cd" />

<p align="justify">
A fun fact: sometimes, the order in which you hack really matters. If I had chosen to root the device first, I probably would have found the correct baud rate clearly written inside a cmdline configuration file first, which would have saved me all the hassle! 
</p>

<img width="519" height="139" alt="image" src="https://github.com/user-attachments/assets/10f2cc70-d8d2-4983-be9f-0ea313ae81da" />

<p align="justify">
In addition, the U-Boot configuration parameter “bootdelay” specifies a 2 seconds wait during the boot process. This
gives us a 2 seconds window to manually interrupt the boot and access the U-Boot prompt of Kobo eReader.
</p>

<img width="519" height="608" alt="K11" src="https://github.com/user-attachments/assets/dece584a-896c-42fb-9fec-74acd6236a22" />

### GOT ROOT? DOUBLE KILL!

<p align="justify">
When the Kobo eReader is powered on for the first time, it automatically contacts the remote server to check for any available firmware updates. we can verify the installed firmware and kernel versions in the Device Information section.
</p>

<img width="519" height="607" alt="K18" src="https://github.com/user-attachments/assets/1f894f06-64b6-49e8-a5fa-5a87b3f12e01" />

<p align="justify">
In addition, the device also supports firmware updates via USB. Specifically, if a file named KoboRoot.tgz is placed in the .kobo folder of the public partition, the Kobo will extract the contents of this archive into the root directory and then reboot automatically.
</p>

<p align="justify">
This mechanism can be leveraged to modify system files to enable additional services. For example, Telnet access can be enabled by including the appropriate configuration within the inetd.conf.user  file. 
</p>

<p align="justify">
Interestingly, up until Kobo firmware version 4.41.23145, the device’s /etc/passwd file contained an admin account with a password that could be easily guessed or cracked.
</p>

<p align="justify">
This finding stands in contrast to Kobo’s stated position that they do not use universal default or easily guessable passwords.
</p>

<p align="justify">
We were then able to login to the Kobo eReader via Telnet or Serial port using those credentials, which granted us root privileges. 
</p>

<p align="justify">
Finally, a firmware patch 4.42.23296 released by Kobo in May 2025 appears to have addressed this issue. 
</p>

<p align="justify">
We have confirmed that the admin account has indeed been removed.
</p>

<p align="justify">
However, the root cause of the issue is that the Kobo eReader does not implement any key signing to harden the firmware.
</p>

<p align="justify">
As a result, it is possible to remove the password for the root account and regain access easily with root privilege.
</p>

### POST-EXPLOITATION 

<p align="justify">
Once we gained root access on the eReader, uncovering more details about the device became effortless. A simple command revealed that the Kobo Clara BW is powered by an ARMv7 processor built on the MediaTek MT8180 platform.
</p>





