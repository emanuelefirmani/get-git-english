.. _obiettivo_7:

Target 7: designing the ideal workflow
######################################

If you have used CVS and SVN you will surely be used to the concept of central
``repository``: every developer make reference to a single central structure, 
where the source code is stored.

.. figure:: img/workflow-1.png

In the example we have used so far the team was made of 2 developers:
your colleague and you. You will have realized that organization
of ``repositories`` with git has something particular even with so 
small a team: first af all bedause there are two
``repositories``; and then because, among the two ``repositories``, one doesn't
understand which is the *official* one.

.. figure:: img/workflow-2.png

To make things more complicated there's the fact that, apparently, 
one should not allow other to access his own ``repository``. The matter 
is definetely confused and hazy.

Let's try to make it clear. Let's start with an assumption: git is versatile 
enough to totally replicate SVN's central organization of ``repositories``
Therefore, in case for you it's an unsustainable cultural schock just thinking og organizing 
your workflow in a different way, copy SVN's structure and you can live happy. You would join
the lengthy list of companies and teams that, in front of the possibilities offered by git, decide 
to shelter in the well known architecture based upon the central ``repository`` . 

That's one option. Not one of the best, because it prevents enjoying some of the great
advantages that come by using a distributed versioning system, but it's however a viable option.
A colleague of mine describes it like "*finally having the gun and using it like a club*\ ". Let's say that
it's not the option that this guide is going to promote.

In this chapter we'll rather try to explore other less trivial implementations.

Let's start with a heuristic that I always founf very effective:

    use a ``repositories`` topology reflecting the real workflow and the real functional roles that exist in the team

Translated in a nutshell and applied to our concrete case: you and your colleague are using git
mainly for 3 functions 

-  you, for code development
-  your colleague, for code development
-  both, to exchange the code and to integrate the work of the two of you 

The idea is: for every function use a dedicated ``repository`` . In other
words, you might consider the hypothesis of adding one 
``repository``, reachable both from you and your colleague, to be used
as integration area

.. figure:: img/workflow-3.png

Now it would appear more spontaneous electing the ``integration`` 
``repository`` as the official one. Don't you agree? 

Strictly speaking, There's phisically nothing characterizing the  repository
``integrazione`` ``repository`` like \central ``repository`` : technically it's totally 
equivalent to the other two. The basic idea is that role and importance 
of one  ``repository`` compared with another is a social and organizative 
question, not forced by technological costraintsor limits:

git merely permits to model it , but it doesn't impose anything on this aspect.

Then, let's suppose that, by convention o by agreement between the parts, 
we decide that the repository ``integration`` is used to allow 
the integration between your work and your colleague's one and as 
*official* archive; the other two ``repositories`` will be meant as
archives at the exclusive usage of each developer.

You can strengthen this structure using a couple of tools offered by 
git.

First of all, you might create the ``integration`` repository with the command 
``git init --bare``; the ``--bare`` option makes sure that the 
``repository`` cannot be used as work base: only the database will be created,
without ``file system``, therefore it'll be not possible doing ``add`` and ``checkout``

Instead, on the two personal ``repositories`` , you could carefully configure 
the access rights, limiting them only to owners; you will be the only one who
may read and write on your personal ``repository``, and for you it'll be 
impossible to access your colleague's one; and vice versa. You lose the 
possibility to deliver ``branches`` each other without passing by 
the central ``repository`` , but very soon we will see more articulated 
configurations.

.. figure:: img/workflow-4.png

Here it is: you have a topology very similar to SVN's centralized solution,
with the only difference that each developer has a local private 
``repository`` .

Can we do more? Course yes. If it's worth it. In particular:
if the entire development team is made of you and your colleague,
this solution could already be perfect.

But things might be very different: for instance consider the case
where your colleague is an external consultant, to whom you
don't want to give the possibility to change directly code on the official
``repository`` if not after your revision and acceptance of code.

One possibility could be deciding that *your*
``repository`` is the official one, so that Continuous
Integration and Deployment tools can be set up to take the code from there.
Or, you could think back to the heuristic 

   use a ``repositories`` topology reflecting the real workflow and the real functional roles that exist in the team

and decide to add a new ``repository`` with role of 
*official archive* of code ready to go on production, and limit the write right
only to you 

.. figure:: img/workflow-5.png

Can you start to guess that this ``repositories`` story offers a nearly
unlimited range of possibilities?

Look: I want to show you a topological configuration that is very spread and
that for sure you will encounter, especially if you take part in 
some *open source* project on GitHub.

Consider again the last image. Your ``repository`` and  your colleague's 
one are very likely local ``repository``, hosted on their respective 
development systems. Generally, hence, they are not ``repositories`` 
easily reachable from outside. Therefore, when I drawed the scheme

