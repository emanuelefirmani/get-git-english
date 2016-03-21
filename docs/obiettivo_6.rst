.. _obiettivo_6:

Target 6: placing the ``repository`` on the network
###################################################

So far you have interacted only with your local ``repository`` , but I had
anticipated that git is a *peer-to-peer* system.

In general, this means that your ``repository`` is a node that can
become part of a network and exchange informations with other nodes, 
that is with other ``repositories``.

A part from your local ``repository``, any other ``repository``
--no matter if it's on GitHub, on a company's server or simply in a different 
directory of your computer-- for git it's a 
``remote``.

To link your local ``repository`` to a ``remote`` it needs just to
give git the address of the remote ``repository`` with the command
``git remote`` (of course, you need also read and write permissions on ``remote``)

To make things simple, let's make a concrete example without 
involving external servers and the Internet; create another ``repository`` 
somewhere on your very computer 

.. code-block:: bash

    cd ..
    mkdir remote-repo
    cd remote-repo
    git init

In this case, in the directory of your project, the remote ``repository``
will be reachable through the path ``../remote-repo`` or with its absolute path.
But, more commonly, you wil deal with remote ``repositories`` that are reachable, depending on 
the protocol that is used, with addresses like

-  ``https://azienda.com/repositories/cool-project2.git``
-  ``git@github.com:johncarpenter/mytool.git``.

For instance, the ``repository`` of this guide has the address
 
-  ``git@github.com:arialdomartini/get-git-english.git``.

Very often it also happens that access to a ``remote`` requires an
authentication. In this case, usually, it's used a user/password pair
or a ssh key.

Go back to your project

.. code-block:: bash

    cd ../project

Well. Add to the ``remote`` list the just created ``repository`` , 
indicating to git whatever name and the address of the ``remote``

.. code-block:: bash

    git remote add foobar ../remote-repo

Very good. You have linked your ``repository`` to another node. You are
officially in a *peer-to-peer* network of ``repositories``. From this moment,
when you want to make reference to that remote ``repository``, you will use 
the name ``foobar``.

The name is necessary because, differently from SVN that has the concept of
*central server*, in git you may be linked to whatever number of remote
``repositories`` at the same time, therefore you will assign a unique identifier to each of them.

There are two things that you fundamentally may do with a ``remote``:
to align with its content or ask it for aligning with you.

Two commands are available: ``push`` and ``fetch``.

With ``push`` you can *deliver* a set of ``commits`` to the remote ``repository``.
With ``fetch`` you can *receive them* from the remote ``repository``.

Both ``push`` and ``fetch``, actually, allow that your ``repository``
and the ``remote`` exchange labels. And, indeed, you have also other commands 
available. But let's go step by step: let's start to see in concrete how 
the communication between a ``repository``and a ``remote`` works.

Delivering a branch with ``push``
=================================

At the moment the ``remote`` that you named ``foobar`` is a completely empty ``repository``:
you just created it. Your local ``repository`` , instead, contains many
``commits`` and many ``branches``:

.. figure:: img/local-1.png

Try to ask the remote ``repository`` for giving you the ``commits`` and the
``branches`` which has available and which you don't have. If you don't indicate a
specific ``branch`` , the remote ``repository`` will try to give you all of them.
In your case the ``remote`` is empty, therefore it shouldn't give back to you anything

.. code-block:: bash

    git fetch foobar

In fact. You don't receive anything. 

Try instead to deliver the branch
``experiment``

.. code-block:: bash

    git push foobar experiment
    Counting objects: 14, done. 
    Delta compression using up to 4 threads. 
    Compressing objects: 100% (8/8), done. 
    Writing objects: 100% (14/14), 1.07 KiB \| 0 bytes/s, done.
    Total 14 (delta 3), reused 0 (delta 0) To ../remote-repo
    [new branch] experiment -> experiment

Wow! Something happened! Among all the response messages, at this moment the 
most interesting is the last one 

.. code-block:: bash

    [new branch] experiment -> experiment

I help you to interpret what has happened:

-  with ``git push foobar experiment`` you asked git for sending
   ``foobar`` the ``experiment`` branch 
