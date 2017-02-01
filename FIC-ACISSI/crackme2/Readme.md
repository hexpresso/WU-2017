#Reverse : crackme2

Un crackme relativement intéressant à casser. J'ai essayé d'être didactique dans la résolution, quitte à être un peu verbeux.

## Découverte du binaire
Avant de lancer un debugger, il est nécessaire d'observer le binaire
```
mitsurugi@dojo:~/chall/FIC$ ls -l crackme2
-rwxr-xr-x 1 mitsurugi mitsurugi 1080 janv. 24 13:00 crackme2
mitsurugi@dojo:~/chall/FIC$ file crackme2
crackme2: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), statically linked, stripped
mitsurugi@dojo:~/chall/FIC$ readelf -h crackme2
En-tête ELF:
  Magique:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  Classe:                            ELF32
  Données:                          complément à 2, système à octets de poids faible d'abord (little endian)
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  Version ABI:                       0
  Type:                              EXEC (fichier exécutable)
  Machine:                           Intel 80386
  Version:                           0x1
  Adresse du point d'entrée:         0x8048229
  Début des en-têtes de programme :  52 (octets dans le fichier)
  Début des en-têtes de section :    880 (octets dans le fichier)
  Fanions:                           0x0
  Taille de cet en-tête:             52 (octets)
  Taille de l'en-tête du programme:  32 (octets)
  Nombre d'en-tête du programme:     2
  Taille des en-têtes de section:    40 (octets)
  Nombre d'en-têtes de section:      5
  Table d'indexes des chaînes d'en-tête de section: 4
mitsurugi@dojo:~/chall/FIC$ objdump -d crackme2

crackme2:     format de fichier elf32-i386


Déassemblage de la section .text :

08048080 <.text>:
 8048080:	31 c0					xor    %eax,%eax
 8048082:	31 db					xor    %ebx,%ebx
 8048084:	b9 3f 82 04 08			mov    $0x804823f,%ecx
 8048089:	81 e9 80 80 04 08		sub    $0x8048080,%ecx
 804808f:	be 80 80 04 08			mov    $0x8048080,%esi
 8048094:	ac						lods   %ds:(%esi),%al
 8048095:	01 c3					add    %eax,%ebx
 8048097:	e2 fb					loop   0x8048094
 8048099:	66 3b 1d 3b 93 04 08	cmp    0x804933b,%bx
 80480a0:	74 05					je     0x80480a7
 80480a2:	e9 61 01 00 00			jmp    0x8048208
 80480a7:	b8 54 93 04 08			mov    $0x8049354,%eax
 80480ac:	31 db					xor    %ebx,%ebx
 80480ae:	31 d2					xor    %edx,%edx
 80480b0:	66 89 1d 76 93 04 08	mov    %bx,0x8049376
 80480b7:	8b 1c 10				mov    (%eax,%edx,1),%ebx
 80480ba:	80 fb 0a				cmp    $0xa,%bl
 80480bd:	74 03					je     0x80480c2
 80480bf:	42						inc    %edx
 80480c0:	eb f5					jmp    0x80480b7
 80480c2:	66 89 15 76 93 04 08	mov    %dx,0x8049376
 80480c9:	66 39 15 3d 93 04 08	cmp    %dx,0x804933d
 80480d0:	0f 85 10 01 00 00		jne    0x80481e6
 80480d6:	31 c0					xor    %eax,%eax
 80480d8:	31 db					xor    %ebx,%ebx
 80480da:	31 c9					xor    %ecx,%ecx
 80480dc:	31 d2					xor    %edx,%edx
 80480de:	31 ff					xor    %edi,%edi
 80480e0:	bf 54 93 04 08			mov    $0x8049354,%edi
 80480e5:	be 47 93 04 08			mov    $0x8049347,%esi
 80480ea:	8a 04 17				mov    (%edi,%edx,1),%al
 80480ed:	8a 4c 17 01				mov    0x1(%edi,%edx,1),%cl
 80480f1:	8a 1c 16				mov    (%esi,%edx,1),%bl
 80480f4:	30 c8					xor    %cl,%al
 80480f6:	38 d8					cmp    %bl,%al
 80480f8:	0f 85 e8 00 00 00		jne    0x80481e6
 80480fe:	42						inc    %edx
 80480ff:	42						inc    %edx
 8048100:	66 3b 15 3d 93 04 08	cmp    0x804933d,%dx
 8048107:	7c e1					jl     0x80480ea
 8048109:	31 c0					xor    %eax,%eax
 804810b:	31 db					xor    %ebx,%ebx
 804810d:	31 c9					xor    %ecx,%ecx
 804810f:	31 d2					xor    %edx,%edx
 8048111:	b8 54 93 04 08			mov    $0x8049354,%eax
 8048116:	31 db					xor    %ebx,%ebx
 8048118:	8a 1c 10				mov    (%eax,%edx,1),%bl
 804811b:	01 d9					add    %ebx,%ecx
 804811d:	42						inc    %edx
 804811e:	66 3b 15 3d 93 04 08	cmp    0x804933d,%dx
 8048125:	7c ef					jl     0x8048116
 8048127:	3b 0d 3f 93 04 08		cmp    0x804933f,%ecx
 804812d:	0f 85 b3 00 00 00		jne    0x80481e6
 8048133:	31 c0					xor    %eax,%eax
 8048135:	31 db					xor    %ebx,%ebx
 8048137:	31 c9					xor    %ecx,%ecx
 8048139:	31 d2					xor    %edx,%edx
 804813b:	b8 54 93 04 08			mov    $0x8049354,%eax
 8048140:	8b 1c 10				mov    (%eax,%edx,1),%ebx
 8048143:	81 e3 ff 00 00 00		and    $0xff,%ebx
 8048149:	c1 eb 04				shr    $0x4,%ebx
 804814c:	01 d9					add    %ebx,%ecx
 804814e:	42						inc    %edx
 804814f:	66 3b 15 3d 93 04 08	cmp    0x804933d,%dx
 8048156:	7c e8					jl     0x8048140
 8048158:	3b 0d 43 93 04 08		cmp    0x8049343,%ecx
 804815e:	0f 85 82 00 00 00		jne    0x80481e6
 8048164:	31 c0					xor    %eax,%eax
 8048166:	31 db					xor    %ebx,%ebx
 8048168:	31 c9					xor    %ecx,%ecx
 804816a:	31 d2					xor    %edx,%edx
 804816c:	b8 54 93 04 08			mov    $0x8049354,%eax
 8048171:	bf 4f 93 04 08			mov    $0x804934f,%edi
 8048176:	8a 1c 50				mov    (%eax,%edx,2),%bl
 8048179:	8a 0c 17				mov    (%edi,%edx,1),%cl
 804817c:	42						inc    %edx
 804817d:	38 cb					cmp    %cl,%bl
 804817f:	75 65					jne    0x80481e6
 8048181:	66 83 fa 05				cmp    $0x5,%dx
 8048185:	7c ef					jl     0x8048176
 8048187:	eb 4c					jmp    0x80481d5
 8048189:	b8 30 00 00 00			mov    $0x30,%eax
 804818e:	bb 05 00 00 00			mov    $0x5,%ebx
 8048193:	b9 80 80 04 08			mov    $0x8048080,%ecx
 8048198:	cd 80					int    $0x80
 804819a:	c3						ret    
 804819b:	31 c0					xor    %eax,%eax
 804819d:	83 c0 03				add    $0x3,%eax
 80481a0:	31 db					xor    %ebx,%ebx
 80481a2:	31 d2					xor    %edx,%edx
 80481a4:	83 c2 1e				add    $0x1e,%edx
 80481a7:	b9 54 93 04 08			mov    $0x8049354,%ecx
 80481ac:	cd 80					int    $0x80
 80481ae:	31 c0					xor    %eax,%eax
 80481b0:	a0 38 82 04 08			mov    0x8048238,%al
 80481b5:	fe c0					inc    %al
 80481b7:	a2 38 82 04 08			mov    %al,0x8048238
 80481bc:	c3						ret    
 80481bd:	31 c0					xor    %eax,%eax
 80481bf:	31 db					xor    %ebx,%ebx
 80481c1:	83 c0 04				add    $0x4,%eax
 80481c4:	83 c3 01				add    $0x1,%ebx
 80481c7:	cd 80					int    $0x80
 80481c9:	c3						ret    
 80481ca:	70 eb					jo     0x80481b7
 80481cc:	81 31 c0 31 db 40		xorl   $0x40db31c0,(%ecx)
 80481d2:	cd 80					int    $0x80
 80481d4:	70 b9					jo     0x804818f
 80481d6:	db 92 04 08 ba 30		fistl  0x30ba0804(%edx)
 80481dc:	00 00					add    %al,(%eax)
 80481de:	00 e8					add    %ch,%al
 80481e0:	d9 ff					fcos   
 80481e2:	ff						(bad)  
 80481e3:	ff						(bad)  
 80481e4:	eb e7					jmp    0x80481cd
 80481e6:	b9 7d 92 04 08			mov    $0x804927d,%ecx
 80481eb:	ba 30 00 00 00			mov    $0x30,%edx
 80481f0:	e8 c8 ff ff ff			call   0x80481bd
 80481f5:	eb d6					jmp    0x80481cd
 80481f7:	b9 ad 92 04 08			mov    $0x80492ad,%ecx
 80481fc:	ba 2e 00 00 00			mov    $0x2e,%edx
 8048201:	e8 b7 ff ff ff			call   0x80481bd
 8048206:	eb c5					jmp    0x80481cd
 8048208:	b9 0b 93 04 08			mov    $0x804930b,%ecx
 804820d:	ba 30 00 00 00			mov    $0x30,%edx
 8048212:	e8 a6 ff ff ff			call   0x80481bd
 8048217:	eb b4					jmp    0x80481cd
 8048219:	b9 40 92 04 08			mov    $0x8049240,%ecx
 804821e:	ba 3d 00 00 00			mov    $0x3d,%edx
 8048223:	e8 95 ff ff ff			call   0x80481bd
 8048228:	c3						ret    
 8048229:	e8 eb ff ff ff			call   0x8048219
 804822e:	e8 68 ff ff ff			call   0x804819b
 8048233:	e8 51 ff ff ff			call   0x8048189
 8048238:	cb						lret   
 8048239:	00 e8					add    %ch,%al
 804823b:	b8						.byte 0xb8
 804823c:	ff						(bad)  
 804823d:	ff						(bad)  
 804823e:	ff						.byte 0xff
mitsurugi@dojo:~/chall/FIC$
```

