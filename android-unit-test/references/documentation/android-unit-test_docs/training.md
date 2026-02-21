# Android-Unit-Test_Docs - Training

**Pages:** 12

---

## Espresso cheat sheet Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/cheat-sheet

**Contents:**
- Espresso cheat sheet Stay organized with collections Save and categorize content based on your preferences.

The Espresso Cheat Sheet is a quick reference you can use during development. This cheat sheet contains most available instances of Matcher, ViewAction, and ViewAssertion.

An offline version is also available in PDF format: espresso-cheat-sheet-2.1.0.pdf.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

---

## Additional Resources for Espresso Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/additional-resources

**Contents:**
- Additional Resources for Espresso Stay organized with collections Save and categorize content based on your preferences.
- Samples

This page contains resources for learning more about using Espresso in Android tests.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

---

## Espresso recipes Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/recipes

**Contents:**
- Espresso recipes Stay organized with collections Save and categorize content based on your preferences.
- Match a view next to another view
  - Kotlin
  - Java
- Match a view that is inside an action bar
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin

This document describes how to set up a variety of common Espresso tests.

A layout could contain certain views that are not unique by themselves. For example, a repeating call button in a table of contacts could have the same R.id, contain the same text, and have the same properties as other call buttons within the view hierarchy.

For example, in this activity, the view with text "7" repeats across multiple rows:

Often, the non-unique view will be paired with some unique label that’s located next to it, such as a name of the contact next to the call button. In this case, you can use the hasSibling() matcher to narrow down your selection:

The ActionBarTestActivity has two different action bars: a normal action bar and a contextual action bar that is created from a options menu. Both action bars have one item that is always visible and two items that are only visible in overflow menu. When an item is clicked, it changes a TextView to the content of the clicked item.

Matching visible icons on both of the action bars is straightforward, as shown in the following code snippet:

The code looks identical for the contextual action bar:

Clicking on items in the overflow menu is a bit trickier for the normal action bar as some devices have a hardware overflow menu button, which opens the overflowing items in an options menu, and some devices have a software overflow menu button, which opens a normal overflow menu. Luckily, Espresso handles that for us.

For the normal action bar:

This is how this looks on devices with a hardware overflow menu button:

For the contextual action bar it is really easy again:

To see the full code for these samples, view the ActionBarTest.java sample on GitHub.

After performing a series of actions, you will certainly want to assert the state of the UI under test. Sometimes, this may be a negative case, such as when something is not happening. Keep in mind that you can turn any hamcrest view matcher into a ViewAssertion by using ViewAssertions.matches().

In the example below, we take the isDisplayed() matcher and reverse it using the standard not() matcher:

The above approach works if the view is still part of the hierarchy. If it is not, you will get a NoMatchingViewException and you need to use ViewAssertions.doesNotExist().

If the view is gone from the view hierarchy—which can happen when an action caused a transition to another activity—you should use ViewAssertions.doesNotExist():

To prove a particular data item is not within an AdapterView you have to do things a little differently. We have to find the AdapterView we’re interested in and interrogate the data its holding. We don’t need to use onData(). Instead, we use onView() to find the AdapterView and then use another matcher to work on the data inside the view.

Then the all we need is onView() to find the AdapterView:

And we have an assertion that will fail if an item that is equal to "item: 168" exists in an adapter view with the ID list.

For the full sample, look at the testDataItemNotInAdapter() method within the AdapterViewTest.java class on GitHub.

Replacing the default FailureHandler in Espresso with a custom one allows for additional or different error handling, such as taking a screenshot or passing along extra debug information.

The CustomFailureHandlerTest example demonstrates how to implement a custom failure handler:

This failure handler throws a MySpecialException instead of a NoMatchingViewException and delegates all other failures to the DefaultFailureHandler. The CustomFailureHandler can be registered with Espresso in the setUp() method of the test:

For more information, see the FailureHandler interface and Espresso.setFailureHandler().

Android supports multiple windows. Normally, this is transparent to the users and the app developer, yet in certain cases multiple windows are visible, such as when an auto-complete window gets drawn over the main application window in the search widget. To simplify things, by default Espresso uses a heuristic to guess which Window you intend to interact with. This heuristic is almost always good enough; however, in rare cases, you’ll need to specify which window an interaction should target. You can do this by providing your own root window matcher, or Root matcher:

As is the case with ViewMatchers, we provide a set of pre-provided RootMatchers. Of course, you can always implement your own Matcher object.

Take a look at the MultipleWindowTest sample on GitHub.

Headers and footers are added to ListViews using the addHeaderView() and addFooterView() methods. To ensure Espresso.onData() knows what data object to match, make sure to pass a preset data object value as the second parameter to addHeaderView() and addFooterView(). For example:

Then, you can write a matcher for the footer:

And loading the view in a test is trivial:

Take a look at the full code sample, found in the testClickFooter() method of AdapterViewTest.java on GitHub.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (json):
```json
onView(allOf(withText("7"), hasSibling(withText("item: 0"))))
    .perform(click())
```

Example 2 (json):
```json
onView(allOf(withText("7"), hasSibling(withText("item: 0"))))
    .perform(click());
```

Example 3 (unknown):
```unknown
fun testClickActionBarItem() {
    // We make sure the contextual action bar is hidden.
    onView(withId(R.id.hide_contextual_action_bar))
        .perform(click())

    // Click on the icon - we can find it by the r.Id.
    onView(withId(R.id.action_save))
        .perform(click())

    // Verify that we have really clicked on the icon
    // by checking the TextView content.
    onView(withId(R.id.text_action_bar_result))
        .check(matches(withText("Save")))
}
```

Example 4 (java):
```java
public void testClickActionBarItem() {
    // We make sure the contextual action bar is hidden.
    onView(withId(R.id.hide_contextual_action_bar))
        .perform(click());

    // Click on the icon - we can find it by the r.Id.
    onView(withId(R.id.action_save))
        .perform(click());

    // Verify that we have really clicked on the icon
    // by checking the TextView content.
    onView(withId(R.id.text_action_bar_result))
        .check(matches(withText("Save")));
}
```

---

## Espresso Web Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/web

**Contents:**
- Espresso Web Stay organized with collections Save and categorize content based on your preferences.
- When to use Espresso-Web
- How it works
  - What is a WebDriver Atom?
  - Kotlin
  - Java
- Implement WebView
  - Packages
  - Groovy
  - Kotlin

Espresso-Web is an entry point to work with Android WebView UI components. Espresso-Web reuses Atoms from the popular WebDriver API to examine and control the behavior of a WebView.

Use Espresso-Web to test your hybrid apps, especially the integration of your app’s native UI components with its WebView UI components. You can use the Espresso-Web API in conjunction with other Espresso APIs to fully interact with web elements inside WebView objects.

If you need to test only the WebView itself, and not the interactions between the WebView and native components in your app, consider writing a general web test using a framework like WebDriver. If you use a web testing framework, you don’t need to use an Android device or a Java Virtual Machine, which makes your tests run more quickly and reliably. That being said, Espresso-Web allows you to reuse your custom WebDriver atoms, which gives you a lot of flexibility, especially when writing tests that you plan to run against both standalone web apps and apps that include an Android UI.

Similarly to Espresso’s onData() method, a WebView interaction comprises several Atoms. WebView interactions use a combination of the Java programming language and a JavaScript bridge to do their work. Because there is no chance of introducing race conditions by exposing data from the JavaScript environment—everything Espresso sees on the Java-based side is an isolated copy—returning data from Web.WebInteraction objects is fully supported, allowing you to verify all data that’s returned from a request.

The WebDriver framework uses Atoms to find and manipulate web elements programmatically. Atoms are used by WebDriver to allow browser manipulation. An Atom is conceptually similar to a ViewAction, a self-contained unit that performs an action in your UI. You expose Atoms using a list of defined methods, such as findElement() and getElement(), to drive the browser from the user’s point of view. However, if you use the WebDriver framework directly, Atoms need to be properly orchestrated, requiring logic that is quite verbose.

Within Espresso, the classes Web and Web.WebInteraction wrap this boilerplate and give an Espresso-like feel to interacting with WebView objects. So in a context of a WebView, Atoms are used as a substitution to traditional Espresso ViewMatchers and ViewActions.

The API then looks quite simple:

To learn more, read Selenium’s documentation on Atoms.

Follow the guidance shown in the following sections to work with WebView in your app's tests.

To include Espresso-Web in your project, complete the following steps:

Add the following line inside dependencies:

Espresso-Web is only compatible with Espresso 2.2 or higher and version 0.3 or higher of the testing library, so make sure you update those lines as well:

The onWebView() method is the main entry point when working with WebView on Android using Espresso. You use this method to perform Espresso-Web tests, such as the following:

In this example, Espresso-Web locates a DOM element whose ID is "link_2" and clicks on it. The tool then verifies that the WebView sends a GET request containing the "navigation_2.html" string.

When executing your tests, the system performs all WebView interactions using JavaScript. Therefore, to support JavaScript evaluation, the WebView under test must have JavaScript enabled.

You can force JavaScript to be enabled by calling forceJavascriptEnabled() as an action in your activity under test, as shown in the following code snippet.