-  to execute the command git took into consideration your branch 
   ``experiment`` and drew the list of all ``commits`` reachable from 
   that branch (as a usual: they are all the ``commits``
   which you may find starting from ``experiment`` and following backward 
   in time any path you may go through)
-  git has then contacted the remote ``repository`` ``foobar`` to know
   which of those ``commits`` were not present remotely
-  after that, it created a packet with all the necessary ``commits`` ,
   delivered them and asked the remote ``repository`` to add them
   to its own database
-  the ``remote`` has placed its ``branch`` ``experiment``
   so that it pointed exactly the same ``commit`` pointed on your local
   ``repository`` . The ``remote`` didn't have that ``branch``, therefore it created it.

Now try to visualize the remote ``repository``

.. figure:: img/remote-1.png

Do you see? The ``remote`` has not become a copy of your ``repository``:
it contains only the ``branch`` that you sent it.

You may verify that the 4 ``commits`` really are all and only the 
``commits`` that you had in local on the ``experiment`` branch.

Even on your local ``repository`` something happened. try to visualize it

.. figure:: img/push-1.png

Look look! It seems a new ``branch``, called
``foobar/experiment``, has been added. and it also seems that it's a little particular
``branch``, because the interface is concerned to draw it in a different colour.

Try to delete that ``branch``

.. code-block:: bash

    git branch -d foobar/experiment
    error: branch 'foobar/experiment' not found.

It cannot br deleted. git says that ``branch`` doesn't exist. Uhm.
That label has really something particular!

The fact is that ``branch`` is not on your ``repository``: it's on 
``foobar``. git has added a ``remote branch`` in order to allow you to 
keep track of the fact that on ``foobar`` the ``branch`` ``experiment``
is just pointing to that ``commit``.

``remote branches`` are a sort of reminder that allow you to understand
where are the ``branches`` on remote ``repositories`` you are linked with.

It's one of those subjects that may result less clear to new git users, 
but if you think of it, the concept is not difficult at all. With the 
``remote branch`` called ``foobar/experiment`` git is simply tellin you 
that the ``branch`` ``experiment`` on the  ``foobar`` ``repository`` is
in correspondence of that ``commit``.

As well as you can't delete that ``branch`` you can not even move it directly.
The only manner to gain direct control on that ``branch``is to access directly 
the ``foobar`` ``repository``.

But you have an indirect way to control delivering with ``push`` an update of the 
``experiment`` ``branch``; we had seen before that the``push`` request is always 
accompanied by the request of update of the position of your ``branch``.


Before trying with a concrete example, I'd like to recall your attention on 
a verty important aspect, that you will have to het used to: while you were 
reading these lines, a colleague of yours could have added some ``commit`` 
just on his ``branch`` ``experiment`` on his remote ``repository``, and 
you wouldn't know anything, because your ``repository`` is not linked in 
real time its ``remotes``, but synchronizes only when you interact with 
proper commands. Therefore, the ``commit`` pointed by ``foobar/experiment`` 
has to be meant as the last known position of the ``experiment``  ``branch``
on ``foobar``.

Receiving uodates with ``fetch``
================================

Let's try and simulate this last case. 
Change `foobar` as if a colleague of yours is working on ``experiment``. 

That is: add a ``commit`` on the ``experiment`` branch of ``foobar``

.. code-block:: bash

    cd ../repo-remoto
    touch x
    git add x
    git commit -m "a contribution from your colleague" 

Here you have the final result on ``foobar``

.. figure:: img/push-2.png

Go back to your local``repository`` and let's see what has chenged

.. code-block:: bash

    cd ../project

.. figure:: img/push-1.png

In fact. It has not changed anything at all. Your local``repository`` 
continues saying that the ``experiment`` branch on ``foobar`` stays at
"*a commit with an experiment*\ ". And you know very well that it's not true!
``foobar`` has gone forward, and your ``repository`` doesn't know it.

All this is coherentwith what I said before: your
``repository`` is not linked in real time with its ``remote``; matching 
is just on command.

Then ask your ``repository`` for matching with ``foobar``. You
may ask for an update on a single branch or on all of them.
Usually it's chosen the second way.

