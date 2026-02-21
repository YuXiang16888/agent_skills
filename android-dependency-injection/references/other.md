# Hilt-Android - Other

**Pages:** 5

---

## Using Dagger in Android apps Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/dagger-android

**Contents:**
- Using Dagger in Android apps Stay organized with collections Save and categorize content based on your preferences.
- Best practices summary
- Adding dependencies
  - Kotlin
  - Java
- Dagger in Android
  - Kotlin
  - Java
  - Kotlin
  - Java

The Dagger basics page explained how Dagger can help you automate dependency injection in your app. With Dagger, you don't have to write tedious and error-prone boilerplate code.

To use Dagger in your project, add these dependencies to your application in your build.gradle file. You can find the latest version of Dagger in this GitHub project.

Consider an example Android app with the dependency graph from Figure 1.

Figure 1. Dependency graph of the example code

In Android, you usually create a Dagger graph that lives in your application class because you want an instance of the graph to be in memory as long as the app is running. In this way, the graph is attached to the app lifecycle. In some cases, you might also want to have the application context available in the graph. For that, you would also need the graph to be in the Application class. One advantage of this approach is that the graph is available to other Android framework classes. Additionally, it simplifies testing by allowing you to use a custom Application class in tests.

Because the interface that generates the graph is annotated with @Component, you can call it ApplicationComponent or ApplicationGraph. You usually keep an instance of that component in your custom Application class and call it every time you need the application graph, as shown in the following code snippet:

Because certain Android framework classes such as activities and fragments are instantiated by the system, Dagger can't create them for you. For activities specifically, any initialization code needs to go into the onCreate() method. That means you cannot use the @Inject annotation in the constructor of the class (constructor injection) as you did in the previous examples. Instead, you have to use field injection.

Instead of creating the dependencies an activity requires in the onCreate() method, you want Dagger to populate those dependencies for you. For field injection, you instead apply the @Inject annotation to the fields that you want to get from the Dagger graph.

For simplicity, LoginViewModel is not an Android Architecture Components ViewModel; it's just a regular class that acts as a ViewModel. For more information about how to inject these classes, check out the code in the official Android Blueprints Dagger implementation, in the dev-dagger branch.

One of the considerations with Dagger is that injected fields cannot be private. They need to have at least package-private visibility like in the preceding code.

Dagger needs to know that LoginActivity has to access the graph in order to provide the ViewModel it requires. In the Dagger basics page, you used the @Component interface to get objects from the graph by exposing functions with the return type of what you want to get from the graph. In this case, you need to tell Dagger about an object (LoginActivity in this case) that requires a dependency to be injected. For that, you expose a function that takes as a parameter the object that requests injection.

This function tells Dagger that LoginActivity wants to access the graph and requests injection. Dagger needs to satisfy all the dependencies that LoginActivity requires (LoginViewModel with its own dependencies). If you have multiple classes that request injection, you have to specifically declare them all in the component with their exact type. For example, if you had LoginActivity and RegistrationActivity requesting injection, you'd have two inject() methods instead of a generic one covering both cases. A generic inject() method doesn't tell Dagger what needs to be provided. The functions in the interface can have any name, but calling them inject() when they receive the object to inject as a parameter is a convention in Dagger.

To inject an object in the activity, you'd use the appComponent defined in your Application class and call the inject() method, passing in an instance of the activity that requests injection.

When using activities, inject Dagger in the activity's onCreate() method before calling super.onCreate() to avoid issues with fragment restoration. During the restore phase in super.onCreate(), an activity attaches fragments that might want to access activity bindings.

When using fragments, inject Dagger in the fragment's onAttach() method. In this case, it can be done before or after calling super.onAttach().

Let's tell Dagger how to provide the rest of the dependencies to build the graph:

For this example, you're using the Retrofit networking library. UserRemoteDataSource has a dependency on LoginRetrofitService. However, the way to create an instance of LoginRetrofitService is different from what you've been doing until now. It's not a class instantiation; it's the result of calling Retrofit.Builder() and passing in different parameters to configure the login service.

Apart from the @Inject annotation, there's another way to tell Dagger how to provide an instance of a class: the information inside Dagger modules. A Dagger module is a class that is annotated with @Module. There, you can define dependencies with the @Provides annotation.

Modules are a way to semantically encapsulate information on how to provide objects. As you can see, you called the class NetworkModule to group the logic of providing objects related to networking. If the application expands, you can also add how to provide an OkHttpClient here, or how to configure Gson or Moshi.

The dependencies of a @Provides method are the parameters of that method. For the previous method, LoginRetrofitService can be provided with no dependencies because the method has no parameters. If you had declared an OkHttpClient as a parameter, Dagger would need to provide an OkHttpClient instance from the graph to satisfy the dependencies of LoginRetrofitService. For example:

In order for the Dagger graph to know about this module, you have to add it to the @Component interface as follows:

The recommended way to add types to the Dagger graph is by using constructor injection (i.e. with the @Inject annotation on the constructor of the class). Sometimes, this is not possible and you have to use Dagger modules. One example is when you want Dagger to use the result of a computation to determine how to create an instance of an object. Whenever it has to provide an instance of that type, Dagger runs the code inside the @Provides method.

This is how the Dagger graph in the example looks right now:

Figure 2. Representation of the graph with LoginActivity being injected by Dagger

The entry point to the graph is LoginActivity. Because LoginActivity injects LoginViewModel, Dagger builds a graph that knows how to provide an instance of LoginViewModel, and recursively, of its dependencies. Dagger knows how to do this because of the @Inject annotation on the classes' constructor.

Inside the ApplicationComponent generated by Dagger, there's a factory-type method to get instances of all the classes it knows how to provide. In this example, Dagger delegates to the NetworkModule included in ApplicationComponent to get an instance of LoginRetrofitService.

Scopes were mentioned on the Dagger basics page as a way to have a unique instance of a type in a component. This is what is meant by scoping a type to the component's lifecycle.

Because you might want to use UserRepository in other features of the app and might not want to create a new object every time you need it, you can designate it as a unique instance for the whole app. It is the same for LoginRetrofitService: it can be expensive to create, and you also want a unique instance of that object to be reused. Creating an instance of UserRemoteDataSource is not that expensive, so scoping it to the component's lifecycle is not necessary.

@Singleton is the only scope annotation that comes with the javax.inject package. You can use it to annotate ApplicationComponent and the objects you want to reuse across the whole application.

Take care not to introduce memory leaks when applying scopes to objects. As long as the scoped component is in memory, the created object is in memory too. Because ApplicationComponent is created when the app is launched (in the Application class), it is destroyed when the app gets destroyed. Thus, the unique instance of UserRepository always remains in memory until the application is destroyed.

If your login flow (managed by a single LoginActivity) consists of multiple fragments, you should reuse the same instance of LoginViewModel in all fragments. @Singleton cannot annotate LoginViewModel to reuse the instance for the following reasons:

The instance of LoginViewModel would persist in memory after the flow has finished.

You want a different instance of LoginViewModel for each login flow. For example, if the user logs out, you want a different instance of LoginViewModel, rather than the same instance as when the user logged in for the first time.

To scope LoginViewModel to the lifecycle of LoginActivity you need to create a new component (a new subgraph) for the login flow and a new scope.

Let's create a graph specific to the login flow.

Now, LoginActivity should get injections from LoginComponent because it has a login-specific configuration. This removes the responsibility to inject LoginActivity from the ApplicationComponent class.

LoginComponent must be able to access the objects from ApplicationComponent because LoginViewModel depends on UserRepository. The way to tell Dagger that you want a new component to use part of another component is with Dagger subcomponents. The new component must be a subcomponent of the component containing shared resources.

Subcomponents are components that inherit and extend the object graph of a parent component. Thus, all objects provided in the parent component are provided in the subcomponent too. In this way, an object from a subcomponent can depend on an object provided by the parent component.

To create instances of subcomponents, you need an instance of the parent component. Therefore, the objects provided by the parent component to the subcomponent are still scoped to the parent component.

In the example, you must define LoginComponent as a subcomponent of ApplicationComponent. To do this, annotate LoginComponent with @Subcomponent:

You also must define a subcomponent factory inside LoginComponent so that ApplicationComponent knows how to create instances of LoginComponent.

To tell Dagger that LoginComponent is a subcomponent of ApplicationComponent, you have to indicate it by:

Creating a new Dagger module (e.g. SubcomponentsModule) passing the subcomponent's class to the subcomponents attribute of the annotation.

Adding the new module (i.e. SubcomponentsModule) to ApplicationComponent:

Note that ApplicationComponent doesn't need to inject LoginActivity anymore because that responsibility now belongs to LoginComponent, so you can remove the inject() method from ApplicationComponent.

Consumers of ApplicationComponent need to know how to create instances of LoginComponent. The parent component must add a method in its interface to let consumers create instances of the subcomponent out of an instance of the parent component:

Expose the factory that creates instances of LoginComponentin the interface:

If you build the project, you can create instances of both ApplicationComponent and LoginComponent. ApplicationComponent is attached to the lifecycle of the application because you want to use the same instance of the graph as long as the application is in memory.

What's the lifecycle of LoginComponent? One of the reasons why you needed LoginComponent is because you needed to share the same instance of the LoginViewModel between Login-related fragments. But also, you want different instances of LoginViewModel whenever there's a new login flow. LoginActivity is the right lifetime for LoginComponent: for every new activity, you need a new instance of LoginComponent and fragments that can use that instance of LoginComponent.

