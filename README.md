# SSH-Windows
SSH your Windows and extend its functionality!
This is an alternative of "Windows Subsystem for Linux" which is featured in Windows 10 but in my opinion this is a lot more powerful. Make your Windows the OS that it was supposed to be! Don't run Linux as a second system, integrate it as a service!

# SSH your Windows and extend its functionality
### Introduction
This is a tutorial which I was inspired to write after I couldn’t find any recent information on how to setup a CYGWIN environment on a Windows machine. This will extend the Windows operating system functionality to a level that you can use Linux commands in your console. You can run KDE or GNOME graphic user interface within your Windows. You can also run X11 Unix applications on the host machine or even remotely. The main advantage of extending your Windows functionality should be the ability to use a secured remote access to your system using SSH and VNC tunnels. This is a cross-platform solution which is compatible with the majority Operating Systems such as Linux, Mac OSX and Windows. 
Currently this method is tested on Windows 7 but it should be compatible with any other Windows systems.

Pay attention that if you don’t know what you are doing this could lead to security vulnerabilities and I am in no way responsible. Use what you learn at your own risk! 
In this matter I would suggest trying it first on a virtual machine.

I’m going to create a Virtual Machine using VMware and install a clean copy of Windows 7 which will be the remote machine. This is not necessary in case that you want to use your physical machine and make your Windows a lot more functional by adding most of the Linux tools to it. This setup will give you the opportunity to start Linux applications within your Windows system. You can also use most of the Linux terminal commands from your Windows command prompt. You can connect to your machine remotely from any point of the world over a secured SSH connection which is the main point of doing all of this. You can forward X11 Linux application directly to your host machine and fully interact with your Windows file system and Unix shell. You can establish a remote VNC desktop sharing connection by forwarding the right ports in PuTTY. All the applications used are cross-platform. In Mac OSX it is all included natively (SSH, Sharing Screen, Port Forwarding). If you are not happy with the performance of the remote VNC desktop connection, you can start a Linux graphical environment and Forward X11 server to your host machine. This way you can have a nice graphic user interface (GUI) when interacting with your main file system. In addition to all of that, you can use remotely a fully featured command line tools and control every process within your remote machine. You can also connect to your remote computer with an FTP applications like FileZilla. Pretty cool, isn’t it? Let’s have a look and see how this could be done!

### Setting up the environment
  - Installing Windows and configuring the environment
  
![Win7Install](/Pictures/WinInstall.png "Windows 7 Installation")

I am going to use a Virtual Machine for a remote client as I mentioned at the beginning of this document. I assume that you have a full working knowledge of Microsoft Windows so I am going to explain just the most important settings that should be made. 
I will set my default web browser to be Google Chrome. Next I will disable Windows Firewall and set my IP address locally. This way I will be able to ping the virtual machine from the host OS. 
From Control Panel change the category from the top right corner to small icons and in the end of the first column is the Windows Firewall. Open Windows Firewall and then on the left side you will see “Turn Windows Firewall on or off”. Go there and turn it off.

![firewall](/Pictures/firewall.png "Firewall")

After this is done go to your network settings and set your network adapter manually.

![network settings](/Pictures/ip.png "Network")

You can set the IP address that corresponds to your local network. If you want to enable internet you can specify your DNS servers. Next step is to set the network adapter in the VMware settings. Go to the VM tab and select Settings like on the picture below.

![VMnetwork](/Pictures/VMnet.png "VMware network")

Set the network connection to be “Bridged: Connected directly to the physical network.” and click OK.
On the popup window, you can choose that this is a Home Network.
You can now try to ping from the command prompt both, the host and the virtual machine, which is on the same local network to ensure that there is a connectivity between them. 

![cmd](/Pictures/cmd.png "command prompt")

At this point our Operating System is ready!

### Set of tools

Let me introduce you the Toolbox!
I have prepared the full package of applications needed to save your time but if you wish you can download all the apps from their official sources!

![Tools](/Pictures/Tools.png "Tools")

This is all you need to start with! You can download it from my tutorial or the official websites but please stick to those versions. One more thing that you will need to install is Cygwin which will give you a similar functionality to a Linux distribution on your Windows. Cygwin’s DLL is running on all current Windows versions so we are not limited to use Windows 7. 

http://www.cygwin.com/

Choose the appropriate version of Cygwin for your Windows. I will download to my remote machine the “setup-x86_64.exe” file. On the remote machine which is the Virtual VMware machine you will need the first row of files which I am providing and this is: Disable UAC, HideUserOnWelcomeScreen, nircmd, nircmd-x64, PSTools and Cygwin.On the host machine, you will need all the apps from the second row: PuTTY, TightVNC, Xming, Xming-fonts.
So, let’s start by installing the Cygwin and select the installation packages from the repository.
__Click the right button on the installation file and click “Run as Administrator”.__

