{% include_relative menu.md %}

[Back To : Computer Language Comparison](20190316_Computer_Language_Comparison.md)

# 20190324 Passing Closures And Functions

Here I compare these aspects :

* Can closures be passed as function arguments
* Can functions be passed as function arguments
* Can functions have inner functions
* Can functions have inner closures

## Groovy

* In Groovy you can only pass closures and not functions as function arguments
* You can pass functions as closures as function arguments by using the `&` operator though
* Groovy does not allow inner functions, but inner closures are allowed

      class GroovyTest {
      
            static def closureUserFunction(c, x1, y1, z1) {
                c(x1, y1, z1)
            }

            static def closureProviderFunction(x2, y2, z2) {
                for (i in x2) {
                    println i + " -> " + y2 + " : " + z2
                }
            }

            static def closures() {

              def myClosure = { x, y, z ->
                for (i in x) {
                      println i + " -> " + y + " : " + z
                }
              }

              def a = 1..7

              myClosure(a, "x-value", "y-value")

              closureUserFunction(myClosure, 2..3, "u-value", "v-value")

              closureUserFunction(GroovyTest.&closureProviderFunction, 10..13, "a-value", "b-value")
            }
      }

## Swift

In Swift functions are first class citizens, you can do more or less everything with them.

For this reason I use some more examples and split the aspects these examples try to show.

### Inner Functions

* Swift supports inner functions
* You can use functions as return type by declaring the returned functions signature with 

      () -> Type1, Type2, ...

* You can pass functions as argument by declaring the returned functions signature with 

      () -> Type1, Type2, ...
      
Example :

      func greeter() -> (Int) -> Void {

          func innerGreeter(innerCounter : Int) {
              print(innerCounter.description + ". innerGreeter : Greetings from within!")
          }

          print("Greeter : Hello World!")
          innerGreeter(innerCounter : 1)
          return innerGreeter
      }

      func remoteGreeter(passedFunction : (Int) -> Void, counter : Int) {
          passedFunction(counter)
      }

      let returnedInnerGreeter = greeter()

      returnedInnerGreeter(3)

      remoteGreeter(passedFunction : returnedInnerGreeter, counter : 4)

### Inner Closures

* In Swift a closure can have inner closures and return them 

Example :

      let myClosure : (String, String) -> (String, String)->()  = { (a, b) -> (String, String)->() in
          print("in order : " + a + " -> " + b)

          let myInnerClosure : (String, String) -> () = { (u,v) in
              print("reverse : " + v + " <- " + u)
          }

          return myInnerClosure
      }

      let innerClosure = myClosure("X","Y")

      innerClosure("X","Y")

### Function Returning A Closure And Function With Closure As Parameter

* In Swift functions can return closures
* In Swift functions can have closures as parameters

Example :

      func aFunctionToGetAClosure() -> (String, String)->() {

          let myClosure : (String, String) -> (String, String)->()  = { (a, b) -> (String, String)->() in
              print("in order : " + a + " -> " + b)

              let myInnerClosure : (String, String) -> () = { (u,v) in
                  print("reverse : " + v + " <- " + u)
              }

              return myInnerClosure
          }
          return myClosure("X1", "Y1")
      }

      let aClosureFromWithin = aFunctionToGetAClosure()
      aClosureFromWithin("X2","Y2")

      func aFunctionWithClosureAsParameter(aClosureParameter:(String, String)->()) {
          aClosureParameter("X3","Y3")
      }

      aFunctionWithClosureAsParameter(aClosureParameter: aClosureFromWithin)

### Closure Can Have States

* In Swift a closure captures the state of surrounding variables

Example :

      func aFunctionToCreateAClosureWithState(state : Int) -> ()->Int {
          return {
              return state
          }
      }

      let one = aFunctionToCreateAClosureWithState(state: 1)
      let two = aFunctionToCreateAClosureWithState(state: 2)
      let three = aFunctionToCreateAClosureWithState(state: 3)

      print("numbers : " + String(one()) + ", " + String(two()) + ", ", String(three()))
      assert(one() + two() + three() == 6)
      
## Kotlin

### Passing And Returning Functions And Closures

* In Kotlin functions can have other functions and closures as parameters, with the same signature
* Kotlin functions can return functions and closures

Example :

      fun caller(input : String, f : (String) -> Unit) : (String) -> Unit {
          f(input)
          return f
      }

      fun main(args: Array<String>) {

          val x = caller("Hallo Welt 1!\n", ::print)

          x("Hallo Welt 2!\n")

          val y = { input : String ->
              print(input)
          }

          y("Hallo Welt 3!\n")

          val z = caller("Hallo Welt 4!\n", x)

          z("Hallo Welt 5!\n")
      }

### Return Inner Closure And Capture States

* Kotlin can create inner closures to return them
* These closure can capture the states of outer variables

Example :

      fun creatorForClosure(input : String) : (String) -> Unit {
          val aClosure = { a : String ->
              print(a + " : " + input + "\n")
          }

          return aClosure
      }

      fun main(args: Array<String>) {

          val aPrinter = creatorForClosure("Input A")
          val bPrinter = creatorForClosure("Input B")
          val cPrinter = creatorForClosure("Input C")

          aPrinter("First")
          bPrinter("Second")
          cPrinter("Third")
      }

### Return Inner Function And Capture States

* Kotlin can create inner functions and return them
* These functions can capture the states of outer variables

Example :

      fun creatorForFunction(input : String) : (String) -> Unit {
          fun createdFunction(a : String) {
              print(a + " : " + input + "\n")
          }

          return ::createdFunction
      }

      fun main(args: Array<String>) {

          val uPrinter = creatorForFunction("Input U")
          val vPrinter = creatorForFunction("Input V")
          val wPrinter = creatorForFunction("Input W")

          uPrinter("Fourth")
          vPrinter("Fifth")
          wPrinter("Sixth")
      }

## Python

* Python supports creation and passing of functions and lambdas
* Both capture the states of outer variables
* A lambda is a closure, which supports only one expression, e.g. : a + b
* Please read here on [Stackoverlow](https://stackoverflow.com/questions/2970858/why-doesnt-print-work-in-a-lambda) why you need to use *the back-ported print function if you are using the latest Python 2.x*

Example :

      # must be at the beginning of the file
      from __future__ import print_function
      
      def createFunction(s):
          def created():
              print(s + "I come from a function!")
          return created

      def consumeFunction(f):
          f()

      x = createFunction("Good morning : ")

      consumeFunction(x)

      def createLambda(s):
          y = lambda a : a + s
          z = lambda b : print(b)
          return (y,z)

      def consumeLambda(q,j):
          j(q("Good evening : "))

      (u,v) = createLambda("I come from a lambda!")

      consumeLambda(u,v)
