# Jetpack-Compose - Tooling

**Pages:** 10

---

## Compose lint Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/lint

**Contents:**
- Compose lint Stay organized with collections Save and categorize content based on your preferences.
- Minimum version requirement for Compose lint checks

Android Lint is a powerful tool for verifying the correctness or your code. It is highly recommended that you fix any identified lint errors before releasing your app to production.

Compose ships with a number of lint checks by default. This helps verify the correctness of your Compose code.

To simplify compatibility and improve stability for lint check support, Compose 1.9 requires Android Gradle Plugin (AGP) / Lint version 8.8.2 or higher.

If you're using an AGP version lower than 8.8.2 and are unable to upgrade, you can specify the lint version to use in your gradle.properties file:

Previously, the complex dependencies of Compose lint checks led to frequent compatibility issues and made it difficult to determine the correct tool versions (Android Studio, AGP/Lint, Compose).

Minimum version requirements for Compose, AGP, and Studio are as follows:

Required AGP / Lint version

Required Studio version

Koala Feature Drop -> Meerkat

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
android.experimental.lint.version = 8.8.2
```

---

## Debug your Compose UI Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/debug

**Contents:**
- Debug your Compose UI Stay organized with collections Save and categorize content based on your preferences.
- Layout Inspector
  - Get recomposition counts
  - Compose semantics
- Compose UI Check

Tools for debugging your Compose UI are available in Android Studio.

Layout Inspector lets you inspect a Compose layout inside a running app in an emulator or physical device. You can use the Layout Inspector to check how often a composable is recomposed or skipped, which can help identify issues with your app. For example, some coding errors might force your UI to recompose excessively, which can cause poor performance. Some coding errors can prevent your UI from recomposing and, therefore, prevent your UI changes from showing up on the screen. If you're new to Layout inspector, check the guidance on how to run it.

When debugging your Compose layouts, knowing when composables recompose is important in understanding whether your UI is implemented properly. For example, if it's recomposing too many times, your app might be doing more work than is necessary. On the other hand, components that don't recompose when you anticipate them to can lead to unexpected behaviors.

The Layout Inspector shows you when discrete composables in your layout hierarchy have either recomposed or skipped, as you interact with your app. In Android Studio, your recompositions are highlighted to help you determine where in the UI your composables are recomposing.

Figure 1. Recompositions are highlighted in Layout Inspector.

The highlighted portion shows a gradient overlay of the composable in the image section of the Layout Inspector, and gradually disappears so that you can get an idea of where in the UI the composable with the highest recompositions can be found. If one composable is recomposing at a higher rate than another composable, then the first composable receives a stronger gradient overlay color. If you double-click a composable in the layout inspector, you're taken to the corresponding code for analysis.

Open the Layout Inspector window and connect to your app process. In the Component Tree, there are two columns that appear next to the layout hierarchy. The first column shows the number of compositions for each node and the second column displays the number of skips for each node. Selecting a composable node shows the dimensions and parameters of the composable, unless it's an inline function, in which case the parameters can't be shown. You can also see similar information in the Attributes pane when you select a composable from the Component Tree or the Layout Display.

Resetting the count can help you understand recompositions or skips during a specific interaction with your app. If you want to reset the count, click Reset near the top of the Component Tree pane.

Figure 3. Enable the composition and skip counter in Layout Inspector.

In Compose, Semantics describe your UI in an alternative manner that is understandable for Accessibility services and for the Testing framework. You can use the Layout Inspector to inspect semantic information in your Compose layouts.

When selecting a Compose node, use the Attributes pane to check whether it declares semantic information directly, merges semantics from its children, or both. To quickly identify which nodes include semantics, either declared or merged, use select the View options drop-down in the Component Tree pane and select Highlight Semantics Layers. This highlights only the nodes in the tree that include semantics, and you can use your keyboard to quickly navigate between them.

To help you build more adaptive and accessible UIs in Jetpack Compose, Android Studio provides a UI Check mode in Compose Preview. This feature is similar to Accessibility Scanner for views.

When you activate Compose UI check mode on a Compose Preview, Android Studio automatically audits your Compose UI and suggests improvements to make your UI more accessible and adaptive. Android Studio checks that your UI works across different screen sizes. In the Problems panel, the tool shows the issues that it detects, such as text stretched on large screens or low color contrast.

To access this feature, click the UI Check icon on Compose Preview:

UI check automatically previews your UI in different configurations and highlights issues found in different configurations. In the Problems panel, when you click an issue, you can see the details of the issue, suggested fixes, and the renderings that highlight the area of the issue.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Tools for Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling

**Contents:**
- Tools for Compose Stay organized with collections Save and categorize content based on your preferences.
- Tools for designing your UI
- Tools for developing and testing your UI
- Tools for debugging your UI

Android Studio provides a collection of tools to help you speed up UI design using Jetpack Compose libraries. Start working with the tools by first adding the necessary dependencies to your Gradle file through the Bill of Materials (BOM).

Create components, design systems, and layouts to align with design specs.

Accelerate building the UI and testing the running application with less context switching.

Analyze layout, recompositions, and composition tracing to improve your app's UI performance.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Editor actions Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/editor-actions

**Contents:**
- Editor actions Stay organized with collections Save and categorize content based on your preferences.
- Live templates
- Gutter icons
  - Deploy preview
  - Color picker
  - Image resource picker
- Recommended for you

Android Studio has features inside the editor area to improve your productivity with Jetpack Compose.

Android Studio comes with these Compose-related live templates, which allow you to enter code snippets for fast insertion by typing the corresponding template abbreviation:

Gutter icons are contextual actions visible on the sidebar, next to the line numbers. Android Studio introduces several gutter icons specific to Jetpack Compose to ease your developer experience.

You can deploy a @Preview to the emulator or physical device directly from the gutter icon:

Whenever a color is defined inside or outside a composable, its preview is shown on the gutter. You can change the color via the color picker by clicking on it like this:

Whenever a drawable, vector, or image is defined inside or outside a composable, its preview is shown on the gutter. You can change it via the image resource picker by clicking on it like this:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Preview your UI with composable previews Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/previews

**Contents:**
- Preview your UI with composable previews Stay organized with collections Save and categorize content based on your preferences.
- Define your @Preview
  - Dimensions
  - Dynamic color preview
  - Use with different devices
  - Locale
  - Set background color
  - System UI
  - UI mode
  - LocalInspectionMode

A composable is defined by a function and annotated with @Composable:

@Composable fun SimpleComposable() { Text("Hello World") }AndroidStudioComposeSnippets.kt

To enable a preview of this composable, create another composable, annotated with @Composable and @Preview. This new, annotated composable now contains the composable you created initially, SimpleComposable:

@Preview @Composable fun SimpleComposablePreview() { SimpleComposable() }AndroidStudioComposeSnippets.kt

The @Preview annotation tells Android Studio that this composable should be shown in the design view of this file. You can see live updates to your composable preview as you make your edits.

You can add parameters manually in your code to customize the way Android Studio renders @Preview. You can even add the @Preview annotation to the same function multiple times to preview a composable with different properties.

One of the primary benefits of using @Preview composables is to avoid reliance on the emulator in Android Studio. You can save the memory-heavy startup of the emulator for more final look-and-feel changes, and @Preview's ability to make and test small code changes with ease.

To leverage @Preview annotation most effectively, make sure to define your screens in terms of the state it receives as input and the events that it outputs.

Android Studio offers some features to extend composable previews. You can change their container design, interact with them, or deploy them directly to an emulator or device.

By default, @Preview dimensions are chosen automatically to wrap its content. To set the dimensions manually, add heightDp and widthDp parameters. Those values are already interpreted as dp, so you don't need to add .dp to them:

@Preview(widthDp = 50, heightDp = 50) @Composable fun SquareComposablePreview() { Box(Modifier.background(Color.Yellow)) { Text("Hello World") } }AndroidStudioComposeSnippets.kt

If you've enabled dynamic color in your app, use the wallpaper attribute to switch wallpapers and see how your UI reacts to different users' chosen wallpaper. Select from the different wallpaper themes offered by the Wallpaper class. This feature requires Compose 1.4.0 or higher.

In Android Studio Flamingo, you can edit the device parameter of the Preview annotation to define configurations for your composables in different devices.

When the device parameter has an empty string (@Preview(device = "")), you can invoke autocomplete by pressing Ctrl + Space. Then, you can set the values of each parameter.

From autocomplete, you can select any device option from the list–for example, @Preview(device = "id:pixel_4"). Alternatively, you can enter a custom device by choosing spec:width=px,height=px,dpi=int… to set the individual values of each parameter.

To apply, press Enter, or cancel with Esc.

If you set an invalid value, the declaration is underlined in red and a fix may be available (Alt + Enter (⌥ + ⏎ for macOS) > Replace with …. The Inspection attempts to provide a fix that is closest to resembling your input.

To test different user locales, add the locale parameter:

@Preview(locale = "fr-rFR") @Composable fun DifferentLocaleComposablePreview() { Text(text = stringResource(R.string.greeting)) }AndroidStudioComposeSnippets.kt

By default, your composable is displayed with a transparent background. To add a background, add the showBackground and backgroundColor parameters. Keep in mind that backgroundColor is an ARGB Long, not a Color value:

@Preview(showBackground = true, backgroundColor = 0xFF00FF00) @Composable fun WithGreenBackground() { Text("Hello World") }AndroidStudioComposeSnippets.kt

If you need to display the status and action bars inside a preview, add the showSystemUi parameter:

@Preview(showSystemUi = true) @Composable fun DecoratedComposablePreview() { Text("Hello World") }AndroidStudioComposeSnippets.kt

The parameter uiMode can take any of the Configuration.UI_* constants and allows you to change the behavior of the preview accordingly. For example, you can set the preview to Night Mode to see how the theme reacts.

You can read from the LocalInspectionMode CompositionLocal to see if the composable is rendered in a preview (inside an inspectable component). If the composition is rendered in a preview, LocalInspectionMode.current evaluates to true. This information lets you customize your preview; for example, you can show a placeholder image in the preview window instead of showing real data.

This way, you can also work around the limitations. For example, showing sample data instead of calling network request.

@Composable fun GreetingScreen(name: String) { if (LocalInspectionMode.current) { // Show this text in a preview window: Text("Hello preview user!") } else { // Show this text in the app: Text("Hello $name!") } }AndroidStudioComposeSnippets.kt

Android Studio provides features that allow you to interact with your defined previews. This interaction helps you understand your previews' runtime behavior and allows you to better navigate your UI with previews.

The interactive mode lets you interact with a preview similarly to how you would on a device running your program, like a phone or tablet. The interactive mode is isolated in a sandbox environment (meaning, isolated from other previews), where you can click elements and enter user input in the preview. It's a quick way to test different states, gestures, and even animations of your composable.

You can hover over a preview to see the outlines of the composables contained within. Clicking on a composable outline triggers your editor view to navigate to its definition.

You can run a specific @Preview on an emulator or a physical device. The preview is deployed within the same project app as a new Activity, so it shares the same context and permissions. It does not require you to write boilerplate code asking for a permission if it has already been granted.

Click the Run Preview icon next to the @Preview annotation or at the top of the preview, and Android Studio deploys that @Preview to your connected device or emulator.

Every rendered preview can be copied as an image by right clicking on it.

You can showcase multiple versions of the same @Preview composable with different specifications, or different parameters passed to the composable. This way, you can reduce the boilerplate code that you would need to write otherwise.

androidx.compose.ui:ui-tooling-preview 1.6.0-alpha01+ introduces Multipreview API templates: @PreviewScreenSizes, @PreviewFontScales, @PreviewLightDark, and @PreviewDynamicColors, so that with one single annotation, you can preview your Compose UI in common scenarios.

With multipreview, you can define an annotation class that itself has multiple @Preview annotations with different configurations. Adding this annotation to a composable function automatically renders all of the different previews at once. For example, you can use this annotation to preview multiple devices, font sizes, or themes at the same time without repeating those definitions for every single composable.

Start by creating your own custom annotation class:

@Preview( name = "small font", group = "font scales", fontScale = 0.5f ) @Preview( name = "large font", group = "font scales", fontScale = 1.5f ) annotation class FontScalePreviewsAndroidStudioComposeSnippets.kt

You can use this custom annotation for your preview composables:

@FontScalePreviews @Composable fun HelloWorldPreview() { Text("Hello World") }AndroidStudioComposeSnippets.kt

You can combine multiple multipreview annotations and normal preview annotations to create a more complete set of previews. Combining multipreview annotations doesn't mean all the different combinations are shown. Instead, each multipreview annotation acts independently and renders only its own variants.

@Preview( name = "Spanish", group = "locale", locale = "es" ) @FontScalePreviews annotation class CombinedPreviews @CombinedPreviews @Composable fun HelloWorldPreview2() { MaterialTheme { Surface { Text(stringResource(R.string.hello_world)) } } }AndroidStudioComposeSnippets.kt

The mix-and-match nature of multipreview-- and normal preview!-- lets you more comprehensively test many properties of larger scale projects.

Very often, a need arises where you must pass a large dataset to your composable preview. To do this, simply pass sample data to a Composable Preview function by adding a parameter with the @PreviewParameter annotation.

@Preview @Composable fun UserProfilePreview( @PreviewParameter(UserPreviewParameterProvider::class) user: User ) { UserProfile(user) } AndroidStudioComposeSnippets.kt

To provide the sample data, create a class that implements PreviewParameterProvider and returns the sample data as a sequence.

class UserPreviewParameterProvider : PreviewParameterProvider<User> { override val values = sequenceOf( User("Elise"), User("Frank"), User("Julia") ) }AndroidStudioComposeSnippets.kt

This renders one preview per data element in the sequence:

You can use the same provider class for multiple previews. If necessary, limit the number of previews by setting the limit parameter.

@Preview @Composable fun UserProfilePreview2( @PreviewParameter(UserPreviewParameterProvider::class, limit = 2) user: User ) { UserProfile(user) }AndroidStudioComposeSnippets.kt

Previews using @PreviewParameter are named by default using the parameter index and property name (user 0, user 1, user 2, and so on), which can make it difficult to tell them apart. To improve preview clarity, you can provide custom display names for each preview by overriding getDisplayName() in your PreviewParameterProvider. This helps distinguish between different data variations or UI states. For example, you can label previews based on the input data:

class UserAgePreviewParameterProvider : PreviewParameterProvider<User> { // Using a List internally for efficient index-based access private val userList = listOf( User(name = "Elise", age = 30), User(name = "Frank", age = 31), User(name = "Julia", age = 40) ) override val values = userList.asSequence() override fun getDisplayName(index: Int): String? { // Return null or an empty string to use the default index-based name val user = userList.getOrNull(index) ?: return null return "${user.name} - ${user.age}" } }AndroidStudioComposeSnippets.kt

Android Studio executes previews code directly in the preview area. It doesn't require running an emulator or physical device because it leverages a ported part of the Android framework called Layoutlib. Layoutlib is a custom version of the Android framework designed to run outside of Android devices. The goal of the library is to provide a preview of a layout in Android Studio that is very close to its rendering on devices.

Because of the way previews are rendered within Android Studio, they are lightweight and don't require the whole Android framework to render them. However, this comes with the following limitations:

Previews are limited when using ViewModel within a composable. The previews system is not capable of constructing all of the parameters passed to a ViewModel, such as repositories, use cases, managers, or similar. Also, if your ViewModel participates in dependency injection (such as with Hilt), the previews system can't build the whole dependency graph to construct the ViewModel.

When you try to preview a composable with ViewModel, Android Studio shows an error when rendering the particular composable:

If you want to preview a composable that uses a ViewModel, you should create another composable with the parameters from ViewModel passed as arguments of the composable. This way, you don't need to preview the composable that uses the ViewModel.

You can always 'ctrl or ⌘ + click' the @Preview annotation in Android Studio for a full list of parameters that can be adjusted when customizing your preview.

annotation class Preview( val name: String = "", val group: String = "", @IntRange(from = 1) val apiLevel: Int = -1, val widthDp: Int = -1, val heightDp: Int = -1, val locale: String = "", @FloatRange(from = 0.01) val fontScale: Float = 1f, val showSystemUi: Boolean = false, val showBackground: Boolean = false, val backgroundColor: Long = 0, @UiMode val uiMode: Int = 0, @Device val device: String = Devices.DEFAULT, @Wallpaper val wallpaper: Int = Wallpapers.NONE, )AndroidStudioComposeSnippets.kt

To read more about how Android Studio promotes @Preview ease of use, and learn more Tooling tips, check out the blog Compose Tooling.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (typescript):
```typescript
@Composable
fun AuthorScreen(viewModel: AuthorViewModel = viewModel()) {
  AuthorScreen(
    name = viewModel.authorName,
    // ViewModel sends the network requests and makes posts available as a state
    posts = viewModel.posts
  )
}

