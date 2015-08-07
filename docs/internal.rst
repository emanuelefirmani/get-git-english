.. _internal:

#############
The internals
#############

3 main differences
##################

Let's start with three git's features you should become familiar with.

1. **There's no server**: the repository is local. Most operations are 
   local and don't require network access. Also for this you will find
   git incredibly fast. 
2. **The project is indivisible**: git always works with the whole 
   source code of the project and not on single directories or files;
   between committing in the main directory or in a sub-directory there's
   no difference. The concept ``checkout`` of a single file or directory 
   doesn't exist. git assumes the project as the indivisible unit of work.
3. **git doesn't memorize files' changes**: git always saves files in 
   full. If you modify a single character of a 2 mega file, git memorizes
   in full the new version of the file.
   This is an important difference: SVN memorizes the differences and, 
   when required, rebuilds the file; git memorizes the file and, when 
   required, rebuilds the differences.

4 levels of *offline* operations
================================

Re the absence of a server i lied a little: as I already told and as you
will see later, git is a *peer-to-peer* system, and can interact with 
remote servers. Despite this, it remains a substantially local system.

In order to better understand how this can benefit you, try to see what 
follows: when a project's source code is hosted on a remote computer,
you have three ways to edit the code

1. You leave all the code on the remote computer and  **access it with 
   ssh to edit a single file**
2. You find the way to obtain **a copy of the single file** so that you
   can work on it in local more comfortably, and you leave all the rest
   on the remote computer
3. You find the way to obtain **a local copy of a whole file system 
   tree** and leave the rest of commits' history on the remote computer
4. You obtain **a local copy of the whole repository** with the whole 
   history of the project and you work locally

You may have noticed two things.

First, SVN and other versioning systems you are probably in the habit of,
operate at level 3.

Second, the 4 systems are listed in comfort order: in principle, when the 
material stays on the remote system, your work becomes more intricate, slow
and umcomfortable. SVN allows you to checkout a whole directory precisely
because in this way you find more comfortable to pass from one file to 
another without need to interact continually with the remote server.

git is even more extreme; it prefers that you have everything at hand on
your local computer; not just the single checkout, but the whole history
of the project, from first to last commit.

In fact, whatever you want to do, git normally asks to get a complete copy
of what is present on the remote server. But don't worry too much: git is
faster in getting the whole history of the project than SVN is in getting a
single checkout.

The storage model
=================

Let's pass now to third difference. And be prepared to know the true reason
why git is very fast going to substitute SVN as the new *de-facto* standard.

-  SVN memorizes the collection of various applied to files during time; when 
   required it rebuilds the present state; 
-  git memorizes files as they are, in full; when required it computes their 
   deltas.

If you want to avoid many headaches with git, the best suggestion you can follow
is to treat it as  **key/value database**.

Now go to your terminal and look in concrete.

Put yourself in the condition to have two empty files on your file system:

.. code-block:: bash

    mkdir project
    cd project 
    mkdir libs 
    touch libs/foo.txt 
    mkdir templates 
    touch templates/bar.txt

::

    /
    ├──libs
    |     └──foo.txt
    |
    ├──templates
            └──bar.txt

Let's decide to manage the project with git

.. code-block:: bash

    git init

Add the first file to git

.. code-block:: bash

    git add libs/foo.txt

