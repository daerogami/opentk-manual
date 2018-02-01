To help you write cleaner code, OpenTK defines commonly used vectors, quaternions and matrices to extend the standard scalar types. They are generally nicer to handle than the arrays which the C API expects.

For example: OpenTK allows you to set the parameters of`GL.Color()`in various ways, the color cyan is used in this case.

```
GL.Color( 0.0f, 1.0f, 1.0f );
 
Vector3 MyColor = new Vector3( 0.0f, 1.0f, 1.0f );
GL.Color( MyColor );
 
// requires the System.Drawing library
GL.Color( Color.Cyan );
```

Function overloads like this can be found all over OpenTK.Graphics and OpenTK.Audio, where applicable.

**Overview**  
Everything listed below is type-safe, code using these types will work across all supported platforms.

**Vectors**

* Half-precision Floating-point
* * `Half`- new scalar type with 16 Bits of precision. More information can be found [here](https://web.archive.org/web/20140829080916/http://www.opentk.com/node/788).
  * `Vector2h`- 2-component vector of the type`Half`.
  * `Vector3h`- 3-component vector of the type`Half`.
  * `Vector4h`- 4-component vector of the type`Half`.
* Single-precision Floating-point
* * `Single`- standard scalar type with 32 Bits of precision.
  * `Vector2`- 2-component vector of the type`Single`.
  * `Vector3`- 3-component vector of the type`Single`.
  * `Vector4`- 4-component vector of the type`Single`.
* Double-precision Floating-point
* * `Double`- standard scalar type with 64 Bits of precision.
  * `Vector2d`- 2-component vector of the type`Double`.
  * `Vector3d`- 3-component vector of the type`Double`.
  * `Vector4d`- 4-component vector of the type`Double`.

**Quaternion**

* `Quaternion`- single-precision floating-point Quaternion using 4 components.
* `Quaterniond`- double-precision floating-point Quaternion using 4 components.

**Row-Major Matrices**

* `Matrix3d`- 3x3 double-precision Matrix.
* `Matrix4`- 4x4 single-precision Matrix.
* `Matrix4d`- 4x4 double-precision Matrix.

These are all value types \(struct\), not reference types. The implications of this can be[read here](https://web.archive.org/web/20140829080916/http://www.opentk.com/doc/chapter/2/rules_of_thumb).

**Casts**

For symmetry with established types, all OpenTK.Math types can be cast and allow serialization.

```
Vector2d TexCoord = new Vector2d( 0.2, 0.5 );
Vector2h HalfTexCoord = (Vector2h)TexCoord;
 
Vector3h Normal = (Vector3h)Vector3.UnitX;
```

**Instance and Static Methods**

The exact methods for each struct would be too numberous to list here, the function reference and inline documentation serve that purpose. It might not be obvious that some functionality is only available for instances of the structs, while others are static.

```
Vector3 Normal = Vector3.UnitX;
Normal.Normalize();
 
Vector4 TransformedVector = Vector4.Transform( Vector4.UnitX, Matrix4.Identity );
```



