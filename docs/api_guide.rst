API Guide
=========

This API is what powers the :ref:`cli <cli:Command-Line Interface>` but is also available to developers that wish
to make use of the data Streamlink can retrieve in their own application.


Extracting streams
------------------

The simplest use of the Streamlink API looks like this:

.. code-block:: python

    >>> import streamlink
    >>> streams = streamlink.streams("https://twitch.tv/day9tv")

This simply attempts to find a plugin and use it to extract streams from
the URL. This works great in simple cases but if you want more
fine tuning you need to use a `session object`_ instead.

The returned value is a dict containing :class:`Stream <stream.Stream>` objects:

.. code-block:: python

    >>> streams
    {'best': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'high': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'low': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'medium': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'mobile': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'source': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>,
     'worst': <HLSStream('https://video11.fra01.hls.twitch.tv/ ...')>}


If no plugin for the URL is found, a :exc:`NoPluginError` will be raised.
If an error occurs while fetching streams, a :exc:`PluginError` will be raised.


Opening streams to read data
----------------------------

Now that you have extracted some streams we might want to read some data from
one of them. When you call `open()` on a stream, a file-like object will be
returned, which you can call `.read(size)` and `.close()` on.


.. code-block:: python

    >>> stream = streams["source"]
    >>> fd = stream.open()
    >>> data = fd.read(1024)
    >>> fd.close()

If an error occurs while opening a stream, a :exc:`StreamError` will be raised.


Inspecting streams
------------------

It's also possible to inspect streams internal parameters, go to
:ref:`Stream subclasses <api:Stream subclasses>` to see what attributes are available
for inspection for each stream type.

For example this is a :class:`HLSStream <stream.HLSStream>` object which
contains a `url` attribute.

.. code-block:: python

    >>> stream.url
    'https://video38.ams01.hls.twitch.tv/hls11/ ...'


Session object
--------------

The session allows you to set various options and is more efficient
when extracting streams more than once. You start by creating a
:class:`Streamlink` object:

.. code-block:: python

    >>> from streamlink import Streamlink
    >>> session = Streamlink()

You can then extract streams like this:

.. code-block:: python

    >>> streams = session.streams("https://twitch.tv/day9tv")

or set options like this:

.. code-block:: python

    >>> session.set_option("stream-timeout", 30)


See :func:`Streamlink.set_option` to see which options are available.
