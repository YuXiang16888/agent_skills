# Jetpack-Compose - Performance

**Pages:** 9

---

## Use a baseline profile Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/baseline-profiles

**Contents:**
- Use a baseline profile Stay organized with collections Save and categorize content based on your preferences.
- Compose performance considerations
- Benefits of baseline profiles
  - Macrobenchmark
- Additional Resources

Baseline Profiles improve code execution speed by about 30% from the first launch by avoiding interpretation and just-in-time (JIT) compilation steps for included code paths. By shipping a Baseline Profile in an app or library, you enable Android Runtime (ART) to optimize included code paths through ahead-of-time (AOT) compilation, providing performance enhancements for every new app install and every app update. This profile-guided optimization (PGO) lets apps optimize startup, reduce interaction jank, and improve overall runtime performance from the first launch for end users.

Compose is distributed as a library instead of as part of the Android platform. This approach lets the Compose team update Compose frequently and support a wide range of Android versions. However, distributing Compose as a library imposes a cost.

Android platform code is already compiled and installed on the device. Libraries, however, need to be loaded when the app launches and interpreted JIT when needed. This can slow the app on startup and when it uses a library feature for the first time.

You can improve performance by defining Baseline Profiles. These profiles define classes and methods needed on critical user journeys and are distributed with your app's APK or AAB. During app installation, ART compiles this critical code AOT so that it's ready for use when the app launches.

A good Baseline Profile definition is not always straightforward, and because of this, Compose ships with one by default. You might not have to do any work to see this benefit. However, the Baseline Profile that ships with Compose only contains optimizations for the code within the Compose library.

To get the best optimization, create a Baseline Profile for your app that uses Macrobenchmark to cover critical user journeys. When you define your own profile, you must test the profile to verify that it's helping. A good way to do that is to write Macrobenchmark tests for your app and check the test results as you write and revise your Baseline Profile.

For an example of how to write Macrobenchmark tests for your Compose UI, see the Macrobenchmark Compose sample.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-04-11 UTC.

---

## Semantics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/semantics

**Contents:**
- Semantics Stay organized with collections Save and categorize content based on your preferences.
- Semantic properties
  - Headings
  - Alerts and pop ups
  - Window-like components
  - Error components
  - Progress tracking components
  - List and item information
  - State description
  - Custom actions

Aside from the primary information that a composable carries, like a text string of a Text composable, it can be helpful to have more supplemental information about UI elements.

Information about the meaning and role of a component in Compose is called semantics, which are a way to provide additional context about composables to services like accessibility, autofill, and testing. For example, a camera icon might visually just be an image, but the semantic meaning could be "Take a photo".

By combining the appropriate semantics with the appropriate Compose APIs, you provide as much information about your component as possible to accessibility services, which then decide how to represent it to the user.

Material and Compose UI and Foundation APIs come with built-in semantics that follow their specific role and function, but you can also modify these semantics for existing APIs or set new ones for custom components, according to your specific requirements.

Semantic properties convey the meaning of the corresponding composable. For example, the Text composable contains a semantic property text, because that's the meaning of that composable. An Icon contains a contentDescription property (if set by the developer) that conveys in text what the meaning of the icon is.

Consider how semantics properties convey the meaning of a composable. Consider a Switch. This is how it looks to the user:

To describe the meaning of this element, you could say the following: "This is a Switch, which is a toggleable element in its 'On' state. You can click it to interact with it."

This is exactly what the semantics properties are used for. The semantics node of this Switch element contains the following properties, as visualized with the Layout Inspector:

The Role indicates the type of element. The StateDescription describes how the "On" state should be referenced. By default this is a localized version of the word "On", but this can be made more specific (for example, "Enabled") based on the context. The ToggleableState is the current state of the Switch. The OnClick property references the method used to interact with this element.

Keeping track of the semantics properties of each composable in your app unlocks a lot of powerful possibilities:

Composables and modifiers that are built on top of the Compose foundation library already set the relevant properties for you by default. Optionally, you can change these properties manually, to improve the accessibility support for specific use cases, or change your composables' merging or clearing strategy.

To signal the specific content type of your component to accessibility services, you can apply a variety of different semantics. These additions will support the main semantic information in place and help accessibility services fine-tune how your component is represented, announced, or interacted with.

For a full list of semantics properties, see the SemanticsProperties object. For a full list of possible Accessibility Actions, see the SemanticsActions object.

Apps often contain screens with text-rich content, like long articles or news pages, which are usually divided into different subsections with headings:

Users with accessibility needs can have difficulties navigating such a screen easily. To improve the navigation experience, some accessibility services allow for easier navigation directly between sections or headings. To enable this, indicate that your component is a heading by defining its semantics property:

@Composable private fun Subsection(text: String) { Text( text = text, style = MaterialTheme.typography.headlineSmall, modifier = Modifier.semantics { heading() } ) }AccessibilitySnippets.kt

If your component is an alert or a pop up, like a Snackbar, you might want to signal to accessibility services that a new structure or updates to content can be conveyed to users.