Le fichier est vraiment très petit (1080 octets), compilé en statique (eh!).
La fin d'objdump montre des instructions (bad), le programme s'automodifie
sans doute, ou jump au milieu d'une instruction. Nous gagnerons donc à le debugger
en dynamique.

Observons les chaines de caractères, et un lancement en live:
```
mitsurugi@dojo:~/chall/FIC$ strings crackme2
BBf;
*** Welcome in CrypT crackme ***
 Please tape a password :
*** Of course it's not the good pass ! ^^ ***
*** No no no ! Debug is not allowed =P  ***
*** Yeah !! This is the good flag, boy =) ***
*** Don't try to patch ! Just analyse =]  ***
ssAAHrCr
.shstrtab
.text
.data
.bss
mitsurugi@dojo:~/chall/FIC$ ./crackme2 

*** Welcome in CrypT crackme ***

 Please tape a password :The name's 0xMitsurugi

*** Of course it's not the good pass ! ^^ ***
mitsurugi@dojo:~/chall/FIC$
```

Le Debug semble être interdit, ainsi que le
patching du binaire, il doit y avoir un checksum réalisé quelque
part.

## Reverse, première étape
Fort de ces informations, je préfère m'attacher au binaire une
fois la demande de mot de passe faite plutôt que de lancer gdb
directement:
- si une vérification d'intégrité est faite au début, elle
fonctionnera
- si un antidebug est fait au début, ça le contourne
- si le code s'automodifie au lancement, j'aurai la version modifiée sans avoir
a reverser toute la partie décodage du binaire, gagnant ainsi du temps.

