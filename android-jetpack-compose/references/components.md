# Jetpack-Compose - Components

**Pages:** 12

---

## Bottom sheets Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/bottom-sheets

**Contents:**
- Bottom sheets Stay organized with collections Save and categorize content based on your preferences.
- Control sheet state programmatically

If you want to implement a bottom sheet, you can use the ModalBottomSheet composable.

You can use the content slot, which uses a ColumnScope to layout sheet content composables in a column:

ModalBottomSheet(onDismissRequest = { /* Executed when the sheet is dismissed */ }) { // Sheet content }MaterialLayoutSnippets.kt

To programmatically expand and collapse the sheet, use SheetState. You can use rememberModalBottomSheetState to create an instance of SheetState that must be passed to ModalBottomSheet with the sheetState parameter. SheetState provides access to the show and hide functions, as well as properties related to the current sheet state. These suspending functions require a CoroutineScope — for example, using rememberCoroutineScope — and you can call them in response to UI events. Make sure to remove the ModalBottomSheet from composition upon hiding the bottom sheet.

val sheetState = rememberModalBottomSheetState() val scope = rememberCoroutineScope() var showBottomSheet by remember { mutableStateOf(false) } Scaffold( floatingActionButton = { ExtendedFloatingActionButton( text = { Text("Show bottom sheet") }, icon = { Icon(Icons.Filled.Add, contentDescription = "") }, onClick = { showBottomSheet = true } ) } ) { contentPadding -> // Screen content if (showBottomSheet) { ModalBottomSheet( onDismissRequest = { showBottomSheet = false }, sheetState = sheetState ) { // Sheet content Button(onClick = { scope.launch { sheetState.hide() }.invokeOnCompletion { if (!sheetState.isVisible) { showBottomSheet = false } } }) { Text("Hide bottom sheet") } } } }MaterialLayoutSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Navigate from top app bar Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/app-bars-navigate

**Contents:**
- Navigate from top app bar Stay organized with collections Save and categorize content based on your preferences.
- Example
  - Key points about the code
  - Pass a function
- Additional resources

This guide demonstrates how you can make the navigation icon in a top app bar perform navigation actions.

The following snippet is a minimal example of how you can implement a top app bar with a functional navigation icon. In this case, the icon takes the user to their previous destination in the app:

@Composable fun TopBarNavigationExample( navigateBack: () -> Unit, ) { Scaffold( topBar = { CenterAlignedTopAppBar( title = { Text( "Navigation example", ) }, navigationIcon = { IconButton(onClick = navigateBack) { Icon( imageVector = Icons.AutoMirrored.Filled.ArrowBack, contentDescription = "Localized description" ) } }, ) }, ) { innerPadding -> Text( "Click the back button to pop from the back stack.", modifier = Modifier.padding(innerPadding), ) } }AppBar.kt

Note the following in this example:

As such, whenever the user clicks the navigation icon in the top app back, it calls navigateBack().

This example uses a back arrow for the icon. As such, the argument for navigateBack() should take the user to the previous destination.

To do so, pass TopBarNavigationExample a call to NavController.popBackStack(). You do this where you build your navigation graph. For example:

For more information on how to implement navigation in your app, see the following series of guides:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
NavHost(navController, startDestination = "home") {
    composable("topBarNavigationExample") {
        TopBarNavigationExample{ navController.popBackStack() }
    }
    // Other destinations...
```

---

## App bars Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/app-bars

**Contents:**
- App bars Stay organized with collections Save and categorize content based on your preferences.
- Top app bars
  - API surface
  - Scroll behavior
  - Examples
    - Small
    - Center aligned
    - Medium
    - Large
- Bottom app bar

App bars are containers that provide the user access to key features and navigation items. There are two types of app bars, top app bars and bottom app bars. Their respective appearance and purpose are as follows:

Across the top of the screen.

Provides access to key tasks and information. Generally hosts a title, core action items, and certain navigation items.

Across the bottom of the screen.

Typically includes core navigation items. May also provide access to other key actions, such as through a contained floating action button.

To implement a top app bar and bottom app bar, use the TopAppBar and BottomAppBar composables, respectively. They let you create consistent interfaces that encapsulate navigation and action controls, and which are aligned with Material Design principles.

The following table outlines the four types of top app bars:

Small: For screens that don't require a lot of navigation or actions.

Center aligned: For screens that have a single, primary action.

Medium: For screens that require a moderate amount of navigation and actions.

Large: For screens that require a lot of navigation and actions.

The various composables that allow you to implement the four different top app bars are quite similar. They share several key parameters:

You can control how the app bar responds when the user scrolls the given scaffold's inner content. To do so, create an instance of TopAppBarScrollBehavior and pass it to your top app bar for the scrollBehavior parameter.

There are three types of TopAppBarScrollBehavior. They are as follows:

The following examples implement several of these options.

The following sections provide implementations for the four different types of top app bars, including varying examples of how you can control scroll behavior.

To create a small top app bar, use the TopAppBar composable. The following example implements a basic top app bar that contains only a title.

The following example does not pass TopAppBar a value for scrollBehavior and it therefore does not react to scrolling of the inner content.

@Composable fun SmallTopAppBarExample() { Scaffold( topBar = { TopAppBar( colors = TopAppBarDefaults.topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), title = { Text("Small Top App Bar") } ) }, ) { innerPadding -> ScrollContent(innerPadding) } }AppBar.kt

This implementation appears as follows:

The center aligned top app bar is essentially the same as the small app bar, though the title is centered within the component. To implement it, use the dedicated CenterAlignedTopAppBar composable.

This example uses enterAlwaysScrollBehavior() to get the value that it passes for scrollBehavior. As such, the bar collapses when the user scrolls the scaffold's inner content.

@Composable fun CenterAlignedTopAppBarExample() { val scrollBehavior = TopAppBarDefaults.pinnedScrollBehavior(rememberTopAppBarState()) Scaffold( modifier = Modifier.nestedScroll(scrollBehavior.nestedScrollConnection), topBar = { CenterAlignedTopAppBar( colors = TopAppBarDefaults.topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), title = { Text( "Centered Top App Bar", maxLines = 1, overflow = TextOverflow.Ellipsis ) }, navigationIcon = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.AutoMirrored.Filled.ArrowBack, contentDescription = "Localized description" ) } }, actions = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.Filled.Menu, contentDescription = "Localized description" ) } }, scrollBehavior = scrollBehavior, ) }, ) { innerPadding -> ScrollContent(innerPadding) } }AppBar.kt

This implementation appears as follows:

The medium top app bar places the title beneath any additional icons. To create one, use the MediumTopAppBar composable.

Like the previous snippet, this example uses enterAlwaysScrollBehavior() to get the value that it passes for scrollBehavior.

@Composable fun MediumTopAppBarExample() { val scrollBehavior = TopAppBarDefaults.enterAlwaysScrollBehavior(rememberTopAppBarState()) Scaffold( modifier = Modifier.nestedScroll(scrollBehavior.nestedScrollConnection), topBar = { MediumTopAppBar( colors = TopAppBarDefaults.topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), title = { Text( "Medium Top App Bar", maxLines = 1, overflow = TextOverflow.Ellipsis ) }, navigationIcon = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.AutoMirrored.Filled.ArrowBack, contentDescription = "Localized description" ) } }, actions = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.Filled.Menu, contentDescription = "Localized description" ) } }, scrollBehavior = scrollBehavior ) }, ) { innerPadding -> ScrollContent(innerPadding) } }AppBar.kt

This implementation appears as follows, with a demonstration of how the scroll behavior from enterAlwaysScrollBehavior() appears:

A large top app bar is similar to the medium, though the padding between the title and the icons is greater and it occupies more space on screen overall. To create one, use the LargeTopAppBar composable.

Unlike the preceding snippets, this example uses exitUntilCollapsedScrollBehavior() to get the value that it passes for scrollBehavior. As such, the bar collapses when the user scrolls the scaffold's inner content, but then expands when the user scrolls to the end of the inner content.

@Composable fun LargeTopAppBarExample() { val scrollBehavior = TopAppBarDefaults.exitUntilCollapsedScrollBehavior(rememberTopAppBarState()) Scaffold( modifier = Modifier.nestedScroll(scrollBehavior.nestedScrollConnection), topBar = { LargeTopAppBar( colors = TopAppBarDefaults.topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), title = { Text( "Large Top App Bar", maxLines = 1, overflow = TextOverflow.Ellipsis ) }, navigationIcon = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.AutoMirrored.Filled.ArrowBack, contentDescription = "Localized description" ) } }, actions = { IconButton(onClick = { /* do something */ }) { Icon( imageVector = Icons.Filled.Menu, contentDescription = "Localized description" ) } }, scrollBehavior = scrollBehavior ) }, ) { innerPadding -> ScrollContent(innerPadding) } }AppBar.kt

This implementation appears as follows:

To create a bottom app bar, use the BottomAppBar composable. Using this composable is quite similar to the top app bar composables described in the preceding sections of this page. You pass composables for the following key parameters:

@Composable fun BottomAppBarExample() { Scaffold( bottomBar = { BottomAppBar( actions = { IconButton(onClick = { /* do something */ }) { Icon(Icons.Filled.Check, contentDescription = "Localized description") } IconButton(onClick = { /* do something */ }) { Icon( Icons.Filled.Edit, contentDescription = "Localized description", ) } IconButton(onClick = { /* do something */ }) { Icon( Icons.Filled.Mic, contentDescription = "Localized description", ) } IconButton(onClick = { /* do something */ }) { Icon( Icons.Filled.Image, contentDescription = "Localized description", ) } }, floatingActionButton = { FloatingActionButton( onClick = { /* do something */ }, containerColor = BottomAppBarDefaults.bottomAppBarFabColor, elevation = FloatingActionButtonDefaults.bottomAppBarFabElevation() ) { Icon(Icons.Filled.Add, "Localized description") } } ) }, ) { innerPadding -> Text( modifier = Modifier.padding(innerPadding), text = "Example of a scaffold with a bottom app bar." ) } }AppBar.kt

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Dialog Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/dialog

**Contents:**
- Dialog Stay organized with collections Save and categorize content based on your preferences.
- Alert dialog
- Dialog composable
  - Basic example
  - Advanced example
- Additional resources

The Dialog component displays dialog messages or requests user input on a layer above the main app content. It creates an interruptive UI experience to capture user attention.

Among the use cases for a dialog are the following:

The AlertDialog composable provides a convenient API for creating a Material Design themed dialog. AlertDialog has specific parameters for handling particular elements of the dialog. Among them are the following:

The following example implements two buttons in an alert dialog, one that dismisses the dialog, and another that confirms its request.

@Composable fun AlertDialogExample( onDismissRequest: () -> Unit, onConfirmation: () -> Unit, dialogTitle: String, dialogText: String, icon: ImageVector, ) { AlertDialog( icon = { Icon(icon, contentDescription = "Example Icon") }, title = { Text(text = dialogTitle) }, text = { Text(text = dialogText) }, onDismissRequest = { onDismissRequest() }, confirmButton = { TextButton( onClick = { onConfirmation() } ) { Text("Confirm") } }, dismissButton = { TextButton( onClick = { onDismissRequest() } ) { Text("Dismiss") } } ) }Dialog.kt

This implementation implies a parent composable that passes arguments to the child composable in this way:

@Composable fun DialogExamples() { // ... val openAlertDialog = remember { mutableStateOf(false) } // ... when { // ... openAlertDialog.value -> { AlertDialogExample( onDismissRequest = { openAlertDialog.value = false }, onConfirmation = { openAlertDialog.value = false println("Confirmation registered") // Add logic here to handle confirmation. }, dialogTitle = "Alert dialog example", dialogText = "This is an example of an alert dialog with buttons.", icon = Icons.Default.Info ) } } } }Dialog.kt

This implementation appears as follows:

Dialog is a basic composable that doesn't provide any styling or predefined slots for content. It is a relatively straightforward container that you should populate with a container such as Card. The following are some of the key parameters of a dialog:

The following example is a basic implementation of the Dialog composable. Note that it uses a Card as the secondary container. Without the Card, the Text component would appear alone above the main app content.

@Composable fun MinimalDialog(onDismissRequest: () -> Unit) { Dialog(onDismissRequest = { onDismissRequest() }) { Card( modifier = Modifier .fillMaxWidth() .height(200.dp) .padding(16.dp), shape = RoundedCornerShape(16.dp), ) { Text( text = "This is a minimal dialog", modifier = Modifier .fillMaxSize() .wrapContentSize(Alignment.Center), textAlign = TextAlign.Center, ) } } }Dialog.kt

This implementation appears as follows. Note that when the dialog is open, the main app content beneath it appears darkened and grayed out:

The following is a more advanced implemented of the Dialog composable. In this case, the component manually implements a similar interface to the AlertDialog example above.

@Composable fun DialogWithImage( onDismissRequest: () -> Unit, onConfirmation: () -> Unit, painter: Painter, imageDescription: String, ) { Dialog(onDismissRequest = { onDismissRequest() }) { // Draw a rectangle shape with rounded corners inside the dialog Card( modifier = Modifier .fillMaxWidth() .height(375.dp) .padding(16.dp), shape = RoundedCornerShape(16.dp), ) { Column( modifier = Modifier .fillMaxSize(), verticalArrangement = Arrangement.Center, horizontalAlignment = Alignment.CenterHorizontally, ) { Image( painter = painter, contentDescription = imageDescription, contentScale = ContentScale.Fit, modifier = Modifier .height(160.dp) ) Text( text = "This is a dialog with buttons and an image.", modifier = Modifier.padding(16.dp), ) Row( modifier = Modifier .fillMaxWidth(), horizontalArrangement = Arrangement.Center, ) { TextButton( onClick = { onDismissRequest() }, modifier = Modifier.padding(8.dp), ) { Text("Dismiss") } TextButton( onClick = { onConfirmation() }, modifier = Modifier.padding(8.dp), ) { Text("Confirm") } } } } } }Dialog.kt

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Create a dynamic top app bar Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/app-bars-dynamic

**Contents:**
- Create a dynamic top app bar Stay organized with collections Save and categorize content based on your preferences.
- Implement dynamic top app bar behavior
  - Key points about the code
  - Result
- Integrate selectable list into dynamic top app bar
  - Key points about the code
  - Result
- Additional resources

This guide explains how to create a dynamic top app bar in Compose that changes its options when items are selected from the list. You can modify the top app bar's title and actions based on the selection state.

This code defines a composable function for the top app bar that changes based on item selection:

@Composable fun AppBarSelectionActions( selectedItems: Set<Int>, modifier: Modifier = Modifier, ) { val hasSelection = selectedItems.isNotEmpty() val topBarText = if (hasSelection) { "Selected ${selectedItems.size} items" } else { "List of items" } TopAppBar( title = { Text(topBarText) }, colors = TopAppBarDefaults.topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), actions = { if (hasSelection) { IconButton(onClick = { /* click action */ }) { Icon( imageVector = Icons.Filled.Share, contentDescription = "Share items" ) } } }, modifier = modifier ) }AppBar.kt

This example demonstrates how to add a selectable list to a dynamic top app bar:

@Composable private fun AppBarMultiSelectionExample( modifier: Modifier = Modifier, ) { val listItems by remember { mutableStateOf(listOf(1, 2, 3, 4, 5, 6)) } var selectedItems by rememberSaveable { mutableStateOf(setOf<Int>()) } Scaffold( modifier = modifier, topBar = { AppBarSelectionActions(selectedItems) } ) { innerPadding -> LazyColumn(contentPadding = innerPadding) { itemsIndexed(listItems) { _, index -> val isItemSelected = selectedItems.contains(index) ListItemSelectable( selected = isItemSelected, Modifier .combinedClickable( interactionSource = remember { MutableInteractionSource() }, indication = null, onClick = { /* click action */ }, onLongClick = { if (isItemSelected) selectedItems -= index else selectedItems += index } ) ) } } } }AppBar.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Scaffold

**URL:** https://developer.android.com/develop/ui/compose/components/scaffold

**Contents:**
- Scaffold
- Example
- Additional resources

In Material Design, a scaffold is a fundamental structure that provides a standardized platform for complex user interfaces. It holds together different parts of the UI, such as app bars and floating action buttons, giving apps a coherent look and feel.

The Scaffold composable provides a straightforward API you can use to quickly assemble your app's structure according to Material Design guidelines. Scaffold accepts several composables as parameters. Among these are the following:

You can also pass Scaffold content as you would to other containers. It passes PaddingValues to the content lambda that you should apply to your content's root composable to constrain its size.

The following example shows a complete Scaffold implementation. It contains a top app bar, a bottom app bar, and a floating action button.

@Composable fun ScaffoldExample() { var presses by remember { mutableIntStateOf(0) } Scaffold( topBar = { TopAppBar( colors = topAppBarColors( containerColor = MaterialTheme.colorScheme.primaryContainer, titleContentColor = MaterialTheme.colorScheme.primary, ), title = { Text("Top app bar") } ) }, bottomBar = { BottomAppBar( containerColor = MaterialTheme.colorScheme.primaryContainer, contentColor = MaterialTheme.colorScheme.primary, ) { Text( modifier = Modifier .fillMaxWidth(), textAlign = TextAlign.Center, text = "Bottom app bar", ) } }, floatingActionButton = { FloatingActionButton(onClick = { presses++ }) { Icon(Icons.Default.Add, contentDescription = "Add") } } ) { innerPadding -> Column( modifier = Modifier .padding(innerPadding), verticalArrangement = Arrangement.spacedBy(16.dp), ) { Text( modifier = Modifier.padding(8.dp), text = """ This is an example of a scaffold. It uses the Scaffold composable's parameters to create a screen with a simple top app bar, bottom app bar, and floating action button. It also contains some basic inner content, such as this text. You have pressed the floating action button $presses times. """.trimIndent(), ) } } }Scaffold.kt

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Chip Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/chip

**Contents:**
- Chip Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Assist chip
- Filter chip
- Input chip
- Suggestion chip
- Elevated chip
- Additional resources

The Chip component is a compact, interactive UI element. It represents complex entities like a contact or tag, often with an icon and label. It can be checkable, dismissible, or clickable.

The four types of chips and where you might use them are as follows:

There are four composables that correspond to the four types of chips. The following sections outline these composables and their differences in detail. However, they share the following parameters:

The AssistChip composable provides a straightforward way to create an assist chip that nudges the user in a particular direction. One distinguishing feature is its leadingIcon parameter that lets you display an icon on the left side of the chip. The following example demonstrates how you can implement it:

@Composable fun AssistChipExample() { AssistChip( onClick = { Log.d("Assist chip", "hello world") }, label = { Text("Assist chip") }, leadingIcon = { Icon( Icons.Filled.Settings, contentDescription = "Localized description", Modifier.size(AssistChipDefaults.IconSize) ) } ) }Chip.kt

This implementation appears as follows.

The FilterChip composable requires you to track whether or not the chip is selected. The following example demonstrates how you can show a leading checked icon only when the user has selected the chip:

@Composable fun FilterChipExample() { var selected by remember { mutableStateOf(false) } FilterChip( onClick = { selected = !selected }, label = { Text("Filter chip") }, selected = selected, leadingIcon = if (selected) { { Icon( imageVector = Icons.Filled.Done, contentDescription = "Done icon", modifier = Modifier.size(FilterChipDefaults.IconSize) ) } } else { null }, ) }Chip.kt

This implementation appears as follows when unselected:

And appears as follows when selected:

You can use the InputChip composable to create chips that result from user interaction. For example, in an email client, when the user is writing an email, an input chip might represent a contact the user has added to the "To:" field.

The following implementation demonstrates an input chip that is already in a selected state. The user dismisses the chip when they press it.

@Composable fun InputChipExample( text: String, onDismiss: () -> Unit, ) { var enabled by remember { mutableStateOf(true) } if (!enabled) return InputChip( onClick = { onDismiss() enabled = !enabled }, label = { Text(text) }, selected = enabled, avatar = { Icon( Icons.Filled.Person, contentDescription = "Localized description", Modifier.size(InputChipDefaults.AvatarSize) ) }, trailingIcon = { Icon( Icons.Default.Close, contentDescription = "Localized description", Modifier.size(InputChipDefaults.AvatarSize) ) }, ) }Chip.kt

This implementation appears as follows.

The SuggestionChip composable is the most basic of the composables listed on this page, both in its API definition and its common use cases. Suggestion chips present dynamically generated hints. For example, in an AI chat app, you might use suggestion chips to present possible responses to the most recent message.

Consider this implementation of SuggestionChip:

@Composable fun SuggestionChipExample() { SuggestionChip( onClick = { Log.d("Suggestion chip", "hello world") }, label = { Text("Suggestion chip") } ) }Chip.kt

This implementation appears as follows:

All the examples in this document use the base composables that take a flat appearance. If you want a chip that has an elevated appearance, use one of the three following composables:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Snackbar Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/snackbar

**Contents:**
- Snackbar Stay organized with collections Save and categorize content based on your preferences.
- Basic example
- Snackbar with action
  - Key points

The snackbar component serves as a brief notification that appears at the bottom of the screen. It provides feedback about an operation or action without interrupting the user experience. Snackbars disappear after a few seconds. The user can also dismiss them with an action, such as tapping a button.

Consider these three use cases where you might use a snackbar:

To implement a snackbar, you first create SnackbarHost, which includes a SnackbarHostState property. SnackbarHostState provides access to the showSnackbar() function which you can use to display your snackbar.

This suspending function requires a CoroutineScope—like the one returned by rememberCoroutineScope—and can be called in response to UI events to show a Snackbar within Scaffold.

val scope = rememberCoroutineScope() val snackbarHostState = remember { SnackbarHostState() } Scaffold( snackbarHost = { SnackbarHost(hostState = snackbarHostState) }, floatingActionButton = { ExtendedFloatingActionButton( text = { Text("Show snackbar") }, icon = { Icon(Icons.Filled.Image, contentDescription = "") }, onClick = { scope.launch { snackbarHostState.showSnackbar("Snackbar") } } ) } ) { contentPadding -> // Screen content }MaterialLayoutSnippets.kt

You can provide an optional action and adjust the duration of the Snackbar. The snackbarHostState.showSnackbar() function accepts additional actionLabel and duration parameters, and returns a SnackbarResult.

val scope = rememberCoroutineScope() val snackbarHostState = remember { SnackbarHostState() } Scaffold( snackbarHost = { SnackbarHost(hostState = snackbarHostState) }, floatingActionButton = { ExtendedFloatingActionButton( text = { Text("Show snackbar") }, icon = { Icon(Icons.Filled.Image, contentDescription = "") }, onClick = { scope.launch { val result = snackbarHostState .showSnackbar( message = "Snackbar", actionLabel = "Action", // Defaults to SnackbarDuration.Short duration = SnackbarDuration.Indefinite ) when (result) { SnackbarResult.ActionPerformed -> { /* Handle snackbar action performed */ } SnackbarResult.Dismissed -> { /* Handle snackbar dismissed */ } } } } ) } ) { contentPadding -> // Screen content }MaterialLayoutSnippets.kt

You can provide a custom Snackbar with the snackbarHost parameter. See the SnackbarHost API reference docs for more information.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Button Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/button

**Contents:**
- Button Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Filled button
- Filled tonal button
- Outlined button
- Elevated button
- Text button
- Additional resources

Buttons are fundamental components that allow the user to trigger a defined action. There are five types of buttons. This table describes the appearance of each of the five button types, as well as where you should use them:

This image demonstrates the five types of buttons in Material Design:

The filled button component uses the basic Button composable. It is filled with a solid color by default. The snippet shows how to implement the component:

@Composable fun FilledButtonExample(onClick: () -> Unit) { Button(onClick = { onClick() }) { Text("Filled") } }Button.kt

This implementation appears as shown:

The filled tonal button component uses the FilledTonalButton composable. It is filled with a tonal color by default.

The snippet shows how to implement the component:

@Composable fun FilledTonalButtonExample(onClick: () -> Unit) { FilledTonalButton(onClick = { onClick() }) { Text("Tonal") } }Button.kt

This implementation appears as shown:

The outlined button component uses the OutlinedButton composable. It appears with an outline by default.

The snippet shows how to implement the component:

@Composable fun OutlinedButtonExample(onClick: () -> Unit) { OutlinedButton(onClick = { onClick() }) { Text("Outlined") } }Button.kt

This implementation appears as shown:

The elevated button component uses the ElevatedButton composable. It has a shadow that represents the elevation effect by default. It is a filled button that includes a shadow.

The snippet shows how to implement the component:

@Composable fun ElevatedButtonExample(onClick: () -> Unit) { ElevatedButton(onClick = { onClick() }) { Text("Elevated") } }Button.kt

This implementation appears as shown:

The text button component uses the TextButton composable. It appears as only text until pressed. It does not have a solid fill or outline by default.

The snippet shows how to implement the component:

@Composable fun TextButtonExample(onClick: () -> Unit) { TextButton( onClick = { onClick() } ) { Text("Text Button") } }Button.kt

This implementation appears as shown:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Partial bottom sheet Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/bottom-sheets-partial

**Contents:**
- Partial bottom sheet Stay organized with collections Save and categorize content based on your preferences.
- Example
  - Key points about the code
  - Results
- Additional resources

You can partially show a bottom sheet and then let the user either make it full screen or dismiss it.

To do so, pass your ModalBottomSheet an instance of SheetState with skipPartiallyExpanded set to false.

This example demonstrates how you can use the sheetState property of ModalBottomSheet to display the sheet only partially at first:

@Composable fun PartialBottomSheet() { var showBottomSheet by remember { mutableStateOf(false) } val sheetState = rememberModalBottomSheetState( skipPartiallyExpanded = false, ) Column( modifier = Modifier.fillMaxWidth(), horizontalAlignment = Alignment.CenterHorizontally, ) { Button( onClick = { showBottomSheet = true } ) { Text("Display partial bottom sheet") } if (showBottomSheet) { ModalBottomSheet( modifier = Modifier.fillMaxHeight(), sheetState = sheetState, onDismissRequest = { showBottomSheet = false } ) { Text( "Swipe up to open sheet. Swipe down to dismiss.", modifier = Modifier.padding(16.dp) ) } } } }BottomSheet.kt

In this example, note the following:

When the user first presses the button, the sheet displays partially:

If the user swipes up on the sheet, it fills the screen:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Menus Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/menu

**Contents:**
- Menus Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Create a basic drop-down menu
  - Key points about the code
  - Result
- Create a longer drop-down menu
  - Key points about the code
  - Result
- Create a longer drop-down menu with dividers
  - Key points about the code

Drop-down menus let users click an icon, text field, or other component, and then select from a list of options on a temporary surface. This guide describes how to create both basic menus and more complex menus with dividers and icons.

Use DropdownMenu, DropdownMenuItem, and the IconButton components to implement a custom drop-down menu. The DropdownMenu and DropdownMenuItem components are used to display the menu items, while the IconButton is the trigger to display or hide the drop-down menu.

The key parameters for the DropdownMenu component include the following:

The key parameters for DropdownMenuItem include the following:

The following snippet demonstrates a minimal DropdownMenu implementation:

@Composable fun MinimalDropdownMenu() { var expanded by remember { mutableStateOf(false) } Box( modifier = Modifier .padding(16.dp) ) { IconButton(onClick = { expanded = !expanded }) { Icon(Icons.Default.MoreVert, contentDescription = "More options") } DropdownMenu( expanded = expanded, onDismissRequest = { expanded = false } ) { DropdownMenuItem( text = { Text("Option 1") }, onClick = { /* Do something... */ } ) DropdownMenuItem( text = { Text("Option 2") }, onClick = { /* Do something... */ } ) } } }Menus.kt

DropdownMenu is scrollable by default if all the menu items can't be displayed at once. The following snippet creates a longer, scrollable drop-down menu:

@Composable fun LongBasicDropdownMenu() { var expanded by remember { mutableStateOf(false) } // Placeholder list of 100 strings for demonstration val menuItemData = List(100) { "Option ${it + 1}" } Box( modifier = Modifier .padding(16.dp) ) { IconButton(onClick = { expanded = !expanded }) { Icon(Icons.Default.MoreVert, contentDescription = "More options") } DropdownMenu( expanded = expanded, onDismissRequest = { expanded = false } ) { menuItemData.forEach { option -> DropdownMenuItem( text = { Text(option) }, onClick = { /* Do something... */ } ) } } } }Menus.kt

The preceding code snippet produces the following scrollable menu:

The following snippet shows a more advanced implementation of a drop-down menu. In this snippet, leading and trailing icons are added to menu items, and dividers separate groups of menu items.

@Composable fun DropdownMenuWithDetails() { var expanded by remember { mutableStateOf(false) } Box( modifier = Modifier .fillMaxWidth() .padding(16.dp) ) { IconButton(onClick = { expanded = !expanded }) { Icon(Icons.Default.MoreVert, contentDescription = "More options") } DropdownMenu( expanded = expanded, onDismissRequest = { expanded = false } ) { // First section DropdownMenuItem( text = { Text("Profile") }, leadingIcon = { Icon(Icons.Outlined.Person, contentDescription = null) }, onClick = { /* Do something... */ } ) DropdownMenuItem( text = { Text("Settings") }, leadingIcon = { Icon(Icons.Outlined.Settings, contentDescription = null) }, onClick = { /* Do something... */ } ) HorizontalDivider() // Second section DropdownMenuItem( text = { Text("Send Feedback") }, leadingIcon = { Icon(Icons.Outlined.Feedback, contentDescription = null) }, trailingIcon = { Icon(Icons.AutoMirrored.Outlined.Send, contentDescription = null) }, onClick = { /* Do something... */ } ) HorizontalDivider() // Third section DropdownMenuItem( text = { Text("About") }, leadingIcon = { Icon(Icons.Outlined.Info, contentDescription = null) }, onClick = { /* Do something... */ } ) DropdownMenuItem( text = { Text("Help") }, leadingIcon = { Icon(Icons.AutoMirrored.Outlined.Help, contentDescription = null) }, trailingIcon = { Icon(Icons.AutoMirrored.Outlined.OpenInNew, contentDescription = null) }, onClick = { /* Do something... */ } ) } } }Menus.kt

This code defines a DropdownMenu within a Box.

The preceding snippet produces a drop-down menu with icons and dividers:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Card Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/card

**Contents:**
- Card Stay organized with collections Save and categorize content based on your preferences.
- Basic implementation
- Advanced implementations
  - Filled card
  - Elevated Card
  - Outlined Card
- Limitations
- Additional resources

The Card composable acts as a Material Design container for your UI. Cards typically present a single coherent piece of content. The following are some examples of where you might use a card:

It is the focus on portraying a single piece of content that distinguishes Card from other containers. For example, Scaffold provides general structure to a whole screen. Card is generally a smaller UI element inside a larger layout, whereas a layout component such as Column or Row provides a simpler and more generic API.

Card behaves much like other containers in Compose. You declare its content by calling other composables within it. For example, consider how Card contains a call to Text in the following minimal example:

See the reference for the API definition of Card. It defines several parameters that allow you customize the appearance and behavior of the component.

Some key parameters to note are the following:

The following example demonstrates how you might use these parameters, as well as other common parameters such as shape and modifier.

The following is an example of how you can implement a filled card.

The key here is the use of the colors property to change the filled color.

@Composable fun FilledCardExample() { Card( colors = CardDefaults.cardColors( containerColor = MaterialTheme.colorScheme.surfaceVariant, ), modifier = Modifier .size(width = 240.dp, height = 100.dp) ) { Text( text = "Filled", modifier = Modifier .padding(16.dp), textAlign = TextAlign.Center, ) } }Card.kt

This implementation appears as follows:

The following snippet demonstrates how to implement an elevated card. Use the dedicated ElevatedCard composable.

You can use the elevation property to control the appearance of elevation and the resulting shadow.

@Composable fun ElevatedCardExample() { ElevatedCard( elevation = CardDefaults.cardElevation( defaultElevation = 6.dp ), modifier = Modifier .size(width = 240.dp, height = 100.dp) ) { Text( text = "Elevated", modifier = Modifier .padding(16.dp), textAlign = TextAlign.Center, ) } }Card.kt

This implementation appears as follows:

The following is an example of an outlined card. Use the dedicated OutlinedCard composable.

@Composable fun OutlinedCardExample() { OutlinedCard( colors = CardDefaults.cardColors( containerColor = MaterialTheme.colorScheme.surface, ), border = BorderStroke(1.dp, Color.Black), modifier = Modifier .size(width = 240.dp, height = 100.dp) ) { Text( text = "Outlined", modifier = Modifier .padding(16.dp), textAlign = TextAlign.Center, ) } }Card.kt

This implementation appears as follows:

Cards don't come with inherent scroll or dismiss actions, but can integrate into composables offering these features. For example, to implement swipe to dismiss on a card, integrate it with the SwipeToDismiss composable. To integrate with scroll, use scroll modifiers such as verticalScroll. See the Scroll documentation for more information.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (swift):
```swift
@Composable
fun CardMinimalExample() {
    Card() {
        Text(text = "Hello, world!")
    }
}
```

---
