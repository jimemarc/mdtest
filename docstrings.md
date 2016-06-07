# Writing docstrings for Sphinx

Before using Sphinx, we documented strings like this:

    """ Assert that multiples next-hops for a single static route are displayed
    on a the show ip route output using libvtysh.

    Arguments:
    node -- A modular framework node object that supports the vty shell.
    num_routes -- Number of routes expected on the output. Type: Integer.
    network -- Network that will be verified for next-hops. Ex:'100.0.0.0/24'.
    nexthop_init_1oct -- Initial value for the next-hop first octet.
                         Type: Integer(1-255).
    nexthop_final_1oct -- Final value for the next-hop first octet.
                          Type: Integer(1-255).
    nexthop_step_1oct -- Incremental step for the next-hop first octet.
                         Type: Integer.
    nexthop_4oct -- Host number for the next-hop. Type: Integer(1-255).
    step -- A modular framework step object to set a mark for the test step.
    """

To comply with Sphinx's tool that extracts information from docstrings,
several things must be taken into account:

-   No indentation change is allowed unless it is introduced by a Sphinx
    directive
-   Inside a paragraph, new lines are ignored.
-   The list of parmeters will be generated writing some formatting mark
    up.

## Packages

For packages and subpackages, write docstrings that describe the package
in `__init__.py`.  One liners are OK.

    """
    Brief description of package

    Long description of package.
    This line is in the same paragraph.
    """

## Modules

For each module, describe the module in the docstring :

    """
    Brief description of module

    Detailed description of module.
    """

## Functions

As a convention, the format we will use for docstrings is as follows:

    def function_name1(1st_param, 2nd_param, 3rd_param):
       """
       Brief description of function

       Detailed descrption of function

       :param 1st_param: <0-255> Description
       :param type_x 2nd_param: The description of this parameter is too long
          to fit in one line.  Use indentation.
       :param 3rd_param: Description
       :type 3rd_param: type_of_3rd_param
       :returns: Description of the return value
       :rtype: return_type
       """

> **Tip**
>
> - Use type information only in necessary cases.
> - Use parameter lists and return values where applicable.

> **Warning**
>
> The indentation in the multiline description of `2nd_param` is
> mandatory. Any number of spaces produces the same output.

This will generate the following html documentation:

<dl class="function">
<dt id="function_name1">
<code class="descname">function_name1</code><span class="sig-paren">(</span><em>1st_param</em>, <em>2nd_param</em>, <em>3rd_param</em><span class="sig-paren">)</span><a class="headerlink" href="#function_name1" title="Permalink to this definition">¶</a></dt>
<dd><p>Brief description of function</p>
<p>Detailed descrption of function</p>
<table class="docutils field-list" frame="void" rules="none">
<colgroup><col class="field-name">
<col class="field-body">
</colgroup><tbody valign="top">
<tr class="field-odd field"><th class="field-name">Parameters:</th><td class="field-body"><ul class="first simple">
<li><strong>1st_param</strong> – &lt;0-255&gt; Description</li>
<li><strong>2nd_param</strong> (<em>type_x</em>) – The description of this parameter is too long
to fit in one line.  Use indentation.</li>
<li><strong>3rd_param</strong> (<em>type_of_3rd_param</em>) – Description</li>
</ul>
</td>
</tr>
<tr class="field-even field"><th class="field-name">Returns:</th><td class="field-body"><p class="first">Description of the return value</p>
</td>
</tr>
<tr class="field-odd field"><th class="field-name">Return type:</th><td class="field-body"><p class="first last">return_type</p>
</td>
</tr>
</tbody>
</table>
</dd></dl>


