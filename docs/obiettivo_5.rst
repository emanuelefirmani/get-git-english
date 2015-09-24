.. _obiettivo_5:

Target 5: To unify two branches
###############################

Let's pass to an operation that you probably do very often: ``merge``. 
Compare the two images that we have seen, that is your ``repository``
before and after ``rebase``\ 

.. figure:: img/rebase-5-6.png

In the first on you can clearly see that ``development`` does not contain
the two contributions ``developer 1`` ``developer 2`` by your colleagues. Those two
``commits`` are not *reachable* from your branch. That is: going back along the history
starting from your ``development``branch, you will not go through those two ``commits``.

Now look at the second image, that is the history that you got after ``rebase``: 
now the two ``commits`` are *reachable* from
``development``.

It makes sense: you had done ``rebase`` purposely in order to align with 
your colleagues' work therefore, correctly, git made sure that your branch
*contain* their contributions as well.

``rebase`` and ``cherry-pick`` are not the only tools to be used to
*integrate* in your branch the content of others. On the contrar: one of the tools
that you will be using more often is ``merge``

``merge`` works exactly as you would expect: it melts two
``commits`` between them.

There are only 3 particularties that I guess it's worth to deepen. 
First is that git's  ``merge`` works tremendously fine.
Merit of git's storage model: during merges git should not being going crazy,
like SVN, to understand whether or not a delta has already been applied, 
because it starts from comparison of project's photos. 
But let's not go in the details: enjoy ``git merge``'s power and
forget all the difficulties that you always encountered with SVN.

The other two particularities are ``fast-forward`` and
``octopus merge``.

But you fin it better  to show them immediately with examples

``merge``
=========

Last photo of your ``repository`` is

.. figure:: img/rebase-6.png

Detach a branch from ``dev`` and add a couple of ``commits``

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

Very well. You reproduced again a quite common situation:
two ``branches``, on two divergent development lines, both containing contributions
that sooner or later we want to integrate.

For instance, suppose that it's you, after completing your bugfixing 
work in the proper branch, to ask your colleagues for integrating your
work in theirs. 

To integrate ``bugfix`` in ``development`` your colleagues might do 

.. code-block:: bash

    git checkout development
    git merge bugfix

.. figure:: img/merge-2.png

With ``git merge bugfix`` you ask git: "*provide me a ``commit``
containing everything that is in my current ``branch`` and add to it
all changes introduced by branch ``bugfix``*\ ".

Before running the merge, git looks into its ``Object Database`` and 
searches if exists already a ``commit`` containing both branches. Since it doesn't find it,
git creates it, melts the two file systems and then assigns both ``commits`` of origin 
as parents of the new ``commit``. In effect, the result is a new ``commit`` with two
parents. Note also that your branch's label, ``development`` moved on the new ``commit``. 
It shouldn't be a surprise: current
``branch`` is meant just to follow you, ``commit`` after ``commit``.

The ``fast-forward merge``
--------------------------

If you find correct this reasoning, you won't find it difficult to understand 
``fast-forward``. Test yourself; try to answer this question:

Starting from last state of your ``repository``

.. figure:: img/merge-2.png

what would happen if you moved on ``dev`` branch and asked for a
``merge`` with branch ``development``, that is, if you did ``git merge development``?

To answer this question, repeat the reasoning we have done in occasion
of the previous ``merge``: you are asking git "*provide me a  ``commit``
containing both my current branch ``dev`` and 
``development`` branch*\ ". git would examine ``commits`` in its database in order to
assure that a ``commit`` with these characteristics is already present.

And it would find it! Look at ``commit`` just pointed from 
``development`` branch: no doubt it contains ``development`` (by definition!); 
and since it's possible, going down through the history from ``development``, 
to reach ``dev``, no doubt as well that ``development`` contains already
the changes introduced from ``dev``. Therefore, it's that the``commit``
containing the ``merge`` between ``dev`` and ``development``. Do you confirm?

Allora, git non ha motivo per creare un nuovo ``commit`` e si limiterà a
spostarvi sopra la tua etichetta corrente.

Prova:

.. code-block:: bash

    git checkout dev
    git merge sviluppo

.. figure:: img/fast-forward.png

Prova a confrontare la storia prima e dopo il merge

.. figure:: img/fast-forward-2.png

Vedi cosa è accaduto? Che l'etichetta ``dev`` è stata *spinta in
avanti*.

Ecco: hai appena visto un caso di ``fast-forward``. Tieni a mente
questo comportamento: di tanto in tanto capita di averne a che fare,
soprattutto quando vuoi evitare che avvenga. Per esempio, in questa
occasione il ``fast-forward`` non è molto espressivo: si è creata una
storia nella quale risulta un po' difficile capire *quando* il ramo
``dev`` sia stato staccato. Non si vede nemmeno bene quando il ``merge``
sia stato effettuato, perché manca un ``commit`` con un commento tipo
``merge branch 'dev' into sviluppo``.

``fast-forward`` è un argomento cruciale nell'interazione con altri
``repository``. Ne parleremo nel paragrafo su ``push``.

Per adesso cerca solo di tenere a mente il concetto:

-  il ``merge`` di due ``branch`` è eseguito in ``fast-forward`` quando
   è possibile spostare il primo ramo sul secondo semplicemente
   spingengolo in avanti
-  il ``merge`` non può essere ``fast-forward`` quando i due ``branch``
   si trovano su linee di sviluppo divergenti

Un esempio potrebbe aiutarti a fissare il concetto

In questo ``repository``, un merge di ``bugfix`` su ``dev`` avverrà in
``fast-forward``

.. figure:: img/fast-forward.png

In quest'altro caso, un merge di ``sviluppo`` su ``bugfix`` non potrà
essere in ``fast-forward``, e risulterà in un nuovo ``commit``

.. figure:: img/merge-1.png

``octopus merge``
-----------------

E per chiudere l'argomento vediamo l'\ ``octopus merge``. Ma ci vorranno
pochi secondi, perché è una cosa di una semplicità sconcertante.

Guarda un ``commit`` nato da un ``merge``: non è diverso dagli altri
``commit`` se non per il fatto di avere due genitori invece di uno solo.

.. figure:: img/fast-forward.png

Ecco: su git il numero di genitori di un ``commit`` non è limitato a
due. In altre parole, puoi mergiare tra loro quanti ``branch`` vuoi, in
un colpo solo.

Guarda. Crea 4 ``branch`` qualsiasi


.. code-block:: bash

    git branch uno 
    git branch due 
    git branch tre 
    git branch quattro 

    git checkout uno
    touch uno && git add uno && git commit -m "uno" 
    
    git checkout due
    touch due && git add due && git commit -m "due" 
    
    git checkout tre
    touch tre&& git add tre && git commit -m "tre"
    
    git checkout quattro
    touch quattro && git add quattro && git commit -m "e quattro"

.. figure:: img/octopus-1.png

Bene. Hai 4 rami. Adesso chiedi a ``dev`` di mergiarli tutti, in un
colpo solo

.. code-block:: bash

    git checkout dev 
    git merge uno due tre quattro

.. figure:: img/octopus-2.png

Et voilà! Un ``merge`` di 4 ``branch``.

E ora qualcosa di completamente diverso. Vediamo un po' come si comporta
git con i server remoti.

:ref:`Indice <indice>` :: :ref:`Obiettivo 6: mettere il repository in rete <obiettivo_6>`
