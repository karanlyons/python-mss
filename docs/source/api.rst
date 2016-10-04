=======
MSS API
=======

Classes
=======

``MSSBase`` is the parent's class for every OS implementation.

.. module:: mss.linux

.. class:: MSS

    .. method:: __init__(display=None)

        :param: str display: display to use.

        GNU/Linux initializations.


Methods
=======

.. module:: mss.base

.. class:: MSSBase

    .. method:: enum_display_monitors(force=False) -> list(dict)

        :param bool force: force rescan of monitors informations.
        :exception NotImplementedError: Subclasses need to implement this.

        Get positions of one or more monitors.
        If the monitor has rotation, you have to deal with it inside this method.

        This method has to fill :attr:`monitors` with all informations and use it as a cache:

        - ``monitors[0]`` is a dict of all monitors together;
        - ``monitors[N]`` is a dict of the monitor N (with N > 0).

        Each monitor is a dict with::

            {
                'left':   the x-coordinate of the upper-left corner,
                'top':    the y-coordinate of the upper-left corner,
                'width':  the width,
                'height': the height
            }


    .. method:: get_pixels(monitor) -> bytes

        :param dict monitor: monitor's informations generated by :attr:`enum_display_monitors()`.
        :exception NotImplementedError: Subclasses need to implement this.

        Retrieve screen pixels for a given monitor. 
        This method has to define :attr:`width` and :attr:`height`.
        It stocks pixels data into :attr:`image` (RGB) and returns it.


    .. method:: save(mon=0, output='monitor-%d.png', callback=lambda *x: True) -> generator

        :param int mon: the monitor's number.
        :param str output: the output's file name. ``%d``, if present, will be replaced by the monitor number.
        :param callable callback: callback called before saving the screenshot to a file. Takes the ``output`` argument as parameter.

        Grab a screenshot and save it to a file. This is a generator which returns created files.


    .. method:: to_png(data, output) -> None

        :param bytes data: raw pixels (RGBRGB...RGB) fom :attr:`get_pixels()`.
        :param str output: output's file name.
        :exception ScreenshotError: On error when writing ``data`` to ``output``.

        Dump data to the image file. Pure Python PNG implementation.


Attributes
==========

.. class:: MSSBase

    .. attribute:: image

        :getter: Raw pixels of a monitor.
        :setter: See :attr:`get_pixels`.
        :type: bytes


    .. attribute:: monitors

        :getter: The list of all monitors.
        :setter: See :attr:`enum_display_monitors()`.
        :type: list(dict)


    .. attribute:: width

        :getter: Width of a monitor.
        :setter: See :attr:`get_pixels()`.
        :type: int


    .. attribute:: height

        :getter: Height of a monitor.
        :setter: See :attr:`get_pixels()`.
        :type: int


Exception
=========

.. module:: mss.exception

.. exception:: ScreenshotError

    Base class for MSS exceptions.


Factory
=======

.. module:: mss

.. function:: mss() -> MSSBase

    Factory function to instance the appropriate MSS class.