Common interactions with Web.WebInteraction objects include the following:

withElement() references a DOM element within the WebView.

withContextualElement() references a scoped DOM element within the WebView, relative to another DOM element. You should call withElement() first to establish the reference Web.WebInteraction object (DOM element).

check() evaluates a condition, making sure that it resolves to true.

perform() executes an action within a WebView, such as clicking on an element.

reset() reverts the WebView to its initial state. This is necessary when a prior action, such as a click, introduces a navigation change that makes ElementReference and WindowReference objects inaccessible.

Note: Although using reset() is useful when making assertions against multi-page workflows, such as form submissions, your tests should usually be limited in scope and focus on a single page.

The following example tests whether, after entering text into a WebView and selecting a Submit button, the same text appears within a different element in the same WebView:

For more information about using Espresso-Web in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-08 UTC.

**Examples:**

Example 1 (unknown):
```unknown
onWebView()
    .withElement(Atom)
    .perform(Atom)
    .check(WebAssertion)
```

Example 2 (unknown):
```unknown
onWebView()
    .withElement(Atom)
    .perform(Atom)
    .check(WebAssertion);
```

Example 3 (json):
```json
androidTestImplementation 'androidx.test.espresso:espresso-web:3.6.1'
```

Example 4 (json):
```json
androidTestImplementation('androidx.test.espresso:espresso-web:3.6.1')
```

---

## Accessibility checking Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/accessibility-checking

**Contents:**
- Accessibility checking Stay organized with collections Save and categorize content based on your preferences.
- Enable checks
  - Kotlin
  - Java
  - Kotlin
  - Java
- Suppress subsets of results
  - Kotlin
  - Java

Testing for accessibility lets you experience your app from the perspective of your entire user base, including users with accessibility needs. This form of testing can reveal opportunities to make your app more powerful and versatile.

This page describes how to add accessibility checks to your existing Espresso tests. For more information about accessibility, see the Accessibility guides.

You can enable and configure accessibility testing using the AccessibilityChecks class:

By default, the checks run when you perform any view action defined in ViewActions. Each check includes the view on which the action is performed as well as all descendant views. You can evaluate the entire view hierarchy of a screen during each check by passing true into setRunChecksFromRootView(), as shown in the following code snippet:

After Espresso runs accessibility checks on your app, you might find several opportunities to improve your app's accessibility that you cannot address immediately. In order to stop Espresso tests from continually failing because of these results, you can ignore them temporarily. The Accessibility Test Framework (ATF) provides this functionality using the setSuppressingResultMatcher() method, which instructs Espresso to suppress all results that satisfy the given matcher expression.

When you make changes to your app that address one aspect of accessibility, it's beneficial for Espresso to show results for as many other aspects of accessibility as possible. For this reason, it's best to suppress only specific known opportunities for improvement.

When you temporarily suppress accessibility test findings that you plan to address later, it's important to not accidentally suppress similar findings. For this reason, use matchers that are narrowly scoped. To do so, choose a matcher so that Espresso suppresses a given result only if it satisfies each of the following accessibility checks:

The ATF defines several matchers to help you define which results to show in your Espresso tests. The following example suppresses the results of checks that relate to a single TextView element's color contrast. The element's ID is countTV.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (php):
```php
import androidx.test.espresso.accessibility.AccessibilityChecks

@RunWith(AndroidJUnit4::class)
@LargeTest
class MyWelcomeWorkflowIntegrationTest {
    init {
        AccessibilityChecks.enable()
    }
}
```

Example 2 (java):
```java
import androidx.test.espresso.accessibility.AccessibilityChecks;

@RunWith(AndroidJUnit4.class)
@LargeTest
public class MyWelcomeWorkflowIntegrationTest {
    @BeforeClass
    public void enableAccessibilityChecks() {
        AccessibilityChecks.enable();
    }
}
```

Example 3 (unknown):
```unknown
AccessibilityChecks.enable().setRunChecksFromRootView(true)
```

Example 4 (unknown):
```unknown
AccessibilityChecks.enable().setRunChecksFromRootView(true);
```

---

## Espresso setup instructions Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/setup

**Contents:**
- Espresso setup instructions Stay organized with collections Save and categorize content based on your preferences.
- Set up your test environment
- Add Espresso dependencies
  - Groovy
  - Kotlin
- Set the instrumentation runner
  - Groovy
  - Kotlin
- Example Gradle build file
  - Groovy

This guide covers installing Espresso using the SDK Manager and building it using Gradle. Android Studio is recommended.

To avoid flakiness, we highly recommend that you turn off system animations on the virtual or physical devices used for testing. On your device, under Settings > Developer options, disable the following 3 settings:

To add Espresso dependencies to your project, complete the following steps:

View the complete set of Gradle dependencies.

Add to the same build.gradle file the following line in android.defaultConfig:

In order to make sure we are on the right track with each new release, the test runner collects analytics. More specifically, it uploads a hash of the package name of the application under test for each invocation. This allows us to measure both the count of unique packages using Espresso as well as the volume of usage.

If you do not wish to upload this data, you can opt out by including the disableAnalytics argument in your instrumentation command:

Android Studio creates tests by default in src/androidTest/java/com.example.package/.

Example JUnit4 test using Rules:

You can run your tests in Android Studio or from the command line.

To create a test configuration in Android Studio, complete the following steps:

Execute the following Gradle command:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
androidTestImplementation 'androidx.test.espresso:espresso-core:3.6.1'
androidTestImplementation 'androidx.test:runner:1.6.1'
androidTestImplementation 'androidx.test:rules:1.6.1'
```

Example 2 (json):
```json
androidTestImplementation('androidx.test.espresso:espresso-core:3.6.1')
androidTestImplementation('androidx.test:runner:1.6.1')
androidTestImplementation('androidx.test:rules:1.6.1')
```

Example 3 (unknown):
```unknown
testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
```

Example 4 (unknown):
```unknown
testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
```

---

## Espresso idling resources Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/idling-resource

**Contents:**
- Espresso idling resources Stay organized with collections Save and categorize content based on your preferences.
- Identify when idling resources are needed
- Common use cases
- Example idling resource implementations
- Create your own idling resource
  - Kotlin
  - Java
- Register idling resources
- Integrate idling resources into your app
  - Recommended approach

An idling resource represents an asynchronous operation whose results affect subsequent operations in a UI test. By registering idling resources with Espresso, you can validate these asynchronous operations more reliably when testing your app.

Espresso provides a sophisticated set of synchronization capabilities. This characteristic of the framework, however, applies only to operations that post messages on the MessageQueue, such as a subclass of View that's drawing its contents on the screen.

Because Espresso isn't aware of any other asynchronous operations, including those running on a background thread, Espresso can't provide its synchronization guarantees in those situations. In order to make Espresso aware of your app's long-running operations, you must register each one as an idling resource.

If you don't use idling resources when testing the results of your app's asynchronous work, you might find yourself having to use one of the following bad workarounds to improve your tests' reliability:

Espresso allows you to remove these unreliable workarounds from your tests and instead register your app's asynchronous work as idling resources.

When performing operations similar to the following examples in your tests, consider using an idling resource:

It's especially important to register idling resources when these operations update a UI that your tests then validate.

The following list describes several example implementations of idling resources that you can integrate into your app:

As you use idling resources in your app's tests, you might need to provide custom resource management or logging. In those cases, the implementations listed in the previous section might not suffice. If that's the case, you can extend one of these idling resource implementations or create your own.

If you implement your own idling resource functionality, keep the following best practices in mind, particularly the first one:

The following code snippet illustrates this recommendation:

The synchronization benefits associated with idling resources only take effect following Espresso's first invocation of that resource's isIdleNow() method.

The following list shows several examples of this property:

To conserve system resources, you should unregister idling resources as soon as you don't need them anymore. For example, if you register an idling resource in a method annotated with @Before, it's best to unregister this resource in a corresponding method that's annotated with @After.

By using this container for your app's idling resources, you can register and unregister idling resources repeatedly as needed and still observe consistent behavior.

For example, the idling resources that you implement and register shouldn't contain references to View objects.

Espresso provides a container class into which you can place your app's idling resources. This class, called IdlingRegistry, is a self-contained artifact that introduces minimal overhead to your app. The class also allows you to take the following steps toward improving your app's maintainability:

Although you can add idling resources to an app in several different ways, one approach in particular maintains encapsulation for your app while still allowing you to specify a particular operation that a given idling resource represents.

When adding idling resources into your app, we highly recommend placing the idling resource logic in the app itself and performing only the registration and unregistration operations in your tests.

Although you create the unusual situation of using a test-only interface in production code by following this approach, you can wrap idling resources around code that you already have, maintaining your app's APK size and method count.

If you'd prefer to not have idling resources logic in your app's production code, there are several other viable integration strategies:

Implement an idling resource in your app's tests, and expose the part of your app's implementation that needs to be synchronized in those tests.

Caution: Although this design decision seems to create a self-contained reference to idling resources, it also breaks encapsulation in all but the simplest of apps.

For more information about using Espresso in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (unknown):
```unknown
fun isIdle() {
    // DON'T call callback.onTransitionToIdle() here!
}

