#### Task description
We found a leak of a blackmarket website's login credentials. Can you find the password of the user osman and successfully decrypt it?

#### Solve
We are given two files: `user.txt` and `passwd.txt` so that is definetly users and their passwords:
user.txt:
![[Pasted image 20241229055643.png]]
passwd.txt:
![[Pasted image 20241229055718.png]]
Firstly we should find password for user `osman`, doing it with python:
```python
with open('user.txt', 'r') as uf, open('passwd.txt', 'r') as pf:
    users = uf.readlines()
    passwords = pf.readlines()

combined = [f"{users[i].strip()}: {passwords[i].strip()}" for i in range(len(users))]

for line in combined:
    print(line)
```
And we get password for `osman`:
![[Pasted image 20241229060120.png]]
We should decrypt it, the structure of the flag has been preserved, so we can easily understand that its `Caesar's cipher`, and we should just find the right one:
```python
input_string = "ZJPB{e6g180g9f302g8d8gddg1i2174d0e212}"

n = len(input_string)

for shift in range(26):
    encrypted = ""
    for char in input_string:
        if char.isalpha():
            shift_amount = shift % 26
            if char.islower():
                new_char = chr(((ord(char) - ord('a') + shift_amount) % 26) + ord('a'))
            else:
                new_char = chr(((ord(char) - ord('A') + shift_amount) % 26) + ord('A'))
        else:
            new_char = char
        encrypted += new_char
    print(f"Shift {shift}: {encrypted}")
```
And here is out flag: WGMY{b6d180d9c302d8a8daad1f2174a0b212}
