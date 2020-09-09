############
Introduction
############




This handbook is a bit different from the others.

Many texts I have read about git are concerned about introducing you to 
basic commands and leave the description of 
the internal model to more advanced chapters, or they skip it at all.

But I noticed that if you learn git starting with basic commands, you are 
risking to end up using it like a tool that is vaguely similar to SVN, but 
devoid of esotheric commands, whose behaviour is going to remain for you 
substantially obscure. 

Maybe you could have already observed that some of those that have learned
git well enough to use it daily, say that it was very difficult 
for them to understand what a ``rebase`` is, or that they still don't get 
exactly how to use \ ``index``.

My impression is that once you understand the internal model (that is 
surprisingly simple!), the whole git suddenly looks straightforward and
coherent: there's really no reason why ``rebase`` should be a mysterious
matter. 

This guide tries to explain git following a path that is opposite to 
the usually adopted one: you will start with an exposition of the internals and
you will find yourself learning at the same time both basic commands and 
advanced ones, in little time and without headaches.

But you will not learn all commands. Instead of showing you all possible
options, this guide aims to have you to understand the concepts and the 
underlying model, to give you tools in order to be autonomous when you
will want to deepen a subject on *man page* or when you will want to do something
extraordinary with your ``repository``. 

One last note: this guide is organised as a long tutorial. If you arm 
yourself with a terminal and you run each command, that you find
in this typographic form

.. code-block:: bash

    ls

you will be able to reproduce on your computer exactly every example
on the guide.


I'm not SVN's relative
######################

If you arrive from SVN, git presents one difficulty: it has many 
identical commands. But it's a superficial and deceitful similarity:
under the hood git is totally different. 

For this reason I suggest to refrain from the temptation of drawing parallels
with SVN, because they would be only misleading. You will find commands
like ``add``, ``checkout``, ``commit`` and ``branch`` that you will 
think to know. So: make a clean sweep of what you know, because in 
git those commands mean very different things.

If you try to understand git using SVN as a model, sometimes you may
be simply misleaded. For instance, would you believe that this repository
has 3 branches?

.. figure:: img/3-branches.png


   
Yes: 3 branches, not 2.

Or: would you believe that git, more than a code versioning system, could
be better described as a  "*peer-to-peer system
of key/value database on file system*\ "?

After reading the guide come back and read these two statements: I'm 
ready to bet that you will find them obvious. 

This is my suggestion: forget what you know about branch and changeset of SVN
and prepare yourself to completely new concepts. 
I'm convinced that you will find them much more powerful than SVN's ones.

You need only to be ready for a small cultural jump.

Setup
#####

Install `git <http://git-scm.com/downloads>`__.

Then configure it in order that it can recognize you

.. code-block:: bash

    git config --global user.name "Arialdo Martini"
    git config --global user.emal arialdomartini@gmail.com

If you are on Windows you can execute those commands in ``git bash``, a
terminal prepared for ``git``. On Linux and Mac OS X, after
installation, you will find your preferred terminal ready to use. 

If you want, install a graphical client as well. I suggest
`SmartGit <http://www.syntevo.com/smartgithg/>`__, free of charge for
OpenSource projects. Otherwise you can use the tool ``gitk`` that you
find bundled together with git installation.

Exciting. Let's go.

:ref:`Indice <indice>` ::  :ref:`Gli internal di git <internal>`
