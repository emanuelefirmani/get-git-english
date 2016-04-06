.. _obiettivo_5:

Target 5: unifying two ``branches``
###################################

Let's pass to an operation that you probably do very often: ``merge``. 
Compare the two images that we have seen, that is your ``repository``
before and after ``rebase``\ 

.. figure:: img/rebase-5-6.png

In the first you can clearly see that ``development`` does not contain
the two contributions ``developer 1`` and ``developer 2`` by your colleagues. Those two
``commits`` are not *reachable* from your branch. That is: going back along the history
starting from your ``development`` branch, you will not go through those two ``commits``.

Now look at the second image, that is the history that you got after ``rebase``: 
now the two ``commits`` are *reachable* from
``development``.

It makes sense: you had done ``rebase`` purposely in order to align with 
your colleagues' work therefore git correctly made sure that your ``branch``
*contains* their contributions as well.

``rebase`` and ``cherry-pick`` are not the only tools to be used to
*integrate* in your branch content of others. On the contrary: one of the tools
that you will be using more often is ``merge``

``merge`` works exactly as you would expect: it melts two
``commits`` one another.

There are only 3 particularities that I guess it's worth to deepen. 
First is that git's  ``merge`` works tremendously fine.
Merit of git's storage model: during merges git should not being going crazy,
like SVN, to understand whether or not a delta has already been applied, 
because it starts from comparison of project's photos. 
But don't let's go in the details: enjoy ``git merge``'s power and
forget all difficulties always encountered with SVN.

Other two particularities are ``fast-forward`` and
``octopus merge``.

But I find it better to show them straightaway with examples

``merge``
=========

Last photo of your ``repository`` is

.. figure:: img/rebase-6.png

Detach a ``branch`` from ``dev`` and add a couple of ``commits``

.. code-block:: bash

    git checkout -b bugfix dev

Note: here I used an even more concise form, equivalent to commands:

.. code-block:: bash

    git checkout dev
    git branch bugfix
    git checkout bugfix

Go further adding the two ``commits``

.. code-block:: bash

    touch fix1 && git add fix1 && git commit -m "bugfixing 1"
    touch fix2 && git add fix2 && git commit -m "bugfixing 2"

.. figure:: img/merge-1.png

Very well. You reproduced again quite a common situation:
two ``branches``, on two divergent development lines, both containing contributions
that sooner or later we want to integrate.

For instance, suppose that it's you, after completing your bugfixing 
work in the proper ``branch``, to ask your colleagues for integrating your
work in theirs. 

To integrate ``bugfix`` in ``development`` your colleagues might type

.. code-block:: bash

    git checkout development
    git merge bugfix

.. figure:: img/merge-2.png

With ``git merge bugfix`` you ask git: "*provide me a ``commit``
containing everything that is in my current ``branch`` and add to it
all changes introduced by branch ``bugfix``*\ ".

Before running the merge, git looks into its ``Object Database`` and 
searches if exists already a ``commit`` containing both ``branches``. Since it doesn't find it,
git creates it, melts the two file systems and then assigns both original ``commits`` 
as parents of the new ``commit``. In effect, the result is a new ``commit`` with two
parents. Note also that your branch's label, ``development`` moved on the new ``commit``. 
It shouldn't be a surprise: current
``branch`` is meant just to follow you, ``commit`` after ``commit``.

``fast-forward merge``
----------------------

If you find correct this argumentation, you won't find it difficult to understand 
``fast-forward``. Put yourself to the test; try to answer this question:

Starting from last state of your ``repository``

.. figure:: img/merge-2.png

what would happen if you moved on ``dev`` ``branc``h and asked for a
``merge`` with ``development`` ``branch``, that is, if you run ``git merge development``?

To answer this question, repeat the argument we have done in occasion
of the previous ``merge``: you are asking git "*provide me a  ``commit``
containing both my current branch ``dev`` and 
``development`` ``branch``*\ ". git would examine ``commits`` in its database in order to
assure that a ``commit`` with these characteristics is already present.

And it would find it! Look at ``commit`` just pointed from 
``development`` ``branch``: no doubt it contains ``development`` (by definition!); 
and since it's possible, going down through the history from ``development``, 
to reach ``dev``, no doubt either that ``development`` contains already
changes introduced from ``dev``. Therefore, it's just that the``commit``
containing ``merge`` between ``dev`` and ``development``. Do you confirm?

Then, git has no reason to create a new ``commit`` and it'll just move on current label on it.

Try:

.. code-block:: bash

    git checkout dev
    git merge development

.. figure:: img/fast-forward.png

Try to compare the history before and after ``merge``

.. figure:: img/fast-forward-2.png

Do you see what happened? The label ``dev`` has been *pushed forward*.

Here: you have just seen a case of ``fast-forward``. Keep in mind this 
behaviour: from time to time it may be the case to deal with it, especially
when you want to avoid that it happens. For instance, in this occasion
``fast-forward`` is not very expressive: a history has been created 
where it seems a little difficult to understand *when* the
``dev`` ``branch`` has been detached. You cannot even see well when ``merge``
has been done, because a ``commit`` with a comment like
``merge branch 'dev' into development`` is missing.

``fast-forward`` is a crucial subject in the interaction with other
``repositories``. We'll talk again about it in the paragraph on ``push``.

For the time being, simply try to keep in mind the concept:

-  ``merge`` of two ``branches`` is executed in ``fast-forward`` when 
    it is possible to move the first branch on the second simply by pushing it forward
-  ``merge`` may not be ``fast-forward`` when two ``branches``
   lay on divergent development lines

An example could help in fixing this concept

In this ``repository``, a ``merge`` of ``bugfix`` on ``dev`` will take place in
``fast-forward``

.. figure:: img/fast-forward.png

In this other case, a ``merge`` of ``development`` on ``bugfix`` will not be able to be 
in ``fast-forward``, and will result in a new ``commit``

.. figure:: img/merge-1.png

``octopus merge``
-----------------

In order to close the subject, let's see ``octopus merge``. It will take just few seconds,
because it's a thing of staggering simplicity

Look at a ``commit`` arisen from a ``merge``: it's not different than other
``commits`` if not for the fact to have two parents instead of one.

.. figure:: img/fast-forward.png

Here: on git the number of parents for ``commit`` is not limited to two. 
In other words, you may merge between them as many ``branches`` as you want, in
one shot.

Look. Create 4 whatever ``branches``


.. code-block:: bash

    git branch one 
    git branch two 
    git branch three 
    git branch four 

    git checkout one
    touch one && git add one && git commit -m "one" 
    
    git checkout two
    touch two && git add two && git commit -m "two" 
    
    git checkout three
    touch three && git add three && git commit -m "three"
    
    git checkout four
    touch four && git add four && git commit -m "and four"

.. figure:: img/octopus-1.png

Well. You have 4 ``branches``. Now ask ``dev`` for ``merging`` all of them, in one shot 

.. code-block:: bash

    git checkout dev 
    git merge one two three four

.. figure:: img/octopus-2.png

Et voilà! A ``merge`` of 4 ``branches``.

And now something completely different. Let's see how git behaves with
remote servers.

:ref:`Indice <indice>` :: :ref:`Obiettivo 6: mettere il repository in rete <obiettivo_6>`