Because LoginComponent is attached to the LoginActivity lifecycle, you have to keep a reference to the component in the activity in the same way you kept the reference to the applicationComponent in the Application class. That way, fragments can access it.

Notice that the variable loginComponent is not annotated with @Inject because you're not expecting that variable to be provided by Dagger.

You can use the ApplicationComponent to get a reference to LoginComponent and then inject LoginActivity as follows:

LoginComponent is created in the activity's onCreate() method, and it'll get implicitly destroyed when the activity gets destroyed.

The LoginComponent must always provide the same instance of LoginViewModel each time it's requested. You can ensure this by creating a custom annotation scope and annotating both LoginComponent and LoginViewModel with it. Note that you cannot use the @Singleton annotation because it's already been used by the parent component and that'd make the object an application singleton (unique instance for the whole app). You need to create a different annotation scope.

In this case, you could have called this scope @LoginScope but it's not a good practice. The scope annotation's name should not be explicit to the purpose it fulfills. Instead, it should be named depending on its lifetime because annotations can be reused by sibling components such as RegistrationComponent and SettingsComponent. That's why you should call it @ActivityScope instead of @LoginScope.

Now, if you had two fragments that need LoginViewModel, both of them are provided with the same instance. For example, if you have a LoginUsernameFragment and a LoginPasswordFragment they need to get injected by the LoginComponent:

The components access the instance of the component that lives in the LoginActivity object. Example code for LoginUserNameFragment appears in the following code snippet:

And the same for LoginPasswordFragment:

Figure 3 shows how the Dagger graph looks with the new subcomponent. The classes with a white dot (UserRepository, LoginRetrofitService, and LoginViewModel) are the ones that have a unique instance scoped to their respective components.

Figure 3. Representation of the graph you built for the Android app example

Let's break down the parts of the graph:

The NetworkModule (and therefore LoginRetrofitService) is included in ApplicationComponent because you specified it in the component.

UserRepository remains in ApplicationComponent because it's scoped to the ApplicationComponent. If the project grows, you want to share the same instance across different features (e.g. Registration).

Because UserRepository is part of ApplicationComponent, its dependencies (i.e. UserLocalDataSource and UserRemoteDataSource) need to be in this component too in order to be able to provide instances of UserRepository.

LoginViewModel is included in LoginComponent because it's only required by the classes injected by LoginComponent. LoginViewModel is not included in ApplicationComponent because no dependency in ApplicationComponent needs LoginViewModel.

Similarly, if you hadn't had scoped UserRepository to ApplicationComponent, Dagger would automatically have included UserRepository and its dependencies as part of LoginComponent because that is currently the only place UserRepository is used.

Apart from scoping objects to a different lifecycle, creating subcomponents is a good practice to encapsulate different parts of your application from each other.

Structuring your app to create different Dagger subgraphs depending on the flow of your app helps towards a more performant and scalable application in terms of memory and startup time.

When building the Dagger graph for your application:

When you create a component, you should consider what element is responsible for the lifetime of that component. In this case, the Application class is in charge of ApplicationComponent and LoginActivity is in charge of LoginComponent.

Use scoping only when it makes sense. Overusing scoping can have a negative effect on your app's runtime performance: the object is in memory as long as the component is in memory and getting a scoped object is more expensive. When Dagger provides the object, it uses DoubleCheck locking instead of a factory-type provider.

One of the benefits of using dependency injection frameworks like Dagger is that it makes testing your code easier.

You don't have to use Dagger for unit tests. When testing a class that uses constructor injection, you don't need to use Dagger to instantiate that class. You can directly call its constructor passing in fake or mock dependencies directly just as you would if they weren't annotated.

For example, when testing LoginViewModel:

For integration tests, a good practice is to create a TestApplicationComponent meant for testing. Production and testing use a different component configuration.

This requires more up-front design of the modules in your application. The testing component extends the production component and installs a different set of modules.

FakeNetworkModule has a fake implementation of the original NetworkModule. There you can provide fake instances or mocks of whatever you want to replace.

In your integration or end-to-end tests, you'd use a TestApplication that creates the TestApplicationComponent instead of an ApplicationComponent.

Then, this test application is used in a custom TestRunner that you'll use to run instrumentation tests. For more information about this, check out the Using Dagger in your Android app codelab.

Dagger modules are a way to encapsulate how to provide objects in a semantic way. You can include modules in components but you can also include modules inside other modules. This is powerful, but can be easily misused.

Once a module has been added to either a component or another module, it's already in the Dagger graph; Dagger can provide those objects in that component. Before adding a module, check if that module is part of the Dagger graph already by checking if it's already added to the component or by compiling the project and seeing if Dagger can find the required dependencies for that module.

