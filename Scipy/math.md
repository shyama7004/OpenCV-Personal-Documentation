> I just wrote the code and told chat gpt  to convert it into informative doc.

### 1. **Number-Theoretic and Representation Functions**

- **`math.ceil(x)`**: Rounds `x` up to the nearest integer.
  ```python
  import math
  result = math.ceil(4.2)  # result is 5
  ```

- **`math.comb(n, k)`**: Computes the number of combinations (ways to choose `k` items from `n` without repetition).
  ```python
  result = math.comb(5, 2)  # result is 10
  ```

- **`math.copysign(x, y)`**: Copies the sign of `y` to `x`.
  ```python
  result = math.copysign(-3.4, 1.0)  # result is 3.4
  ```

- **`math.fabs(x)`**: Returns the absolute value of `x`.
  ```python
  result = math.fabs(-7.5)  # result is 7.5
  ```

- **`math.factorial(n)`**: Computes the factorial of `n`.
  ```python
  result = math.factorial(5)  # result is 120
  ```

- **`math.floor(x)`**: Rounds `x` down to the nearest integer.
  ```python
  result = math.floor(4.7)  # result is 4
  ```

- **`math.fmod(x, y)`**: Computes the remainder of `x / y`.
  ```python
  result = math.fmod(7.5, 2.5)  # result is 2.5
  ```

- **`math.frexp(x)`**: Breaks down `x` into its mantissa and exponent.
  ```python
  mantissa, exponent = math.frexp(8.0)  # mantissa is 0.5, exponent is 4
  ```

- **`math.fsum(iterable)`**: Accurately sums the values in an iterable.
  ```python
  result = math.fsum([0.1, 0.2, 0.3])  # result is 0.6
  ```

- **`math.gcd(a, b)`**: Finds the greatest common divisor of `a` and `b`.
  ```python
  result = math.gcd(8, 12)  # result is 4
  ```

- **`math.isclose(a, b, *, rel_tol=1e-09, abs_tol=0.0)`**: Checks if `a` and `b` are close within a tolerance.
  ```python
  result = math.isclose(0.001, 0.0011, rel_tol=0.01)  # result is True
  ```

- **`math.isfinite(x)`**: Checks if `x` is finite (not infinity or NaN).
  ```python
  result = math.isfinite(3.0)  # result is True
  ```

- **`math.isinf(x)`**: Checks if `x` is infinity.
  ```python
  result = math.isinf(math.inf)  # result is True
  ```

- **`math.isnan(x)`**: Checks if `x` is NaN (Not a Number).
  ```python
  result = math.isnan(float('nan'))  # result is True
  ```

- **`math.isqrt(n)`**: Returns the integer square root of `n`.
  ```python
  result = math.isqrt(16)  # result is 4
  ```

- **`math.lcm(a, b)`**: Finds the least common multiple of `a` and `b`.
  ```python
  result = math.lcm(4, 6)  # result is 12
  ```

- **`math.ldexp(x, i)`**: Computes `x * (2**i)`.
  ```python
  result = math.ldexp(1.5, 3)  # result is 12.0
  ```

- **`math.modf(x)`**: Splits `x` into its fractional and integer parts.
  ```python
  frac_part, int_part = math.modf(3.5)  # frac_part is 0.5, int_part is 3.0
  ```

- **`math.nextafter(x, y)`**: Returns the next floating-point value after `x` towards `y`.
  ```python
  result = math.nextafter(1.0, 2.0)  # result is 1.0000000000000002
  ```

- **`math.perm(n, k=None)`**: Computes the number of permutations of `k` items from `n`.
  ```python
  result = math.perm(5, 2)  # result is 20
  ```

- **`math.prod(iterable)`**: Computes the product of all elements in the iterable.
  ```python
  result = math.prod([2, 3, 4])  # result is 24
  ```

- **`math.remainder(x, y)`**: Computes the remainder of `x` with respect to `y`.
  ```python
  result = math.remainder(7.0, 2.0)  # result is 1.0
  ```

- **`math.trunc(x)`**: Truncates `x` to its integer part.
  ```python
  result = math.trunc(3.9)  # result is 3
  ```

- **`math.ulp(x)`**: Returns the value of the least significant bit of `x`.
  ```python
  result = math.ulp(1.0)  # result is 2.220446049250313e-16
  ```

### 2. **Power and Logarithmic Functions**

- **`math.cbrt(x)`**: Computes the cube root of `x`.
  ```python
  result = math.cbrt(27)  # result is 3.0
  ```

- **`math.exp(x)`**: Computes `e**x`.
  ```python
  result = math.exp(2)  # result is 7.38905609893065
  ```

- **`math.exp2(x)`**: Computes `2**x`.
  ```python
  result = math.exp2(3)  # result is 8.0
  ```

