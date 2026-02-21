# Jetpack-Compose - Getting Started

**Pages:** 5

---

## Get started with Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/documentation

**Contents:**
- Get started with Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.
- Foundation
- Adaptive UI
- Development environment
- Design
- Adopting Compose
- Additional resources
- Recommended for you

Jetpack Compose is the modern toolkit for building Android UI, simplifying the development of apps that adapt to any display size.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-11-05 UTC.

---

## Use a Bill of Materials Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/bom

**Contents:**
- Use a Bill of Materials Stay organized with collections Save and categorize content based on your preferences.
  - Kotlin
  - Groovy
- Why isn't the Compose Compiler library included in the BOM?
- How do I use a different library version than what's designated in the BOM?
  - Kotlin
  - Groovy
- Does the BOM automatically add all the Compose libraries to my app?
- Why is the BOM the recommended way to manage Compose library versions?
- Am I forced to use the BOM?

The Compose Bill of Materials (BOM) lets you manage all of your Compose library versions by specifying only the BOM's version. The BOM itself has links to the stable versions of the different Compose libraries, in such a way that they work well together. When using the BOM in your app, you don't need to add any version to the Compose library dependencies themselves. When you update the BOM version, all the libraries that you're using are automatically updated to their new versions.

To find out which Compose library versions are mapped to a specific BOM version, check out the BOM to library version mapping.

The Compose Kotlin compiler extension (androidx.compose.compiler) is not linked to the Compose library versions. Instead, it is linked to versions of the Kotlin compiler plugin and released in a separate cadence from the rest of Compose.

As of Kotlin 2.0, the Compose appcompiler is managed alongside the Kotlin compiler and uses the same version as the Kotlin compiler. See Compose Compiler Gradle plugin for configuration details.

In versions lower than Kotlin 2.0, consult the Compose to Kotlin Compatibility Map to identify a compiler version that's compatible with your Kotlin version and see Compose Compiler for guidance on configuring it.

In the build.gradle dependencies section, keep the import of the BOM platform. On the library dependency import, specify the overriding version. For example, here's how to declare dependencies if you want to use a newer version of the animation library, no matter what version is designated in the BOM:

No. To actually add and use Compose libraries in your app, you must declare each library as a separate dependency line in your module (app-level) Gradle file (usually app/build.gradle).

Using the BOM helps verify that the versions of any Compose libraries in your app are compatible, but the BOM doesn't actually add those Compose libraries to your app.

Going forward, Compose libraries will be versioned independently, which means version numbers will start to be incremented at their own pace. The latest stable releases of each library are tested together. However, finding the latest stable versions of each library can be difficult, and the BOM helps you to automatically use these latest versions.

No. You can still choose to add each dependency version manually. However, we recommend using the BOM as it will make it easier to use all of the latest stable versions at the same time.

Yes. You can include the BOM itself in the version catalog, and omit the other Compose library versions:

Don't forget to import the BOM in your module's build.gradle:

There are three available Compose BOMs. Each BOM is a point-in-time snapshot of the latest-available versions of Compose libraries.

The Alpha and Beta versions of the BOM are specified by adding -alpha and -beta to the BOM artifact name. The stable version has no suffix.

