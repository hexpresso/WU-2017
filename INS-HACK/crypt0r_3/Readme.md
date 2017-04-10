#Crypt0r 3

     _Nokorimono ni wa fuku ga aru_

The CTF was meant to last 5 hours, I did some work for other challenges and at this point I 
had only few minutes left before the time over. 

The two first challenges gave 150 points each, this one awards 250 points. It should be as
hard as the two first one combined, right? I spent too much time on other challenges,
so it must be impossible to flag a 250 points chall in a so tight timing? Shall we give up?

No. Never surrender.

## Run for the win
The goal is to decrypt a file which has been crypted by the ransomware without paying
bitcoins. I choose a total blackbox approach. At first let see how things works when
crypting files:

```
mitsurugi@dojo:~/chall/inshack/mails$ mkdir test
mitsurugi@dojo:~/chall/inshack/mails$ cd test/
mitsurugi@dojo:~/chall/inshack/mails/test$ cp ../crypt.me ../crypt0r.jessie .
mitsurugi@dojo:~/chall/inshack/mails/test$ ./crypt0r.jessie 
./crypt0r.jessie: error while loading shared libraries: libssl.so.1.0.0: cannot open shared object file: No such file or directory
mitsurugi@dojo:~/chall/inshack/mails/test$ export LD_LIBRARY_PATH=../usr/lib/x86_64-linux-gnu/
mitsurugi@dojo:~/chall/inshack/mails/test$ ./crypt0r.jessie 
src/crypt.c :: 149168520

src/crypt.c :: Generate p
src/crypt.c :: Generate q
src/crypt.c :: Compute n
src/crypt.c :: Compute phi
src/crypt.c :: Generate e
src/crypt.c :: Compute modular inverse
src/crypt.c :: Compute derivative p
src/crypt.c :: Compute derivative q
src/crypt.c :: Compute modular inverse of q mod p
src/crypt.c :: Gonna encrypt the input
src/crypt.c :: Got encrypted data.
src/comm.c :: Retrieve enc key
src/comm.c :: Connect to the CnC.
src/comm.c :: Get host by name
src/comm.c :: cnc.challenge-by.ovh
src/comm.c :: Prepare sockaddr
src/comm.c :: Connect.
src/comm.c :: Ask server enc key.
src/comm.c :: POST /gate.php HTTP/1.1
User-Agent: Crypt0r
Host: crypt0r.gate
Content-Type: application/json

Q 

KI^CE
AOSMO 

NK^
D_FF W 
src/comm.c :: Wait response.
src/comm.c :: Skip headers
src/comm.c :: HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 103
Server: Crypt0r

Q
MODOXK^ONaO
~_
KCGOY
FK
HCOXO
FOY
LXC^OY
O^
FO
IENO
iK
^EGHO
HCOD
IBOP
e|b
DE_Y
K_YYC

W
src/comm.c :: Decrypt envelope
src/comm.c :: HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 103
Server: Crypt0r

{ "generatedKey": "$$Tu aimes la biere, les frites et le code? Ca tombe bien, chez OVH nous aussi!$$" }
src/comm.c :: Tu aimes la biere, les frites et le code? Ca tombe bien, chez OVH nous aussi!$
src/comm.c :: Connect to the CnC.
src/comm.c :: Get host by name
src/comm.c :: cnc.challenge-by.ovh
src/comm.c :: Prepare sockaddr
src/comm.c :: Connect.
src/comm.c :: Build http.
src/comm.c :: Count CRLF
src/comm.c :: Start replace
src/comm.c :: Replace done.
src/comm.c :: Build http payload.
src/comm.c :: Encrypt payload
src/comm.c :: Base64
src/comm.c :: "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"

src/comm.c :: Encrypt payload.
src/comm.c :: Concat.
src/comm.c :: Send payload.
src/comm.c :: Recv res.
src/comm.c :: HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 0
Server: Crypt0r


src/comm.c :: Free request.
src/main.c :: Write enc file.
src/main.c :: BAM
      ____                  _    ___       
     / ___|_ __ _   _ _ __ | |_ / _ \ _ __ 
    | |   | '__| | | | '_ \| __| | | | '__|
    | |___| |  | |_| | |_) | |_| |_| | |   
     \____|_|   \__, | .__/ \__|\___/|_|   
                |___/|_|                   


   !!  You've been Crypt0riz3d nigga  !!

Send 50 bitcoins on my wallet to get back
your file and send me the following code:
                 4cfc-63f-8

If you don't, in 7 days it's gonna be 100.

Your file has been encrypted using RSA
algorithm, only my decrypt0r can give you
the file back.

~~~ Cyber Super Villain of the Darknet ~~~

src/main.c :: free mem
src/crypt.c :: free_keys()
mitsurugi@dojo:~/chall/inshack/mails/test$ ls
crypt.me  crypt0r.jessie  decrypt.me  private.pem
mitsurugi@dojo:~/chall/inshack/mails/test$
```