fun backgroundWorkDone() {
    // Background work finished.
    callback.onTransitionToIdle() // Good. Tells Espresso that the app is idle.

    // Don't do any post-processing work beyond this point. Espresso now
    // considers your app to be idle and moves on to the next test action.
}
```

Example 2 (java):
```java
public void isIdle() {
    // DON'T call callback.onTransitionToIdle() here!
}

public void backgroundWorkDone() {
    // Background work finished.
    callback.onTransitionToIdle() // Good. Tells Espresso that the app is idle.

    // Don't do any post-processing work beyond this point. Espresso now
    // considers your app to be idle and moves on to the next test action.
}
```

---

## Espresso lists Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/lists

**Contents:**
- Espresso lists Stay organized with collections Save and categorize content based on your preferences.
- Interact with adapter view list items
  - Match data using a custom view matcher
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin

Espresso offers mechanisms to scroll to or act on a particular item for two types of lists: adapter views and recycler views.

When dealing with lists, especially those created with a RecyclerView or an AdapterView object, the view that you’re interested in might not even be on the screen because only a small number of children are displayed and are recycled as you scroll. The scrollTo() method can’t be used in this case because it requires an existing view.

Instead of using the onView() method, start your search with onData() and provide a matcher against the data that is backing the view you’d like to match. Espresso will do all the work of finding the row in the Adapter object and making the item visible in the viewport.

The activity below contains a ListView, which is backed by a SimpleAdapter that holds data for each row in a Map<String, Object> object.

Each map has two entries: a key "STR" that contains a String, such as "item: x", and a key "LEN" that contains an Integer, which represents the length of the content. For example:

The code for a click on the row with "item: 50" looks like this:

Note that Espresso scrolls through the list automatically as needed.

Let’s take apart the Matcher<Object> inside onData(). The is(instanceOf(Map.class)) method narrows the search to any item of the AdapterView, which is backed by a Map object.

In our case, this aspect of the query matches every row of the list view, but we want to click specifically on an item, so we narrow the search further with:

This Matcher<String, Object> will match any Map that contains an entry with the key "STR" and the value "item: 50". Because the code to look up this is long and we want to reuse it in other locations, let’s write a custom withItemContent matcher for that:

You use a BoundedMatcher as a base because to only match objects of type Map. Override the matchesSafely() method, putting in the matcher found earlier, and match it against a Matcher<String> that you can pass as an argument. This allows you to call withItemContent(equalTo("foo")). For code brevity, you can create another matcher that already calls the equalTo() and accepts a String object:

Now the code to click on the item is simple:

For the full code of this test, take a look at the testClickOnItem50() method within the AdapterViewTest class and this custom LongListMatchers matcher on GitHub.

The sample above issues a click in the middle of the entire row of a ListView. But what if we want to operate on a specific child of the row? For example, we would like to click on the second column of the row of the LongListActivity, which displays the String.length of the content in the first column:

Just add an onChildView() specification to your implementation of DataInteraction:

RecyclerView objects work differently than AdapterView objects, so onData() cannot be used to interact with them.

To interact with RecyclerViews using Espresso, you can use the espresso-contrib package, which has a collection of RecyclerViewActions that can be used to scroll to positions or to perform actions on items:

The following snippets feature some examples from the RecyclerViewSample sample:

For more information about using Espresso lists in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (json):
```json
{"STR" : "item: 0", "LEN": 7}
```

Example 2 (julia):
```julia
onData(allOf(`is`(instanceOf(Map::class.java)), hasEntry(equalTo("STR"),
        `is`("item: 50")))).perform(click())
```

Example 3 (json):
```json
onData(allOf(is(instanceOf(Map.class)), hasEntry(equalTo("STR"), is("item: 50"))))
    .perform(click());