You can file issues on our issue tracker.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
[libraries]
androidx-compose-bom = { group = "androidx.compose", name = "compose-bom", version.ref = "androidxComposeBom" }
androidx-compose-foundation = { group = "androidx.compose.foundation", name = "foundation" }
```

---

## BOM to library version mapping Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/bom/bom-mapping

**Contents:**
- BOM to library version mapping Stay organized with collections Save and categorize content based on your preferences.

The URL on the Group Release Note column is updated depending on which BOM version is selected from the drop-down.

Except as otherwise noted, the content of this page is licensed under the Creative Commons Attribution 4.0 License, and code samples are licensed under the Apache 2.0 License. For details, see the Google Developers Site Policies. Java is a registered trademark of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Quick start Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/setup

**Contents:**
- Quick start Stay organized with collections Save and categorize content based on your preferences.
- Create a new app with support for Compose
- Set up Compose for an existing app
  - Groovy
  - Kotlin
  - Groovy
  - Kotlin
- Try Jetpack Compose sample apps
- Recommended for you

For the best experience developing with Compose, download and install Android Studio. It includes many smart editor features, such as new project templates and the ability to immediately preview your Compose UI and animations.

Follow these instructions to create a new Compose app project, set up Compose for an existing app project, or import a sample app written in Compose.

If you want to start a new project that includes support for Compose by default, Android Studio includes various project templates to help you get started. To create a new project that has Compose setup correctly, proceed as follows:

Now you're ready to start developing an app using Jetpack Compose. To help you get started and learn about what you can do with the toolkit, try the Jetpack Compose tutorial.

First, configure the Compose compiler using the Compose Compiler Gradle plugin.

Then, add the following definition to your app's build.gradle file:

Setting the compose flag to true inside the Android BuildFeatures block enables Compose functionality in Android Studio.

Finally, add the Compose BOM and the subset of Compose library dependencies you need to your dependencies from the following block:

The fastest way to experiment with the capabilities of Jetpack Compose is by trying Jetpack Compose sample apps hosted on GitHub. To import a sample app project from Android Studio, proceed as follows:

Android Studio downloads the sample app to the path you specified and opens the project. You can then inspect MainActivity.kt in each of the examples to see Jetpack Compose APIs such as crossfade animation, custom components, using typography, and displaying light and dark colors in the in-IDE preview.

To use Jetpack Compose for Wear OS, see Set up Jetpack Compose on Wear OS.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
android {
    buildFeatures {
        compose true
    }
}
```

Example 2 (unknown):
```unknown
android {
    buildFeatures {
        compose = true
    }
}
```

Example 3 (python):
```python
dependencies {

    def composeBom = platform('androidx.compose:compose-bom:2025.12.00')
    implementation composeBom
    androidTestImplementation composeBom

    // Choose one of the following:
    // Material Design 3
    implementation 'androidx.compose.material3:material3'
    // or skip Material Design and build directly on top of foundational components
    implementation 'androidx.compose.foundation:foundation'
    // or only import the main APIs for the underlying toolkit systems,
    // such as input and measurement/layout
    implementation 'androidx.compose.ui:ui'

    // Android Studio Preview support
    implementation 'androidx.compose.ui:ui-tooling-preview'
    debugImplementation 'androidx.compose.ui:ui-tooling'

    // UI Tests
    androidTestImplementation 'androidx.compose.ui:ui-test-junit4'
    debugImplementation 'androidx.compose.ui:ui-test-manifest'

    // Optional - Add window size utils
    implementation 'androidx.compose.material3.adaptive:adaptive'

    // Optional - Integration with activities
    implementation 'androidx.activity:activity-compose:1.11.0'
    // Optional - Integration with ViewModels
    implementation 'androidx.lifecycle:lifecycle-viewmodel-compose:2.8.5'
    // Optional - Integration with LiveData
    implementation 'androidx.compose.runtime:runtime-livedata'
    // Optional - Integration with RxJava
    implementation 'androidx.compose.runtime:runtime-rxjava2'

}
```

Example 4 (python):
```python
dependencies {

    val composeBom = platform("androidx.compose:compose-bom:2025.12.00")
    implementation(composeBom)
    androidTestImplementation(composeBom)

    // Choose one of the following:
    // Material Design 3
    implementation("androidx.compose.material3:material3")
    // or skip Material Design and build directly on top of foundational components
    implementation("androidx.compose.foundation:foundation")
    // or only import the main APIs for the underlying toolkit systems,
    // such as input and measurement/layout
    implementation("androidx.compose.ui:ui")

    // Android Studio Preview support
    implementation("androidx.compose.ui:ui-tooling-preview")
    debugImplementation("androidx.compose.ui:ui-tooling")

    // UI Tests
    androidTestImplementation("androidx.compose.ui:ui-test-junit4")
    debugImplementation("androidx.compose.ui:ui-test-manifest")

    // Optional - Add window size utils
    implementation("androidx.compose.material3.adaptive:adaptive")

    // Optional - Integration with activities
    implementation("androidx.activity:activity-compose:1.11.0")
    // Optional - Integration with ViewModels
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.5")
    // Optional - Integration with LiveData
    implementation("androidx.compose.runtime:runtime-livedata")
    // Optional - Integration with RxJava
    implementation("androidx.compose.runtime:runtime-rxjava2")

}
```

