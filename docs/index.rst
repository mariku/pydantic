pydantic
========

.. toctree::
   :maxdepth: 2

|pypi| |license|

Current Version: |version|

Data validation and settings management using python 3.6 type hinting.

Define how data should be in pure, canonical python; validate it with *pydantic*.

`PEP 484 <https://www.python.org/dev/peps/pep-0484/>`_ introduced type hinting into python 3.5,
`PEP 526 <https://www.python.org/dev/peps/pep-0526/>`_ extended that with syntax for variable annotation in python 3.6.

*pydantic* uses those annotations to validate that untrusted data takes the form you want.

Example:

.. literalinclude:: examples/example1.py

(This script is complete, it should run "as is")

What's going on here:

* ``id`` is of type int; the annotation only declaration tells pydantic that this field is required. Strings,
  bytes or floats will be coerced to ints if possible, otherwise an exception would be raised.
* ``name`` is inferred as a string from the default, it is not required as it has a default.
* ``signup_ts`` is a datetime field which is not required (``None`` if it's not supplied), pydantic will process
  either a unix timestamp int (e.g. ``1496498400``) or a string representing the date & time.
* ``friends`` uses python's typing system, it is required to be a list of integers, as with ``id`` integer-like objects
  will be converted to integers.

If validation fails pydantic with raise an error with a breakdown of what was wrong:

.. literalinclude:: examples/example2.py

Rationale
---------

So *pydantic* uses some cool new language feature, but why should I actually go an use it?

**no brainfuck**
    no new schema definition micro-language to learn. If you know python (and perhaps skim read the
    `type hinting docs <https://docs.python.org/3/library/typing.html>`_) you know how to use pydantic.

**plays nicely with your IDE/linter/brain**
    because pydantic data structures are just instances of classes you define; auto-completion, linting,
    :ref:`mypy <usage_mypy>` and your intuition should all work properly with your validated data.

**dual use**
    pydantic's :ref:`BaseSettings <settings>` class allows it to be used in both a "validate this request data" context
    and "load my system settings" context. The main difference being that system settings can can have defaults changed
    by environment variables and more complex objects like DSNs and python objects often required.

**fast**
    In `benchmarks <https://github.com/samuelcolvin/pydantic/tree/master/benchmarks>`_ pydantic is around twice as
    fast as `trafaret <https://github.com/tailhook/trafaret>`_. Other comparisons to cerberus, marshmallow,
    DRF, jsonmodels etc. to come.

**validate complex structures**
    use of recursive pydantic models and ``typing``'s ``List`` and ``Dict`` etc. allow complex data schemas to be
    clearly and easily defined.

**extendible**
    pydantic allows custom data types to be defined or you can extend validation with the `clean_*` methods on a model.


Install
-------

Just::

    pip install pydantic

pydantic has no dependencies except python 3.6+. If you've got python 3.6 and ``pip`` installed - you're good to go.

Usage
-----

PEP 484 Types
.............

pydantic uses ``typing`` types to define more complex objects.

.. literalinclude:: examples/ex_typing.py

(This script is complete, it should run "as is")

Choices
.......

pydantic uses python's standard ``enum`` classes to define choices.

.. literalinclude:: examples/choices.py

(This script is complete, it should run "as is")

Recursive Models
................

More complex hierarchical data structures can be defined using models as types in annotations themselves.

The ellipsis ``...`` just means "Required" same as annotation only declarations above.

.. literalinclude:: examples/recursive.py

(This script is complete, it should run "as is")

Error Handling
..............

.. literalinclude:: examples/errors.py

(This script is complete, it should run "as is")

Exotic Types
............

pydantic comes with a number of utilities for parsing or validating common objects.

.. literalinclude:: examples/exotic.py

(This script is complete, it should run "as is")

Model Config
............

Behaviour of pydantic can be controlled via the ``Config`` class on a model.

Here default for config parameter are shown together with their meaning.

.. literalinclude:: examples/config.py

.. _settings:

Settings
........

One of pydantic's most useful applications is to define default settings, allow them to be overridden by
environment variables or keyword arguments (e.g. in unit tests).

This usage example comes last as it uses numerous concepts described above.

.. literalinclude:: examples/settings.py

Here ``redis_port`` could be modified via ``export MY_PREFIX_REDIS_PORT=6380`` or ``auth_key`` by
``export my_api_key=6380``.

.. _usage_mypy:

Usage with mypy
...............

Pydantic works with `mypy <http://mypy-lang.org/>`_ provided you use the "annotation only" version of
required variables:

.. literalinclude:: examples/mypy.py

This script is complete, it should run "as is". You can also run it through mypy with::

    mypy --ignore-missing-imports --follow-imports=skip --strict-optional pydantic_mypy_test.py

Strict Optional
~~~~~~~~~~~~~~~

For your code to pass with ``--strict-optional`` you need to to use ``Optional[]`` or an alias of ``Optional[]``
for all fields with ``None`` default, this is standard with mypy.

Pydantic provides a few useful optional or union types:

* ``NoneStr`` aka. ``Optional[str]``
* ``NoneBytes`` aka. ``Optional[bytes]``
* ``StrBytes`` aka. ``Union[str, bytes]``
* ``NoneStrBytes`` aka. ``Optional[StrBytes]``

If these aren't sufficient you can of course define your own.

Required Fields and mypy
~~~~~~~~~~~~~~~~~~~~~~~~

The ellipsis notation ``...`` will not work with mypy, you need to use annotation only fields as in the example above.

.. warning::

   Be aware that using annotation only fields will alter the order of your fields in metadata and errors:
   annotation only fields will always come last, but still in the order they were defined.

To get round this you can use the ``Required`` (via ``from pydantic import Required``) field as an alias for
ellipses or annotation only.

.. include:: ../HISTORY.rst


.. |pypi| image:: https://img.shields.io/pypi/v/pydantic.svg
   :target: https://pypi.python.org/pypi/pydantic
.. |license| image:: https://img.shields.io/pypi/l/pydantic.svg
   :target: https://github.com/samuelcolvin/pydantic
