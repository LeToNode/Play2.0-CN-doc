# Iteratees, Enumerators, Enumeratees and handling data streams reactively

Progressive Stream Processing and manipulation is an important task in modern Web Programming, starting from chunked upload/download to Live Data Streams consumption, creation, composition and publishing through different technologies including Comet and WebSockets.

Iteratees provide a paradigm and an api that allow this manipulation focusing on several important aspects:

* Create, consume and transform streams of data.
* Treat different data sources in the same manner (Files on disk, Websockets, Chunked Http, Data Upload, ...).
* Composability: User trasnformers to change the shape of the source, or consumer. Construct your own or start with primitives.
* Have control over when it is enough data sent and being informed when source is done sending data.
* Non blocking, reactive and allowing control over respurce consumption (Thread, Memory)

## Iteratees

An Iteratee is a consumer, it describes the way input will be consumed to produce some value. Iteratee is a consumer that returns a value it calculates after being fed some input.

```scala
Iteratee[String,Int] // an iteratee that consumes chunkes of String and produces an Int
```

The Iteratee interface `Iteratee[E,A]` takes two type parameters, `E` representing the type of the Input it accepts and `A` the type of the calculated result.
An iteratee has one of three states, `Cont` meaning accepting more input, `Error` to indicate an error state and `Done` which carries the calculated result. These three states are defined by the `fold` method of an `Iteratee[E,A]` interface:

```scala
  def fold[B](
    done: (A, Input[E]) => Promise[B],
    cont: (Input[E] => Iteratee[E, A]) => Promise[B],
    error: (String, Input[E]) => Promise[B]): Promise[B]
```

The fold method defines an iteratee as one of the three mentioned state. It accepts three callback functions and will call the appropiate one depending on its state to eventually extract a required value. When calling `fold` on an iteratee you are basically saying:

- If the iteratee is in the state `Done`, then I'd take the calculated result of type `A` and what is left from the last consumed chunk of input `Input[E]` and eventually produce a `B`
- If the iteratee is in state `Cont`, then I'd take the provided continuation (which accepting an input) `Input[E] => Iteratee[E,A]` amd eventually produce a `B`. Note that this state provides the only way to push input into the iteratee using the provided continuation function. 
- If the iteratee is in state `Error` then, then I'd take the error message of type `String` and the input that caused it and eventually produce a B.

Obviously depending on the state of the iteratee, `fold` will produce the appropriate `B` using the corresponding passed function.

To sum up, iteratee consists of 3 states, and `fold` provides the means to interact with the state of the iteratee.

## Some important types in the `Iteratee` definition:

Before providing some concrete examples of iteratees, let's clarify two important types we mentioned above:

- `Input[E]` represents a chunk of input that can be either an `El[E]` containing the actual input, and `Empty` chunk or an `EOF` representing the end of the stream.
For example, `Input[String]` can be `El("Hello!")`, Empty, or EOF

- `Promise[A]` represents, as the name talls, a promise of value of type `A`. This means that it will eventually be redeemed with a value of type `A` and you can schedule a callback, among other things you can do, if you are interested in that value. A promise is a very nice primitive for synchronization and composing async calls, and is explained further at the [PromiseScala] section.

## Some primitive iteratees

By implementing the iteratee, and more specifically it's fold method, we can now create some primitive iteratees that we can use later on.

- An iteratee in the `Done` state producing an `1:Int` and returning `Empty` as left from last `Input[String]`

```scala
  val doneIteratee = new Iteratee[String,Int] {
    def fold[B](
      done: (A, Input[E]) => Promise[B],
      cont: (Input[E] => Iteratee[E, A]) => Promise[B],
      error: (String, Input[E]) => Promise[B]): Promise[B] = done(1,Input.Empty)
  }
```

As shown above, this is easily done by calling the appropriate callback function, in our case done, with the necessary information.
To use this iteratee we will make use of the `Promise.pure` that is a promise already in the Redeemed state.

```scala

   val eventuallyMaybeResult:Promise[Option[Int]] = 
     doneIteratee.fold(
     // if done return the computed result
     (a,in) => Promise.pure(Some(a)),

     //if continue return None
     k => Promise.pure(None),

     //on error return None
     (msg,in) => Promise.pure(None) )

```