.. code-block:: bash

    git fetch foobar
    remote: Counting objects: 3, done. remote:
    Compressing objects: 100% (2/2), done. remote: Total 2 (delta 1),
    reused 0 (delta 0) Unpacking objects: 100% (2/2), done. 
    From ../repo-remoto
    e5bb7c4..c8528bb experiment -> foobar/experiment

Something has arrived.

Look again at the new local ``repository``. (In order to simplify our life,
let's start to take advantage from an option present in every git's graphic 
interface: the capability of visualizing a single branch andhiding al the 
others, so that final result can be simplified) 

.. figure:: img/push-3.png

Carefully look at what has happened: your ``experiment`` branch
didn't move at all. If you check, your 
``file system`` hasn't absolutely changed as well. Just your local
``repository`` has been updated: git added there a new 
``commit``, the same remotely added; at the same time, git has also 
updated  the ``foobar/experiment`` position, in order to communicate 
that "*according with latest available information, last position of 
the branch ``experiment`` recorded on ``foobar`` is this*\ ".

This is the way with which normally git allows you to know that 
someone continued his work an a remote ``repository`` .

Another important remark: ``fetch`` is not equivalent to 
``svn update``; only your local ``repository`` is synced to
the remote one; your ``file system`` has not changed! This generally means that
``fetch`` is a very safe operatuon: even though you should sync
with a dubious quality ``repository``, you can rest easy, 
because the operation will never do the 
``merge`` on your code without your explicit intervention.

If instead you really want to include the remotely introduced changes in 
*your* work, you could use the ``merge`` command.

.. code-block:: bash

    git merge foobar/experiment

.. figure:: img/push-4.png

Do you recognize the kind of ``merge`` that resulted? Yes, a
``fast-forward``. INterpret it this way: your  ``merge`` has been a
``fast-forward`` because while your colleague was working the branch 
has not been modified by anyone else; your colleague has been the only one 
who added contributions, and development has been linear.

This is such a common case that you will want to avoid doing
``git fetch`` followed by ``git merge``: git offers the 
``git pull`` command, which executes the two commands together.

Therefore, instead of

.. code-block:: bash

    git fetch foobar
    git merge foobar/experiment

you should have run

.. code-block:: bash

    git pull foobar experiment

We can extend the diagram of te interactions between git commands and
its environments adding the ``remote`` column and the action of
``push``, ``fetch`` e ``pull``

.. figure:: img/push-fetch.png

Non linear development
======================

Let's try to complicate the situation. I would like to show a case that
will contnously happen: two developers are working on a branch at the same time,
on two separated ``repositories`` . It usuallyhappens that at the moment when you 
will want to send your new ``commits``to ``remote``  , you discover that, 
in the meantime, someone on the remote
``repository`` changed the``branch``.

Start to simulate your colleague's work progress, adding
a ``commit`` on its ``repository``

.. code-block:: bash

    cd ../repo-remoto
    touch progress && git add progress
    git commit -m "a new commit of your colleague"

.. figure:: img/collaborating-1.png

(En passant, note this: on the remote ``repository`` there's no indication
of your ``repository``; git is an asymmetric peer-to-peer system)

Go back to your ``repository``

.. figure:: img/push-4.png

Like before: as soon as yo don't explicitly ask for an alignment with
``fetch`` your ``repository`` doesn't know anything of your new ``commit``.

By the way, this is one of the remarkable git's features: being
compatible with the strongly non linear nature of development activities.
Think it over: when two developers works on a single branch,
SVN requires that every ``commit`` is preceded by an ``update``; that is,
in order to record a change the developer has to integrate preventively the 
other developer's work. You cannot run a 
``commit`` if you beforehand don't integrate your colleague's ``commits`` . 
git, on this viewpoint, is less demanding: developers mayduverge locally, 
even working on the same ``branch``; the decision if and how to integrate their work 
may be intentionally and indefinitely moved on in time.

Anyway: embrace the strongly non linear git's nature and, purposely ignoring that there
could have been progresses on the remote ``repository`` , proceed locally without delay 
with your new ``commits``