```

Example 4 (json):
```json
hasEntry(equalTo("STR"), `is`("item: 50"))
```

---

## Espresso Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso

**Contents:**
- Espresso Stay organized with collections Save and categorize content based on your preferences.
  - Kotlin
  - Java
- Target audience
- Synchronization capabilities
- Packages
- Additional resources
  - Samples

Use Espresso to write concise, beautiful, and reliable Android UI tests.

The following code snippet shows an example of an Espresso test:

Kotlin @Test fun greeterSaysHello() { onView(withId(R.id.name_field)).perform(typeText("Steve")) onView(withId(R.id.greet_button)).perform(click()) onView(withText("Hello Steve!")).check(matches(isDisplayed())) } Java @Test public void greeterSaysHello() { onView(withId(R.id.name_field)).perform(typeText("Steve")); onView(withId(R.id.greet_button)).perform(click()); onView(withText("Hello Steve!")).check(matches(isDisplayed())); }

The core API is small, predictable, and easy to learn and yet remains open for customization. Espresso tests state expectations, interactions, and assertions clearly without the distraction of boilerplate content, custom infrastructure, or messy implementation details getting in the way.

Espresso tests run optimally fast! It lets you leave your waits, syncs, sleeps, and polls behind while it manipulates and asserts on the application UI when it is at rest.

Espresso is targeted at developers, who believe that automated testing is an integral part of the development lifecycle. While it can be used for black-box testing, Espresso’s full power is unlocked by those who are familiar with the codebase under test.

Each time your test invokes onView(), Espresso waits to perform the corresponding UI action or assertion until the following synchronization conditions are met:

By performing these checks, Espresso substantially increases the likelihood that only one UI action or assertion can occur at any given time. This capability gives you more reliable and dependable test results.

You can learn more about the latest versions by reading the release notes.

For more information about using Espresso in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (python):
```python
@Test
fun greeterSaysHello() {
    onView(withId(R.id.name_field)).perform(typeText("Steve"))
    onView(withId(R.id.greet_button)).perform(click())
    onView(withText("Hello Steve!")).check(matches(isDisplayed()))
}
```

Example 2 (java):
```java
@Test
public void greeterSaysHello() {
    onView(withId(R.id.name_field)).perform(typeText("Steve"));
    onView(withId(R.id.greet_button)).perform(click());
    onView(withText("Hello Steve!")).check(matches(isDisplayed()));
}
```

---

## Espresso-Intents Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/intents

**Contents:**
- Espresso-Intents Stay organized with collections Save and categorize content based on your preferences.
- Include Espresso-Intents in your project
  - Groovy
  - Kotlin
  - Groovy
  - Kotlin
- Write test rules
  - Kotlin
  - Java
- Match

Espresso-Intents is an extension to Espresso, which enables validation and stubbing of intents sent out by the application under test. It’s like Mockito, but for Android Intents.

If your app delegates functionality to other apps or the platform, you can use Espresso-Intents to focus on your own app's logic while assuming that other apps or the platform will function correctly. With Espresso-Intents, you can match and validate your outgoing intents or even provide stub responses in place of actual intent responses.

In your app's app/build.gradle file, add the following line inside dependencies:

Espresso-Intents is only compatible with Espresso 2.1+ and version 0.3+ of Android testing libraries, so make sure you update those lines as well:

Before writing an Espresso-Intents test, set up an IntentsTestRule. This is an extension of the class ActivityTestRule and makes it easy to use Espresso-Intents APIs in functional UI tests. An IntentsTestRule initializes Espresso-Intents before each test annotated with @Test and releases Espresso-Intents after each test run.

The following code snippet is an example of an IntentsTestRule:

Espresso-Intents provides the ability to intercept outgoing intents based on certain matching criteria, which are defined using Hamcrest Matchers. Hamcrest allows you to:

Espresso-Intents offers the intended() and intending() methods for intent validation and stubbing, respectively. Both take a Hamcrest Matcher<Intent> object as an argument.

The following code snippet shows intent validation that uses existing intent matchers that matches an outgoing intent that starts a browser:

Espresso-Intents records all intents that attempt to launch activities from the application under test. Using the intended() method, which is similar to Mockito.verify(), you can assert that a given intent has been seen. However, Espresso-Intents doesn't stub out responses to intents unless you explicitly configure it to do so.

The following code snippet is an example test that validates, but doesn't stub out responses to, an outgoing intent that launches an external "phone" activity:

Using the intending() method, which is similar to Mockito.when(), you can provide a stub response for activities that are launched with startActivityForResult(). This is particularly useful for external activities because you cannot manipulate the user interface of an external activity nor control the ActivityResult returned to the activity under test.

The following code snippets implement an example activityResult_DisplaysContactsPhoneNumber() test, which verifies that when a user launches a "contact" activity in the app under test, the contact phone number is displayed:

Build the result to return when a particular activity is launched. The example test intercepts all Intents sent to "contacts" and stubs out their responses with a valid ActivityResult, using the result code RESULT_OK

Instruct Espresso to provide the stub result object in response to all invocations of the "contacts" intent:

Verify that the action used to launch the activity produces the expected stub result. In this case, the example test checks that the phone number "123-345-6789" is returned and displayed when the "contacts activity" is launched:

Here is the complete activityResult_DisplaysContactsPhoneNumber() test:

For more information about using Espresso-Intents in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-08-08 UTC.

**Examples:**

Example 1 (json):
```json
androidTestImplementation 'androidx.test.espresso:espresso-intents:3.6.1'
```

Example 2 (json):
```json
androidTestImplementation('androidx.test.espresso:espresso-intents:3.6.1')
```

Example 3 (json):
```json
androidTestImplementation 'androidx.test:runner:1.6.1'
androidTestImplementation 'androidx.test:rules:1.6.1'
androidTestImplementation 'androidx.test.espresso:espresso-core:3.6.1'
```

Example 4 (json):
```json
androidTestImplementation('androidx.test:runner:1.6.1')
androidTestImplementation('androidx.test:rules:1.6.1')
androidTestImplementation('androidx.test.espresso:espresso-core:3.6.1')
```

---

## Espresso basics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/basics

**Contents:**
- Espresso basics Stay organized with collections Save and categorize content based on your preferences.
- API components
  - Kotlin
  - Java
- Find a view
  - Kotlin
  - Java
  - Kotlin
  - Java
  - Kotlin

This document explains how to complete common automated testing tasks using the Espresso API.

The Espresso API encourages test authors to think in terms of what a user might do while interacting with the application - locating UI elements and interacting with them. At the same time, the framework prevents direct access to activities and views of the application because holding on to these objects and operating on them off the UI thread is a major source of test flakiness. Thus, you will not see methods like getView() and getCurrentActivity() in the Espresso API. You can still safely operate on views by implementing your own subclasses of ViewAction and ViewAssertion.

The main components of Espresso include the following:

In the vast majority of cases, the onView() method takes a hamcrest matcher that is expected to match one — and only one — view within the current view hierarchy. Matchers are powerful and will be familiar to those who have used them with Mockito or JUnit. If you are not familiar with hamcrest matchers, we suggest you start with a quick look at this presentation.

Often the desired view has a unique R.id and a simple withId matcher will narrow down the view search. However, there are many legitimate cases when you cannot determine R.id at test development time. For example, the specific view may not have an R.id or the R.id is not unique. This can make normal instrumentation tests brittle and complicated to write because the normal way to access the view—with findViewById()— does not work. Thus, you may need to access private members of the Activity or Fragment holding the view or find a container with a known R.id and navigate to its content for the particular view.

Espresso handles this problem cleanly by allowing you to narrow down the view using either existing ViewMatcher objects or your own custom ones.

Finding a view by its R.id is as simple as calling onView():

Sometimes, R.id values are shared between multiple views. When this happens an attempt to use a particular R.id gives you an exception, such as AmbiguousViewMatcherException. The exception message provides you with a text representation of the current view hierarchy, which you can search for and find the views that match the non-unique R.id:

Looking through the various attributes of the views, you may find uniquely identifiable properties. In the example above, one of the views has the text "Hello!". You can use this to narrow down your search by using combination matchers:

You can also choose not to reverse any of the matchers:

See ViewMatchers for the view matchers provided by Espresso.

When you have found a suitable matcher for the target view, it is possible to perform instances of ViewAction on it using the perform method.

For example, to click on the view:

You can execute more than one action with one perform call:

If the view you are working with is located inside a ScrollView (vertical or horizontal), consider preceding actions that require the view to be displayed—such as click() and typeText()—with scrollTo(). This ensures that the view is displayed before proceeding to the other action:

See ViewActions for the view actions provided by Espresso.

Assertions can be applied to the currently selected view with the check() method. The most used assertion is the matches() assertion. It uses a ViewMatcher object to assert the state of the currently selected view.

For example, to check that a view has the text "Hello!":

If you want to assert that "Hello!" is content of the view, the following is considered bad practice:

On the other hand, if you want to assert that a view with the text "Hello!" is visible—for example after a change of the views visibility flag—the code is fine.

In this example, SimpleActivity contains a Button and a TextView. When the button is clicked, the content of the TextView changes to "Hello Espresso!".

Here’s how to test this with Espresso:

The first step is to look for a property that helps to find the button. The button in the SimpleActivity has a unique R.id, as expected.

Now to perform the click:

The TextView with the text to verify has a unique R.id too:

Now to verify the content text:

AdapterView is a special type of widget that loads its data dynamically from an Adapter. The most common example of an AdapterView is ListView. As opposed to static widgets like LinearLayout, only a subset of the AdapterView children may be loaded into the current view hierarchy. A simple onView() search would not find views that are not currently loaded.

Espresso handles this by providing a separate onData() entry point which is able to first load the adapter item in question, bringing it into focus prior to operating on it or any of its children.

Warning: Custom implementations of AdapterView can have problems with the onData() method if they break inheritance contracts, particularly the getItem() API. In such cases, the best course of action is to refactor your application code. If you cannot do so, you can implement a matching custom AdapterViewProtocol. For more information, take a look at the default AdapterViewProtocols class provided by Espresso.

This simple test demonstrates how to use onData(). SimpleActivity contains a Spinner with a few items that represent types of coffee beverages. When an item is selected, there is a TextView that changes to "One %s a day!", where %s represents the selected item.

The goal of this test is to open the Spinner, select a specific item, and verify that the TextView contains the item. As the Spinner class is based onAdapterView, it is recommended to use onData() instead of onView() for matching the item.

For the item selection, the Spinner creates a ListView with its contents. This view can be very long, and the element might not be contributed to the view hierarchy. By using onData() we force our desired element into the view hierarchy. The items in the Spinner are strings, so we want to match an item that is equal to the String "Americano":

Espresso provides useful debugging information when a test fails:

Espresso logs all view actions to logcat. For example:

Espresso prints the view hierarchy in the exception message when onView() fails.

When dealing with a complicated view hierarchy or unexpected behavior of widgets it is always helpful to use the Hierarchy Viewer in Android Studio for an explanation.

Espresso warns users about presence of AdapterView widgets. When an onView() operation throws a NoMatchingViewException and AdapterView widgets are present in the view hierarchy, the most common solution is to use onData(). The exception message will include a warning with a list of the adapter views. You may use this information to invoke onData() to load the target view.

For more information about using Espresso in Android tests, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-10 UTC.

**Examples:**

Example 1 (unknown):
```unknown
// withId(R.id.my_view) is a ViewMatcher
// click() is a ViewAction
// matches(isDisplayed()) is a ViewAssertion
onView(withId(R.id.my_view))
    .perform(click())
    .check(matches(isDisplayed()))
