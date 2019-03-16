{% include_relative menu.md %}

# 20190316 Multiple Assignments

## Groovy

You can return tuples from functions :

    def f() {
      return [1,2,3]
    }
    
    def (a, b, c) = f()
    
    assert a == 1
    assert b == 2
    assert c == 3

My problem was : Do I have to create unused variables if I don't need the complete tuple?

The answer is : No.

You can omit parts of the tuple by using the underscore '_' :

    def f() {
      return [1,2,3]
    }
    
    def (d, _, e) = f()
    
    assert d == 1
    assert e == 3





