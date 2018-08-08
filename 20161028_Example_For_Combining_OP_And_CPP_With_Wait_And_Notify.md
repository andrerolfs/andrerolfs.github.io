{% include_relative menu.md %}

# 20161028 Example For Combining OP And CPP With Wait And Notify

This is the example implementation for how I want to implement the **Communication In CISystem Between Threads**.

The server thread produces String messages, which are passed to the clients by using the
[Observer Pattern](https://en.wikipedia.org/wiki/Observer_pattern) implementation, the `class Observable` provided by Java.

The clients implement the `Observer interface` to be able to pass new information to them
and they get a wake up call for this so they don't need to wait actively for the server to produce
something.

This is the main class :

    package com.wartbar.main;

    import com.wartbar.client.OClient;
    import com.wartbar.server.OServer;

    /**
     * Created by amos on 28.10.16.
     */
    public class Main {

    	public static void main(String... args) {
    		System.out.println("Starting threaded Observer-Pattern Example");

    		OServer server = new OServer();

    		for (int i=1; i<=5; i++) {
    			String name = "C" + i;
    			OClient client = new OClient(name, server);
    			server.addObserver(client);
    			Thread t = new Thread(client);
    			t.start();
    		}

    		Thread t = new Thread(server);
    		t.start();
    	}
    }

This is the server class :

    package com.wartbar.server;

    import java.util.ArrayList;
    import java.util.Observable;

    /**
     * Created by amos on 28.10.16.
     */
    public class OServer extends Observable implements Runnable {

    	private ArrayList<String> list = new ArrayList<>();
    	private int counter = 0;

    	private void produceMessage() {
    		counter++;
    		String message = "Message " + counter;
    		list.add(message);
    		System.out.println("server notifies " + countObservers() + " observers for " + message);
    	}

    	private void distributeMessage() {
    		setChanged();
    		notifyObservers(list);
    	}

    	private void wakeUpClients() {
    		notifyAll();
    	}

    	private void prepareList() {
    		list.clear();
    		System.out.println("server has cleared the list, only new messages will be communicated!");
    	}

    	/*
    	 * prepare the list (clear it),
    	 * produce a message,
    	 * distribute the message via Observer pattern,
    	 * wake up the client threads
    	 */
    	synchronized public void communicateMessage() {
    		prepareList();
    		produceMessage();
    		distributeMessage();
    		wakeUpClients();
    	}

    	private void sleepOneMilliSecond() {
    		try {
    			Thread.sleep(1);
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		}
    	}

    	/**
    	 * communicates 25x with clients,
    	 * sleep(1) only necessary in this example to give clients a chance to consume before producer has finished
    	 */
    	private void communicationLoop() {
    		for (int i=0; i<5; i++) {
    			for (int j=0; j<5; j++) {
    				communicateMessage();
    			}
    			sleepOneMilliSecond();
    		}
    		System.out.println("server has stopped production");
    	}

    	private void lastWakeUpClients() {
    		synchronized (this) {
    			System.out.println("last notification of the client threads");
    			notifyAll();
    		}
    	}

    	@Override
    	public void run() {
    		try {
    			communicationLoop();

    			/*
    			 * It is not guaranteed the clients have consumed everything before the server stopped the production!
    			 * In this case the server informed via console that it stopped production,
    			 * but there are clients which still have to consume.
    			 * To do this we need to wake them up a last time!
    			 */
    			lastWakeUpClients();
    		} catch (Exception e) {
    			System.out.println(e.getMessage());
    		}
    	}
    }

and this is the client class :

    package com.wartbar.client;

    import com.wartbar.server.OServer;
    import java.util.ArrayList;
    import java.util.Observable;
    import java.util.Observer;

    /**
     * Created by amos on 28.10.16.
     */
    public class OClient implements Runnable, Observer {

    	ArrayList<String> messages = new ArrayList<>();
    	String id;
    	OServer server;

    	public OClient(String name, OServer server) {
    		this.id = name;
    		this.server = server;
    	}

    	private void waitForServer() {
    		System.out.println("client " + id + " starts to wait for the server");
    		try {
    			server.wait();
    		} catch (InterruptedException e) {
    			e.printStackTrace();
    		}
    	}

    	private void consumeMessages() {
    		messages.clear();
    	}

    	private void reportMessages() {
    		System.out.println("client " + id + " : " + messages.size() + " messages in list");
    		for (String s : messages) {
    			System.out.println("client " + id + " : " + s);
    		}
    	}

    	/*
    	 * wait for the wake-up from the server,
    	 * report the messages, which were distributed by the server via the Observer-Pattern,
    	 * consume the messages (clear the list)
    	 */
    	private void communicate() {
    		/*
    		 * synchronizing via server
    		 *
    		 * 1. enables us to wait for the server signal that something is produced (no active-wait-polling!)
    		 * 2. guarantees the code block will be executed without interruption
    		 *
    		 */
    		synchronized(server) {
    			waitForServer();
    			reportMessages();
    			consumeMessages();
    		}
    	}

    	@Override
    	public void run() {
    		try {
    			while (true) {
    				communicate();
    			}
    		} catch (Exception e) {
    			System.out.println(e.getMessage());
    		}
    	}

    	@Override
    	public void update(Observable o, Object arg) {
    		/*
    		 * take the messages from ther server
    		 */
    		ArrayList<String> serverMessages = (ArrayList<String>)(arg);

    		/*
    		 * copy the messages to this clients list
    		 */
    		messages.addAll(serverMessages);

    		System.out.println("client " + id + " is updated");
    	}
    }

and this is the output :

    amos$ java -jar build/libs/ObserverProject-all.jar
    Starting threaded Observer-Pattern Example
    client C1 starts to wait for the server
    client C3 starts to wait for the server
    client C2 starts to wait for the server
    client C4 starts to wait for the server
    client C5 starts to wait for the server
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 1
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 2
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 3
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 4
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 5
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    client C5 : 5 messages in list
    client C5 : Message 1
    client C5 : Message 2
    client C5 : Message 3
    client C5 : Message 4
    client C5 : Message 5
    client C5 starts to wait for the server
    client C4 : 5 messages in list
    client C4 : Message 1
    client C4 : Message 2
    client C4 : Message 3
    client C4 : Message 4
    client C4 : Message 5
    client C4 starts to wait for the server
    client C2 : 5 messages in list
    client C2 : Message 1
    client C2 : Message 2
    client C2 : Message 3
    client C2 : Message 4
    client C2 : Message 5
    client C2 starts to wait for the server
    client C3 : 5 messages in list
    client C3 : Message 1
    client C3 : Message 2
    client C3 : Message 3
    client C3 : Message 4
    client C3 : Message 5
    client C3 starts to wait for the server
    client C1 : 5 messages in list
    client C1 : Message 1
    client C1 : Message 2
    client C1 : Message 3
    client C1 : Message 4
    client C1 : Message 5
    client C1 starts to wait for the server
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 6
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 7
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 8
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 9
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 10
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    client C1 : 5 messages in list
    client C1 : Message 6
    client C1 : Message 7
    client C1 : Message 8
    client C1 : Message 9
    client C1 : Message 10
    client C1 starts to wait for the server
    client C3 : 5 messages in list
    client C3 : Message 6
    client C3 : Message 7
    client C3 : Message 8
    client C3 : Message 9
    client C3 : Message 10
    client C3 starts to wait for the server
    client C2 : 5 messages in list
    client C2 : Message 6
    client C2 : Message 7
    client C2 : Message 8
    client C2 : Message 9
    client C2 : Message 10
    client C2 starts to wait for the server
    client C4 : 5 messages in list
    client C4 : Message 6
    client C4 : Message 7
    client C4 : Message 8
    client C4 : Message 9
    client C4 : Message 10
    client C4 starts to wait for the server
    client C5 : 5 messages in list
    client C5 : Message 6
    client C5 : Message 7
    client C5 : Message 8
    client C5 : Message 9
    client C5 : Message 10
    client C5 starts to wait for the server
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 11
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 12
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 13
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 14
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 15
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    client C5 : 5 messages in list
    client C5 : Message 11
    client C5 : Message 12
    client C5 : Message 13
    client C5 : Message 14
    client C5 : Message 15
    client C5 starts to wait for the server
    client C4 : 5 messages in list
    client C4 : Message 11
    client C4 : Message 12
    client C4 : Message 13
    client C4 : Message 14
    client C4 : Message 15
    client C4 starts to wait for the server
    client C2 : 5 messages in list
    client C2 : Message 11
    client C2 : Message 12
    client C2 : Message 13
    client C2 : Message 14
    client C2 : Message 15
    client C2 starts to wait for the server
    client C3 : 5 messages in list
    client C3 : Message 11
    client C3 : Message 12
    client C3 : Message 13
    client C3 : Message 14
    client C3 : Message 15
    client C3 starts to wait for the server
    client C1 : 5 messages in list
    client C1 : Message 11
    client C1 : Message 12
    client C1 : Message 13
    client C1 : Message 14
    client C1 : Message 15
    client C1 starts to wait for the server
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 16
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 17
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 18
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 19
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 20
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    client C1 : 5 messages in list
    client C1 : Message 16
    client C1 : Message 17
    client C1 : Message 18
    client C1 : Message 19
    client C1 : Message 20
    client C1 starts to wait for the server
    client C3 : 5 messages in list
    client C3 : Message 16
    client C3 : Message 17
    client C3 : Message 18
    client C3 : Message 19
    client C3 : Message 20
    client C3 starts to wait for the server
    client C2 : 5 messages in list
    client C2 : Message 16
    client C2 : Message 17
    client C2 : Message 18
    client C2 : Message 19
    client C2 : Message 20
    client C2 starts to wait for the server
    client C4 : 5 messages in list
    client C4 : Message 16
    client C4 : Message 17
    client C4 : Message 18
    client C4 : Message 19
    client C4 : Message 20
    client C4 starts to wait for the server
    client C5 : 5 messages in list
    client C5 : Message 16
    client C5 : Message 17
    client C5 : Message 18
    client C5 : Message 19
    client C5 : Message 20
    client C5 starts to wait for the server
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 21
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 22
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 23
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 24
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    server has cleared the list, only new messages will be communicated!
    server notifies 5 observers for Message 25
    client C5 is updated
    client C4 is updated
    client C3 is updated
    client C2 is updated
    client C1 is updated
    client C5 : 5 messages in list
    client C5 : Message 21
    client C5 : Message 22
    client C5 : Message 23
    client C5 : Message 24
    client C5 : Message 25
    client C5 starts to wait for the server
    client C4 : 5 messages in list
    client C4 : Message 21
    client C4 : Message 22
    client C4 : Message 23
    client C4 : Message 24
    client C4 : Message 25
    client C4 starts to wait for the server
    client C2 : 5 messages in list
    client C2 : Message 21
    client C2 : Message 22
    client C2 : Message 23
    client C2 : Message 24
    client C2 : Message 25
    client C2 starts to wait for the server
    client C3 : 5 messages in list
    client C3 : Message 21
    client C3 : Message 22
    client C3 : Message 23
    client C3 : Message 24
    client C3 : Message 25
    client C3 starts to wait for the server
    client C1 : 5 messages in list
    client C1 : Message 21
    client C1 : Message 22
    client C1 : Message 23
    client C1 : Message 24
    client C1 : Message 25
    client C1 starts to wait for the server
    server has stopped production
    last notification of the client threads
    client C1 : 0 messages in list
    client C1 starts to wait for the server
    client C3 : 0 messages in list
    client C3 starts to wait for the server
    client C2 : 0 messages in list
    client C2 starts to wait for the server
    client C4 : 0 messages in list
    client C4 starts to wait for the server
    client C5 : 0 messages in list
    client C5 starts to wait for the server
