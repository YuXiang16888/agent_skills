# Hilt-Android - Hilt Testing

**Pages:** 1

---

## Hilt testing guide Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/dependency-injection/hilt-testing

**Contents:**
- Hilt testing guide Stay organized with collections Save and categorize content based on your preferences.
- Unit tests
  - Kotlin
  - Java
- End-to-end tests
  - Adding testing dependencies
  - Groovy
  - Kotlin
  - UI test setup
  - Kotlin

One of the benefits of using dependency injection frameworks like Hilt is that it makes testing your code easier.

Hilt isn't necessary for unit tests, since when testing a class that uses constructor injection, you don't need to use Hilt to instantiate that class. Instead, you can directly call a class constructor by passing in fake or mock dependencies, just as you would if the constructor weren't annotated:

For integration tests, Hilt injects dependencies as it would in your production code. Testing with Hilt requires no maintenance because Hilt automatically generates a new set of components for each test.

To use Hilt in your tests, include the hilt-android-testing dependency in your project:

You must annotate any UI test that uses Hilt with @HiltAndroidTest. This annotation is responsible for generating the Hilt components for each test.

Also, you need to add the HiltAndroidRule to the test class. It manages the components' state and is used to perform injection on your test:

Next, your test needs to know about the Application class that Hilt automatically generates for you.

You must execute instrumented tests that use Hilt in an Application object that supports Hilt. The library provides HiltTestApplication for use in tests. If your tests need a different base application, see Custom application for tests.

You must set your test application to run in your instrumented tests or Robolectric tests. The following instructions aren't specific to Hilt, but are general guidelines on how to specify a custom application to run in tests.

To use the Hilt test application in instrumented tests, you need to configure a new test runner. This makes Hilt work for all of the instrumented tests in your project. Perform the following steps:

Next, configure this test runner in your Gradle file as described in the instrumented unit test guide. Make sure you use the full classpath:

If you use Robolectric to test your UI layer, you can specify which application to use in the robolectric.properties file:

application = dagger.hilt.android.testing.HiltTestApplication

Alternatively, you can configure the application on each test individually by using Robolectric's @Config annotation:

If you use an Android Gradle Plugin version lower than 4.2, enable transforming @AndroidEntryPoint classes in local unit tests by applying the following configuration in your module's build.gradle file:

More information about enableTransformForLocalTests in the Hilt documentation.

Once Hilt is ready to use in your tests, you can use several features to customize the testing process.

To inject types into a test, use @Inject for field injection. To tell Hilt to populate the @Inject fields, call hiltRule.inject().

See the following example of an instrumented test:

If you need to inject a fake or mock instance of a dependency, you need to tell Hilt not to use the binding that it used in production code and to use a different one instead. To replace a binding, you need to replace the module that contains the binding with a test module that contains the bindings that you want to use in the test.

For example, suppose your production code declares a binding for AnalyticsService as follows:

To replace the AnalyticsService binding in tests, create a new Hilt module in the test or androidTest folder with the fake dependency and annotate it with @TestInstallIn. All the tests in that folder are injected with the fake dependency instead.

To replace a binding in a single test instead of all tests, uninstall a Hilt module from a test using the @UninstallModules annotation and create a new test module inside the test.

Following the AnalyticsService example from the previous version, begin by telling Hilt to ignore the production module by using the @UninstallModules annotation in the test class:

Next, you must replace the binding. Create a new module within the test class that defines the test binding:

This only replaces the binding for a single test class. If you want to replace the binding for all test classes, use the @TestInstallIn annotation from the section above. Alternatively, you can put the test binding in the test module for Robolectric tests, or in the androidTest module for instrumented tests. The recommendation is to use @TestInstallIn whenever possible.

Use the @BindValue annotation to easily bind fields in your test into the Hilt dependency graph. Annotate a field with @BindValue and it will be bound under the declared field type with any qualifiers that are present for that field.

In the AnalyticsService example, you can replace AnalyticsService with a fake by using @BindValue:

This simplifies both replacing a binding and referencing a binding in your test by allowing you to do both at the same time.

@BindValue works with qualifiers and other testing annotations. For example, if you use testing libraries such as Mockito, you could use it in a Robolectric test as follows:

