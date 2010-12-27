title: SHadoop
tags: hadoop, mapreduce, scala, shadoop

## What is Scala and Hadoop?

[Scala][] is a modern multi-paradigm programming language designed to express common programming patterns in a concise, and type-safe way. It smoothly integrates features of object-oriented and functional languages including mixins, algebraic datatypes with pattern matching, genericity, and more.

[Hadoop][] is a free Java software platform that supports running applications to process vast amounts of data. It has been developed under the [Apache Lucene][lucene] Project and was originally developed to support distribution for [Nutch][], which is an effort to build an open source search engine for the search and index component. Hadoop consists of an open source implementation of Google’s published computing infrastructure, specifically [MapReduce][] and the [Google File System][gfs].


## Scala + Hadoop!?

The [Hadoop Map-Reduce][hadoopMR] framework is map based, whose keys and values are serializable objects which implements a simple serialization protocol. This serialization protocol is defined by the [Writable][] interface. In addition, Hadoop provides `Writable`'s implementations for each basic types(`Ints`, `Long`, `Float`, `String`, ...). This implementations wrap a value of the basic type in an `Writable` object. Let us take this example, and think about possible utilization of int values in Hadoop:

    :::java
    private final static IntWritable one = new IntWritable(1);

Sounds like primitive wrappers before Java 5 boxing!

## Why Scala?

Read [this][why] and [this][whynot]. Moreover, Scala offers a bag of others features:

  * Implicit conversion methods

> Implicit methods that are often used for converting types, essentially give you statically guaranteed and provided dynamic scoping. Implicit conversions between types (You know the way a number get implicitly converted to another number type in Java, e.g. `int` to `long`, `short` to `int`, ...? Scala does that too, but it's all programmer definable. They're just library functions in [scala.Predef][predef]).

  * Type inference

> The Scala compiler can oftentimes infer the type of an object so there is no need to explicitly specify the type. It is, for instance, often not necessary in Scala to specify the type of a variable, since the compiler can deduce the type from the initialization expression of the variable. Also return types of methods can often be omitted since they corresponds to the type of the body, which gets inferred by the compiler.

In short, Scala provides a clear, concise and stylized syntax.

## SHadoop = Scala + Hadoop

What we would like is something like this:

    :::scala
    val one = 1

Or like this:

    :::scala
    def map(key: LongWritable, value: Text, output: OutputCollector[Text, IntWritable], reporter: Reporter) =
      (value split " ") foreach (output collect (_, one))

The interesting point is that with Scala, this is quite simple to implement. SHadoop is the proof!!! SHadoop consists in only one [source file][source] containing a Scala object with some implicit methods that are often used for converting primitives Java types (including `String`) to `Writable` instances. Furthermore, the SHadoop object provides implicit methods that are often used for converting writable java iterators to primitives type scala iterators - scala iterators provides a lot of useful methods, like `foreach`, `map`, `filter` and [others][iterator].

## Usage

The [Hadoop Map-Reduce Tutorial][mapred-tutorial] shows a very simple Map-Reduce application that counts the number of occurences of each word in a given input set.

> Source Code - [WordCount.scala][source]

    :::scala
    package shadoop

    import SHadoop._
    import java.util.Iterator
    import org.apache.hadoop.fs._

    import org.apache.hadoop.io._
    import org.apache.hadoop.mapred._

    object WordCount {

      class Map extends MapReduceBase with Mapper[LongWritable, Text, Text, IntWritable] {

        val one = 1

        def map(key: LongWritable, value: Text, output: OutputCollector[Text, IntWritable], reporter: Reporter) =
          (value split " ") foreach (output collect (_, one))
      }

      class Reduce extends MapReduceBase with Reducer[Text, IntWritable, Text, IntWritable] {

        def reduce(key: Text, values: Iterator[IntWritable],
          output: OutputCollector[Text, IntWritable], reporter: Reporter) = {

          val sum = values reduceLeft ((a: Int, b: Int) => a + b)
          output collect (key, sum)
        }
      }

      def main(args: Array[String]) = {
        val conf = new JobConf(classOf[Map])
        conf setJobName "wordCount"

        conf setOutputKeyClass classOf[Text]
        conf setOutputValueClass classOf[IntWritable]

        conf setMapperClass classOf[Map]
        conf setCombinerClass classOf[Reduce]

        conf setReducerClass classOf[Reduce]

        conf setInputFormat classOf[TextInputFormat]

        conf setOutputFormat classOf[TextOutputFormat[_ <: WritableComparable, _ <: Writable]]

        conf setInputPath(args(0))
        conf setOutputPath(args(1))

        JobClient runJob conf
      }
    }