![Cygwin](/Pictures/cyg.png "Cygwin")

Click next and on the next step you can specify the path for the installation. I will leave it default to C:\cygwin64
Click next on the next two steps and select a download site from the list. It will initialize a list of packages and then let’s select what we would need.

![cygSettings](/Pictures/Screenshot_1.png "Settings")

In the Search box write gcc-core and under the Devel menu select gcc-core: GNU Compiler Collection. To install this package click on the most left icon and it will change to the number of the version that we will install.
Next package is “make”. It is also under the Devel menu. 
make: The GNU version of the ‘make’ utility.
Next is OpenSSL. Install all the three tools under the Net dropdown and go to Libs and select libopenssl100 as well.
OpenSSH goes next. You can install all the packages.
Now search for SSH. Under Debug you can add ssh-pageant-debuginfo. Go to KDE and select the package and do the same for X11. After that in Net you can select the package ssh-pageant.
We will need a nice text editor so lets search for ‘nano’ and ‘vim’. Install it from Editors. Install vim-common and vim as well.
__Install lynx, wget, rsync and curl from Net.__
For archiving install bzip and tar.
Next from the list are optional: python, python3, *bash-completion, tmux, git, diffutils.
If we plan to start a graphic environment or X11 apps we should add everything from the KDE tab excluding the unnecessary translation packages.
We are ready to go! Just hit next and start downloading and installing.

- Don’t install fzf-bash-completion

| Tools | KDE Tab | Lib tab |
| ------ | ------ | ------- |
| gcc-core | kde-baseapps | kf5-kded -> KDE 5 daemon
| make  (The GNU version of "make" utility) | kde-cli-tools | libkdecore5 -> KDE4 app framework runtime |
| openssl | kde-gtk-config |
| libopenssl | kde-runtime |
| openssh | kde-workspace |
| vim, vim-common | kdedebugsettings |
| lynx, wget, curl, rsync | kdeedu-data |
| python, python3 | kdepasswd |
| bash-completion | kdeplasma-addons |
| tmux | kdesdk-kioslaves |
| git | kdesdk-thumbnailers |
| rsync | kdevplatform |
| __konqueror__ | oxygen-kde4 |


### Setting up the Cygwin environmnet

After the installation has completed we can set up our Cygwin from the desktop shortcut. 
Start the application as administrator!
We are now going to set up our SSH host and client configurations by executing the following commands in the Unix terminal:

$ __ssh-host-config__

“Should StrictModes be used” > yes

“New local accout ‘sshd’?” > yes

“Do you want to install sshd as a service” > yes 

“CYGWIN for daemon” > ntsec

“Do you want to use different name” > no

(so we will use the default cyg_server)

“Create new privileged user account” > yes

Password > ******** (type something for a password and remember it)


We are done here! You can check the screenshot below to verify your inputs.
Now we are going to configure the user by typing __ssh-user-config__.

$ __ssh-user-config__

Create SSH2 RSA identity file > yes

type a password *****

Select ‘yes’ when prompted for connecting this machine with this identity.

SSH2 DSA identity > yes

type a password *****

Use this identity > yes

Do the same for ECDSA algorithm!

SSH1 RSA identity > no


![ssh-config](/Pictures/ssh-host.png "ssh")

Very important step before we continue is to execute the following command:
__$ net start sshd__  (to start the service).
Now that we have everything set up in Cygwin, in order to make our SSH connection work on Windows we need to perform a few more crucial steps in configuring the Windows security policy.
Within the Cygwin bash we can write > __$ ssh -v localhost__

On the prompts that follows, we answer with ‘yes’ and enter the password.

Now we started the ssh locally but if we try to start it remotely from our host machine we will get an error message with connection refused.
To prevent this, we go to Control Panel, Users and select Change User Account Control settings __(UAC)__. We set the bar to the minimum protection. (This could be just a temporary setting. We do this because the UAC policy is preventing remote connections to be done). This is the best time to run my registry file __“Disable UAC”__. 
Now we can go to our host computer and install PuTTY. After the installation is done we can open it and type our IP address of the remote machine with the SSH configuration.

![Putty](/Pictures/putty.png)

We can click Open and establish a connection. For the security authentication we can select ‘yes’. Our username now is cyg_server and the password we specified. Whatever you try to attempt will always result in “Access Denied” so don’t be upset. Your password is right but there are a few more things that needs to be done…
On the remote machine open Cygwin bash terminal and write these two commands just to be sure that they are not causing troubles in future:

__$ mkpasswd.exe -l > /etc/passwd__

__$ mkgroup --local > /etc/group__

