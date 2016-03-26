.. _dailygit:

#########
Daily git
#########

This guide ends with a short set of small practical suggestions that you will find very useful using git  
day by day

Getting a copy of one ``repository``
####################################

So far you have seen how to create a ``repository`` from scratch and how to
do for populating an empty one using ``push``. Often (in fact, very often) you will find yourself 
having to start with a single copy of an existing ``repository`` .

To the aim you may use the command ``git clone`` with which you will get 
a complete local copy of the ``commits`` story of one
``repository``. After having cloned a remote ``repository`` , this will be 
authomatically added as ``remote`` under the default name
``origin``.

For instance, in order to get a ``clone`` of this guide, run 

.. code-block:: bash

    git clone https://github.com/arialdomartini/get-git.git
    cd get-git
    git remote
    origin

Remove a file
#############

Do you remind that for adding a file in ``index`` you have used
the ``git add`` command? See: when you remove from  ``file system`` a file 
already tracked by git, to include the removal in ``commit``
you have to delete the file also from ``index`` with


.. code-block:: bash

    git rm file\_name

You might find very cozy the ``-a`` option of ``commit``

.. code-block:: bash

    git -am "include add e rm"

which implicitly does ``add`` of changed files and ``rm`` of removed ones
before running ``commit``.

The ``detached head state``
##########################

Consider this ``repository``

.. figure:: img/bob.png

It's apparent that the last ``checkout`` command was
``git checkout bob``: we are  *hanging on* the label ``bob``.

Using a little more correct terminology, you might say "*``HEAD`` in
this moment points to ``bob``*\ ".

This ``HEAD`` is not a metaphor: there really is a ``HEAD`` variable 
whose content is a pointer to the  ``bob`` ``branch``. This
variable (as indeed all local and remote ``branches`` ) is
stored in the hidden directory ``.git``

.. code-block:: bash

    cat .git/HEAD
    ref: refs/heads/bob

The ``HEAD`` variable, among other things, allows git to update 
the ``branch`` it where you are, so that *it follows you*
``, when you run a commit``.

So: ``HEAD`` points to ``bob``. In turn ``bob`` points to the ``A``
``commit``. Toverify it, run

.. code-block:: bash

    cat .git/refs/heads/bob
    dd15c2bee7059de07c4d74cf5f264b906d332e30

Try to *detach* from ``branch`` ``bob``, staying always on the same
``commit``; that is, type ``checkout`` using directly the key of  ``A``
``commit``

.. code-block:: bash

    git checkout dd15c2bee7059de07c4d74cf5f264b906d332e30

    Note: checking out 'dbf9b91bac0bc93ab2979ca6a65bf2ac3dbc16ff'.

    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by performing another checkout.
    
    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -b with the checkout command again. Example:
    
    git checkout -b new_branch_name
    
    HEAD is now at dbf9b91... ** inside a code block doesn't work: removed

git complains a little. Better, it warns you that you're not *attached* to a
``branch``, so whatever change you could make will not impact the position of 
any ``branch`` . It also suggests to create one using command ``git checkout -b``.

If you repeat

.. code-block:: bash

    cat .git/HEAD
    dd15c2bee7059de07c4d74cf5f264b906d332e30

you find that ``HEAD`` is indeed pointing directly to
``commit`` and not to a ``branch``

The state where ``HEAD`` is not pointing to a ``branch`` is called
``detached head``.

Now, there is nothing particularly wrong in detaching from a 
``branch`` and entering the ``detached head state``: it happens to need it. 
But often it gives some headache, mainly if it's not realized having entered it.  
This is why git warns against it.

Should it happen to read that lengthy notice, don't be frightened: 
everything you will probably have to do is wondering if maybe you wanted to 
enter a ``branch`` instead.

Overwrite last ``commit``
#########################

Take the ``repository``

.. figure:: img/bug-5.png

and add a  ``commit`` to it

.. code-block:: bash

    echo something >> feature
    git commit -am "I ave added something"

.. figure:: img/amend-1.png

But no, what an impression! You have written "have" without h!

You may remedy *overwriting* your last ``commit`` with the 
``--amend`` option of ``commit``

.. code-block:: bash

    git commit -am "I have added something" --amend

.. figure:: img/amend-2.png

