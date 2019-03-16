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

The answer is : No.

You can omit parts of the tuple by using the underscore '_' :

    def f() {
      return [1,2,3]
    }
    
    def (d, _, e) = f()
    
    assert d == 1
    println d
    
    assert e == 3
    println e
    
## Swift

The same as in Groovy :

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