## Elaborate docstrings

    """
    You can cross-reference functions with :py:func:`function_name1`,
    and modules with :py:mod:`my_other_module`.

    To show a hyperlink use
    `PEP-8 <https://www.python.org/dev/peps/pep-0008/>`_
    or just the link https://www.python.org/dev/peps/pep-0008/

    Use two astericks for text in **bold**,
    one for text in *italics*,
    double backquotes for ``code``.

    To show an example in the interactive shell write it as it is:

       >>> def foo(msj='Success'):
       ...     assert msj == 'Success', 'Foo Fail'

       >>> retry_wrapper('Hello World','Error msj',1,3)(foo)()
       Hello World

       >>> retry_wrapper('Hello World','Error msj',1,3)(foo)('fail')
       Hello World
       Error msj
       Waiting 1 seconds to retry
       Error msj
       Waiting 1 seconds to retry
       Error msj
       Waiting 1 seconds to retry
       Error msj
       Retry time of 3 seconds expired
       Traceback (most recent call last):
          File "<stdin>", line 1, in <module>
          File "retry_wrapper.py", line 19, in wrapper
             func(*args, **kwargs)
          File "<stdin>", line 2, in foo
       AssertionError: Foo Fail

    For more formatting marks see: http://www.sphinx-doc.org/en/stable/rest.html
    """

This will produce:

<dl class="function">
<dt id="function_name2">
<code class="descname">function_name2</code><span class="sig-paren">(</span><span class="sig-paren">)</span><a class="headerlink" href="#function_name2" title="Permalink to this definition">¶</a></dt>
<dd><p>“””
Short description</p>
<p>You can cross-reference functions with <a class="reference internal" href="#function_name1" title="function_name1"><code class="xref py py-func docutils literal"><span class="pre">function_name1()</span></code></a>,
and modules with <code class="xref py py-mod docutils literal"><span class="pre">my_other_module</span></code>.</p>
<p>To show a hyperlink use: :a_link:<a class="reference external" href="https://www.python.org/dev/peps/pep-0008/">https://www.python.org/dev/peps/pep-0008/</a></p>
<p>Use two astericks for text in <strong>bold</strong>,
one for text in <em>italics</em>,
backquotes for <cite>monospace</cite> or <code class="docutils literal"><span class="pre">like</span> <span class="pre">this</span></code>.</p>
<p>To show an example in the interactive shell write it as it is:</p>
<div class="highlight-sh"><div class="highlight"><pre><span></span>&gt;&gt;&gt; def foo<span class="o">(</span><span class="nv">msj</span><span class="o">=</span><span class="s1">'Success'</span><span class="o">)</span>:
...     assert <span class="nv">msj</span> <span class="o">==</span> <span class="s1">'Success'</span>, <span class="s1">'Foo Fail'</span>
...
</pre></div>
</div>
<div class="highlight-sh"><div class="highlight"><pre><span></span>&gt;&gt;&gt; retry_wrapper<span class="o">(</span><span class="s1">'Hello World'</span>,<span class="s1">'Error msj'</span>,1,3<span class="o">)(</span>foo<span class="o">)()</span>
Hello World
</pre></div>
</div>
<div class="highlight-sh"><div class="highlight"><pre><span></span>&gt;&gt;&gt; retry_wrapper<span class="o">(</span><span class="s1">'Hello World'</span>,<span class="s1">'Error msj'</span>,1,3<span class="o">)(</span>foo<span class="o">)(</span><span class="s1">'fail'</span><span class="o">)</span>
Hello World
Error msj
Waiting <span class="m">1</span> seconds to retry
Error msj
Waiting <span class="m">1</span> seconds to retry
Error msj
Waiting <span class="m">1</span> seconds to retry
Error msj
Retry <span class="nb">time</span> of <span class="m">3</span> seconds expired
Traceback <span class="o">(</span>most recent call last<span class="o">)</span>:
   File <span class="s2">"&lt;stdin&gt;"</span>, line 1, in &lt;module&gt;
   File <span class="s2">"retry_wrapper.py"</span>, line 19, in wrapper
      func<span class="o">(</span>*args, **kwargs<span class="o">)</span>
   File <span class="s2">"&lt;stdin&gt;"</span>, line 2, in foo
AssertionError: Foo Fail
<span class="s2">"""</span>
</pre></div>
</div>
</dd></dl>

## Global variables

For absolutely important global variables and constants write

    variable_name = 1000
    """This is an important global variable."""

which will produce:

<dl class="data">
<dt id="variable_name">
<code class="descname">variable_name</code><a class="headerlink" href="#variable_name" title="Permalink to this definition">¶</a></dt>
<dt>
<code class="descname">This is an important global variable.</code></dt>
<dd></dd></dl>
