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