- **`math.expm1(x)`**: Computes `e**x - 1` accurately for small `x`.
  ```python
  result = math.expm1(0.00001)  # result is 1.0000050000166668e-05
  ```

- **`math.log(x, base=e)`**: Computes the logarithm of `x` with an optional base.
  ```python
  result = math.log(10, 10)  # result is 1.0
  ```

- **`math.log1p(x)`**: Computes the natural logarithm of `1+x`.
  ```python
  result = math.log1p(0.00001)  # result is 9.999950000333332e-06
  ```

- **`math.log2(x)`**: Computes the base-2 logarithm of `x`.
  ```python
  result = math.log2(8)  # result is 3.0
  ```

- **`math.log10(x)`**: Computes the base-10 logarithm of `x`.
  ```python
  result = math.log10(100)  # result is 2.0
  ```

- **`math.pow(x, y)`**: Computes `x**y`.
  ```python
  result = math.pow(2, 3)  # result is 8.0
  ```

- **`math.sqrt(x)`**: Computes the square root of `x`.
  ```python
  result = math.sqrt(16)  # result is 4.0
  ```

### 3. **Trigonometric Functions**

- **`math.acos(x)`**: Computes the arc cosine of `x`.
  ```python
  result = math.acos(0.5)  # result is 1.0471975511965979
  ```

- **`math.asin(x)`**: Computes the arc sine of `x`.
  ```python
  result = math.asin(0.5)  # result is 0.5235987755982989
  ```

- **`math.atan(x)`**: Computes the arc tangent of `x`.
  ```python
  result = math.atan(1)  # result is 0.7853981633974483
  ```

- **`math.atan2(y, x)`**: Computes the angle from the origin to the point `(x, y)`.
  ```python
  result = math.atan2(1, 1)  # result is 0.7853981633974483
  ```

- **`math.cos(x)`**: Computes the cosine of `x` radians.
  ```python
  result = math.cos(math.pi/3)  # result is 0.5
  ```

- **`math.dist

(p, q)`**: Computes the Euclidean distance between two points `p` and `q`.
  ```python
  result = math.dist([0, 0], [3, 4])  # result is 5.0
  ```

- **`math.hypot(*coordinates)`**: Computes the Euclidean norm (distance from origin).
  ```python
  result = math.hypot(3, 4)  # result is 5.0
  ```

- **`math.sin(x)`**: Computes the sine of `x` radians.
  ```python
  result = math.sin(math.pi/2)  # result is 1.0
  ```

- **`math.tan(x)`**: Computes the tangent of `x` radians.
  ```python
  result = math.tan(math.pi/4)  # result is 1.0
  ```

### 4. **Angular Conversion**

- **`math.degrees(x)`**: Converts `x` radians to degrees.
  ```python
  result = math.degrees(math.pi)  # result is 180.0
  ```

- **`math.radians(x)`**: Converts `x` degrees to radians.
  ```python
  result = math.radians(180)  # result is 3.141592653589793
  ```

### 5. **Hyperbolic Functions**

- **`math.acosh(x)`**: Computes the inverse hyperbolic cosine of `x`.
  ```python
  result = math.acosh(1.5)  # result is 0.9624236501192069
  ```

- **`math.asinh(x)`**: Computes the inverse hyperbolic sine of `x`.
  ```python
  result = math.asinh(1.5)  # result is 1.1947632172871094
  ```

- **`math.atanh(x)`**: Computes the inverse hyperbolic tangent of `x`.
  ```python
  result = math.atanh(0.5)  # result is 0.5493061443340549
  ```

- **`math.cosh(x)`**: Computes the hyperbolic cosine of `x`.
  ```python
  result = math.cosh(0)  # result is 1.0
  ```

- **`math.sinh(x)`**: Computes the hyperbolic sine of `x`.
  ```python
  result = math.sinh(0)  # result is 0.0
  ```

- **`math.tanh(x)`**: Computes the hyperbolic tangent of `x`.
  ```python
  result = math.tanh(1)  # result is 0.7615941559557649
  ```

### 6. **Special Functions**

- **`math.erf(x)`**: Computes the error function at `x`.
  ```python
  result = math.erf(1.0)  # result is 0.842700792949715
  ```

- **`math.erfc(x)`**: Computes the complementary error function at `x`.
  ```python
  result = math.erfc(1.0)  # result is 0.157299207050285
  ```

- **`math.gamma(x)`**: Computes the Gamma function at `x`.
  ```python
  result = math.gamma(5.0)  # result is 24.0
  ```

- **`math.lgamma(x)`**: Computes the natural logarithm of the Gamma function at `x`.
  ```python
  result = math.lgamma(5.0)  # result is 3.1780538303479458
  ```
