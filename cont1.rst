SpreadServe Containters I
=========================

**Introduction and cautionary note**

This tech note is a record of the various hacks and tweaks necessary to get SpreadServe 0.3.1 running 
in a container on an Azure hosted Windows Server 2016 TP3 VM. It's not SpreadServe documentation
as such, but it may be useful to any parties interested in running SpreadServe in a container.
At the original time of writing - 2015-10-13 - work had progressed to enable a one line
launch of SpreadServe containers on spreadserve01.cloudapp.net. There are still outstanding
issues to resolve and they will be addressed in a later note. The following Microsoft resources
were used as source material for this work.

* Quick start: https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/manage_docker
* Work in progress: https://msdn.microsoft.com/virtualization/windowscontainers/about/work_in_progress#DockermanagementDockercommandsthatdontworkwithWindowsServerContainers
* Forums: https://social.msdn.microsoft.com/Forums/en-US/home?forum=windowscontainers&sort=lastpostdesc&brandIgnore=true&page=7

**Stuff that didn't work**

I ran into lots of issues when I tried to apply the recipe outlined in Quick start to SpreadServe.
First up was the simple fact that, by design, you can't run a GUI in PowerShell. MS provide a
handy recipe in the Work in progress page for RDPing into a container, which does allow
you to run a desktop. It all worked fine for me until the container desktop turned up in
my mstsc with a DOS box showing this prompt: ``Your account has been disabled. Please see your system administrator``
The fix was to do this in a container PowerShell::

    net user administrator /active:yes
    net user administrator <password>
    
This enabled me to RDP in, and run my SpreadServe installer inside the container. My aim was to create 
a container with SpreadServe installed, and then commit as an image I could reuse. I was able to
run the installer in my GUI session, but it appeared to hang. Intially I couldn't figure out why, so
I revisited the installer design, which is GUIcentric, and uses NSIS. As well as a SpreadServe
directory tree, it sets environment variables, and launches vcredist_x86.exe to install the
Visual Studio 2008 C++ runtime. I revised the installer to work in silent mode, to not attempt
to set environment variables via the Registry, and to not launch the vcredist_x86.exe installer.
Windows Server 2016 TP3 has the Visual Studio 2008 C++ runtime installed already so we don't
need it. It took several attempts to rebuild the installer to run cleanly in silent mode.
Sys Internals Process Explorer was invaluable in watching the progress of the installer
with the lower pane handles view. If you launch it from a cmd shell, not a PowerShell, in
the container host you can see its GUI, which seems to show you all processes, container
hosted or not on the VM. Invaluable! You can download procexp.exe to your Azure WM by
doing::

    wget -uri 'https://download.sysinternals.com/files/ProcessExplorer.zip' -OutFile ProcessExplorer.zip
    
While I was paring down my install I was attempting to create a SpreadServe container image
in two steps. Step one used a dockerfile to create an image with my installer imported::

    FROM windowsservercore
    LABEL Description="SpreadServe for Windows 2016 TP3" Vendor="SpreadServe" Version="0.3.1"
    ADD zip/ss031.exe /osullivj/zip/ss031.exe
    
I used that dockerfile to create an ssinstall image in which I'd run the ``ss031.exe`` installer
interactively. Then stop the resulting container and attempt to commit the image::

    docker build -t ssinstall .
    docker run -it --name ss031 ssinstall cmd
    cd \osullivj\zip
    .\ss031.exe /S
    exit
    docker commit ef79f92a7479 ss031a
    
This failed everytime at the final step with this error::

    PS C:\> docker commit ef79f92a7479 ss031a
    Error response from daemon: hcsshim::ImportLayer - Win32 API call returned error r1=2147549183 err=Catastrophic failure layerId=60d6b5ec18466f1b368f67a111bc476b717d9ec497e0097fcf04ff419e01077e flavour=1 folder=C:\ProgramData\Docker\windowsfilter\60d6b5ec18466f1b368f67a111bc476b717d9ec497e0097fcf04ff419e01077e-3287807202
    PS C:\> docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    ssinstall           latest              74e160e024e8        39 hours ago        9.749 GB
    windowsservercore   10.0.10514.0        0d53944cb84d        7 weeks ago         9.697 GB
    windowsservercore   latest              0d53944cb84d        7 weeks ago         9.697 GB
    