---

## Jetpack Compose Tutorial

**URL:** https://developer.android.com/develop/ui/compose/tutorial

**Contents:**
- Jetpack Compose Tutorial
- Lesson 1: Composable functions
    - Add a text element
    - Define a composable function
    - Preview your function in Android Studio
- Lesson 2: Layouts
    - Add multiple texts
    - Using a Column
    - Add an image element
    - Configure your layout

Jetpack Compose is a modern toolkit for building native Android UI. Jetpack Compose simplifies and accelerates UI development on Android with less code, powerful tools, and intuitive Kotlin APIs.

In this tutorial, you'll build a simple UI component with declarative functions. You won't be editing any XML layouts or using the Layout Editor. Instead, you will call composable functions to define what elements you want, and the Compose compiler will do the rest.

Jetpack Compose is built around composable functions. These functions let you define your app's UI programmatically by describing how it should look and providing data dependencies, rather than focusing on the process of the UI's construction (initializing an element, attaching it to a parent, etc.). To create a composable function, just add the @Composable annotation to the function name.

To begin, download the most recent version of Android Studio, and create an app by selecting New Project, and under the Phone and Tablet category, select Empty Activity. Name your app ComposeTutorial and click Finish. The default template already contains some Compose elements, but in this tutorial you will build it up step by step.

First, display a “Hello world!” text by adding a text element inside the onCreate method. You do this by defining a content block, and calling the Text composable function. The setContent block defines the activity's layout where composable functions are called. Composable functions can only be called from other composable functions.

Jetpack Compose uses a Kotlin compiler plugin to transform these composable functions into the app's UI elements. For example, the Text composable function that is defined by the Compose UI library displays a text label on the screen.

To make a function composable, add the @Composable annotation. To try this out, define a MessageCard function which is passed a name, and uses it to configure the text element.

The @Preview annotation lets you preview your composable functions within Android Studio without having to build and install the app to an Android device or emulator. The annotation must be used on a composable function that does not take in parameters. For this reason, you can't preview the MessageCard function directly. Instead, make a second function named PreviewMessageCard, which calls MessageCard with an appropriate parameter. Add the @Preview annotation before @Composable.

Rebuild your project. The app itself doesn't change, since the new PreviewMessageCard function isn't called anywhere, but Android Studio adds a preview window which you can expand by clicking on the split (design/code) view. This window shows a preview of the UI elements created by composable functions marked with the @Preview annotation. To update the previews at any time, click the refresh button at the top of the preview window.

UI elements are hierarchical, with elements contained in other elements. In Compose, you build a UI hierarchy by calling composable functions from other composable functions.

So far you’ve built your first composable function and preview! To discover more Jetpack Compose capabilities, you’re going to build a simple messaging screen containing a list of messages that can be expanded with some animations.

Start by making the message composable richer by displaying the name of its author and a message content. You need to first change the composable parameter to accept a Message object instead of a String, and add another Text composable inside the MessageCard composable. Make sure to update the preview as well.

This code creates two text elements inside the content view. However, since you haven't provided any information about how to arrange them, the text elements are drawn on top of each other, making the text unreadable.

The Column function lets you arrange elements vertically. Add Column to the MessageCard function. You can use Row to arrange items horizontally and Box to stack elements.

Enrich your message card by adding a profile picture of the sender. Use the Resource Manager to import an image from your photo library or use this one. Add a Row composable to have a well structured design and an Image composable inside it.

Your message layout has the right structure but its elements aren't well spaced and the image is too big! To decorate or configure a composable, Compose uses modifiers. They allow you to change the composable's size, layout, appearance or add high-level interactions, such as making an element clickable. You can chain them to create richer composables. You'll use some of them to improve the layout.

