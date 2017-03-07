SpreadServe Configuration
=========================

**SpreadServeEngine configuration**

``sseng.ini``: this config file lives in ``%SSROOT%\bin`` as it needs to be in the same directory as sseng.exe.
sseng.exe uses it to determine the location of various resources including dynamically loaded DLLs and
the xll.txt file that specifies the XLLs to be loaded. Most of the settings are purely internal; two may be
of interest to users.


* ``LICENSE_FILE``: path to the license key file, which can contain an offline key to stop the SpreadServeEngine
  phoning home to spreadserve.com.
* ``DNS_HOST_NAME``: path to a flat text file containing a hostname. If the file exists, SpreadServeEngine will
  use this hostname when phoning home to spreadserve.com.
* ``OWNER``: the email address associated with this SpreadServe host by spreadserve.com.
* ``FORMAT_LOADING``: switches cell formatting on or off to accelerate loading xlsx files. No impact on xls handing.
* ``XLL_CFG_FILE``: path to the file that specifies the XLLs to be loaded.
* ``XLL_REG_FILE``: path to the file sseng.exe uses to dump signatures of XLL worksheet functions
  that have been successfully registered.

**RealTimeWebServer configuration**

The RealTimeWebServer implementation is mostly in one Python module: ``%SSROOT%\py\http\rtwebsvr.py``, which has
gets configuration variables from ``%SSROOT%\cfg\webcfg.py``. If you want to switch SpreadServe to work with
Active Directory authentication, rather than it's default social login, then edit webcfg.py to configure
user group mappings. You'll also need to supply the ``AUTH`` command line parameter to RealTimeWebServer. See
the command line parameters section below for detail on ``AUTH``.

* ``ADGroupMappings``: a dictionary defining the Active Directory groups that for which user
  membership will give view, edit or admin permissions.
  
  * ``view`` defaults to ``Users``. All Windows hosts have a ``Users`` group. ``view`` gives
    a user basic view only privileges.
  * ``edit`` defaults to ``SSEdit``. You probably won't have an SSEdit group in your
    Active directory group mappings. You should either create one, or change the edit
    setting to name an appropriate existing AD group. Users with edit permissions can
    click on a sheet's value fields in a browser and change them. They can also hit the
    Calc and RTD buttons for a SpreadServeEngine on the dashboard.
  * ``admin``: defaults to ``SSAdmin``. As with SSEdit, you should either create an SSAdmin
    group in your environment, or map admin to an appropriate existing AD group. Users with
    admin permission can start and stop SpreadServe processes via the dashboard, and upload
    new spreadsheets to the repository via the repository page.
    

**SpreadServe command line parameters**

All SpreadServe processes, whether they are RealTimeWebServer, SpreadServeEngine, Dora, Pan, SocketServer 
or DBLogServer take a common set of command line paramters. Some have custom parameters that tailor a specific
part of their behaviour. You'll see the parameters used in the shell scripts in ``%SSROOT%\sh`` and the JSON
launch files in ``%SSROOT%\cfg``. Command line options are always presented with a leading hyphen and a following
value eg ``-ENV SIT -NAME DBLogServer``.

* ``HTTP_PORT``: supply this on the rtwebsvr.py command line to change the RealTimeWebServer port. For example
  ``-HTTP_PORT 80`` to run on port 80.
* ``AUTH``: supply this on the rtwebsvr.py command line to specify the authentication mechanism. The three
  possible settings are
  
  * ``sscld``: the default. Cloud authentication with spreadserve.com. To edit permissions at http://spreadserve.com/adm/cldperms.html
    you must ensure your email address is set in ``sseng.ini:OWNER``
  * ``ssad``: Active Directory. Configure your user group mappings as describe above.
  * ``ssna``: No authentication. We suggest you only use this is development and test environments, and not production!  

* ``ENV``: Mandatory. Environment that this SpreadServe process belongs to. Several environments can co-exist on the
  same host as components will only recognise and communicate with components in the same named environment.
* ``NAME``: Optional. C++ processes will default to the exe name on the dashboard page, and Python processes will
  show up as ``python``. Using NAME you can override these defaults to make processes more readily identifiable.
* SpreadServeEngine: these options are specific to SpreadServeEngine

  * ``PIPE_LOG``: used to switch on detailed logging from the engine's subsystems. For instance ``-PIPE_LOG BSX``.
    The value of the option should be one or more letters from D, B, S, X. Switching some or all of these options
    on will generate a lot of logging.
    
    * ``D``: general debugging output.
    * ``S``: spreadsheet compiler and interpreter
    * ``X``: XLL subsystem
    * ``B``: Basic
    
  * ``SUPPRESS_OUTPUT_LOG``: set to ``1`` to switch off logging of the engine's interprocess communication with
    the RealTimeWebServer. SpreadServeEngine sends whole web pages in HTML as well as JSON formatted updates of
    sheet state. That can generate a huge amount of logging if there's a high update frequency, and it can make
    it difficult to follow higher level events like incoming data triggering recalcs.
  
**SpreadServe scripts**

The ``%SSROOT%\sh`` directory holds several scripts for starting and stopping SpreadServe processes singly or as
a group.

* ``launch.cmd``: launch a group of SpreadServe processes. 
* ``halt.cmd``: stop a group of SpreadServe processes.
* ``sseng.cmd``: launch a SpreadServeEngine process.
* ``sspy.cmd``: launch a SpreadServe Python process.
* ``dbconn.cmd``: launch the DB connector.

See the User Guide for examples of their use.

**Log files**

SpreadServe log files appear in the ``%TEMP%`` directory.

**Profiles**

Profiles are JSON config files that determine which SpreadServe processes are launched at startup time. A launch command
for SpreadServe takes the form ``launch <environment> <profile>``. for instance ``launch SIT baseweb``. Several ready
made profiles are supplied in the ``%SSROOT%\cfg`` directory. They are...

* ``pandora``: a minimal profile that only starts ssdora.exe, the ProcessRegistry and sspan.exe, the EventBus. Useful for
  developers who want to control hte launch of other processes, perhaps because they're debugging.
* ``base``: launches three processes: ProcessRegistry, EventBus and sseng.exe, the SpreadServeEngine itself. 
* ``baseweb``: launches the same three processes as ``base``, but with the addition of the RealTimeWebServer.
* ``demo``: same as ``baseweb``, but adds BlackScholesMockMarketData to pump fake market data into the BlackScholes.xls
  example sheet.
  
**Windows Service**

The ``launch.cmd`` and ``halt.cmd`` scripts described above are appropriate for manually launching and halting
SpreadServe. You may also find them convenient for other job control systems like AutoSys. You can also
configure SpreadServe to run as a Windows Service::

    cd %SSROOT%\py\util
    ..\..\sh\sspy windows_service.py install
    
Then you can use Windows' Services GUI to configure Automatic or Manual startup, and to start and stop the service.
We recommend you do not use the Local System account to run SpreadServe as a Windows Service, and instead configure
it to run under Administrator or some other user account. SpreadServe's RTD capabilities, as implemented in SSAddin,
rely on Registry ClassId and ProgId lookup that access the HKCU hive, and they don't wotk under Local System. Once
you've created the service you can start and stop SpreadServe at the command line like so::

    sc start SpreadServe
    sc stop SpreadServe
    
To automate SpreadServe start and stop times on a specific host you can use Windows Task Scheduler to invoke
``sc start SpreadServe`` and ``sc stop SpreadServe``.