## Source code explained: Java x Scala

### 1. The one field from the Map class

Java

    :::java
    private final static IntWritable one = new IntWritable(1);

Scala

    :::scala
    val one = 1

Scala to specify the type of the field by type inference. Very cool!!!

### 2. The map method from the Map class

Java

    :::java
    public void map(LongWritable key, Text value,
                     OutputCollector<Text, IntWritable> output, Reporter reporter) throws IOException {

      String line = value.toString();
      StringTokenizer tokenizer = new StringTokenizer(line);

      while (tokenizer.hasMoreTokens()) {
        word.set(tokenizer.nextToken());
        output.collect(word, one);
      }
    }


Scala

    :::scala
    def map(key: LongWritable, value: Text,
             output: OutputCollector[Text, IntWritable], reporter: Reporter) =
      (value split " ") foreach (output collect (_, one))

Wow!!! Scala implicitly converts value to `String` and applies String's `split` method that returns a String array. This array iterate over each String adding it as key(implicitly converted to `Text`) from `output` object and whose value is `one`. Note: Scala doesn't require semicolons at the end of each instruction, they are optionals.

### 3. The reduce method from the Reduce class

Java

    :::java
    public void reduce(Text key, Iterator<IntWritable> values,
                        OutputCollector<Text, IntWritable> output, Reporter reporter) throws IOException {
      int sum = 0;
      while (values.hasNext()) {
        sum += values.next().get();
      }
      output.collect(key, new IntWritable(sum));
    }

Scala

    :::scala
    def reduce(key: Text, values: Iterator[IntWritable],
                output: OutputCollector[Text, IntWritable], reporter: Reporter) = {
      val sum = values reduceLeft ((a: Int, b: Int) => a + b)
      output collect (key, sum)
    }

Again, wow!!! On first line Scala calculates the sum of the values using the `reduceLeft` method from a `Int` iterator, implicitly converted from `IntWritable` java iterator. After, the `output` object collects the sum result.

## Running

Assuming `HADOOP_HOME` is the root of the installation from Hadoop:

* Copy the `scala-library.jar` to `${HADOOP_HOME}/lib` directory
* Run the application:

        ${HADOOP_HOME}/bin/hadoop jar shadoop-0.0.1-alpha.jar shadoop.WordCount input/ output/

> `input/` - a directory containing the text-files as input set <br>
> `ouput/` - a ouput directory

Download jar with sources [here][download].


[scala]: http://www.scala-lang.org/
[hadoop]: http://hadoop.apache.org/core/
[lucene]: http://lucene.apache.org/
[nutch]: http://lucene.apache.org/nutch/
[mapreduce]: http://labs.google.com/papers/mapreduce.html
[gfs]: http://labs.google.com/papers/gfs.html
[hadoopMR]: http://wiki.apache.org/hadoop/HadoopMapReduce
[Writable]: http://hadoop.apache.org/core/docs/current/api/org/apache/hadoop/io/Writable.html
[why]: http://www.infoq.com/news/2008/01/why-scala
[whynot]: http://www.drmaciver.com/2008/01/why-not-scala/
[predef]: http://www.scala-lang.org/docu/files/api/scala/Predef$object.html
[source]: http://code.google.com/p/jweslley/source/browse/trunk/scala/shadoop/src/main/scala/shadoop/SHadoop.scala
[download]: http://jweslley.googlecode.com/files/shadoop-0.0.1-alpha.jar
[iterator]: http://www.scala-lang.org/docu/files/api/scala/Iterator.html
[mapred-tutorial]: http://hadoop.apache.org/core/docs/r0.15.3/mapred_tutorial.html