.. code-block:: bash

    cd ../progetto
    touch my-contribution && git add my-contribution
    git commit -m "a new commit of mine"

.. figure:: img/collaborating-2.png

Let's assess again the situation on what I have just described:

-  your ``repository`` doesn't know about the new ``commit`` recorded on
   ``foobar`` and continues to see a non up to date situation
-  starting with the same ``commit`` "*a contribution from your colleague*\ "
   you and the other developer have recorde two completely indipendent ``commits``.
   
Having worked concurrently on the same branch, with two potentially incompatible ``commits`` is
if you think of it, a little like working concurrently on the same file with potentially incompatible changes:
when the two results will be put together, we can expect that a conflict is reported. 

And infact it's just like this. The confict arises at the moment when 
we try to sync the two ``repositories``. For example: try to send 
your branch on ``foobar``

.. code-block:: bash

    git push foobar experiment
    To ../repo-remoto ! [rejected]
    experiment -> experiment (fetch first) 
    error: failed to push some refs to '../repo-remoto' 
    hint: Updates were rejected because the remote contains work that you do 
    hint: not have locally. This is usually caused by another repository pushing 
    hint: to the same ref.  You may want to first integrate the remote changes 
    hint: (e.g., 'git pull ...') before pushing again. 
    hint: See the 'Note about fast-forwards' in 'git push --help' for details.

Rejected. Failed. Error. It's more than evident that the operation has not 
been successful. And we could expect it. With
``git push foobar experiment`` you had asked ``foobar`` for completing two operations:

-  saving in its database all yours ``commits`` that are not yet remotely present
-  moving its  ``experiment`` label so that it points the same``commit`` that is pointed locally

Now: about first operation there would have been no problem. 
But about the second one git sets a supplemental costraint:
the remote ``repository`` will move its label only on condition that the 
operation can be completed with a ``fast-forward``, that is, only on 
condition that it's not necessary to execute ``merges``. 
Or, said in different word: a ``remote`` accepts a ``branch`` 
only if the operation doesn't create diverging development lines.

``fast-forward`` is mentioned just on the last line of the error message


.. code-block:: bash

    hint: **See the 'Note about fast-forwards'** in 'git push --help'
    for details.<br/

In the same message git provides a tip: it suggests to try to ``fetch``. Let's try

.. code-block:: bash

    git fetch foobar

.. figure:: img/collaborating-3.png

The situation should be clear at a glance. You can see that the two development lines are diverging. 
The position of the two branches helps in understanding where you are locally and where your colleague is 
on the ``remote`` ``foobar``.

Resta solo da decidere cosa fare. A differenza di SVN, che di fronte a
questa situazione avrebbe richiesto necessariamente un merge in locale,
git ti lascia 3 possibilità

-  **andare avanti ignorando il collega**: puoi ignorare il lavoro del
   tuo collega e proseguire lungo la tua linea di sviluppo; certo, non
   potrai spedire il tuo ramo su ``foobar``, perché è incompatibile col
   lavoro del tuo collega (anche se puoi spedire il tuo lavoro
   assegnando alla tua linea di sviluppo un altro nome creando un nuovo
   ``branch`` e facendo il ``push`` di quello); comunque, il concetto è
   che non sei costretto ad integrare il lavoro del tuo collega;
-  **``merge``**: puoi fondere il tuo lavoro con quello del tuo collega
   con un ``merge``
-  **``rebase``**\ puoi riallinearti al lavoro del tuo collega con un
   ``rebase``

Prova la terza di queste possibilità. Anzi, per insistere sulla natura
non lineare di git, prova a far precedere alla terza strada la prima. In
altre parole, prova a vedere cosa succede se, temporaneamente, ignori il
disallineamento col lavoro del tuo collega e continui a sviluppare sulla
tua linea. È un caso molto comune: sai di dover riallinearti, prima o
poi, col lavoro degli altri, ma vuoi prima completare il tuo lavoro. git
non ti detta i tempi e non ti obbliga ad anticipare le cose che non vuoi
fare subito

.. code-block:: bash

    echo modifica >> mio-contributo
    git commit -am "avanzo lo stesso"

