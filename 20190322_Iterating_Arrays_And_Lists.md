{% include_relative menu.md %}

[Back To : Computer Language Comparison](20190316_Computer_Language_Comparison.md)

# 20190322 Iterating Arrays And Lists

Here I compare these aspects :

* How are arrays and lists declared
* Is it possible to add new elements to arrays
* How do you iterate over complete lists and arrays

## Groovy

* Arrays and lists are the same in Groovy, they are declared with `[]`, Groovy itself has no array datatype
* Since there are only lists, you can `add` new elements to arrays
* You can iterate over lists using the `for-loop` where you can break and the 'each' method, where you cannot break

      def words = ["Hello", ",", " ", "World", "!"]
      
      println ""
      println "Sentence :"
      for (String w : words) {
          print w
      }
      
      words.add(" ")
      words.add("How")
      words.add(" ")
      words.add("are")
      words.add(" ")
      words.add("you")
      words.add("?")
      
      println ""
      println "Sentence :"
      words.each { w ->
          print w
      }
       
## Python

* Arrays and lists are the same in Python, they are declared with `[]`, Python itself has no array datatype
* Python has an `Array` module for maths operations which can only work with numbers (not shown here) 
* Since there are only lists, you can `append` new elements to arrays
* You can iterate over lists using the `for-loop` and you can implement your own `foreach-function`(shown later)

      words = ["Hello", ",", " ", "World", "!"]
      for w in words:
          print w

      words.append("How")
      words.append("are")
      words.append("you")
      words.append("?")

      for w in words:
          print w

## Kotlin

* In Kotlin arrays can be created with `arrayOf`
* Arrays in Kotlin have a fixed size, you cannot add elements to it
* With `listOf` you can create immutable lists where you cannot add elements, too
* You need to use a `MutableList` to add elements to it

      var words1 = listOf("Hello", " ", "World", "!")
      var words2 = arrayOf("Hello", " ", "World", "!")
       
      val iterator = words2.iterator()
      
      for (w in words1) {
          print(w)
      }
      
      iterator.forEach {w ->
          print(w)
      }
        
      var words3 : MutableList<String> = mutableListOf("Hello", " ", "World", "!")
      
      words3.add("How")
      words3.add(" ")
      words3.add("are")
      words3.add(" ")
      words3.add("you ")
      words3.add("?")
      
      for (w in words3) {
          print(w)
      }
      
## Swift

* In Swift Arrays and Lists are both called `Array`
* If you declare it `var` it will be a variable list where you can add elements with `append`
* If you declare it `let`it becomes an array with a constant immutable size
* Both `for-loop`and `forEach`method are available

      var words = ["Hello", ",", " ", "World", "!"]
      
      for w in words {
          print(w)
      }
      
      words.append("How")
      words.append(" ")
      words.append("Are")
      words.append(" ")
      words.append("You")
      words.append("?")

      words.forEach { w in
          print(w)
      }
