# Hilt-Android - Hilt Modules

**Pages:** 2

---

## Using Dagger in multi-module apps Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/dagger-multi-module

**Contents:**
- Using Dagger in multi-module apps Stay organized with collections Save and categorize content based on your preferences.
- Implementation with Dagger subcomponents
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java

A project with multiple Gradle modules is known as a multi-module project. In a multi-module project that ships as a single APK with no feature modules, it's common to have an app module that can depend on most modules of your project and a base or core module that the rest of the modules usually depend on. The app module typically contains your Application class, whereas the base module contains all common classes shared across all modules in your project.

The app module is a good place to declare your application component (for example, ApplicationComponent in the image below) that can provide objects that other components might need as well as the singletons of your app. As an example, classes like OkHttpClient, JSON parsers, accessors for your database, or SharedPreferences objects that may be defined in the core module, will be provided by the ApplicationComponent defined in the app module.

In the app module, you could also have other components with shorter lifespans. An example could be a UserComponent with user-specific configuration (like a UserSession) after a log in.

In the different modules of your project, you can define at least one subcomponent that has logic specific to that module as seen in figure 1.

Figure 1. Example of a Dagger graph in a multi-module project

For example, in a login module, you could have a LoginComponent scoped with a custom @ModuleScope annotation that can provide objects common to that feature such as a LoginRepository. Inside that module, you can also have other components that depend on a LoginComponent with a different custom scope, for example @FeatureScope for a LoginActivityComponent or a TermsAndConditionsComponent where you can scope more feature-specific logic such as ViewModel objects.

For other modules such as Registration, you would have a similar setup.

A general rule for a multi-module project is that modules of the same level shouldn't depend on each other. If they do, consider whether that shared logic (the dependencies between them) should be part of the parent module. If so, refactor to move the classes to the parent module; if not, create a new module that extends the parent module and have both of the original modules extend the new module.

As a best practice, you would generally create a component in a module in the following cases:

You need to perform field injection, as with LoginActivityComponent.

You need to scope objects, as with LoginComponent.

If neither of these casses apply and you need to tell Dagger how to provide objects from that module, create and expose a Dagger module with @Provides or @Binds methods if construction injection is not possible for those classes.

The Using Dagger in Android apps doc page covers how to create and use subcomponents. However, you cannot use the same code because feature modules don't know about the app module. As an example, if you think about a typical Login flow and the code we have in the previous page, it doesn't compile any more:

The reason is that the login module doesn't know about MyApplication nor appComponent. To make it work, you need to define an interface in the feature module that provides a FeatureComponent that MyApplication needs to implement.

In the following example, you can define a LoginComponentProvider interface that provides a LoginComponent in the login module for the Login flow:

Now, the LoginActivity will use that interface instead of the snippet of code defined above:

Now, MyApplication needs to implement that interface and implement the required methods:

This is how you can use Dagger subcomponents in a multi-module project. With feature modules, the solution is different due to the way modules depend on each other.

With feature modules, the way modules usually depend on each other is inverted. Instead of the app module including feature modules, the feature modules depend on the app module. See figure 2 for a representation of how modules are structured.

Figure 2. Example of a Dagger graph in a project with feature modules

In Dagger, components need to know about their subcomponents. This information is included in a Dagger module added to the parent component (like the SubcomponentsModule module in Using Dagger in Android apps).

Unfortunately, with the reversed dependency between the app and the feature module, the subcomponent is not visible from the app module because it's not in the build path. As an example, a LoginComponent defined in a login feature module cannot be a subcomponent of the ApplicationComponent defined in the app module.

Dagger has a mechanism called component dependencies that you can use to solve this issue. Instead of the child component being a subcomponent of the parent component, the child component is dependent on the parent component. With that, there is no parent-child relationship; now components depend on others to get certain dependencies. Components need to expose types from the graph for dependent components to consume them.

For example: a feature module called login wants to build a LoginComponent that depends on the AppComponent available in the app Gradle module.

Below are definitions for the classes and the AppComponent that are part of the app Gradle module:

In your login gradle module that includes the app gradle module, you have a LoginActivity that needs a LoginViewModel instance to be injected:

LoginViewModel has a dependency on UserRepository that is available and scoped to AppComponent. Let's create a LoginComponent that depends on AppComponent to inject LoginActivity:

LoginComponent specifies a dependency on AppComponent by adding it to the dependencies parameter of the component annotation. Because LoginActivity will be injected by Dagger, add the inject() method to the interface.

When creating a LoginComponent, an instance of AppComponent needs to be passed in. Use the component factory to do it:

Now, LoginActivity can create an instance of LoginComponent and call the inject() method.

LoginViewModel depends on UserRepository; and for LoginComponent to be able to access it from AppComponent, AppComponent needs to expose it in its interface:

The scoping rules with dependent components work in the same way as with subcomponents. Because LoginComponent uses an instance of AppComponent, they cannot use the same scope annotation.

If you wanted to scope LoginViewModel to LoginComponent, you would do it as you did previously using the custom @ActivityScope annotation.

The ApplicationComponent should always be in the app module.

Create Dagger components in modules if you need to perform field injection in that module or you need to scope objects for a specific flow of your application.

For Gradle modules that are meant to be utilities or helpers and don't need to build a graph (that's why you'd need a Dagger component), create and expose public Dagger modules with @Provides and @Binds methods of those classes that don't support constructor injection.

To use Dagger in an Android app with feature modules, use component dependencies to be able to access dependencies provided by the ApplicationComponent defined in the app module.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (swift):
```swift
class LoginActivity: Activity() {
  ...

  override fun onCreate(savedInstanceState: Bundle?) {
    // Creation of the login graph using the application graph
    loginComponent = (applicationContext as MyDaggerApplication)
                        .appComponent.loginComponent().create()

    // Make Dagger instantiate @Inject fields in LoginActivity
    loginComponent.inject(this)
    ...
  }
}
```

Example 2 (java):
```java
public class LoginActivity extends Activity {
    ...

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        // Creation of the login graph using the application graph
        loginComponent = ((MyApplication) getApplicationContext())
                                .appComponent.loginComponent().create();

        // Make Dagger instantiate @Inject fields in LoginActivity
        loginComponent.inject(this);

        ...
    }
}
```

Example 3 (typescript):
```typescript
interface LoginComponentProvider {
    fun provideLoginComponent(): LoginComponent
}
```

Example 4 (typescript):
```typescript
public interface LoginComponentProvider {
   public LoginComponent provideLoginComponent();
}
```

---

## Hilt in multi-module apps Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/hilt-multi-module

**Contents:**
- Hilt in multi-module apps Stay organized with collections Save and categorize content based on your preferences.
- Hilt in feature modules
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java

Hilt code generation needs access to all the Gradle modules that use Hilt. The Gradle module that compiles your Application class needs to have all Hilt modules and constructor-injected classes in its transitive dependencies.

If your multi-module project is composed of regular Gradle modules, then you can use Hilt as described in Dependency injection with Hilt. However, this is not the case with apps that include feature modules.

In feature modules, the way that modules usually depend on each other is inverted. Therefore, Hilt cannot process annotations in feature modules. You must use Dagger to perform dependency injection in your feature modules.

You must use component dependencies to solve this problem with feature modules. Follow these steps:

Consider the example from the Dependency injection with Hilt page. Suppose you add a login feature module to your project. You implement the login feature with an activity called LoginActivity. This means that you can get bindings only from the application component.

For this feature, you need an OkHttpClient with the authInterceptor binding.

First, create an @EntryPoint interface installed in the SingletonComponent with the bindings that the login module needs:

To perform field injection in the LoginActivity, create a Dagger component that depends on the @EntryPoint interface:

Once those steps are complete, use Dagger as usual in your feature module. For example, you can use the bindings from the SingletonComponent as a dependency of a class:

To perform field injection, create an instance of the Dagger component using the applicationContext to get the SingletonComponent dependencies:

For more context on module dependencies in feature modules, see Component dependencies with feature modules.

For more information about Dagger on Android, see Using Dagger in Android apps.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (typescript):
```typescript
// LoginModuleDependencies.kt - File in the app module.

@EntryPoint
@InstallIn(SingletonComponent::class)
interface LoginModuleDependencies {

  @AuthInterceptorOkHttpClient
  fun okHttpClient(): OkHttpClient
}
```

Example 2 (typescript):
```typescript
// LoginModuleDependencies.java - File in the app module.

@EntryPoint
@InstallIn(SingletonComponent.class)
public interface LoginModuleDependencies {

  @AuthInterceptorOkHttpClient
  OkHttpClient okHttpClient();
}
```

Example 3 (typescript):
```typescript
// LoginComponent.kt - File in the login module.

@Component(dependencies = [LoginModuleDependencies::class])
interface LoginComponent {

  fun inject(activity: LoginActivity)

  @Component.Builder
  interface Builder {
    fun context(@BindsInstance context: Context): Builder
    fun appDependencies(loginModuleDependencies: LoginModuleDependencies): Builder
    fun build(): LoginComponent
  }
}
```

Example 4 (typescript):
```typescript
// LoginComponent.java - File in the login module.

@Component(dependencies = LoginModuleDependencies.class)
public interface LoginComponent {

  void inject(LoginActivity loginActivity);

  @Component.Builder
  interface Builder {
    Builder context(@BindsInstance Context context);
    Builder appDependencies(LoginModuleDependencies loginModuleDependencies);
    LoginComponent build();
  }
}
```

---