.. figure:: img/collaborating-4.png

Benissimo. Sei andato avanti col tuo lavoro, disallineandoti ancora di
più col lavoro del tuo collega. Supponiamo tu decida sia arrivato il
momento di allinearsi, per poi spedire il tuo lavoro a ``foobar``.

Potresti fare un ``git merge foobar/experiment`` ed ottenere questa
situazione

.. figure:: img/collaborating-5.png

Vedi? Adesso ``foobar/experiment`` potrebbe essere spinto in avanti (con
un ``fast-forward``) fino a ``experiment``. Per cui, a seguire, potresti
fare ``git push foobar``.

Ma invece di fare un ``merge``, fai qualcosa di più raffinato: usa
``rebase``. Guarda nuovamente la situazione attuale

.. figure:: img/collaborating-3.png

Rispetto ai lavori su ``foobar`` è come se tu avessi staccato un ramo di
sviluppo ma, disgraziatamente, mentre tu facevi le tue modifiche,
``foobar`` non ti ha aspettato ed è stato modificato.

Bene: se ricordi, ``rebase`` ti permette di applicare tutte le tue
modifiche ad un altro ``commit``; potresti applicare il tuo ramo a
``foobar/experiment``. È un po' come se potessi staccare di netto il
tuo ramo ``experiment`` per riattaccarlo su un'altra base
(``foobar/experiment``)

Prova

.. code-block:: bash

    git rebase foobar/experiment

.. figure:: img/collaborating-6.png

Visto? A tutti gli effetti appare come se tu avessi iniziato il tuo
lavoro *dopo* la fine dei lavori su ``foobar``. In altre parole:
``rebase`` ha apparentemente reso lineare il processo di sviluppo, che
era intrinsecamente non lineare, senza costringerti ad allinearti con il
lavoro del tuo collega esattamente nei momenti in cui aggiungeva
``commit`` al proprio ``repository``.

Puoi spedire il tuo lavoro a ``foobar``: apparirà come tu abbia
apportato le tue modifiche a partire dall'ultimo ``commit`` eseguito su
``foobar``.

.. code-block:: bash

    **git push foobar experiment**\  
    Counting objects: 6, done. 
    Delta compression using up to 4 threads. 
    Compressing objects: 100% (4/4), done. 
    Writing objects: 100% (5/5), 510 bytes \| 0 bytes/s, done.
    Total 5 (delta 2), reused 0 (delta 0) 
    remote: error: refusing to update checked out branch: refs/heads/experiment 
    remote: error: By default, updating the current branch in a non-bare repository
    remote: error: is denied, because it will make the index and work
    tree >inconsistent 
    remote: error: with what you pushed, and will require 'git reset --hard' to match 
    remote: error: the work tree to HEAD. 
    remote: error: remote: error: You can set 'receive.denyCurrentBranch' configuration variable to 
    remote: error: 'ignore' or 'warn' in the remote repository to allow pushing into
    remote: error: its current branch; however, this is not recommended unless you 
    remote: error: arranged to update its work tree to match what you pushed in some remote: error: other way. 
    remote: error:
    remote: error: To squelch this message and still keep the default behaviour, set 
    remote: error: 'receive.denyCurrentBranch' configuration variable to 'refuse'. 
    To ../repo-remoto ! [remote rejected] experiment -> experiment (branch is currently checked out)
    error: failed to push some refs to '../repo-remoto'

Mamma mia! Sembra proprio che a git questo ``push`` non sia piaciuto.
Nel lunghissimo messaggio di errore git ti sta dicendo di non poter fare
``push`` di un ``branch`` attualmente "*checked out*\ ": il problema non
sembra essere nel ``push`` in sé, ma nel fatto che sull'altro
``repository`` il tuo collega abbia fatto ``checkout experiment``.

Questo problema potrebbe capitarti di continuo, se non sai come
affrontarlo, per cui a breve gli dedicheremo un po' di tempo. Per
adesso, rimedia chiedendo gentilmente al tuo collega di spostarsi su un
altro ramo e ripeti il ``push``.

Quindi: su ``foobar`` vedi di spostarti su un altro ``branch``

