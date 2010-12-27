title: Scala Shell
tags: scala, tool

*Scala Shell* (scalash) is a [shell](http://en.wikipedia.org/wiki/Shell_%28computing%29) for programming in [Scala](http://www.scala-lang.org/). Scalash is run from the [command line](http://en.wikipedia.org/wiki/Command_line_interface) and allows the programmer to experiment with code in real time. It allows you to enter Scala commands at the prompt and have the interpreter respond immediately.

A quick summary of the features present:

* colourized output (_highlighting_)
* auto-completion, aka [Tab-completion](http://en.wikipedia.org/wiki/Tab_completion)
* start script support - when an interactive shell is started, [Scala Shell](http://code.google.com/p/scalashell/) reads and executes commands from `~/.scalarc`, if that file exists.
* persistent history (`~/.scala_history`)
* [command history](http://en.wikipedia.org/wiki/Command_history)
* command load

{{#vimeo}}1709559{{/vimeo}}

You can install the current release directly with Scala Bazaar:

    sbaz update
    sbaz install scalashell-scala