@Composable
fun AuthorScreen(
  name: NameLabel,
  posts: PostsList
) {
  // ...
}

@Preview
@Composable
fun AuthorScreenPreview(
  // You can use some sample data to preview your composable without the need to construct the ViewModel
  name: String = sampleAuthor.name,
  posts: List<Post> = samplePosts[sampleAuthor]
) {
  AuthorScreen(
      name = NameLabel(name),
      posts = PostsList(posts)
  )
}
```

---

## Kotlin for Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/kotlin

**Contents:**
- Kotlin for Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.
- Default arguments
- Higher-order functions and lambda expressions
  - Trailing lambdas
- Scopes and receivers
- Delegated properties
- Destructuring data classes
- Singleton objects
- Type-safe builders and DSLs
- Kotlin coroutines

Jetpack Compose is built around Kotlin. In some cases, Kotlin provides special idioms that make it easier to write good Compose code. If you think in another programming language and mentally translate that language to Kotlin, you're likely to miss out on some of the strength of Compose, and you might find it difficult to understand idiomatically-written Kotlin code. Gaining more familiarity with Kotlin's style can help you avoid those pitfalls.

When you write a Kotlin function, you can specify default values for function arguments, used if the caller doesn't explicitly pass those values. This feature reduces the need for overloaded functions.

For example, suppose you want to write a function that draws a square. That function might have a single required parameter, sideLength, specifying the length of each side. It might have several optional parameters, like thickness, edgeColor and so on; if the caller doesn't specify those, the function uses default values. In other languages, you might expect to write several functions:

In Kotlin, you can write a single function and specify the default values for the arguments:

fun drawSquare( sideLength: Int, thickness: Int = 2, edgeColor: Color = Color.Black ) { }KotlinSnippets.kt

Besides saving you from having to write multiple redundant functions, this feature makes your code much clearer to read. If the caller doesn't specify a value for an argument, that indicates that they're willing to use the default value. In addition, the named parameters make it much easier to see what's going on. If you look at the code and see a function call like this, you might not know what the parameters mean without checking the drawSquare() code:

By contrast, this code is self-documenting:

drawSquare(sideLength = 30, thickness = 5, edgeColor = Color.Red)KotlinSnippets.kt

Most Compose libraries use default arguments, and it's a good practice to do the same for the composable functions that you write. This practice makes your composables customizable, but still makes the default behavior simple to invoke. So, for example, you might create a simple text element like this:

Text(text = "Hello, Android!")KotlinSnippets.kt

That code has the same effect as the following, much more verbose code, in which more of the Text parameters are set explicitly:

Text( text = "Hello, Android!", color = Color.Unspecified, fontSize = TextUnit.Unspecified, letterSpacing = TextUnit.Unspecified, overflow = TextOverflow.Clip )KotlinSnippets.kt

Not only is the first code snippet much simpler and easier to read, it's also self-documenting. By specifying only the text parameter, you document that for all the other parameters, you want to use the default values. By contrast, the second snippet implies that you want to explicitly set the values for those other parameters, though the values you set happen to be the default values for the function.

Kotlin supports higher-order functions, functions that receive other functions as parameters. Compose builds upon this approach. For example, the Button composable function provides an onClick lambda parameter. The value of that parameter is a function, which the button calls when the user clicks it:

Button( // ... onClick = myClickFunction ) // ...KotlinSnippets.kt

Higher-order functions pair naturally with lambda expressions, expressions which evaluate to a function. If you only need the function once, you don't have to define it elsewhere to pass it to the higher-order function. Instead, you can just define the function right there with a lambda expression. The previous example assumes that myClickFunction() is defined elsewhere. But if you only use that function here, it's simpler to just define the function inline with a lambda expression:

Button( // ... onClick = { // do something // do something else } ) { /* ... */ }KotlinSnippets.kt

Kotlin offers a special syntax for calling higher-order functions whose last parameter is a lambda. If you want to pass a lambda expression as that parameter, you can use trailing lambda syntax. Instead of putting the lambda expression within the parentheses, you put it afterwards. This is a common situation in Compose, so you need to be familiar with how the code looks.

For example, the last parameter to all layouts, such as the Column() composable function, is content, a function which emits the child UI elements. Suppose you wanted to create a column containing three text elements, and you need to apply some formatting. This code would work, but it's very cumbersome:

Column( modifier = Modifier.padding(16.dp), content = { Text("Some text") Text("Some more text") Text("Last text") } )KotlinSnippets.kt

Because the content parameter is the last one in the function signature, and we're passing its value as a lambda expression, we can pull it out of the parentheses:

Column(modifier = Modifier.padding(16.dp)) { Text("Some text") Text("Some more text") Text("Last text") }KotlinSnippets.kt

The two examples have exactly the same meaning. The braces define the lambda expression that is passed to the content parameter.

In fact, if the only parameter you're passing is that trailing lambda—that is, if the final parameter is a lambda, and you aren't passing any other parameters—you can omit the parentheses altogether. So, for example, suppose you didn't need to pass a modifier to the Column. You could write the code like this:

Column { Text("Some text") Text("Some more text") Text("Last text") }KotlinSnippets.kt

This syntax is quite common in Compose, especially for layout elements like Column. The last parameter is a lambda expression defining the element's children, and those children are specified in braces after the function call.

Some methods and properties are only available in a certain scope. The limited scope lets you offer functionality where it's needed and avoid accidentally using that functionality where it isn't appropriate.

Consider an example used in Compose. When you call the Row layout composable, your content lambda is automatically invoked within a RowScope. This enables Row to expose functionality which is only valid within a Row. The example below demonstrates how Row has exposed a row-specific value for the align modifier:

Row { Text( text = "Hello world", // This Text is inside a RowScope so it has access to // Alignment.CenterVertically but not to // Alignment.CenterHorizontally, which would be available // in a ColumnScope. modifier = Modifier.align(Alignment.CenterVertically) ) }KotlinSnippets.kt

Some APIs accept lambdas which are called in receiver scope. Those lambdas have access to properties and functions that are defined elsewhere, based on the parameter declaration:

Box( modifier = Modifier.drawBehind { // This method accepts a lambda of type DrawScope.() -> Unit // therefore in this lambda we can access properties and functions // available from DrawScope, such as the `drawRectangle` function. drawRect( /*...*/ /* ... ) } )KotlinSnippets.kt

For more information, see function literals with receiver in the Kotlin documentation.

Kotlin supports delegated properties. These properties are called as if they were fields, but their value is determined dynamically by evaluating an expression. You can recognize these properties by their use of the by syntax:

class DelegatingClass { var name: String by nameGetterFunction() // ... }KotlinSnippets.kt

Other code can access the property with code like this:

val myDC = DelegatingClass() println("The name property is: " + myDC.name)KotlinSnippets.kt

When println() executes, nameGetterFunction() is called to return the value of the string.

These delegated properties are particularly useful when you're working with state-backed properties:

var showDialog by remember { mutableStateOf(false) } // Updating the var automatically triggers a state change showDialog = trueKotlinSnippets.kt

If you define a data class, you can easily access the data with a destructuring declaration. For example, suppose you define a Person class:

data class Person(val name: String, val age: Int)KotlinSnippets.kt

If you have an object of that type, you can access its values with code like this:

val mary = Person(name = "Mary", age = 35) // ... val (name, age) = maryKotlinSnippets.kt

You'll often see that kind of code in Compose functions:

Row { val (image, title, subtitle) = createRefs() // The `createRefs` function returns a data object; // the first three components are extracted into the // image, title, and subtitle variables. // ... }KotlinSnippets.kt

Data classes provide a lot of other useful functionality. For example, when you define a data class, the compiler automatically defines useful functions like equals() and copy(). You can find more information in the data classes documentation.

Kotlin makes it easy to declare singletons, classes which always have one and only one instance. These singletons are declared with the object keyword. Compose often makes use of such objects. For example, MaterialTheme is defined as a singleton object; the MaterialTheme.colors, shapes, and typography properties all contain the values for the current theme.

Kotlin allows creating domain-specific languages (DSLs) with type-safe builders. DSLs allow building complex hierarchical data structures in a more maintainable and readable way.

Jetpack Compose uses DSLs for some APIs such as LazyRow and LazyColumn.

@Composable fun MessageList(messages: List<Message>) { LazyColumn { // Add a single item as a header item { Text("Message List") } // Add list of messages items(messages) { message -> Message(message) } } }KotlinSnippets.kt

Kotlin guarantees type-safe builders using function literals with receiver. If we take the Canvas composable as example, it takes as a parameter a function with DrawScope as the receiver, onDraw: DrawScope.() -> Unit, allowing the block of code to call member functions defined in DrawScope.

Canvas(Modifier.size(120.dp)) { // Draw grey background, drawRect function is provided by the receiver drawRect(color = Color.Gray) // Inset content by 10 pixels on the left/right sides // and 12 by the top/bottom inset(10.0f, 12.0f) { val quadrantSize = size / 2.0f // Draw a rectangle within the inset bounds drawRect( size = quadrantSize, color = Color.Red ) rotate(45.0f) { drawRect(size = quadrantSize, color = Color.Blue) } } }KotlinSnippets.kt

Learn more about type-safe builders and DSLs in Kotlin's documentation.

Coroutines offer asynchronous programming support at the language level in Kotlin. Coroutines can suspend execution without blocking threads. A responsive UI is inherently asynchronous, and Jetpack Compose solves this by embracing coroutines at the API level instead of using callbacks.

Jetpack Compose offers APIs that make using coroutines safe within the UI layer. The rememberCoroutineScope function returns a CoroutineScope with which you can create coroutines in event handlers and call Compose suspend APIs. See the example below using the ScrollState's animateScrollTo API.

// Create a CoroutineScope that follows this composable's lifecycle val composableScope = rememberCoroutineScope() Button( // ... onClick = { // Create a new coroutine that scrolls to the top of the list // and call the ViewModel to load data composableScope.launch { scrollState.animateScrollTo(0) // This is a suspend function viewModel.loadData() } } ) { /* ... */ }KotlinSnippets.kt

Coroutines execute the block of code sequentially by default. A running coroutine that calls a suspend function suspends its execution until the suspend function returns. This is true even if the suspend function moves the execution to a different CoroutineDispatcher. In the previous example, loadData won't be executed until the suspend function animateScrollTo returns.

To execute code concurrently, new coroutines need to be created. In the example above, to parallelize scrolling to the top of the screen and loading data from viewModel, two coroutines are needed.

// Create a CoroutineScope that follows this composable's lifecycle val composableScope = rememberCoroutineScope() Button( // ... onClick = { // Scroll to the top and load data in parallel by creating a new // coroutine per independent work to do composableScope.launch { scrollState.animateScrollTo(0) } composableScope.launch { viewModel.loadData() } } ) { /* ... */ }KotlinSnippets.kt

Coroutines make it easier to combine asynchronous APIs. In the following example, we combine the pointerInput modifier with the animation APIs to animate the position of an element when the user taps on the screen.

@Composable fun MoveBoxWhereTapped() { // Creates an `Animatable` to animate Offset and `remember` it. val animatedOffset = remember { Animatable(Offset(0f, 0f), Offset.VectorConverter) } Box( // The pointerInput modifier takes a suspend block of code Modifier .fillMaxSize() .pointerInput(Unit) { // Create a new CoroutineScope to be able to create new // coroutines inside a suspend function coroutineScope { while (true) { // Wait for the user to tap on the screen and animate // in the same block awaitPointerEventScope { val offset = awaitFirstDown().position // Launch a new coroutine to asynchronously animate to // where the user tapped on the screen launch { // Animate to the pressed position animatedOffset.animateTo(offset) } } } } } ) { Text("Tap anywhere", Modifier.align(Alignment.Center)) Box( Modifier .offset { // Use the animated offset as the offset of this Box IntOffset( animatedOffset.value.x.roundToInt(), animatedOffset.value.y.roundToInt() ) } .size(40.dp) .background(Color(0xff3c1361), CircleShape) ) }KotlinSnippets.kt

To learn more about Coroutines, check out the Kotlin coroutines on Android guide.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Animation Preview Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/animation-preview

**Contents:**
- Animation Preview Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Android Studio allows you to inspect animations from Animation Preview. If an animation is described in a composable preview, you can inspect the exact value of each animated value at a given time, pause the animation, loop it, fast-forward it, or slow it, to help you debug the animation throughout its transitions:

You can also use Animation Preview to graph visualize animation curves, which is useful for making sure that the animation values are choreographed properly:

Animation Preview automatically detects inspectable animations, which are indicated by the Start Animation Preview icon .

If you have multiple animations, you can use Animation Preview to inspect and coordinate them all at once. You can also freeze a specific animation.

Use pickers to set non-enum or boolean states to debug your Compose animation using precise inputs. For all supported Compose Animation APIs, you can play, pause, scrub, control speed, and coordinate.

Animation Preview currently supports the updateTransition, AnimatedVisibility, animate*AsState, CrossFade, rememberInfiniteTransition, and AnimatedContent APIs. To access the latest features, use Animation Preview with Android Studio Otter 3 Feature Drop and compose.animation 1.10.0 and higher.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Composition tracing Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/tracing

**Contents:**
- Composition tracing Stay organized with collections Save and categorize content based on your preferences.
- Composition tracing setup
- Take a system trace
- Caveats
  - APK size overhead
  - Accurate timing
- Capture a trace from terminal
  - Add dependencies
  - Generate a record command
  - Capture a trace

Traces are often the best source of information when first looking into a performance issue. They allow you to form a hypothesis of what the issue is and where to start looking.

There are two levels of tracing supported on Android: system tracing and method tracing.

Because system tracing only tracks areas specifically marked for tracing, it has low overhead and doesn't affect the performance of your app greatly. System tracing is great for seeing how long particular sections of your code are taking to run.

Method tracing tracks every function call in your app. This is very expensive and so it greatly affects the performance of your app, but it gives you a total picture of what is happening, what functions are being called, and how often they are being called.

By default, system traces do not include individual composable functions. They are available in method traces.

Composition tracing shows composable functions inside system traces. The feature gives you the low intrusiveness from system tracing, with method tracing levels of detail in composition.

To try out the recomposition tracing in your project, you need to update to at least the following versions:

The device or emulator you run your trace on must also be at minimum API level 30.

Additionally, you need to add a new dependency on Compose Runtime Tracing:

If you are using the Compose BOM, you don't have to specify the version:

With this dependency, when you take a system trace that includes recomposition, you can see the composable functions automatically.

To take a system trace and see the new recomposition tracing in action, follow these steps:

Navigate your app to the UI you want to trace and then select System Trace and Record

Use your app to cause recomposition and stop recording. Once the trace has been processed and appears, you should now be able to see the composables in the recomposition trace. You can use the keyboard and mouse to zoom and pan around the trace; if you are unfamiliar with navigating a trace, see the Record traces documentation.

Double-clicking on a composable in the chart takes you to its source code.

You can also see composables in the Flame Chart along with the file and line number:

While we aimed to minimize the overhead of the feature as much as possible, there is an APK size increase for Compose apps coming from tracing strings embedded in the APK by the Compose compiler. This size increase can be relatively small if your app isn't using much Compose or larger for full Compose apps. These tracing strings are additionally unobfuscated so they can appear in tracing tools, as shown earlier. The Compose compiler injects them into all apps, starting with version 1.3.0.

The tracing strings can be removed in your production build by adding the following Proguard rule:

These functions may change in the future, but any changes will be mentioned in the Compose release notes.

Note that keeping the functions in, while incurring some APK size cost, ensures that the APK being profiled is the same one that the app users run.

For accurate profiling, like with any performance testing, you need to make the app profileable and non-debuggable as per Profileable applications.

It is possible to capture a composition trace from terminal. To do so, you have to perform the steps that Android Studio normally does for you automatically.

First, add the additional dependencies to your app.

Manually add the track_event data source section as per following example:

Enable tracing in the app by issuing a broadcast.

Start your recording command you created previously.

adb pull <location> the trace from the device (location specified in the record command).

You can measure performance with Jetpack Macrobenchmark, which provides traces as results. To enable composition tracing with macrobenchmarks, you need to:

Add these additional dependencies to the Macrobenchmark test module:

Add androidx.benchmark.fullTracing.enable=true instrumentation argument before running benchmarks. Check Macrobenchmark instrumentation arguments for more information about Macrobenchmark instrumentation arguments.

We would love to hear your feedback on this feature, any bugs you find with it, and any requests you have. You can send us feedback using the issue tracker.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
implementation("androidx.compose.runtime:runtime-tracing:1.10.0")
```

