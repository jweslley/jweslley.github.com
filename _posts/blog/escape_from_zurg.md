---
title: Escape from Zurg
tags:
  - scala
---

A long time ago, I got fascinated with the [Zurg riddle][zurg-riddle]. Aka [Escape from Zurg][escape-zurg], the Zurg riddle is a puzzle (featuring characters from the movie [Toy Story](http://en.wikipedia.org/wiki/Toy_Story)) which has been used to teach students Logic Programming. Recently, Sam Halliday from the [Javablog](http://javablog.co.uk/) wrote a blog post with a [Java implementation][java-zurg], and Paul Butcher from the [Texperts](http://texperts.com/) wrote another blog post with a [Ruby implementation][ruby-zurg] of [Escape from Zurg][escape-zurg]. Others implementations can to be find [here][others-zurg]. However it wasn't long before I got the craving to write a [Scala](http://www.scala-lang.org/) version. The idea is not to solve this specific problem, but to use it to show some Scala features.

[zurg-riddle]: http://web.engr.oregonstate.edu/%7Eerwig/zurg/
[escape-zurg]: http://web.engr.oregonstate.edu/%7Eerwig/papers/Zurg_JFP04.pdf
[java-zurg]: http://javablog.co.uk/2007/10/13/escape-from-zurg/
[ruby-zurg]: http://www.texperts.com/2007/09/09/escape-from-zurg/
[others-zurg]: http://lambda-the-ultimate.org/node/2434

Here's the puzzle:

> [Buzz][Buzz], [Woody][Woody], [Rex][Rex], and [Hamm][Hamm] have to escape from [Zurg][Zurg]. They merely have to cross one last bridge before they are free. However, the bridge is fragile and can hold at most two of them at the same time. Moreover, to cross the bridge a flashlight is needed to avoid traps and broken parts. The problem is that our friends have only one flashlight with one battery that lasts for only 60 minutes. The toys need different times to cross the bridge (in either direction):

> **Buzz**: 5 minutes <br>
> **Woody**: 10 minutes <br>
> **Rex**: 20 minutes <br>
> **Hamm**: 25 minutes <br>

[Buzz]: http://en.wikipedia.org/wiki/Buzz_Lightyear
[Woody]: http://en.wikipedia.org/wiki/Sheriff_Woody#Woody
[Rex]: http://en.wikipedia.org/wiki/Rex_%28Toy_Story%29#Rex
[Hamm]: http://en.wikipedia.org/wiki/Hamm_%28Toy_Story%29#Hamm
[Zurg]: http://en.wikipedia.org/wiki/Emperor_Zurg

Since there can be only two toys on the bridge at the same time, they cannot cross the bridge all at once. Since they need the flashlight to cross the bridge, whenever two have crossed the bridge, somebody has to go back and bring the flashlight to those toys on the other side that still have to cross the bridge.

The problem is: In which order can the four toys cross the bridge in time (that is, in 60 minutes) to be saved from Zurg?

Let's start defining the Toys as a case class with `name` and `time` fields.

    :::scala
    case class Toy(name: String, time: Int)

Like as the Toys, the sides of the bridge are case classes, in truth, case objects.

    :::scala
    abstract class Direction
    case object Left extends Direction
    case object Right extends Direction

## What are case classes/objects?

_Case classes_ and _case objects_ are defined like a normal classes or objects, except that the definition is prefixed with the modifier case. The case modifier in front of a class or object definition has the following effects:

1. Case classes implicitly come with a constructor function, with the same name as the class.
2. Case classes and case objects implicitly come with implementations of methods `toString`, `equals` and `hashCode`.
3. Case classes implicitly come with nullary accessor methods which retrieve the constructor arguments.
4. Case classes allow the constructions of patterns which refer to the case class constructor (Pattern Matching).

For more details about case classes/objects see [here][caseclasses] or in [Scala Documentation][scaladoc].

Next, we define `Move` which we will use to represent transitions between states. Each move consists of a direction (right or left) and a group of toys. `Move` provides two methods, the cost method which returns the time taken to complete the move and the overrided `toString` method which provides a better print version from the move.

    :::scala
    class Move(direction: Direction, toys: List[Toy]) {

      def cost = Iterable.max(toys.map{_.time})

      override def toString = "Move: " + direction + " " + toys.map{_.name}.mkString("[", ",", "]")

    }

A `State` comprises two fields, `direction` which represents the current flashlight position and `group` which represents the toys remaining on the left-hand side of the bridge.

    :::scala
    class State(direction: Direction, group: List[Toy]) {

      def done = group.isEmpty

      def next(f: (Move, State) => Unit) = direction match {

        case Left => for { tuple <- group.zipWithIndex
                           toy <- group drop (tuple._2 + 1)
                           toys = List(toy, tuple._1) }
                       f(new Move(Right, toys), new State(Right, group diff toys))

        case Right => for(toy <- (ToyStory.toys diff group))
                        f(new Move(Left, List(toy)), new State(Left, toy :: group))
      }
    }

## What are the Scala features in State class?

  1. First-Class Functions
  2. Pattern Matching
  3. For-Comprehensions

### First-Class Functions

In Scala each function is a "first-class value". Like any other value, it may be passed as a parameter or returned as a result. Functions which take other functions as parameters or return them as results are called higher-order functions. The `next` method takes a function from the type `(Move, State) => Unit` as parameter named `f`.

For more details about functions see [here][first-class] or in [Scala Documentation][scaladoc].

### Pattern Matching

Scala has a built-in general pattern matching mechanism. Pattern matching is a generalization of C or Java's switch statement to class hierarchies. Instead of a switch statement, there is a standard method `match`, which is defined in Scala’s root class Any, and therefore is available for all objects. The `match` method takes as argument a number of cases. Scala's pattern matching statement is most useful for matching on algebraic types expressed via [case classes][caseclasses-doc]. Scala also allows the definition of patterns independently of case classes, using unapply methods in [extractor objects][extractors]. The `next` method starts with the statement: `direction match...`, a pattern matching expression with two options: `Left` and `Right`.

For more details about pattern matching and extractor objects see [here][patternmatching] or in [Scala Documentation][scaladoc].

### For-Comprehensions

Scala offers special syntax to express combinations of certain higher-order functions more naturally. For comprehensions are a generalization of list comprehensions found in languages like Haskell and Python. They are mapped to combinations involving methods `foreach` and `filter`. For instance, the for loop `for (path <- problem) ...` in ToyStory object is mapped to `problem foreach (path => ...)` defined in `SearchProblem` class.

    :::scala
    class SearchProblem(initial: State) {

      def foreach(f: List[Move] => Unit) {

        def solve(path: List[Move], state: State) {
          if (state.done) {
            f(path)
          } else {
            state next { (move, state) => solve(move :: path, state) }
          }
        }
        solve(Nil, initial)
      }
    }

    object ToyStory extends Application {

      val toys = Toy("Buzz", 5) :: Toy("Woody", 10) :: Toy("Rex", 20) :: Toy("Hamm", 25) :: Nil

      val problem = new SearchProblem(new State(Left, toys)) 
      for (path <- problem)
        if ((0 /: path) {(cost, move) => cost + move.cost} <= 60)
          println("Solution: " + path)
    }

The complete source code can be downloaded [here][download].

[scaladoc]: http://www.scala-lang.org/docu/index.html
[caseclasses]: http://alblue.blogspot.com/2007/12/scala-introduction-to-scala-case.html
[caseclasses-doc]: http://www.scala-lang.org/intro/caseclasses.html
[extractors]: http://www.scala-lang.org/intro/extractors.html
[first-class]: http://alblue.blogspot.com/2007/10/scala-introduction-to-scala-functions.html
[patternmatching]: http://langexplr.blogspot.com/2007/05/pattern-matching-on-java-objects-with.html
[download]: http://docs.google.com/Doc?id=dfzs39d9_4kz3cwh
