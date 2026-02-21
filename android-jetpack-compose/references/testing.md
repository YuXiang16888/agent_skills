# Jetpack-Compose - Testing

**Pages:** 9

---

## Testing cheatsheet Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing-cheatsheet

**Contents:**
- Testing cheatsheet Stay organized with collections Save and categorize content based on your preferences.
- Additional Resources

The Compose testing cheat sheet is a quick reference of some of the most useful Compose test APIs. The cheat sheet is also downloadable in PDF format.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Testing cheatsheet Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/testing-cheatsheet

**Contents:**
- Testing cheatsheet Stay organized with collections Save and categorize content based on your preferences.
- Additional Resources

The Compose testing cheat sheet is a quick reference of some of the most useful Compose test APIs. The cheat sheet is also downloadable in PDF format.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Testing APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/apis

**Contents:**
- Testing APIs Stay organized with collections Save and categorize content based on your preferences.
  - Finders
    - Select a single node
    - Select multiple nodes
    - Unmerged tree
  - Assertions
  - Actions
  - Matchers
    - Hierarchical matchers
    - Selectors

There are three main ways to interact with UI elements:

Some of these APIs accept a SemanticsMatcher to refer to one or more nodes in the semantics tree.

You can use onNode and onAllNodes to select one or multiple nodes respectively, but you can also use convenience finders for the most common searches, such as onNodeWithText, and onNodeWithContentDescription. You can browse the complete list in the Compose Testing cheat sheet.

Some nodes merge the semantics information of their children. For example, a button with two text elements merges the text element labels:

From a test, use printToLog() to show the semantics tree:

This code prints the following output:

If you need to match a node of what would be the unmerged tree, you can set useUnmergedTree to true:

This code prints the following output:

The useUnmergedTree parameter is available in all finders. For example, here it's used in an onNodeWithText finder.

Check assertions by calling assert() on the SemanticsNodeInteraction returned by a finder with one or multiple matchers:

You can also use convenience functions for the most common assertions, such as assertExists, assertIsDisplayed, and assertTextEquals. You can browse the complete list in the Compose Testing cheat sheet.

There are also functions to check assertions on a collection of nodes:

To inject an action on a node, call a perform…() function:

Here are some examples of actions:

You can browse the complete list in the Compose Testing cheat sheet.

A variety of matchers are available for testing your Compose code.

Hierarchical matchers let you go up or down the semantics tree and perform matching.

Here are some examples of these matchers being used:

An alternative way to create tests is to use selectors which can make some tests more readable.

You can browse the complete list in the Compose Testing cheat sheet.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (typescript):
```typescript
composeTestRule.onNode(<<SemanticsMatcher>>, useUnmergedTree = false): SemanticsNodeInteraction
```

Example 2 (unknown):
```unknown
// Example
composeTestRule
    .onNode(hasText("Button")) // Equivalent to onNodeWithText("Button")
```

Example 3 (typescript):
```typescript
composeTestRule
    .onAllNodes(<<SemanticsMatcher>>): SemanticsNodeInteractionCollection
```

Example 4 (unknown):
```unknown
// Example
composeTestRule
    .onAllNodes(hasText("Button")) // Equivalent to onAllNodesWithText("Button")
```

---

## Synchronize your tests Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/synchronization

**Contents:**
- Synchronize your tests Stay organized with collections Save and categorize content based on your preferences.
  - Disable automatic synchronization
  - Idle resources
  - Manual synchronization
  - Wait for conditions
- Additional Resources

Compose tests are synchronized by default with your UI. When you call an assertion or an action with the ComposeTestRule, the test is synchronized beforehand, waiting until the UI tree is idle.

Normally, you don't have to take any action. However, there are some edge cases you should know about.

When a test is synchronized, your Compose app is advanced in time using a virtual clock. This means Compose tests don't run in real time, so they can pass as fast as possible.

However, if you don't use the methods that synchronize your tests, no recomposition will occur and the UI will appear to be paused.

Note that this requirement only applies to Compose hierarchies and not to the rest of the app.

When you call an assertion or action through the ComposeTestRule such as assertExists(), your test is synchronized with the Compose UI. In some cases you might want to stop this synchronization and control the clock yourself. For example, you can control time to take accurate screenshots of an animation at a point where the UI would still be busy. To disable automatic synchronization, set the autoAdvance property in the mainClock to false:

