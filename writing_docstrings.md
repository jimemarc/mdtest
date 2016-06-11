# Writing docstrings for Sphinx

To comply with Sphinx, a tool that extracts information from docstrings,
several considerations must be taken into account:

-   No indentation change is allowed unless it is introduced by a Sphinx directive
-   Inside a paragraph, new lines are ignored.
-   The list of parmeters will be generated writing some formatting markup.

## Packages

For packages and subpackages, write docstrings that describe the package
in `__init__.py`.  One liners are OK.

    """
    Brief description of package

    Long description of package.
    This line is in the same paragraph.
    """

## Modules

For each module, describe the module in the docstring:

    """
    Brief description of module

    Detailed description of module.
    """

## Functions

As a convention, the format for docstrings in topology common is as follows:

    def function_name1(param1, param2, param3):
       """
       Brief description of function

       Detailed descrption of function

       :param param1: <0-255> Description
       :param type_x param2: The description of this parameter is too long
          to fit in one line.  Use indentation.
       :param param3: Description
       :type param3: type_of_param3
       :returns: Description of the return value
       :rtype: return_type
       """

> ### Tip
> - Use type information only in necessary cases.
> - Use parameter lists and return values where applicable.

> ### Warning
> The indentation in the multiline description of `param2` is
> mandatory. Any number of spaces produces the same output.


## Elaborate docstrings
Sphinx understands reStructuredText, a lightweight markup language, and expands it for code API
documentation.  Some useful formatting is presented in the following docstring:

    """
    You can cross-reference functions with :py:func:`function_name1`,
    and modules with :py:mod:`my_other_module`.

    To show a hyperlink use:
    `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_
    or just the link: https://www.python.org/dev/peps/pep-0008/

    Use two asterisks for text in **bold**,
    one for text in *italics*,
    double backquotes for ``code``.

    To show an example in the interactive shell write it with one indentation and separated by
    blank lines:

       >>> def foo(msj='Success'):
       ...     assert msj == 'Success', 'Foo Fail'
       ...
       >>> retry_wrapper('Hello World','Error msj',1,3)(foo)()
       Hello World

       >>> retry_wrapper('Hello World','Error msj',1,1)(foo)('fail')
       Hello World
       Error msj
       Waiting 1 seconds to retry
       Error msj
       Retry time of 1 seconds expired
       Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
          File "retry_wrapper.py", line 19, in wrapper
             func(*args, **kwargs)
          File "<stdin>", line 2, in foo
       AssertionError: Foo Fail

    For more formatting markup see: http://www.sphinx-doc.org/en/stable/rest.html
    """


## Global variables

For absolutely necessary cases, add important global variables and constants to the API
documentation like this:

    variable_name = 1000
    """This is an important global variable."""