.. figure:: img/workflow-2.png

I had been very superficial and hasty, because I had completely overflown
on the far from trivial problem, of how to make the two ``repositories``, 
maybe hosted on two *laptop*, without fixed IP or domain, communicate:
a folders sharing with Samba? A ssh server installed on both *laptops*? Dropbox?

One of most successful solutions seems suggested by a David Wheeler's aphorism,
that says 

    *All problems in computer science can be solved by another level of
    indirection*

In git might be valid such a law: when you have a workflow problem, try to model
your ``repositories`` topology, adding a new level of indirection.

Applicato al nostro caso, potremmo pensare di fornire a te e al tuo
collega non un singolo ``repository`` ciascuno, ma una coppia di
``repository``: uno ad uso privato, per sostenere le attività di
sviluppo, ed uno pubblico, per consentire la reciproca comunicazione

.. figure:: img/workflow-6.png

Quindi: ogni sviluppatore dispone del proprio ``repository`` privato di
lavoro, e di un ``repository`` pubblico. Tutti possono accedere al
``repository`` pubblico di chiunque, ma solo il legittimo proprietario
può scriverci (nel grafico, per semplicità, è inteso che chiunque possa
accedere in lettura a qualunque ``repository`` pubblico).

Ecco: questa è la tipica organizzazione di un'azienda che abbia adottato
il workflow di GitHub.

Sono possibili innumerevoli variazioni di questa organizzazione base.
Per esempio: il team potrebbe prevedere che il codice vada in produzione
in pacchetti di funzionalità decise da un ``release manager``

.. figure:: img/workflow-7.png

In questa topologia si è deciso che il ``repository`` dal quale si
preleva il codice per il deployment in produzione sia il ``repository``
pubblico del *release manager*: il *release manager* preleva il codice
da ``integrazione``. Il flusso di lavoro è garantito dal fatto che il
*release manager* sia l'unico a disporre dei diritti di ``push`` sul
proprio ``repository`` pubblico.

Facciamo un altro esempio: si potrebbe decidere che il prodotto debba
sempre passare da un ambiente di stage (per esempio, un ambiente di
produzione solo per utenti abilitati al *beta testing*)

.. figure:: img/workflow-8.png

Nota come l'organizzazione, in git, sia ottenuta non limitando le
letture (sostanzialmente, in tutti questi schemi tutti hanno diritti di
lettura su qualsiasi ``repository`` pubblico), ma garantendo i permessi
di scrittura su ``repository`` solo ai proprietari designati; sarà poi
la convenzione sociale a stabilire a quale uso destinare ogni
``repository`` (collegando, per esempio, gli script di deployment ad un
``repository`` piuttosto che ad un altro).

Si potrebbe immaginare la topologia dei ``repository`` come un sistema
di vasche comunicanti; in ogni vasca si può far fluire selettivamente il
codice da una o più altre vasche comunicante; ad ogni persona che
ricopra un determinato ruolo nel flusso di lavoro viene dato il
controllo esclusivo della chiusa che apre o chiude il flusso di codice
nella propri vasca.

In linea generale: tutti i tipi di workflow che prima con SVN si era
costretti ad implementare usando convenzioni sui nomi e sugli usi dei
branch, in git sono molto facilmente modellabili con topologie di
``repository``. È un vero peccato quando un team che abbia adottato git
cerchi di riprodurre un controllo del workflow con gli stessi sistemi di
SVN, perché farà un grande sforzo per ottenere molto meno di quel che git
potrebbe fornire.

Ti accorgerai, invece, di come convenga quasi sempre modellare la rete
di ``repository`` in modo che rifletta il workflow e l'organizazione
gerarchica del tuo team. Per esempio, non è raro che in grandi
organizzazioni il flusso di lavoro sia abbastanza articolato da
richiedere più team, con una distribuzione gerarchica dei ruoli e delle
responsabilità: potrebbe esserci un responsabile del progetto a cui
riportano un paio di responsabili di team che, a loro volta, gestiscono
più persone. Ecco: è comune che in queste occasioni si tenda a modellare
la rete di ``repository`` ad immagine della gerarchia dei ruoli,
adottando quello che viene chiamato "*Dictator and Lieutenants
Workflow*\ "

.. figure:: img/dictator.png

Nota che quando i diagrammi delle topologie sono particolarmente
articolati, si rappresentano solo i ``repository`` pubblici, dando per
scontato che ogni persona adibita al controllo di quel ``repository``
pubblico (cioè, fornita dei diritti di ``push``) avrà un ``repository``
privato sulal propria macchina locale.

:ref:`Indice <indice>` :: :ref:`Daily git <dailygit>`
