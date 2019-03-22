{% include_relative menu.md %}

# 20190322 Iterating Arrays And Lists

Here I compare these aspects :

* How are arrays and lists declared
* Is it possible to add new elements to arrays
* How do you iterate over complete lists and arrays

## Groovy

* Arrays and lists are the same in Groovy, they are declared with `[]`, Groovy itself has no pure arrays
* Since there are only lists, you can add new elements to arrays
* You can iterate over lists using `for-loop` where you can break and the 'each' method, where you cannot break

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
