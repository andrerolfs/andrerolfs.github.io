{% include_relative menu.md %}

[Back To : Computer Language Comparison](20190316_Computer_Language_Comparison.md)

# Classes, Constructors, Named Parameters

Here I compare these aspects :

* How are objects created 
* Support of named parameters

## Groovy

* If the first parameter is a map, then named parameters are supported as elements of this map 
* This only works for the first parameter, it does not work for any other position, even if it is a map
* If you omit these named parameters, the map will be null
* If there are other parameters after the map, you have to pass at least one named parameter or it will not compile

      class GT2 {

          int i
          String s

          GT2(aMap) {
              i = aMap.anInt
              s = aMap.aString
          }

          String f(aMap) {
              if (aMap == null) {
                  return s + i + " : -\n"
              } else {
                  return s + i + " : " + aMap.otherInt + aMap.otherString + "\n"
              }
          }
                    
          String g(def aMap, i) {
              if (aMap == null) {
                  return "output : " + i + "\n"
              } else {
                  return "output : " + aMap.otherInt + aMap.otherString + "\n"
              }
          }
      }
      
      GT2 gt2 = new GT2(anInt:  1, aString : "A")
      print gt2.f(otherInt: 2, otherString: "B")
      print gt2.f()
      print gt2.g(otherInt: 3, otherString: "C",101)
      print gt2.g(otherInt: 4, otherString: "D", lalala : 17, 102)

* You can put the named parameters where ever you want
* If you want to pass an empty map or a not existing map, it must be the first parameter in the function call

      String h(aMap, i, j, k) {
        if (aMap == null) {
            return "output : " + i + j + k + "\n"
        } else {
            return "output : " + aMap.otherInt + aMap.otherString + i + j + k + "\n"
        }
      }

      print gt2.h(101, "17", 307, otherInt: 5, otherString: "e")
      print gt2.h(101, otherInt: 6, otherString: "f","17", 307 )
      print gt2.h(101, otherInt: 7, "17", otherString: "g",  307)
      print gt2.h(null , 101, "17", 307)

## Kotlin

* Kotlin supports primary and secondary constructors plus initializers
* In this comparison I only use primary constructors
* Constructors specify if values are constant or variable
* function parameters seem always to be constants, but I have not investigated this any further yet
* Both constructors and functions support real named parameters
* Both constructors and functions support default values

      class K2(val a : Int = 99, val b : String = "Z")

      fun printValues(a : Int = 0, b : String = "a") {
          print("values : ")
          print(a)
          print(b)
          print("\n")
      }

      fun main(args: Array<String>) {

          val k21 = K2(1, "A")
          printValues(k21.a, k21.b)
          printValues( a = k21.a, b = k21.b)
          printValues( b = k21.b, a = k21.a)
          printValues( a = k21.a)
          printValues( b = k21.b)
          printValues()

          val k22 = K2( b = "B")
          printValues(k22.a, k22.b)
          printValues( a = k22.a, b = k22.b)
          printValues( b = k22.b, a = k22.a)
          printValues( a = k22.a)
          printValues( b = k22.b)
          printValues()

          val k23 = K2( b = "C", a = 3)
          printValues(k23.a, k23.b)
          printValues( a = k23.a, b = k23.b)
          printValues( b = k23.b, a = k23.a)
          printValues( a = k23.a)
          printValues( b = k23.b)
          printValues()

          val k24 = K2( a = 4)
          printValues(k24.a, k24.b)
          printValues( a = k24.a, b = k24.b)
          printValues( b = k24.b, a = k24.a)
          printValues( a = k24.a)
          printValues( b = k24.b)
          printValues()

          val k25 = K2(5)
          printValues(k25.a, k25.b)
          printValues( a = k25.a, b = k25.b)
          printValues( b = k25.b, a = k25.a)
          printValues( a = k25.a)
          printValues( b = k25.b)
          printValues()

          val k26 = K2( a = 6)
          printValues(k26.a, k26.b)
          printValues( a = k26.a, b = k26.b)
          printValues( b = k26.b, a = k26.a)
          printValues( a = k26.a)
          printValues( b = k26.b)
          printValues()
      }

## Swift

* Swift supports multiple constructors with the name init, called initializers
* Both constructors and functions support real named parameters
* Both constructors and functions support default values
* Both constructors and functions demand that the order of named arguments may not be changed
* Swift supports unnamed arguments (underscore operator), I have not analyzed this here
* When using named parameters, you must use them, even if there is no other initializer as alternative available
* Swift supports different initializers with different parameters, same argument number and default values, I have not analyzed this here

      class K2 {
          let a : Int
          let b : String

          init (x : Int = 99, y : String = "Z") {
              a = x
              b = y
          }

          init (q : Bool) {
              if (q) {
                  a = 7
                  b = "U"
              } else {
                  a = 9
                  b = "V"
              }
          }
      }

      func printValues(a : Int = 0, b : String = "a") {
          print("values : ")
          print(a)
          print(b)
          print("\n")
      }

      let k20a = K2(q:true)
      printValues( a : k20a.a, b : k20a.b)
      let k20b = K2(q:false)
      printValues( a : k20b.a, b : k20b.b)

      let k21 = K2( x : 1, y : "A")
      // printValues( k21.a, k21.b) is forbidden due to missing argument names
      printValues( a : k21.a, b : k21.b)
      // printValues( b : k21.b, a : k21.a) is forbidden due to argument order
      printValues( a : k21.a)
      printValues( b : k21.b)
      printValues()

      let k22 = K2( y : "B")
      printValues( a : k22.a, b : k22.b)
      printValues( a : k22.a)
      printValues( b : k22.b)
      printValues()

      // let k23 = K2( b : "C", a : 3) is forbidden due to argument order

      let k24 = K2( x : 4)
      printValues( a : k24.a, b : k24.b)
      printValues( a : k24.a)
      printValues( b : k24.b)
      printValues()

      // let k25 = K2(5) is forbidden due to missing argument name

      let k26 = K2( x : 6)
      printValues( a : k26.a, b : k26.b)
      printValues( a : k26.a)
      printValues( b : k26.b)
      printValues()
