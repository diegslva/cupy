Using GPU(s) in CuPy
--------------------

.. currentmodule:: cupy

In this section, you will learn about the following things:

* Basics of CuPy


Basics of :class:`cupy.ndarray`
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

CuPy is a GPU array backend that implements a subset of NumPy interface.
The :class:`cupy.ndarray` class is in its core, which is a compatible GPU alternative of :class:`numpy.ndarray`.
CuPy implements many functions on :class:`cupy.ndarray` objects.
:ref:`See the reference for the supported subset of NumPy API <cupy_reference>`.
Understanding NumPy might help utilizing most features of CuPy.
`See the NumPy documentation for learning it <http://docs.scipy.org/doc/numpy/index.html>`_.

.. testcode::

   x_gpu = cupy.array([1, 2, 3, 4, 5])

The main difference of :class:`cupy.ndarray` from :class:`numpy.ndarray` is that the content is allocated on the device memory.
The allocation takes place on the current device by default.
The current device can be changed by :class:`cupy.cuda.Device` object as follows:

.. testcode::

   with cupy.cuda.Device(1):
       x_on_gpu1 = cupy.array([1, 2, 3, 4, 5])

Most operations of CuPy is done on the current device.
Be careful that it causes an error to process an array on a non-current device.

Moving a device array to the host can be done by :func:`cupy.asnumpy` as follows:

.. testcode::

   x_cpu = cupy.asnumpy(x_gpu)

It is equivalent to the following code:

.. testcode::

   x_cpu = x_gpu.get()

.. note::

   The *with* statements in these codes are required to select the appropriate CUDA device.
   If user uses only one device, these device switching is not needed.


The compatibility of CuPy with NumPy enables us to write CPU/GPU generic code.
It can be made easy by the :func:`cupy.get_array_module` function.
This function returns the :mod:`numpy` or :mod:`cupy` module based on arguments.
A CPU/GPU generic function is defined using it like follows:

.. testcode::

   # Stable implementation of log(1 + exp(x))
   def softplus(x):
       xp = cupy.get_array_module(x)
       return xp.maximum(0, x) + xp.log1p(xp.exp(-abs(x)))