Good practice dictates that modules should only be declared once in a component (outside of specific advanced Dagger use cases).

Let's say you have your graph configured in this way. ApplicationComponent includes Module1 and Module2 and Module1 includes ModuleX.

If now Module2 depends on classes provided by ModuleX. A bad practice is including ModuleX in Module2 because ModuleX is included twice in the graph as seen in the following code snippet:

Instead, you should do one of the following: Refactor the modules and extract the common module out to the component. Create a new module with the objects that both modules share and extract it out to the component.

Not refactoring in this way results in a lot of modules including each other without a clear sense of organization and making it more difficult to see where each dependency is coming from.

Good practice (Option 1): ModuleX is declared once in the Dagger graph.

Good practice (Option 2): Common dependencies from Module1 and Module2 in ModuleX are extracted out to a new module named ModuleXCommon that is included in the component. Then two other modules named ModuleXWithModule1Dependencies and ModuleXWithModule2Dependencies are created with the dependencies that are specific to each module. All modules are declared once in the Dagger graph.

Assisted injection is a DI pattern that is used to construct an object where some parameters may be provided by the DI framework and others must be passed in at creation time by the user.

In Android, this pattern is common in details screens where the id of the element to show is only known at runtime, not at compile time when Dagger generates the DI graph. To learn more about assisted injection with Dagger, see the Dagger documentation.

If you haven't already, review the best practices section. To see how to use Dagger in an Android app, see the Using Dagger in an Android app codelab.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (json):
```json
plugins {
  id 'kotlin-kapt'
}

dependencies {
    implementation 'com.google.dagger:dagger:2.x'
    kapt 'com.google.dagger:dagger-compiler:2.x'
}
```

Example 2 (json):
```json
dependencies {
    implementation 'com.google.dagger:dagger:2.x'
    annotationProcessor 'com.google.dagger:dagger-compiler:2.x'
}
```

Example 3 (typescript):
```typescript
// Definition of the Application graph
@Component
interface ApplicationComponent { ... }

// appComponent lives in the Application class to share its lifecycle
class MyApplication: Application() {
    // Reference to the application graph that is used across the whole app
    val appComponent = DaggerApplicationComponent.create()
}
```

Example 4 (java):
```java
// Definition of the Application graph
@Component
public interface ApplicationComponent {
}

// appComponent lives in the Application class to share its lifecycle
public class MyApplication extends Application {

    // Reference to the application graph that is used across the whole app
    ApplicationComponent appComponent = DaggerApplicationComponent.create();
}
```

---

## Dagger basics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/dagger-basics

**Contents:**
- Dagger basics Stay organized with collections Save and categorize content based on your preferences.
- Benefits of using Dagger
- A simple use case in Dagger: Generating a factory
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java
- Dagger components

Manual dependency injection or service locators in an Android app can be problematic depending on the size of your project. You can limit your project's complexity as it scales up by using Dagger to manage dependencies.

Dagger automatically generates code that mimics the code you would otherwise have hand-written. Because the code is generated at compile time, it's traceable and more performant than other reflection-based solutions such as Guice.

Dagger frees you from writing tedious and error-prone boilerplate code by:

Generating the AppContainer code (application graph) that you manually implemented in the manual DI section.

Creating factories for the classes available in the application graph. This is how dependencies are satisfied internally.

Deciding whether to reuse a dependency or create a new instance through the use of scopes.

Creating containers for specific flows as you did with the login flow in the previous section using Dagger subcomponents. This improves your app's performance by releasing objects in memory when they're no longer needed.

Dagger automatically does all of this at build time as long as you declare dependencies of a class and specify how to satisfy them using annotations. Dagger generates code similar to what you would have written manually. Internally, Dagger creates a graph of objects that it can reference to find the way to provide an instance of a class. For every class in the graph, Dagger generates a factory-type class that it uses internally to get instances of that type.

At build time, Dagger walks through your code and:

Builds and validates dependency graphs, ensuring that:

Generates the classes that are used at runtime to create the actual objects and their dependencies.

To demonstrate how you can work with Dagger, let's create a simple factory for the UserRepository class shown in the following diagram:

Define UserRepository as follows:

Add an @Inject annotation to the UserRepository constructor so Dagger knows how to create a UserRepository:

In the above snippet of code, you're telling Dagger:

How to create a UserRepository instance with the @Inject annotated constructor.

What its dependencies are: UserLocalDataSource and UserRemoteDataSource.

Now Dagger knows how to create an instance of UserRepository, but it doesn't know how to create its dependencies. If you annotate the other classes too, Dagger knows how to create them:

Dagger can create a graph of the dependencies in your project that it can use to find out where it should get those dependencies when they are needed. To make Dagger do this, you need to create an interface and annotate it with @Component. Dagger creates a container as you would have done with manual dependency injection.

Inside the @Component interface, you can define functions that return instances of the classes you need (i.e. UserRepository). @Component tells Dagger to generate a container with all the dependencies required to satisfy the types it exposes. This is called a Dagger component; it contains a graph that consists of the objects that Dagger knows how to provide and their respective dependencies.

When you build the project, Dagger generates an implementation of the ApplicationGraph interface for you: DaggerApplicationGraph. With its annotation processor, Dagger creates a dependency graph that consists of the relationships between the three classes (UserRepository, UserLocalDatasource, and UserRemoteDataSource) with only one entry point: getting a UserRepository instance. You can use it as follows:

Dagger creates a new instance of UserRepository every time it's requested.

Sometimes, you need to have a unique instance of a dependency in a container. You might want this for several reasons:

You want other types that have this type as a dependency to share the same instance, such as multiple ViewModel objects in the login flow using the same LoginUserData.

An object is expensive to create and you don't want to create a new instance every time it's declared as a dependency (for example, a JSON parser).

In the example, you might want to have a unique instance of UserRepository available in the graph so that every time you ask for a UserRepository, you always get the same instance. This is useful in your example because in a real-life application with a more complex application graph, you might have multiple ViewModel objects depending on UserRepository and you don't want to create new instances of UserLocalDataSource and UserRemoteDataSource every time UserRepository needs to be provided.

In manual dependency injection, you do this by passing in the same instance of UserRepository to the constructors of the ViewModel classes; but in Dagger, because you are not writing that code manually, you have to let Dagger know you want to use the same instance. This can be done with scope annotations.

You can use scope annotations to limit the lifetime of an object to the lifetime of its component. This means that the same instance of a dependency is used every time that type needs to be provided.

To have a unique instance of a UserRepository when you ask for the repository in ApplicationGraph, use the same scope annotation for the @Component interface and UserRepository. You can use the @Singleton annotation that already comes with the javax.inject package that Dagger uses:

Alternatively, you can create and use a custom scope annotation. You can create a scope annotation as follows:

Then, you can use it as before:

In both cases, the object is provided with the same scope used to annotate the @Component interface. Thus, every time you call applicationGraph.repository(), you get the same instance of UserRepository.

It is important to be aware of Dagger's benefits and the basics of how it works before you can use it in more complicated scenarios.

In the next page, you'll learn how to add Dagger to an Android application.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (php):
```php
class UserRepository(
    private val localDataSource: UserLocalDataSource,
    private val remoteDataSource: UserRemoteDataSource
) { ... }
```

Example 2 (java):
```java
public class UserRepository {

    private final UserLocalDataSource userLocalDataSource;
    private final UserRemoteDataSource userRemoteDataSource;

    public UserRepository(UserLocalDataSource userLocalDataSource, UserRemoteDataSource userRemoteDataSource) {
        this.userLocalDataSource = userLocalDataSource;
        this.userRemoteDataSource = userRemoteDataSource;
    }

    ...
}
```

Example 3 (php):
```php
// @Inject lets Dagger know how to create instances of this object
class UserRepository @Inject constructor(
    private val localDataSource: UserLocalDataSource,
    private val remoteDataSource: UserRemoteDataSource
) { ... }
```

Example 4 (java):
```java
public class UserRepository {

    private final UserLocalDataSource userLocalDataSource;
    private final UserRemoteDataSource userRemoteDataSource;

    // @Inject lets Dagger know how to create instances of this object
    @Inject
    public UserRepository(UserLocalDataSource userLocalDataSource, UserRemoteDataSource userRemoteDataSource) {
        this.userLocalDataSource = userLocalDataSource;
        this.userRemoteDataSource = userRemoteDataSource;
    }
}
```

---

## Hilt and Dagger annotations cheat sheet Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/hilt-cheatsheet

**Contents:**
- Hilt and Dagger annotations cheat sheet Stay organized with collections Save and categorize content based on your preferences.

This cheat sheet allows you to quickly see what the different Hilt and Dagger annotations do and how to use them. The cheat sheet is also downloadable in PDF format.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

---

## Manual dependency injection Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/manual

**Contents:**
- Manual dependency injection Stay organized with collections Save and categorize content based on your preferences.
- Basics of manual dependency injection
  - Kotlin
  - Java
  - Kotlin
  - Java
- Managing dependencies with a container
  - Kotlin
  - Java
  - Kotlin

Android's recommended app architecture encourages dividing your code into classes to benefit from separation of concerns, a principle where each class of the hierarchy has a single defined responsibility. This leads to more, smaller classes that need to be connected together to fulfill each other's dependencies.