Now: there is nothing magical in what you have just seen: git, as a 
usual, did not *rewrite* the story. Try to visualize all 
``commits`` of the ``repository``, including those of orphan ``branches``
(SmartGit call them "*lost heads*\ ")

.. figure:: img/amend-3.png

Do you see? ``commit`` with the wrong comment is still there.

Let's try to imagine what git could have done behind the scenes 
when you have used the ``--amend``option: it went back to ``commit``, it recovered 
the same changes you had made and then he repeated 
``commit`` modifying the comment.

TRy to simulate it step: you were leaving from

.. figure:: img/amend-1.png

Go back by one ``commit``

.. code-block:: bash

    git checkout feature^1

.. figure:: img/amend-4.png

Recover the changes made in ``feature``, without committing them

.. code-block:: bash

    git cherry-pick feature --no-commit

and then commit them with the correct message

.. code-block:: bash

    git commit -am "I have added something"

.. figure:: img/amend-5.png

It  just remains for you to move on current ``commit`` 
the ``feature`` branch

.. code-block:: bash

    git branch -f feature HEAD

.. figure:: img/amend-6.png

and finally, run ``checkout`` of the ``branch``

.. code-block:: bash

    git checkout feature

.. figure:: img/amend-7.png

As you can see, the ``--amend`` option in another example of *macro*
commands built on smaller operations that you could also run
manually step by step, but that are so common that it's much more cozy
to link them to a dedicated command. 

You may use ``--amend`` not just tomodify the comment: you may
overwrite your last commit adding files that yiu had forgotten, 
correcting changes, and so on. As a matter of fact, it's a new 
``commit``, so there are no costraints for the kind of
corrections allowed.

Eliminating the last ``commit``
###############################

Start with the photo of the ``repository`` obtained by previous
paragraph 

.. figure:: img/amend-7.png

Imagine that you have considered that, after all, your last ``commit``
is wrong: you would like to remove it.

One thing you might do is moving the ``feature``  ``branch``to previous
``commit`` , to obtain

.. figure:: img/reset-4.png

Let's see step by step how to do

Start from

.. figure:: img/amend-7.png

You move on previous ``commit``

.. code-block:: bash

    git checkout HEAD^1

which means "*go to ``commit`` father of ``HEAD``*\ ", that is to
``commit`` preceding the one where you are now

.. figure:: img/reset-1.png

Now you can move ``feature`` to the point where you are: for doing that, you
may create a ``feature`` branch in the point where you are, overwriten the 
current position of ``feature`` with the ``-f`` option of ``branch``

.. code-block:: bash

    git branch -f feature HEAD

.. figure:: img/reset-2.png

Hiding orphan ``commits`` the result becomes apparent

.. figure:: img/reset-4.png

For sure you will agree wit me that it's a too complex
procedure for such a common need.

As a usual, git has a command that, behind the scenes, 
runs all these steps: ``git reset``. To telle the truth, ``reset`` 
is much more versatile and powerful.

``git reset`` moves ``HEAD`` to the point specified as argument.
You remember that ``HEAD`` is always your current``branch`` , don't you? 
Therefore, in other words,  ``reset`` allows to *move* your current ``branch``
to whatever other point of the ``repository``.

For instance, starting from

.. figure:: img/amend-7.png

you can *reset* your current ``branch`` to previous ``commit``
type

.. code-block:: bash

    git reset HEAD^1

.. figure:: img/reset-4.png

You are not limited to move the current ``branch`` on previous``commit``:
You can *reset it* wherever you want. For instance,
to move ``feature`` on master you can type

.. code-block:: bash

    git reset master

.. figure:: img/reset-5.png

You may also move the current branch from one development line to another

Starting from

.. figure:: img/reset-6.png

with

.. code-block:: bash

    git reset prod

you get

.. figure:: img/reset-7.png

Keep in mind a very important thing: ``reset`` does not involve only
moving ``branches`` on ``repositories`` butalso changes on the 
``file system``. The ``branch`` you are moving is in fact the current
one, that is, the one on which you run
``checkout``; in other words, when you run ``reset`` at the 
same time you are running the ``checkout`` of another``commit``.

.. figure:: img/index-add-commit.png

:ref:`Indice <indice>` :: :ref:`Risorse per approfondire  <proseguire>`
