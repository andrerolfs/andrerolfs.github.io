{% include_relative menu.md %}

# 20161008 Reflection

This is a small explanation for how to use reflection in Java like I do in my example
for code of [CISystem](http://www.wartbar.de/topic_CISYSTEM.html)

This explanation shall not replace a documentation, it is by no means complete
and I am no expert for this yet, but since I did not had much more when I started,
I thought this might be interesting for you :

You ask with `Class.forName()` for the class object.

You have to pass the full qualified class name, including the full package structure.

This example shows it for a `String` :

    Class<?> classType = Class.forName("java.lang.String");

With `classType.getConstructor()` you get the default constructor :

		Constructor<?> ctor = classType.getConstructor();

With the constructor and a type cast you create your object :

    String s = (String)ctor.newInstance();

When you want to request a constructor which requires parameters,
you provide a variable argument list when requesting the constructor with `getConstructor(Class<?>... parameterTypes)` and you pass a variable argument list containing these parameters calling
`newInstance(Object... initargs)`.

This example shows how to do it with creating a String :

			Class<?> clazz = Class.forName("java.lang.String");
			Constructor<?> ctor = clazz.getConstructor(String.class);
			String s = (String)ctor.newInstance(new Object[] { "Hello World!" });
			System.out.println(s);

which prints

      Hello World!

