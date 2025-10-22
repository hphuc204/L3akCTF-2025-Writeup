# L3akCTF-2025-Writeup
These are some challenges I was able to solve in this competition. I am a newbie new to playing CTF. I was very happy to have solved some of these challenges together with my teammates, with the assistant GPT by my side. I have never done write-ups before - please excuse any mistakes
## Hash Cracking
![image](https://hackmd.io/_uploads/BkamgCN8xg.png)

### Rule Breaker 1

The flag here will require finding 3 passwords and combining them with the form L3AK{pass1_pass2_pass3} with 3 hashes 
```
5e09f66ae5c6b2f4038eba26dc8e22d8aeb54f624d1d3ed96551e90dac7cf0d
fb58c041b0059e8424ff1f8d2771fca9ba0bf5dcdd10c48e7a67a9463ebfa8
4ac53d0444a86786752ac78e2dc86f60a629e4639edacc6a5937146f3eacc30f
```

Find password 1:

First, we will create the file hashes.txt
```echo "5e09f66ae5c6b2f4038eba26dc8e22d8aeb54f624d1d3ed96551e900dac7cf0d" > hashes.txt```

Second ``` hashcat -m 1400 -a 0 -r append_custom.rule -o found.txt hashes.txt rockyou.txt```

When there is a notification```INFO: All hashes found as potfile and/or empty entries! Use --show to display them.```

It means the hash has been cracked and the result has been saved in hashcat's potfile.

We will see the result as 

![image](https://hackmd.io/_uploads/H1tTqRE8el.png)

Now I will find password 2:
Based on the assignment requirements, we will write a Python script named ```crack_pass2.p```y to:
- Iterate through each word in rockyou.txt
- Remove each character in the word one by one
- Hash each modified version and compare it with the given hash
