{% include_relative menu.md %}

# 20160710 Calling Groovy From Java To Read Json

For my software project CISystem I have to read a configuration containing
some information about a server and a list of client addresses.

I have decided to read the configuration from a Json file and
since Java has no native Json support and I did not want to use third party
jars I haver decided to call a Groovy script instead, because Groovy has native
Json support and CISystem will have other connections to using Groovy.

For more information about how I came to write this code
please read my [blog post](index.html#calling-groovy-from-java-to-read-json).

Here I show you the code.

I did not focus on implementing a proper exception handling, the code is really only
meant as example for showing how to call Groovy scripts from Java and how to read
a Json file in Groovy.

This is the complete example code :
[zipped example of Calling Groovy From Java To Read Json](examples/CallingGroovyFromJavaToReadJson.zip)

I have skipped providing a gradle file, you would have to adapt the classpath to the groovy jar
and maybe it is interesting for some of you how to do it directly in the shell.

How to build and run the example, please adapt the classpath :

    javac CIConfigNetwork.java
    javac -cp .:/home/amos/bin/groovy-2.4.6/embeddable/groovy-all-2.4.6.jar ReadConfigNetworkExample.java
    java -cp .:/home/amos/bin/groovy-2.4.6/embeddable/groovy-all-2.4.6.jar ReadConfigNetworkExample

A helper class to handle pairs, you could also use it for key-value pairs, but
I wanted to have more abstract names for the member variables, because an
IP address is no key for the port value when you have several ports in use for the
same IP :

    public class CIPair<L,R> {

      public L leftValue;
      public R rightValue;

    }

The Java class representing the Json configuration :

    import java.util.ArrayList;

    public class CIConfigNetwork {

      public String myName;
      public String myPort;
      public ArrayList<CIPair> nodeList = new ArrayList<CIPair>();

    }

The Groovy script :

    import groovy.json.JsonSlurper
    import CIConfigNetwork

    CIConfigNetwork runScript(CIConfigNetwork input) {
      String inputString = new File("configNetwork.json").text

      JsonSlurper jsonSlurper = new JsonSlurper()

      Map jsonResult = (Map)jsonSlurper.parseText(inputString)

      input.myName = jsonResult['myName']
      input.myPort = jsonResult['myPort']

      List nodes = (List)jsonResult['nodeList']

      for (node in nodes) {
        Map m = (Map)node
        CIPair<String, String> pair = new CIPair<String, String>()
        pair.leftValue = m.get("IP")
        pair.rightValue = m.get("Port")
        input.nodeList.add(pair)
      }

      return input
    }

    def printConfigNetwork(CIConfigNetwork cicn) {
      println "Server " + cicn.myName + ":" + cicn.myPort
      for (node in cicn.nodeList) {
        println "Node   " + node.leftValue + ":" + node.rightValue
      }
    }

    CIConfigNetwork cicn = new CIConfigNetwork()
    CIConfigNetwork cicnOut = runScript(cicn)
    println "Within groovy script : the reference passed as parameter"
    printConfigNetwork(cicn)

    println "Within groovy script : the reference returned by the function"
    printConfigNetwork(cicnOut)

You can execute it standalone for testing with :

    groovy ReadConfigNetworkJson.groovy

and it should produce this output :

    Within groovy script : the reference passed as parameter
    Server N150:8900
    Node   localhost:8910
    Node   localhost:8911
    Within groovy script : the reference returned by the function
    Server N150:8900
    Node   localhost:8910
    Node   localhost:8911

This is the Java code calling the Groovy script :

    import groovy.lang.GroovyClassLoader;
    import groovy.lang.GroovyObject;
    import java.io.File;

    public class ReadConfigNetworkExample {

      public static CIConfigNetwork readConfigNetwork(CIConfigNetwork cicn) throws Exception {

        final GroovyClassLoader classLoader = new GroovyClassLoader();

        Class groovy = classLoader.parseClass(new File("ReadConfigNetworkJson.groovy"));
        GroovyObject groovyObj = (GroovyObject)groovy.newInstance();

        CIConfigNetwork rv = (CIConfigNetwork)groovyObj.invokeMethod("runScript", new Object[] {cicn});

        System.out.println("Within Java : the input parameter");
        groovyObj.invokeMethod("printConfigNetwork", new Object[] {cicn});

        System.out.println("Within Java : the return value");
        groovyObj.invokeMethod("printConfigNetwork", new Object[] {rv});

        return rv;
      }

      public static void main(final String[] args) throws Exception {

        CIConfigNetwork cicn = new CIConfigNetwork();
        CIConfigNetwork rv = readConfigNetwork(cicn);
        System.out.println("Returned by Groovy : " + rv.myName + ":" + rv.myPort);
      }
    }

You have to compile it including the classpath to the groovy-all-x.y.z.jar

    javac -cp .:/home/amos/bin/groovy-2.4.6/embeddable/groovy-all-2.4.6.jar ReadConfigNetworkExample.java

and when calling it with something like (please adapt the path) this

    java -cp .:/home/amos/bin/groovy-2.4.6/embeddable/groovy-all-2.4.6.jar ReadConfigNetworkExample

you should get this output :

    Within Java : the input parameter
    Server N150:8900
    Node   localhost:8910
    Node   localhost:8911
    Within Java : the return value
    Server N150:8900
    Node   localhost:8910
    Node   localhost:8911
    Returned by Groovy : N150:8900