Now run > __$ cyglsa-config__ (This is optional Local Security Authority)
Okay, let’s move on!
Click the Windows Start button and select run (hold start + r). Type there __gpedit.msc__

Now read careful! 

Go to 
- Computer Configuration 
  - Windows Settings 
    - Security Policies 
      - Local Policies 
        - User Rights Assignment = __Deny log on locally__ > delete our ssh account here (cyg_server)

You can also delete the user account name from “Deny log on through remote desktop services” as well. However, it doesn’t affect much our login.

Okay get out of there quickly!
We can now go to Control Panel and go to Users > __Manage Accounts__ and we will see our User Account “cyg_server” appearing there and we don’t want this. On the next restart of the remote machine we will be held on the Windows Log on Welcome screen to choose which username we are going to use. Most probably we don’t want this as well. 
We can run Microsoft’s netplwiz application to set our main user to login automatically but this is pretty unprofessional.
Now is the time to run the regkey that I provided in my Toolbox which is “HideUserOnWelcomeScreen”. Run that or write it manually! It will hide the username cyg_server and we won’t see it again, but we will be able to still use it in our remote login!

After all of this is done… We can go back to the host machine and run PuTTY again! Type the IP address 192.168.2.50 on port 22. Type username cyg_server and the password.

__CONGRATULATIONS!__

If you performed everything step by step it should have brought you here!

Now we got a great SSH remote connection!

![success](/Pictures/success.png)

Keep reading and I will show you how to make SSH Tunnels with PuTTY and how to establish a remote VNC connection. You will learn how to execute commands from our Unix terminal on the remote machine, how to start and close system processes with GUI or without a GUI. I will teach you how to control the remote system sound levels with just a console and much more. 

### Bonus to this chapter

In a real situation, we would definitely want our remote machine to keep its Firewall on. In this matter go to Control Panel -> Windows Firewall and fully activate it. While you are still in Windows Firewall, on the left side you will see Advanced Settings. Go there and it will open a new window. Select Inbound Rules and from the right side of the window choose New Rule… Select Port and click next. For specific ports write 21-23 and click next. Allow the connection and go next. Click next again and on the last window type a name SSH and description CYGWIN.
If you want to have a terminal package manager so you can install any future apps with an ease, follow the instructions bellow!

```
cd /bin
curl https://github.com/git/git/raw/master/contrib/completion/git-completion.bash -OL
source git-completion.bash

lynx -source rawgit.com/transcode-open/apt-cyg/master/apt-cyg > apt-cyg
install apt-cyg /bin
apt-cyg install nano

vim ~/.bashrc
alias desktop="cd C:/Users/$USER/Desktop"
alias open='cygstart'
alias reload='source ~/.bash_profile'
export PATH="${HOME}/bin:${PATH}"
```

# Uninstall Instructions

If for some reason you want to uninstall you can do the following:

```
* Remove the sshd service using cmd

cygrunsrv --stop sshd
cygrunsrv --remove sshd

* Delete the created users such as sshd and cyg_server from the system using cmd

net user sshd /delete
net user cyg_server /delete
```

# Chapter 2
## Establishing a secured VNC connection

In this chapter I will show you how to configure a remote VNC connection using an SSH tunnel from the previous chapter. Using this method, the whole traffic will be securely encrypted.

First thing is to install the application TightVNC. You can download it from the official website or you can also get it from the Install Packages from my repository. 
When you begin installing the application you can select custom installation and install the server package. Only this package is needed on the machine that will be accessible remotely. The client version of the program should be installed on the host machine. 
After you finish installing the server side of the application a pop-up window will appear. You need to enter two passwords. One for the remote connection and one for the local configuration of the program.

![1](/Pictures/chapters/1.jpg "1")

After this is done you can see the application in the Tray icons. 
Left click on it and open it!

![3](/Pictures/chapters/2.jpg "2")

In this window we can see the port which is used for incoming connections. We are going to use it later. We can also disable the Web Access and optionally remove the Tray icon from Miscellaneous if we don’t want to see it in future.
On the Access Control tab, we should enable “Allow Loopback Connections”.
Apply the changes and close the window.

Let’s move to the host machine!
On the host machine we need to install the client package of TightVNC. Next step will be to start the application PuTTY and configure an SSH Tunnel. 

![3](/Pictures/chapters/3.jpg "3")

In PuTTY let’s type the IP address of the remote machine. You can type a name of this PuTTY configuration under the Saved Sessions field and click on the Save button. After this is done, on the left panel expand the Connection and then expand SSH. Scroll down until you see Tunnels. Select the two checkboxes under Port Forwarding. After that on the Source port type 5900 and for destination type your remote machine address for example 192.168.2.50:5900 and hit the Add button.
Your window should look like this now:

