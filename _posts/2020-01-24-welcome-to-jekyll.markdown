---
layout: post
title:  "Welcome to Jekyll!"
date:   2019-01-24 00:00:00 +9:30
author:	javad
categories: jekyll update
---
You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

Jekyll requires blog post files to be named according to the following format:

`YEAR-MONTH-DAY-title.MARKUP`

Where `YEAR` is a four-digit number, `MONTH` and `DAY` are both two-digit numbers, and `MARKUP` is the file extension representing the format used in the file. After that, include the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight py %}
import math,random,pickle,time



# The Euclidean algorithm for gcd.

def gcd(a,b):
  while a!=0:
    a,b=b%a,a
  return(b)


# Find the inverse of a mod n, cf. Wiki, Extended Euclidean algorithm.

def modinv(a,n):
  t,nt=0,1
  r,nr=n,a
  while nr!=0:
    q=r//nr
    t,nt=nt,t-q*nt
    r,nr=nr,r-q*nr
  if r>1:
    return None
  while t<0:
    t+=n
  return(t)


# The initial constellation of the table of small primes.

ptab=[2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,59,61,67,71]


# ptab, the table of small primes, will be extended, if necessary, to have its
# last element not less than trialdivbound. (Sieve of Eratosthenes is used.)

def checkptab(trialdivbound):
  global ptab
  g=ptab[-1]
  while ptab[-1]<trialdivbound:
    g+=2
    h=math.ceil(math.sqrt(g))
    for p in ptab:
      if p>h:
        suc=1
        break
      if (g%p)==0:
        suc=0
        break
    if suc==0:
      continue
    ptab+=[g]
  return


# We extend ptab by default to a size appropriate for primality test with trial
# division for numbers up to 20 bits (this is required in the first part of the
# function provableprime() further below).

checkptab(2**10)


# Apply t rounds of the Miller-Rabin test to n.
#
# Return value: 0: n is composite.
#
#               1: n passes t rounds of the strong probable prime test.
#
# Set kn=1, if the first round is to use a=2, else set kn=0.
#
# See [1, p.139].
#
# We use this test only to improve the efficiency of Maurer's algorithm, see
# comments in provableprime() futher below.

def millerrabin(n,t,kn):
  assert t >= 1 and 0 <= kn <= 1
  if n<=3:
    if n>1:
      return(1)
    return(0)
  elif n%2==0:
    return(0)
  RANDOM=random.SystemRandom()
  r=n1=n-1
  s=0
  while (r%2)>0:
    s+=1
    r//=2
  s1=s-1
  for i in range(t):
    if i==0 and kn==1:
      a=2
    else:
      a=RANDOM.randint(2,n-2)    
    y=pow(a,r,n)
    if y!=1 and y!=n1:
      j=1
      while j<=s1 and y!=n1:
        y=(y*y)%n
        if y==1:
          return(0)      
        j+=1
      if y!=n1:
        return(0)
  return(1)


# Generate a random odd number n (2**k > n > 2**(k-1), k >= 2) with proveable
# primality employing Maurer's algorithm (the (k-1)-th bit of n is 1).
# Cf. sec.4.62 of HAC [1, p.153].

def provableprime(k):
  global ptab
  RANDOM=random.SystemRandom()
# The default size of ptab (see above) is sufficiently large for cases k<=20.
  if k<=20:
    while True:
# Select a random odd integer in the interval [2**(k-1), 2**k-1] (A. J. Menezes,
# personal communication).
      n=RANDOM.randint(2**(k-1),2**k-1)|1
      h=math.ceil(math.sqrt(n))
      for p in ptab[1:]:
        if p>h:
          return(n) 
        if (n%p)==0:
          break    
# We use c=0.005 which has been experimentally found to be optimal in processing
# time for common PC under MS Windows and values of k of practical interest.
# A different c value may be desirable for use in different computing
# environments.        
  c=0.005
  bb=math.ceil(c*k*k)
  checkptab(bb)
  m=20
  if k>2*m:
    while True:
      s=RANDOM.uniform(0,1)
      r=2**(s-1)
      if (k-r*k)>m:
        break
  else:
    r=0.5
  q=provableprime(math.floor(r*k)+1)
  ii=2**(k-1)//(2*q)
  success=0
  while success==0:
    rr=RANDOM.randint(ii+1,2*ii)    
    n=2*rr*q+1
    suc=1
    for p in ptab:
      if p>bb:
        break
      if (n%p)==0:
        suc=0
        break
    if suc==0:
      continue
# See [1, p.153, 4.6.3 (ii)], Miller-Rabin test is employed here for purposes of
# improvement of efficiency only.
    if millerrabin(n,1,1)==1:
      a=RANDOM.randint(2,n-2)
      if pow(a,n-1,n)==1:
        b=pow(a,2*rr,n)
        if gcd(b-1,n)==1:
          success=1          
  return(n)


print(provableprime(1000))

{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
