# Hilt-Android - Hilt Basics

**Pages:** 2

---

## Dependency injection with Hilt Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/hilt-android

**Contents:**
- Dependency injection with Hilt Stay organized with collections Save and categorize content based on your preferences.
- Adding dependencies
  - Groovy
  - Kotlin
  - Groovy
  - Kotlin
  - Groovy
  - Kotlin
- Hilt application class
  - Kotlin

Hilt is a dependency injection library for Android that reduces the boilerplate of doing manual dependency injection in your project. Doing manual dependency injection requires you to construct every class and its dependencies by hand, and to use containers to reuse and manage dependencies.

Hilt provides a standard way to use DI in your application by providing containers for every Android class in your project and managing their lifecycles automatically. Hilt is built on top of the popular DI library Dagger to benefit from the compile-time correctness, runtime performance, scalability, and Android Studio support that Dagger provides. For more information, see Hilt and Dagger.

This guide explains the basic concepts of Hilt and its generated containers. It also includes a demonstration of how to bootstrap an existing app to use Hilt.

First, add the hilt-android-gradle-plugin plugin to your project's root build.gradle file:

Then, apply the Gradle plugin and add these dependencies in your app/build.gradle file:

Hilt uses Java 8 features. To enable Java 8 in your project, add the following to the app/build.gradle file:

All apps that use Hilt must contain an Application class that is annotated with @HiltAndroidApp.

@HiltAndroidApp triggers Hilt's code generation, including a base class for your application that serves as the application-level dependency container.

This generated Hilt component is attached to the Application object's lifecycle and provides dependencies to it. Additionally, it is the parent component of the app, which means that other components can access the dependencies that it provides.

Once Hilt is set up in your Application class and an application-level component is available, Hilt can provide dependencies to other Android classes that have the @AndroidEntryPoint annotation:

Hilt currently supports the following Android classes:

If you annotate an Android class with @AndroidEntryPoint, then you also must annotate Android classes that depend on it. For example, if you annotate a fragment, then you must also annotate any activities where you use that fragment.

@AndroidEntryPoint generates an individual Hilt component for each Android class in your project. These components can receive dependencies from their respective parent classes as described in Component hierarchy.

To obtain dependencies from a component, use the @Inject annotation to perform field injection:

Classes that Hilt injects can have other base classes that also use injection. Those classes don't need the @AndroidEntryPoint annotation if they're abstract.

To learn more about which lifecycle callback an Android class gets injected in, see Component lifetimes.

To perform field injection, Hilt needs to know how to provide instances of the necessary dependencies from the corresponding component. A binding contains the information necessary to provide instances of a type as a dependency.

One way to provide binding information to Hilt is constructor injection. Use the @Inject annotation on the constructor of a class to tell Hilt how to provide instances of that class:

The parameters of an annotated constructor of a class are the dependencies of that class. In the example, AnalyticsAdapter has AnalyticsService as a dependency. Therefore, Hilt must also know how to provide instances of AnalyticsService.

Sometimes a type cannot be constructor-injected. This can happen for multiple reasons. For example, you cannot constructor-inject an interface. You also cannot constructor-inject a type that you do not own, such as a class from an external library. In these cases, you can provide Hilt with binding information by using Hilt modules.

A Hilt module is a class that is annotated with @Module. Like a Dagger module, it informs Hilt how to provide instances of certain types. Unlike Dagger modules, you must annotate Hilt modules with @InstallIn to tell Hilt which Android class each module will be used or installed in.

Dependencies that you provide in Hilt modules are available in all generated components that are associated with the Android class where you install the Hilt module.

Consider the AnalyticsService example. If AnalyticsService is an interface, then you cannot constructor-inject it. Instead, provide Hilt with the binding information by creating an abstract function annotated with @Binds inside a Hilt module.

The @Binds annotation tells Hilt which implementation to use when it needs to provide an instance of an interface.

The annotated function provides the following information to Hilt:

The Hilt module AnalyticsModule is annotated with @InstallIn(ActivityComponent.class) because you want Hilt to inject that dependency into ExampleActivity. This annotation means that all of the dependencies in AnalyticsModule are available in all of the app's activities.

Interfaces are not the only case where you cannot constructor-inject a type. Constructor injection is also not possible if you don't own the class because it comes from an external library (classes like Retrofit, OkHttpClient, or Room databases), or if instances must be created with the builder pattern.

Consider the previous example. If you don't directly own the AnalyticsService class, you can tell Hilt how to provide instances of this type by creating a function inside a Hilt module and annotating that function with @Provides.

The annotated function supplies the following information to Hilt:

In cases where you need Hilt to provide different implementations of the same type as dependencies, you must provide Hilt with multiple bindings. You can define multiple bindings for the same type with qualifiers.

