.. moduleauthor:: Paul Ross <apaulross@gmail.com>
.. sectionauthor:: Paul Ross <apaulross@gmail.com>

.. PpLexer Tutorial

.. _cpip.tutorial.PpLexer:

****************
PpLexer Tutorial
****************

The PpLexer module represents the user side view of pre-processing. This tutorial shows you how to get going.

Setting Up
==========

.. _pplexer.tutorial.files:

Files to Pre-Process
--------------------

First let's get some demonstration code to pre-process. You can find this
at *cpip/demo/* and the directory structure looks like this::

    \---demo/
        |   cpip.py
        |
        \---proj/
            +---src/
            |       main.cpp
            |
            +---sys/
            |       system.h
            |
            \---usr/
                    user.h

In :file:`proj/` is some source code that includes files from :file:`usr/` and :file:`sys/`. 
This tutorial will take you through writing cpip.py to use PpLexer to
pre-process them.

First lets have a look at the source code that we are preprocessing.
It is a pretty trivial variation of a common them, but beware,
pre-processing directives abound!

The file :file:`demo/proj/src/main.cpp` looks like this:

.. highlight:: c

.. literalinclude:: demo/proj/src/main.cpp
    :language: c

That includes a file :file:`user.h` that can be found at :file:`demo/proj/usr/user.h`:

.. literalinclude:: demo/proj/usr/user.h
    :language: c

In turn that includes a file :file:`system.h` that can be found at
*demo/proj/sys/system.h*:

.. literalinclude:: demo/proj/sys/system.h
    :language: c

Clearly since the system is mandating language support and the user
is specifying French as their language of choice then you would not
expect this to write out "Hello World", or would you?

Well you are in the hands of the pre-processor and that is what
CPIP knows all about. First we need to create a :class:`PpLexer`.

Creating a PpLexer
------------------

This is the template that we will use for the tutorial, it just takes a
single argument from the command line ``sys.argv[1]``:

.. literalinclude:: demo/cpip_00.py
    :linenos:
    :language: python

Of course this doesn't do much yet, invoking it just gives::

    python cpip.py proj/src/main.cpp
    Processing: proj/src/main.cpp

We now need to import and create and :class:`PpLexer.PpLexer` object, and this
takes at least two arguments; firstly the file to pre-process, the secondly an
*include handler*. The latter is need because the C/C++ standards do not
specify how an ``#include`` directive is to be processed as that is
as an implementation issue. So we need to provide an defined implementation
of something that can find ``#include'd`` files.

CPIP provides several such implementations in the module
:mod:`IncludeHandler` and the one that does what, I guess,
most developers expect from a pre-processor is
:class:`IncludeHandler.CppIncludeStdOs`. This class takes at least two
arguments; a list of search paths to the user include directories and a list of
search paths to the system include directories. With this we can construct a
:class:`PpLexer` object so our code now looks like this:

.. literalinclude:: demo/cpip_01.py
    :language: python
    :emphasize-lines: 6-10

This still doesn't do much yet, invoking it just gives::

    python cpip.py proj/src/main.cpp
    Processing: proj/src/main.cpp

But, in the absence of error, shows that we can construct a
:class:`PpLexer`.

Put the PpLexer to Work
=======================
To get :class:`PpLexer` to do something, we need to make the call
to :func:`PpLexer.PpTokens()`. This function is a generator of preprocessing *tokens*.

Lets just print them out with this code:

.. literalinclude:: demo/cpip_02.py
    :language: python
    :emphasize-lines: 11,12

Invoking it now gives:

.. code-block:: console

    $ python cpip.py proj/src/main.cpp
    Processing: proj/src/main.cpp
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    ...
    PpToken(t="int", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=" ", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="main", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t="(", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="char", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=" ", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="*", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="*", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="argv", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=",", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t=" ", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="int", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=" ", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="argc", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=")", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="{", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="printf", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t="(", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t=""Bonjour tout le monde\n"", tt=string-literal, line=False, prev=False, ?=False)
    PpToken(t=")", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t=";", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="return", tt=identifier, line=True, prev=False, ?=False)
    PpToken(t=" ", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="1", tt=pp-number, line=False, prev=False, ?=False)
    PpToken(t=";", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)
    PpToken(t="}", tt=preprocessing-op-or-punc, line=False, prev=False, ?=False)
    PpToken(t="\n", tt=whitespace, line=False, prev=False, ?=False)

The PpLexer is yielding PpToken objects that are interesting in
themselves because they not only have content but the type of content
(whitespace, punctuation, literals etc.). A simplification is to change the
code to print out the token *value* by changing a line in the code from::

    print tok

To::

    print tok.t

To give:

.. literalinclude:: demo/cpip_03.out.txt
    :language: c

It is definately pre-processed and although the output is correct it is
rather verbose because of all the whitespace generated by the pre-processing
(newlines are always the consequence of pre-processing directives).

We can clean this whitespace up very simply by invoking
:func:`PpTokens.ppTokens()` with a suitable argument to reduce spurious
whitespace thus: ``myLex.ppTokens(minWs=True)``. This minimises the whitespace
runs to a single space or newline. Our code now
looks like this:

.. literalinclude:: demo/cpip_04.py
    :language: python
    :emphasize-lines: 11

Invoking it now gives:

.. literalinclude:: demo/cpip_04.out.txt
    :language: c

This is exactly the result that one would expect from pre-processing the
original source code.

And now for something Completely Different
==========================================
So far, so boring because any pre-processor can do the same, PpLexer
can do far more than this.
PpLexer keeps track of a large amount of significant pre-processing
information and that is available to you through the :class:`PpLexer` APIs.

For a moment lets remove the ``minWs=True`` from ``myLex.ppTokens()``
so that we can inspect the state of the PpLexer at every token (rather
than skipping whitespace tokens that might represent pre-processing
directives).

File Include Stack
------------------
Changing the code to this shows the ``include`` file
hierarchy every step of the way::

    for tok in myLex.ppTokens():
        print myLex.fileStack

Gives the following output:

.. code-block:: console

    $ python cpip.py proj/src/main.cpp
    Processing: proj/src/main.cpp
    ['proj/src/main.cpp', 'proj/usr/user.h']
    ['proj/src/main.cpp', 'proj/usr/user.h']
    ['proj/src/main.cpp', 'proj/usr/user.h', 'proj/sys/system.h']
    ['proj/src/main.cpp', 'proj/usr/user.h', 'proj/sys/system.h']
    ['proj/src/main.cpp', 'proj/usr/user.h', 'proj/sys/system.h']
    ['proj/src/main.cpp', 'proj/usr/user.h', 'proj/sys/system.h']
    ['proj/src/main.cpp', 'proj/usr/user.h']
    ['proj/src/main.cpp', 'proj/usr/user.h']
    ['proj/src/main.cpp', 'proj/usr/user.h']
    ['proj/src/main.cpp']
    ...

Conditional State
-----------------

Changing the code to this::

    for tok in myLex.ppTokens(condLevel=1):
        print myLex.condState

Produces this output:

.. literalinclude:: demo/cpip_05.out.txt
    :language: sh

State of the ``PpLexer`` After Pre-processing
===============================================

A more common use case is to query the ``PpLexer`` after processing the file. The following code example will:

* Capture all tokens as a Translation Unit and write it out with minimal whitespace [lines 11-16].
* Print out a text representation of the file include graph [lines 18-21].
* Print out a text representation of the conditional compilation graph [lines 23-26].
* Print out a text representation of the macro environment as it exists at the end of processing the Translation Unit [lines 28-31].
* Print out a text representation of the macro history for all macros, whether referenced or not, as it exists at the end of processing the Translation Unit [lines 33-36].

Here is the code, named :file:`cpip_07.py`:

.. literalinclude:: demo/cpip_07.py
    :language: python
    :linenos:

Invoking this code thus:

.. code-block:: console

    $ python3 cpip_07.py ../src/main.cpp

Gives this output:

.. literalinclude:: demo/cpip_07.out.txt
    :language: sh

This is simple to the point of crude as the ``PpLexer`` supplies a far richer data seam than just text.

File Include Graph interface is described here: :ref:`cpip.tutorial.FileIncludeGraph`

Summary
=======

There are several ways that you can inspect pre-processing with PpLexer:

* Supplying arguments to ``PpLexer.ppTokens()`` with arguments such as ``minWs`` or ``incCond``.
* Accessing the state of each token as it is generated such as ``tok.tt`` or ``tok.isCond``.
* Accessing the state of PpLexer as each token as it is generated or once all tokens have been generated such as PpLexer.condState.
* Creating PpLexer with a user specified behaviour. This is the subject of the next section.

Advanced PpLexer Construction
==================================

The ``PpLexer`` constructor allows you to change the behaviour of pre-processing is a number of ways, effectively these are hooks into pre-processing that can:

* Varying how ``#include``'d files are inserted into the Translation Unit.
* Pre-including header files.
* Changing the behaviour of ``PpLexer`` in unusual circumstances (errors etc.).
* Handling ``#pragma`` statements, in this way various compilers can be imitated.

Include Handler
------------------

When an ``#include`` directive is encountered a compliant implementation is required to search for and insert into the Translation Unit the content referenced by the payload of the ``#include`` directive.

The standard does not specify *how* this should be accomplished. In CPIP the *how* is achieved by an implementation of an :py:mod:`cpip.core.IncludeHandler`.

An Aside
^^^^^^^^^^^^^^^

It is entirely acceptable within the standard to have an ``#include`` system that does not rely on a file system at all. Perhaps it might rely on a database like this:

.. code-block:: c

    #include "SQL:spam.eggs#1284"

An include handler could take that payload and recover the content from some database rather than the local file system.

Or, more prosaically, an include mechanism such as this:

.. code-block:: c

    #include "http:://some.url.org/spam/eggs#1284"

That leads to a fairly obvious way of managing that ``#include`` payload.

Implementation
^^^^^^^^^^^^^^^^^^^

If you want to create a new include mechanism then you should sub-class the base class :py:class:`cpip.core.IncludeHandler.CppIncludeStd` [reference documentation: :ref:`cpip.ref.IncludeHandler`]. 

Sub-classing this requires implementing the following methods :

* ``def initialTu(self, theTuIdentifier):``
    Given an Translation Unit Identifier this should return a
    class FilePathOrigin or None for the initial translation unit.
    As a precaution this should include code to check that the stack
    of current places is empty.
    For example:

    .. code-block:: python
    
        if len(self._cpStack) != 0:
            raise ExceptionCppInclude('setTu() with CP stack: %s' % self._cpStack)

* ``def _searchFile(self, theCharSeq, theSearchPath):``
    Given an HcharSeq/Qcharseq and a searchpath this should return a class ``FilePathOrigin`` or None.

As examples there are a couple of reference implementations in :file:`cpip.core.IncludeHandler`:

* :py:class:`cpip.core.IncludeHandler.CppIncludeStdOs` - An implementation that behaves as most developers think the ``#include`` mechanism works.
* :py:class:`cpip.core.IncludeHandler.CppIncludeStringIO` - An implementation that recovers content from a dictionary of in-memory files. This is used a lot within CPIP for unit testing.

Pre-includes
------------

The PpLexer can be supplied with an ordered list of file like objects that are pre-include files. These are processed in order before the ITU is processed. Macro redefinition rules apply.

For example :file:`CPIPMain.py` can take a list of user defined macros on the command line. It then creates a list with a single pre-include file thus:

.. code-block:: python

    import io
    from cpip.core import PpLexer
    
    # defines is a list thus:
    # ['spam(x)=x+4', 'eggs',]
    
    myStr = '\n'.join(['#define '+' '.join(d.split('=')) for d in defines])+'\n'
    myPreIncFiles = [io.StringIO(myStr), ]
    # Create other constructor information here...
    myLexer = PpLexer.PpLexer(
                anItu, # File to pre-process
                myIncH, # Include handler
                preIncFiles=myPreIncFiles,
            )

Diagnostic
----------

You can pass in to ``PpLexer`` a diagnostic object, this controls how the lexer responds to various conditions such as warning error etc. The default is for the lexer to create a :class:`CppDiagnostic.PreprocessDiagnosticStd`.

If you want to create your own then sub-class the :py:class:`cpip.core.CppDiagnostic.PreprocessDiagnosticStd` class in the module :py:mod:`cpip.ref.CppDiagnostic`.

Sub-classing ``PreprocessDiagnosticStd`` allows you to override any of the following that might be called by the ``PpLexer``:

* ``def undefined(self, msg, theLoc=None):`` Reports when an 'undefined' event happens.
* ``def partialTokenStream(self, msg, theLoc=None):`` Reports when an partial token stream exists (e.g. an unclosed comment).
* ``def implementationDefined(self, msg, theLoc=None):`` Reports when an 'implementation defined' event happens.
* ``def error(self, msg, theLoc=None):`` Reports when an error event happens.
* ``def warning(self, msg, theLoc=None):`` Reports when an warning event happens.
* ``def handleUnclosedComment(self, msg, theLoc=None):`` Reports when an unclosed comment is seen at EOF.
* ``def unspecified(self, msg, theLoc=None):`` Reports when unspecified behaviour is happening, For example order of evaluation of '#' and '##'.
* ``def debug(self, msg, theLoc=None):`` Reports a debug message.

There are a couple of implementations in the :ref:`cpip.ref.CppDiagnostic` module that may be of interest:

* :py:class:`cpip.core.CppDiagnostic.PreprocessDiagnosticKeepGoing`: Sub-class that does not raise exceptions.
* :py:class:`cpip.core.CppDiagnostic.PreprocessDiagnosticRaiseOnError`: Sub-class that raises an exception on a ``#error`` directive.


Pragma
------

You can pass in a specialised handler for ``#pragma`` statements [default: None]. This shall sub-class :py:class:`cpip.core.PragmaHandler.PragmaHandlerABC` and can implement:

* The boolean attribute ``replaceTokens`` is to be implemented. If True then the tokens following the ``#pragma`` statement will be be macro replaced by the PpLexer using the current macro environment before being passed to this pragma handler.
* A method ``def pragma(self, theTokS):`` that takes a non-zero length list of ``PpTokens`` the last of which will be a newline token. Any token this method returns will be yielded as part of the Translation Unit (and thus subject to macro replacement for example).

Have a look at the core module :py:mod:`cpip.core.PragmaHandler` for some example implementations.
