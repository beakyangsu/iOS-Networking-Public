
> ### reference : https://appstuff.teachable.com/courses/enrolled/2176637

The full code is [iOS-Networking-private](https://github.com/beakyangsu/iOS-Networking-private). The reason for locking it is to protect the copyright of the original author since the reference lectures are paid.

<a href="https://github.com/beakyangsu/iOS-Networking/blob/main/README.md"><img alt="Static Badge" src="https://img.shields.io/badge/switch_to-readme_kr-white?style=plastic&link=https://github.com/beakyangsu/iOS-Networking/blob/main/README.md">

![iOS badge](https://img.shields.io/badge/iOS-15.0%2B-green)
![iOS badge](https://img.shields.io/badge/iOS-Swift_UI%2B-red)
![iOS badge](https://img.shields.io/badge/iOS-Async_Await%2B-yellow)

## 1. Networking: Handler vs Concurrency
async wait is more suitable than handling networking
+ You don’t have to worry about the optional type like self? 
+ Since you don’t use a handler, you don’t have to worry about the retain cycle.
+ The code is much cleaner and readability increases
+ Even when accessing UI-thread, you only need to use MainActor, so the code is cleaner then DispatchQueue.main.async{} version.


## 2. observedObject vs EnviromentObejct

 + EnvironmentObejct and observedObject share the same data across multiple views through injection.
 + The code is messy because observedObject has to be passed continuously as a parameter.
 + If you declare @StateObject in the rootview of APP and inject it into contentView(),
 After that, all views inside ContentView can access EnviromentObejct without any additional coding.
 + However, on the other hand, when to decide using some object as a EnviromentObejct then, it must be accessed from all views.
 + If you try to solve everything with EnvironmentObejct, you will have to put all the logic for different views in one class.
 There is too much work for one class, not recommended. so the code becomes messy.
 + Appropriate use of observedObject and EnvironmentObejct is required depending on the situation.

## 3.Dependency Injection

 Define the service as a protocol and inject it.

 + Calling the API for every test is too much abuse.
 + Switching between mock-up service and actual service should be easy.
 + It is efficient to develop a service that can be tested without calling the actual API as a mock-up service.
 + If you use dependency injection as a protocol, the actual modification only needs to be done once in the root app View and other parts do not need to be touched -> Easy to modify.
 + You must not define other functions within class witch inherit protocol
 + In this case, the advantage of dependency injection based on the protocol type is lost. Ultimately, the function becomes dependent on a specific class type and service switching is not possible.




## 4. Unit test

 + Helps you create high-quality code
 + Prevention is possible by testing exception cases through unit testing. You can check whether the code is well structured or not.
 + Code that is easy to write test code ultimately means is well-organized code and easy to maintain.
 + When refactoring or changing business logic, You can check whether the unit test was written properly by simply checking that it operates properly during build without modifying the unit test separately.
 + When new code is modified, it is possible to check through unit tests whether functions that previously had no problems still have no problems (regression prevention).
 + Serves as documentation - When you look at a unit test, you can immediately understand what each element does and what input and output are.
 + Based on this, it makes collaboration easy.
 + Unit tests automate the sustainable integration (merge) (continuous integration == CI) pipeline. -> Unit tests run continuously and repeatedly to ensure that issues are caught whenever there is a change in the code.



## 5. Pagination
 + Do not retrieve all data from the server at once
 + When the end of the list is reached, additional items are fetched.
 + Performance-wise, there is no need to import everything at once.
 + It is more advantageous to retrieve it at the point when it is exposed to users.


## 6. async urlsession more 
 + https://www.wwdcnotes.com/notes/wwdc21/10095/

 + Upload data
````
func upload(for request: URLRequest, from data: Data) async throws -> (Data, URLResponse)
func upload(for request: URLRequest, fromFile url: URL) async throws -> (Data, URLResponse)
````


 + Example:

 Encode data into data type
 ````
 let order = Order(customerId: "12345", items: ["Cheese pizza", "Diet soda"])
 guard let data = try? JSONEncoder().encode(order) else {
     return
 }
````

Create URLRequest and specify it as POS
 ````
 var request = URLRequest(url: url)
 request.httpMethod = "POST"
 let (data, response) = try await URLSession.shared.upload(for: request, from data: data)
 guard let httpResponse = response as? HTTPURLResponse,
       httpResponse.statusCode == 201 /* Created */ else {
   throw MyNetworkingError.invalidServerResponse
 }
````



## 7. AsyncImage

 + It is very convenient as it supports downloading images using Async.
 + However, caching is not supported.
````
 AsyncImage(url: URL(string: coin.image)) { image in
     image
         .resizable()
         .frame(width: 32, height: 32)
 } placeholder: {
     EmptyView()
 }
````