It was only when I stripped down my install process to work completely silently, not touch
the registry for environment variable settings, and not attempt to run vcredist_x86.exe
that the final commit started working. When this worked I tried extending the dockerfile to
make the SpreadServe image build a one shot operation so I wouldn't have to run ss031.exe
at the command line. This was the dockerfile::

    FROM windowsservercore
    LABEL Description="SpreadServe for Windows 2016 TP3" Vendor="SpreadServe" Version="0.3.1"
    ADD zip/ss031.exe /osullivj/zip/ss031.exe
    RUN /osullivj/zip/ss031.exe /S
    
But this would always error at the final step. This is what I got::

    PS C:\osullivj> docker build -t ss031a .
    Sending build context to Docker daemon  55.8 MB
    Step 0 : FROM windowsservercore
     ---> 0d53944cb84d
    Step 1 : LABEL Description "SpreadServe for Windows 2016 TP3" Vendor "SpreadServe" Version "0.3.1"
     ---> Using cache
     ---> 01e5edd46b4a
    Step 2 : ADD zip/ss031.exe /osullivj/zip/ss031.exe
     ---> Using cache
     ---> b8f27700fecb
    Step 3 : RUN /osullivj/zip/ss031.exe /S
     ---> Running in 3b05abbb5b2a
    The command 'cmd /S /C /osullivj/zip/ss031.exe /S' returned a non-zero code: 1223

I'm guessing this must be something to do with the wasy the NSIS installer exits, but
can't figure a way to resolve it.

Once I had a working container image committed, my next challenge was port mapping the
SpreadServe web server. SpreadServe's RealTimeWebServer listens on port 8888 by default,
so I spent a couple of hours tinkering with various permutations of the ``docker run`` -p
parameter. For instance ``docker run -p 80:8888``. I also created a firewall rule
for 8888 in the container host, since the a post in the forums told me that containers
don't have their own firewall, they inherit the host firewall. Eventually I gave up
and fixed SpreadServe to run its RealTimeWebServer on port 80, and suddenly it worked!

**Stuff that worked**

There were three key points to getting SpreadServe going in Windows Containers...

* Two step process to build container image due to installer issues
* Web server inside the container should be on port 80 internally
* Create a one line launch script that sets up environment variables

The two step image building process is covered in detail above. I'll say a little more
about the other two points here. Firstly the launch script. I mentioned above that
I had to strip out the installer script code that created Registry entries for the 
two environment variables that SpreadServe needs: SSROOT and SSROOTX. To enable single
line launch from docker I created a cmd script - ``dbaseweb.cmd`` - that sets the
environment variables and launches SpreadServe. This enables me to launch SpreadServe
instances like so...

    docker run -p 80:80 ss031c c:\spreadserve\ss0.3.1\sh\dbaseweb.cmd
    docker run -p 81:80 ss031c c:\spreadserve\ss0.3.1\sh\dbaseweb.cmd
    
To enable those launch lines to work the container host needs an Azure Endpoint defined 
for port 80, port 81 and any external port that needs mapping to a container. There
also needs to be a firewall rule opening the port like so...

    New-NetFirewallRule -Name "TCP81" -DisplayName "HTTP on TCP/81" -Protocol tcp -LocalPort 81
    
**Next steps**

* SpreadServe's RealTimeWebServer authenticates against Windows user IDs and groups. How will
  that work in a container. Will groups and users be inherited from the host VM?
* Port mapping. I may want to run 1000 containers on the same host. Do I just
  write a single script to open 80 -> 1080, and have my container launch server
  use and reuse them as necessary?