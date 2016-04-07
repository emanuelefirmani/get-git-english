.. _proseguire:

################
Further readings
################

This guide has touched only a minimal part of the tools offered by git. 

git is a system made of more than 150 commands. each of which would require a specific chapter. 

In order to have a complete list of available commands, run ``git help -a``


.. code-block:: bash

    git help -a
    usage: git [--version] [--help] [-c name=value]
               [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
               [-p|--paginate|--no-pager] [--no-replace-objects] [--bare]
               [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
               <command> [<args>]
    
    available git commands in '/usr/local/Cellar/git/1.8.4/libexec/git-core'
    
      add                       config                    gc                        merge-one-file            relink                    show-ref
      add--interactive          count-objects             get-tar-commit-id         merge-ours                remote                    stage
      am                        credential                grep                      merge-recursive           remote-ext                stash
      annotate                  credential-cache          gui                       merge-resolve             remote-fd                 status
      apply                     credential-cache--daemon  gui--askpass              merge-subtree             remote-ftp                stripspace
      archimport                credential-store          hash-object               merge-tree                remote-ftps               submodule
      archive                   cvsexportcommit           help                      mergetool                 remote-http               svn
      bisect                    cvsimport                 http-backend              mktag                     remote-https              symbolic-ref
      bisect--helper            cvsserver                 http-fetch                mktree                    remote-testsvn            tag
      blame                     daemon                    http-push                 mv                        repack                    tar-tree
      branch                    describe                  imap-send                 name-rev                  replace                   unpack-file
      bundle                    diff                      index-pack                notes                     repo-config               unpack-objects
      cat-file                  diff-files                init                      p4                        request-pull              update-index
      check-attr                diff-index                init-db                   pack-objects              rerere                    update-ref
      check-ignore              diff-tree                 instaweb                  pack-redundant            reset                     update-server-info
      check-mailmap             difftool                  log                       pack-refs                 rev-list                  upload-archive
      check-ref-format          difftool--helper          lost-found                patch-id                  rev-parse                 upload-pack
      checkout                  fast-export               ls-files                  peek-remote               revert                    var
      checkout-index            fast-import               ls-remote                 prune                     rm                        verify-pack
      cherry                    fetch                     ls-tree                   prune-packed              send-email                verify-tag
      cherry-pick               fetch-pack                mailinfo                  pull                      send-pack                 web--browse
      citool                    filter-branch             mailsplit                 push                      sh-i18n--envsubst         whatchanged
      clean                     fmt-merge-msg             merge                     quiltimport               shell                     write-tree
      clone                     for-each-ref              merge-base                read-tree                 shortlog
      column                    format-patch              merge-file                rebase                    show
      commit                    fsck                      merge-index               receive-pack              show-branch
      commit-tree               fsck-objects              merge-octopus             reflog                    show-index
    
    git commands available from elsewhere on your $PATH

      credential-osxkeychain  subtree
    
    'git help -a' and 'git help -g' lists available subcommands and some
    concept guides. See 'git help <command>' or 'git help <concept>'
    to read about a specific subcommand or concept.


Probably the best way to know the details of each command is reading the respective *man page*. 

To access the ``merge`` *man page* you have simply to run

.. code-block:: bash

    git help merge


and you may do the same for all other commands.

But don't get scared, you will not need to read the documantation of all commands: 
use the *man pages* as *reference guide* to be consulted when necessary, when you will need details on the specific command. 


Learn git branching
###################

Rather than jumping again in lengthy readings , I suggest to start practising.

A very amusing system to take familiarity with git is the wonderful `Learn git branching <http://pcottle.github.io/learnGitBranching/?demo>`_,
a very practical and very challenging guide, made of a set of exercises with increasing difficulty.

I consider it a must. 

Face it, and make the effort of reaching the end : it deserves very much. 

ProGit
######


A much more practical and discursive reading than *man pages* is the lovely Scott Chacon's ProGit.

You may buy the book `on Amazon <http://www.amazon.com/Pro-Git-Scott-Chacon/dp/1430218339>`_ or 
you can read it `for free online <http://www.git-scm.com/book>`_.

I warmly suggest to you to dedicate time to it: it's considered one of the best books on git in circulation.





:ref:`Indice <indice>`
