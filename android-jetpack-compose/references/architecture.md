# Jetpack-Compose - Architecture

**Pages:** 2

---

## Compose and other libraries Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/libraries

**Contents:**
- Compose and other libraries Stay organized with collections Save and categorize content based on your preferences.
- Activity
  - Activity Result
  - Requesting runtime permissions
  - Handling the system back button
- ViewModel
  - Groovy
  - Kotlin
  - Usage guidelines
- Streams of data

You can use your favorite libraries in Compose. This section describes how to incorporate a few of the most useful libraries.

To use Compose in an activity, you must use ComponentActivity, a subclass of Activity that provides the appropriate LifecycleOwner and components to Compose. It also provides additional APIs that decouple your code from overriding methods in your activity class. Activity Compose exposes these APIs to composables such that overriding methods outside of your composables or retrieving an explicit Activity instance is no longer required. Moreover, these APIs ensure that they are only initialized once, survive recomposition, and clean up properly if the composable is removed from the composition.

The rememberLauncherForActivityResult() API allows you to get a result from an activity in your composable:

@Composable fun GetContentExample() { var imageUri by remember { mutableStateOf<Uri?>(null) } val launcher = rememberLauncherForActivityResult(ActivityResultContracts.GetContent()) { uri: Uri? -> imageUri = uri } Column { Button(onClick = { launcher.launch("image/*") }) { Text(text = "Load Image") } Image( painter = rememberAsyncImagePainter(imageUri), contentDescription = "My Image" ) } }ComposeWithOtherLibraries.kt

This example demonstrates a simple GetContent() contract. Tapping the button launches the request. The trailing lambda for rememberLauncherForActivityResult() is invoked once the user selects an image and returns to the launching activity. This loads the selected image using Coil’s rememberImagePainter() function.

Any subclass of ActivityResultContract can be used as the first argument to rememberLauncherForActivityResult(). This means that you can use this technique to request content from the framework and in other common patterns. You can also create your own custom contracts and use them with this technique.

The same Activity Result API and rememberLauncherForActivityResult() explained above can be used to request runtime permissions using the RequestPermission contract for a single permission or RequestMultiplePermissions contract for multiple permissions.

The Accompanist Permissions library can also be used a layer above those APIs to map the current granted state for permissions into State that your Compose UI can use.

To provide custom back navigation and override the default behavior of the system back button from within your composable, your composable can use a BackHandler to intercept that event:

var backHandlingEnabled by remember { mutableStateOf(true) } BackHandler(backHandlingEnabled) { // Handle back press }ComposeWithOtherLibraries.kt

The first argument controls whether the BackHandler is currently enabled; you can use this argument to temporarily disable your handler based on the state of your component. The trailing lambda will be invoked if the user triggers a system back event, and the BackHandler is currently enabled.

If you use the Architecture Components ViewModel library, you can access a ViewModel from any composable by calling the viewModel() function. Add the following dependency to your Gradle file:

You can then use the viewModel() function in your code.

class MyViewModel : ViewModel() { /*...*/ } // import androidx.lifecycle.viewmodel.compose.viewModel @Composable fun MyScreen( viewModel: MyViewModel = viewModel() ) { // use viewModel here }ComposeWithOtherLibraries.kt

viewModel() returns an existing ViewModel or creates a new one. By default, the returned ViewModel is scoped to the enclosing activity, fragment or navigation destination, and is retained as long as the scope is alive.

For example, if the composable is used in an activity, viewModel() returns the same instance until the activity is finished or the process is killed.

class MyViewModel : ViewModel() { /*...*/ } // import androidx.lifecycle.viewmodel.compose.viewModel @Composable fun MyScreen( // Returns the same instance as long as the activity is alive, // just as if you grabbed the instance from an Activity or Fragment viewModel: MyViewModel = viewModel() ) { /* ... */ } @Composable fun MyScreen2( viewModel: MyViewModel = viewModel() // Same instance as in MyScreen ) { /* ... */ }ComposeWithOtherLibraries.kt

You usually access ViewModel instances at screen-level composables, that is, close to a root composable called from an activity, fragment, or destination of a Navigation graph. This is because ViewModels are, by default, scoped to those screen level objects. Read more about a ViewModel's lifecycle and scope here.

Try to avoid passing down ViewModel instances to other composables as this can make those composables more difficult to test and can break previews. Instead, pass only the data and functions they need as parameters.

You can use ViewModel instances to manage state for sub screen-level composables, however, be aware of the ViewModel's lifecycle and scope. If the composable is self-contained, you may want to consider using Hilt to inject the ViewModel to avoid having to pass dependencies from parent composables.

If your ViewModel has dependencies, viewModel() takes an optional ViewModelProvider.Factory as a parameter.

For more information about ViewModel in Compose and how instances are used with the Navigation Compose library, or activities and fragments, see the Interoperability docs.

Compose comes with extensions for Android's most popular stream-based solutions. Each of these extensions is provided by a different artifact:

These artifacts register as a listener and represent the values as a State. Whenever a new value is emitted, Compose recomposes those parts of the UI where that state.value is used. For example, in this code, ShowData recomposes every time exampleLiveData emits a new value.

// import androidx.lifecycle.viewmodel.compose.viewModel @Composable fun MyScreen( viewModel: MyViewModel = viewModel() ) { val dataExample = viewModel.exampleLiveData.observeAsState() // Because the state is read here, // MyScreen recomposes whenever dataExample changes. dataExample.value?.let { ShowData(dataExample) } }ComposeWithOtherLibraries.kt

Jetpack Compose lets you execute asynchronous operations using coroutines from within your composables.

See the LaunchedEffect, produceState, and rememberCoroutineScope APIs in the side effects documentation for more information.

The Navigation component provides support for Jetpack Compose applications. See Navigating with Compose and Migrate Jetpack Navigation to Navigation Compose for more information.

Hilt is the recommended solution for dependency injection in Android apps, and works seamlessly with Compose.

The viewModel() function mentioned in the ViewModel section automatically uses the ViewModel that Hilt constructs with the @HiltViewModel annotation. We've provided documentation with information about Hilt's ViewModel integration.

@HiltViewModel class MyViewModel @Inject constructor( private val savedStateHandle: SavedStateHandle, private val repository: ExampleRepository ) : ViewModel() { /* ... */ } // import androidx.lifecycle.viewmodel.compose.viewModel @Composable fun MyScreen( viewModel: MyViewModel = viewModel() ) { /* ... */ } ComposeWithOtherLibraries.kt

Hilt also integrates with the Navigation Compose library. Add the following additional dependencies to your Gradle file:

When using Navigation Compose, always use the hiltViewModel composable function to obtain an instance of your @HiltViewModel annotated ViewModel. This works with fragments or activities that are annotated with @AndroidEntryPoint.

For example, if ExampleScreen is a destination in a navigation graph, call hiltViewModel() to get an instance of ExampleViewModel scoped to the destination as shown in the code snippet below:

// import androidx.hilt.navigation.compose.hiltViewModel @Composable fun MyApp() { val navController = rememberNavController() val startRoute = "example" NavHost(navController, startDestination = startRoute) { composable("example") { backStackEntry -> // Creates a ViewModel from the current BackStackEntry // Available in the androidx.hilt:hilt-navigation-compose artifact val viewModel = hiltViewModel<MyViewModel>() MyScreen(viewModel) } /* ... */ } }ComposeWithOtherLibraries.kt

If you need to retrieve the instance of a ViewModel scoped to navigation routes or the navigation graph instead, use the hiltViewModel composable function and pass the corresponding backStackEntry as a parameter:

// import androidx.hilt.navigation.compose.hiltViewModel // import androidx.navigation.compose.getBackStackEntry @Composable fun MyApp() { val navController = rememberNavController() val startRoute = "example" val innerStartRoute = "exampleWithRoute" NavHost(navController, startDestination = startRoute) { navigation(startDestination = innerStartRoute, route = "Parent") { // ... composable("exampleWithRoute") { backStackEntry -> val parentEntry = remember(backStackEntry) { navController.getBackStackEntry("Parent") } val parentViewModel = hiltViewModel<ParentViewModel>(parentEntry) ExampleWithRouteScreen(parentViewModel) } } } }ComposeWithOtherLibraries.kt

The Paging library makes it easier for you to load data gradually and it's supported in Compose. The Paging release page contains information about the extra paging-compose dependency that needs to be added to the project and its version.

Here's an example of the Paging library's Compose APIs:

@Composable fun MyScreen(flow: Flow<PagingData<String>>) { val lazyPagingItems = flow.collectAsLazyPagingItems() LazyColumn { items( lazyPagingItems.itemCount, key = lazyPagingItems.itemKey { it } ) { index -> val item = lazyPagingItems[index] Text("Item is $item") } } }ComposeWithOtherLibraries.kt

Check out the Lists and grids documentation for more information about using Paging in Compose.

You can use the Maps Compose library to provide Google Maps in your app. Here's a usage example:

@Composable fun MapsExample() { val singapore = LatLng(1.35, 103.87) val cameraPositionState = rememberCameraPositionState { position = CameraPosition.fromLatLngZoom(singapore, 10f) } GoogleMap( modifier = Modifier.fillMaxSize(), cameraPositionState = cameraPositionState ) { Marker( state = remember { MarkerState(position = singapore) }, title = "Singapore", snippet = "Marker in Singapore" ) } }ComposeWithOtherLibraries.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Compose UI Architecture Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/architecture

**Contents:**
- Compose UI Architecture Stay organized with collections Save and categorize content based on your preferences.
- Unidirectional data flow
  - Unidirectional data flow in Jetpack Compose
  - Define composable parameters
- Events in Compose
  - ViewModels, states, and events: an example
- Learn more
  - Samples
- Recommended for you

In Compose the UI is immutable—there's no way to update it after it's been drawn. What you can control is the state of your UI. Every time the state of the UI changes, Compose recreates the parts of the UI tree that have changed. Composables can accept state and expose events—for example, a TextField accepts a value and exposes a callback onValueChange that requests the callback handler to change the value.

var name by remember { mutableStateOf("") } OutlinedTextField( value = name, onValueChange = { name = it }, label = { Text("Name") } )ArchitectureSnippets.kt

Because composables accept state and expose events, the unidirectional data flow pattern fits well with Jetpack Compose. This guide focuses on how to implement the unidirectional data flow pattern in Compose, how to implement events and state holders, and how to work with ViewModels in Compose.

A unidirectional data flow (UDF) is a design pattern where state flows down and events flow up. By following unidirectional data flow, you can decouple composables that display state in the UI from the parts of your app that store and change state.

The UI update loop for an app using unidirectional data flow looks like this:

Following this pattern when using Jetpack Compose provides several advantages:

Composables work based on state and events. For example, a TextField is only updated when its value parameter is updated and it exposes an onValueChange callback—an event that requests the value to be changed to a new one. Compose defines the State object as a value holder, and changes to the state value trigger a recomposition. You can hold the state in a remember { mutableStateOf(value) } or a rememberSaveable { mutableStateOf(value) depending on how long you need to remember the value for.

The type of the TextField composable's value is String, so this can come from anywhere—from a hardcoded value, from a ViewModel, or passed in from the parent composable. You don't have to hold it in a State object, but you need to update the value when onValueChange is called.

When defining the state parameters of a composable, keep the following questions in mind:

To promote decoupling and reuse, each composable should hold the least amount of information possible. For example, when building a composable to hold the header of a news article, prefer passing in only the information that needs to be displayed, rather than the entire news article:

@Composable fun Header(title: String, subtitle: String) { // Recomposes when title or subtitle have changed. } @Composable fun Header(news: News) { // Recomposes when a new instance of News is passed in. }ArchitectureSnippets.kt

Sometimes, using individual parameters also improves performance—for example, if News contains more information than just title and subtitle, whenever a new instance of News is passed into Header(news), the composable will recompose, even if title and subtitle haven't changed.

Consider carefully the number of parameters you pass in. Having a function with too many parameters decreases the ergonomics of the function, so in this case grouping them up in a class is preferred.

Every input to your app should be represented as an event: taps, text changes, and even timers or other updates. As these events change the state of your UI, the ViewModel should handle them and update the UI state.

The UI layer should never change state outside of an event handler because this can introduce inconsistencies and bugs in your application.

Prefer passing immutable values for state and event handler lambdas. This approach has the following benefits:

For example, a composable that accepts a String and a lambda as parameters can be called from many contexts and is highly reusable. Suppose that the top app bar in your app always displays text and has a back button. You can define a more generic MyAppTopAppBar composable that receives the text and the back button handle as parameters:

@Composable fun MyAppTopAppBar(topAppBarText: String, onBackPressed: () -> Unit) { TopAppBar( title = { Text( text = topAppBarText, textAlign = TextAlign.Center, modifier = Modifier .fillMaxSize() .wrapContentSize(Alignment.Center) ) }, navigationIcon = { IconButton(onClick = onBackPressed) { Icon( Icons.AutoMirrored.Filled.ArrowBack, contentDescription = localizedString ) } }, // ... ) }ArchitectureSnippets.kt

By using ViewModel and mutableStateOf, you can also introduce unidirectional data flow in your app if one of the following is true:

For example, when implementing a sign-in screen, tapping on a Sign in button should cause your app to display a progress spinner and a network call. If the login was successful, then your app navigates to a different screen; in case of an error the app shows a Snackbar. Here's how you would model the screen state and the event:

The screen has four states:

You can model these states as a sealed class. The ViewModel exposes the state as a State, sets the initial state, and updates the state as needed. The ViewModel also handles the sign-in event by exposing an onSignIn() method.

class MyViewModel : ViewModel() { private val _uiState = mutableStateOf<UiState>(UiState.SignedOut) val uiState: State<UiState> get() = _uiState // ... }ArchitectureSnippets.kt

In addition to the mutableStateOf API, Compose provides extensions for LiveData, Flow, and Observable to register as a listener and represent the value as a state.

class MyViewModel : ViewModel() { private val _uiState = MutableLiveData<UiState>(UiState.SignedOut) val uiState: LiveData<UiState> get() = _uiState // ... } @Composable fun MyComposable(viewModel: MyViewModel) { val uiState = viewModel.uiState.observeAsState() // ... }ArchitectureSnippets.kt

To learn more about architecture in Jetpack Compose, consult the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---