The dependencies between classes can be represented as a graph, in which each class is connected to the classes it depends on. The representation of all your classes and their dependencies makes up the application graph. In figure 1, you can see an abstraction of the application graph. When class A (ViewModel) depends on class B (Repository), there's a line that points from A to B representing that dependency.

Dependency injection helps make these connections and enables you to swap out implementations for testing. For example, when testing a ViewModel that depends on a repository, you can pass different implementations of Repository with either fakes or mocks to test the different cases.

This section covers how to apply manual dependency injection in a real Android app scenario. It walks through an iterated approach of how you might start using dependency injection in your app. The approach improves until it reaches a point that is very similar to what Dagger would automatically generate for you. For more information about Dagger, read Dagger basics.

Consider a flow to be a group of screens in your app that correspond to a feature. Login, registration, and checkout are all examples of flows.

When covering a login flow for a typical Android app, the LoginActivity depends on LoginViewModel, which in turn depends on UserRepository. Then UserRepository depends on a UserLocalDataSource and a UserRemoteDataSource, which in turn depends on a Retrofit service.

LoginActivity is the entry point to the login flow and the user interacts with the activity. Thus, LoginActivity needs to create the LoginViewModel with all its dependencies.

The Repository and DataSource classes of the flow look like this:

Here's what LoginActivity looks like:

There are issues with this approach:

There's a lot of boilerplate code. If you wanted to create another instance of LoginViewModel in another part of the code, you'd have code duplication.

Dependencies have to be declared in order. You have to instantiate UserRepository before LoginViewModel in order to create it.

It's difficult to reuse objects. If you wanted to reuse UserRepository across multiple features, you'd have to make it follow the singleton pattern. The singleton pattern makes testing more difficult because all tests share the same singleton instance.

To solve the issue of reusing objects, you can create your own dependencies container class that you use to get dependencies. All instances provided by this container can be public. In the example, because you only need an instance of UserRepository, you can make its dependencies private with the option of making them public in the future if they need to be provided:

Because these dependencies are used across the whole application, they need to be placed in a common place all activities can use: the Application class. Create a custom Application class that contains an AppContainer instance.

Now you can get the instance of the AppContainer from the application and obtain the shared UserRepository instance:

In this way, you don't have a singleton UserRepository. Instead, you have an AppContainer shared across all activities that contains objects from the graph and creates instances of those objects that other classes can consume.

If LoginViewModel is needed in more places in the application, having a centralized place where you create instances of LoginViewModel makes sense. You can move the creation of LoginViewModel to the container and provide new objects of that type with a factory. The code for a LoginViewModelFactory looks like this:

You can include the LoginViewModelFactory in the AppContainer and make the LoginActivity consume it:

This approach is better than the previous one, but there are still some challenges to consider:

You have to manage AppContainer yourself, creating instances for all dependencies by hand.

There is still a lot of boilerplate code. You need to create factories or parameters by hand depending on whether you want to reuse an object or not.

AppContainer gets complicated when you want to include more functionality in the project. When your app becomes larger and you start introducing different feature flows, there are even more problems that arise:

When you have different flows, you might want objects to just live in the scope of that flow. For example, when creating LoginUserData (that might consist of the username and password used only in the login flow) you don't want to persist data from an old login flow from a different user. You want a new instance for every new flow. You can achieve that by creating FlowContainer objects inside the AppContainer as demonstrated in the next code example.

Optimizing the application graph and flow containers can also be difficult. You need to remember to delete instances that you don't need, depending on the flow you're in.

Imagine you have a login flow that consists of one activity (LoginActivity) and multiple fragments (LoginUsernameFragment and LoginPasswordFragment). These views want to:

Access the same LoginUserData instance that needs to be shared until the login flow finishes.

Create a new instance of LoginUserData when the flow starts again.

You can achieve that with a login flow container. This container needs to be created when the login flow starts and removed from memory when the flow ends.

Let's add a LoginContainer to the example code. You want to be able to create multiple instances of LoginContainer in the app, so instead of making it a singleton, make it a class with the dependencies the login flow needs from the AppContainer.

Once you have a container specific to a flow, you have to decide when to create and delete the container instance. Because your login flow is self-contained in an activity (LoginActivity), the activity is the one managing the lifecycle of that container. LoginActivity can create the instance in onCreate() and delete it in onDestroy().

Like LoginActivity, login fragments can access the LoginContainer from AppContainer and use the shared LoginUserData instance.

Because in this case you're dealing with view lifecycle logic, using lifecycle observation makes sense.

Dependency injection is a good technique for creating scalable and testable Android apps. Use containers as a way to share instances of classes in different parts of your app and as a centralized place to create instances of classes using factories.

