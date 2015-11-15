.. index::
    single: WPS
    single: OGC
OGC Web Processing Service
##########################
.. note:: This section should be usable for *any* WPS implementation and
    should give you general picture about the OGC WPS standard.

`Open Geospatial Consortium <http://opengeospatial.org>`_
    is a non-profit, international, voluntary consensus **standards
    organization** that is leading the development of standards for
    **geospatial** and location based services.

In short: it defines standards for geo-people, to communicate in the
Internet environment. 

One of the standards, defined by OGC is `Web Processing Service
<http://opengeospatial.org/standards/wps>`_:
    ... provides rules for standardizing **how inputs and outputs**
    (requests and responses) **for geospatial processing services**, such
    as polygon overlay. The standard also defines **how a client can
    request the execution of a process, and how the output from the process
    is handled**.  It defines an **interface that facilitates the
    publishing of geospatial processes and clients’ discovery of and
    binding to those processes**. The data required by the WPS can be
    delivered across a network or they can be available at the server.

Again in short: the standard describes, how to execute some geospatial
analysis deployed on the server and obtain the result back.

.. note:: Before you start to deal with PyWPS, consider to have a look at
    the standard - you can `download it for free
    <http://portal.opengeospatial.org/files/?artifact_id=32766>`_. 

.. index::
    single: KVP
    single: POST
    single: GET

Invoking WPS server
*******************
You can invoke WPS server in two ways: using HTTP GET method with
key-value-pairs encoded request,  or HTTP POST method, using XML format of
the request. They are both described well in the standard.

KVP request encoding
====================
In general, parameters are formated in the form::

    ...&key1=value1&key2=value2&...

They are written usually behind ``?`` sign of the server address. For
example, invoking GetCapabilites operation, see :ref:`my-getcapabilities-label`

    http://foo/bar/wps?service=WPS&request=GetCapabilities

Let's look at this URL more detaily:

*http://*
    It is the protocol of the request. We are using Hyper Text Transfer
    Protocol

*foo*
    The WPS server address, where you will install it. This is usually
    something like google.com, microsoft.com, gnu.org or similar. At the
    beginning, it will be mostly *localhost*, which means, that it will be
    your computer.

*/bar/wps*
    Path name of the installed PyWPS server.

*?*
    The ``?`` sign indicates, that the request parameters will start

*service=WPS&request=GetCapabilities*
    The KVP-encoded  request. We send two request parameters to the server:

    * service - which we set to *WPS*
    * request - which is set to *GetCapabilities*

The parameter names should be case insensitive, means, that ``SERVICE`` is the
same as ``service`` and the same as ``SeRvIcE``. The parameter values
should be mostly case insensitive as well, means ``GETCAPABILITIES`` should
be the same as ``getcapabilities`` and ``GetCapabilities``.

XML request encoding
====================
In this case, the request is encoded in XML form and send to the server
*directly* via HTTP POST (the WPS server will read the file from standard input
directly).

.. note:: Since XMLs are maybe too complicated, we will be using KVP
    encoding in this tutorial. Only one XML-encoded request type example
    will be showed. There are examples in the test directory of PYWPS as
    well as in the WPS Standard document.

GetCapabilities request in XML form would look like:

.. code-block:: xml

    <?xml version="1.0" encoding="UTF-8"?>
    <ows:GetCapabilities xmlns:ows="http://www.opengis.net/ows/1.1" xmlns:wps="http://www.opengis.net/wps/1.0.0" xmlns:xlink="http://www.w3.org/1999/xlink" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.opengis.net/ows/1.1 ..\wpsGetCapabilities_request.xsd" language="en" service="WPS">
            <ows:AcceptVersions>
                    <ows:Version>1.0.0</ows:Version>
            </ows:AcceptVersions>
    </ows:GetCapabilities>

Other requests can be much more complicated.

.. index::
    single: Request
    single: GetCapabilities
    single: DescribeProcess
    single: Execute
WPS Request types
*****************
Similar to other OGC Web Services (such as WMS, WFS or WCS), WPS has three basic
request types. Namely **GetCapabilities, DescribeProcess** and **Execute**.

.. _my-getcapabilities-label:

GetCapabilities
===============
http://apps.esdi-humboldt.cz/pywps/?service=WPS&request=GetCapabilities

Mandatory parameters:

    service 
    request

The server returns basic `Capabilities` document. Among others it contains:

**ServiceIdentification**
  * Title
  * Abstract
  * Keywords
  * Service provider (Name, Organization, Address, ...)

**OperationsMetadata** - URLs to particular operations (GetCapabilities, DescribeProcess and Execute)

**ProcessOfferings** - List of available processes

DescribeProcess
===============
http://apps.esdi-humboldt.cz/pywps/?service=WPS&version=1.0.0&request=DescribeProcess&identifier=all

.. note:: New *mandatory* parameters appeared in the request URL: 

    * WPS version number
    * Process identifier - it can be process identifier as stated in the
      Capabilites document or key word 'all' for all process descriptions

The *ProcessDescriptions* document contains detailed description of selected
processes. Each process is identified by

    * Title
    * Identifier
    * Abstract (optional)
    * DataInputs
    * DataOutputs

Now the client can be configured in order to be able to Execute the
process.

Execute
=======
http://apps.esdi-humboldt.cz/pywps/?service=WPS&version=1.0.0&request=Execute&identifier=literalprocess&datainputs=[int=1;float=3.2;zeroset=0;string=spam]&storeExecuteResponse=false

.. note:: No new *mandatory* parameters appeared in the request URL,
    however, several optional did, among others **DataInputs**, but we will
    discuss some of them later.

Most important parameter, **DataInputs** is encoded as again as
key-value-pairs, separated with ``;`` sign. 


WPS In- and Outputs
*******************
Three types of inputs and outputs are defined in the OGC standard.
LiteralData, ComplexData and BoundingBox data.

LiteralData
===========
LiteralData can be any character string of special type, as described in http://www.w3.org/TR/xmlschema-2/

In PyWPS, there is currently support for
    * String
    * Float
    * Integer
    * Boolean

ComplexData
===========
This data type is used for pasting *complex* - Vector- Raster- or other
data to the server or obtain it as result of the process. There are two
ways, how this complex data are handled, 

    * Either you send them **directly** as part of the request to the server
      or you obtain them as part of the XML response from the server. This
      is mostly done by vector data, using GML or other text-based format.
      For raster data, they can be encoded using `base64 encoding
      <http://en.wikipedia.org/wiki/Base64>`_
    * Or you send or obtain just **reference** to the data -- URL to the file
      or service, where the data can be downloaded.

BoundingBoxData
===============
If you need to define or obtain some kind of bounding box (2 or more
dimensional), you can use this type of data.

~
*
Now you should be able to understand, what WPS is about, which are the in-
and outputs, get some image *how* to call each request type and *what* you
can expect as result. Let's continue with PyWPS.
