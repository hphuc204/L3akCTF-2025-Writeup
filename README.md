# L3akCTF-2025-Writeup
These are some challenges I was able to solve in this competition. I am a newbie new to playing CTF. I was very happy to have solved some of these challenges together with my teammates, with the assistant GPT by my side. I have never done write-ups before - please excuse any mistakes
## Hash Cracking
![image](https://hackmd.io/_uploads/BkamgCN8xg.png)
The flag here will require finding 3 passwords and combining them with the form L3AK{pass1_pass2_pass3}
Find password 1:
First, we will create the file hashes.txt
```echo "5e09f66ae5c6b2f4038eba26dc8e22d8aeb54f624d1d3ed96551e900dac7cf0d" > hashes.txt```
Second ``` hashcat -m 1400 -a 0 -r append_custom.rule -o found.txt hashes.txt rockyou.txt```
When there is a notification```INFO: All hashes found as potfile and/or empty entries! Use --show to display them.```
It means the hash has been cracked and the result has been saved in hashcat's potfile.
We will see the result as 
![image](https://hackmd.io/_uploads/H1tTqRE8el.png)
