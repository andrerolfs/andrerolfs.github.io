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