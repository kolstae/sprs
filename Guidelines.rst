==========
Guidelines
==========

This document describes development guidelines for this library.

Error policy
============

As encouraged by rust, contract violations should be handled by panicking,
whereas softer errors such as I/O errors should be dealt with using the
``Result`` type. Here we describe, in the particular context of sparse linear
algebra, what we consider to be contract violations. Any error not mentionned
here should be dealt with using the ``Result`` type.

Contract violation
------------------

- *Dimension mismatch* in linear algebra operations
- *Storage assumption violation* in functions that require a specific storage
  type.
- *Out of bounds indices* when constructing a sparse matrix. Generally speaking
  all indices should be in bounds for the prescriber shape.
- *Length mismatch in constructors*, such as an ``indptr`` length not
  corresponding to the matrix' dimension, different lengths for ``indices`` and
  ``data``, etc.
- *Wrong workspace length*

Code formatting
===============

A consistent code style is enforced automatically using rustfmt_. Before
opening a pull request, please ensure that your code has been formatted
using ``rustfmt`` on the latest stable Rust channel:

.. code-block:: console

  rustup default stable
  rustup component add rustfmt
  cargo fmt --all

.. _rustfmt: https://github.com/rust-lang-nursery/rustfmt

Reborrowing
===========

Some methods return views of their containers, eg ``CsMatBase::slice_outer``
returns a ``CsMatViewI``. However, in certain situations, mostly when
implementing iterators, we are calling these kind of methods on a view, and
need to take the lifetime of the view, not the lifetime of ``self`` in the
method call. To deal with this issue, the method should in fact be implemented
on the view type (on ``CsMatViewI`` in the example), with a ``_rbr`` suffix (
``CsMatViewI::slice_outer_rbr`` in the example), and the implementation on the
base type should simply call the view version (in the example, it should call
``self.view().slice_outer_rbr(range)``).
