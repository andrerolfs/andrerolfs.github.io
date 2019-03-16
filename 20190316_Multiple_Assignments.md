{% include_relative menu.md %}

# 20190316 Multiple Assignments

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

The answer is : Yes, you cannot omit receiving single variables, the underscore '_' is only a fake, it is a real variable.

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

The the same problem as with Groovy :

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
