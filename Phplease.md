## Phplease

As usual we got an IP address for a Web site. The Web site shows a message:

action should be any of {source,dirlist,getflag}

`http://HOST?action=source` shows the PHP source of the page.

It has some conditional logic to react to the action parameter.

The flag is output if `$hashed_key === hash("sha512", $key)`.
However, `$hashed_key` is defined at the top of the file.

How can we manipulate these variables?

In the code block for 'getflag' two variables are created that are never used:

    $parsed = parse_url($_SERVER["REQUEST_URI"]);
    $parsed_query = parse_str($parsed["query"]);

The documentation for `parse_url` and `parse_str` tells us that `parse_str`
with only one parameter creates variables in global scope.

This means if we pass query parameters 'key' and 'hashed_key' we can take
control over the condition.

First, encode a random text:

    HASH=$(echo -n foo | sha512sum)

Sending a request with the path `?action=getflag&hashed_key=$HASH&key=foo` returns the flag:

    DORF18{0H_PHP_WHY_4R3_Y0U_50_BR0K3N}

