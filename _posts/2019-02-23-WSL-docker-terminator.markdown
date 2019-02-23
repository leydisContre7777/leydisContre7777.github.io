---
layout: post
title:  "WSL+docker+terminator"
date:   2019-02-22 22:43:51
---

<h3>Windows 10 Home Edition + docker</h3>

Guess what?, you are a linux user and a developer (yes, you also use docker), but for project requirements you need to use windows, and for some reason (let’s say the destiny) you choose Windows 10 home edition version. 

So, you are on a big trouble, why?. Because docker doesn’t work in Windows 10 home, instead you can use docker toolbox, from my experience is something that  I wouldn’t recommend this because it doesn’t work properly. 

Don’t worry I have a solution for you, probably is not the best but you can move on with it. First, we are going to install the Windows Subsystem for Linux (WSL), which let us to use a linux distribution in windows, let’s try an ubuntu 18.04, you can follow the <a href="https://docs.microsoft.com/en-us/windows/wsl/install-win10">instructions</a>to install it here:

Once you’ve installed we need to install <a href="https://thenewstack.io/hyper-docker-done-right-way/">Hyper</a>, which let’s us to run the docker containers. But before please make sure that virtualization is enabled in your machine. Then, we are going to make something dirty, modifying the windows register and running two .bat files:

I came across this <a href="https://forums.docker.com/t/installing-docker-on-windows-10-home/11722/25
">forum</a>:

Create two .bat files that will execute as an administrator in the command line: 

File 1:
{% highlight html %}
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*Hyper-V*.mum >hyper-v.txt
for /f %%i in ('findstr /i . hyper-v.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del hyper-v.txt
Dism /online /enable-feature /featurename:Microsoft-Hyper-V -All /LimitAccess /ALL
pause
{% endhighlight %}

File 2:
{% highlight html %}
pushd "%~dp0"
dir /b %SystemRoot%\servicing\Packages\*containers*.mum >containers.txt
for /f %%i in ('findstr /i . containers.txt 2^>nul') do dism /online /norestart /add-package:"%SystemRoot%\servicing\Packages\%%i"
del containers.txt
Dism /online /enable-feature /featurename:Containers -All /LimitAccess /ALL
pause
{% endhighlight %}



*Execute file.1 as administrator*
*Execute file.2 as administrator*

Install windows for docker.

We need to modify the current version in windows registry, please refer to this <a href="https://forums.docker.com/t/installing-docker-on-windows-10-home/11722/29">comment</a>:

After the previous modification reboot your machine. 

Now, we are really going to open the WSL and install docker, the next is a great <a href="https://nickjanetakis.com/blog/setting-up-docker-for-windows-and-wsl-to-work-flawlessly#install-docker-and-docker-compose-within-wsl
">tutorial</a> for this:


Once you’ve installed docker, the final step is to install the terminator tool.
Install the terminator tool

After you’ve rebooted your machine, so let’s start the WSL. Terminator is for me one indispensable tool for development. So, let’s installed it in the WSL:

- Install VcXsrv for windows and run it.
- In the WSL run  echo "export DISPLAY=:0" >> $HOME/.bashrc
- for changes of .bashrc file to get append run source $HOME/.bashrc
- after that you can run terminator -u and use it.

That's it, I hope you find this post usefull :)