The new half-precision floating-point type in OpenTK.Math is specifically designed for computer graphics. It is commonly used to reduce the memory footprint of floating-point textures, which can become huge at high resolutions. It is also useful for Vertex attributes, because it can help your vertex struct to stay within the 16 or 32 Byte boundary, which processors like.

Internally the 16 Bit are represented similar to IEEE floating-point numbers:

* 1 Sign Bit.
* 5 Exponent Bits.
* 10 Mantissa Bits. This is sometimes called the Significand, but called Mantissa in all OpenTK documentation.

It is important to understand that this type is a pure container to transfer data to or from the GPU, it does not support arithmetic operations. So if you want to use`Half`for Texture Coordinates or Normals, you have to perform any calculations with it in single- or double-precision first and then cast the final result to half-precision.

When using the`Half`type you should be aware of its poor precision:

* Casting Math.PI to`Half`will result in 3.1406...
* Numbers above 2048 and below -2048 will be rounded to the closest representable number.
* The further away the value of the floating-point number gets from 1.0, the worse the Epsilon gets. Try to stay within \[0.0 .. 1.0\] range for best accuracy.



