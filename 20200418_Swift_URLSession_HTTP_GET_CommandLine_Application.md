{% include_relative menu.md %}

# 20200418 Swift URLSession HTTP GET CommandLine Application

I have implemented a command line application to test my understanding of how to use an URLSession object in Swift 
to perform a HTTP GET request is correct.

My code works, but what I missed when reading the documentation of URLSession was, it was asynchron, 
means you have to wait for the request to finish.

I did not handle this in my first try and my application terminated without performing the request.
Luckily ... better than thinking it works because you see the request being performed 
but then this only works sometimes ... that would be bad.

I have found the solution here : [Steven G. Harms : Learning to work with command-line Swift on Linux](https://gist.github.com/sgharms/3b0d7239dcb0623100b862a5fa6a2730)

There I learned, that you need to wait for asynchron tasks to finish.

I guess when doing this in an IOS APP it is a little bit more obvious, because you would connect it with a delegate to handle it.

Anyroad, I learned you need to use a `DispatchGroup` or `DispatchSemaphore` to wait for tasks to finish.

This is my code :

        import Foundation

        var components = URLComponents()
        components.scheme = "http"
        components.host = "localhost"
        components.port = 4567
        components.path = "/hello"
        let parameter1 = URLQueryItem(name:"x700", value:"macos")
        let parameter2 = URLQueryItem(name:"x800", value:"right")
        components.queryItems = [URLQueryItem]()
        components.queryItems!.append(parameter1)
        components.queryItems!.append(parameter2)

        print("url :")
        print(components.url!)

        var request = URLRequest(url: components.url!)
        request.httpMethod = "GET"
        request.setValue("application/json", forHTTPHeaderField: "Accept")

        let sessionConfiguration = URLSessionConfiguration.default
        let session = URLSession(configuration: sessionConfiguration)
        let group = DispatchGroup()

        print("starting sync")
        group.enter()
        session.dataTask(with: request, completionHandler: {
            (data, response, error) in
            print("Task ran!")
            group.leave()
        }).resume()

        print("ending sync")
        group.wait()
