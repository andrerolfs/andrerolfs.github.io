{% include_relative menu.md %}

# 20160820 Example For Multi Threading And Synchronized Data Structure Access

As mentioned in this [blog post](index.html#a-spike-for-implementing-the-threading-of-cisystem)
I have implemented a spike/test which shall show, that I am able to implement the [Requirements
For CISystem](CISystemRequirements.html) regarding multi threading and synchronized data structures
holding the information about the nodes of the network.

My idea is to abstract from real node information (IP address, port number, name, ...) and to use just a map instead,
where the key (of the node) is just a character of the alphabet and the value is an Integer value, which is increased by 1 every time a
thread adds its information to the map.

This is the description of what the classes/threads of my example do :

1. The class `Threading` owns the `Map` and creates the other threads
2. The `Worker` threads write an array of information (character strings) 10.000 times into the `Map`
3. The `Reader` thread reads 5 times the complete `Map` while it is filled by the `Worker` threads
4. The class `Threading` waits for all other threads to end to present the final state/content of the `Map`

This is the code of `Threading.java`

    package com.wartbar.example;

    import java.util.Map;
    import java.util.HashMap;
    import java.util.List;
    import java.util.ArrayList;
    import java.util.Collections;

    public class Threading {

      public static Map<String, Integer> synchronizedMap = null;
      public static List<Thread> threads = new ArrayList<Thread>();

      public static void initSynchronizedMap() {
        Map<String, Integer> unsynchronizedMap = new HashMap<String, Integer>();
        synchronizedMap = Collections.synchronizedMap(unsynchronizedMap);
      }

      public static void add(String item) {
        synchronized(synchronizedMap) {
          Integer value = 1;
          if (synchronizedMap.containsKey(item)) {
            value = synchronizedMap.get(item);
            value++;
          }
          synchronizedMap.put(item,value);
        }
      }

      public Threading() {
        initSynchronizedMap();

        String[] info = {"a","b","c","d","e","f","g","h","i","j","k","l","m","n"};

        for (int i=0; i<50; i++) {
          Thread t = new Thread(new Worker(info));
          t.start();
          threads.add(t);
        }

        Thread reader = new Thread(new Reader());
        reader.start();
        threads.add(reader);

        try {
          for (Thread t : threads) {
            t.join();
          }
        } catch (Exception e) {
          System.out.println(e);
        }

        synchronized(synchronizedMap) {
          System.out.println("Final state of the map: ");
          for (String item : synchronizedMap.keySet()) {
            System.out.println(item + ":" + synchronizedMap.get(item));
          }
        }
      }

    }

This is the code of `Worker.java`

    package com.wartbar.example;

    public class Worker implements Runnable {

      private String myInfo[];

      public Worker(String[] info) {
        myInfo = info;
      }

      public void run() {
        try {
          for (int i=0; i<10000; i++) {
            for (String item : myInfo) {
              Threading.add(item);
            }
          }
        } catch (Exception e) {
          System.out.println(e);
        }
      }
    }

This is the code of `Reader.java`

    package com.wartbar.example;

    public class Reader implements Runnable {

      public void run() {
        try {
          for (int i=0; i<5; i++) {
            synchronized(Threading.synchronizedMap) {
                System.out.println("Intermediate state of the map :");
                for (String item : Threading.synchronizedMap.keySet()) {
                  System.out.println(item + ":" + Threading.synchronizedMap.get(item));
                }
            }
            Thread.sleep(1000);
          }
        } catch (Exception e) {
          System.out.println(e);
        }
      }
    }

and this is the output when creating an object with `new Threading()`

    Intermediate state of the map :
    a:4338
    b:4333
    c:4330
    d:4324
    e:4323
    f:4320
    g:4316
    h:4314
    i:4313
    j:4311
    k:4308
    l:4306
    m:4306
    n:4303
    Intermediate state of the map :
    a:112741
    b:112736
    c:112731
    d:112726
    e:112723
    f:112720
    g:112719
    h:112715
    i:112710
    j:112707
    k:112705
    l:112703
    m:112698
    n:112696
    Intermediate state of the map :
    a:202609
    b:202606
    c:202599
    d:202594
    e:202591
    f:202589
    g:202587
    h:202581
    i:202579
    j:202576
    k:202573
    l:202570
    m:202569
    n:202566
    Intermediate state of the map :
    a:292532
    b:292525
    c:292519
    d:292508
    e:292504
    f:292503
    g:292503
    h:292502
    i:292500
    j:292499
    k:292499
    l:292498
    m:292494
    n:292491
    Intermediate state of the map :
    a:379525
    b:379519
    c:379518
    d:379516
    e:379513
    f:379508
    g:379506
    h:379505
    i:379504
    j:379503
    k:379500
    l:379500
    m:379499
    n:379495
    Final state of the map:
    a:500000
    b:500000
    c:500000
    d:500000
    e:500000
    f:500000
    g:500000
    h:500000
    i:500000
    j:500000
    k:500000
    l:500000
    m:500000
    n:500000