Alert-like components can be marked with the liveRegion semantics property. This allows accessibility services to automatically notify the user of changes to this component, or its children:

PopupAlert( message = "You have a new message", modifier = Modifier.semantics { liveRegion = LiveRegionMode.Polite } )AccessibilitySnippets.kt

You should use liveRegionMode.Polite in most cases where users' attention should only briefly be drawn to alerts or important changing content on-screen.

You should use liveRegion.Assertive sparingly to avoid disruptive feedback. It should be used for situations where it's crucial that users are made aware of time-sensitive content:

PopupAlert( message = "Emergency alert incoming", modifier = Modifier.semantics { liveRegion = LiveRegionMode.Assertive } )AccessibilitySnippets.kt

Live regions shouldn't be used on content that updates frequently, such as countdown timers, to avoid overwhelming users with constant feedback.

Window-like custom components, similar to ModalBottomSheet, need additional signals to differentiate them from surrounding content. For this, you can use paneTitle semantics, so that any relevant window or pane changes may be represented appropriately by the accessibility services, along with its main semantic information:

ShareSheet( message = "Choose how to share this photo", modifier = Modifier .fillMaxWidth() .align(Alignment.TopCenter) .semantics { paneTitle = "New bottom sheet" } )AccessibilitySnippets.kt

For reference, see how Material 3 uses paneTitle for its components.

For other content types, such as error-like components, you might want to expand on the main semantic information for users with accessibility needs. When defining error states, you can inform the accessibility services of its error semantics, and provide expanded error messaging.

In this example, TalkBack reads the main error text information, followed by additional, expanded messaging:

Error( errorText = "Fields cannot be empty", modifier = Modifier .semantics { error("Please add both email and password") } )AccessibilitySnippets.kt

For custom components that track progress, you might want to notify users of their progress changes, including the current progress value, its range, and step size. You can do so with progressBarRangeInfo semantics―this ensures that accessibility services are aware of progress changes, and can update users accordingly. Different assistive technologies may also have unique ways of hinting at increasing and decreasing progression.

ProgressInfoBar( modifier = Modifier .semantics { progressBarRangeInfo = ProgressBarRangeInfo( current = progress, range = 0F..1F ) } )AccessibilitySnippets.kt

In custom lists and grids with many items, it might be helpful for accessibility services to also receive more detailed information, like the total number of items and indices.

By using collectionInfo and collectionItemInfo semantics on the list and items respectively, in this long list, accessibility services can inform users of what item index they are at out of the total collection, in addition to textual semantic information:

MilkyWayList( modifier = Modifier .semantics { collectionInfo = CollectionInfo( rowCount = milkyWay.count(), columnCount = 1 ) } ) { milkyWay.forEachIndexed { index, text -> Text( text = text, modifier = Modifier.semantics { collectionItemInfo = CollectionItemInfo(index, 0, 0, 0) } ) } }AccessibilitySnippets.kt

A composable can define a stateDescription for semantics which the Android framework uses to read out the state that the composable is in. For example, a toggleable composable can be in either a "checked" or an "unchecked" state. In some cases, you might want to override the default state description labels that Compose uses. You can do so by explicitly specifying the state description labels before defining a composable as toggleable:

@Composable private fun TopicItem(itemTitle: String, selected: Boolean, onToggle: () -> Unit) { val stateSubscribed = stringResource(R.string.subscribed) val stateNotSubscribed = stringResource(R.string.not_subscribed) Row( modifier = Modifier .semantics { // Set any explicit semantic properties stateDescription = if (selected) stateSubscribed else stateNotSubscribed } .toggleable( value = selected, onValueChange = { onToggle() } ) ) { /* ... */ } }AccessibilitySnippets.kt

Custom actions can be used for more complex touchscreen gestures, like swipe to dismiss or drag and drop, as these can be challenging for users with motor impairments or other disabilities to interact with.

To make the swipe to dismiss gesture more accessible, you can link it to a custom action, passing the dismissal action and label there:

SwipeToDismissBox( modifier = Modifier.semantics { // Represents the swipe to dismiss for accessibility customActions = listOf( CustomAccessibilityAction( label = "Remove article from list", action = { removeArticle() true } ) ) }, state = rememberSwipeToDismissBoxState(), backgroundContent = {} ) { ArticleListItem() }AccessibilitySnippets.kt

An accessibility service like TalkBack then highlights the component, and hints that there are more actions available in its menu, representing the swipe to dismiss action there:

Another use case for custom actions are long lists with items that have more available actions, as it might be tedious for users to iterate through each action for every single item individually:

To improve the navigation experience, which is especially helpful for interaction-based assistive technologies like Switch Access or Voice Access, you can use custom actions on the container to move actions out of individual traversal, and into a separate action menu:

ArticleListItemRow( modifier = Modifier .semantics { customActions = listOf( CustomAccessibilityAction( label = "Open article", action = { openArticle() true } ), CustomAccessibilityAction( label = "Add to bookmarks", action = { addToBookmarks() true } ), ) } ) { Article( modifier = Modifier.clearAndSetSemantics { }, onClick = openArticle, ) BookmarkButton( modifier = Modifier.clearAndSetSemantics { }, onClick = addToBookmarks, ) }AccessibilitySnippets.kt

In these cases, make sure to manually clear the original children's semantics with the clearAndSetSemantics modifier, as you are moving them into custom actions.

Using Switch Access as an example, its menu opens upon selection of the container and lists the available nested actions there:

A composition describes the UI of your app and is produced by running composables. The composition is a tree-structure that consists of the composables that describe your UI.

Next to the Composition, there exists a parallel tree, called the semantics tree. This tree describes your UI in an alternative manner that is understandable for Accessibility services and for the Testing framework. Accessibility services use the tree to describe the app to users with a specific need. The testing framework uses the tree to interact with your app and make assertions about it. The Semantics tree does not contain the information on how to draw your composables, but it contains information about the semantic meaning of your composables.

If your app consists of composables and modifiers from the Compose foundation and material library, the Semantics tree is automatically filled and generated for you. However when you're adding custom low-level composables, you have to manually provide its semantics. There might also be situations where your tree does not correctly or fully represent the meaning of the elements on the screen, in which case you can adapt the tree.

Consider for example this custom calendar composable:

In this example, the entire calendar is implemented as a single low-level composable, using the Layout composable and drawing directly to the Canvas. If you don't do anything else, accessibility services won't receive enough information about the content of the composable and the user's selection within the calendar. For example, if a user clicks on the day containing 17, the accessibility framework only receives the description information for the whole calendar control. In this case, the TalkBack accessibility service would announce "Calendar" or, only slightly better, "April Calendar" and the user would be left to wonder what day was selected. To make this composable more accessible, you'll need to add semantic information manually.

As mentioned before, each composable in the UI tree might have zero or more semantics properties set. When a composable has no semantics properties set, it isn't included as part of the Semantics tree. That way, the Semantics tree contains only the nodes that actually contain semantic meaning. However, sometimes to convey the correct meaning of what is shown on the screen, it is also useful to merge certain sub-trees of nodes and treat them as one. That way you can reason about a set of nodes as a whole, instead of dealing with each descendant node individually. As a rule of thumb, each node in this tree represents a focusable element when using Accessibility services.

An example of such a composable is Button. You can reason about a button as a single element, even though it may contain multiple child nodes:

Button(onClick = { /*TODO*/ }) { Icon( imageVector = Icons.Filled.Favorite, contentDescription = null ) Spacer(Modifier.size(ButtonDefaults.IconSpacing)) Text("Like") }SemanticsSnippets.kt

In the Semantics tree, the properties of the button's descendants are merged, and the button is presented as a single leaf node in the tree:

Composables and modifiers can indicate that they want to merge their descendants' semantics properties by calling Modifier.semantics (mergeDescendants = true) {}. Setting this property to true indicates that the semantics properties should be merged. In the Button example, the Button composable uses the clickable modifier internally that includes this semantics modifier. Therefore, the descendant nodes of the button are merged. Read the accessibility documentation to learn more about when you should change merging behavior in your composable.

Several modifiers and composables in the Foundation and Material Compose libraries have this property set. For example, the clickable and toggleable modifiers will automatically merge their descendants. Also, the ListItem composable will merge its descendants.

The semantics tree is in fact two different trees. There's a merged Semantics tree, which merges descendant nodes when mergeDescendants is set to true. There's also an unmerged Semantics tree, which does not apply the merging, but keeps every node intact. Accessibility services use the unmerged tree and apply their own merging algorithms, taking into consideration the mergeDescendants property. The testing framework uses the merged tree by default.

You can inspect both trees with the printToLog() method. By default, and as in the earlier examples, the merged tree is logged. To print the unmerged tree instead, set the useUnmergedTree parameter of the onRoot() matcher to true:

composeTestRule.onRoot(useUnmergedTree = true).printToLog("MY TAG")SemanticsSnippets.kt

The Layout Inspector lets you display both the merged and the unmerged Semantics tree, by selecting the preferred one in the view filter:

For each node in your tree, the Layout Inspector shows both the Merged Semantics and the Semantics set on that node in the properties panel:

By default, matchers in the Testing Framework use the merged Semantics tree. That's why you can interact with a Button by matching the text shown inside it:

composeTestRule.onNodeWithText("Like").performClick()SemanticsSnippets.kt

Override this behavior by setting the useUnmergedTree parameter of the matchers to true, as with the onRoot matcher.

As mentioned before, you can override or clear certain semantics properties or change the merging behavior of the tree. This is particularly relevant when you're creating your own custom components. Without setting the correct properties and merge behavior, your app might not be accessible, and tests might behave differently than you expect. If you want to learn more about testing, see the testing guide.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Stability in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/stability

**Contents:**
- Stability in Compose Stay organized with collections Save and categorize content based on your preferences.
- Immutable objects
  - Mutable objects
- Implementation in Compose
  - Functions
  - Types
- Debug stability
- Fix stability issues
- Summary
- Further reading

Compose considers types to be either stable or unstable. A type is stable if it is immutable, or if it is possible for Compose to know whether its value has changed between recompositions. A type is unstable if Compose can't know whether its value has changed between recompositions.

Compose uses the stability of a composable's parameters to determine whether it can skip the composable during recomposition:

If your app includes many unnecessarily unstable components that Compose always recomposes, you might observe performance issues and other problems.

This document details how you can increase the stability of your app to improve performance and overall user experience.

The following snippets demonstrates the general principles behind stability and recomposition.

The Contact class is an immutable data class. This is because all its parameters are primitives defined with the val keyword. Once you create an instance of Contact, you cannot change the value of the object's properties. If you attempted to do so, you would create a new object.

The ContactRow composable has a parameter of type Contact.

Consider what happens when the user clicks the toggle button and the selected state changes:

While the preceding example uses an immutable object, it is possible to create a mutable object. Consider the following snippet:

As each parameter of Contact is now a var, the class is no longer immutable. If its properties changed, Compose wouldn't become aware. This is because Compose only tracks changes to Compose State objects.

Compose considers such a class unstable. Compose doesn't skip recomposition of unstable classes. As such, if Contact were defined in this way, ContactRow in the previous example would recompose any time selected changed.

It can be helpful, though not crucial, to consider how exactly Compose determines which functions to skip during recomposition.

When the Compose compiler runs on your code, it marks each function and type with one of several tags. These tags reflect how Compose handles the function or type during recomposition.

Compose can mark functions as skippable or restartable. Note that it may mark a function as one, both, or neither of these:

Compose marks types as either immutable or stable. Each type is one or the other:

If your app is recomposing a composable whose parameters have not changed, first check its definition for parameters that are clearly mutable. Compose always recomposes a component if you pass in a type with var properties, or a val property that use a known unstable type.

For detailed information about how to diagnose complex issues with stability in Compose, see the Debug stability guide.

For information about how to bring stability to your Compose implementation, see the Fix stability issues guide.

Overall, you should note the following points:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-03-25 UTC.

**Examples:**

Example 1 (php):
```php
data class Contact(val name: String, val number: String)
```

Example 2 (typescript):
```typescript
@Composable
fun ContactRow(contact: Contact, modifier: Modifier = Modifier) {
   var selected by remember { mutableStateOf(false) }

   Row(modifier) {
      ContactDetails(contact)
      ToggleButton(selected, onToggled = { selected = !selected })
   }
}
```

Example 3 (php):
```php
data class Contact(var name: String, var number: String)
```

---

## Strong skipping mode Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/stability/strongskipping

**Contents:**
- Strong skipping mode Stay organized with collections Save and categorize content based on your preferences.
- Enable strong skipping mode
- Composable skippability
  - When to skip
  - Annotate classes as stable
- Lambda memoization
  - Examples
  - Memoization and recomposition
  - Avoid memoization
- APK Size

Strong Skipping is a mode available in the Compose compiler. When enabled, it changes the compiler's behavior in two ways:

Strong skipping is enabled by default in Kotlin 2.0.20.

To enable strong skipping for a Gradle module in a release prior to 2.0.20, include the following option in the composeCompiler block of your Gradle configuration:

Strong skipping mode relaxes some of the stability rules normally applied by the Compose compiler when it comes to skipping and composable functions. By default, the Compose compiler marks a composable function as skippable if all of its arguments have stable values. Strong skipping mode changes this.

With strong skipping enabled, all restartable composable functions become skippable. This applies whether or not they have unstable parameters. Non-restartable composable functions remain unskippable.

To determine whether to skip a composable during recomposition, Compose compares the value of each parameter with their previous values. The type of comparison depends on the stability of the parameter.

If all parameters meet these requirements, Compose skips the composable during recomposition.

You might want a composable to opt out of strong skipping. That is, you might want a restartable but non-skippable composable. In this case, use the @NonSkippableComposable annotation.

If you want an object using object equality instead of instance equality, continue to annotate the given class with @Stable. An example of when you might have to do this is when observing an entire list of objects, data sources such as Room will allocate new objects for every item in the list any time one of them changes.

Strong skipping mode also enables more memoization of lambdas inside composables. With strong skipping enabled, every lambda inside a composable function will be automatically remembered.

To achieve memoization of lambdas inside composables when using strong skipping, the compiler wraps your lambda with a remember call. It is keyed with the captures of the lambda.

Consider a case where you have a lambda as in the following example:

With strong skipping enabled, the compiler memoizes the lambda by wrapping it in a remember call:

The keys follow the same comparison rules as composable functions. The runtime compares unstable keys using instance equality. It compares stable keys using object equality.

This optimization greatly increases the number of composables that the runtime skips during recomposition. Without memoization, the runtime is much more likely to allocate a new lambda to any composable that takes a lambda parameter during recomposition. As a result, the new lambda has parameters that are not equal to the last composition. This results in recomposition.

If you have a lambda that you don't want to memoize, use the @DontMemoize annotation.

When compiled, skippable Composables result in more generated code than composables that are not skippable. With strong skipping enabled, the compiler marks nearly all composables as skippable and wraps all lambdas in a remember{...}. Due to this, enabling strong skipping mode has a very small impact on the APK size of your application.

Enabling strong skipping in Now In Android increased the APK size by 4kB. The difference in size largely depends on the number of previously unskippable composables that were present in the given app but should be relatively minor.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-27 UTC.

**Examples:**

Example 1 (unknown):
```unknown
android { ... }

composeCompiler {
   enableStrongSkippingMode = true
}
```

Example 2 (python):
```python
@NonSkippableComposable
@Composable
fun MyNonSkippableComposable {}
```

Example 3 (python):
```python
@Composable
fun MyComposable(unstableObject: Unstable, stableObject: Stable) {
    val lambda = {
        use(unstableObject)
        use(stableObject)
    }
}
```

Example 4 (json):
```json
@Composable
fun MyComposable(unstableObject: Unstable, stableObject: Stable) {
    val lambda = remember(unstableObject, stableObject) {
        {
            use(unstableObject)
            use(stableObject)
        }
    }
}
```

---

## Fix stability issues Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/stability/fix

**Contents:**
- Fix stability issues Stay organized with collections Save and categorize content based on your preferences.
- Enable strong skipping
- Make the class immutable
- Immutable collections
- Annotate with Stable or Immutable
  - Annotated classes in collections
    - Configuration file
    - Immutable collection
    - Wrapper
    - Solution

When faced with an unstable class that causes performance issues, you should make it stable. This document outlines several techniques you can use to do so.

You should first try to enable strong skipping mode. Strong skipping mode allows composables with unstable parameters to be skipped and is the easiest method to fix performance issues caused by stability.

See Strong skipping for more information.

You can also try to make an unstable class completely immutable.

A common reason why Compose considers a class unstable are collections. As noted in the Diagnose stability issues page, the Compose compiler cannot be completely sure that collections such as List, Map, and Set are truly immutable and therefore marks them as unstable.

To resolve this, you can use immutable collections. The Compose compiler includes support for Kotlinx Immutable Collections. These collections are guaranteed to be immutable, and the Compose compiler treats them as such. This library is still in alpha, so expect possible changes to its API.

Consider again this unstable class from the Diagnose stability issues guide:

You can make tags stable using an immutable collection. In the class, change type of tags to ImmutableSet<String>:

After doing so, all the class's parameters are immutable, and the Compose compiler marks the class as stable.

A possible path to resolving stability issues is to annotate unstable classes with either @Stable or @Immutable.

Annotating a class is overriding what the compiler would otherwise infer about your class. It is similar to the !! operator in Kotlin. You should be very careful about how you use these annotations. Overriding the compiler behavior could lead you to unforeseen bugs, such as your composable not recomposing when you expect it to.

If it is possible to make your class stable without an annotation, you should strive to achieve stability that way.

The following snippet provides a minimal example of a data class annotated as immutable:

Whether you use the @Immutable or @Stable annotation, the Compose compiler marks the Snack class as stable.

Consider a composable that includes a parameter of type List<Snack>:

Even if you annotate Snack with @Immutable, the Compose compiler still marks the snacks parameter in HighlightedSnacks as unstable.

Parameters face the same problem as classes when it comes to collection types, the Compose compiler always marks a parameter of type List as unstable, even when it is a collection of stable types.

You cannot mark an individual parameter as stable, nor can you annotate a composable to always be skippable. There are multiple paths forwards.

There are several ways you can get around the problem of unstable collections. The following subsections outline these different approaches.

If you are happy to abide by the stability contract in your codebase, then you can opt in to considering Kotlin collections as stable by adding kotlin.collections.* to your stability configuration file.

For compile time safety of immutability, you can use a kotlinx immutable collection, instead of List.

If you cannot use an immutable collection, you could make your own. To do so, wrap the List in an annotated stable class. A generic wrapper is likely the best choice for this, depending on your requirements.

You can then use this as the type of the parameter in your composable.

After taking either of these approaches, the Compose compiler now marks the HighlightedSnacks Composable as both skippable and restartable.

During recomposition, Compose can now skip HighlightedSnacks if none of its inputs have changed.

Beginning with Compose Compiler 1.5.5, a configuration file of classes to consider stable can be provided at compile time. This allows for considering classes you don't control, such as standard library classes like LocalDateTime, as stable.

The configuration file is a plain text file with one class per row. Comments, single, and double wildcards are supported. An example configuration:

To enable this feature, pass the path of the configuration file to the composeCompiler options block of the Compose compiler Gradle plugin configuration.

As the Compose compiler runs on each module in your project separately, you can provide different configurations to different modules if needed. Alternatively, have one configuration at the root level of your project and pass that path to each module.

Another common issue involves multi-module architecture. The Compose compiler can only infer whether a class is stable if all of the non-primitive types that it references are either explicitly marked as stable or in a module that was also built with the Compose compiler.

If your data layer is in a separate module to your UI layer, which is the recommended approach, this may be an issue you encounter.

To solve this issue you can take one of the following approaches:

The same issue also occurs when using external libraries if they don't use the Compose compiler.

When working to fix issues with stability, you shouldn't attempt to make every composable skippable. Attempting to do so can lead to premature optimisation that introduces more issues than it fixes.

There are many situations where being skippable doesn't have any real benefit and can lead to hard to maintain code. For example:

When a composable is skippable it adds a small overhead which may not be worth it. You can even annotate your composable to be non-restartable in cases where you determine that being restartable is more overhead than it's worth.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-10-22 UTC.

**Examples:**

Example 1 (php):
```php
unstable class Snack {
  …
  unstable val tags: Set<String>
  …
}
```

Example 2 (php):
```php
data class Snack{
    …
    val tags: ImmutableSet<String> = persistentSetOf()
    …
}
```

Example 3 (php):
```php
@Immutable
data class Snack(
…
)
```

Example 4 (typescript):
```typescript
restartable scheme("[androidx.compose.ui.UiComposable]") fun HighlightedSnacks(
  …
  unstable snacks: List<Snack>
  …
)
```

---

## Tooling Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/tooling

**Contents:**
- Tooling Stay organized with collections Save and categorize content based on your preferences.
- Layout Inspector
- Composition tracing
- Additional Resources

It can be hard to know where a performance issue lies and what code to start optimizing. Start by using tools to help narrow down where your issue is.

Use the Layout Inspector to inspect your layout and see recomposition counts.

If your UI has poor performance, this is often because of a coding error that forces your UI to be recomposed excessively. On the other hand, some coding errors can prevent your UI from being recomposed when it needs to be, which means UI changes aren't showing up on the screen. Tracking recompositions can help find both of these kinds of problems.

Recomposition in itself is not bad; however, unexpected recomposition can be an issue.

For more information, see the Layout Inspector recomposition counts documentation.

Use composition tracing to trace your composable functions in a system trace. Traces are often the best source of information when first looking into a performance issue. They allow you to form a hypothesis of what the issue is and where to start looking.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-07-29 UTC.

---

## Jetpack Compose Performance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance

**Contents:**
- Jetpack Compose Performance Stay organized with collections Save and categorize content based on your preferences.
- Key concepts
- Properly configure your app
- Tools
- Best Practices
- Views
- Additional Resources

Jetpack Compose delivers excellent performance out of the box. Configure your app using best practices to avoid common pitfalls and optimize your Compose application's performance.

These are some of the key concepts for performance in Compose:

If your app is performing poorly, there might be a configuration problem. A good first step is to check the following configuration options:

Familiarize yourself with the suite of tools available to help you measure and analyze your Compose app's performance.

When developing your app with Compose, keep these best practices in mind:

For more details, see the best practices guide.

If you are working with views instead of Compose, see the dedicated Improve layout performance guide.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-28 UTC.

---

## Diagnose stability issues Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/stability/diagnose

**Contents:**
- Diagnose stability issues Stay organized with collections Save and categorize content based on your preferences.
- Layout Inspector
- Compose compiler reports
  - Setup
    - Example output
    - Composables report
    - Classes report

If you are experiencing performance issues that result from unnecessary or excessive recomposition, you should debug the stability of your app. This guide outlines several methods for doing so.

The Layout Inspector in Android Studio lets you see which composables are recomposing in your app. It displays counts of how many times Compose has recomposed or skipped a component.

The Compose compiler can output the results of its stability inference for inspection. Using this output, you can determine which of your composables are skippable, and which are not. The follow subsections summarize how to use these reports, but for more detailed information see the technical documentation.

Compose compiler reports are not enabled by default. You can activate them with a compiler flag. The exact setup varies depending on your project, but for projects using the Compose compiler gradle plugin you can add the following in each module's build.gradle file.

Compose compiler reports will now be generated when building your project.

The reportsDestination outputs three files. The following are example outputs from JetSnack.

The composables.txt file details each composable functions for the given module, including the stability of their parameters, and whether they are restartable or skippable. The following is a hypothetical example from JetSnack:

This SnackCollection composable is completely restartable, skippable, and stable. This is generally preferable, although certainly not mandatory.

On the other hand, let's take a look at another example.

The HighlightedSnacks composable is not skippable. Compose never skips it during recomposition. This occurs even if none of its parameters have changed. The reason for this is the unstable parameter, snacks.

The file classes.txt contains a similar report on the classes in the given module. The following snippet is the output for the class Snack:

For reference, the following is the definition of Snack:

The Compose compiler has marked Snack as unstable. This is because the type of the tags parameter is Set<String>. This is an immutable type, given that it is not a MutableSet. However, standard collection classes such as Set, List, and Map are ultimately interfaces. As such, the underlying implementation may still be mutable.

For example, you could write val set: Set<String> = mutableSetOf("foo"). The variable is constant and its declared type is not mutable, but its implementation is still mutable. The Compose compiler cannot be sure of the immutability of this class as it only sees the declared type. It therefore marks tags as unstable.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-06-27 UTC.

**Examples:**

Example 1 (unknown):
```unknown
android { ... }

  composeCompiler {
    reportsDestination = layout.buildDirectory.dir("compose_compiler")
    metricsDestination = layout.buildDirectory.dir("compose_compiler")
  }
```

Example 2 (typescript):
```typescript
restartable skippable scheme("[androidx.compose.ui.UiComposable]") fun SnackCollection(
  stable snackCollection: SnackCollection
  stable onSnackClick: Function1<Long, Unit>
  stable modifier: Modifier? = @static Companion
  stable index: Int = @static 0
  stable highlight: Boolean = @static true
)
```

Example 3 (swift):
```swift
restartable scheme("[androidx.compose.ui.UiComposable]") fun HighlightedSnacks(
  stable index: Int
  unstable snacks: List<Snack>
  stable onSnackClick: Function1<Long, Unit>
  stable modifier: Modifier? = @static Companion
)
```

Example 4 (jsx):
```jsx
unstable class Snack {
  stable val id: Long
  stable val name: String
  stable val imageUrl: String
  stable val price: Long
  stable val tagline: String
  unstable val tags: Set<String>
  <runtime stability> = Unstable
}
```

---

## Follow best practices Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/bestpractices

**Contents:**
- Follow best practices Stay organized with collections Save and categorize content based on your preferences.
- Use remember to minimize expensive calculations
- Use lazy layout keys
- Use derivedStateOf to limit recompositions
- Defer reads as long as possible
- Avoid backwards writes
- Additional Resources
- Recommended for you

You might encounter common Compose pitfalls. These mistakes might give you code that seems to run well enough, but can hurt your UI performance. Follow best practices to optimize your app on Compose.

Composable functions can run very frequently, as often as for every frame of an animation. For this reason, you should do as little calculation in the body of your composable as you can.

An important technique is to store the results of calculations with remember. That way, the calculation runs once, and you can fetch the results whenever they're needed.

For example, here's some code that displays a sorted list of names, but does the sorting in a very expensive way:

@Composable fun ContactList( contacts: List<Contact>, comparator: Comparator<Contact>, modifier: Modifier = Modifier ) { LazyColumn(modifier) { // DON’T DO THIS items(contacts.sortedWith(comparator)) { contact -> // ... } } }PerformanceSnippets.kt

Every time ContactsList is recomposed, the entire contact list is sorted all over again, even though the list hasn't changed. If the user scrolls the list, the Composable gets recomposed whenever a new row appears.

To solve this problem, sort the list outside the LazyColumn, and store the sorted list with remember:

@Composable fun ContactList( contacts: List<Contact>, comparator: Comparator<Contact>, modifier: Modifier = Modifier ) { val sortedContacts = remember(contacts, comparator) { contacts.sortedWith(comparator) } LazyColumn(modifier) { items(sortedContacts) { // ... } } }PerformanceSnippets.kt

Now, the list is sorted once, when ContactList is first composed. If the contacts or comparator change, the sorted list is regenerated. Otherwise, the composable can keep using the cached sorted list.

Lazy layouts efficiently reuse items, only regenerating or recomposing them when they have to. However, you can help optimize lazy layouts for recomposition.

Suppose a user operation causes an item to move in the list. For example, suppose you show a list of notes sorted by modification time with the most recently modified note on top.

@Composable fun NotesList(notes: List<Note>) { LazyColumn { items( items = notes ) { note -> NoteRow(note) } } }PerformanceSnippets.kt

There's a problem with this code though. Suppose the bottom note is changed. It's now the most recently modified note, so it goes to the top of the list, and every other note moves down one spot.

Without your help, Compose doesn't realize that unchanged items are just being moved in the list. Instead, Compose thinks the old "item 2" was deleted and a new one was created for item 3, item 4, and all the way down. The result is that Compose recomposes every item on the list, even though only one of them actually changed.

The solution here is to provide item keys. Providing a stable key for each item lets Compose avoid unnecessary recompositions. In this case, Compose can determine the item now at spot 3 is the same item that used to be at spot 2. Since none of the data for that item has changed, Compose doesn't have to recompose it.

@Composable fun NotesList(notes: List<Note>) { LazyColumn { items( items = notes, key = { note -> // Return a stable, unique key for the note note.id } ) { note -> NoteRow(note) } } }PerformanceSnippets.kt

One risk of using state in your compositions is that, if the state changes rapidly, your UI might get recomposed more than you need it to. For example, suppose you're displaying a scrollable list. You examine the list's state to see which item is the first visible item on the list:

val listState = rememberLazyListState() LazyColumn(state = listState) { // ... } val showButton = listState.firstVisibleItemIndex > 0 AnimatedVisibility(visible = showButton) { ScrollToTopButton() }PerformanceSnippets.kt

The problem here is, if the user scrolls the list, listState is constantly changing as the user drags their finger. That means the list is constantly being recomposed. However, you don't actually need to recompose it that often—you don't need to recompose until a new item becomes visible at the bottom. So, that's a lot of extra computation, which makes your UI perform badly.

The solution is to use derived state. Derived state lets you tell Compose which changes of state actually should trigger recomposition. In this case, specify that you care about when the first visible item changes. When that state value changes, the UI needs to recompose, but if the user hasn't yet scrolled enough to bring a new item to the top, it doesn't have to recompose.

val listState = rememberLazyListState() LazyColumn(state = listState) { // ... } val showButton by remember { derivedStateOf { listState.firstVisibleItemIndex > 0 } } AnimatedVisibility(visible = showButton) { ScrollToTopButton() }PerformanceSnippets.kt

When a performance issue has been identified, deferring state reads can help. Deferring state reads will ensure that Compose re-runs the minimum possible code on recomposition. For example, if your UI has state that is hoisted high up in the composable tree and you read the state in a child composable, you can wrap the state read in a lambda function. Doing this makes the read occur only when it is actually needed. For reference, see the implementation in the Jetsnack sample app. Jetsnack implements a collapsing-toolbar-like effect on its detail screen. To understand why this technique works, see the blog post Jetpack Compose: Debugging Recomposition.

To achieve this effect, the Title composable needs the scroll offset in order to offset itself using a Modifier. Here's a simplified version of the Jetsnack code before the optimization is made:

@Composable fun SnackDetail() { // ... Box(Modifier.fillMaxSize()) { // Recomposition Scope Start val scroll = rememberScrollState(0) // ... Title(snack, scroll.value) // ... } // Recomposition Scope End } @Composable private fun Title(snack: Snack, scroll: Int) { // ... val offset = with(LocalDensity.current) { scroll.toDp() } Column( modifier = Modifier .offset(y = offset) ) { // ... } }PerformanceSnippets.kt

When the scroll state changes, Compose invalidates the nearest parent recomposition scope. In this case, the nearest scope is the SnackDetail composable. Note that Box is an inline function, and so is not a recomposition scope. So Compose recomposes SnackDetail and any composables inside SnackDetail. If you change your code to only read the state where you actually use it, then you could reduce the number of elements that need to be recomposed.

@Composable fun SnackDetail() { // ... Box(Modifier.fillMaxSize()) { // Recomposition Scope Start val scroll = rememberScrollState(0) // ... Title(snack) { scroll.value } // ... } // Recomposition Scope End } @Composable private fun Title(snack: Snack, scrollProvider: () -> Int) { // ... val offset = with(LocalDensity.current) { scrollProvider().toDp() } Column( modifier = Modifier .offset(y = offset) ) { // ... } }PerformanceSnippets.kt

The scroll parameter is now a lambda. That means Title can still reference the hoisted state, but the value is only read inside Title, where it's actually needed. As a result, when the scroll value changes, the nearest recomposition scope is now the Title composable–Compose no longer needs to recompose the whole Box.

This is a good improvement, but you can do better! You should be suspicious if you are causing recomposition just to re-layout or redraw a Composable. In this case, all you are doing is changing the offset of the Title composable, which could be done in the layout phase.

@Composable private fun Title(snack: Snack, scrollProvider: () -> Int) { // ... Column( modifier = Modifier .offset { IntOffset(x = 0, y = scrollProvider()) } ) { // ... } }PerformanceSnippets.kt

Previously, the code used Modifier.offset(x: Dp, y: Dp), which takes the offset as a parameter. By switching to the lambda version of the modifier, you can make sure the function reads the scroll state in the layout phase. As a result, when the scroll state changes, Compose can skip the composition phase entirely, and go straight to the layout phase. When you are passing frequently changing State variables into modifiers, you should use the lambda versions of the modifiers whenever possible.

Here is another example of this approach. This code hasn't been optimized yet:

// Here, assume animateColorBetween() is a function that swaps between // two colors val color by animateColorBetween(Color.Cyan, Color.Magenta) Box( Modifier .fillMaxSize() .background(color) )PerformanceSnippets.kt

Here, the box's background color is switching rapidly between two colors. This state is thus changing very frequently. The composable then reads this state in the background modifier. As a result, the box has to recompose on every frame, since the color is changing on every frame.

To improve this, use a lambda-based modifier—in this case, drawBehind. That means the color state is only read during the draw phase. As a result, Compose can skip the composition and layout phases entirely—when the color changes, Compose goes straight to the draw phase.

val color by animateColorBetween(Color.Cyan, Color.Magenta) Box( Modifier .fillMaxSize() .drawBehind { drawRect(color) } )PerformanceSnippets.kt

Compose has a core assumption that you will never write to state that has already been read. When you do this, it is called a backwards write and it can cause recomposition to occur on every frame, endlessly.

The following composable shows an example of this kind of mistake.

@Composable fun BadComposable() { var count by remember { mutableIntStateOf(0) } // Causes recomposition on click Button(onClick = { count++ }, Modifier.wrapContentSize()) { Text("Recompose") } Text("$count") count++ // Backwards write, writing to state after it has been read</b> }PerformanceSnippets.kt

This code updates the count at the end of the composable after reading it on the preceding line. If you run this code, you'll see that after you click the button, which causes a recomposition, the counter rapidly increases in an infinite loop as Compose recomposes this Composable, sees a state read that is out of date, and so schedules another recomposition.

You can avoid backwards writes altogether by never writing to state in Composition. If at all possible, always write to state in response to an event and in a lambda like in the preceding onClick example.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---
