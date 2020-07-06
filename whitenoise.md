{% include_relative menu.md %}

# white noise - My commented link page

Here I simply list links I where I cannot add much more to the content, I could need again and where I make comments for myself.

## 20200706

* [Groovy : Getters and Setters](https://groovy-lang.org/style-guide.html#_getters_and_setters)

Comments :

1. `x.y = 5`calls `x.setY(5)`
2. it is not needed to have a variable `y`, `setY()` can write the value where ever you need to have it and you can still use `x.y = 5`
3. only implementing `getY()` without `setY()` and without declaring a variable `y` makes `y`readonly

## 20200705

* [Equals, Is, CompareTo, and the Groovy Identity Operator](https://objectpartners.com/2018/11/07/equals-is-compareto-and-the-groovy-identity-operator/)

Comments :

In **Groovy** `==` calls `compareTo` if the class of the compared class implements the interface `Comparable`.
If the class does not implement `Comparable`, `==` calls `equals`.

To autmatically add the `equals()` function, you can use `@EqualsAndHashCode` to annotate the class, then `equals()` will be implemented to compare the values of the class members.

* [Why to use @Override annotation in Java/Groovy](https://beginnersbook.com/2014/07/override-annotation-in-java/)
* [How object defines/implements "equals()"](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)

Comments :

In bothe **Java** and **Groovy** it is the same reason : You should use `@Override`so the compiler can check you override an existing function with the same signature. If you fail in this, your function will be a new function but not overriding anything, so your new function will not have the use you aim at.

I use `@Override` to check I correctly override `boolean equals(Object obj)` :

    class X {
    
        @Override
        boolean equals(Object other) {
            X o = (X)other
            return this.equals(o)
        }
    }

I use `@Override` to check I correctly override `int CompareTo(X obj)` :

    @EqualsAndHashCode
    class X implements Comparable<X> {
    
        @Override
        int compareTo(X other) {
            return this.equals(x) ? 1 : 0
        }
    }

## 20200618

* [21 CMD Commands All Windows Users Should Know](https://helpdeskgeek.com/help-desk/21-cmd-commands-all-windows-users-should-know/#)<br>
* [Command Prompt (CMD) - 8 network commands you should know](https://www.digitalcitizen.life/command-prompt-advanced-networking-commands)

Comments :

`ipconfig /all` to check IP address

* [Here are the most popular ways to make an HTTP request in JavaScript](https://www.freecodecamp.org/news/here-is-the-most-popular-ways-to-make-an-http-request-in-javascript-954ce8c95aaa/)<br>
* [Executing a url without opening it on the browser in Javascript / Jquery](https://stackoverflow.com/questions/47881606/executing-a-url-without-opening-it-on-the-browser-in-javascript-jquery/47881934)

Comments :

My idea is to have a view in small web service which is reloaded if a get request from client side sees a change in the data. This way I can have a timed get request loading only a true/false and if true comes the whole view is updated.

* [Spring Service Registration and Discovery](https://spring.io/guides/gs/service-registration-and-discovery/)

Comments :

I want to learn how to connect different microservices all available on different URLs but through the same IP address and port.

* [Angular vs JQuery](https://www.educba.com/angular-vs-jquery/)

Comments :

I need to make some web pages more dynamic. The comparison angular vs jquery brings it to the point that jquery is a library good to make the javascript code of web pages simpler, smarter, but which should not be used if you rather need a complete framework like angular, to create a complete web application.

My current understanding is, it depends if you want to have a spring boot service with thymeleaf as html framework where jquery can improve the Javascript part or if you want to use angular instead.

* [How to setup your Core Data OSX application](https://dev.mikamai.com/2015/07/01/how-to-setup-your-core-data-osx-application/)

Comments :

I admit, I am no fan of something so hard coupled as Core Data and it makes it even harder for me if I need to create an IOS application only to look into the Core Data coding.

My idea is to instead write an OSX application using Core Data.