With this command, git inspect the content of the file (it's empty!) and
memorizes it on its key/value database, named ``Object Database`` and
stored on the file system in the  ``.git`` hidden directory.

Since the ``blob-storage`` is a key/value database, git will try to
compute a key and a value for the file you have added. For the value it 
will use tha same content of the file; for the key , it will compute the 
SHA1 of the content (if you are curious, in case of an empty file it's
``e69de29bb2d1d6434b8b29ae775ad8c2e48c5391``)

So, in the ``Object Database`` git will save a ``blob`` object,
uniquely identifiable by its key (that, in absence of ambiguity, it's 
worth to shorten) 

.. figure:: img/blob.png
   
Now add the second file

.. code-block:: bash

    git add templates/bar.txt

Now, since ``libs/foo.txt`` and ``templates/bar.txt`` have the same identical 
content (they are both empty!), in the ``Object Database`` they are going to
be stored both in a single object: 

.. figure:: img/blob.png

   
As you can see, in the ``Object Database`` git has memorized only the file 
content, and not its name or its location. 

But of course we are very interested in file names and locations, aren't we? 
For this, in the ``Object Database``, git memorizes also other objects, 
named ``tree`` that serve just to memorize the content of the different 
directories and the file names.

In our case, we will have 3 ``tree``

.. figure:: img/tree.png

   
As any other object, also ``trees`` are memorized as key/value objects.

All these structures are collected in a container, called ``commit``.

.. figure:: img/commit.png

   
As you have probably guessed, a ``commit`` is nothing else that an element
of the key/value database chiave/valore, whose key is a SHA1, as for all
other objects, and whose value is a pointer to the project's ``tree`` ,
that is its key (together with some other information, like creation date, 
comment and author). In the end it's not that much complicated, isn't it?


So, the  ``commit`` is the present photography of the file system.

Now type

.. code-block:: bash

    git commit -m "commit A, my first commit"

You are saying to git:

*memorize in the repository, that is in the project's history, the commit 
I have prepared by several add*

Your ``repository``, seen by SmartGit, has now this aspect

.. figure:: img/first-commit.png

   
The line with the bullet that you see on the left represents the ``commit``
object. In the panel on the right, instead, you may see the ``commit`` key.

In general, unless we want to speak precisely of the internal model, like 
we are doing now, there's not a great nedd to represent the whole structure
of  ``blob`` eand ``tree`` that constitutes a ``commit``. In fact, after 
next paragraph we will start to represent the  ``commit`` like in the figure
above: with a simple bullet.

Even now, however, for you it should be clearer that inside a ``commit`` 
there is the whole photography of the project and a ``commit`` actually is
the minimal and indivisible unit of work.

L' ``index`` o ``staging area``
===============================

Sostanzialmente, non c'è molto altro che tu debba sapere del modello di
storage di git. Ma prima di passare a vedere i vari comandi, vorrei
introdurti ad un altro meccanismo interno: la ``staging area`` o
``index``. L'\ ``index`` risulta sempre misterioso a chi arriva da SVN:
vale la pena parlarne perché, quando saprai come funzionano l'``Object Database`` 
e l'\ ``index``, git non ti sembrerà più contorto e
incomprensibile; piuttosto, ne coglierai la coerenza e lo troverai
estremamente prevedibile.

L'\ ``index`` è una struttura che fa da cuscinetto tra il ``file system`` e
il ``repository``. È un piccolo buffer che puoi utilizzare per costruire il
prossimo ``commit``.

.. figure:: img/index1.png

   
Non è troppo complicato:

-  il ``file system`` è la directory con i tuoi file.
-  il ``repository`` è il database locale su file che conserva i vari
   ``commit``
-  l'\ ``index`` è lo spazio che git ti mette a disposizione per creare
   il tuo prossimo ``commit`` prima di registrarlo definitivamente nel
   ``repository``

Fisicamente, l'\ ``index`` non è molto diverso dal ``repository``:
entrambi conservano i dati nell'``Object Database``, usando le strutture che
hai visto prima.

In questo momento, appena dopo aver completato il tuo primo ``commit``,
l'\ ``index`` conserva una copia del tuo ultimo ``commit`` e si aspetta
che tu lo modifichi.

.. figure:: img/index2.png

Sul file system hai

::

    /
    ├──libs
    |     └──foo.txt
    |
    ├──templates
            └──bar.txt

Proviamo a fare delle modifiche al file ``foo.txt``

.. code-block:: bash

    echo "nel mezzo del cammin" >> libs/foo.txt

e aggiornare l'\ ``index`` con

.. code-block:: bash

    git add libs/foo.txt

Ecco un'altra differenza con svn: in svn ``add`` serve a mettere sotto 
versionamento un file e va eseguito una sola volta; in git serve
a salvare un file dentro l'``index`` ed è un'operazione che va ripetuta
ad ogni ``commit``.

All'esecuzione di ``git add`` git ripete quel che aveva già fatto prima:
analizza il contenuto di ``libs/foo.txt``, vede che c'è un contenuto che
non ha mai registrato e quindi aggiunge all'``Object Database`` un nuovo
``blob`` col nuovo contenuto del file; contestualmente, aggiorna il
``tree`` ``libs`` perché il puntatore chiamato ``foo.txt`` indirizzi il
suo nuovo contenuto

.. figure:: img/index3.png

Prosegui aggiungendo un nuovo file ``doh.html`` alla root del progetto

.. code-block:: bash

    echo "happy happy joy joy" > doh.html
    git add doh.html

Come prima: git aggiunge un nuovo ``blob`` object col contenuto del file
e, contestualmente, aggiunge nel ``tree`` "/" un nuovo puntatore
chiamato ``doh.html`` che punta al nuovo ``blob`` object

.. figure:: img/index4.png

Il contenitore di tutta questa struttura è sempre un oggetto ``commit``;
git lo tiene parcheggiato nella ``staging area`` in attesa che tu lo
spedisca al ``repository``. Questa struttura rappresenta esattamente la
nuova situazione sul file system: è nuovamente una fotografia
dell'intero progetto, ed include anche il file ``bar.txt``, nonostante
tu non lo abbia modificato. Per inciso: non dovresti preoccuparti per il
consumo di spazio perché, come vedi, per memorizzare ``bar.txt`` git sta
riutilizzando l'oggetto ``blob`` creato nel ``commit`` precedente, per
evitare duplicazioni.

Bene. Abbiamo quindi una nuova fotografia del progetto. A noi interessa,
però, che git conservi anche la storia del nostro file system, per cui
ci sarà bisogno di memorizzare da qualche parte il fatto che questa
nuova situazione (lo stato attuale dell'\ ``index``) sia figlia della
precedente situazione (il precedente ``commit``).

In effetti, git aggiunge automaticamente al ``commit`` parcheggiato
nella ``staging area`` un puntatore al ``commit`` di provenienza

.. figure:: img/index-and-first-commit.png

La freccia rappresenta il fatto che l'\ ``index`` è figlio del
``commit A``. È un semplice puntatore. Nessuna sopresa, se ci pensi;
git, dopo tutto, utilizza il solito, medesimo, semplicissimo modello
ovunque: un database chiave/valore per conservare il dato, e una chiave
come puntatore tra un elemento e l'altro.

Ok. Adesso committa

.. code-block:: bash

    git commit -m "Commit B, Il mio secondo commit"

Con l'operazione di commit si dice a git "*Ok, prendi l'attuale
``index`` e fallo diventare il tuo nuovo ``commit``. Poi restituiscimi
l'\ ``index`` così che possa fare una nuova modifica*\ "

Dopo il ``commit`` nel database di git avrai

.. figure:: img/index-and-second-commit.png

Una breve osservazione: spesso le interfacce grafiche di git omettono di
visualizzare l'\ ``index``. ``gitk``, per esempio, la visualizza solo se
ci sono modifiche da committare. Il tuo repository in ``gitk`` adesso
viene visualizzato così

.. figure:: img/gitk.png

Guarda tu stesso. Lancia

.. code-block:: bash

    gitk

Ricapitolando:

1. git memorizza sempre i file nella loro interezza
2. il ``commit`` è uno dei tanti oggetti conservati dentro il database
   chiave/valore di git. È un contenitore di tanti puntatori ad altri
   oggetti del database: i ``tree``, che rappresentano directory, 
   che a loro volta puntano ad altri ``tree`` (sotto-directory) o
   a dei ``blob`` (il contenuto dei file)
3. ogni oggetto ``commit`` ha un puntatore al ``commit`` padre da cui
   deriva
4. l'\ ``index`` è uno spazio di appoggio nel quale puoi costruire, a
   colpi di ``git add``, il nuovo ``commit``
5. con ``git commit``
   registri l'attuale ``index`` facendolo diventare il nuovo ``commit``.



.. figure:: img/index-add-commit.png



Bene: adesso hai tutta la teoria per capire i concetti più astrusi di
git come il ``rebase``, il ``cherrypick``, l'\ ``octopus-merge``,
l'\ ``interactive rebase``, il ``revert`` e il ``reset``.

Passiamo al pratico.

:ref:`Indice <indice>` ::  :ref:`I comandi di git <comandi>`