```

Example 2 (unknown):
```unknown
// withId(R.id.my_view) is a ViewMatcher
// click() is a ViewAction
// matches(isDisplayed()) is a ViewAssertion
onView(withId(R.id.my_view))
    .perform(click())
    .check(matches(isDisplayed()));
```

Example 3 (unknown):
```unknown
onView(withId(R.id.my_view))
```

Example 4 (unknown):
```unknown
onView(withId(R.id.my_view));
```

---

## Multiprocess Espresso Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/training/testing/espresso/multiprocess

**Contents:**
- Multiprocess Espresso Stay organized with collections Save and categorize content based on your preferences.
- Use the tool
  - Groovy
  - Kotlin
- Understand the tool's architecture
- Additional resources

As your app grows, you might find it useful to place some of your app components in a process other than your app's main process. To test app components in these non-default processes, you can use the functionality of Multiprocess Espresso. This tool, available on Android 8.0 (API level 26) and higher, allows you to seamlessly test your app's UI interactions that cross your app's process boundaries while maintaining Espresso's synchronization guarantees.

When using Multiprocess Espresso, keep the following versioning and scope considerations in mind:

To test a process within your app using Multiprocess Espresso, add a reference to the espresso-remote artifact in your app's build.gradle file:

You also need to add the following to your app's androidTest manifest:

The following code snippet shows how to add these elements:

src/androidTest/AndroidManifest.xml

The previous snippet indicates to the Android framework that you want it to test every process in your app's package. If you want to test only a subset of your app's processes, you can specify a comma-separated list within the targetProcesses element instead:

When you test your app and launch its default process, you might perform a UI interaction, such as a button press, that starts an activity in a secondary process. The system then completes the following steps to enable cross-process testing using Espresso:

Figure 1 illustrates the result of this process:

For further information on this topic, consult the following resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-08-08 UTC.

**Examples:**

Example 1 (json):
```json
dependencies {
    ...
    androidTestImplementation 'androidx.test.espresso:espresso-remote:3.6.1'
}
```

Example 2 (json):
```json
dependencies {
    ...
    androidTestImplementation('androidx.test.espresso:espresso-remote:3.6.1')
}
```

Example 3 (jsx):
```jsx
<manifest ... package="androidx.test.mytestapp.tests">
  <uses-sdk android:targetSdkVersion="27" android:minSdkVersion="14" />
  <instrumentation
    android:name="androidx.test.runner.AndroidJUnitRunner"
    android:targetPackage="androidx.test.mytestapp"
    android:targetProcesses="*">
    <meta-data
      android:name="remoteMethod"
      android:value="androidx.test.espresso.remote.EspressoRemote#remoteInit" />
  </instrumentation>
</manifest>
```

Example 4 (jsx):
```jsx
<instrumentation
    ...
    android:targetProcesses=
            "androidx.test.mytestapp:myFirstAppProcessToTest,
             androidx.test.mytestapp:mySecondAppProcessToTest" ... />
```

---
