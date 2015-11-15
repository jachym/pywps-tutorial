Deploying PyWPS on the server
#############################

PyWPS can run as `CGI application <>`_, in `mod_python environment <>`_ or as `java
servlet <>`_ (with one limitation). There are examples how to do this in
:file:`webservices` directory of the source code. 

Here we will use the CGI approach, because it is the easiest one.

In the server :file:`cgi-bin` directory (/usr/lib/cgi-bin) create file,
called :file:`wps` with following content::

    #!/bin/sh

    export PYWPS_CFG=/usr/local/pywps/process/pywps.cfg
    export PYWPS_PROCESSES=/usr/local/pywps/processes

    /usr/local/pywps/wps.py

Now you should be able to point the browser to following address
http://localhost/cgi-bin/wps and you should see "something"

This is also the way, how to setup *several PyWPS servers*  with separete
set of processes on one web server.
