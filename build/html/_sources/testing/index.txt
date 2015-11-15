Testing the PyWPS and first process
###################################
The very first test can be done in the command line. Before we start,
several environment variables have to be set::

    export PYWPS_PROCESSES=/usr/local/processes/
    export PYWPS_CFG=/usr/local/processes/pywps.cfg

GetCapabilities
***************
Now we can run the wps.py binary from the command line::
    
    /usr/local/pywps/wps.py "service=wps&request=getcapabilities"

You should obtain following text.

.. literalinclude:: capabilities.xml
    :language: xml
    :linenos:
    :lines: 1-8,63-70,80

You see, that ``returner`` process is available

DescribeProcess
***************
::

    /usr/local/pywps/wps.py "service=wps&version=1.0.0&request=describeprocess&identifier=returner"

We now can see process inputs definition

.. literalinclude:: describeprocess.xml
    :language: xml
    :linenos:
    :lines: 10-35

as well as process outputs

.. literalinclude:: describeprocess.xml
    :language: xml
    :linenos:
    :lines: 36-60

Execute
*******
Finally, let the process be executed::

    /usr/local/pywps/wps.py "service=wps&version=1.0.0&request=execute&identifier=returner&datainputs=[text=1;data=http://apps.esdi-humboldt.cz/classification/traning_areas/training_areas_en.gml]"

First thing you see, is bunch of INFOs about current process status::

    PyWPS INFO: Reading processes from [/home/jachym/usr/src/pywps/processes/course/]
    PyWPS INFO: Following processes are imported: ['returner']
    PyWPS INFO: Status [processpaused]: Getting input text of process returner
    PyWPS INFO: Status [processpaused]: Getting input data of process returner
    PyWPS INFO: Status [processstarted][0.0]: Process returner started
    PyWPS INFO: Status [processsucceeded]: PyWPS Process returner successfully calculated

And afterwards, the resulting XML will come

.. literalinclude:: execute.xml
    :language: xml
    :linenos:
    :lines: 3-12,136-142,13-29

As reference output
===================
You can see, that the output XML  response contains the resulting GML. We
can request the **reference** to it, put this at the end of previous request
string::

    &responseform=responsedocument=[output=@asreference=true]

And you get 

.. code-block:: xml

        <wps:Output>
            <ows:Identifier>output</ows:Identifier>
            <ows:Title>Output vector data</ows:Title>
            <wps:Reference xlink:href="http://localhost/wps/wpsoutputs/output-9115" mimeType="text/xml"/>
        </wps:Output>

You can also configure PyWPS, so the output reference URL is `OGC WFS
<http://opengeospatial.org/standards/wfs>`_ or `OGC WCS
<http://opengeospatial.org/standards/wcs>`_, if you `use mapscript in your
process <http://pywps.wald.intevation.org/documentation/pywps-3.2/special/mapserver.html>`_