![4](/Pictures/chapters/4.jpg "4")

Now go back to the first window. That was the Session tab. Then click on the profile that we created earlier and save it again to reflect the changes for the Tunnels. Now click Open and enter your credentials for the remote machine. 
Your window should like this:
 
![5](/Pictures/chapters/5.jpg "5")

You can minimize this window on the background as we are going to start the TightVNC application now. (tvnviewer)

![6](/Pictures/chapters/6.jpg "6")

Your window should look like this! 
For Remote Host enter “localhost:5900” and click connect. You will be required to enter the password that you entered after the installation of TightVNC.

![7](/Pictures/chapters/7.jpg "7")

Congratulations! Now you can access your machine remotely with a secured connection! Read more to understand what else you can do!
*Note: If you want to access your computer outside of your home network you should go to your router settings and set a static IP address to this computer and then forward the port 22 to its address. This is the SSH port which you are using with PuTTY. If you also forward port 5900 in the router, your traffic won’t be tunneled and it won’t be encrypted. However, once you forward the ports, you should use your real IP address from your ISP followed by the port 0.0.0.0:22


# Chapter 3
## Extend Windows functionality and run applications remotely using a terminal

Let’s locally extend our Windows functionality. This will let us execute Linux commands from the command prompt (cmd). In order to do that, we need to specify the path to the Cygwin’s bin directory in Windows “Environment Variables”. If you don’t know how to get there you can open Run from the start menu and paste this __“rundll32 sysdm.cpl,EditEnvironmentVariables”__. 
Another way is: 

1.	Right click 'My Computer' and select 'Properties'.
2.	Click 'Advanced System Settings' link.
3.	Click 'Advanced' tab.
4.	Click 'Environment Variables...' button.

Your window should look like this:

![8](/Pictures/chapters/8.jpg "8")

If you are not on Windows 10 you should separate each path with a semicolon. Find the Path variable and add to it your Cygwin installation path. 

Mine looks like this: __C:\cygwin64\bin__

After you do that click OK on each window and then open cmd. You should now be able to type commands like __ls, git, even ssh__. I will show you on the next chapter how to run X11 Linux applications with GUI in your Windows.

![9](/Pictures/chapters/9.jpg "9")

If you intend to connect to your computer remotely and you also want to have the full power to control system processes just by a command line you will need to install PSTools. I have included it in my packages. You just need to extract the files in your C:\cygwin64\bin folder and you are good to go. There is another program in my packages and it is called Nircmd. It needs to be placed in the Windows directory and you can control the system sound levels from the console, turn off the monitor and etc. Let’s see how we can use this!
Start PuTTY and connect to your remote machine. The first time you use the PSTools you will need to accept the eula. For example, type PsList -accepteula in the PuTTY terminal and it will show you the running processes on the remote machine. To start a process on the remote machine type:

PsExec \\\\127.0.0.1 -u cyg_server –p [password] -i -d cmd -accepteula

![10](/Pictures/chapters/10.jpg "10")

The -d attribute indicates that the client won't have to wait until notepad's end of execution.

The -i attribute allows the application to be launched on the target's desktop.

You can also start a batch file like this: PsExec \\\\127.0.0.1 -u cyg_server -p [password] -i -d cmd /c C:\\something.bat

I will add below a list of commands you can test.
$ PsExec \\\\127.0.0.1 -u cyg_server -p (password) cmd /c nircmd changesysvolume +30000

$ PsExec \\\\127.0.0.1 -u cyg_server -p (password) -i -d cmd /c nircmd mutesysvolume 0          (0 - mute) (1 - unmute) (2 - switch)

Next command takes the input text from the txt file and outputs it into a wav file.

$ PsExec \\\\127.0.0.1 -u cyg_server -p 0887313660 cmd /c nircmd speak file "c:\temp\speak.txt" 0 100 "c:\temp\speak.wav" 48kHz16BitStereo

This command outputs the speech through the computer's speakers:

$ PsExec \\\\127.0.0.1 -u cyg_server -p (password) -i -d cmd /c nircmd speak file "c:\temp\speak.txt" 0 100

The following style will execute invisibly with no traces!

$ PsExec \\\\127.0.0.1 -u cyg_server -p (password) -i -d nircmd speak file "c:\temp\speak.txt" 0 100

$ PsExec \\\\127.0.0.1 -u cyg_server -p (password) -i -d nircmd monitor off

I guess this is enough for a start. In the next chapter I will show you how to start X11 graphic applications and how to forward them remotely. Lastly, I will show you how to configure a fancy Unix Terminal running zsh natively on your Windows. I guess that’s what most developers on Windows are missing, a beautiful terminal solving their troubles with NPM and other tools like git.

![11](/Pictures/chapters/11.jpg "11")
