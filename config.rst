SpreadServe Configuration
=========================

**SpreadServeEngine configuration**

``sseng.ini``: this config file lives in ``%SSROOT%\bin`` as it needs to be in the same directory as sseng.exe.
sseng.exe uses it to determine the location of various resources including dynamically loaded DLLs and
the xll.txt file that specifies the XLLs to be loaded. Most of the settings are purely internal; two may be
of interest to users.

* ``XLL_CFG_FILE``: path to the file that specifies the XLLs to be loaded.
* ``XLL_REG_FILE``: path to the file sseng.exe uses to dump signatures of XLL worksheet functions
  that have been successfully registered.

**RealTimeWebServer configuration**

The RealTimeWebServer implementation is all in one Python module: ``%SSROOT%\py\http\rtwebsvr.py``, which has
several global variables you can change to configure its behaviour. Look for the section near the top
with the Config comment. If you want to change the port that RealTimeWebServer listens on then change
this line::

    define( "port", default=8888, help="run on the given port", type=int)

Below the port config line are several variables...

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
    
* ``ssroot``: defaults to the value of the ``SSROOT`` environment variable. It is unlikely
  you'll need to change this.
* ``DefaultRepoDir``: path for the repository RealTimeWebServer serves as ``/repository.html``. Uploaded
  spreadsheets are saved here. When you click the ``Load`` button on the Dashboard page you're offered
  a list of the repository contents to load.
* ``AideName``: process name used for rtwebsvr.py in the dashboard.
* ``DefaultProcessName``: process name used for rtwebsvr.py when no command line parameters are supplied in a
  command line launch.
* ``DefaultCmdLine``: command line parameters used when they're not supplied at the command line.


**SpreadServe Command line parameters**

All SpreadServe processes, whether they are RealTimeWebServer, SpreadServeEngine, Dora, Pan, SocketServer 
or DBLogServer take a common set of command line paramters. Some have custom parameters that tailor a specific
part of their behaviour. You'll see the parameters used in the shell scripts in ``%SSROOT%\sh`` and the JSON
launch files in ``%SSROOT%\cfg``. Command line options are always presented with a leading hyphen and a following
value eg ``-ENV SIT -NAME DBLogServer``.

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
