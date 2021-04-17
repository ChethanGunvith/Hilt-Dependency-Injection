Hilt is the next generation dependency injection framework from Google. It's built on top of Dagger and simplifies DI greatly.Hilt provides a standard way
for your application to do DI by providing a container for every Android class in your project and automatically managing its lifecycle. Hilt is built on top 
of the well-known DI library Dagger , so you can benefit from Dagger in terms of compile-time accuracy, run-time performance, scalability, and Android Studio support . See Hilt and Dagger for more information


### Setting up Hilt
To set up Hilt in your app, follow the [Gradle Build Setup guide first](https://dagger.dev/hilt/gradle-setup).After installing all the dependencies and plugins, 
annotate your Application class with @HiltAndroidApp to use Hilt. You don’t need to do anything else or otherwise directly invoke it.

```Kotlin

@HiltAndroidApp
class App : Application()
```

Defining and injecting dependencies


To make something injectable in Hilt, you must tell Hilt how to create an instance of that thing. These instructions are called bindings.
There are three ways to define a binding in Hilt.
 1. Annotate the constructor with @Inject
 2. Use @Binds in a module
 3. Use @Provides in a module


### Annotate the constructor with @Inject
Any class can have a constructor annotated with @Inject which makes it available as a dependency anywhere in your project.

```Kotlin
class YourClassName @Inject constructor() {
  ...
}
``` 


### Using a module
The other two ways of making something injectable in Hilt involve using modules.
A Hilt module can be thought of as a collection of “recipes” that tell Hilt how to create an instance of something that doesn’t have a constructor — such as an interface or a system service.
In addition, any module can be replaced in your tests with a different module. This makes it easy to replace interface implementations with mocks, 

for example.
Modules are installed in a Hilt component specified using the @InstallIn annotation. I’ll explain this in more detail later.

Option 1: use @Binds to create binding for an interface
If you want to use OatMilk in your code when Milk is requested, create an abstract method inside a module and annotate it with @Binds. Note that OatMilk must itself be injectable for this to work, 
which you can achieve by annotating its constructor with @Inject.

```Kotlin 
interface Milk { ... }

class OatMilk @Inject constructor(): Milk {
  ...
}

@Module
@InstallIn(ActivityComponent::class)
abstract class MilkModule {
  @Binds
  abstract fun bindMilk(oatMilk: OatMilk): Milk
}
```


Option 2: use @Provides to create a factory function
When an instance can’t be constructed directly, you can create a provider. A provider is a factory function that returns an instance of an object.
An example of this is a system service such as ConnectivityManager which needs to be obtained from a context.

```Kotlin
@Module
@InstallIn(ApplicationComponent::class)
object ConnectivityManagerModule {
  @Provides
  fun provideConnectivityManager(
    @ApplicationContext context: Context
  ) = context.getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager
}
```

The Context object is injectable by default, as long as you annotate it with either @ApplicationContext or @ActivityContext.
Inject a dependency
Once your dependencies are injectable, you can inject them using Hilt in two ways.
 1. As constructor parameters
 2. As fields


### As constructor parameters

```Kotlin 
interface Milk { ... }
interface Coffee { ... }

class Latte @Inject constructor(
  private val Milk milk,
  private val Coffee coffee
) {
  ...
}

```

If the constructor is marked with @Inject, Hilt injects all of the parameters according to the bindings you defined for those types.

### As fields

```Kotlin 
interface Milk { ... }
interface Coffee { ... }

@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  @Inject lateinit var milk: Milk
  @Inject lateinit var coffee: Coffee
  
  ...
}
```


If the class is an entry point, here specified using the @AndroidEntryPoint annotation (more about that in the next section), all fields annotated with @Inject are injected.
Fields annotated with @Inject must be public. It’s also convenient to make them lateinit to avoid making them nullable, as their initial value prior to injection is null.
Note that injecting dependencies as fields is only useful when your class must have a constructor without parameters, such as Activity. In most cases, you’ll want to inject via constructor parameters instead.



### Android Entry Point
Most of your entry points will be one of these so-called Android Entry Points:
- Activity
- Fragment
- View
- Service
- BroadcastReceiver
- If that’s the case, annotate it with @AndroidEntryPoint.

``` Kotlin

@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  ...
}
```


### Other entry points
Most apps only ever need Android Entry Points, but if you’re interfacing with non-Dagger libraries or Android components that are not yet supported in Hilt,
you may need to create your own entry point to access the Hilt graph manually. You can read more about turning arbitrary classes into entry points.

ViewModel
A ViewModel is a special case: it’s not instantiated directly, as the framework needs to create them, but is also not an Android Entry Point. Instead, 
ViewModels use the special @ViewModelInject annotation which allows Hilt to inject dependencies into them when they’re created using by viewModels(), 
similar to how @Inject works for other classes.

```Kotlin
interface Milk { ... }
interface Coffee { ... }

class LatteViewModel @ViewModelInject constructor(
  private val milk: Milk,
  private val coffee: Coffee
) : ViewModel() {
  ...
}

@AndroidEntryPoint
class LatteActivity : AppCompatActivity() {
  private val viewModel: LatteViewModel by viewModels()

  ...
}

```