When your application gets larger, you will start seeing that you write a lot of boilerplate code (such as factories), which can be error-prone. You also have to manage the scope and lifecycle of the containers yourself, optimizing and discarding containers that are no longer needed in order to free up memory. Doing this incorrectly can lead to subtle bugs and memory leaks in your app.

In the Dagger section, you'll learn how you can use Dagger to automate this process and generate the same code you would have written by hand otherwise.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-05-08 UTC.

**Examples:**

Example 1 (php):
```php
class UserRepository(
    private val localDataSource: UserLocalDataSource,
    private val remoteDataSource: UserRemoteDataSource
) { ... }

class UserLocalDataSource { ... }
class UserRemoteDataSource(
    private val loginService: LoginRetrofitService
) { ... }
```

Example 2 (php):
```php
class UserLocalDataSource {
    public UserLocalDataSource() { }
    ...
}

class UserRemoteDataSource {

    private final Retrofit retrofit;

    public UserRemoteDataSource(Retrofit retrofit) {
        this.retrofit = retrofit;
    }

    ...
}

class UserRepository {

    private final UserLocalDataSource userLocalDataSource;
    private final UserRemoteDataSource userRemoteDataSource;

    public UserRepository(UserLocalDataSource userLocalDataSource, UserRemoteDataSource userRemoteDataSource) {
        this.userLocalDataSource = userLocalDataSource;
        this.userRemoteDataSource = userRemoteDataSource;
    }

    ...
}
```

Example 3 (swift):
```swift
class LoginActivity: Activity() {

    private lateinit var loginViewModel: LoginViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // In order to satisfy the dependencies of LoginViewModel, you have to also
        // satisfy the dependencies of all of its dependencies recursively.
        // First, create retrofit which is the dependency of UserRemoteDataSource
        val retrofit = Retrofit.Builder()
            .baseUrl("https://example.com")
            .build()
            .create(LoginService::class.java)

        // Then, satisfy the dependencies of UserRepository
        val remoteDataSource = UserRemoteDataSource(retrofit)
        val localDataSource = UserLocalDataSource()

        // Now you can create an instance of UserRepository that LoginViewModel needs
        val userRepository = UserRepository(localDataSource, remoteDataSource)

        // Lastly, create an instance of LoginViewModel with userRepository
        loginViewModel = LoginViewModel(userRepository)
    }
}
```

Example 4 (java):
```java
public class MainActivity extends Activity {

    private LoginViewModel loginViewModel;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        // In order to satisfy the dependencies of LoginViewModel, you have to also
        // satisfy the dependencies of all of its dependencies recursively.
        // First, create retrofit which is the dependency of UserRemoteDataSource
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("https://example.com")
                .build()
                .create(LoginService.class);

        // Then, satisfy the dependencies of UserRepository
        UserRemoteDataSource remoteDataSource = new UserRemoteDataSource(retrofit);
        UserLocalDataSource localDataSource = new UserLocalDataSource();

        // Now you can create an instance of UserRepository that LoginViewModel needs
        UserRepository userRepository = new UserRepository(localDataSource, remoteDataSource);

        // Lastly, create an instance of LoginViewModel with userRepository
        loginViewModel = new LoginViewModel(userRepository);
    }
}
```

---

## Dependency injection in Android Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection

**Contents:**
- Dependency injection in Android Stay organized with collections Save and categorize content based on your preferences.
- Fundamentals of dependency injection
  - What is dependency injection?
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Automated dependency injection

Dependency injection (DI) is a technique widely used in programming and well suited to Android development. By following the principles of DI, you lay the groundwork for good app architecture.

Implementing dependency injection provides you with the following advantages:

Before covering dependency injection in Android specifically, this page provides a more general overview of how dependency injection works.

Classes often require references to other classes. For example, a Car class might need a reference to an Engine class. These required classes are called dependencies, and in this example the Car class is dependent on having an instance of the Engine class to run.

There are three ways for a class to get an object it needs:

The third option is dependency injection! With this approach you take the dependencies of a class and provide them rather than having the class instance obtain them itself.

Here's an example. Without dependency injection, representing a Car that creates its own Engine dependency in code looks like this:

This is not an example of dependency injection because the Car class is constructing its own Engine. This can be problematic because:

Car and Engine are tightly coupled - an instance of Car uses one type of Engine, and no subclasses or alternative implementations can easily be used. If the Car were to construct its own Engine, you would have to create two types of Car instead of just reusing the same Car for engines of type Gas and Electric.

The hard dependency on Engine makes testing more difficult. Car uses a real instance of Engine, thus preventing you from using a test double to modify Engine for different test cases.

What does the code look like with dependency injection? Instead of each instance of Car constructing its own Engine object on initialization, it receives an Engine object as a parameter in its constructor:

The main function uses Car. Because Car depends on Engine, the app creates an instance of Engine and then uses it to construct an instance of Car. The benefits of this DI-based approach are:

Reusability of Car. You can pass in different implementations of Engine to Car. For example, you might define a new subclass of Engine called ElectricEngine that you want Car to use. If you use DI, all you need to do is pass in an instance of the updated ElectricEngine subclass, and Car still works without any further changes.

Easy testing of Car. You can pass in test doubles to test your different scenarios. For example, you might create a test double of Engine called FakeEngine and configure it for different tests.

There are two major ways to do dependency injection in Android:

Constructor Injection. This is the way described above. You pass the dependencies of a class to its constructor.

Field Injection (or Setter Injection). Certain Android framework classes such as activities and fragments are instantiated by the system, so constructor injection is not possible. With field injection, dependencies are instantiated after the class is created. The code would look like this:

In the previous example, you created, provided, and managed the dependencies of the different classes yourself, without relying on a library. This is called dependency injection by hand, or manual dependency injection. In the Car example, there was only one dependency, but more dependencies and classes can make manual injection of dependencies more tedious. Manual dependency injection also presents several problems:

For big apps, taking all the dependencies and connecting them correctly can require a large amount of boilerplate code. In a multi-layered architecture, in order to create an object for a top layer, you have to provide all the dependencies of the layers below it. As a concrete example, to build a real car you might need an engine, a transmission, a chassis, and other parts; and an engine in turn needs cylinders and spark plugs.

When you're not able to construct dependencies before passing them in — for example when using lazy initializations or scoping objects to flows of your app — you need to write and maintain a custom container (or graph of dependencies) that manages the lifetimes of your dependencies in memory.

There are libraries that solve this problem by automating the process of creating and providing dependencies. They fit into two categories:

Reflection-based solutions that connect dependencies at runtime.

Static solutions that generate the code to connect dependencies at compile time.

Dagger is a popular dependency injection library for Java, Kotlin, and Android that is maintained by Google. Dagger facilitates using DI in your app by creating and managing the graph of dependencies for you. It provides fully static and compile-time dependencies addressing many of the development and performance issues of reflection-based solutions such as Guice.

An alternative to dependency injection is using a service locator. The service locator design pattern also improves decoupling of classes from concrete dependencies. You create a class known as the service locator that creates and stores dependencies and then provides those dependencies on demand.

The service locator pattern is different from dependency injection in the way the elements are consumed. With the service locator pattern, classes have control and ask for objects to be injected; with dependency injection, the app has control and proactively injects the required objects.

Compared to dependency injection:

The collection of dependencies required by a service locator makes code harder to test because all the tests have to interact with the same global service locator.

Dependencies are encoded in the class implementation, not in the API surface. As a result, it's harder to know what a class needs from the outside. As a result, changes to Car or the dependencies available in the service locator might result in runtime or test failures by causing references to fail.

Managing lifetimes of objects is more difficult if you want to scope to anything other than the lifetime of the entire app.

Hilt is Jetpack's recommended library for dependency injection in Android. Hilt defines a standard way to do DI in your application by providing containers for every Android class in your project and managing their lifecycles automatically for you.

Hilt is built on top of the popular DI library Dagger to benefit from the compile time correctness, runtime performance, scalability, and Android Studio support that Dagger provides.

To learn more about Hilt see Dependency Injection with Hilt.

Dependency injection provides your app with the following advantages:

Reusability of classes and decoupling of dependencies: It's easier to swap out implementations of a dependency. Code reuse is improved because of inversion of control, and classes no longer control how their dependencies are created, but instead work with any configuration.

Ease of refactoring: The dependencies become a verifiable part of the API surface, so they can be checked at object-creation time or at compile time rather than being hidden as implementation details.

Ease of testing: A class doesn't manage its dependencies, so when you're testing it, you can pass in different implementations to test all of your different cases.

To fully understand the benefits of dependency injection, you should try it manually in your app as shown in Manual dependency injection.

To learn more about dependency injection, see the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (php):
```php
class Car {

    private val engine = Engine()

    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val car = Car()
    car.start()
}
```

Example 2 (java):
```java
class Car {

    private Engine engine = new Engine();

    public void start() {
        engine.start();
    }
}


class MyApp {
    public static void main(String[] args) {
        Car car = new Car();
        car.start();
    }
}
```

Example 3 (php):
```php
class Car(private val engine: Engine) {
    fun start() {
        engine.start()
    }
}

fun main(args: Array) {
    val engine = Engine()
    val car = Car(engine)
    car.start()
}
```

Example 4 (java):
```java
class Car {

    private final Engine engine;

    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.start();
    }
}


class MyApp {
    public static void main(String[] args) {
        Engine engine = new Engine();
        Car car = new Car(engine);
        car.start();
    }
}
```

---