If you need to add a multibinding, you can use the @BindValueIntoSet and @BindValueIntoMap annotations in place of @BindValue. @BindValueIntoMap requires you to also annotate the field with a map key annotation.

Hilt also provides features to support nonstandard use cases.

If you cannot use HiltTestApplication because your test application needs to extend another application, annotate a new class or interface with @CustomTestApplication, passing in the value of the base class you want the generated Hilt application to extend.

@CustomTestApplication will generate an Application class ready for testing with Hilt that extends the application you passed as a parameter.

In the example, Hilt generates an Application named HiltTestApplication_Application that extends the BaseApplication class. In general, the name of the generated application is the name of the annotated class appended with _Application. You must set the generated Hilt test application to run in your instrumented tests or Robolectric tests as described in Test application.

If you have other TestRule objects in your test, there are multiple ways to ensure that all of the rules work together.

You can wrap the rules together as follows:

Alternatively, you can use both rules at the same level as long as the HiltAndroidRule executes first. Specify the execution order using the order attribute in the @Rule annotation. This only works in JUnit version 4.13 or higher:

It is not possible to use launchFragmentInContainer from the androidx.fragment:fragment-testing library with Hilt, because it relies on an activity that is not annotated with @AndroidEntryPoint.

Use the launchFragmentInHiltContainer code from the architecture-samples GitHub repository instead.

The @EarlyEntryPoint annotation provides an escape hatch when a Hilt entry point needs to be created before the singleton component is available in a Hilt test.

More information about @EarlyEntryPoint in the Hilt documentation.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (php):
```php
@ActivityScoped
class AnalyticsAdapter @Inject constructor(
  private val service: AnalyticsService
) { ... }

class AnalyticsAdapterTest {

  @Test
  fun `Happy path`() {
    // You don't need Hilt to create an instance of AnalyticsAdapter.
    // You can pass a fake or mock AnalyticsService.
    val adapter = AnalyticsAdapter(fakeAnalyticsService)
    assertEquals(...)
  }
}
```

Example 2 (java):
```java
@ActivityScope
public class AnalyticsAdapter {

  private final AnalyticsService analyticsService;

  @Inject
  AnalyticsAdapter(AnalyticsService analyticsService) {
    this.analyticsService = analyticsService;
  }
}

public final class AnalyticsAdapterTest {

  @Test
  public void happyPath() {
    // You don't need Hilt to create an instance of AnalyticsAdapter.
    // You can pass a fake or mock AnalyticsService.
    AnalyticsAdapter adapter = new AnalyticsAdapter(fakeAnalyticsService);
    assertEquals(...);
  }
}
```

Example 3 (json):
```json
dependencies {
    // For Robolectric tests.
    testImplementation 'com.google.dagger:hilt-android-testing:2.57.1'
    // ...with Kotlin.
    kaptTest 'com.google.dagger:hilt-android-compiler:2.57.1'
    // ...with Java.
    testAnnotationProcessor 'com.google.dagger:hilt-android-compiler:2.57.1'


    // For instrumented tests.
    androidTestImplementation 'com.google.dagger:hilt-android-testing:2.57.1'
    // ...with Kotlin.
    kaptAndroidTest 'com.google.dagger:hilt-android-compiler:2.57.1'
    // ...with Java.
    androidTestAnnotationProcessor 'com.google.dagger:hilt-android-compiler:2.57.1'
}
```

Example 4 (json):
```json
dependencies {
    // For Robolectric tests.
    testImplementation("com.google.dagger:hilt-android-testing:2.57.1")
    // ...with Kotlin.
    kaptTest("com.google.dagger:hilt-android-compiler:2.57.1")
    // ...with Java.
    testAnnotationProcessor("com.google.dagger:hilt-android-compiler:2.57.1")


    // For instrumented tests.
    androidTestImplementation("com.google.dagger:hilt-android-testing:2.57.1")
    // ...with Kotlin.
    kaptAndroidTest("com.google.dagger:hilt-android-compiler:2.57.1")
    // ...with Java.
    androidTestAnnotationProcessor("com.google.dagger:hilt-android-compiler:2.57.1")
}
```

---