Example 2 (json):
```json
val composeBom = platform("androidx.compose:compose-bom:2025.12.00")
implementation(composeBom)
// ...

// dependency without a version
implementation("androidx.compose.runtime:runtime-tracing")
```

Example 3 (csharp):
```csharp
-assumenosideeffects public class androidx.compose.runtime.ComposerKt {

   boolean isTraceInProgress();

   void traceEventStart(int,int,int,java.lang.String);

   void traceEventStart(int,java.lang.String);

   void traceEventEnd();

}
```

Example 4 (json):
```json
implementation("androidx.tracing:tracing-perfetto:1.0.0")
implementation("androidx.tracing:tracing-perfetto-binary:1.0.0")
```

---

## Iterative code development Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/iterative-development

**Contents:**
- Iterative code development Stay organized with collections Save and categorize content based on your preferences.
- Live Edit
  - Get started with Live Edit
    - Set up your new project
    - Enable Live Edit
    - Make and review changes
  - Troubleshoot Live Edit
  - Limitations of Live Edit
  - Frequently asked questions about Live Edit
  - Iterative code development with Compose

As a mobile developer, you're often developing your app's UI step by step rather than developing everything at once. Android Studio embraces this approach with Jetpack Compose by providing tools that don't require a full build to inspect, modify values, and verify the final result.

