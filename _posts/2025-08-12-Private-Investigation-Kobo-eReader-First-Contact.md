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




















