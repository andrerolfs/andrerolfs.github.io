{% include_relative menu.md %}

# 20190322 Iterating Arrays And Lists

Here I compare these aspects :

* How are arrays and lists declared
* Is it possible to add new elements to arrays
* How do you iterate over complete lists and arrays

## Groovy

* Arrays and lists are the same in Groovy, they are declared with `[]`, Groovy itself has no Array datatype
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

* Arrays and lists are the same in Python, they are declared with `[]`, Python itself has no Array datatype
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
