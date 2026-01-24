
## Overview
This project demonstrates the use of steganography to hide confidential data within image files and later extract it using Linux-based tools. The lab highlights how data can be seceretly transferred without drawing attention, a technique often abused in cyber attacks.

##  Objectives
- Hide a text file inside an image
- Extract hidden data using a passphrase
- Understand steganography from a cybersecurity perspective
  
---

##  Tools Used
- Kali Linux
- steghide

---

## Steps taken
- On Kali Choose an image -cf example osinmehn.jpg
- Create a secret file: echo "hiding files in files" > checkpoint.txt
- Hide data inside the image (steghide embed -ef cheeckpoint.txt -cf osinmehn.jpg
- Enter a demo passphrase
- Extract the hidden data (steghide extract -sf osinmehn.jpg)
- checkpoint.txt is extracted

---

##  Screenshots

### Embedding Secret Data
To embed a file into an image, run the cmd **steghide embed -ef checkpoint.txt -cf osinmehn.jpg.**
what the command does it to embed the file checkpoint.txt into the image file osinmehn.jpg 
-ef stands for embed file
-cf stands for cover file
<img width="889" height="201" alt="Steghide 1" src="https://github.com/user-attachments/assets/89d277a0-b25d-4c8d-917c-3fef26538e5b" />


### Extracting Hidden Data
To embed a file into an image, run the cmd **steghide extract -sf osinmehn.jpg.**
what the command does it to extract any hidden file in the osinmehn.jpg file. 
-sf stands for stego-file i.e file that has been hidden data.
<img width="687" height="266" alt="steghide 2" src="https://github.com/user-attachments/assets/ae546597-5e0b-47b7-9928-fe319f272540" />

---


##  Security Implications
- Steganography Attackers may hide malicious commands or stolen data inside image or audio files and upload them to websites or send them via email.
- Defenders may use steganography to secretly embed watermarks or authentication data inside digital media to prove ownership, detect tampering, or securely transmit sensitive information    without attracting attention 
- Traditional security controls may not detect stego-based attacks

---

##  Key Takeaways
- Steganography hides the *existence* of data while encryption conceals the content of a message. 
- Encryption and steganography together increase secrecy
- Stego-analysis is important in digital forensics
  
