{% include_relative menu.md %}

[Back To : Computer Language Comparison](20190316_Computer_Language_Comparison.md)

# 20190316 Multiple Assignments

I stumbled over this problem when writing code in Groovy, where I return a tuple from a function.

You can access the single values of the tuple by destructuring it, but Intellij Idea will inform you about unused variables
if you don't use all values of the tuple.

I wanted to know if there is a way to receive only parts of the tuple.

I still learn how this works.

While writing the code examples I understood I also have to check and compare which languages support tuples with mixed types...

## Groovy

You can return tuples from functions :

    def f() {
      return [1,2,3]
    }
    
    def (a, b, c) = f()
    
    assert a == 1
    println a
    
    assert b == 2
    println b
    
    assert c == 3
    println c

My problem was : Do I have to create unused variables if I don't need the complete tuple?

The answer is : Yes, you cannot omit receiving single variables, the underscore '_' is only a fake, it is a real variable and Intellij Idea still warns me '_' is unused if I comment out the last two lines :

    def f() {
      return [1,2,3]
    }
    
    def (d, _, e) = f()
    
    assert d == 1
    println d
    
    assert e == 3
    println e
    
    assert _ == 2
    println _
    
## Swift

Here it works with the underscore '_', you cannot compile `print(_)` :

    func f() -> (Int, Int, Int){
        return (1,2,3)
    }
    
    let (a,b,c) = f()
    
    assert (a == 1, "error : a")
    print(a)
    
    assert (b == 2, "error : b")
    print(b)
    
    assert (c == 3, "error : c")
    print(c)
    
    let (d, _, e) = f()
    
    assert (d == 1, "error : d")
    print(d)
    
    assert (e == 3, "error : e")
    print(e)

## Python

The same problem as with Groovy, the underscore '_' works like a real variable  :

    def f():
        return 1, 2, 3

    a, b, c = f()
    assert a == 1
    print(a)  

    assert b == 2
    print(b)  

    assert c == 3
    print(c)

    e, _, f = f()

    assert e == 1
    print(e)

    assert _ == 2
    print(_)
    
    assert f == 3
    print(f)

## Kotlin

The same as for Swift, it really works, you cannot reference the underscore '_' :

	fun f(): Array<Int> {
	    return arrayOf(1,2,3)
	}

	fun main(args: Array<String>) {
	    val (a,b,c) = f()
	    assert(a == 1)
	    println(a)
	    assert(b == 2)
	    println(b)
	    assert(c == 3)
	    println(c)

	    val (d, _, e) = f()
	    assert(d == 1)
	    println(d)
	    assert(e == 3)
	    println(e)
	}
