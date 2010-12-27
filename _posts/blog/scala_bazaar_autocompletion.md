title: Guide to Scala Bazaar auto completion using BASH
tags: bash. linux, scala

The [Scala Bazaar][scala-bazaar] system, "sbaz" in short, is a system used by [Scala][scala] enthusiasts to share computer files with each other. In particular, it makes it easy to share libraries and applications. In this post, I'll show you how easy it to use one of the nicest facilities of the modern shell, the built in "completion" support, to become more easy to use sbaz in command line.

First you must go to the following site to install the BASH programmable auto completion setup if your distro doesn't have it by default. I don't think many do so you'll need to go to the [Programmable Completion Website][completion].

Once you've setup your system for auto completion you need to take the following:

    :::bash
    #!/bin/bash

    _sbaz_complete()
    {
      local cur commands

      COMPREPLY=()
      cur=${COMP_WORDS[COMP_CWORD]}

      commands='available compact help install installed keycreate keyforget keyknown
      keyremember keyremoteknown keyrevoke pack remove retract setuniverse setup share show
      showuniverse update packages upgrade'
      cur=`echo $cur | sed 's/\\\\//g'`

      COMPREPLY=($(compgen -W "${commands}" ${cur} | sed 's/\\\\//g') )
    }

    complete -F _sbaz_complete -o filenames sbaz

And place it in `/etc/bash_completion.d/sbaz`. Once you've done that the next time you start up your BASH shell you will have sbaz auto completion!

Download snippet code [here][snippet].

[scala]: http://www.scala-lang.org/
[scala-bazaar]: http://www.scala-lang.org/node/93
[completion]: http://www.caliban.org/bash/index.shtml#completion
[snippet]: http://snippets.dzone.com/posts/show/5968
