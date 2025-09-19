REMOVE \ ESCAPE CHARACTERS BEFORE CRACKING!!!
## Usage
3 MAIN TOOLS
```
crackstation -- NTLMs GO HERE FIRST
john
hashcat
```

Basic Usage
```bash
# basic john format
john <file> --wordlist=/usr/share/wordlists/rockyou.txt --rules=best64

# basic hashcat usage
1. # IDENTIFY HASH
hashcat hash.txt /usr/share/wordlists/rockyou.txt

2. # RUN
hashcat -m <value> hash.txt /usr/share/wordlists/rockyou.txt --rules-file /usr/share/hashcat/rules/best64.rule
```

Premade wordlists to try
```
/usr/share/wordlists/rockyou.txt
/usr/share/wordlists/fasttrack.txt
```

## Show Previously Cracked Hashes
```bash
# Hashcat
hashcat -m 1000 hash.file /usr/share/wordlists/rockyou.txt --show

# John
john hash.txt --show
```

## Cracking Protected Files
<>2john can convert database passwords to hashes that can be cracked

https://github.com/willstruggle/john/blob/master/
	Master file for all <>2john 

```bash
# locate all 2john binaries
locate *2john*

# examples
ssh2john SSH.private > ssh.hash   # ssh
office2john protected.docx > protected-docx.hash   # documents
pdf2john.py PDF.pdf > pdf.hash    # pdf
keepass2john    # keepass
keystore2john    # keystore

etc...

# crack
john --wordlist=rockyou.txt <file>.hash
john <file>.hash --show
```


## Cracking Protected Archives

Cracking
```bash
# crack ZIP files
zip2john

# crack OpenSSL encrypted GZIP files
for i in $(cat rockyou.txt);do openssl enc -aes-256-cbc -d -in GZIP.gzip -k $i 2>/dev/null| tar xz;done   # once done, check working directory for extracted file

# crack BitLocker-encrypted drives
bitlocker2john -i Backup.vhd > backup.hashes
grep "bitlocker\$0" backup.hashes > backup.hash
cat backup.hash
hashcat -a 0 -m 22100 '<HASH>' /usr/share/wordlists/rockyou.txt
```

Mount BitLocker Encrypted Drive
```bash
# Windows
double-click .vhd file, enter pass

# Linux
sudo apt-get install dislocker
sudo mkdir -p /media/bitlocker
sudo mkdir -p /media/bitlockermount
sudo losetup -f -P Backup.vhd      # Configure VHD as loop device
sudo dislocker /dev/loop0p2 -u1234qwer -- /media/bitlocker    # Decrypt
sudo mount -o loop /media/bitlocker/dislocker-file /media/bitlockermount  # Mount
cd /media/bitlockermount/  # Browse
...
sudo umount /media/bitlockermount   # Unmount when done
sudo umount /media/bitlocker        # Unmount when done

```

## Identify Hashes
```bash
hash-identifier
	<hash>
```

```bash
# NOTE: john/hashcat SHOULD autoidentify the hash but sometimes they do not
take the FIRST $<value> and grep it through hashcat -h to find the -m value to use

example:
	hashcat -h | grep '$2'
		3200 | bcrypt $2*$, Blowfish
```



## Cracking with Salts
```bash
# Download binary
wget https://www.techsolvency.com/pub/bin/mdxfind/mdxfind.static -O mdxfind
chmod +x mdxfind 


# Usage
try default value if salt unknown, otherwise input salt
	echo "YOUR_SALT_HERE" > salt.txt
	
any known passwords mdxfind can use as reference?
	echo "known_password" > pass.txt 

now try and determine hash (iterations set to 5)
	echo "a2b4e80cd640aaa6e417febe095dcbfc" | ./mdxfind -h 'MD5' -s salt.txt pass.txt -i 5
	the results of this will show us how many iterations we actually need

final crack
	echo "844ffc2c7150b93c4133a6ff2e1a2dba" | ./mdxfind -h 'MD5PASSSALT' -s salt.txt /usr/sharewordlists/rockyou.txt -i <iterations we need>

```

## Cracking Cisco Hashes

Cisco hashes
```bash
enable secret 5 $1$pdQG$o8nrSzsGXeaduXrjlvKc91 # Cisco Type 5 salted md5

username rout3r password 7 0242114B0E143F015F5D1E161713 # Cisco Type 7 Custom, reversible

username admin privilege 15 password 7 02375012182C1A1D751618034F36415408 # Cisco Type 7 Custom, reversible
```

Type 5
	Use `john` to crack

Type 7
	Use this [Cisco Type 7 Cracker](https://www.firewall.cx/cisco/cisco-routers/cisco-type7-password-crack.html)

