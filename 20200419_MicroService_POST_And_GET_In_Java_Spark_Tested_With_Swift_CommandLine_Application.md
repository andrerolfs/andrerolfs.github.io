{% include_relative menu.md %}

# 20200419 MicroService POST And GET In Java Spark Tested Swift CommandLine Application

This is my test example for a microservice providing POST and GET tested with client command line application implemented in Swift.

## The MicroService In Java Spark

        package de.wartbar.sparkserver;

        import spark.Request;
        import java.util.HashMap;
        import static spark.Spark.*;

        public class Main {

          private static final HashMap<String, String> map = new HashMap<>();

          private static String processGet(Request request) {
            synchronized (map) {
              String user = request.queryParams("user");
              System.out.println("GET request for content of user " + user + " : " +  map.get(user));
              return map.get(user);
            }
          }

          private static String processPost(Request request) {
            synchronized (map) {
              String user = request.queryParams("user");
              map.put(user, request.body());
              System.out.println("user " + user + " executed POST request : " + map.get(user));
              return "{ \"" + user + "\" : \"OK\" }";
            }
          }

          public static void main(String[] args) {
            get("/", (request, response) -> processGet(request));
            post("/", (request, response) -> processPost(request));
          }
        }

## The CommandLine Client In Swift

        import Foundation

        var components = URLComponents()
        components.scheme = "http"
        components.host = "localhost"
        components.port = 4567
        components.path = "/"
        let parameter1 = URLQueryItem(name:"user", value:"andre")
        components.queryItems = [URLQueryItem]()
        components.queryItems!.append(parameter1)

        print("url :")
        print(components.url!)

        let sessionConfiguration = URLSessionConfiguration.default
        let session = URLSession(configuration: sessionConfiguration)
        let group = DispatchGroup()

        var json = [String:Any]()
        json["Value1"] = 1
        json["Value2"] = 2
        json["Value3"] = 3

        let data = try JSONSerialization.data(withJSONObject: json, options: [])

        var postRequest = URLRequest(url: components.url!)
        postRequest.httpMethod = "POST"
        postRequest.httpBody = data
        postRequest.addValue("application/json", forHTTPHeaderField: "Content-Type")
        postRequest.addValue("application/json", forHTTPHeaderField: "Accept")

        print("starting sync POST request")
        group.enter()
        session.dataTask(with: postRequest, completionHandler: {
            (data, response, error) in
            print("response data of POST : " + String(data: data!, encoding: .utf8)!)
            group.leave()
        }).resume()
        print("ending sync POST request")
        group.wait()

        var getRequest = URLRequest(url: components.url!)
        getRequest.setValue("application/json", forHTTPHeaderField: "Accept")
        getRequest.httpMethod = "GET"

        print("starting sync GET request")
        group.enter()
        session.dataTask(with: getRequest, completionHandler: {
            (data, response, error) in
            print("response data of GET : " + String(data: data!, encoding: .utf8)!)
            group.leave()
        }).resume()
        print("ending sync GET request")
        group.wait()

