SpreadServe Tech Spec
=====================

**Technical specification**

SpreadServe is a Windows Server hosted server product with a web UI.

* OSes: any Windows desktop OS from Windows 7, and any server OS from 2003. SpreadServe has been tested on Windows 7, Windows 8, 
  Windows Server 2008 & 2012.
* User interface: web UI built with `Angular <http://angularjs.org>`_ 1.5, `Bootstrap <http://getbootstrap.com/>`_ 3.3 and `jQuery <http://jquery.com/>`_ 2.2
  enabling job control and spreadsheet interaction from a browser.
* APIs

  * Python: the best API for Web integrations, as well as rapid, ad hoc development. SpreadServe uses Python 2.7.8, 3.x is not
    yet supported.
  * C++: the best API for high performance systems integration. For example, this is the right way to connect SpreadServe to
    a low latency pub sub messaging system like TibRV or Solace. Integrations should be built with MS Visual Studio 2008 or
    later.
  * REST: external processes can drive SpreadServeEngines via the RealTimeWebServer REST API.
  * Java: the best option for relational database integration with JDBC. spreadserve.jar is built with JDK 1.6.
  
* Core implementation: SpreadServe's native implementation language is C++ built as 32 bit binaries with Microsoft Visual C++ 9.
* VBA: SpreadServe supports VBA macros, but does not support VBA GUI operations.
* XLL: SpreadServe supports XLL addins, and has been tested with the `QuantLib <http://quantlib.org/index.shtml>`_ 1.4.0 addin.
* RTD: SpreadServe supports RTD updates.
* Excel file formats: Excel 97-2003 .xls as well as the default Excel .xlsx format are supported.
* RDBMS: the dblog component enables logging of all SpreadSheet operations to an RDB.
  SpreadServe has been tested with `MySQL <http://www.mysql.com/>`_ 5.6, and should work with any RDB that has a JDBC driver.
* Sockets: The SocketServer enables connections from processes based on other hosts and implementation technologies.
* Open Source Software: SpreadServe uses several powerful OSS technologies as building blocks.
  They include `Tornado <http://www.tornadoweb.org/en/stable/>`_, `Python <https://www.python.org/>`_,
  `STLport <http://www.stlport.org/>`_ , `Boost <http://www.boost.org/>`_ and `Apache Open Office <https://www.openoffice.org/>`_.
* Cloud ready: SpreadServe has been deployed and tested on `Amazon EC2 <http://aws.amazon.com/ec2>`_ Windows 2008 & 2012 AMIs.
