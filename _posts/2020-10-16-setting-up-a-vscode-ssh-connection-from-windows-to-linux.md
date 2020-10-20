---
layout: post
title:  "Setting up a VSCode SSH Connection from Windows 10 to Linux"
date: 2020-10-16 12:00:00
categories: programming
---

I've been doing a lot of remote development in 2020. My preferred IDE is Visual Studio Code, and for the past year they've had a really great way to do remote development via Codespaces on Azure on your own server (aka my lab computer) that avoids SSH configuration stuff. It's pretty mind-blowing because you could do all of your development in the browser. Unfortunately, early next year they are scrapping Azure Codespaces for Github Codespaces and also getting rid of the ability to host a Codespace on your own server... So I had to switch to normal Visual Studio Code and use their Remote-SSH plugin to connect to my server. However, last year CSAIL made it possible to SSH into CSAIL-hosted computers only via a jump server. So that adds another hoop to jump through...

I've recorded the steps I went through for future reference. My "server", the Linux computer in my lab, is already set up with a static IP address so I didn't have to do anything there. But I had to set up my client (my Windows 10 laptop).

1. **Install Visual Studio:** If you're a student you can register for an Azure Education account, which gets you access to professional software. The Azure Education page is [here](https://portal.azure.com/#blade/Microsoft_Azure_Education/EducationMenuBlade/overview). Don't make the same mistake I made and install Visual Studio Enterprise... you want Visual Studio Code.
2. **Install OpenSSH:** that is, if you don't already have it on your computer (trying opening the Powershell and typing `which ssh`). If you don't, you can follow [these instructions](https://docs.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse).
3. **Set up Remote-SSH:** Using the [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery), install the Remote-SSH extension.
4. **Initliaze the SSH host:** Now that you have Remote-SSH, you can follow the "Getting Started" instructions [here](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh) to basically open the Command Pallette (`F1`), select the `Remote-SSH: Connect to Host...` command, and what I did is I gave it a flawed SSH command, `ssh -J jumpuser@jumphost serveruser@serverhost`. Importantly, you have to agree to create a `~/.ssh/config` file, and this will set up a flawed version of that file.
5. **Fix the .ssh/config file:** Open the `~/.ssh/config` file and replace the code in it with code like this:

        Host jump.csail.mit.edu
        User jumpuser

        Host serverhostname
        User serveruser
        
        ProxyCommand C:\Windows\System32\OpenSSH\ssh.exe -W %h:%p jump.csail.mit.edu

After this, I'm able to connect to my server via the jump host, and the only annoying this is that I have to enter two passwords (I should add SSH certificates so I at least can skip logging in to my server).
