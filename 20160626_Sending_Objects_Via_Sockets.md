{% include_relative menu.md %}

# 20160626 Sending Objects Via Sockets

For my software project I have to implement a communication between
different PCs.

I have decided :

1. I want to communicate via sockets.
2. I want to communicate using objects.

Here I show you some code-snippets of how I did it.

I did not focus on closing the sockets and so on, the code is really only
meant as example for showing how to implement the object via socket communication.

How to build and run the example :

    gradle jar
    java -jar build/libs/CISystem.jar

The main function :

    public static void main(String... args) throws IOException {
      System.out.println("Setting up threads...");
      Thread serverThread = new Thread(new CIServerSocket());
      serverThread.start();
      Thread clientThread = new Thread(new CIClientSocket());
      clientThread.start();
    }

The class of the object to send :

    public class TestData implements Serializable {

      public String message;
      public int number;
      public File fileHandle;
    }

The client code :

    public class CIClientSocket implements Runnable {

      Socket socket = null;
      ObjectInputStream inputStream = null;
      ObjectOutputStream outputStream = null;

      public void test() {

        System.out.println("client receives data");
        try {
          while (true) {

            TestData testData = (TestData)inputStream.readObject();

            System.out.println("client has received this data :");
            System.out.println(testData.message);
            System.out.println(testData.number);
            System.out.println(testData.fileHandle.getPath());
          }
        } catch (Exception e) {
          System.out.println(e);
        }

        System.out.println("client stopped");
      }

      public void run() {
        try {
          connect();
          test();
        } catch (Exception e) {
          System.out.println(e);
        }
      }

      public void connect() throws Exception {
        System.out.println("Client connecting ......");
        socket = new Socket("localhost", 4444);
        inputStream = new ObjectInputStream(socket.getInputStream());
        outputStream = new ObjectOutputStream(socket.getOutputStream());
        System.out.println("Client connected ......");
      }

    }

The server code :

    public class CIServerSocket implements Runnable {

      ServerSocket server;
      ObjectOutputStream outputStream;
      //Solution 2: when only changing an existing memeber object before sending ...
      TestData testData = new TestData();

      public void send(String message, int number, File fileHandle) {
        try {

          System.out.println("server sends data : ");
          System.out.println(message);
          System.out.println(number);
          System.out.println(fileHandle.getPath());

          //solution 1: create a new object to send everytime ...
          //TestData testData = new TestData();
          testData.message = message;
          testData.number = number;
          testData.fileHandle = fileHandle;

          //solution 1 : ... so java will serialize the object always
          //             and cannot use an old reference
          //outputStream.writeObject(testData);

          //solution 2: ... tell java to treat object to send as new object
          //                and not to use hashed version due to old reference
          outputStream.writeUnshared(testData);
          outputStream.flush();
          System.out.println("server flushed");

        } catch (Exception e) {
          System.out.println(e);
        }
      }

      public void test() {

        try {
          Socket socket = server.accept();

          System.out.println("server accepted connection");
          outputStream = new ObjectOutputStream(socket.getOutputStream());

          System.out.println("server has created the outputstream");

          System.out.println("server sends data");

          send("hello world", 1, new File("/home/user/blog"));
          send("how are you", 22, new File("c:\\home\\user\\blog"));
          send("yeah!!!", 333, new File("/usr/local/bin"));
        } catch (Exception e) {
          System.out.println(e);
        }

      }

      public void run() {
        server = connect();
        test();
      }

      public ServerSocket connect() {
        ServerSocket ss=null;
        System.out.println("Server listening");
        try{
          ss = new ServerSocket(4444);
        } catch(IOException e) {
          e.printStackTrace();
          System.out.println("Server error");
        }

        return ss;
      }

    }

The console output :

    Setting up threads...
    Server listening
    Client connecting ......
    server accepted connection
    server has created the outputstream
    server sends data
    server sends data :
    hello world
    1
    /home/user/blog
    Client connected ......
    client receives data
    server flushed
    server sends data :
    how are you
    22
    c:\home\user\blog
    server flushed
    server sends data :
    yeah!!!
    333
    /usr/local/bin
    server flushed
    client has received this data :
    hello world
    1
    /home/user/blog
    client has received this data :
    how are you
    22
    c:\home\user\blog
    client has received this data :
    yeah!!!
    333
    /usr/local/bin