of course to see what is inside the `Promise` when it is redeemed we use `onRedeem`

```scala
  eventuallyMaybeResult.onRedeem(i => println(i)) //will eventually print 1

```

There is already a built-in way allowing us to create an iteratee in the `Done` state by providing a result and input, generalizing what is implemented above:

```scala
  val doneIteratee = Done[Int,String](1,Input.Empty)
```

Creating a `Done` iteratee is simple, and sometimes useful, but it obviously does not consume any input. Let's create an iteratee that consumes one chunk and eventually returns it as the result:

```scala
   
   val consumeOneInputAndEventuallyReturnIt = new Iteratee[String,Int] {
    def fold[B](
      done: (Int, Input[String]) => Promise[B],
      cont: (Input[String] => Iteratee[String, Int]) => Promise[B],
      error: (String, Input[String]) => Promise[B]): Promise[B] = cont ( in => Done(in,Input.Empty))
  }

```

As for `Done` there is a built-in way to define an iteratee in the `Cont` state by providing a function that takes `Input[E]` and returns a state of `Iteratee[E,A]` :

```
  val consumeOneInputAndEventuallyReturnIt = Cont[String,Int]( in => Done(in,Input.Empty))

```

In the same manner there is a built-in way to create an iteratee in the `Error` state by providing and error message and an `Input[E]`

Back to the `consumeOneInputAndEventuallyReturnIt`, it is possible to create a two step simple iteratee manually but it becomes harder and cumbersome to create any real world iteratee capable of consuming a lot of chunks before, possibly contionally, it eventually returns a result. Luckily there are some built in methods to create common iteratee shapes in the `Iteratee` object.

## Folding input

One common task when using iteratees is maintaining some state and altering it each time input is pushed. This type of iteratee can be easily created using the `Iteratee.fold` which has the signature:

```scala

   def fold[E, A](state: A)(f: (A, E) => A): Iteratee[E, A]

```

Reading the signature one can realize that this fold takes an initial state `A`, a function that takes the state and an input chunk `(A, E) => A` and returning an `Iteratee[E,A]` capable of consuming `E`s and eventually returning an `A`. The created iteratee will return `Done` with the computed `A` when an input `EOF` is pushed.

One example can be creating an iteratee that counts the number of bytes pushed in:

```scala

  val inputLength : Iteratee[Array[Byte],A] = Iteratee.fold[Array[Byte],Int](0){ (length, bytes) => length + bytes.size  }

```
Another could be consuming all input and eventually returning it:

```scala

  val consume =  Iteratee.fold[String,String](""){ (result, chunk) => result ++ chunk  }

```

There is actually already a method in `Iteratee` object that does exactly this for any scala `TraversableLike` called consume, so our example becomes:

```scala

  val consume = Iteratee.consume[String]()

```

One common case is to create an iteratee that does some imperative operation for each chunk of input:

```scala

  val printlnIteratee = Iteratee.foreach[String](s => println(s))

```

More interesting methods exist like `repeat`, `ignore` and `fold1` which is different for the preceding `fold` in offering the opportunity to be asynchronous in treating input chunks.

Of course one should be worried now about how hard would it be to manually push input into an iteratee by folding over iteratee states over and over again. For example pushing input manually into this iteratee using nested folds. That's when `Enumerator`s come in handy.

## Enumerators

If iteratee represents the consumer, or sink, of input, an `Enumerator` is the source that pushes input into a given iteratee. As the name tells, it enumerates some input into the iteratee and eventually returns a new state of that iteratee. This can be easily seen looking at the Enumerators signature:

```scala

  trait Enumerator[E] {

    /**
      * Apply this Enumerator to an Iteratee
     */
    def apply[A](i: Iteratee[E, A]): Promise[Iteratee[E, A]]

```
An `Enumerator[E]` takes an `Iteratee[E, A]` which is any iteratee that consumes `Input[E]` and returns a `Promise[Iteratee[E,A]]` which eventually give a new state of the iteratee.
We can go ahead and manually implement `Enumerator`s by consequently calling the iteratee's fold method, or use one of the provided `Enumerator` creation methods. For instance we can create an `Enumerator[String]` that pushs a list of strings into an iteratee, like the following:

```scala

   val enumerateUsers: Enumerator[String] = Enumerator("Guillaume", "Sadek", "Peter", "Erwan")

```

Now we can apply it to the consume iteratee we created above:

```scala

   val newIteratee: Promise[Iteratee[String,String]] = enumerateUsers( consume ) 

```

To terminate the iteratee and extract the computed result we should pass `Input.EOF`, an `Iteratee` carries a method `run` that does just this. It pushs an `Input.EOF` and returns a `Promise[A]` ignoring the left input if any.

```scala

  // We use flatMap since newIteratee is a promise, and run itself return a promise
  val eventuallyResult: Promise[String] = newIteratee.flatMap( i => i.run)

  //Eventually print the result
  eventuallyResult.onRedeem( s => println(s)) // Prints "GuillaumeSadekPeterErwan"

```
You might notice here that an `Iteratee` will eventually produce a result (returning a promise when calling fold and passing appropriate calbacks), and a `Promise` eventually produces a result. Then a `Promise[Iteratee[E,A]]` can be viewed as `Iteratee[E,A]`. Indeed this is what `Iteratee.flatten` does, Let's apply it to the previous example:

```scala

  //Apply the enumerator and flatten then run the resulting iteratee
  val eventuallyResult: Promise[String] = Iteratee.flatten( enumerateUsers( consume ) ).run
   
  //Eventually print the result 
  eventuallyResult.onRedeem( s => println(s)) // Prints "GuillaumeSadekPeterErwan"

```

An `Enumerator` has some symbolic methods that can act as operators, this can be useful in some contexts for saving some parentheses, like the `|>>` method which does exactly like apply:

```scala

  val eventuallyResult: Promise[String] = Iteratee.flatten( enumerateUsers |>> consume  ).run

```
Since an `Enumerator` pushes some input into an iteratee and eventually return a new state of the iteratee, we can go on pushing more input into the returned iteratee using another `Enumerator`. This can be done either by using the `flatMap` function on `Promise`s or more simply by combining 'Enumerator's using the `andThen` method like the following:

```scala

  val colorsEnumerators = Enumerator("Red","Blue","Green")

  val moreColors = Enumerator("Grey","Orange","Yellow")

  val combinedEnumerator = colors.andThen(moreColors)

  val eventuallyIteratee = combinedEnumerator(consume)

```

As for apply, there is a symbolic version of the `andThen` that can be used to save some parentheses when appropriate:

```scala

  val eventuallyIteratee = 

    Enumerator("Red","Blue","Green") >>>

    Enumerator("Grey","Orange","Yellow") |>>

    consume

```

We can also create `Enumerators` for enumerating files contents:

```scala

  val fileEnumerator: Enumerator[Array[Byte]] = Enumerator.enumerateFile(new File("path/to/some/file"))

```

Or more generally enumerating a `java.io.InputStream`. It is important to note that input won't be read until the iteratee this `Enumerator` is applied on is ready to take more input.
Actually both methods are based on the more generic `Enumerator.callback` that has the following signature:

```scala

  def callback[E](
    retriever: () => Promise[Option[E]],
    onComplete: () => Unit = () => (),
    onError: (String, Input[E]) => Unit = (_: String, _: Input[E]) => ()): Enumerator[E] = ...

```

This method defined on the `Enumerator` object is one of the most important methods for creating `Enumerator`s from imperative logic. Looking closely at the signature, this method takes a callback function `retriever: () => Promise[Option[E]]` that will be called each time the iteratee this `Enumerator` is applied to is ready to take some input. It can be easily used to create an `Enumerator` that represents a stream of time values every 100 millisecond using the opportunity that we can return a promise, like the following:

```scala

  Enumerator.callbackEnumerator { () =>
        Promise.timeout(Some(dateFormat.format(new Date)), 100 milliseconds)
  }

```

In the same manner we can construct an `Enumerator` that would fetch a url every some time using the `WS` api which returns, not suprisingly` a `Promise`