Compose is built to support Material Design principles. Many of its UI elements implement Material Design out of the box. In this lesson, you'll style your app with Material Design widgets.

Your message design now has a layout, but it doesn't look great yet.

Jetpack Compose provides an implementation of Material Design 3 and its UI elements out of the box. You'll improve the appearance of our MessageCard composable using Material Design styling.

To start, wrap the MessageCard function with the Material theme created in your project, ComposeTutorialTheme, as well as a Surface. Do it both in the @Preview and in the setContent function. Doing so will allow your composables to inherit styles as defined in your app's theme ensuring consistency across your app.

Material Design is built around three pillars: Color, Typography, and Shape. You will add them one by one.

Note: the Empty Compose Activity template generates a default theme for your project that allows you to customize MaterialTheme. If you named your project anything different from ComposeTutorial, you can find your custom theme in the Theme.kt file in the ui.theme subpackage.

Use MaterialTheme.colorScheme to style with colors from the wrapped theme. You can use these values from the theme anywhere a color is needed. This example uses dynamic theming colors (defined by device preferences). You can set dynamicColor to false in the MaterialTheme.kt file to change this.

Style the title and add a border to the image.

Material Typography styles are available in the MaterialTheme, just add them to the Text composables.

With Shapeyou can add the final touches. First, wrap the message body text around a Surface composable. Doing so allows customizing the message body's shape and elevation. Padding is also added to the message for a better layout.

Dark theme (or night mode) can be enabled to avoid a bright display especially at night, or simply to save the device battery. Thanks to the Material Design support, Jetpack Compose can handle the dark theme by default. Having used Material Design colors, text and backgrounds will automatically adapt to the dark background.

You can create multiple previews in your file as separate functions, or add multiple annotations to the same function.

Add a new preview annotation and enable night mode.

Color choices for the light and dark themes are defined in the IDE-generated Theme.kt file.

So far, you've created a message UI element that displays an image and two texts with different styles, and it looks good both in light and dark themes!

Lists and animations are everywhere in apps. In this lesson, you will learn how Compose makes it easy to create lists and fun to add animations.

A chat with one message feels a bit lonely, so we are going to change the conversation to have more than one message. You'll need to create a Conversation function that will show multiple messages. For this use case, use Compose’s LazyColumn and LazyRow. These composables render only the elements that are visible on screen, so they are designed to be very efficient for long lists.

In this code snippet, you can see that LazyColumn has an items child. It takes a List as a parameter and its lambda receives a parameter we’ve named message (we could have named it whatever we want) which is an instance of Message. In short, this lambda is called for each item of the provided List. Copy the sample dataset into your project to help bootstrap the conversation quickly.

The conversation is getting more interesting. It's time to play with animations! You will add the ability to expand a message to show a longer one, animating both the content size and the background color. To store this local UI state, you need to keep track of whether a message has been expanded or not. To keep track of this state change, you have to use the functions remember and mutableStateOf.

Composable functions can store local state in memory by using remember, and track changes to the value passed to mutableStateOf. Composables (and their children) using this state will get redrawn automatically when the value is updated. This is called recomposition.

By using Compose’s state APIs like remember and mutableStateOf, any changes to state automatically update the UI.

Note: You need to add the following imports to correctly use Kotlin's delegated property syntax (the by keyword). Alt+Enter or Option+Enter adds them for you. import androidx.compose.runtime.getValue import androidx.compose.runtime.setValue

Now you can change the background of the message content based on isExpanded when we click on a message. You will use the clickable modifier to handle click events on the composable. Instead of just toggling the background color of the Surface, you will animate the background color by gradually modifying its value from MaterialTheme.colorScheme.surface to MaterialTheme.colorScheme.primary and vice versa. To do so, you will use the animateColorAsState function. Lastly, you will use the animateContentSize modifier to animate the message container size smoothly:

Congratulations, you’ve finished the Compose tutorial! You’ve built a simple chat screen efficiently showing a list of expandable & animated messages containing an image and texts, designed using Material Design principles with a dark theme included and previews—all in under 100 lines of code!

Here’s what you've learned so far:

If you want to dig deeper on some of these steps, explore the resources below.

---