Live Edit is a feature that lets you update composables in emulators and physical devices in real time. This functionality minimizes context switches between writing and building your app, letting you focus on writing code longer without interruption.

Live Edit has three modes:

Live Edit is focused on UI- and UX-related code changes. Live Edit doesn't support changes such as method signature updates, adding new methods, or class hierarchy changes. For more information, see the list of Limitations of Live Edit.

This feature is not a replacement for building and running your app or for Apply Changes. Instead, it's designed to optimize your workflow as you build, deploy, and iterate to develop Compose UI.

The best practice workflow is as follows:

To get started, follow these steps to create an empty Compose Activity, enable Live Edit for your project, and make changes with Live Edit.

Before you begin, make sure that you have Android Studio Giraffe or higher installed and that the API level of your physical device or emulator is at least 30.

Open Android Studio and select New Project in the Welcome to Android Studio dialog. If you already have a project open, you can create a new one by navigating to File > New > New Project.

Choose the Empty Compose Activity template for Phone and Tablet, and then click Next.

Complete the New Project dialog with the required information: name, package name, save location, minimum SDK, and build configuration language.

Navigate to the settings to enable Live Edit.

Select the Live Edit option and the mode you want to run from the settings.

In manual mode, your code changes are pushed every time you press Control+' (Command+' on macOS). In manual mode on save, your code changes are applied every time you manually save, using Control+S (Command+S on macOS). In automatic mode, your code changes are applied in your device or emulator as you make your changes.

