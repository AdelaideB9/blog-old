---
layout: post
title: ångstromCTF - Substitution
date: 2021-04-08T13:12:52.939Z
authors:
  - authors: lachlan
categories:
  - ctf
  - write-ups
---
For this challenge we are given a source file and a netcat server which presumally runs the source. Downloading the source and running it with python whilst a text file called flag.txt is present in the directory allows us to get numbers encrypted with the flag in some way.\
\
Opening the source file we see the follow script.   

```
#!/usr/bin/python

from functools import reduce

with open("flag", "r") as f:
    key = [ord(x) for x in f.read().strip()]



def substitute(value):
    return (reduce(lambda x, y: x*value+y, key)) % 691



print("Enter a number and it will be returned with our super secret synthetic substitution technique")
while True:
    try:
        value = input("> ")
        if value == 'quit':
            quit()
        value = int(value)
        enc = substitute(value)
        print(">> ", end="")
        print(enc)
    except ValueError:
        print("Invalid input. ")
```

\
\
We can see that this program uses the flag to encypt the numbers we input and prints the result. The main function of interest to us is the substitute function.

```
def substitute(value):
    return (reduce(lambda x, y: x*value+y, key)) % 691

```

\
\
After researching what the reduce function does, we see that we're essentially taking the first character in the flag, multiplying it by out input and adding it to the follow charcater. This process is repreated with the first character of the flag now being the previous result. This result is then m\
odded with 691. In essence, we have the following function:\
\
   $$f(f(f(...)), y) = n mod(691)$$\
\
Since $f$ is a linear function (namely $f(x,y) = x*k+y$), we are essentially creating the linear equation:\
\
   $$g(k) = x_0\*k^{1}+x\_2\*k^{2}+...+x\_{n-1}*k+x_n = s_k mod(691)$$\
\
where $k$ is the key and $x_n$ is the nth character of the flag. To test this understanding,let us evaluate $g(0)$. If our understanding is correct, we should get the ascii value of }, this is $125$. Connecting to the server and trying it, we indeed get the right output.\
\
Knowing this, we can make a NxN linear system where the nth row is the equation $g(n)$. This is as follows:\
\
   -- Maths --\
\
This can be rewritten as:\
\
   $$Ax=B mod(691)$$\
\
where A is:\
\
and B is:\
\
While we are not sure as to how long the flag will be, it is reasonable to say it will be less that 100 characters (based off previously retrieved flags). Using this equation and the fact that the characters must be integers, we can solve the system over $\mathbb{Z}mod(691)$. This can be done with th\
e following sage maths script that uses pwntools to connect to the server and collect the results.

```
from pwn import *
import re

# Connecting with pwntools
nc = remote('crypto.2021.chall.actf.co', 21601)
nc.recvline()

nums = []

for i in range(100):

    % Sending the numbers from 0-99 to the server and listening to the response
    nc.sendline(str(i))
    res = str(nc.recvline())
    % Stripping the number from the response and appending it to numbers
    nums.append(int(re.findall("[0-9]+", res)[0]))

% Using sagemaths to solve the linear system
M = matrix(ZZ, 100, 100, lambda x, y: pow(x, 99-y))
b = vector(GF(691), 100, nums)
solution = M.solve_right(b)

% Converting the solution into a string
solution = list(map(chr, solution))
flag = ''.join(solution)

print(flag)
```

\
\
Sure enough, after a minute or so of running, we get the flag!