#### Task description
Santa Claus is coming to town! Send your wishes by connecting to the netcat service!

#### Solve
We are given `server.py` file. As we analyze it we can see that it is cryptosystem where we can register, login, enter out wish, get list of the wishes (for Santa only, so flag should be there) and exit.
As we are registering we can get the token to the future login, it is using our name to compute it:
```python
def generateToken(name):
    data = name.encode(errors="surrogateescape")
    crc = (1 << 128) - 1
    for b in data:
        crc ^= b
        for _ in range(8):
            crc = (crc >> 1) ^ (m & -(crc & 1))
    return hex(crc ^ ((1 << 128) - 1))[2:]
```
We need to user name Santa Claus, but we cannot register if in the name "Sants Claus":
```python
if option == 1:
	name = str(input("Enter your name: "))
	if "Santa Claus" in name:
		print("Cannot register as Santa!\n")
		continue
	print(f"Use this token to login: {generateToken(name)}\n")
```
`m` is a secret parametr, so we cant generate token for "Santa Claus", so we need to get m. While analyzing function `generateToken` i thought that `(m & -(crc & 1))` could be interesting, and it is.
`-(crc & 1)` could be only `0` or `0xfff...ffff`, so if `-(crc & 1)` is equal to `0`, then `m` doesnt affect `crc`, as `(m & -(crc & 1)) == 0` and `(crc >> 1) ^ (m & -(crc & 1)) == (crc >> 1)`.
That does mean that if we will register as a user with the lenght of the name that is equal to 1, then we can take this one character in such a way that `m` will affect `crc` only once, then we will get encrypted `m` in our token.
In the beggining of token generation `crc == 0xffff...fff or 0b1111...111`, so we need xor `crc` with our symbol and get last 7 bits to be equal to 0, thats for sure `0x7f == 0b1111111`.
Now we should just do it on the server, and after getting token for user with name `0x7f` we can get `m`: `m = int(hex(int(token, 16) ^ ((1 << 128) - 1))[2:], 16) ^ 0x0ffffffffffffffffffffffffffffff`
And now we can login as Santa: `token = generateToken("Santa Claus")`, choose option 2 for login, then chooes option 4 for getting flag.
Here is my solve:
```python
# solve.py
def generateToken(name, m):
    data = name.encode(errors="surrogateescape")
    crc = (1 << 128) - 1
    for b in data:
        crc ^= b
        for _ in range(8):
            crc = (crc >> 1) ^ (m & -(crc & 1))
    return hex(crc ^ ((1 << 128) - 1))[2:]  

import socket

client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

host = '43.217.80.203'
port = 32825  
client_socket.connect((host, port))

data = client_socket.recv(161)
print(data)

client_socket.send(b'1\n')

data = client_socket.recv(1024)
print(data)

client_socket.send(b'\x7f\n')
data = client_socket.recv(1024)
token = data.decode().split("\n")[0].split(" ")[5]
m = int(hex(int(token, 16) ^ ((1 << 128) - 1))[2:], 16) ^ 0x0ffffffffffffffffffffffffffffff
tok = generateToken("Santa Claus", m)
print('Santa Claus', tok)

while True:
    client_socket.send(input(': ').encode() + b'\n'); print(client_socket.recv(1024))
```