Typically you will then advance the time yourself. You can advance exactly one frame with advanceTimeByFrame() or by a specific duration with advanceTimeBy():

Compose can synchronize tests and the UI so that every action and assertion is done in an idle state, waiting or advancing the clock as needed. However, some asynchronous operations whose results affect the UI state can be run in the background while the test is unaware of them.

Create and register these idling resources in your test so that they're taken into account when deciding whether the app under test is busy or idle. You don't have to take action unless you need to register additional idling resources, for example, if you run a background job that is not synchronized with Espresso or Compose.

This API is very similar to Espresso's Idling Resources to indicate whether the subject under test is idle or busy. Use the Compose test rule to register the implementation of the IdlingResource.

In certain cases, you have to synchronize the Compose UI with other parts of your test or the app you're testing.

The waitForIdle() function waits for Compose to be idle, but the function depends on the autoAdvance property:

Note that in both cases, waitForIdle() also waits for pending draw and layout passes.

Also, you can advance the clock until a certain condition is met with advanceTimeUntil().

Note that the given condition should be checking the state that can be affected by this clock (it only works with Compose state).

Any condition that depends on external work, such as data loading or Android's measure or draw (that is, measure or draw external to Compose), should use a more general concept such as waitUntil():

You can also use any of the waitUntil helpers:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (swift):
```swift
@Test
fun counterTest() {
    val myCounter = mutableStateOf(0) // State that can cause recompositions.
    var lastSeenValue = 0 // Used to track recompositions.
    composeTestRule.setContent {
        Text(myCounter.value.toString())
        lastSeenValue = myCounter.value
    }
    myCounter.value = 1 // The state changes, but there is no recomposition.

    // Fails because nothing triggered a recomposition.
    assertTrue(lastSeenValue == 1)

    // Passes because the assertion triggers recomposition.
    composeTestRule.onNodeWithText("1").assertExists()
}
```

Example 2 (unknown):
```unknown
composeTestRule.mainClock.autoAdvance = false
```

Example 3 (unknown):
```unknown
composeTestRule.mainClock.advanceTimeByFrame()
composeTestRule.mainClock.advanceTimeBy(milliseconds)
```

Example 4 (unknown):
```unknown
composeTestRule.registerIdlingResource(idlingResource)
composeTestRule.unregisterIdlingResource(idlingResource)
```

---

## Common patterns Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/common-patterns

**Contents:**
- Common patterns Stay organized with collections Save and categorize content based on your preferences.
  - Test in isolation
  - Access the activity and resources after setting your own content
  - Custom semantics properties
  - Verify state restoration
  - Test different device configurations
- Additional Resources

You can test your Compose app with well-established approaches and patterns.

ComposeTestRule lets you start an activity displaying any composable: your full application, a single screen, or a small element. It's also a good practice to check that your composables are correctly encapsulated and they work independently, allowing for easier and more focused UI testing.

This doesn't mean you should only create unit UI tests. UI tests scoping larger parts of your UI are also very important.

Oftentimes you need to set the content under test using composeTestRule.setContent and you also need to access activity resources, for example to assert that a displayed text matches a string resource. However, you can't call setContent on a rule created with createAndroidComposeRule() if the activity already calls it.

A common pattern to achieve this is to create an AndroidComposeTestRule using an empty activity such as ComponentActivity.

Note that ComponentActivity needs to be added to your app's AndroidManifest.xml file. Enable that by adding this dependency to your module:

You can create custom semantics properties to expose information to tests. To do this, define a new SemanticsPropertyKey and make it available using the SemanticsPropertyReceiver.

Now use that property in the semantics modifier:

From tests, use SemanticsMatcher.expectValue to assert the value of the property:

Verify that the state of your Compose elements is correctly restored when the activity or process is recreated. Perform such checks without relying on activity recreation with the StateRestorationTester class.

This class lets you simulate the recreation of a composable. It's especially useful to verify the implementation of rememberSaveable.

Android apps need to adapt to many changing conditions: window sizes, locales, font sizes, dark and light themes, and more. Most of these conditions are derived from device-level values controlled by the user and exposed with the current Configuration instance. Testing different configurations directly in a test is difficult since the test must configure device-level properties.

