.. _obiettivo_4:

Target 4: to juggle with ``commit``
###################################

As you have seen, git succeeds in storing the history of all file changes
without saving the differences. At the beginning of this guide I had
anticipated that SVN and git showed an opposite behaviour on this point: 

-  SVN stores deltas and, when required, rebuilds the present state;
-  git stores the present state and, when required, computes deltas. 

Therefore, when you look at the ``repository``

.. figure:: img/angular.png

and make reference to the ``dev``  ``commit``, you mean "*the whole project,
as it was photographed at the moment of that commit*\ ".

If you had the same situation on SVN, you would say that the ``dev`` commit
"*contains all changes applied to files, starting from the immediately 
preceding commit*\ ".

git computes the changes applied to files from one ``commit`` to another without
difficulty. For instance, you may get them with 

.. code-block:: bash

    git diff dev master

With ``git diff from to`` you're asking git "*what is the list of changes 
to files that I have to apply to ``from`` so that the project becomes 
identical to that photographed in ``to``*\ "?

With some imagination you can think that lines between ``commits`` represent
changes that you applied to files and directories to obtain a ``commit``. 
For instance, here in red I put in evidence the line that represents what
you did when you started from ``B`` and created the commit pointed by ``dev``.

.. figure:: img/angular-highlighted.png

If you remember, you had done 

.. code-block:: bash

    touch style.css
    git add style.css
    git commit -m "Now I have also css"

Then you might say that that red line represents the addition of file 
``style.css``.

Ok. Keep in mind this model. Now I'll show you one the craziest and
most versatile git's commands: ``cherry-pick``.

The Swiss Army knife: ``cherry-pick``
=====================================

``cherry-pick`` applies the changes introduced by one ``commit`` in a 
different point of the ``repository``.

Let's see it straightaway with an example. Starting from ``dev`` create a ``branch``
named ``experiment`` and add a  ``commit``

.. code-block:: bash

    git checkout dev
    git branch experiment
    git checkout experiment
    touch experiment
    git add experiment
    git commit -m "one commit with an experiment"

.. figure:: img/cherry-pick-1.png

Well: now take into account the change you have just applied starting from 
the last ``dev``'s ``commit`` and suppose you're interested in applying the same
change to the ``master`` branch as well. With the command ``cherry-pick`` you may ask
git to compute the changes introduced by your ``commit`` and apply them again
somewhere else , for instance exactly on ``master``

.. code-block:: bash

    git checkout master
    git cherry-pick experiment

.. figure:: img/cherry-pick-2.png

``cherry-pick`` "gets" the ``commit`` you indicate and applies it on
the ``commit`` where you are.

Do you begin to sense how it'll be possible for you to juggle with this 
tool?
I want to give you some cue.

To correct a bug in the middle of a branch
-----------------------------------

Starting from ``master`` create a branch ``feature`` and add 3
``commits`` to it

.. code-block:: bash

    git checkout -b feature    # shortcut to do branch + checkout
    
    touch feature && git add feature 
    git commit -m "feature"
    
    touch horrible-bug && git add horrible-bug
    git commit -m "horror and revulsion"
    
    touch other-feature && git add other-feature
    git commit -m "other feature"

    
.. figure:: img/bug-1.png

Oh, no! The second ``commit``, that with the comment "*horrore and
revulsion*\ " has been a huge mistake! Ah, if we only could rewrite the story
and remove it!

You can do it! The idea is to bring ``feature`` back in time, on
``master``, and to use ``cherry-pick`` to apply changes again one by one, 
careful to not apply the changes introduced by 
"*horror and revulsion*\ ". You only need to know the values of the
keys of the 3 ``commits``

.. code-block:: bash

    git log master..feature --oneline
    8f41bb8 other feature
    ec0e615 horror and revulsion 
    b5041f3 feature