A qualifier is an annotation that you use to identify a specific binding for a type when that type has multiple bindings defined.

Consider the example. If you need to intercept calls to AnalyticsService, you could use an OkHttpClient object with an interceptor. For other services, you might need to intercept calls in a different way. In that case, you need to tell Hilt how to provide two different implementations of OkHttpClient.

First, define the qualifiers that you will use to annotate the @Binds or @Provides methods:

Then, Hilt needs to know how to provide an instance of the type that corresponds with each qualifier. In this case, you could use a Hilt module with @Provides. Both methods have the same return type, but the qualifiers label them as two different bindings:

You can inject the specific type that you need by annotating the field or parameter with the corresponding qualifier:

As a best practice, if you add a qualifier to a type, add qualifiers to all the possible ways to provide that dependency. Leaving the base or common implementation without a qualifier is error-prone and could result in Hilt injecting the wrong dependency.

Hilt provides some predefined qualifiers. For example, as you might need the Context class from either the application or the activity, Hilt provides the @ApplicationContext and @ActivityContext qualifiers.

Suppose that the AnalyticsAdapter class from the example needs the context of the activity. The following code demonstrates how to provide the activity context to AnalyticsAdapter:

For other predefined bindings available in Hilt, see Component default bindings.

For each Android class in which you can perform field injection, there's an associated Hilt component that you can refer to in the @InstallIn annotation. Each Hilt component is responsible for injecting its bindings into the corresponding Android class.

The previous examples demonstrated the use of ActivityComponent in Hilt modules.

Hilt provides the following components:

Hilt automatically creates and destroys instances of generated component classes following the lifecycle of the corresponding Android classes.

By default, all bindings in Hilt are unscoped. This means that each time your app requests the binding, Hilt creates a new instance of the needed type.

In the example, every time Hilt provides AnalyticsAdapter as a dependency to another type or through field injection (as in ExampleActivity), Hilt provides a new instance of AnalyticsAdapter.

However, Hilt also allows a binding to be scoped to a particular component. Hilt only creates a scoped binding once per instance of the component that the binding is scoped to, and all requests for that binding share the same instance.

The table below lists scope annotations for each generated component:

In the example, if you scope AnalyticsAdapter to the ActivityComponent using @ActivityScoped, Hilt provides the same instance of AnalyticsAdapter throughout the life of the corresponding activity:

Suppose that AnalyticsService has an internal state that requires the same instance to be used every time—not only in ExampleActivity, but anywhere in the app. In this case, it is appropriate to scope AnalyticsService to the SingletonComponent. The result is that whenever the component needs to provide an instance of AnalyticsService, it provides the same instance every time.

The following example demonstrates how to scope a binding to a component in a Hilt module. A binding's scope must match the scope of the component where it is installed, so in this example you must install AnalyticsService in SingletonComponent instead of ActivityComponent:

To learn more about Hilt component scopes, see Scoping in Android and Hilt.

Installing a module into a component allows its bindings to be accessed as a dependency of other bindings in that component or in any child component below it in the component hierarchy:

Each Hilt component comes with a set of default bindings that Hilt can inject as dependencies into your own custom bindings. Note that these bindings correspond to the general activity and fragment types and not to any specific subclass. This is because Hilt uses a single activity component definition to inject all activities. Each activity has a different instance of this component.

The application context binding is also available using @ApplicationContext. For example:

The activity context binding is also available using @ActivityContext. For example:

Hilt comes with support for the most common Android classes. However, you might need to perform field injection in classes that Hilt doesn't support.

In those cases, you can create an entry point using the @EntryPoint annotation. An entry point is the boundary between code that is managed by Hilt and code that is not. It is the point where code first enters into the graph of objects that Hilt manages. Entry points allow Hilt to use code that Hilt does not manage to provide dependencies within the dependency graph.

For example, Hilt doesn't directly support content providers. If you want a content provider to use Hilt to get some dependencies, you need to define an interface that is annotated with @EntryPoint for each binding type that you want and include qualifiers. Then add @InstallIn to specify the component in which to install the entry point as follows:

To access an entry point, use the appropriate static method from EntryPointAccessors. The parameter should be either the component instance or the @AndroidEntryPoint object that acts as the component holder. Make sure that the component you pass as a parameter and the EntryPointAccessors static method both match the Android class in the @InstallIn annotation on the @EntryPoint interface:

In this example, you must use the ApplicationContext to retrieve the entry point because the entry point is installed in SingletonComponent. If the binding that you wanted to retrieve were in the ActivityComponent, you would instead use the ActivityContext.

Hilt is built on top of the Dagger dependency injection library, providing a standard way to incorporate Dagger into an Android application.

With respect to Dagger, the goals of Hilt are as follows:

Because the Android operating system instantiates many of its own framework classes, using Dagger in an Android app requires you to write a substantial amount of boilerplate. Hilt reduces the boilerplate code that is involved in using Dagger in an Android application. Hilt automatically generates and provides the following:

Dagger and Hilt code can coexist in the same codebase. However, in most cases it is best to use Hilt to manage all of your usage of Dagger on Android. To migrate a project that uses Dagger to Hilt, see the migration guide and the Migrating your Dagger app to Hilt codelab.

To learn more about Hilt, see the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
plugins {
  ...
  id 'com.google.dagger.hilt.android' version '2.57.1' apply false
}
```

Example 2 (unknown):
```unknown
plugins {
  ...
  id("com.google.dagger.hilt.android") version "2.57.1" apply false
}
```

Example 3 (json):
```json
...
plugins {
  id 'com.google.devtools.ksp'
  id 'com.google.dagger.hilt.android'
}

android {
  ...
}

dependencies {
  implementation "com.google.dagger:hilt-android:2.57.1"
  ksp "com.google.dagger:hilt-compiler:2.57.1"
}
```

Example 4 (json):
```json
plugins {
  id("com.google.devtools.ksp")
  id("com.google.dagger.hilt.android")
}

android {
  ...
}

dependencies {
  implementation("com.google.dagger:hilt-android:2.57.1")
  ksp("com.google.dagger:hilt-android-compiler:2.57.1")
}
```

---

## Use Hilt with other Jetpack libraries Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/hilt-jetpack

**Contents:**
- Use Hilt with other Jetpack libraries Stay organized with collections Save and categorize content based on your preferences.
- Inject ViewModel objects with Hilt
  - Kotlin
  - Java
  - Kotlin
  - Java
  - @ViewModelScoped
- Integration with the Jetpack navigation library
  - Groovy
  - Kotlin

Hilt includes extensions for providing classes from other Jetpack libraries. Hilt currently supports the following Jetpack components:

You must add the Hilt dependencies to take advantage of these integrations. For more information about adding dependencies, see Dependency injection with Hilt.

Provide a ViewModel by annotating it with @HiltViewModel and using the @Inject annotation in the ViewModel object's constructor.

Then, an activity or a fragment that is annotated with @AndroidEntryPoint can get the ViewModel instance as normal using ViewModelProvider or the by viewModels() KTX extensions:

All Hilt ViewModels are provided by the ViewModelComponent which follows the same lifecycle as a ViewModel, and as such, can survive configuration changes. To scope a dependency to a ViewModel use the @ViewModelScoped annotation.

A @ViewModelScoped type will make it so that a single instance of the scoped type is provided across all dependencies injected into the ViewModel. Other instances of a ViewModel that request the scoped instance will receive a different instance.

If a single instance needs to be shared across various ViewModels, then it should be scoped using either @ActivityRetainedScoped or @Singleton.

Add the following additional dependencies to your Gradle file:

If your ViewModel is scoped to the navigation graph, use the hiltNavGraphViewModels function that works with fragments that are annotated with @AndroidEntryPoint.

To see how Hilt integrates with Jetpack Compose, see the Hilt section of Compose and other libraries.

Add the following additional dependencies to your Gradle file. Note that in addition to the library, you need to include an additional annotation processor that works on top of the Hilt annotation processor:

Inject a Worker using the @HiltWorker annotation in the class and @AssistedInject in the Worker object's constructor. You can use only @Singleton or unscoped bindings in Worker objects. You must also annotate the Context and WorkerParameters dependencies with @Assisted:

Then, have your Application class implement the Configuration.Provider interface, inject an instance of HiltWorkFactory, and pass it into the WorkManager configuration as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (php):
```php
@HiltViewModel
class ExampleViewModel @Inject constructor(
  private val savedStateHandle: SavedStateHandle,
  private val repository: ExampleRepository
) : ViewModel() {
  ...
}
```

Example 2 (java):
```java
@HiltViewModel
public class ExampleViewModel extends ViewModel {

  private final ExampleRepository repository;
  private final SavedStateHandle savedStateHandle;

  @Inject
  ExampleViewModel(
      SavedStateHandle savedStateHandle,
      ExampleRepository repository)
    {
    this.savedStateHandle = savedStateHandle;
    this.repository = repository;
  }
  ...
}
```

Example 3 (swift):
```swift
@AndroidEntryPoint
class ExampleActivity : AppCompatActivity() {
  private val exampleViewModel: ExampleViewModel by viewModels()
  ...
}
```

Example 4 (java):
```java
@AndroidEntryPoint
public class ExampleActivity extends AppCompatActivity {

  private ExampleViewModel exampleViewModel;

  @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    exampleViewModel = new ViewModelProvider(this).get(ExampleViewModel.class);
  }
  ...
}
```

---