DeviceConfigurationOverride is a test-only API that lets you simulate different device configurations in a localized way for the @Composable content under test.

The companion object of DeviceConfigurationOverride has the following extension functions, which override device-level configuration properties:

To apply a specific override, wrap the content under test in a call to the DeviceConfigurationOverride() top-level function, passing the override to apply as a parameter.

For example, the following code applies the DeviceConfigurationOverride.ForcedSize() override to change the density locally, forcing the MyScreen composable to be rendered in a large landscape window, even if the device the test is running on doesn't support that window size directly:

composeTestRule.setContent { DeviceConfigurationOverride( DeviceConfigurationOverride.ForcedSize(DpSize(1280.dp, 800.dp)) ) { MyScreen() // Will be rendered in the space for 1280dp by 800dp without clipping. } }DeviceConfigurationOverrideSnippets.kt

To apply multiple overrides together, use DeviceConfigurationOverride.then():

composeTestRule.setContent { DeviceConfigurationOverride( DeviceConfigurationOverride.FontScale(1.5f) then DeviceConfigurationOverride.FontWeightAdjustment(200) ) { Text(text = "text with increased scale and weight") } }DeviceConfigurationOverrideSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (php):
```php
class MyComposeTest {

    @get:Rule
    val composeTestRule = createAndroidComposeRule<ComponentActivity>()

    @Test
    fun myTest() {
        // Start the app
        composeTestRule.setContent {
            MyAppTheme {
                MainScreen(uiState = exampleUiState, /*...*/)
            }
        }
        val continueLabel = composeTestRule.activity.getString(R.string.next)
        composeTestRule.onNodeWithText(continueLabel).performClick()
    }
}
```

Example 2 (bash):
```bash
debugImplementation("androidx.compose.ui:ui-test-manifest:$compose_version")
```

Example 3 (typescript):
```typescript
// Creates a semantics property of type Long.
val PickedDateKey = SemanticsPropertyKey<Long>("PickedDate")
var SemanticsPropertyReceiver.pickedDate by PickedDateKey
```

Example 4 (css):
```css
val datePickerValue by remember { mutableStateOf(0L) }
MyCustomDatePicker(
    modifier = Modifier.semantics { pickedDate = datePickerValue }
)
```

---

## Test your Compose layout Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing

**Contents:**
- Test your Compose layout Stay organized with collections Save and categorize content based on your preferences.
- Views
- Key Concepts
- Testing cheatsheet
- Setup
- Additional Resources
- Codelab
  - Samples
- Recommended for you

Test your app's UI to verify that behavior of your Compose code is correct. This lets you catch errors early and improve the quality of your app.

Compose provides a set of testing APIs to find elements, verify their attributes, and perform user actions. The APIs also include advanced features such as time manipulation. Use these APIs to create robust tests that verify your app's behavior.

If you are working with views instead of Compose, see the general Test apps on Android section.

In particular, a good place to start is the Automate UI tests guide. It lays out how you can automate tests that run on-device, including when using views.

The following are some key concepts for testing your Compose code:

See the testing cheatsheet for an overview of all the key topics you should learn about testing in Compose.

Set up your app to let you test compose code.

First, add the following dependencies to the build.gradle file of the module containing your UI tests:

This module includes a ComposeTestRule and an implementation for Android called AndroidComposeTestRule. Through this rule you can set Compose content or access the activity. You construct the rules using factory functions, either createComposeRule or, if you need access to an activity, createAndroidComposeRule. A typical UI test for Compose looks like this:

To learn more, try the Jetpack Compose Testing codelab.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (typescript):
```typescript
// Test rules and transitive dependencies:
androidTestImplementation("androidx.compose.ui:ui-test-junit4:$compose_version")
// Needed for createComposeRule(), but not for createAndroidComposeRule<YourActivity>():
debugImplementation("androidx.compose.ui:ui-test-manifest:$compose_version")
```

Example 2 (php):
```php
// file: app/src/androidTest/java/com/package/MyComposeTest.kt

class MyComposeTest {

    @get:Rule val composeTestRule = createComposeRule()
    // use createAndroidComposeRule<YourActivity>() if you need access to
    // an activity

    @Test
    fun myTest() {
        // Start the app
        composeTestRule.setContent {
            MyAppTheme {
                MainScreen(uiState = fakeUiState, /*...*/)
            }
        }

        composeTestRule.onNodeWithText("Continue").performClick()

        composeTestRule.onNodeWithText("Welcome").assertIsDisplayed()
    }
}
```

---

## Debug tests Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/debug

**Contents:**
- Debug tests Stay organized with collections Save and categorize content based on your preferences.
- Additional Resources

The main way to solve problems in your tests is to look at the semantics tree. Print the tree by calling composeTestRule.onRoot().printToLog() at any point in your test. This function prints a log like this:

These logs contain valuable information for tracking bugs down.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
Node #1 at (...)px
 |-Node #2 at (...)px
   OnClick = '...'
   MergeDescendants = 'true'
    |-Node #3 at (...)px
    | Text = 'Hi'
    |-Node #5 at (83.0, 86.0, 191.0, 135.0)px
      Text = 'There'
```

---

## Semantics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/semantics

**Contents:**
- Semantics Stay organized with collections Save and categorize content based on your preferences.
- Additional Resources

UI tests in Compose use semantics to interact with the UI hierarchy. Semantics, as the name implies, give meaning to a piece of UI. In this context, a "piece of UI" (or element) can mean anything from a single composable to a full screen. The semantics tree is generated alongside the UI hierarchy and describes the hierarchy.

You can learn more about semantics generally in Semantics in Compose.

Figure 1. A typical UI hierarchy and its semantics tree.

The semantics framework is primarily used for accessibility, so tests take advantage of the information exposed by semantics about the UI hierarchy. Developers decide what and how much to expose.

Figure 2. A typical button containing an icon and text.

For example, given a button like this that consists of an icon and a text element, the default semantics tree only contains the text label "Like". This is because some composables, such as Text, already expose some properties to the semantics tree. You can add properties to the semantics tree by using a Modifier.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (css):
```css
MyButton(
    modifier = Modifier.semantics { contentDescription = "Add to favorites" }
)
```

---

## Interoperability Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/testing/interoperability

**Contents:**
- Interoperability Stay organized with collections Save and categorize content based on your preferences.
- Interoperability with Espresso
- Interoperability with UiAutomator
- Additional Resources

Compose integrates with common testing frameworks.

In a hybrid app, you can find Compose components inside view hierarchies and views inside Compose composables (via the AndroidView composable).

There are no special steps needed to match either type. You match views with Espresso's onView, and Compose elements with the ComposeTestRule.

By default, composables are accessible from UiAutomator only by their convenient descriptors (displayed text, content description, etc.). If you want to access any composable that uses Modifier.testTag, you need to enable the semantic property testTagsAsResourceId for the particular composable's subtree. Enabling this behavior is useful for composables that don't have any other unique handle, such as scrollable composables (for example, LazyColumn).

Enable the semantic property only once high in your composables hierarchy to ensure all nested composables with Modifier.testTag are accessible from UiAutomator.

Any composable with the Modifier.testTag(tag) can be accessible with the use of By.res(resourceName) using the same tag as the resourceName.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (python):
```python
@Test
fun androidViewInteropTest() {
    // Check the initial state of a TextView that depends on a Compose state.
    Espresso.onView(withText("Hello Views")).check(matches(isDisplayed()))
    // Click on the Compose button that changes the state.
    composeTestRule.onNodeWithText("Click here").performClick()
    // Check the new value.
    Espresso.onView(withText("Hello Compose")).check(matches(isDisplayed()))
}
```

Example 2 (sql):
```sql
Scaffold(
    // Enables for all composables in the hierarchy.
    modifier = Modifier.semantics {
        testTagsAsResourceId = true
    }
){
    // Modifier.testTag is accessible from UiAutomator for composables nested here.
    LazyColumn(
        modifier = Modifier.testTag("myLazyColumn")
    ){
        // Content
    }
}
```

Example 3 (swift):
```swift
val device = UiDevice.getInstance(getInstrumentation())

val lazyColumn: UiObject2 = device.findObject(By.res("myLazyColumn"))
// Some interaction with the lazyColumn.
```

---
