# **Work in Progress**

# How to use VS Code to remotely run and debug your Python applications.

As I have begun my journey with the Raspberry Pi and electronic circuits, I have encountered one very annoying problem.
My main Windows computer is a powerhouse, so I want to do all my coding on it for ease of use, but I need to code on my Raspberry Pi to use its GPIO interface.

As a quick, slapped together solution I have simply opened a file share on my Windows computer and manually copied updated scripts via SSH. This is obviously a very tedious process and I have finally reached the point where I decided I need a better solution.  

Initially I Googled around to figure out which IDEs supported running and debugging your code on a remote machine and was happy to find that many did so. Of note, I discovered that PyCharm, the IDE I have historically preferred for Python development, has such an offering but it is limited only to users who pay for a professional license. I had no desire to pay ~$90 a year just to get this functionality, so my search continued until I discovered VS Code's 'Remote - SSH' extension. This extension allows you to connect to a remote host via SSH and work with the files located on that machine, no local files are needed. Perhaps most important for me, this extension is *free* and will work with various languages! So let's run through how to get this environment all set up so you (and I!) can get back to working on our projects.  

## Getting Started
1. Obviously, we need to install VS Code. You can do so [here](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client).
2. VS Code requires you to install extensions to get Python support, so download that [here](https://marketplace.visualstudio.com/items?itemName=ms-python.python). (As an aside, make sure that you enable linting to get syntax corrections)
<!--- 3. Now that we have our IDE set up with Python enabled, we need to download the aforementioned Remote - SSH extension. Download and install  it from [this page](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh). --->
3. Go to VS Code settings, search for remote.SSH.showlogin and enable the option to always reveal te SSH login terminal.
4. Windows runs an outdated version of ssh-agent by default that makes it impossible to connect without first manually updating it. So, we must download the updated version from GitHub [here](https://github.com/PowerShell/Win32-OpenSSH/releases), extract that file, then run the install-sshd.ps1 PowerShell script. (I am seriously starting to hate Windows because of stuff like this)
5. Now it is time to set up a SSH connection. We must generate a new SSH key and copy it over to our remote host.
  - Run ```ssh-keygen -t rsa -b 4096``` and enter the name for your key and, optionally, a passphrase.
  - Next we will set up a couple of variables to make sending the public key to our Raspberry Pi a little bit easier. Enter ```$USER_AT_HOST="your-user-name-on-host@hostname"``` and ```$PUBKEYPATH="$HOME\.ssh\id_rsa.pub"``` with your respective username, hostname, and path to your public key.
  - Next we will send the public key to the remote host with the command ```Get-Content "$PUBKEYPATH" | Out-String | ssh $USER_AT_HOST "powershell `"New-Item -Force -ItemType Directory -Path `"`$HOME\.ssh`"; Add-Content -Force -Path `"`$HOME\.ssh\authorized_keys`" `""```
  - Now we must ensure that the Windows OpenSSH Authentication Agent service is running. Open an elevated Powershell window and run the command ```Set-Service ssh-agent -StartupType Automatic``` to enable the agent, run ```Start-Service ssh-agent``` to start it, then run ```Get-Service ssh-agent``` to double check that it started correctly.
  - Finally, add your public key to the SSH Agent by running ```ssh-add ~\.ssh\NAME_OF_YOUR_KEY```.
6. Now everything should be set up. Let's try connecting to our Raspberry Pi by clicking the little green connection button at the bottom left of the VS Code window and selecting the option to connect to a host.
7. Type in the details to connect to your host, in my case it is pi@raspi-zero-1 (username@hostname).

# Add stuff about Windows OpenSSH server and the ssh-agent service that needs to be installed. https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_keymanagement

# Credits
This guide is mostly pared down information from the official documentation for the Remote - SSH extension located [here](https://code.visualstudio.com/docs/remote/troubleshooting#_installing-a-supported-ssh-client) and [here](https://code.visualstudio.com/docs/remote/ssh-tutorial).
