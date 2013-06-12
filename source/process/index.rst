Writing custom WPS Process
##########################
PyWPS Processes is `Python module
<http://docs.python.org/tutorial/modules.html>`_ or instance of the module.

Processes are stored in special directory, `Python Package <http://docs.python.org/tutorial/modules.html#packages>`_. This must contain ``__init__.py`` file containing list of available processes in ``__all__`` array.

There are several possibilities, where to locate the directory (in general,
it can be *anywhere* in the system), we will put it at the same level, as
PyWPS installation::

    mkdir /usr/local/processes

Process structure
*****************
Each process is python Class (or it's instance) with **at least two methods**:

    * ``__init__`` which initializes the process, where you will define
      inputs and outputs, and other stuff
    * ``execute`` which will be called, once PyWPS accepts Execute request
      type

Start to edit file of name ``returner.py`` in the created processes
directory:

.. literalinclude:: ../../../../trunk/tests/processes/returner.py
    :linenos:

Let's talk about it more deeply:

Initialization
==============

.. literalinclude:: ../../../../trunk/tests/processes/returner.py
    :linenos:
    :lines: 1-16

The process is inherited from the :class:`pywps.Process.WPSProcess` class.
In Python, you must initialize this parent class at first place.

In the initialization, you have to define 

    * **identifier** Process identifier
    * **title**  Human-readable title
    * **abstract** Optionally longer text for process description

Several other things can be defined:

    * **version** process version
    * **statusSupported** indicates, whether the process supports
      asynchronous calls
    * **storeSupported** indicates, whether the process can deploy it's
      result to the server for later usage

And other parameters

Then Inpus can be defined

Process Inputs
--------------

.. literalinclude:: ../../../../trunk/tests/processes/returner.py
    :linenos:
    :lines: 21-26

In this case, we create and add ComplexData input and LiteralData input.
Both do have long line of configuration options, but for now, we let the
default ones.

The **LiteralData** input will be of type *integer*. The **ComplexData**
input will accept only XML-encoded files, ideally `GML
<http://opengeospatial.org/standards/gml>`_.

Process Outputs
---------------

.. literalinclude:: ../../../../trunk/tests/processes/returner.py
    :linenos:
    :lines: 31-36

As you can see, Outputs are defined in very similar way to the inputs.

For more, see `in- and output documentation <http://pywps.wald.intevation.org/documentation/pywps-3.2/api/Process/InAndOutputs.html>`_

Execute method
==============
Here it comes: this is the actual working method. Here your data are
analyzed, imported to GRASS, translated using GDAL, transformed using OGR,
interpolated using R. Here it happens. 

In our case, the "analysis" is pretty simple.

.. literalinclude:: ../../../../trunk/tests/processes/returner.py
    :linenos:
    :lines: 40-46

We used the :meth:`getValue()` method of data inputs and :meth:`setValue()`
method of data outputs and just copied input values to output values.

Reporting some error
--------------------
If :meth:`execute()` returns anything **but** null, it is considered as
error and exception is called::
    
    return "Oups! Something went wrong"

Calling command line utils
--------------------------
In some case, you need to call command line utils. You can either use the
:mod:`subprocess` module, or :class:`WPSProcess` method
:meth:`WPSProcess.cmd`::

    region = self.cmd(["g.region","-g"])

.. note:: The command is accepted as **array** of parameters. Calling it as
    string, e.g. "g.region -g" would NOT work.

.. note:: Better do not use such command line utils. It is much better to
    work with standard python interface of various programs. 

Logging and debugging
---------------------
Do not use :func:`print` function. Use ``logging`` package instead::

    import logging

    logging.info("Just information message")
    logging.debug("Variable content: %s" % variable)
    logging.error("Error occured: %s" % e)

If you set the file name in the configuration file, you should obtain the
result there. Otherwise, you should be able to see something in the
standard error file (e.g. :file:`/var/log/apache2/error.log`)

Registering the process into PyWPS server
*****************************************
Now, we should have following directory structure::

    /usr/
    /usr/local/
    /usr/local/processes/
    /usr/local/processes/returner.py

Since :file:`processes` are *python package*, :file:`__init__.py` file must
be available. Every python module, which is part of the python package must
be listed in the ``__all__`` array in the init file. 

Content of :file:`/usr/local/process/__init__.py`::
    
    __all__ = ["returner"]