(``master..feature`` is a sintax that allows to express a *range*
of ``commits``: we'll speakof it later on)

It is time to go back in time. Take place on ``master`` again

.. code-block:: bash

    git checkout master

and move ``feature`` on it, in such a way that it goes back to the position
where it was when you created it before the ``commits``

.. code-block:: bash

    git branch --force feature
    git checkout feature

.. figure:: img/bug-2.png

Perfect. You didn't revive exactly the past ``repository``, 
because your new 3 ``commits`` are still there, but the ``branches`` have
been positioned where they were before. You just have to take, with
``cherry-pick`` the only ``commits`` you're interested in. Take the first one,
that with the ``feature`` comment

.. code-block:: bash

    git cherry-pick b5041f3

.. figure:: img/bug-3.png

Can you see? The ``commit`` has been added to ``feature``, that moved foreward afterwards.
Go on with the second ``commit``, skipping the incriminated ``commit``

.. code-block:: bash

    git cherry-pick 8f41bb8

.. figure:: img/bug-4.png

Et voilà. You have rebuilt the development branch skipping the wrong ``commit``.
It remains an orphan branch, that is, with no ``branch``: it'll be 
removed sooner or later by the git's garbage collector. Moreover, usually 
orphan branches are not shown by graphical editors, therefore, normally, you
should see as starting situation the following one:

.. figure:: img/bug-1.png

and this one as final situation:

.. figure:: img/bug-5.png

Wow! You have the impression that git rewrote the history canceling
one ``commit`` inthe middle of a branch, don't you?

In fact,many people tell that git is able to rewrite the history, and that 
this behaviour is extremely dangerous. It should be a little clearer that it's 
not exactly s; git is extremely conservativè and when it allows you to manipulate 
``commits``, it does nothing but act in *append*, building *new* branches,
never removing what exists already.

Note also another thing: in the moment when you rebuilt the branch
bringing with ``cherry-pick`` one ``commit`` at a time, nothing was 
obliging you to apply again the ``commits`` in the same original order:
if desired, you could have applied them conversely, obtaining, in fact, a 
branch with the ``commit`` in reverse order. It's not something that often 
happens to need, but now you know that it's possible. 

To move a development branch
----------------------------

I want you to see another magic of ``cherry-pick``, in order to introduce
the ``rebase`` command.

Resume your``repository``.

.. figure:: img/rebase-1.png

Let's say you want carry on the development of your css, therefore
you'll do a new ``commit`` on ``dev``

.. code-block:: bash

    git checkout dev
    echo "a { color:red; }" >> style.css
    git commit -am "links are red"

Note: I have used the ``-a`` option of ``commit``, that implicitly executes  
``git add`` of any changed file. Keep in mind this option: 
it's very handy and you very often will find yourself using it.

.. figure:: img/rebase-2.png

Very good. Your css are ready to go to production. It's just a pity
that the ``dev`` tree lagged a little compared to ``master``,
that you might decide to account as the *production-ready* code.
After all, what could you do? While you were dealing with css, ``master``
went ahead and ``dev``, obviously, remained there where you created it.

It would be wonderful if we could detach the ``dev`` branch and move it *on*
``master``...

Don't you rememeber ``cherry-pick``? It's a case like the previous one:
but instead of travelling in the past you have to have a bit of fantasy 
and to imagine to travel in the future. It would be about bringing one by one
the two ``dev``'s ``commits`` and applying them again on last 
``master``'s ``commit`` (that, re ``dev``, is the future).

That is: using ``cherry-pick`` you could rewrite the history as if 
``dev``'s ``commits`` had been written *after*
``master``'s ``commits``.

If you did it, this would be the result

.. figure:: img/rebase-3.png

Compare it with the initial situation

.. figure:: img/rebase-2.png

You could interpret this way: the ``dev`` branch has been detached and implanted on ``master``.

Here: ``rebase`` is nothing different than a *macro* that authomatically executes 
a set of ``cherry-pick``, in order to avoid you to move one
``commit`` at a time from one branch to the other all.

Prova. Sul tuo ``repository``

.. figure:: img/rebase-2.png

esegui

.. code-block:: bash

    git rebase master

.. figure:: img/rebase-3.png

Voilà!

Hai chiesto a git: "*sposta il ramo corrente sulla nuova base
``master``*\ ".

Ricorda: ``rebase`` è del tutto equivalente a spostare uno per uno i
``commit`` con ``cherry-pick``. Solo, è più comodo.

Riesci ad immaginare dove potrebbe tornarti utile ``rebase``? Guarda,
provo a descriverti una situazione molto comune.

Inizia staccando un nuovo ramo da ``dev`` e registrando 3 nuovi
``commit``

.. code-block:: bash

    git checkout -b sviluppo
    touch file1 && git add file1 && git commit -m "avanzamento 1"
    touch file2 && git add file2 && git commit -m "avanzamento 2"
    touch file3 && git add file3 && git commit -m "avanzamento 3"

.. figure:: img/rebase-4.png

Bene. Adesso simuliamo una cosa che accade molto spesso nel mondo reale:
i tuoi colleghi, mentre tu lavoravi sui tuoi 3 ``commit`` hanno fatto
avanzare il ramo ``dev`` con i loro contributi


.. code-block:: bash

    git checkout dev
    touch dev1 && git add dev1 && git commit -m "developer 1"
    touch dev2 && git add dev2 && git commit -m "developer 2"

.. figure:: img/rebase-5.png

Questa situazione è sostanzialmente inevitabile, a causa della natura
fortemente non lineare del processo di sviluppo: è figlia diretta del
fatto che le persone lavorino in parallelo. ``rebase`` ti permette di
rendere la storia del ``repository`` nuovamente lineare. Come
nell'esempio precedente, il tuo ramo ``sviluppo`` è rimasto indietro
rispetto alle evoluzioni di ``dev``: usa ``rebase`` per staccarlo dalla
sua base e riattaccarlo più avanti

.. code-block:: bash

    git checkout sviluppo
    git rebase dev

Con ``git rebase dev`` stai chiedendo a git "*riapplica tutto il lavoro
che ho fatto nel mio ramo come se lo avessi staccato dall'ultimo commit
di sviluppo, ma non costringermi a spostare i commit uno per uno con
cherry-pick*\ "

Il risultato è

.. figure:: img/rebase-6.png

Vedi? Gli ultimi 3 ``commit`` introducono le stesse identiche modifiche
che avevi apportato tu nel tuo ramo, ma tutto appare come se tu avessi
staccato il ramo dall'ultima versione di ``dev``. Di nuovo:
apparentemente hai riscritto la storia.

Via via che prenderai la mano con git scoprirai di poter usare
``cherry-pick`` (ed altri comandi, che spesso sono una sorta di
combinazione di comandi di più basso livello) per manipolare i tuoi
``commit`` e ottenere risultati che sono letteralmente impossibili con
altri sistemi di versionamento:

-  invertire l'ordine di una serie di ``commit``
-  spezzare in due rami separati una singola linea di sviluppo
-  scambiare ``commit`` tra un ramo e l'altro
-  aggiungere un ``commit`` con un bugfix a metà di un ramo
-  spezzare un ``commit`` in due

e così via.

Questa versatilità non dovrebbe poi stupirti troppo: alla fine git non è
altro che un database chiave/valore e i suoi comandi non sono altro che
delle macro per creare oggetti e applicare l'aritmetica dei puntatori.

Per cui, tutto quel che può venirti in mente di fare con oggetti e
puntatori, tendenzialmente, puoi farlo con git.

Ganzo, no?

:ref:`Indice <indice>` :: :ref:`Obiettivo 5: unire due rami <obiettivo_5>`
