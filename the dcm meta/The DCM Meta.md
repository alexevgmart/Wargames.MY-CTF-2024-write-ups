#### Task description
`[25, 10, 0, 3, 17, 19, 23, 27, 4, 13, 20, 8, 24, 21, 31, 15, 7, 29, 6, 1, 9, 30, 22, 5, 28, 18, 26, 11, 2, 14, 16, 12]`

#### Solve
We are given `challenge.dcm` file. So we should look whats inside:
![[Pasted image 20241229052943.png]]
There is definetly just a flag, so we should try it. And that is incorrect. We can recognize that description is a huuuge hint, we need to take parts of the flag by those indexes, but only md5 between `{}` as the maximum number in those indexes is 31.
Here is simple python script to get the flag:
```python
with open('challenge.dcm', 'r') as file:
    content = file.read()

filtered_chars = [char for char in content if 0x30 <= ord(char) <= 0x7a]

first_four_chars = filtered_chars[:4]
rest_chars = filtered_chars[4:]

indices = [25, 10, 0, 3, 17, 19, 23, 27, 4, 13, 20, 8, 24, 21, 31, 15, 7, 29, 6, 1, 9, 30, 22, 5, 28, 18, 26, 11, 2, 14, 16, 12]

reordered_chars = [None] * len(rest_chars)
for i, index in enumerate(indices):
    if index < len(rest_chars):
        reordered_chars[index] = rest_chars[i]

result = 'WGMY{' + ''.join([char for char in reordered_chars if char is not None]) + '}'
print(result) # WGMY{3e5a7b372766483d5c5d1c037f6b5d01}
```
Task is really easy, but i got first blood for that one, and that feels really nice.