Fenêtre 1:
```
mitsurugi@dojo:~/chall/FIC$ ./crackme2 

*** Welcome in CrypT crackme ***

 Please tape a password :
```

Fenêtre 2:
```
mitsurugi@dojo:~/chall/FIC$ ps ax | grep crackme
 3812 pts/4    S+     0:00 ./crackme2
 3832 pts/5    S+     0:00 grep crackme
mitsurugi@dojo:~/chall/FIC$ gdb -q
gdb$ attach 3812
Attaching to process 3812
Reading symbols from /home/mitsurugi/chall/FIC/crackme2...(no debugging symbols found)...done.
--------------------------------------------------------------------------[regs]
  EAX: 0xFFFFFE00  EBX: 0x00000000  ECX: 0x08049354  EDX: 0x0000001E  o d I t s z a P c 
  ESI: 0x00000000  EDI: 0x00000000  EBP: 0x00000000  ESP: 0xFFB2373C  EIP: 0x080481AE
  CS: 0023  DS: 002B  ES: 002B  FS: 0000  GS: 0000  SS: 002B
--------------------------------------------------------------------------[code]
=> 0x80481ae:	xor    eax,eax
   0x80481b0:	mov    al,ds:0x8048238
   0x80481b5:	inc    al
   0x80481b7:	mov    ds:0x8048238,al
   0x80481bc:	ret    
   0x80481bd:	xor    eax,eax
   0x80481bf:	xor    ebx,ebx
   0x80481c1:	add    eax,0x4
--------------------------------------------------------------------------------
0x080481ae in ?? ()
gdb$
```

