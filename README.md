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
Based on the assignment requirements, we will write a Python script named ```crack_pass2.py``` to:
- Iterate through each word in rockyou.txt
- Remove each character in the word one by one
- Hash each modified version and compare it with the given hash

```python
import hashlib
def sha256(s):
    return hashlib.sha256(s.encode()).hexdigest()

target_hash = "fb58c041b0059e8424ff1f8d2771fca9ab0f5dcdd10c48e7a67a9467aa8ebfa8"
count = 0

with open("rockyou.txt", "r", encoding="latin-1") as f:
    for word in f:
        word = word.strip()
        if len(word) < 2:
            continue
        for i in range(len(word)):
            mutated = word[:i] + word[i+1:]  
            count += 1
            if count % 500000 == 0:
                print(f"Đã thử {count} tổ hợp: {mutated}")
            if sha256(mutated) == target_hash:
                print("✅ Password 2 FOUND:", mutated)
                exit()
```
I spent quite some time, and the result is: 

![image](https://hackmd.io/_uploads/HJ0oZRE8xx.png)

Finally it's pass 3 :

I will create a Python script named ```crack_pass3.py```

```python
python import hashlib
from itertools import product
from multiprocessing import Pool, cpu_count

# SHA256 hash của pass3
target = "4ac53d04443e6786752ac78e2dc86f60a629e4639edacc6a5937146f3eacc30f"

# Bảng leet hóa nguyên âm
leet_map = {
    'a': ['a', '4', '@'],
    'e': ['e', '3', '€'],
    'i': ['i', '1', '!'],
    'o': ['o', '0'],
    'u': ['u', 'µ']
}

def leet_variants(word):
    slots = []
    for c in word:
        if c.lower() in leet_map:
            slots.append(leet_map[c.lower()])
        else:
            slots.append([c])
    return map(''.join, product(*slots))

def process_word(word):
    word = word.strip()
    for variant in leet_variants(word):
        h = hashlib.sha256(variant.encode()).hexdigest()
        if h == target:
            return variant
    return None

def main():
    with open("rockyou.txt", "r", errors="ignore") as f:
        words = [line.strip() for line in f if any(v in line for v in 'aeiou')]

    with Pool(cpu_count()) as pool:
        for result in pool.imap_unordered(process_word, words):
            if result:
                print(f"\n✅ Found pass3: {result}")
                break

if __name__ == "__main__":
    main()
```

The result is :

![image](https://hackmd.io/_uploads/ryH-M0NUgg.png)

Now we will put it back together 

FLAG : ``` L3AK{hyepsi^4B_thecowsaysmo_unf0rg1v@bl3} ```

## Forensic
### Wi-Fight A Ghost?
![image](https://hackmd.io/_uploads/S1yw_hMIxl.png)

This is an assignment I worked on together with my teammate [@daq2712](https://github.com/daq2712)

We need to answer 14 questions to get the flag

![image](https://hackmd.io/_uploads/S1_3O3MUxg.png)

Tools used for the assignment: Registry Explorer, DB Browser for SQLite, MFT Explorer

#### 1. What was the ComputerName of the device?

Navigate to 'KAPEOUT/C/Windows/System32/config/SYSTEM'

Open hive 'SYSTEM' using registry explorer, go to 'ControlSet001ControlComputerNameComputerName'

![image](https://hackmd.io/_uploads/Skk0Fnz8el.png)

Found the computer name is `99PHOENIXDOWNS`

![image](https://hackmd.io/_uploads/Hy1Z5nGLlx.png)

#### 2. What was the SSID of the first Wi-Fi network they connected to?

Need to load the `SOFTWARE` hive using Registry Explorer, then go to `MicrosoftWindows NTCurrentVersionNetworkList`

![image](https://hackmd.io/_uploads/r13352fUex.png)

There are two, but since the question asks which one came first, you should base it on the time and choose the earlier one, which is `mugs_guest_5G`.

![image](https://hackmd.io/_uploads/HyQLohzLel.png)

#### 3. When did they obtain the DHCP lease at the first café?

In hive 'SYSTEM', at 'ControlSet001ServicesTcpipParametersInterfaces'

![image](https://hackmd.io/_uploads/rypL22MUxl.png)

The answer is `2025-05-14 00:13:36`

![image](https://hackmd.io/_uploads/Hy4C2hf8lg.png)

#### 4. What IP address was assigned at the first café?

Still in the same position as the sentence above, the IP address obtained is `192.168.0.114`

![image](https://hackmd.io/_uploads/rJYzTnz8ge.png)

![image](https://hackmd.io/_uploads/BJXEpnGLgx.png)


#### 5. What GitHub page did they visit at the first café?

Access Edge's History file `KAPEOUT\C\Users\NotVi\AppData\Local\Microsoft\Edge\User Data\Default\History`

Open with DB Browser, select the `Browse Data` tab, and choose the `urls` table

![image](https://hackmd.io/_uploads/S1KJChzIle.png)

Saw the Github link and that is the answer `https://github.com/dbissell6/DFIR/blob/main/Blue_Book/Blue_Book.md`

![image](https://hackmd.io/_uploads/Hk77Rnf8lg.png)

#### 6. What did they download at the first café?

Still in DB Browser, switch to the `downloads` table

![image](https://hackmd.io/_uploads/S1mFRnMUle.png)

Saw that the downloaded file is `ChromeSetup.exe`

![image](https://hackmd.io/_uploads/BkI2RnGUex.png)

#### 7. What was the name of the notes file?

In `KAPEOUTCUsersNotViAppDataRoamingMicrosoftWindowsRecent` there is a .txt file

![image](https://hackmd.io/_uploads/HkjUkTfIlx.png)

The answer is `HowToHackTheWorld.txt`

![image](https://hackmd.io/_uploads/rkpt1TzLgg.png)

#### 8. What are the contents of the notes?

I tried clicking on that file but it didn't work

![image](https://hackmd.io/_uploads/SkPoJTz8el.png)

I need to use MFT Explorer to open the `$MFT` file

![image](https://hackmd.io/_uploads/r1hTJaGUxe.png)

After more than 30 minutes of waiting for it to load, I finally found the content of the txt file.

![image](https://hackmd.io/_uploads/ByTzlTGIlg.png)

The answer is `Practice and take good notes.`

![image](https://hackmd.io/_uploads/ByOBlTGLgl.png)

####  9. What was the SSID of the second Wi-Fi network they connected to?

Similar to question 2, this time the answer is the remaining one, `AlleyCat`

![image](https://hackmd.io/_uploads/rkc9x6z8xe.png)

![image](https://hackmd.io/_uploads/HyZnlaGLxg.png)

#### 10. When did they obtain the second lease?

The answer is `2025-05-14 00:35:07`

![image](https://hackmd.io/_uploads/B1O1baG8gx.png)

![image](https://hackmd.io/_uploads/SyzfZpzIge.png)

####  11. What was the IP address assigned at the second café?

![image](https://hackmd.io/_uploads/r1g7LZaf8el.png)

The answer is `10.0.6.28`

![image](https://hackmd.io/_uploads/S1BOWpGLll.png)

#### 12. What website did they log into at the second café?

As seen in question 5, Edge's browsing history ends when the user has installed Google Chrome, so you need to check the History in Chrome at `KAPEOUTCUsersNotViAppDataLocalGoogleChromeUser DataDefaultHistory`

![image](https://hackmd.io/_uploads/rJkMMTG8ll.png)

Thus, the user has logged in at `l3ak.team`

![image](https://hackmd.io/_uploads/BJ0mz6f8xe.png)

#### 13. What was the MAC address of the Wi-Fi adapter used?

The answer is ```48:51:C5:35:EA:53```

Install python-evtx in a virtual environment ```pip install python-evtx```

Since the task requires finding the MAC address, we can look for it in the ```.evtx``` fileThen we will check where the .evtx file is located```find . -iname "*.evtx"```

After extracting, we will focus on 3 files:

```
./Windows/System32/winevt/logs/Microsoft-Windows-WLAN-AutoConfig%4Operational.evtx
./Windows/System32/winevt/logs/Microsoft-Windows-NetworkProfile%4Operational.evtx
./Windows/System32/winevt/logs/Microsoft-Windows-Dhcp-Client%4Admin.evtx
```

Create a Python file named parse_evtx.py

```python
from Evtx.Evtx import Evtx
import sys

if len(sys.argv) != 2:
    print(f"Usage: {sys.argv[0]} <evtx_file>")
    sys.exit(1)

evtx_path = sys.argv[1]

with Evtx(evtx_path) as log:
    for record in log.records():
        print(record.xml())
```


