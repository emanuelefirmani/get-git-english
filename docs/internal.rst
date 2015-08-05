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
   between committing in the main directory or in a sub-directory. The 
   concept ``checkout`` of a single file or directory doesn't exist. git
   assumes the project as the indivisible unit of work.
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
   history of thr project and you work locally

You may have noticed two things.

First, SVN and other versioning systems you are probably in the habit of,
operate at level 3.

Second, the 4 systems are listed in comfort order: in principle, when the 
material stays on the remote system, your work becomes more intrecate, slow
and umcomfortable. SVN allows you to checkout a whole directory precisely
because in this way you find more comfortable to pass from one file to 
another without the need to interact continually with the remote server.

git is even more extreme; it prefers that you have everything at hand on
your local computer; not just the single checkout, but the whole history
of the project, from first to last commit.

In fact, whatever you want to do, git normally asks to get a complete copy
of what is present on the remote server. But don't worry too much: git is
faster in getting the whole history of the project than SVN in getting a
single checkout.

Il modello di storage
=====================

Passiamo alla terza differenza. E preparati a conoscere il vero motivo
per cui git sta sostituendo molto velocemente SVN come nuovo standard
*de-facto*.

-  SVN memorizza la collezione dei vari delta applicati nel
   tempo ai file; all'occorrenza ricostruisce lo stato attuale;
-  git memorizza i file così come sono, nella loro interezza;
   all'occorrenza ne calcola i delta.

Se vuoi evitare tanti grattacapi con git, il miglior suggerimento che tu
possa seguire è di trattarlo come un **database chiave/valore**.

Passa al terminal e guarda nel concreto.

Mettiti nella condizione di avere 2 file vuoti sul file system:

.. code-block:: bash

    mkdir progetto
    cd progetto 
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

Decidiamo di gestire il progetto con git

.. code-block:: bash

    git init

Aggiungi il primo file a git

.. code-block:: bash

    git add libs/foo.txt

Con questo comando, git ispeziona il contenuto del file (è vuoto!) e lo
memorizza nel suo database chiave/valore, chiamato ``Object Database`` e
conservato su file system nella directory nascosta ``.git``.

Siccome il ``blob-storage`` è un database chiave/valore, git cercherà di
calcolare una chiave ed un valore per il file che hai aggiunto. Per il
valore git userà il contenuto stesso del file; per la chiave, calcolerà
lo SHA1 del contenuto (se sei curioso, nel caso di un file vuoto vale
``e69de29bb2d1d6434b8b29ae775ad8c2e48c5391``)

Per cui, nell'``Object Database`` git salverà un oggetto ``blob``,
univocamente identificabile dalla sua chiave (che, in assenza di
ambiguità, vale la pena di abbreviare)

.. figure:: img/blob.png
   
Adesso aggiungi il secondo file

.. code-block:: bash

    git add templates/bar.txt

Ora, siccome ``libs/foo.txt`` e ``templates/bar.txt`` hanno lo stesso
identico contenuto (sono entrambi vuoti!), nell'``Object Database`` entrambi
verranno conservati in un unico oggetto:

.. figure:: img/blob.png

   
Come vedi, nell'``Object Database`` git ha memorizzato solo il contenuto del
file, non il suo nome né la sua posizione.

Naturalmente, però, a noi il nome dei file e la loro posizione
interessano eccome. Per questo, nell'``Object Database``, git memorizza
anche altri oggetti, chiamati ``tree`` che servono proprio a memorizzare
il contenuto delle varie directory e i nomi dei file.

Nel nostro caso, avremo 3 ``tree``

.. figure:: img/tree.png

   
Come ogni altro oggetto, anche i ``tree`` sono memorizzati come
oggetti chiave/valore.

Tutte queste strutture vengono raccolte dentro un contenitore, chiamato
``commit``.

.. figure:: img/commit.png

   
Come avrai intuito, un ``commit`` non è altro che un elemento del
database chiave/valore, la cui chiave è uno SHA1, come per tutti gli
altri oggetti, e il cui valore è un puntatore al ``tree`` del progetto,
cioè la sua chiave (più un altro po' di informazioni, come la data di
creazione, il commento e l'autore). Non è troppo complicato, dopo tutto,
no?

Quindi, il ``commit`` è l'attuale fotografia del file system.

Adesso fai

.. code-block:: bash

    git commit -m "commit A, il mio primo commit"

Stai dicendo a git:

*memorizza nel repository, cioè nella storia del progetto, il commit che
ti ho preparato a colpi di add*

Il tuo ``repository``, visto da SmartGit, adesso ha questo aspetto

.. figure:: img/first-commit.png

   
La riga col pallino che vedi sulla sinistra rappresenta l'oggetto
``commit``. Nel pannello sulla destra, invece, puoi vedere la chiave del
``commit``.

In generale, a meno che non si debba parlare proprio del modello interno, 
come stiamo facendo adesso, non c'è una grande necessità di
rappresentare tutta la struttura di ``blob`` e ``tree`` che costituisce
un ``commit``. Difatti, dopo il prossimo paragrafo inizieremo a
rappresentare i ``commit`` come nella figura qui sopra: con un semplice
pallino.

Già da adesso, comunque, dovrebbe risultarti più chiaro il fatto che
dentro un ``commit`` ci sia l'intera fotografia del progetto e che, di
fatto, un ``commit`` sia l'unità minima ed indivisibile di lavoro.

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
