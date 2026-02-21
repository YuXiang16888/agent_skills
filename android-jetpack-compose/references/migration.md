# Jetpack-Compose - Migration

**Pages:** 5

---

## Using Views in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/interoperability-apis/views-in-compose

**Contents:**
- Using Views in Compose Stay organized with collections Save and categorize content based on your preferences.
- AndroidView with view binding
- AndroidView in Lazy lists
- Fragments in Compose
- Calling the Android framework from Compose
  - Composition Locals
- Other interactions
- Case study: Broadcast receivers
- Next steps
- Recommended for you

You can include an Android View hierarchy in a Compose UI. This approach is particularly useful if you want to use UI elements that are not yet available in Compose, like AdView. This approach also lets you reuse custom views you may have designed.

To include a view element or hierarchy, use the AndroidView composable. AndroidView is passed a lambda that returns a View. AndroidView also provides an update callback that is called when the view is inflated. The AndroidView recomposes whenever a State read within the callback changes. AndroidView, like many other built-in composables, takes a Modifier parameter that can be used, for example, to set its position in the parent composable.

@Composable fun CustomView() { var selectedItem by remember { mutableIntStateOf(0) } // Adds view to Compose AndroidView( modifier = Modifier.fillMaxSize(), // Occupy the max size in the Compose UI tree factory = { context -> // Creates view MyView(context).apply { // Sets up listeners for View -> Compose communication setOnClickListener { selectedItem = 1 } } }, update = { view -> // View's been inflated or state read in this block has been updated // Add logic here if necessary // As selectedItem is read here, AndroidView will recompose // whenever the state changes // Example of Compose -> View communication view.selectedItem = selectedItem } ) } @Composable fun ContentExample() { Column(Modifier.fillMaxSize()) { Text("Look at this CustomView!") CustomView() } } InteroperabilityAPIsSnippets.kt

To embed an XML layout, use the AndroidViewBinding API, which is provided by the androidx.compose.ui:ui-viewbinding library. To do this, your project must enable view binding.

@Composable fun AndroidViewBindingExample() { AndroidViewBinding(ExampleLayoutBinding::inflate) { exampleView.setBackgroundColor(Color.GRAY) } }InteroperabilityAPIsSnippets.kt

If you are using an AndroidView in a Lazy list (LazyColumn, LazyRow, Pager, etc.), consider using the AndroidView overload introduced in version 1.4.0-rc01. This overload allows Compose to reuse the underlying View instance when the containing composition is reused as is the case for Lazy lists.

This overload of AndroidView adds 2 additional parameters:

@Composable fun AndroidViewInLazyList() { LazyColumn { items(100) { index -> AndroidView( modifier = Modifier.fillMaxSize(), // Occupy the max size in the Compose UI tree factory = { context -> MyView(context) }, update = { view -> view.selectedItem = index }, onReset = { view -> view.clear() } ) } } }InteroperabilityAPIsSnippets.kt

Use the AndroidFragment composable to add a Fragment in Compose. AndroidFragment has fragment-specific handling such as removing the fragment when the composable leaves the composition.

To include a fragment, use the AndroidFragment composable. You pass a Fragment class to AndroidFragment, which then adds an instance of that class directly into the composition. AndroidFragment also provides a fragmentState object to create the AndroidFragment with a given state, arguments to pass into the new fragment, and an onUpdate callback that provides the fragment from the composition. Like many other built-in composables, AndroidFragment accepts a Modifier parameter that you can use, for example, to set its position in the parent composable.

Call AndroidFragment in Compose as follows:

@Composable fun FragmentInComposeExample() { AndroidFragment<MyFragment>() } InteroperabilityAPIsSnippets.kt

Compose operates within the Android framework classes. For example, it's hosted on Android View classes, like Activity or Fragment, and might use Android framework classes like the Context, system resources, Service, or BroadcastReceiver.

To learn more about system resources, see Resources in Compose.

CompositionLocal classes allow passing data implicitly through composable functions. They're usually provided with a value in a certain node of the UI tree. That value can be used by its composable descendants without declaring the CompositionLocal as a parameter in the composable function.

CompositionLocal is used to propagate values for Android framework types in Compose such as Context, Configuration or the View in which the Compose code is hosted with the corresponding LocalContext, LocalConfiguration, or LocalView. Note that CompositionLocal classes are prefixed with Local for better discoverability with auto-complete in the IDE.

Access the current value of a CompositionLocal by using its current property. For example, the code below shows a toast message by providing LocalContext.current into the Toast.makeToast method.

@Composable fun ToastGreetingButton(greeting: String) { val context = LocalContext.current Button(onClick = { Toast.makeText(context, greeting, Toast.LENGTH_SHORT).show() }) { Text("Greet") } }InteroperabilityAPIsSnippets.kt

For a more complete example, take a look at the Case Study: BroadcastReceivers section at the end of this document.

If there isn't a utility defined for the interaction you need, the best practice is to follow the general Compose guideline, data flows down, events flow up (discussed at more length in Thinking in Compose). For example, this composable launches a different activity:

class OtherInteractionsActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) // get data from savedInstanceState setContent { MaterialTheme { ExampleComposable(data, onButtonClick = { startActivity(Intent(this, MyActivity::class.java)) }) } } } } @Composable fun ExampleComposable(data: DataExample, onButtonClick: () -> Unit) { Button(onClick = onButtonClick) { Text(data.title) } }InteroperabilityAPIsSnippets.kt

For a more realistic example of features you might want to migrate or implement in Compose, and to showcase CompositionLocal and side effects, let's say a BroadcastReceiver needs to be registered from a composable function.

The solution makes use of LocalContext to use the current context, and rememberUpdatedState and DisposableEffect side effects.

@Composable fun SystemBroadcastReceiver( systemAction: String, onSystemEvent: (intent: Intent?) -> Unit ) { // Grab the current context in this part of the UI tree val context = LocalContext.current // Safely use the latest onSystemEvent lambda passed to the function val currentOnSystemEvent by rememberUpdatedState(onSystemEvent) // If either context or systemAction changes, unregister and register again DisposableEffect(context, systemAction) { val intentFilter = IntentFilter(systemAction) val broadcast = object : BroadcastReceiver() { override fun onReceive(context: Context?, intent: Intent?) { currentOnSystemEvent(intent) } } context.registerReceiver(broadcast, intentFilter) // When the effect leaves the Composition, remove the callback onDispose { context.unregisterReceiver(broadcast) } } } @Composable fun HomeScreen() { SystemBroadcastReceiver(Intent.ACTION_BATTERY_CHANGED) { batteryStatus -> val isCharging = /* Get from batteryStatus ... */ true /* Do something if the device is charging */ } /* Rest of the HomeScreen */ }InteroperabilityAPIsSnippets.kt

Now that you know the interoperability APIs when using Compose in Views and vice versa, explore the Other considerations page to learn more.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Migration strategy Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/strategy

**Contents:**
- Migration strategy Stay organized with collections Save and categorize content based on your preferences.
- Build new screens with Compose
  - Add new features in existing screens
- Build a library of common UI components
- Replace existing features with Compose
  - Simple screens
  - Mixed view and Compose screens
- Removing Fragments and Navigation component
- Additional resources
- Next steps

If you have an existing View-based app, you may not want to rewrite its entire UI all at once. This page helps you add new Compose components into your existing app. To get started with using Compose in your app, see Set up Compose for an existing app.

Jetpack Compose was designed with View interoperability right from the start. This functionality means you can migrate your existing View-based app to Compose while still being able to build new features. To migrate to Compose, we recommend an incremental migration where Compose and Views co-exist in your codebase until your app is fully in Compose.

To migrate your app to Compose, follow these steps:

Using Compose to build new features that encompass an entire screen is the best way to drive your adoption of Compose. With this strategy, you can add features and take advantage of the benefits of Compose while still catering to your company’s business needs.

When you use Compose to build new screens in your existing app, you're still working under the constraints of your app’s architecture. If you are using Fragments and the Navigation component, then you would have to create a new Fragment and have its contents in Compose.

To use Compose in a Fragment, return a ComposeView in the onCreateView() lifecycle method of your Fragment. ComposeView has a setContent() method where you can provide a composable function.

class NewFeatureFragment : Fragment() { override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View { return ComposeView(requireContext()).apply { setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed) setContent { NewFeatureScreen() } } } }MigrationStrategySnippets.kt

See ComposeView in Fragments to learn more.

You can also use Compose in an existing View-based screen if the new feature you are adding is part of an existing screen. To do so, add a ComposeView to the View hierarchy, just like any other View.

For example, say you want to add a child view to a LinearLayout. You can do so in XML as follows:

Once the view has been inflated, you can later reference the ComposeView in the hierarchy and call setContent().

To learn more about ComposeView, check out Interoperability APIs.

As you’re building features with Compose, you’ll quickly realize that you end up building a library of components. Creating a library of common UI components allows you to have a single source of truth for these components in your app and promote reusability. Features you build can then depend on this library. This technique is especially useful if you are building a custom design system in Compose.

Depending on your app’s size, this library could be a separate package, module, or library module. For more information on organizing modules in your app, check out the Guide to Android app modularization.

In addition to using Compose to build new features, you’ll want to gradually migrate existing features in your app to take advantage of Compose.

Having your app be Compose-only can accelerate your development and also reduce the APK size and build times of your app. See Compare Compose and View performance to learn more.

The first places to look when migrating existing features to Compose are simple screens. Simple screens can be a welcome screen, a confirmation screen, or a setting screen wherein the data displayed in the UI is relatively static.

Take the following XML file:

The XML file can be rewritten in Compose in a few lines:

@Composable fun SimpleScreen() { Column(Modifier.fillMaxSize()) { Text( text = stringResource(R.string.title), style = MaterialTheme.typography.headlineMedium ) Text( text = stringResource(R.string.subtitle), style = MaterialTheme.typography.headlineSmall ) Text( text = stringResource(R.string.body), style = MaterialTheme.typography.bodyMedium ) Spacer(modifier = Modifier.weight(1f)) Button(onClick = { /* Handle click */ }, Modifier.fillMaxWidth()) { Text(text = stringResource(R.string.confirm)) } } }MigrationStrategySnippets.kt

A screen that already contains a bit of Compose code is another good candidate for migrating entirely to Compose. Depending on the complexity of the screen, you can either migrate it entirely to Compose, or do it piece-by-piece. If the screen started with Compose in a subtree of the UI hierarchy, you would continue migrating UI elements until the entire screen is in Compose. This approach is also called the bottom-up approach.

You can migrate to Navigation Compose once you’re able to remove all of your Fragments and replace with corresponding screen-level composables. Screen-level composables can contain a mix of Compose and View content, but all navigation destinations must be composables to enable Navigation Compose migration. Until then, you should continue using Fragment-based Navigation component in your mixed View and Compose codebase. See Migrate Jetpack Navigation to Navigation Compose for more information.

Check out the following additional resources to learn more about migrating your existing View-based app to Compose:

Now that you know the strategy you can take to migrate your existing View-based app, explore the Interoperability APIs to learn more.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Using Compose in Views Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/interoperability-apis/compose-in-views

**Contents:**
- Using Compose in Views Stay organized with collections Save and categorize content based on your preferences.
- ViewCompositionStrategy for ComposeView
- ComposeView in Fragments
- Multiple ComposeView instances in the same layout
- Preview composables in Layout Editor
- Next steps
- Recommended for you

You can add Compose-based UI into an existing app that uses a View-based design.

To create a new, entirely Compose-based screen, have your activity call the setContent() method, and pass whatever composable functions you like.

class ExampleActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContent { // In here, we can call composables! MaterialTheme { Greeting(name = "compose") } } } } @Composable fun Greeting(name: String) { Text(text = "Hello $name!") }InteroperabilityAPIsSnippets.kt

This code looks just like what you'd find in a Compose-only app.

Caution: To use the ComponentActivity.setContent method, add the androidx.activity:activity-compose:$latestVersion dependency to your build.gradle file.

See the Activity releases page to find out the latest version.

ViewCompositionStrategy defines when the Composition should be disposed. The default, ViewCompositionStrategy.Default, disposes the Composition when the underlying ComposeView detaches from the window, unless it is part of a pooling container such as a RecyclerView. In a single-Activity Compose-only app, this default behavior is what you would want, however, if you are incrementally adding Compose in your codebase, this behavior may cause state loss in some scenarios.

To change the ViewCompositionStrategy, call the setViewCompositionStrategy() method and provide a different strategy.

The table below summarizes the different scenarios you can use ViewCompositionStrategy in:

If you want to incorporate Compose UI content in a fragment or an existing View layout, use ComposeView and call its setContent() method. ComposeView is an Android View.

You can put the ComposeView in your XML layout just like any other View:

In the Kotlin source code, inflate the layout from the layout resource defined in XML. Then get the ComposeView using the XML ID, set a Composition strategy that works best for the host View, and call setContent() to use Compose.

class ExampleFragmentXml : Fragment() { override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View { val view = inflater.inflate(R.layout.fragment_example, container, false) val composeView = view.findViewById<ComposeView>(R.id.compose_view) composeView.apply { // Dispose of the Composition when the view's LifecycleOwner // is destroyed setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed) setContent { // In Compose world MaterialTheme { Text("Hello Compose!") } } } return view } }InteroperabilityAPIsSnippets.kt

Alternatively, you can also use view binding to obtain references to the ComposeView by referencing the generated binding class for your XML layout file:

class ExampleFragment : Fragment() { private var _binding: FragmentExampleBinding? = null // This property is only valid between onCreateView and onDestroyView. private val binding get() = _binding!! override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View { _binding = FragmentExampleBinding.inflate(inflater, container, false) val view = binding.root binding.composeView.apply { // Dispose of the Composition when the view's LifecycleOwner // is destroyed setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed) setContent { // In Compose world MaterialTheme { Text("Hello Compose!") } } } return view } override fun onDestroyView() { super.onDestroyView() _binding = null } }InteroperabilityAPIsSnippets.kt

Figure 1. This shows the output of the code that adds Compose elements in a View UI hierarchy. The "Hello Android!" text is displayed by a TextView widget. The "Hello Compose!" text is displayed by a Compose text element.

You can also include a ComposeView directly in a fragment if your full screen is built with Compose, which lets you avoid using an XML layout file entirely.

class ExampleFragmentNoXml : Fragment() { override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View { return ComposeView(requireContext()).apply { // Dispose of the Composition when the view's LifecycleOwner // is destroyed setViewCompositionStrategy(ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed) setContent { MaterialTheme { // In Compose world Text("Hello Compose!") } } } } }InteroperabilityAPIsSnippets.kt

If there are multiple ComposeView elements in the same layout, each one must have a unique ID for savedInstanceState to work.

class ExampleFragmentMultipleComposeView : Fragment() { override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View = LinearLayout(requireContext()).apply { addView( ComposeView(requireContext()).apply { setViewCompositionStrategy( ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed ) id = R.id.compose_view_x // ... } ) addView(TextView(requireContext())) addView( ComposeView(requireContext()).apply { setViewCompositionStrategy( ViewCompositionStrategy.DisposeOnViewTreeLifecycleDestroyed ) id = R.id.compose_view_y // ... } ) } }InteroperabilityAPIsSnippets.kt

The ComposeView IDs are defined in the res/values/ids.xml file:

You can also preview composables within the Layout Editor for your XML layout containing a ComposeView. Doing so lets you see how your composables look within a mixed Views and Compose layout.

Say you want to display the following composable in the Layout Editor. Note that composables annotated with @Preview are good candidates to preview in the Layout Editor.

@Preview @Composable fun GreetingPreview() { Greeting(name = "Android") }InteroperabilityAPIsSnippets.kt

To display this composable, use the tools:composableName tools attribute and set its value to the fully qualified name of the composable to preview in the layout.

Now that you know the interoperability APIs to use Compose in Views, learn how to use Views in Compose.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Compare Compose and View metrics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/compare-metrics

**Contents:**
- Compare Compose and View metrics Stay organized with collections Save and categorize content based on your preferences.
- APK size and build times
  - APK size
  - Build time
  - Summary
- Runtime performance
  - Smart recompositions
  - Baseline Profiles
  - Comparison with the View system
    - Everything extends View

Jetpack Compose accelerates UI development and improves Android development. However, take into consideration how adding Compose to an existing app can affect metrics such as an app's APK size, build, and runtime performance.

This section goes over the impact to APK size and build time by looking at the Sunflower sample app—an app that demonstrates best practices with migrating a View-based app to Compose.

Adding libraries to your project increases its APK size. The following results are for the minified release APK of each project with resource and code shrinking enabled, using R8 full mode, and measured using APK Analyzer.

When first adding Compose to Sunflower, the APK size increased from 2,252 KB to 3,034 KB—a 782 KB increase. The generated APK consisted of the UI build with a mix of Views and Compose. This increase is to be expected as additional dependencies were added to Sunflower.

Conversely, when Sunflower was migrated to a Compose-only app, the APK size decreased from 3,034 KB to 2,966 KB—a 68 KB decrease. This decrease was due to removing unused View dependencies, such as AppCompat and ConstraintLayout.

Adding Compose increases the build time of your app as the Compose compiler processes composables in your app. The following results were obtained using the standalone gradle-profiler tool, which executes a build several times so that a mean build time can be obtained for the debug build duration of Sunflower:

When first adding Compose to Sunflower, the mean build time increased from 299 ms to 399 ms—a 100 ms increase. This duration is due to the Compose compiler performing additional tasks to transform Compose code defined in the project.

Conversely, the mean build time dropped to 342 ms, a 57 ms decrease, when Sunflower's migration to Compose was completed. This reduction can be attributed to several factors which collectively reduce build time such as removing data binding, migrating dependencies that use kapt to KSP, and updating several dependencies to their latest versions.

Adopting Compose will effectively increase the APK size of your app and also increase the build time performance of your app due to the compilation process of Compose code. These tradeoffs, however, need to be weighed against the benefits of Compose, especially around developer productivity increases when adopting Compose. For example, the Play Store team found that writing UI requires much less code, sometimes up to 50%, thereby increasing productivity and maintainability of code.

You can read more case studies in Adopt Compose for Teams.

This section covers topics related to runtime performance in Jetpack Compose to help understand how Jetpack Compose compares to the View system's performance, and how you can measure it.

When portions of the UI are invalid, Compose tries to recompose just the portions that need to be updated. Read more about this in the Lifecycle of composables and Jetpack Compose phases documentation.

Baseline Profiles are an excellent way of speeding up common user journeys. Including a Baseline Profile in your app can improve code execution speed by about 30% from the first launch by avoiding interpretation and just-in-time (JIT) compilation steps for included code paths.

The Jetpack Compose library includes its own Baseline Profile and you automatically get these optimizations when you use Compose in your app. However, these optimizations only affect code paths within the Compose library, so we recommend that you add a Baseline Profile to your app to cover code paths outside of Compose.

Jetpack Compose has many improvements over the View system. These improvements are described in the following sections.

Every View that draws on the screen, such as TextView, Button, or ImageView, requires memory allocations, explicit state tracking, and various callbacks to support all use cases. Furthermore, the custom View owner needs to implement explicit logic to prevent redrawing when it isn't necessary—for example, for repetitive data processing.

Jetpack Compose addresses this in a few ways. Compose doesn't have explicit updatable objects for drawing views. UI elements are simple composable functions whose information is written to the composition in a replayable way. This helps cut down explicit state tracking, memory allocations, and callbacks to only the composables that require said features instead of requiring them by all extensions of a given View type.

Furthermore, Compose provides smart recompositions, replaying the previously drawn result if you don't need to make changes.

Traditional ViewGroups have a lot of expressiveness in their measure and layout APIs that make them prone to multiple layout passes. These multiple layout passes can cause exponential work if done at specific nested points in the view hierarchy.

Jetpack Compose enforces a single layout pass for all layout composables via its API contract. This lets Compose efficiently handle deep UI trees. If multiple measurements are needed, Compose has intrinsic measurements.

The View system needs to inflate XML layouts when showing a particular layout for the first time. This cost is saved in Jetpack Compose since layouts are written in Kotlin and compiled like the rest of your app.

In Jetpack Compose 1.0, there are notable differences between the performance of an app in debug and release modes. For representative timings, always use the release build instead of debug when profiling your app.

To check how your Jetpack Compose code is performing, you can use the Jetpack Macrobenchmark library. To learn how to use it with Jetpack Compose, see the MacrobenchmarkSample project.

The Jetpack Compose team also uses Macrobenchmark to catch any regressions that can happen. For example, see the benchmark for lazy column and its dashboard to track regressions.

Since Jetpack Compose is an unbundled library, it doesn't benefit from Zygote that preloads the View system's UI Toolkit classes and drawables. Jetpack Compose 1.0 utilizes profile installation for release builds. Profile installers let apps specify critical code to be ahead-of-time (AOT) compiled at installation time. Compose ships profile installation rules which reduce startup time and jank in Compose apps.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
gradle-profiler --benchmark --project-dir . :app:assembleDebug
```

---

## Interoperability APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/interoperability-apis

**Contents:**
- Interoperability APIs Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Incrementally migrating your app to Compose means that Compose and Views will co-exist in your app.

The following pages describe the specific APIs you need to use to integrate Compose within your existing View-based app:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---
