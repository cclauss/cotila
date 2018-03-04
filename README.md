# Cotila: a compile-time linear algebra system for C++

[![Build Status](https://travis-ci.org/calebzulawski/cotila.svg?branch=master)](https://travis-ci.org/calebzulawski/cotila)

Copyright 2018 Caleb Zulawski

## Overview

Cotila (**co**mpile-**ti**me **l**inear **a**lgebra) provides a set of linear algebra functions in C++ intended for use during compile time. 
All functions available in Cotila are ***constexpr***, meaning they can be used at compile-time to generate constants and lookup tables in a type-safe, transparent manner.

## Installation

Cotila is a header-only library.  Simply point your compiler to include the `include/` directory.

Code must be compiled with at least C++17 support to use Cotila.

## Documentation

The documentation can be generated by running `make docs`, which requires Doxygen.  It can also be viewed [online](https://calebzulawski.github.io/cotila/).

## Quickstart Guide

Cotila is designed to be simple to use once you understand the basics.

### Types

Cotila provides support for three types: **scalars**, **vectors**, and **matrices**.

**Scalars** are represented by fundamental types, such as `float` or `double`, as well as `std::complex`.  Cotila provides a variety of operations that manipulate scalar types.  In some cases, such as square roots, a standard library implementation already exists but it is not `constexpr`.  A simple example below:

```c++
// s contains 2
constexpr double s = cotila::sqrt(4.);
```

**Vectors** are represented by the `cotila::vector` class.  The `vector` class is a container for scalar types.  Additionally, `vector` is an aggregate class containing a single array and is constructed via [aggregate initialization](http://en.cppreference.com/w/cpp/language/aggregate_initialization).  If you are confused, some notes on aggregate initialization can be found in the next section.  A simple vector example:
```c++
// v2 contains [1, 2, 3]
constexpr cotila::vector<double, 3> v1 {{1., -2., 3.}};
constexpr auto v2 = cotila::abs(v1);
```

**Matrices** are represented by the `cotila::matrix` class.  Like the `vector` class, `matrix` is an aggregate class containing a single 2-dimensional array.  A `matrix` is initialized like a normal 2-dimensional array in C++ (i.e. row-major order).  A simple matrix example:
```c++
/*  m2 contains:
 *  1 3
 *  2 4
 */
constexpr cotila::matrix<double, 2, 2> m1 {{{1., 2.}, {3., 4.}}};
constexpr auto m2 = cotila::transpose(m1);
```

**Complex values** are not handled any differently, other than initialization:
```c++
/*  m1 contains:
 *  1 + 0i   2 + 1i
 *  3 - 1i   4 + 2i
 *
 *  m2 contains:
 *  1 + 0i   3 + 1i
 *  2 - 1i   4 - 2i
 */
constexpr cotila::matrix<std::complex<double>, 2, 3> m1 {{{1., 0.}, {2., 1.}}, {{3., -1.}, {4., -2.}}};
constexpr auto m2 = cotila::hermitian(m1);
```

### Aggregate Initialization

Aggregate objects can be initialized similarly to C structs by simply providing an initializer list with the values to initialize each member.  In C++, arrays can be initialized like so:
```c++
double arr[3] = {1., 2., 3.};
```
or
```c++
double arr[3] {1., 2., 3.};
```
The `cotila::vector` class contains a single array:
```c++
template<typename T, std::size_t N>
struct vector {
    T arr[N];
};
```
To initialize a `vector`, you must initialize the array member, which results in an extra set of braces:
```c++
vector<double, 3> v = {{1., 2., 3.}};
```
or
```c++
vector<double, 3> v {{1., 2., 3.}};
```
