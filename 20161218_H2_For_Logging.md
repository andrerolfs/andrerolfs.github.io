{% include_relative menu.md %}

# 20161218 H2 For Logging

Some requirements of [CISystem](CISystemRequirements.html) are specifying which information
have to be logged by the nodes. While implementing the concept of [actions](index.html#reflection-on-my-mind)
I had the idea of simply logging every action, so I could do a post mortem analysis about everything
happening on a node and would not even have to implement this every time again since it would be
a part of the action idea.

The only problem would be : log files, they are long, it is hard to search for patterns in them, they are no fun.

What I want is a way to handle these log files

* accessible from every node
* searchable for patterns
* with the option to remove outdated parts of the logs

I have decided to use the [H2 Database Engine](http://www.h2database.com/html/main.html)
to store the log entries, because

1. it is implemented in Java, you just need to have the *jar* file, this database is portable
2. it uses a file on your harddisk and you can access this database file via your browser by just running the *jar*
3. it can be run as in memory database, no need to persist the data to disk if you don't want to

I have already an example running in Eclipse based on [JPA](http://www.oracle.com/technetwork/java/javaee/tech/persistence-jsp-140049.html)
using [EclipseLink](http://www.eclipse.org/eclipselink/).

Now I am working on the **Gradle** build script to build the database model and the
application itself.

Building the application works, but generating the database model using **Gradle**
is not working yet, there seems to be a compiletime conflict with the **resources** folder
where `META-INF/persistence.xml` is stored.

I am working on this and will post my example code and build script when it is
finished.