In the editor, open the MainActivity file, which is the entry point for your app.

Click Run to deploy your app.

After you turn on Live Edit, the Up-to-date green checkmark appears in the top right of the Running Devices tool window:

As you make supported changes in the editor, the virtual or physical test device updates automatically.

For example, edit the existing Greeting method in MainActivity to the following:

@Composable fun Greeting(name: String) { Text( text = "Hello $name!", Modifier .padding(80.dp) // Outer padding; outside background .background(color = Color.Cyan) // Solid element background color .padding(16.dp) // Inner padding; inside background, around text) ) }IterativeDevelopmentSnippets.kt

Your changes appear instantaneously on the test device, as shown in figure 4.

If you don't see your edits on the test device, Android Studio might have failed to update your edits. Check whether the Live Edit indicator says Out Of Date as shown in figure 5, which indicates a compilation error. For information about the error and suggestions for how to resolve it, click the indicator.

The following is a list of current limitations.

[Only applies to Android Studio Giraffe and higher] Live Edit requires Compose Runtime 1.3.0 or higher. If your project uses a lower version of Compose, Live Edit is disabled.

[Only applies to Android Studio Giraffe and higher] Live Edit requires AGP 8.1.0-alpha05 or higher. If your project uses a lower version of AGP, Live Edit is disabled.

Live Edit requires a physical device or emulator that is running API level 30 or higher.

Live Edit only supports editing a function body, which means that you can't change the function name or the signature, add or remove a function, or change non-function fields.

Live Edit resets the app's state the first time you change a Compose function in a file. This only happens after the first code change—the app state isn't reset by subsequent code changes you make to Compose functions in that file.

Live Edit-modified classes might incur some performance penalty. Run your app and use a clean release build if you are evaluating its performance.

You must perform a full run for the debugger to operate on classes that you have modified with Live Edit.

A running app might crash when you edit it with Live Edit. If this happens, you can redeploy the app with the Run button.

Live Edit doesn't perform any bytecode manipulation that's defined in your project's build file—for example, bytecode manipulation that would be applied when the project is built using the options in the Build menu or by clicking the Build or Run buttons.

Non-Composable functions are updated live on the device or emulator, and a full recomposition is triggered. The full recomposition might not invoke the updated function. For non-composable functions, you must trigger the newly updated functions or run the app again.

Live Edit doesn't resume on app restarts. You must run the app again.

Live Edit only supports debuggable processes.

Live Edit does not support projects that use custom values for moduleName under kotlinOptions in build configuration.

Live Edit doesn't work with multi-deploy deployments. This means that you can't deploy to one device, and then to another. Live Edit is only active on the last set of devices the app was deployed to.

Live Edit works with multidevice deployments (deployments to multiple devices that were created through Select multiple devices in the target device dropdown). However, it's not officially supported and there might be issues. If you experience issues, please report them.

Apply Changes/Apply Code Changes are not compatible with Live Edit and require the running app to be restarted.

Live Edit currently does not support Android Automotive projects.

What is the current status of Live Edit?

Live Edit is available in Android Studio Giraffe. To turn it on, navigate to File > Settings > Editor > Live Edit (Android Studio > Settings > Editor > Live Edit on macOS).

When should I use Live Edit?

Use Live Edit when you want to quickly see the effect of updates to UX elements (such as modifier updates and animations) on the overall app experience.

When should I avoid using Live Edit?

Live Edit is focused on UI- and UX-related code changes. It doesn't support changes such as method signature updates, adding new methods, or class hierarchy changes. For more information, see Limitations of Live Edit.

When should I use Compose Preview?

Use Compose Preview when you're developing individual composables. Preview visualizes Compose elements and automatically refreshes to display the effect of code changes. Preview also supports viewing UI elements under different configurations and states, such as dark theme, locales, and font scale.

Live Edit and Hot Reload for Compose Multiplatform are features that can save you time and increase your productivity as you develop with Compose. However, they serve the needs of different types of development:

Live Edit supports iterative development with Jetpack Compose for Android applications. It lets you update composables in emulators and physical devices in real time. This functionality minimizes context switches between writing and building your app, letting you focus on writing code for longer without interruption.

Compose Hot Reload serves the same need, but supports desktop applications built with Compose Multiplatform. It enables you to make changes to your UI code in a Compose Multiplatform application and see the results in real time by intelligently reloading your code whenever it is changed.

While these two features share many technologies within the Compose engine and support many similar use cases, they do not have the same capabilities because they apply to different types of Compose development.

If you are developing an Android app, you should use Live Edit to accelerate your development process. If you are developing a desktop application using Compose Multiplatform, you should use Compose Hot Reload.

Android Studio can update in real time some constant literals used in composables within previews, emulator, and physical device. Here are some supported types:

You can view constant literals that trigger real time updates without the compilation step by enabling literal decorations through the Live Edit of literals UI indicator:

Apply Changes lets you update code and resources without having to redeploy your app to an emulator or physical device (with some limitations).

Whenever you add, modify, or delete composables, you can update your app without having to redeploy it by clicking on the Apply Code Changes button:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Stack traces in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/tooling/stacktraces

**Contents:**
- Stack traces in Compose Stay organized with collections Save and categorize content based on your preferences.
- Add source info to stack traces
- Known limitations
  - Source information stack traces
  - Group keys stack traces
- Stack trace collection crashes

Jetpack Compose executes your code in multiple different phases, which causes some parts of the @Composable function to be executed separately from each other. Crashes in these phases can result in stack traces that are hard to decipher, making it difficult to pinpoint the exact function or line of code that caused the crash.

To improve stack trace readability, an opt-in API provides richer crash location details, including composable names and locations, enabling you to:

The Compose runtime can detect the crash location in composition and reconstruct a stack trace based on your @Composable hierarchy. The stack trace is appended for crashes in:

To enable this feature, add the following lines to the application entry point:

// Enable stack traces at application level: onCreate class SampleStackTracesEnabledApp : Application() { override fun onCreate() { super.onCreate() // Enable Compose stack traces for minified builds only. Composer.setDiagnosticStackTraceMode(ComposeStackTraceMode.Auto) // Alternatively: // Enable verbose Compose stack traces for local debugging Composer.setDiagnosticStackTraceMode(ComposeStackTraceMode.SourceInformation) } }Stacktraces.kt

Ideally, perform this configuration before creating any compositions to verify that the stack trace information is collected correctly.

There are four options for the ComposeStackTraceMode:

When using the SourceInformation option, the stack trace then appears as a DiagnosticComposeException in the suppressed exception list:

There are a few known issues with stack trace frames:

Missing line numbers (<unknown line>) in the first stack frame for crashes in composition. Since source information introspection is happening after a crash, the slot table data can be incomplete and drop line number. ReusableComposeNode and remember don't produce source information, so you will see <unknown line> in the stack frames for those functions.

GroupKeys based stack traces can only point to the first line of the @Composable function by design. They also don't contain any data for any functions that don't produce a group (such as inline or non-Unit returning functions)

If the stack trace collection crashes for any reason, that exception is appended as a suppressed exception instead of DiagnosticComposeException.

Report any suppressed crashes or stack trace inconsistencies to the Compose Runtime component.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (jsx):
```jsx
java.lang.IllegalStateException: Test layout error
    at <original trace>
Suppressed: androidx.compose.runtime.DiagnosticComposeException:
Composition stack when thrown:
    at ReusableComposeNode(Composables.kt:<unknown line>)
    at Layout(Layout.kt:79)
    at <lambda>(TempErrorsTest.kt:164)
    at <lambda>(BoxWithConstraints.kt:66)
    at ReusableContentHost(Composables.kt:164)
    at <lambda>(SubcomposeLayout.kt:514)
    at SubcomposeLayout(SubcomposeLayout.kt:114)
    at SubcomposeLayout(SubcomposeLayout.kt:80)
    at BoxWithConstraints(BoxWithConstraints.kt:64)
    at SubcomposeLayoutErrorComposable(TempErrorsTest.kt:164)
    at <lambda>(TempErrorsTest.kt:86)
    at Content(ComposeView.android.kt:430)
    at <lambda>(ComposeView.android.kt:249)
    at CompositionLocalProvider(CompositionLocal.kt:364)
    at ProvideCommonCompositionLocals(CompositionLocals.kt:193)
    at <lambda>(AndroidCompositionLocals.android.kt:113)
    at CompositionLocalProvider(CompositionLocal.kt:364)
    at ProvideAndroidCompositionLocals(AndroidCompositionLocals.android.kt:102)
    at <lambda>(Wrapper.android.kt:141)
    at CompositionLocalProvider(CompositionLocal.kt:384)
    at <lambda>(Wrapper.android.kt:140)
```

---
