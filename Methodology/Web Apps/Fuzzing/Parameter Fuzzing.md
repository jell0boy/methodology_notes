Sometimes we can fuzz for parameters that allow access to pages we normally wouldn't have. Similarly to how we have been fuzzing various parts of a website, we will use `ffuf` to enumerate parameters.

Wordlists to reference
```bash
/usr/share/seclists/Discovery/Web-Content/burp-parameter-names.txt
```
## GET Request Fuzzing
We can fuzz for `GET` requests which are usually passed right after the URL with a ?

GET Request Fuzzing
```bash
# Initial Fuzz
ffuf -w <wordlist>:FUZZ -u http://<server>:<port>/dir/admin.php?FUZZ=key 

# Filter out default response size
-fs xxx
```

## POST Request Fuzzing


## Value Fuzzing