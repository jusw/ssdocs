SpreadServe Tech Spec
=====================

**Technical specification**

SpreadServe is a Windows Server hosted server product with a web UI.

* OSes: any Windows desktop OS from Windows 7, and any server OS from 2003. SpreadServe has been tested on Windows 7, Windows 8 and Windows Server 2008.
* User interface: web UI built with `Bootstrap <http://getbootstrap.com/>`_ 2.0.4 and `jQuery <http://jquery.com/>`_ 1.7.1 enabling job control and
  spreadsheet interaction from a browser.
* APIs: Python. C++ and Java APIs exist in prototype form. The Python API supports integrations and unit testing of spreadsheets.
* Core implementation: SpreadServe's native implementation language is C++.
* VBA: SpreadServe supports VBA macros.
* XLL: SpreadServe supports XLL addins, and has been tested with the `QuantLib <http://quantlib.org/index.shtml>`_ 1.4.0 addin.
* Excel file formats: Excel 97-2003 .xls is currently supported. The later Excel .xlsx format is in development.
* RDBMS: the dblog component enables logging of all SpreadSheet operations to an RDB.
  SpreadServe has been tested with `MySQL <http://www.mysql.com/>`_ 5.6, and should work with any RDB that has a JDBC driver.
* Sockets: The SocketServer enables connections from processes based on other hosts and implementation technologies.
* Open Source Software: SpreadServe uses several powerful OSS technologies as building blocks.
  They include `Tornado <http://www.tornadoweb.org/en/stable/>`_, `Python <https://www.python.org/>`_,
  `STLport <http://www.stlport.org/>`_ , `Boost <http://www.boost.org/>`_ and `Apache Open Office <https://www.openoffice.org/>`_.
* Cloud ready: SpreadServe has been deployed and tested on an `Amazon EC2 <http://aws.amazon.com/ec2>`_ Windows 2008 AMI.
