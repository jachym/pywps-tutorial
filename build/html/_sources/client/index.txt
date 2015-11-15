WPS OpenLayers Client
#####################

`OpenLayers <http://openlayers.org>`_ makes it easy to put a dynamic map in
any web page. It can display map tiles and markers loaded from any source.
MetaCarta developed the initial version of OpenLayers and gave it to the
public to further the use of geographic information of all kinds.
OpenLayers is completely free, Open Source JavaScript, released under a
BSD-style License.

OpenLayers was chosen as background library for necessary JavaScript
programming, because it contains all the necessary stuff, like XML parsing,
Ajax server proxy, GML parsing, etc.

Several examples are available in the PyWPS webclient directory. Following
code snipplets are taken from the `wps.html <../_static/wps.html>`_ file.
:file:`WPS.js` is located `in the same directory <../_static/WPS.js>`_.

.. note:: For better JavaScript debugging, consider to use `Firebug
    <http://getfirebug.com>`_ Firefox extension, which makes the life of
    JavaScript programmer really easier.

The map
*******
Create the map, with WMS layer and vector layer. The vector layer can be
edited

.. literalinclude:: ../_static/wps.html
    :language: javascript
    :linenos:
    :lines: 6,20-34

Setup the WPS client
********************
The :file:`WPS.js` is part of PyWPS source, in the :file:`webclient`
directory. You have to copy it to the HTML file and include it.

.. literalinclude:: ../_static/wps.html
    :language: javascript
    :linenos:
    :lines: 7,46-78

Once the process is calculated, :func:`onWPSSussceeded` is called.

.. literalinclude:: ../_static/wps.html
    :language: javascript
    :linenos:
    :lines: 81-85

Another way, how to obtain some informations from the server and build the
client automatically is to run ``wps.getCapabilities`` and
``wps.describeProcess`` methods. All informations from the server are then
stored on the client.
