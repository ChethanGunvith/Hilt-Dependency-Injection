
Dagger2 is dependency injection for android and java, And it’s developed by Google .Dependency injections is nice design pattern for our java classes 

Dependency in the program world , One class uses another class , Suppose object of class A has reference of object of class B then class A depends on class B, then class B dependency of class A . Class A need Class B to perform its works 
Example Like : 
User class has dependent on Address class . 
Image loader -> Picasa client internal uses Https client to load image from the web. Then Https clients is dependency of Image loader . 
Similarly -> Recycler View depends on Adapter . 
Injection means – Passing this dependency from outside . 
Idea hear is, classes shouldn’t be the responsible for creating object OR Searching for them , instead it should be initiate somewhere else and pass through the class which are need them . relies on somebody gives them dependencies. 
Car object should not be responsible for creating its own path, instead it should be pass from outside, how do we pass something from outside ? usually over constructor. OR certain method. 

Small class with smaller responsibility, Because our car is not responsible for creating engine OR wheels, It’s only job is to drive. 
Benefits is, 
It allows us to work independently between different modules, like engine/Wheeler class takes changes in its constructor doesn’t affect my code in other module
It’s avoid reflections for better performance. 
we can faster and more easily unit tested car class because Idea behind Unit test is we only test isolated component and other dependency
Real world examples : our class could depend and expensive resources like data base , web services , device locations and we don’t want to test this external resource as same go as our classes. Instead we usually want to mock this objects. Basically we want to feck the objects to simulate the behavior .
Main responsibility to get red off all these boilerplates code. 
Filed Injection : Field injections is meant for framework types  



Constructor injection
Field Injection
 Method injection
Components
Modules 
Providers 
![image](https://user-images.githubusercontent.com/3158100/113475004-26234c80-94ae-11eb-9e02-3da2d5505d8b.png)