There is a lot of things really really interesting here. At first, right after the lauch
of crypt0r, we see this line:
```
mitsurugi@dojo:~/chall/inshack/mails/test$ ./crypt0r.jessie
src/crypt.c :: 149168520

```

And 149168520 really looks like an unix time. That's just a bit shorter, but it reminds
me something:
```
mitsurugi@dojo:~/chall/inshack/mails/test$ strings crypt0r.jessie | grep date
echo $(( `date +%s` / 10 ))
mitsurugi@dojo:~/chall/inshack/mails/test$
```

So, it's a date. And right after the date, we have the generation of p, q and all the crypto
stuff. 

The second thing really really strange is the presence of the private.pem file *after* the
encryption. Could it be the private.pem used to crypt the file???

```
mitsurugi@dojo:~/chall/inshack/mails/test$ openssl rsautl -decrypt -inkey  private.pem -in decrypt.me -out clear_text_file
mitsurugi@dojo:~/chall/inshack/mails/test$ cat clear_text_file 
The name's 0xMitsugi, remember it!
mitsurugi@dojo:~/chall/inshack/mails/test$
```

It works \o/
I think this a pretty bad ransomware :) It does not encrypt all your data, and you have the
key to decrypt it for free.

## Solve faster than light
Everything take place. We assume that the private.pem key used to cipher the file derived only
by the date. And the crypt0r binary is kind enough to give us the file. If only we knew
when the file from the challenge were encrypted, we could reuse the crypt0r file to spit
out the right private.pem file. What can we do?
```
mitsurugi@dojo:~/chall/inshack/mails/test$ file the-file-to-decrypt.flag 
the-file-to-decrypt.flag: POSIX tar archive (GNU)
mitsurugi@dojo:~/chall/inshack/mails/test$ tar xvf the-file-to-decrypt.flag
the-file-to-decrypt.flag
mitsurugi@dojo:~/chall/inshack/mails/test$ ls -l the-file-to-decrypt.flag
-rw-r--r-- 1 mitsurugi mitsurugi 256 Apr  3 19:14 the-file-to-decrypt.flag
mitsurugi@dojo:~/chall/inshack/mails/test$
```

Good for us. The challenge file is a tar, which keeps the date. We know that crypt0r
does a `echo $(( `date +%s` / 10 ))`, and we have the minute when the file was encrypted. It 
means only 6 keys to test at worst. Let's hijack the `date` program with a $PATH hack
and do it:

```
mitsurugi@dojo:~/chall/inshack/mails/test$ cat date  
#! /bin/bash
# 2017 Apr 03 19:14:00 is 1491239640 in unix time
echo 1491239640
mitsurugi@dojo:~/chall/inshack/mails/test$ export PATH=.:$PATH
mitsurugi@dojo:~/chall/inshack/mails/test$ ./crypt0r.jessie 
src/crypt.c :: 149123964

src/crypt.c :: Generate p
src/crypt.c :: Generate q
src/crypt.c :: Compute n
src/crypt.c :: Compute phi
src/crypt.c :: Generate e
(... snip all the debug blah blah...)
mitsurugi@dojo:~/chall/inshack/mails/test$ openssl rsautl -decrypt -inkey  private.pem -in the-file-to-decrypt.flag -out clear_text_file
RSA operation error
140226253108416:error:0407109F:rsa routines:RSA_padding_check_PKCS1_type_2:pkcs decoding error:../crypto/rsa/rsa_pk1.c:243:
140226253108416:error:04065072:rsa routines:rsa_ossl_private_decrypt:padding check failed:../crypto/rsa/rsa_ossl.c:477:
mitsurugi@dojo:~/chall/inshack/mails/test$
```

As it was a matter of seconds before time runs out, I knew than it was quicker to make
six test by hand instead of writing a script to automatize this, so the second date:

```
mitsurugi@dojo:~/chall/inshack/mails/test$ cat date 
#! /bin/bash
echo 1491239650
mitsurugi@dojo:~/chall/inshack/mails/test$ ./crypt0r.jessie > /dev/null
mitsurugi@dojo:~/chall/inshack/mails/test$ openssl rsautl -decrypt -inkey  private.pem -in the-file-to-decrypt.flag -out clear_text_file
mitsurugi@dojo:~/chall/inshack/mails/test$ cat clear_text_file 
Congratulations !

You did decrypt this file. You really should consider joining OVH since we're
looking at candidates just like you. However, here's your reward:

INSA18c21e583eb590c14278068989349ac8
mitsurugi@dojo:~/chall/inshack/mails/test$
```

## Victory
Sometime, in CTF, just trust your instinct, and work hard 'til the last second. Those 5 minutes
left were just enough to get 250 points more.
Nice recruitment strategy, OVH :)

/* 0xMitsurugi quote */
Let's end this... once and for all!
~Soulcalibur III - Mitsurugi