J'indique `1234ABCD` en mot de passe dans la première fenêtre, et gdb breake le programme comme
attendu:

```
gdb$ x/10i $eip
=> 0x80481ae:	xor    eax,eax
   0x80481b0:	mov    al,ds:0x8048238
   0x80481b5:	inc    al
   0x80481b7:	mov    ds:0x8048238,al
   0x80481bc:	ret    
(...)
```

Le suivi en pas à pas jusqu'au RET ne pose pas de difficultés particulières.
L'instruction suivant le RET est un CALL 0x8048189

## call   0x8048189
Ce call implémente un syscall ( http://syscalls.kernelgrok.com/ )
```
=> 0x8048189:	mov    eax,0x30
   0x804818e:	mov    ebx,0x5
   0x8048193:	mov    ecx,0x8048080
   0x8048198:	int    0x80
   0x804819a:	ret    
```

- EAX à 30 : SIGHANDLER
- EBX à 5 : le signal
- ECX : adresse de la fonction à appeler pour ce signal

```
gdb$ x/15i 0x8048080
   0x8048080:	xor    eax,eax
   0x8048082:	xor    ebx,ebx
   0x8048084:	mov    ecx,0x804823f
   0x8048089:	sub    ecx,0x8048080
   0x804808f:	mov    esi,0x8048080
   0x8048094:	lods   al,BYTE PTR ds:[esi]
   0x8048095:	add    ebx,eax
   0x8048097:	loop   0x8048094
   0x8048099:	cmp    bx,WORD PTR ds:0x804933b
   0x80480a0:	je     0x80480a7
   0x80480a2:	jmp    0x8048208
   0x80480a7:	mov    eax,0x8049354
```
On a une fonction qui semble additionner tous les octets en partant de 
l'adresse 0x8048080. On suppose qu'il s'agit de la vérification d'intégrité du
binaire. 

Nous comprenons donc mieux le message lié à l'antidebug et à l'antipatch:
Un INT3 doit être glissé dans le code, cet INT3 se fait trapper, une vérification
d'intégrité est faite. 

## Et l'INT3
L'instruction qui suit le RET de la fonction en 0x8048189 est un INT3 (what a surprise!)

Nous pouvons directement aller dans la fonction via un `set $eip=0x8048080`.
Comme nous le voyons au dessus, cette fonction semble juste vérifier si la
somme des octets du binaire correspond à une référence. En manipulant les registres
ou le jump, nous continuons à 0x80480a7.

## Reverse deuxième étape
La première instruction est un `0x80480a7:   mov    eax,0x8049354`
Et qu'avons nous à cette adresse:
```
gdb$ x/s $eax
0x8049354:	"1234ABCD\n"
gdb$
```
Les protections du binaire ont été coutournées, et il semble que la vérification
du mot de passe débute ici.
### Taille de la chaine
Les premières instructions comptent le nombre de caractères:

```
   0x80480a7:   mov    eax,0x8049354
=> 0x80480ac:	xor    ebx,ebx
   0x80480ae:	xor    edx,edx
   0x80480b0:	mov    WORD PTR ds:0x8049376,bx
   0x80480b7:	mov    ebx,DWORD PTR [eax+edx*1]     
   0x80480ba:	cmp    bl,0xa
   0x80480bd:	je     0x80480c2
   0x80480bf:	inc    edx
   0x80480c0:	jmp    0x80480b7
   0x80480c2:	mov    WORD PTR ds:0x8049376,dx
   0x80480c9:	cmp    WORD PTR ds:0x804933d,dx
   0x80480d0:	jne    0x80481e6
gdb$ x/x 0x804933d
0x804933d:	0x08
gdb$
```
Coup de hasard, le mot de passe semble faire 8 caractères, comme celui que j'ai entré.

### Contrainte 1
La suite du programme se déroule encore une fois:
```
=> 0x80480e0:	mov    edi,0x8049354
   0x80480e5:	mov    esi,0x8049347
   0x80480ea:	mov    al,BYTE PTR [edi+edx*1]
   0x80480ed:	mov    cl,BYTE PTR [edi+edx*1+0x1]
   0x80480f1:	mov    bl,BYTE PTR [esi+edx*1]
   0x80480f4:	xor    al,cl
   0x80480f6:	cmp    al,bl
   0x80480f8:	jne    0x80481e6
   0x80480fe:	inc    edx
   0x80480ff:	inc    edx
   0x8048100:	cmp    dx,WORD PTR ds:0x804933d
   0x8048107:	jl     0x80480ea
gdb$ x/s 0x8049354
0x8049354:	"1234ABCD\n"
gdb$ x/8x 0x8049347
0x8049347:	0x7c	0x7c	0x16	0x16	0x73	0x73	0x41	0x41
gdb$
```
Les caractères du mot de passe sont XORés deux à deux et comparés à une référence. Nous en déduisons pour un mot de passe noté p
une première série de contraintes:
- p[0] ^ p[1] = 0x7C
- p[2] ^ p[3] = 0x16
- p[4] ^ p[5] = 0x73
- p[6] ^ p[7] = 0x41


### Contrainte 2

La suite du programme continue par:
```
=> 0x8048109:	xor    eax,eax
   0x804810b:	xor    ebx,ebx
   0x804810d:	xor    ecx,ecx
   0x804810f:	xor    edx,edx
   0x8048111:	mov    eax,0x8049354
   0x8048116:	xor    ebx,ebx
   0x8048118:	mov    bl,BYTE PTR [eax+edx*1]
   0x804811b:	add    ecx,ebx
   0x804811d:	inc    edx
   0x804811e:	cmp    dx,WORD PTR ds:0x804933d
   0x8048125:	jl     0x8048116
   0x8048127:	cmp    ecx,DWORD PTR ds:0x804933f
   0x804812d:	jne    0x80481e6
```
Cette boucle additionne tous les octets et les compare avec une valeur en 0x804933f. Cette contrainte
donne peu d'informations, mais au cas ou:
```
gdb$ x/dwx 0x804933f
0x804933f:	0x0000026a
gdb$
```
### Contrainte 3
Cette contrainte ressemble beaucoup à la précédente:
```
=> 0x8048133:	xor    eax,eax
   0x8048135:	xor    ebx,ebx
   0x8048137:	xor    ecx,ecx
   0x8048139:	xor    edx,edx
   0x804813b:	mov    eax,0x8049354
   0x8048140:	mov    ebx,DWORD PTR [eax+edx*1]
   0x8048143:	and    ebx,0xff
   0x8048149:	shr    ebx,0x4
   0x804814c:	add    ecx,ebx
   0x804814e:	inc    edx
   0x804814f:	cmp    dx,WORD PTR ds:0x804933d
   0x8048156:	jl     0x8048140
   0x8048158:	cmp    ecx,DWORD PTR ds:0x8049343
   0x804815e:	jne    0x80481e6
gdb$ x/dwx 0x8049343
0x8049343:	0x00000025
gdb$
```
Seuls les 4 octets fort de chaque caractère du mot de passe sont additionnés, et leur somme doit faire 0x25

### Contrainte 4
Encore une boucle qui va ajouter des contraintes sur le mot de passe:
```
=> 0x8048164:	xor    eax,eax
   0x8048166:	xor    ebx,ebx
   0x8048168:	xor    ecx,ecx
   0x804816a:	xor    edx,edx
   0x804816c:	mov    eax,0x8049354
   0x8048171:	mov    edi,0x804934f
   0x8048176:	mov    bl,BYTE PTR [eax+edx*2]
   0x8048179:	mov    cl,BYTE PTR [edi+edx*1]
   0x804817c:	inc    edx
   0x804817d:	cmp    bl,cl
   0x804817f:	jne    0x80481e6
   0x8048181:	cmp    dx,0x5
   0x8048185:	jl     0x8048176
   0x8048187:	jmp    0x80481d5
gdb$ x/4x 0x804934f
0x804934f:	0x48	0x72	0x43	0x72
gdb$ x/s 0x8049354
0x8049354:	"1234ABCD\n"
gdb$
```
Alors que bl (du password) avance de 2 en 2, la vérification se fait consécutivement.
Nous pouvons donc ajouter en contrainte:
- p[0] = 0x48 = H
- p[2] = 0x72 = r
- p[4] = 0x43 = C
- p[6] = 0x72 = r

### Victory
En reprenant les contraintes données au début, nous obtenons rapidement:
- p[1] = p[0] ^ 0x7c = 0x34 = 4
- p[3] = p[2] ^ 0x16 = 0x64 = d
- p[5] = p[4] ^ 0x73 = 0x30 = 0
- p[7] = p[6] ^ 0x41 = 0x33 = 3

Et en recombinant, nous obtenons le flag: `H4rdC0r3`.
```
mitsurugi@dojo:~/chall/FIC$ ./crackme2 

*** Welcome in CrypT crackme ***

 Please tape a password :H4rdC0r3

*** Yeah !! This is the good flag, boy =) ***
mitsurugi@dojo:~/chall/FIC$
```

Bon challenge :)

