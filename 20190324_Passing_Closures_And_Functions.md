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
