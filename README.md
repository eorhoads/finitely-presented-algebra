# finitely-presented-algebra

An implementation of finitely presented algebras into [SageMath](http://www.sagemath.org/). Creates two classes, `FinitelyPresentedAlgebra` and `FinitelyPresentedAlgebraElement`, and provides functions that implements algorithms for finite dimensional representations of finitely presented algebras.

## Installation & setup


To start, open Sage in the same directory as the file `finitely_presented_algebra.py`. Then we can import the file.

```python
sage: from finitely_presented_algebra import *
```

With this, we can now create our `FinitelyPresentedAlgebra` object. We do so with `FinitelyPresentedAlgebra(field, relations, names)`. The input `field` must be a computable field, and `relations` and `names` may be given as a tuple, array, or comma delimited string.

```python
sage: A = FinitelyPresentedAlgebra(QQ, 'b*a - a*b - 1', 'a, b')
sage: A
Finitely presented algebra over Rational Field with presentation <a, b | -1 - a*b + b*a>
```

Note that in this implementation variables must be injected before they can be used. We can also declare `names` in a similar way as to polynomial rings in Sage.

```python
sage: R.<x,y> = FinitelyPresentedAlgebra(QQ, 'x*y + y*x')
sage: R
Finitely presented algebra over Rational Field with presentation <x, y | x*y + y*x>
````

We are also equipped with the class `FinitelyPresentedAlgebraElement`. An instance of this class can be created by calling the element constructor of `FinitelyPresentedAlgebra` in the usual way.

```python
sage: R(x+ y)
x + y
```

When we have relations with two or fewer terms, we can reduce elements under the right conditions.

```python
sage: R(2*x*y + y*x + 1)
1 + x*y
```

## General functions

# `FinitelyPresentedAlgebraElement`

* `lift()`

Returns the value of `self` lifted to the base free algebra of its parent. The returned value will be a `FreeAlgebraElement`.

* `is_constant()`

Returns `True` if `self` is constant, and `False` otherwise. That is, whether `self` is contained in the base field of its parent.

# `FinitelyPresentedAlgebra`

* `ngens()`

Returns the number of generations of `self`.

* `nrels()`

Returns the number of relations of `self`.

* `base_ring()`

Returns the base field of `self`, which is `field` when `self` is initialized.

* `base_field()`

Same functionality as `base_ring()`.

* `free_algebra()`

Returns the base free algebra of `self`, which is a `FreeAlgebra` object over its base field with generators matching the generators of `self`.

* `gen(i)`

Returns the `i`-th generator of `self`, as a `FreeAlgebraElement`.

* `gens()`

Returns the generators of `self`, as a tuple.

* `rel(i)`

Returns the `i`-th relation of `self`, as a `FreeAlgebraElement`.

* `rels()`

Returns the relations of `self`, as a tuple.

* `one()`

Returns the multiplicative identity of `self`, which is equal to the multiplicative identity of its base field.

* `zero()`

Returns the additive identity of `self`, which is equal to the additive identity of its base field.

* `monoid()`

Returns the free monoid on the generators of `self`.

## Functions for representations

* `has_rep(n, restrict=None, force=False)`

Returns `True` if there exists an `n`-dimensional representation of `self`, and `False` otherwise.

```python
sage: R.<x,y> = FinitelyPresentedAlgebra(QQ, 'x*y + y*x')
sage: R.has_rep(2)
True
sage: A.<a,b> = FinitelyPresentedAlgebra(QQ, 'b*a - a*b - 1')
sage: A.has_rep(2)
False
```

The optional argument `restrict` may be used to restrict the possible images of the generators. To do so, `restrict` must be a tuple with entries of `None`, `'diagonal'`, `'lower'`, or `'upper'`. Its length must match the number of generators of `self`.

```python
sage: R.has_rep(2, restrict=[None, 'lower'])
True
sage: R.has_rep(2, restrict=['diagonal', 'upper'])
True
```

Use `force=True` if the function does not recognize the base field as computable, but the field is computable.

---

* `has_irred_rep(n, gen_set=None, restrict=None, force=False)`

Returns `True` if there exists an `n`-dimensional irreducible representation of `self`, and `False` otherwise. Of course, this function runs `has_rep(n, restrict)` to verify there is a representation in the first place, and returns `False` if not.

```python
sage: R.<x,y> = FinitelyPresentedAlgebra(QQ, 'x*y + y*x')
sage: R.has_irred_rep(2)
True
sage: R.has_irred_rep(3)
False
```

The argument `restrict` may be used equivalenty to its use in `has_rep()`.

```python
sage: R.has_irred_rep(2, restrict=[None, 'lower'])
True
sage: R.has_irred_rep(2, restrict=['diagonal', 'upper'])
False
```

The argument `gen_set` may be set to `'PBW'` or `'pbw'`, if `self` has an algebra basis similar to that of a Poincaré-Birkhoff-Witt basis.

```python
sage: A.<a,b> = FinitelyPresentedAlgebra(QQ, 'b*a - a*b - 1')
sage: A.has_irred_rep(2, gen_set='pbw')
False
```

Alternatively, an explicit generating set for the algorithm implemented by this function can be given, as a tuple or array of `FreeAlgebraElements`. This is only useful if the package cannot reduce the elements of `self`, but they can be reduced in theory.

```python
sage: A.<a,b> = FinitelyPresentedAlgebra(QQ, 'b*a - a*b - 1')
sage: A.has_irred_rep(2, gen_set = [A(a), A(b), A(a*b)])
False
```

Use `force=True` if the function does not recognize the base field as computable, but the field is computable.

---

* `is_rep(image, n, force=False)`

Returns `True` if the map generated by mapping the generators to the matrices defined in `image` is an `n`-dimensional representation of `self`, and `False` otherwise. The entries of `image` must be `n`-by-`n` matrices with entries in the algebraic closure of the base field of `self`. Its length must match the number of generators of `self.`

```python
sage: R.<x,y> = FinitelyPresentedAlgebra(QQ, 'x*y + y*x')
sage: M1 = [[0, 1], [1, 0]]; M2 = [[0, -1], [1, 0]]
sage: R.is_rep([M1, M2], 2)
True
```

Use `force=True` if the function does not recognize the base field as computable, but the field is computable.

---

* `is_irred_rep(image, n, force=False)`

Returns `True` if the map generated by mapping the generators to the matrices defined in `image` is an `n`-dimensional irreducible representation of `self`, and `False` otherwise. Like above, the entries of `image` must be `n`-by-`n` matrices with entries in the algebraic closure of the base field of `self`. Its length must match the number of generators of `self.`

```python
sage: R.<x,y> = FinitelyPresentedAlgebra(QQ, 'x*y + y*x')
sage: M1 = [[0, 1], [1, 0]]; M2 = [[0, -1], [1, 0]]
sage: R.is_irred_rep([M1, M2], 2)
True
```

Use `force=True` if the function does not recognize the base field as computable, but the field is computable.

## Citing

If you use `finitely_presented_algebra` in your research, please cite this repository. 

K. Rhoads. `rhoadskj/finitely-presented-algebra`: `finitely-presented-algebra` v1.0, 2019. [`https://github.com/rhoadskj/finitely-presented-algebra`](https://github.com/rhoadskj/finitely-presented-algebra).

```
@software{fpa19,
   AUTHOR = {Rhoads, Kyle\v{s}},
    TITLE = {\texttt{rhoadskj/finitely-presented-algebra}: \texttt{finitely-presented-algebra} v1.0},
     NOTE = {\url{https://github.com/rhoadskj/finitely-presented-algebra}},
     YEAR = {2019},
}
```
