{% include_relative menu.md %}

# 20200418 MicroService Java Spark HTTP GET

For testing the Java [Spark](http://sparkjava.com) framework I have implemented a small example with a HTTP GET handler.

FYI : Yes, I know the [HTTP Protocol](https://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol) 
and that GET requests shall not store anything or change the state of the service.

In this example I store all HTTP header parameters in a member map of my microservice to see if 
its state is re-used as expected for continuous accesses.

This is my Java code :

        package de.wartbar.sparkserver;

        import com.google.gson.Gson;
        import spark.Request;
        import java.util.HashMap;
        import static spark.Spark.*;

        public class Main {

          private static HashMap<String, String> map = new HashMap<>();

          public static String addParameter(Request req) {

            synchronized (map) {
              for (String key : req.queryParams()) {
                System.out.println(key + " : " + req.queryParams(key));
                map.put(key, req.queryParams(key));
              }
            }

            Gson gson = new Gson();
            String json = gson.toJson(map);

            return json;
          }

          public static void main(String[] args) {

            get("/hello", (req, res) -> addParameter(req));

          }
        }
