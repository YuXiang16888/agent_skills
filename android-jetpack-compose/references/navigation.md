# Jetpack-Compose - Navigation

**Pages:** 1

---

## Navigation with Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/navigation

**Contents:**
- Navigation with Compose Stay organized with collections Save and categorize content based on your preferences.
- Setup
  - Groovy
  - Kotlin
- Get started
- Create a NavController
- Create a NavHost
- Navigate to a composable
- Navigate with arguments
  - Retrieve complex data when navigating

The Navigation component provides support for Jetpack Compose applications. You can navigate between composables while taking advantage of the Navigation component's infrastructure and features.

For the latest prerelease navigation library built specifically for Compose, see the Navigation 3 documentation.

To support Compose, use the following dependency in your app module's build.gradle file:

When implementing navigation in an app, implement a navigation host, graph, and controller. For more information, see the Navigation overview.

For information on how to create a NavController in Compose, see the Compose section of Create a navigation controller.

For information on how to create a NavHost in Compose, see the Compose section of Design your navigation graph.

For information on navigating to a Composable, see Navigate to a destination in the architecture documentation.

For information on passing arguments between composable destinations, see the Compose section of Design your navigation graph.

It is strongly advised not to pass around complex data objects when navigating, but instead pass the minimum necessary information, such as a unique identifier or other form of ID, as arguments when performing navigation actions:

Complex objects should be stored as data in a single source of truth, such as the data layer. Once you land on your destination after navigating, you can then load the required information from the single source of truth by using the passed ID. To retrieve the arguments in your ViewModel that's responsible for accessing the data layer, use the SavedStateHandle of the ViewModel:

This approach helps prevent data loss during configuration changes and any inconsistencies when the object in question is being updated or mutated.

For a more in depth explanation on why you should avoid passing complex data as arguments, as well as a list of supported argument types, see Pass data between destinations.

Navigation Compose supports deep links that can be defined as part of the composable() function as well. Its deepLinks parameter accepts a list of NavDeepLink objects which can be created using the navDeepLink() method:

These deep links let you associate a specific URL, action or mime type with a composable. By default, these deep links are not exposed to external apps. To make these deep links externally available you must add the appropriate <intent-filter> elements to your app's manifest.xml file. To enable the deep link in the preceding example, you should add the following inside of the <activity> element of the manifest:

Navigation automatically deep links into that composable when the deep link is triggered by another app.

These same deep links can also be used to build a PendingIntent with the appropriate deep link from a composable:

You can then use this deepLinkPendingIntent like any other PendingIntent to open your app at the deep link destination.

For information on how to create nested navigation graphs, see Nested graphs.

The NavigationSuiteScaffold displays the appropriate top-level navigation UI for your app based on the current WindowSizeClass. For compact screens, the scaffold shows a bottom navigation bar; for medium and expanded screens, a navigation rail.

NavigationSuiteScaffold handles primary navigation; however, adaptive layouts often involve other specialized composables. For the list-detail and supporting pane canonical layouts, which are common in adaptive designs, use ListDetailPaneScaffold and SupportingPaneScaffold, respectively. For more information, see Build adaptive layouts.

If you want to use the Navigation component with Compose, you have two options:

Therefore, the recommendation for mixed Compose and Views apps is to use the Fragment-based Navigation component. Fragments will then hold View-based screens, Compose screens, and screens that use both Views and Compose. Once each Fragment's contents are in Compose, the next step is to tie all of those screens together with Navigation Compose and remove all of the Fragments.

In order to change destinations inside Compose code, you expose events that can be passed to and triggered by any composable in the hierarchy:

In your fragment, you make the bridge between Compose and the fragment-based Navigation component by finding the NavController and navigating to the destination:

Alternatively, you can pass the NavController down your Compose hierarchy. However, exposing functions is much more reusable and testable.

Decouple the navigation code from your composable destinations to enable testing each composable in isolation, separate from the NavHost composable.

This means that you shouldn't pass the navController directly into any composable and instead pass navigation callbacks as parameters. This allows all your composables to be individually testable, as they don't require an instance of navController in tests.

The level of indirection provided by the composable lambda is what lets you separate your Navigation code from the composable itself. This works in two directions:

For example, a ProfileScreen composable that takes in a userId as input and allows users to navigate to a friend's profile page might have the signature of:

This way, the ProfileScreen composable works independently from Navigation, allowing it to be tested independently. The composable lambda would encapsulate the minimal logic needed to bridge the gap between the Navigation APIs and your composable:

It is recommended to write tests that cover your app navigation requirements by testing the NavHost, navigation actions passed to your composables as well as your individual screen composables.

To begin testing your NavHost , add the following navigation-testing dependency:

Wrap your app's NavHost in a composable which accepts a NavHostController as a parameter.

Now you can test AppNavHost and all the navigation logic defined inside NavHost by passing an instance of the navigation testing artifact TestNavHostController. A UI test that verifies the start destination of your app and NavHost would look like this:

You can test your navigation implementation in multiple ways, by performing clicks on the UI elements and then either verifying the displayed destination or by comparing the expected route against the current route.

As you want to test your concrete app's implementation, clicks on the UI are preferable. To learn how to test this alongside individual composable functions in isolation, make sure to check out the Testing in Jetpack Compose codelab.

You also can use the navController to check your assertions by comparing the current route to the expected one, using navController's currentBackStackEntry:

For more guidance on Compose testing basics, see Testing your Compose layout and the Testing in Jetpack Compose codelab. To learn more about advanced testing of navigation code, visit the Test Navigation guide.

To learn more about Jetpack Navigation, see Get started with the Navigation component or take the Jetpack Compose Navigation codelab.

To learn how to design your app's navigation so it adapts to different screen sizes, orientations, and form factors, see Navigation for responsive UIs.

To learn about a more advanced Compose navigation implementation in a modularized app, including concepts like nested graphs and bottom navigation bar integration, take a look at the Now in Android app on GitHub.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (typescript):
```typescript
// Pass only the user ID when navigating to a new destination as argument
navController.navigate(Profile(id = "user1234"))
```

Example 2 (php):
```php
class UserViewModel(
    savedStateHandle: SavedStateHandle,
    private val userInfoRepository: UserInfoRepository
) : ViewModel() {

    private val profile = savedStateHandle.toRoute<Profile>()

    // Fetch the relevant user information from the data layer,
    // ie. userInfoRepository, based on the passed userId argument
    private val userInfo: Flow<UserInfo> = userInfoRepository.getUserInfo(profile.id)

// …

}
```

Example 3 (php):
```php
@Serializable data class Profile(val id: String)
val uri = "https://www.example.com"

composable<Profile>(
  deepLinks = listOf(
    navDeepLink<Profile>(basePath = "$uri/profile")
  )
) { backStackEntry ->
  ProfileScreen(id = backStackEntry.toRoute<Profile>().id)
}
```

Example 4 (jsx):
```jsx
<activity …>
  <intent-filter>
    ...
    <data android:scheme="https" android:host="www.example.com" />
  </intent-filter>
</activity>
```

---