.. code-block:: bash

    cd ../repo-remoto
    git checkout -b parcheggio

Dopo di che, torna al tuo ``repository`` locale e ripeti ``push``

.. code-block:: bash

    cd ../progetto
    git push foobar experiment

Ecco il risultato

.. figure:: img/collaborating-7.png

Ripercorriamo graficamente quello che è successo. Partivi da

.. figure:: img/collaborating-4.png

Poi hai fatto ``rebase`` ed hai ottenuto

.. figure:: img/collaborating-6.png

Poi hai fatt ``push`` su ``foobar``: la nuova posizione del
``remote branch`` ``foobar/experiment`` testimonia l'avanzamento del
ramo anche sul ``repository`` remoto.

.. figure:: img/collaborating-7.png

Contestualmente, il tuo collega su ``foobar`` ha visto passare il
proprio ``repository`` da

.. figure:: img/collaborating-1.png

a

.. figure:: img/collaborating-8.png

Ti torna tutto? Ecco, guarda attentamente le ultime due immagini, perché
è proprio per evitare quello che vedi che git si è lamentato tanto,
quando hai fatto ``git push foobar experiment``.

Per capirlo, mettiti nei panni del tuo collega virtuale, che abbiamo
immaginato sul ``repository`` remoto ``foobar``. Il tuo collega se ne
sta tranquillo sul suo ramo ``experiment``

.. figure:: img/collaborating-1.png

quando ad un tratto, senza che abbia dato alcun comando a git, il suo
``repository`` accetta la tua richiesta di ``push``, salva nel database
locale un paio di nuovi ``commit`` e sposta il ramo ``experiment`` (sì,
proprio il ramo di cui aveva fatto il ``checkout``!) due ``commit`` in
avanti

.. figure:: img/collaborating-8.png

Ammetterai che se questo fosse il comportamento standard di git non
vorresti mai trovarti nella posizione del tuo collega virtuale: la
perdita di controllo del proprio ``repository`` e del proprio
``file system`` sarebbe davvero un prezzo troppo alto da pagare.

Capisci bene che cambiare il ramo del quale si è fatto ``checkout``
significa, sostanzialmente, vedersi cambiare sotto i piedi il
``file system``. Ovviamente questo è del tutto inaccettabile, ed è per
questo che git si è rifiutato di procedere ed ha replicato con un
chilometrico messaggio di errore.

Prima hai rimediato alla situazione spostando il tuo collega virtuale su
un ramo ``parcheggio``, unicamente per poter spedirgli il tuo ramo.

.. figure:: img/collaborating-9.png

Questo sporco trucco ti ha permesso di fare ``push`` di ``experiment``.

Ma a pensarci bene anche questa è una soluzione che, probabilmente, tu
personalmente non accetteresti mai: a parte la scomodità di doversi
interrompere solo perché un collega vuole spedirti del suo codice,
comunque non vorresti che l'avanzamento dei tuoi rami fosse
completamente fuori dal tuo controllo, alla mercé di chiunque. Perché,
alla fine, il ramo ``experiment`` si sposterebbe in avanti contro la tua
volontà, e lo stesso potrebbe accadere a tutti gli altri rami di cui non
hai fatto ``checkout``.

È evidente che debba esistere una soluzione radicale a questo problema.

La soluzione è sorprendentemente semplice: **non permettere ad altri di
accedere al tuo ``repository``**.

Potresti trovarla una soluzione un po' sommaria, ma devi riconoscere che
non esista sistema più drastico ed efficace. E, fortunatamente, è molto
meno limitante di quanto tu possa credere ad una prima analisi.

Naturalmente, ti ho raccontato solo metà della storia e forse vale la
pena di approfondire un po' l'argomento. Apri bene la mente, perché
adesso entrerai nel vivo di un argomento molto affascinante: la natura
distribuita di git. Si tratta, verosimilmente, dell'aspetto più
comunemente incompreso di git e, quasi certamente di una delle sue
caratteristiche più potenti.

:ref:`Indice <indice>` :: :ref:`Obiettivo 7: disegnare il workflow ideale <obiettivo_7>`
