#### Task description
Uh.. Oh.. Help me, I just browsing funny cats memes, when I click download cute cat picture, the file that been download seems little bit wierd. I accidently run the file making my files shredded. Ughh now I hate cat meowing at me.

#### Solve
We are given `flag.shredded` file.
![[Pasted image 20241229051129.png]]
Looking for the header of that file we can understand that it is `jfif` file, so that is picture. 
Analyzing file we can recognize that there are a lot of word `meow` in the file.
![[Pasted image 20241229051435.png]]
And in the description cats where mentioned, so we should remove all the `meow` from the file, i used python for that:
```python
data = open('flag.jfif', 'rb').read()
open('flag.jfif', 'wb').write(data.replace(b'meow', b''))
```
And we are getting image:
![[Pasted image 20241229051804.png]]
We can see that part of flag is still redacted, checking file again, and there are still `meow`, so lets remove it again, and we are getting the flag:
![[Pasted image 20241229052111.png]]
