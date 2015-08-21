.. _obiettivo_3:

Target 3: to create a branch
############################

With the ``checkout`` command you have learned to move from one ``commit``
to another.

All you need is the key of the ``commit`` where you want to land

.. code-block:: bash
    git log --oneline --all
    deaddd3 Here you have commit C
    2a17c43 Commit B, my second commit
    56674fb commit A, my first commit

.. figure:: img/repo1.png

If you wanted to go on `commit B` you should launch ``git checkout 2a17c43``,
in order to go back to `commit C` you should use instead ``git checkout deaddd3`` 
and so on.

But, we have to admit: to manage ``commit`` ``A``, ``B`` e ``C``
having to call them ``56674fb``, ``2a17c43`` and ``deaddd3`` is a unique
unconvinience. 

git solves the problem doing what every judicious programmer would do: 
since those numbers are pointers to objects, git allows to use variables 
to store their values. To assign a value to a variable is simple. If you
correctly executed last checkout, you should be on `commit C`.
Ok, now type:

.. code-block:: bash

    git branch bob 56674fb # here I'm using commit A's key

.. figure:: img/bob.png

Can you see the ``bob`` label just just in correspondence of ``commit A``? 
It stays to indicate that ``bob`` points precisely that ``commit``. 
Look at it as it wasa variable you have assigned the value of `commit A`
key.

When you create a label, if you don't specify a value, git will use the
key of the ``commit`` you are at the moment

.. code-block:: bash

    git checkout 300c737
    git branch piccio

.. figure:: img/piccio.png

Removal of a variable is equally trivial:

.. code-block:: bash

    git branch -d bob
    git branch -d piccio

You may have noted that git creates some of these variables by default. For
instance, in the above figures you see also the ``master`` variable,
pointed to ``B``

.. figure:: img/repo2.png

The ``master`` label allows you to go to that ``commit`` writing:

.. code-block:: bash

    git checkout master

Now be careful, because we are again in one of those occasions where the
knowledge of SVN gives only headaches: these labels in git are named 
``branch``. Repeat many times to yourself: in git a ``branch`` is not a
branch, it's a label, a pointer to a ``commit``, a variable that contains
the key of a ``commit``. Many git's behaviours that appear absurd and 
complicated become very simple if you avoid thinking of git's ``branch``
as something equivalent to SVN's branch.

It should now begin to be clear for you why many people say that "*branches
on git are very economical*\ ": of course! they are simple variables!

Create a new ``branch`` that we'lluse in next pages

.. code-block:: bash

    git branch dev

.. figure:: img/branch-dev.png

Note another thing: do you see that next to ``master`` SmartGit adds a 
green triangle placeholder? That symbol indicates that in this moment 
you are *attached* to the  ``master``  ``branch`` , because your latter
moving command was ``git checkout master``.

You might move on ``dev`` with

.. code-block:: bash

    git checkout dev

.. figure:: img/branch-dev2.png

Have you seen? The placeholder has moved on ``dev``.

That placeholder's name is ``HEAD``. By default, in fact, git always 
adds also an implicit ``branch`` , the ``HEAD`` pointer, always pointing
to the element of the ``repository`` where you are. ``HEAD`` follows
you, any movement you do. Other graphical editor use different 
representations to communicate where ``HEAD`` is.
``gitk``, for instance, shows in bold the ``branch`` where you are. Instead,
from command line, to know on which ``branch`` you are, you just run

.. code-block:: bash

    git branch  
    * dev
    master

The star suggests that ``HEAD`` is now pointing to ``dev``.

You should be not that much surprised veryfing that, despite you've 
chenged ``branch`` from ``master`` to ``dev`` your ``file system`` has 
not changed one iota: in effect both ``dev`` and ``master`` are 
pointing to the same identical ``commit``.

Nevertheless, you'll might wonder what can serve passing from one ``branch`` 
to another, if it doesn't produce effects on the project. 

The fact is that when you run the ``checkout`` of a ``branch``, you somehow
*attach* to the ``branch``; the ``branch``'s labl, in
other words, will start following you, ``commit`` after ``commit``.

Look: you are now on ``dev``. Make any modifications and commit

.. code-block:: bash

    touch style.css
    git add style.css
    git commit -m "Now I have also css"


.. figure:: img/branch-dev3.png

Have you seen what has happened? The label  ``dev`` has moved onward and
attached to your new ``commit``.

You might also wonder why git call those labels  ``branch``.
The reason is that, even though diverging development lines in git are 
not ``branch``, ``branch`` are normally used juest to give them a name.

Look at it in concrete. Go back to ``master`` and make some change.

.. code-block:: bash

    git checkout master
    touch angular.js
    git add angular.js
    git commit -m "angular.js rocks"

.. figure:: img/angular.png

As you could expect, the ``master`` label has moved one place onward, and 
points to your new ``commit``.

Now there's a certain equality between decelopment lines and ``branch``. 
Despite this, you'll want to keep always mentally separate the two concepts,
because this will make much easier the management of the history of your
project 

For instance: no doubt is ``commit`` with comment "*angular.js
rocks*\ " contained in ``branch master``, isn't it? But what about
``A`` and ``B``? Which ``branch`` do they belong?

Pay attention, because this another of those concepts that cause headache
to SVN's users, evrn to Mercurial's ones.

In effect, in order to answer this question, git's users make a different 
question: 

"*is ``commit A`` reachable from ``master``?*\ "

That is: if we walk backwards the history of  ``commit`` starting from
``master``, do we pass by ``A``? If the answer is *yes* we can state that 
``master`` contains changes introduced by ``A``.

One thing that Mercurial's and SVN's fans might find misleading is that,
since ``commit A`` is reachable also from ``dev`` , it belongs *both* to
``master`` and to ``dev``.

Think it over. If you treat ``branch`` like pointer to ``commit`` everything
should appear very linear to you.

:ref:`Indice <indice>` :: :ref:`Obiettivo 4: fare i giocolieri con i commit <obiettivo_4>`
