# Jetpack-Compose - Other

**Pages:** 131

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=component

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Layouts in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts

**Contents:**
- Layouts in Compose Stay organized with collections Save and categorize content based on your preferences.
- Learn more
- Recommended for you

Jetpack Compose makes it easy to design an efficient layout for your app.

The following pages provide details on how to design and implement your layout:

New! Try our Quick Guides to get a fast and focused content experience.

To learn more about Compose layouts, try the Layouts in Jetpack Compose codelab.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Radio button Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/radio-button

**Contents:**
- Radio button Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Create a basic radio button
  - Key points about the code
  - Result
- Additional resources

A radio button lets a user select an option from a set of options. You use a radio button when only one item can be selected from a list. If users need to select more than one item, use a switch instead.

Use the RadioButton composable to list the available options. Wrap each RadioButton option and its label inside a Row component to group them together.

RadioButton includes the following key parameters:

The following code snippet renders a list of radio buttons within a Column:

@Composable fun RadioButtonSingleSelection(modifier: Modifier = Modifier) { val radioOptions = listOf("Calls", "Missed", "Friends") val (selectedOption, onOptionSelected) = remember { mutableStateOf(radioOptions[0]) } // Note that Modifier.selectableGroup() is essential to ensure correct accessibility behavior Column(modifier.selectableGroup()) { radioOptions.forEach { text -> Row( Modifier .fillMaxWidth() .height(56.dp) .selectable( selected = (text == selectedOption), onClick = { onOptionSelected(text) }, role = Role.RadioButton ) .padding(horizontal = 16.dp), verticalAlignment = Alignment.CenterVertically ) { RadioButton( selected = (text == selectedOption), onClick = null // null recommended for accessibility with screen readers ) Text( text = text, style = MaterialTheme.typography.bodyLarge, modifier = Modifier.padding(start = 16.dp) ) } } } }RadioButton.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Create a scrollable grid Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/create-scrollable-grid

**Contents:**
- Create a scrollable grid Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Decide grid orientation
  - Create a scrollable grid
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components

You can manage large datasets and dynamic content with lazy grids, improving app performance. With lazy grid composables, you can display items in a scrollable container, spanned across multiple columns or rows.

Figure 1. A horizontal scrollable grid using LazyHorizontalGrid.

This implementation requires that your project minSDK be set to API level 21 or higher.

The LazyHorizontalGrid and LazyVerticalGrid composables provide support for displaying items in a grid. A lazy vertical grid displays its items in a vertically scrollable container, spanned across multiple columns, while lazy horizontal grids have the same behavior on the horizontal axis.

The following code creates a horizontal scrolling grid with three columns:

@Composable fun ScrollingGrid() { val itemsList = (0..15).toList() val itemModifier = Modifier .border(1.dp, Color.Blue) .width(80.dp) .wrapContentSize() LazyHorizontalGrid( rows = GridCells.Fixed(3), horizontalArrangement = Arrangement.spacedBy(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp) ) { items(itemsList) { Text("Item is $it", itemModifier) } item { Text("Single item", itemModifier) } } }LazyListSnippets.kt

To create a grid with as many rows as possible, set the number of rows using GridCells.Adaptive.

In the following code, the 20.dp value specifies that every column is at least 20.dp, and all columns have equal widths. If the screen is 88.dp wide, there are 4 columns at 22.dp each.

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Build UI with Glance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/build-ui

**Contents:**
- Build UI with Glance Stay organized with collections Save and categorize content based on your preferences.
- Use Box, Column, and Row
- Use scrollable layouts
- Define the SizeMode
  - SizeMode.Single
  - SizeMode.Responsive
  - SizeMode.Exact
- Access resources
- Handle text
- Add compound buttons

This page describes how to handle sizes and provide flexible and responsive layouts with Glance, using existing Glance components.

Glance has three main composable layouts:

Box: Places elements on top of another. It translates to a RelativeLayout.

Column: Places elements after each other in the vertical axis. It translates to a LinearLayout with vertical orientation.

Row: Places elements after each other in the horizontal axis. It translates to a LinearLayout with horizontal orientation.

Glance supports Scaffold objects. Place your Column, Row, and Box composables within a given Scaffold object.

Each of these composables lets you define the vertical and horizontal alignments of its content and the width, height, weight, or padding constraints using modifiers. In addition, each child can define its modifier to change the space and placement inside the parent.

The following example shows you how to create a Row that evenly distributes its children horizontally, as seen in Figure 1:

Row(modifier = GlanceModifier.fillMaxWidth().padding(16.dp)) { val modifier = GlanceModifier.defaultWeight() Text("first", modifier) Text("second", modifier) Text("third", modifier) }GlanceSnippets.kt

The Row fills the max available width, and because each child has the same weight, they evenly share the available space. You can define different weights, sizes, paddings, or alignments to adapt layouts to your needs.

Another way to provide responsive content is to make it scrollable. This is possible with the LazyColumn composable. This composable lets you define a set of items to be displayed inside a scrollable container in the app widget.

The following snippets show different ways to define items inside the LazyColumn.

You can provide the number of items:

// Remember to import Glance Composables // import androidx.glance.appwidget.layout.LazyColumn LazyColumn { items(10) { index: Int -> Text( text = "Item $index", modifier = GlanceModifier.fillMaxWidth() ) } }GlanceSnippets.kt

Provide individual items:

LazyColumn { item { Text("First Item") } item { Text("Second Item") } }GlanceSnippets.kt

Provide a list or array of items:

LazyColumn { items(peopleNameList) { name -> Text(name) } }GlanceSnippets.kt

You can also use a combination of the preceding examples:

LazyColumn { item { Text("Names:") } items(peopleNameList) { name -> Text(name) } // or in case you need the index: itemsIndexed(peopleNameList) { index, person -> Text("$person at index $index") } }GlanceSnippets.kt

Note that the previous snippet does not specify the itemId. Specifying the itemId helps with improving the performance and maintaining the scroll position through list and appWidget updates from Android 12 onwards (for example, when adding or removing items from the list). The following example shows how to specify an itemId:

items(items = peopleList, key = { person -> person.id }) { person -> Text(person.name) }GlanceSnippets.kt

AppWidget sizes may differ depending on the device, user choice, or launcher, so it is important to provide flexible layouts as described in the Provide flexible widget layouts page. Glance simplifies this with the SizeMode definition and the LocalSize value. The following sections describe the three modes.

SizeMode.Single is the default mode. It indicates that only one type of content is provided; that is, even if the AppWidget available size changes, the content size is not changed.

class MyAppWidget : GlanceAppWidget() { override val sizeMode = SizeMode.Single override suspend fun provideGlance(context: Context, id: GlanceId) { // ... provideContent { MyContent() } } @Composable private fun MyContent() { // Size will be the minimum size or resizable // size defined in the App Widget metadata val size = LocalSize.current // ... } }GlanceSnippets.kt

When using this mode, ensure that:

In general, you should use this mode when either:

a) the AppWidget has a fixed size, or b) it does not change its content when resized.

This mode is the equivalent of providing responsive layouts, which allows the GlanceAppWidget to define a set of responsive layouts bounded by specific sizes. For each defined size, the content is created and mapped to the specific size when the AppWidget is created or updated. The system then selects the best fitting one based on the available size.

For example, in our destination AppWidget, you can define three sizes and its content:

class MyAppWidget : GlanceAppWidget() { companion object { private val SMALL_SQUARE = DpSize(100.dp, 100.dp) private val HORIZONTAL_RECTANGLE = DpSize(250.dp, 100.dp) private val BIG_SQUARE = DpSize(250.dp, 250.dp) } override val sizeMode = SizeMode.Responsive( setOf( SMALL_SQUARE, HORIZONTAL_RECTANGLE, BIG_SQUARE ) ) override suspend fun provideGlance(context: Context, id: GlanceId) { // ... provideContent { MyContent() } } @Composable private fun MyContent() { // Size will be one of the sizes defined above. val size = LocalSize.current Column { if (size.height >= BIG_SQUARE.height) { Text(text = "Where to?", modifier = GlanceModifier.padding(12.dp)) } Row(horizontalAlignment = Alignment.CenterHorizontally) { Button() Button() if (size.width >= HORIZONTAL_RECTANGLE.width) { Button("School") } } if (size.height >= BIG_SQUARE.height) { Text(text = "provided by X") } } } } GlanceSnippets.kt

In the previous example, the provideContent method is called three times and mapped to the defined size.

SizeMode.Responsive is a combination of the other two modes, and lets you define responsive content within predefined bounds. In general, this mode performs better and allows smoother transitions when the AppWidget is resized.

The following table shows the value of the size, depending on the SizeMode and the AppWidget available size:

SizeMode.Exact is the equivalent of providing exact layouts, which requests the GlanceAppWidget content each time the available AppWidget size changes (for example, when the user resizes the AppWidget in the homescreen).

For example, in the destination widget, an extra button can be added if the available width is larger than a certain value.

class MyAppWidget : GlanceAppWidget() { override val sizeMode = SizeMode.Exact override suspend fun provideGlance(context: Context, id: GlanceId) { // ... provideContent { MyContent() } } @Composable private fun MyContent() { // Size will be the size of the AppWidget val size = LocalSize.current Column { Text(text = "Where to?", modifier = GlanceModifier.padding(12.dp)) Row(horizontalAlignment = Alignment.CenterHorizontally) { Button() Button() if (size.width > 250.dp) { Button("School") } } } } }GlanceSnippets.kt

This mode provides more flexibility than the others, but it comes with a few caveats:

In general, you should use this mode if SizeMode.Responsive cannot be used (that is, a small set of responsive layouts isn't feasible).

Use LocalContext.current to access any Android resource, as shown in the following example:

LocalContext.current.getString(R.string.glance_title)GlanceSnippets.kt

We recommend providing resource IDs directly to reduce the size of the final RemoteViews object and to enable dynamic resources, such as dynamic colors.

Composables and methods accept resources using a "provider", such as ImageProvider, or using an overload method like GlanceModifier.background(R.color.blue). For example:

Column( modifier = GlanceModifier.background(R.color.default_widget_background) ) { /**...*/ } Image( provider = ImageProvider(R.drawable.ic_logo), contentDescription = "My image", )GlanceSnippets.kt

Glance 1.1.0 includes an API to set your text styles. Set text styles using fontSize, fontWeight, or fontFamily attributes of the TextStyle class.

fontFamily supports all system fonts, as shown in the following example, but custom fonts in apps aren't supported:

Compound buttons were introduced in Android 12. Glance supports backwards compatibility for the following types of compound buttons:

These compound buttons each display a clickable view that represents the "checked" state.

var isApplesChecked by remember { mutableStateOf(false) } var isEnabledSwitched by remember { mutableStateOf(false) } var isRadioChecked by remember { mutableIntStateOf(0) } CheckBox( checked = isApplesChecked, onCheckedChange = { isApplesChecked = !isApplesChecked }, text = "Apples" ) Switch( checked = isEnabledSwitched, onCheckedChange = { isEnabledSwitched = !isEnabledSwitched }, text = "Enabled" ) RadioButton( checked = isRadioChecked == 1, onClick = { isRadioChecked = 1 }, text = "Checked" )GlanceSnippets.kt

When the state changes, the provided lambda is triggered. You can store the check state, as shown in the following example:

class MyAppWidget : GlanceAppWidget() { override suspend fun provideGlance(context: Context, id: GlanceId) { val myRepository = MyRepository.getInstance() provideContent { val scope = rememberCoroutineScope() val saveApple: (Boolean) -> Unit = { scope.launch { myRepository.saveApple(it) } } MyContent(saveApple) } } @Composable private fun MyContent(saveApple: (Boolean) -> Unit) { var isAppleChecked by remember { mutableStateOf(false) } Button( text = "Save", onClick = { saveApple(isAppleChecked) } ) } }GlanceSnippets.kt

You can also provide the colors attribute to CheckBox, Switch, and RadioButton to customize their colors:

CheckBox( // ... colors = CheckboxDefaults.colors( checkedColor = ColorProvider(day = colorAccentDay, night = colorAccentNight), uncheckedColor = ColorProvider(day = Color.DarkGray, night = Color.LightGray) ), checked = isChecked, onCheckedChange = { isChecked = !isChecked } ) Switch( // ... colors = SwitchDefaults.colors( checkedThumbColor = ColorProvider(day = Color.Red, night = Color.Cyan), uncheckedThumbColor = ColorProvider(day = Color.Green, night = Color.Magenta), checkedTrackColor = ColorProvider(day = Color.Blue, night = Color.Yellow), uncheckedTrackColor = ColorProvider(day = Color.Magenta, night = Color.Green) ), checked = isChecked, onCheckedChange = { isChecked = !isChecked }, text = "Enabled" ) RadioButton( // ... colors = RadioButtonDefaults.colors( checkedColor = ColorProvider(day = Color.Cyan, night = Color.Yellow), uncheckedColor = ColorProvider(day = Color.Red, night = Color.Blue) ), )GlanceSnippets.kt

Glance 1.1.0 includes the release of additional components, as described in the following table:

For more information on design specifics, see the component designs in this design kit on Figma.

For more information on canonical layouts visit Canonical widget layouts.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (swift):
```swift
Text(
    style = TextStyle(
        fontWeight = FontWeight.Bold,
        fontSize = 18.sp,
        fontFamily = FontFamily.Monospace
    ),
    text = "Example Text"
)
```

---

## Anatomy of a theme in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/anatomy

**Contents:**
- Anatomy of a theme in Compose Stay organized with collections Save and categorize content based on your preferences.
- Theme system classes
- Theme system composition locals
- Theme function
- Theme object
- Recommended for you

Themes in Jetpack Compose are made up of a number of lower-level constructs and related APIs. These can be seen in the source code of MaterialTheme and can also be applied in custom design systems.

A theme is typically made up of a number of subsystems that group common visual and behavioral concepts. These systems can be modeled with classes which have theming values.

For example, MaterialTheme includes ColorScheme (color system), Typography (typography system), and Shapes (shape system).

@Immutable data class ColorSystem( val color: Color, val gradient: List<Color> /* ... */ ) @Immutable data class TypographySystem( val fontFamily: FontFamily, val textStyle: TextStyle ) /* ... */ @Immutable data class CustomSystem( val value1: Int, val value2: String /* ... */ ) /* ... */ThemeAnatomySnippets.kt

Theme system classes are implicitly provided to the composition tree as CompositionLocal instances. This allows theming values to be statically referenced in composable functions.

To learn more about CompositionLocal, check out the Locally scoped data with CompositionLocal guide.

val LocalColorSystem = staticCompositionLocalOf { ColorSystem( color = Color.Unspecified, gradient = emptyList() ) } val LocalTypographySystem = staticCompositionLocalOf { TypographySystem( fontFamily = FontFamily.Default, textStyle = TextStyle.Default ) } val LocalCustomSystem = staticCompositionLocalOf { CustomSystem( value1 = 0, value2 = "" ) } /* ... */ThemeAnatomySnippets.kt

The theme function is the entry point and primary API. It constructs instances of the theme system CompositionLocals — using real values any logic required — that are provided to the composition tree with CompositionLocalProvider. The content parameter allows nested composables to access theming values relative to the hierarchy.

@Composable fun Theme( /* ... */ content: @Composable () -> Unit ) { val colorSystem = ColorSystem( color = Color(0xFF3DDC84), gradient = listOf(Color.White, Color(0xFFD7EFFF)) ) val typographySystem = TypographySystem( fontFamily = FontFamily.Monospace, textStyle = TextStyle(fontSize = 18.sp) ) val customSystem = CustomSystem( value1 = 1000, value2 = "Custom system" ) /* ... */ CompositionLocalProvider( LocalColorSystem provides colorSystem, LocalTypographySystem provides typographySystem, LocalCustomSystem provides customSystem, /* ... */ content = content ) }ThemeAnatomySnippets.kt

Accessing theme systems is done using an object with convenience properties. For consistency, the object tends to be named the same as the theme function. The properties simply get the current composition local.

// Use with eg. Theme.colorSystem.color object Theme { val colorSystem: ColorSystem @Composable get() = LocalColorSystem.current val typographySystem: TypographySystem @Composable get() = LocalTypographySystem.current val customSystem: CustomSystem @Composable get() = LocalCustomSystem.current /* ... */ }ThemeAnatomySnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Canonical layouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/canonical-layouts

**Contents:**
- Canonical layouts Stay organized with collections Save and categorize content based on your preferences.
- List-detail
  - Implementation
- Feed
  - Implementation
- Supporting pane
  - Implementation
- Additional resources

Canonical layouts are proven, versatile layouts that provide an optimal user experience on a variety of form factors.

The canonical layouts support small screen phones as well as tablets, foldables, and ChromeOS devices. Derived from Material Design guidance, the layouts are aesthetic as well as functional.

The Android framework includes specialized components that make implementation of the layouts straightforward and reliable.

The canonical layouts create engaging, productivity‑enhancing UIs that form the foundation of great apps.

The list-detail layout enables users to explore lists of items that have descriptive, explanatory, or other supplementary information—the item detail.

The layout divides the app window into two side-by-side panes: one for the list, one for the detail. Users select items from the list to display the item detail. Deep links in the detail reveal additional content in the detail pane.

Expanded-width displays (see Use window size classes) accommodate both the list and detail at the same time. Selection of a list item updates the detail pane to show the related content for the selected item.

Medium- and compact-width displays show either the list or the detail, depending on user interaction with the app. When just the list is visible, selection of a list item displays the detail in place of the list. When just the detail is visible, pressing the back button redisplays the list.

Configuration changes such as device orientation changes or app window size changes can change the display's window size class. A list‑detail layout responds accordingly, preserving app state:

List-detail is ideal for messaging apps, contact managers, interactive media browsers or any app where the content can be organized as a list of items that reveal additional information.

The declarative paradigm of Compose supports window size class logic that determines whether to show the list and detail panes at the same time (when the width window size class is expanded) or just the list or detail pane (when the width window size class is medium or compact).

To ensure unidirectional data flow, hoist all state, including current window size class and detail of the selected list item (if any), so all composables have access to the data and can render correctly.

When showing just the detail pane on small window sizes, add a BackHandler to remove the detail pane and display just the list pane. The BackHandler is not part of the overall app navigation since the handler is dependent on the window size class and selected detail state.

For an example implementation, see the List-detail with Compose sample.

A feed layout arranges equivalent content elements in a configurable grid for quick, convenient viewing of a large amount of content.

Size and position establish relationships among the content elements.

Content groups are created by making elements the same size and positioning them together. Attention is drawn to elements by making them larger than nearby elements.

Cards and lists are common components of feed layouts.

A feed layout supports displays of almost any size because the grid can adapt from a single, scrolling column to a multi‑column scrolling feed of content.

Feeds are especially well suited for news and social media apps.

A feed consists of a large number of content elements in a vertical scrolling container laid out in a grid. Lazy lists efficiently render a large number of items in columns or rows. Lazy grids render items in grids, supporting configuration of the item sizes and spans.

Configure the columns of the grid layout based on the available display area to set the minimum allowable width for grid items. When defining grid items, adjust column spans to emphasize some items over others.

For section headers, dividers, or other items designed to occupy the full width of the feed, use maxLineSpan to take up the full width of the layout.

On compact-width displays that don't have enough space to show more than one column, LazyVerticalGrid behaves just like a LazyColumn.

For an example implementation, see the Feed with Compose sample.

Supporting pane layout organizes app content into primary and secondary display areas.

The primary display area occupies the majority of the app window (typically about two thirds) and contains the main content. The secondary display area is a pane that takes up the remainder of the app window and presents content that supports the main content.

Supporting pane layouts work well on expanded-width displays (see Use window size classes) in landscape orientation. Medium- or compact‑width displays support showing both the primary and secondary display areas if the content is adaptable to narrower display spaces, or if the additional content can be initially hidden in a bottom or side sheet accessible by means of a control such as a menu or button.

A supporting pane layout differs from a list‑detail layout in the relationship of the primary and secondary content. Secondary pane content is meaningful only in relation to the primary content; for example, a supporting pane tool window is irrelevant by itself. The supplementary content in the detail pane of a list‑detail layout, however, is meaningful even without the primary content, for example, the description of a product from a product listing.

Use cases for supporting pane include:

Compose supports window size class logic, which enables you to determine whether to show both the main content and the supporting content at the same time or place the supporting content in an alternative location.

Hoist all state, including current window size class and information related to the data in the main content and supporting content.

For compact-width displays, place the supporting content below the main content or inside a bottom sheet. For medium and expanded widths, place the supporting content next to the main content, sized appropriately based on the content and space available. For medium width, split the display space equally between the main and supporting content. For expanded width, give 70% of the space to the main content, 30% to the supporting content.

For an example implementation, see the Supporting pane with Compose sample.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=animate

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Dialogs for time pickers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/time-pickers-dialogs

**Contents:**
- Dialogs for time pickers Stay organized with collections Save and categorize content based on your preferences.
- Basic example
- Advanced example
- Additional resources

Time pickers often appear in dialogs. You can use a relatively generic and minimal implementation of a dialog, or you can implement a custom dialog with more flexibility.

For more information on dialogs in general, including how to use the time picker state, see the Time pickers guide.

The most straightforward way to create a dialog for your time picker is to create a composable that implements AlertDialog. The following snippet provides an example of a relatively minimal dialog using this approach:

@Composable fun DialWithDialogExample( onConfirm: (TimePickerState) -> Unit, onDismiss: () -> Unit, ) { val currentTime = Calendar.getInstance() val timePickerState = rememberTimePickerState( initialHour = currentTime.get(Calendar.HOUR_OF_DAY), initialMinute = currentTime.get(Calendar.MINUTE), is24Hour = true, ) TimePickerDialog( onDismiss = { onDismiss() }, onConfirm = { onConfirm(timePickerState) } ) { TimePicker( state = timePickerState, ) } } @Composable fun TimePickerDialog( onDismiss: () -> Unit, onConfirm: () -> Unit, content: @Composable () -> Unit ) { AlertDialog( onDismissRequest = onDismiss, dismissButton = { TextButton(onClick = { onDismiss() }) { Text("Dismiss") } }, confirmButton = { TextButton(onClick = { onConfirm() }) { Text("OK") } }, text = { content() } ) }TimePickers.kt

Note the key points in this snippet:

This snippet demonstrates an advanced implementation of a customizable time picker dialog in Jetpack Compose.

@Composable fun AdvancedTimePickerExample( onConfirm: (TimePickerState) -> Unit, onDismiss: () -> Unit, ) { val currentTime = Calendar.getInstance() val timePickerState = rememberTimePickerState( initialHour = currentTime.get(Calendar.HOUR_OF_DAY), initialMinute = currentTime.get(Calendar.MINUTE), is24Hour = true, ) /** Determines whether the time picker is dial or input */ var showDial by remember { mutableStateOf(true) } /** The icon used for the icon button that switches from dial to input */ val toggleIcon = if (showDial) { Icons.Filled.EditCalendar } else { Icons.Filled.AccessTime } AdvancedTimePickerDialog( onDismiss = { onDismiss() }, onConfirm = { onConfirm(timePickerState) }, toggle = { IconButton(onClick = { showDial = !showDial }) { Icon( imageVector = toggleIcon, contentDescription = "Time picker type toggle", ) } }, ) { if (showDial) { TimePicker( state = timePickerState, ) } else { TimeInput( state = timePickerState, ) } } } @Composable fun AdvancedTimePickerDialog( title: String = "Select Time", onDismiss: () -> Unit, onConfirm: () -> Unit, toggle: @Composable () -> Unit = {}, content: @Composable () -> Unit, ) { Dialog( onDismissRequest = onDismiss, properties = DialogProperties(usePlatformDefaultWidth = false), ) { Surface( shape = MaterialTheme.shapes.extraLarge, tonalElevation = 6.dp, modifier = Modifier .width(IntrinsicSize.Min) .height(IntrinsicSize.Min) .background( shape = MaterialTheme.shapes.extraLarge, color = MaterialTheme.colorScheme.surface ), ) { Column( modifier = Modifier.padding(24.dp), horizontalAlignment = Alignment.CenterHorizontally ) { Text( modifier = Modifier .fillMaxWidth() .padding(bottom = 20.dp), text = title, style = MaterialTheme.typography.labelMedium ) content() Row( modifier = Modifier .height(40.dp) .fillMaxWidth() ) { toggle() Spacer(modifier = Modifier.weight(1f)) TextButton(onClick = onDismiss) { Text("Cancel") } TextButton(onClick = onConfirm) { Text("OK") } } } } } }TimePickers.kt

Note the key points in this snippet:

This advanced implementation provides a highly customizable and reusable time picker dialog that can adapt to different use cases in your app.

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Learn about foldables Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/learn-about-foldables

**Contents:**
- Learn about foldables Stay organized with collections Save and categorize content based on your preferences.
- Responsive/adaptive design
- Foldable states and postures
- App continuity
- Multitasking
- Drag and drop
- Additional resources

Foldable devices provide an opportunity for innovative app development. Large and small screens on the same device offer complementary but distinct interactive experiences. Folding features such as tabletop posture and book posture enable imaginative layouts and unconventional user interfaces.

Support for foldable devices begins with responsive design. Responsive layouts enable an app to look and work great on a range of display sizes. Implement responsive design with the BoxWithConstraints composable.

But to optimally support the folded and unfolded screens of a foldable device, the layout needs to adapt. The differences in screen size and aspect ratio of folded and unfolded screens can be quite large, such that even a responsive layout can't adequately accommodate both displays. Adaptive design creates alternative layouts optimized for different screen sizes and configurations. Adaptive layouts provide an optimized user experience when a foldable device is folded or unfolded, in portrait or landscape orientation, or in tabletop or book posture.

For example, a large screen foldable device unfolded in landscape orientation is like a tablet; a two‑pane layout with a navigation rail makes excellent use of the wide screen. Folded, the device is similar to a standard phone; a single column layout with a bottom navigation bar is straightforward but effective. Because the layouts are separate, you can optimize each for its specific use case.

Foldable devices fold in a variety of ways, such as inward, with the display folding into the interior of the device, or outward, with the display wrapping around the device. Responsive/adaptive design prepares your app to support all kinds of foldable form factors.

To learn more about responsive/adaptive design for foldables, see the following:

The fold of a foldable device divides the screen into two portions. The fold can be a flexible area of the screen or, on dual‑screen devices, a hinge that separates the two displays.

The fold has dimension and an occlusionType property, which defines whether the fold obscures part of the display. On dual‑screen devices, the occlusionType is FULL, no content is viewable in the fold (hinge) area even though an app might span both screens.

Foldable devices can be in various folded states, such as FLAT (fully open) or HALF_OPENED (somewhere between fully open and completely closed).

When a device is in the HALF_OPENED state, two postures are possible, depending on the orientation of the fold: tabletop posture (horizontal fold) and book posture (vertical fold).

Tabletop and book postures offer new layout possibilities, but the HALF_OPENED device state also imposes some limitations. For example, UI controls near the fold can be difficult for users to access, and text overlaying the fold can be hard to read (or unreadable if occlusionType is FULL).

Design your layouts so that UI elements are accessible in all device states. Position dialog boxes and pop‑up menus so they don't overlay the fold. Make sure important content is viewable when the device is partially folded. Split content into two areas when the device is half opened—top and bottom in tabletop posture, left and right in book posture.

For more information about folds and foldable postures, see Make your app fold aware.

An app stops and restarts as it transitions from one screen to another when a device folds or unfolds. To maintain continuity for the user, the app should restore its state when recreating the app layout on a folded or unfolded screen. For example, apps should do the following:

The different screen layouts of a foldable device should also complement one another. For example, if the folded screen shows an image and description for a product from an online store, the unfolded screen should maintain continuity by showing the same image and description, but also include complementary content, such as product specifications or reviews.

To learn more about managing app state and continuity, see Save UI states and Handle configuration changes.

Large screen foldables have a tablet‑sized screen that's ideal for multitasking in multi‑window mode. Foldables support split‑screen mode; some even support desktop windowing mode, where apps are contained in movable, resizable windows, similar to a desktop windowing system.

Android 12 (API level 31) and later versions default to multi‑window mode—on large screens, all apps run in multi‑window mode regardless of app configuration. On previous versions down to Android 7.0 (API level 24), you must configure your app to be resizable to support multi‑window mode.

For information about multitasking in multi‑window mode, see Support multi-window mode.

Large screen foldable devices provide ample screen space for drag and drop interactions. Multi‑window mode on foldables enables drag and drop between apps.

Drag and drop interactions create a productive and engaging user experience. Add drag and drop capabilities to your app using the Android drag and drop framework. For more information, see Enable drag and drop.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-12-20 UTC.

---

## Shared element transitions in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/shared-elements

**Contents:**
- Shared element transitions in Compose Stay organized with collections Save and categorize content based on your preferences.
- Basic usage
- Shared bounds versus shared element
- Understand scopes
- Shared elements with AnimatedVisibility
- Modifier ordering
- Unique keys
- Manage the visibility of shared elements manually
- Current limitations

Shared element transitions are a seamless way to transition between composables that have content that is consistent between them. They are often used for navigation, allowing you to visually connect different screens as a user navigates between them.

For example, in the following video, you can see the image and title of the snack are shared from the listing page, to the detail page.

In Compose, there are a few high level APIs that help you create shared elements:

An important concept when creating shared elements in Compose is how they work with overlays and clipping. See the clipping and overlays section to learn more about this important topic.

The following transition will be built in this section, transitioning from the smaller "list" item, to the larger detailed item:

The best way to use Modifier.sharedElement() is in conjunction with AnimatedContent, AnimatedVisibility, or NavHost, as this manages the transition between composables automatically for you.

The starting point is an existing basic AnimatedContent that has a MainContent, and DetailsContent composable before adding shared elements: Figure 3. Starting AnimatedContent without any shared element transitions.

To make the shared elements animate between the two layouts, surround the AnimatedContent composable with SharedTransitionLayout. The scopes from SharedTransitionLayout and AnimatedContent are passed to the MainContent and DetailsContent:

var showDetails by remember { mutableStateOf(false) } SharedTransitionLayout { AnimatedContent( showDetails, label = "basic_transition" ) { targetState -> if (!targetState) { MainContent( onShowDetails = { showDetails = true }, animatedVisibilityScope = this@AnimatedContent, sharedTransitionScope = this@SharedTransitionLayout ) } else { DetailsContent( onBack = { showDetails = false }, animatedVisibilityScope = this@AnimatedContent, sharedTransitionScope = this@SharedTransitionLayout ) } } }BasicSharedElementSnippets.kt

Add Modifier.sharedElement() to your composable modifier chain on the two composables that match. Create a SharedContentState object and remember it with rememberSharedContentState(). The SharedContentState object is storing the unique key which determines the elements that are shared. Provide a unique key to identify the content, and use rememberSharedContentState() for the item to be remembered. The AnimatedContentScope is passed into the modifier, which is used to coordinate the animation.

@Composable private fun MainContent( onShowDetails: () -> Unit, modifier: Modifier = Modifier, sharedTransitionScope: SharedTransitionScope, animatedVisibilityScope: AnimatedVisibilityScope ) { Row( // ... ) { with(sharedTransitionScope) { Image( painter = painterResource(id = R.drawable.cupcake), contentDescription = "Cupcake", modifier = Modifier .sharedElement( rememberSharedContentState(key = "image"), animatedVisibilityScope = animatedVisibilityScope ) .size(100.dp) .clip(CircleShape), contentScale = ContentScale.Crop ) // ... } } } @Composable private fun DetailsContent( modifier: Modifier = Modifier, onBack: () -> Unit, sharedTransitionScope: SharedTransitionScope, animatedVisibilityScope: AnimatedVisibilityScope ) { Column( // ... ) { with(sharedTransitionScope) { Image( painter = painterResource(id = R.drawable.cupcake), contentDescription = "Cupcake", modifier = Modifier .sharedElement( rememberSharedContentState(key = "image"), animatedVisibilityScope = animatedVisibilityScope ) .size(200.dp) .clip(CircleShape), contentScale = ContentScale.Crop ) // ... } } }BasicSharedElementSnippets.kt

To get information on if a shared element match has occurred, extract rememberSharedContentState() into a variable, and query isMatchFound.

This results in the following automatic animation:

You may notice that the background color and size of the whole container still uses the default AnimatedContent settings.

Modifier.sharedBounds() is similar to Modifier.sharedElement(). However, the modifiers are different in the following ways:

From the previous example, adding Modifier.sharedBounds() onto the Row and Column in the two different scenarios will allow us to share the bounds of the two and perform the transition animation, allowing them to grow between each other:

@Composable private fun MainContent( onShowDetails: () -> Unit, modifier: Modifier = Modifier, sharedTransitionScope: SharedTransitionScope, animatedVisibilityScope: AnimatedVisibilityScope ) { with(sharedTransitionScope) { Row( modifier = Modifier .padding(8.dp) .sharedBounds( rememberSharedContentState(key = "bounds"), animatedVisibilityScope = animatedVisibilityScope, enter = fadeIn(), exit = fadeOut(), resizeMode = SharedTransitionScope.ResizeMode.scaleToBounds() ) // ... ) { // ... } } } @Composable private fun DetailsContent( modifier: Modifier = Modifier, onBack: () -> Unit, sharedTransitionScope: SharedTransitionScope, animatedVisibilityScope: AnimatedVisibilityScope ) { with(sharedTransitionScope) { Column( modifier = Modifier .padding(top = 200.dp, start = 16.dp, end = 16.dp) .sharedBounds( rememberSharedContentState(key = "bounds"), animatedVisibilityScope = animatedVisibilityScope, enter = fadeIn(), exit = fadeOut(), resizeMode = SharedTransitionScope.ResizeMode.scaleToBounds() ) // ... ) { // ... } } }SharedBoundsSnippets.kt

To use Modifier.sharedElement(), the composable needs to be in a SharedTransitionScope. The SharedTransitionLayout composable provides the SharedTransitionScope. Make sure to place at the same top-level point in your UI hierarchy that contains the elements you want to share.

Generally, the composables should also be placed inside an AnimatedVisibilityScope. This is typically provided by using AnimatedContent to switch between composables or when using AnimatedVisibility directly, or by the NavHost composable function, unless you manage the visibility manually. In order to use multiple scopes, save your required scopes in a CompositionLocal, use context receivers in Kotlin, or pass the scopes as parameters to your functions.

Use CompositionLocals in the scenario where you have multiple scopes to keep track of, or a deeply nested hierarchy. A CompositionLocal lets you choose the exact scopes to save and use. On the other hand, when you use context receivers, other layouts in your hierarchy might accidentally override the provided scopes. For example, if you have multiple nested AnimatedContent, the scopes could be overridden.

val LocalNavAnimatedVisibilityScope = compositionLocalOf<AnimatedVisibilityScope?> { null } val LocalSharedTransitionScope = compositionLocalOf<SharedTransitionScope?> { null } @Composable private fun SharedElementScope_CompositionLocal() { // An example of how to use composition locals to pass around the shared transition scope, far down your UI tree. // ... SharedTransitionLayout { CompositionLocalProvider( LocalSharedTransitionScope provides this ) { // This could also be your top-level NavHost as this provides an AnimatedContentScope AnimatedContent(state, label = "Top level AnimatedContent") { targetState -> CompositionLocalProvider(LocalNavAnimatedVisibilityScope provides this) { // Now we can access the scopes in any nested composables as follows: val sharedTransitionScope = LocalSharedTransitionScope.current ?: throw IllegalStateException("No SharedElementScope found") val animatedVisibilityScope = LocalNavAnimatedVisibilityScope.current ?: throw IllegalStateException("No AnimatedVisibility found") } // ... } } } }BasicSharedElementSnippets.kt

Alternatively, if your hierarchy isn't deeply nested you can pass the scopes down as parameters:

@Composable fun MainContent( animatedVisibilityScope: AnimatedVisibilityScope, sharedTransitionScope: SharedTransitionScope ) { } @Composable fun Details( animatedVisibilityScope: AnimatedVisibilityScope, sharedTransitionScope: SharedTransitionScope ) { }BasicSharedElementSnippets.kt

Previous examples showed how to use shared elements with AnimatedContent, but shared elements work with AnimatedVisibility too.

For example, in this lazy grid example, each element is wrapped in AnimatedVisibility. When the item is clicked on, the content has the visual effect of being pulled out of the UI into a dialog-like component.

var selectedSnack by remember { mutableStateOf<Snack?>(null) } SharedTransitionLayout(modifier = Modifier.fillMaxSize()) { LazyColumn( // ... ) { items(listSnacks) { snack -> AnimatedVisibility( visible = snack != selectedSnack, enter = fadeIn() + scaleIn(), exit = fadeOut() + scaleOut(), modifier = Modifier.animateItem() ) { Box( modifier = Modifier .sharedBounds( sharedContentState = rememberSharedContentState(key = "${snack.name}-bounds"), // Using the scope provided by AnimatedVisibility animatedVisibilityScope = this, clipInOverlayDuringTransition = OverlayClip(shapeForSharedElement) ) .background(Color.White, shapeForSharedElement) .clip(shapeForSharedElement) ) { SnackContents( snack = snack, modifier = Modifier.sharedElement( sharedContentState = rememberSharedContentState(key = snack.name), animatedVisibilityScope = this@AnimatedVisibility ), onClick = { selectedSnack = snack } ) } } } } // Contains matching AnimatedContent with sharedBounds modifiers. SnackEditDetails( snack = selectedSnack, onConfirmClick = { selectedSnack = null } ) }AnimatedVisibilitySharedElementSnippets.kt

With Modifier.sharedElement() and Modifier.sharedBounds(), the order of your modifier chain matters, as with the rest of Compose. The incorrect placement of size-affecting modifiers can cause unexpected visual jumps during shared element matching.

For example, if you place a padding modifier in a different position on two shared elements, there is a visual difference in the animation.

var selectFirst by remember { mutableStateOf(true) } val key = remember { Any() } SharedTransitionLayout( Modifier .fillMaxSize() .padding(10.dp) .clickable { selectFirst = !selectFirst } ) { AnimatedContent(targetState = selectFirst, label = "AnimatedContent") { targetState -> if (targetState) { Box( Modifier .padding(12.dp) .sharedBounds( rememberSharedContentState(key = key), animatedVisibilityScope = this@AnimatedContent ) .border(2.dp, Color.Red) ) { Text( "Hello", fontSize = 20.sp ) } } else { Box( Modifier .offset(180.dp, 180.dp) .sharedBounds( rememberSharedContentState( key = key, ), animatedVisibilityScope = this@AnimatedContent ) .border(2.dp, Color.Red) // This padding is placed after sharedBounds, but it doesn't match the // other shared elements modifier order, resulting in visual jumps .padding(12.dp) ) { Text( "Hello", fontSize = 36.sp ) } } } }BasicSharedElementSnippets.kt

Unmatched bounds: Notice how the shared element animation appears a bit off as it needs to resize to the incorrect bounds

The modifiers used before the shared element modifiers provide constraints to the shared element modifiers, which are then used to derive the initial and target bounds, and subsequently the bounds animation.

The modifiers used after the shared element modifiers use the constraints from before to measure and calculate the child's target size. The shared element modifiers create a series of animated constraints to gradually transform the child from the initial size to the target size.

The exception to this is if you use resizeMode = ScaleToBounds() for the animation, or Modifier.skipToLookaheadSize() on a composable. In this case, Compose lays out the child using the target constraints, and instead uses a scale factor to perform the animation instead of changing the layout size itself.

When working with complex shared elements, it is a good practice to create a key that is not a string, because strings can be error prone to match. Each key must be unique for matches to occur. For example, in Jetsnack we have the following shared elements:

You could create an enum to represent the shared element type. In this example the whole snack card can also appear from multiple different places on the home screen, for example in a "Popular" and a "Recommended" section. You can create a key that has the snackId, the origin ("Popular" / "Recommended"), and the type of the shared element that will be shared:

data class SnackSharedElementKey( val snackId: Long, val origin: String, val type: SnackSharedElementType ) enum class SnackSharedElementType { Bounds, Image, Title, Tagline, Background } @Composable fun SharedElementUniqueKey() { // ... Box( modifier = Modifier .sharedElement( rememberSharedContentState( key = SnackSharedElementKey( snackId = 1, origin = "latest", type = SnackSharedElementType.Image ) ), animatedVisibilityScope = this@AnimatedVisibility ) ) // ... }BasicSharedElementSnippets.kt

Data classes are recommended for keys since they implement hashCode() and isEquals().

In cases where you may not be using AnimatedVisibility or AnimatedContent, you can manage the shared element visibility yourself. Use Modifier.sharedElementWithCallerManagedVisibility() and provide your own conditional that determines when an item should be visible or not:

var selectFirst by remember { mutableStateOf(true) } val key = remember { Any() } SharedTransitionLayout( Modifier .fillMaxSize() .padding(10.dp) .clickable { selectFirst = !selectFirst } ) { Box( Modifier .sharedElementWithCallerManagedVisibility( rememberSharedContentState(key = key), !selectFirst ) .background(Color.Red) .size(100.dp) ) { Text(if (!selectFirst) "false" else "true", color = Color.White) } Box( Modifier .offset(180.dp, 180.dp) .sharedElementWithCallerManagedVisibility( rememberSharedContentState( key = key, ), selectFirst ) .alpha(0.5f) .background(Color.Blue) .size(180.dp) ) { Text(if (selectFirst) "false" else "true", color = Color.White) } }BasicSharedElementSnippets.kt

These APIs have a few limitations. Most notably:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Five quick animations in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/video/five-animations-compose

**Contents:**
- Five quick animations in Compose Stay organized with collections Save and categorize content based on your preferences.
- Key points
- Resources
- Collections that contain this guide
  - Display images
- Have questions or feedback

These 5 quick and easy animations can help make your app come to life in just a few minutes. Make your Compose app stand out even if you don't have the time to learn everything there is to know about animations.

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Adaptive do's and don'ts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/adaptive-dos-and-donts

**Contents:**
- Adaptive do's and don'ts Stay organized with collections Save and categorize content based on your preferences.
- Resizability
- Orientation
- Aspect ratio
- Window size
- Deprecated APIs
- Compose
- Compose Material 3 Adaptive library
- Layouts
- Input devices

Adaptive apps support displays of all sizes: the entire device screen, resizable windows in multi‑window mode, portrait and landscape orientations, folded and unfolded displays of foldable devices.

A short list of configuration settings and APIs enable you to build adaptive apps. But some outdated settings and APIs are incompatible with adaptive apps and must be avoided.

Adaptive apps support app resizability and multi‑window mode.

The resizeableActivity attribute of the <activity> and <application> manifest elements enables or disables multi‑window mode on Android 11 (API level 30) and lower. On Android 12 (API level 31) and higher, large screens support multi‑window mode regardless of the attribute. For more information, see Support multi-window mode.

Enable your app to participant in multi‑window, multitasking scenarios for increased user productivity and satisfaction.

Set resizeableActivity="true" if your app targets API levels lower than 24; otherwise, forget about it—it's true by default on Android 7.0 (API level 24) and higher.

Don't set resizeableActivity="false" for any API level. Don't exclude your app from use cases that involve multi‑window mode.

Adaptive apps support portrait and landscape orientation regardless of display size or windowing mode.

The screenOrientation manifest setting restricts activity orientation.

Eliminate the screenOrientation setting from your app manifest.

Locking the orientation of apps doesn't prevent window size changes. Apps are resized when they enter multi-window mode, when a device is folded or unfolded, or when a desktop‑type window is resized. Your app has to support changes in window size regardless of the screenOrientation attribute setting.

Don't restrict activity orientation. Apps that lock orientation are letterboxed on large screen devices and incompatible window sizes.

Letterboxed apps are subject to decreased discoverability on Google Play for tablets, foldables, and ChromeOS devices.

As screen and window sizes vary, so do their aspect ratios—from tall and narrow, to square, to short and wide.

The minAspectRatio and maxAspectRatio manifest settings restrict your app's aspect ratio to hard‑coded values.

Adapt your app to fit the display regardless of relative dimensions.

Eliminate the minAspectRatio and maxAspectRatio settings from your app manifest. Or ensure your app is resizable, and aspect ratio takes care of itself (see the Resizability section).

Don't try to control the relative dimensions of your app. If your app runs on a screen or in a window that has an aspect ratio that's incompatible with the aspect ratio of the app, your app is letterboxed.

On Android 14 (API level 34) and higher, users can override the app aspect ratio to expand letterboxed apps to fill the available display area. See Device compatibility mode.

Optimizing layouts for different display sizes is the central premise of adaptive design. Adaptive apps focus on app window size rather than device screen size. When the app is full screen, the app window is the device screen.

Window size classes provide a systematic way of determining and categorizing the size of the app window. Adapt your app by changing layouts as the window size class of your app changes.

Evaluate your app window size based on window size classes.

To determine the window size class, use the currentWindowAdaptiveInfo() top‑level function of the Compose Material 3 Adaptive library. For more information, see Build adaptive apps.

Don't disregard the utility of the window size class definitions and the built‑in APIs. Don't use deprecated APIs to calculate window size.

Older platform APIs don't correctly measure the app window; some measure the device screen, some exclude system decor.

Use WindowManager#getCurrentWindowMetrics() and WindowMetrics#getBounds() to get the size of the app window. Use WindowMetrics#getDensity() to get the display density.

Don't use the following deprecated Display APIs to determine window size:

Jetpack Compose is designed for adaptive UI development. No XML, no layout files, no resource qualifiers. Just Kotlin‑based, stateless composables like Column, Row, and Box that describe your UI, and modifiers like offset, padding, and size that add behavior to UI elements.

Build with Compose. Stay up to date with the latest features and releases.

Don't rely on outdated technology. Don't let your app become obsolete.

The Compose Material 3 Adaptive library provides components and APIs that facilitate the development of adaptive apps.

Use the following APIs to make your app adaptive:

Don't reinvent the wheel. Don't miss out on the developer productivity gains provided by all the Jetpack Compose libraries.

Users expect apps to make the most of available display space with supplemental content or enhanced controls.

Adaptive apps optimize layouts based on changes in the display, in particular, changes in the size of the app window or changes in device posture.

Change UI components as the window size changes to take advantage of available display space. For example, swap the bottom navigation bar used on compact window sizes for a vertical navigation rail on medium and expanded windows. Reposition dialogs to be reachable on all displays.

Organize content into panes to enable multi‑pane layouts like list‑detail and supporting pane for dynamic content displays.

If you're not using content panes, don't just stretch UI elements to fill available display space. Long lines of text are difficult to read. Stretched buttons look poorly designed. If you use Modifier.fillMaxWidth, don't assume that's the right behavior for all display sizes.

Users don't just use touch screens to interact with apps.

Adaptive apps support external keyboards, mice, and styluses to provide an enhanced user experience and help users be more productive on form factors of all kinds.

Take advantage of the built‑in functionality of the Android framework for keyboard tab navigation and mouse or trackpad click, select, and scroll. Publish your app's keyboard shortcuts in Keyboard Shortcuts Helper.

Use the Jetpack Material 3 library to enable users to write into any TextField component using a stylus.

Don't make alternative input methods impossible. Don't introduce accessibility issues.

✓ Do what your users expect: optimize your app for the diversity of devices people rely on every day.

✗ Don't wait. Get started today!

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-06-10 UTC.

---

## Migrate XML themes to Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/views-to-compose

**Contents:**
- Migrate XML themes to Compose Stay organized with collections Save and categorize content based on your preferences.

When you introduce Compose in an existing app, you need to migrate your themes in XML to use MaterialTheme for Compose screens. This means your app's theming will have two sources of truth: the View-based theme and the Compose theme. Any changes to your styling need to be made in multiple places. Once your app is fully migrated to Compose, you can remove your XML theming.

To migrate your XML themes to Compose, use the Material Theme Builder to migrate from an XML theme to Material 3 in Compose. You can use your existing color roles, such as primary and secondary colors from your XML theme, and pass them to the Material Theme Builder. This creates a fully Material 3 theme in Compose and provides downloadable color and theme files to use in your app.

Material Theme Builder generates a MaterialTheme and light and dark color schemes for your app. If your app uses custom shapes or typography, migrate your custom shapes and typography by defining a Shape and Typography, respectively. Once defined, provide that information to your MaterialTheme. See shapes and typography to learn more.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Create a button to enable snap scrolling Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/enable-snap-scrolling

**Contents:**
- Create a button to enable snap scrolling Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Create a button to enable snap scrolling
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components
  - Compose basics (video collection)

You can display a button to let a user snap scroll to a specific point in a list, saving time and increasing user engagement.

This implementation requires that your project minSDK be set to API level 21 or higher.

Use the following code to create a button for smooth snap scrolling in a vertical lazy list with 10 items:

@Composable fun MessageList(modifier: Modifier = Modifier) { val listState = rememberLazyListState() val coroutineScope = rememberCoroutineScope() LazyColumn(state = listState, modifier = Modifier.height(120.dp)) { items(10) { index -> Text( modifier = Modifier.height(40.dp), text = "Item $index" ) } } Button(onClick = { coroutineScope.launch { listState.animateScrollToItem(index = 0) } }) { Text(text = "Go top") } }LazyListSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Pull to refresh Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/pull-to-refresh

**Contents:**
- Pull to refresh Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Basic example
  - Key points about the code
  - Result
- Advanced example: Customize indicator color
  - Key points about the code
  - Result
- Advanced example: Create a fully customized indicator
  - Key points about the code

The pull to refresh component allows users to drag downwards at the beginning of an app's content to refresh the data.

Use the PullToRefreshBox composable to implement pull-to-refresh, which acts as a container for your scrollable content. The following key parameters control the refresh behavior and appearance:

This snippet demonstrates basic usage of PullToRefreshBox:

@Composable fun PullToRefreshBasicSample( items: List<String>, isRefreshing: Boolean, onRefresh: () -> Unit, modifier: Modifier = Modifier ) { PullToRefreshBox( isRefreshing = isRefreshing, onRefresh = onRefresh, modifier = modifier ) { LazyColumn(Modifier.fillMaxSize()) { items(items) { ListItem({ Text(text = it) }) } } } }PullToRefreshBox.kt

This video demonstrates the basic pull-to-refresh implementation from the preceding code:

@Composable fun PullToRefreshCustomStyleSample( items: List<String>, isRefreshing: Boolean, onRefresh: () -> Unit, modifier: Modifier = Modifier ) { val state = rememberPullToRefreshState() PullToRefreshBox( isRefreshing = isRefreshing, onRefresh = onRefresh, modifier = modifier, state = state, indicator = { Indicator( modifier = Modifier.align(Alignment.TopCenter), isRefreshing = isRefreshing, containerColor = MaterialTheme.colorScheme.primaryContainer, color = MaterialTheme.colorScheme.onPrimaryContainer, state = state ) }, ) { LazyColumn(Modifier.fillMaxSize()) { items(items) { ListItem({ Text(text = it) }) } } } }PullToRefreshBox.kt

This video shows a pull-to-refresh implementation with a colored indicator:

You can create complex custom indicators by leveraging existing composables and animations.This snippet demonstrates how to create a fully custom indicator in your pull-to-refresh implementation:

@Composable fun PullToRefreshCustomIndicatorSample( items: List<String>, isRefreshing: Boolean, onRefresh: () -> Unit, modifier: Modifier = Modifier ) { val state = rememberPullToRefreshState() PullToRefreshBox( isRefreshing = isRefreshing, onRefresh = onRefresh, modifier = modifier, state = state, indicator = { MyCustomIndicator( state = state, isRefreshing = isRefreshing, modifier = Modifier.align(Alignment.TopCenter) ) } ) { LazyColumn(Modifier.fillMaxSize()) { items(items) { ListItem({ Text(text = it) }) } } } } // ... @Composable fun MyCustomIndicator( state: PullToRefreshState, isRefreshing: Boolean, modifier: Modifier = Modifier, ) { Box( modifier = modifier.pullToRefresh( state = state, isRefreshing = isRefreshing, threshold = PositionalThreshold, onRefresh = { } ), contentAlignment = Alignment.Center ) { Crossfade( targetState = isRefreshing, animationSpec = tween(durationMillis = CROSSFADE_DURATION_MILLIS), modifier = Modifier.align(Alignment.Center) ) { refreshing -> if (refreshing) { CircularProgressIndicator(Modifier.size(SPINNER_SIZE)) } else { val distanceFraction = { state.distanceFraction.coerceIn(0f, 1f) } Icon( imageVector = Icons.Filled.CloudDownload, contentDescription = "Refresh", modifier = Modifier .size(18.dp) .graphicsLayer { val progress = distanceFraction() this.alpha = progress this.scaleX = progress this.scaleY = progress } ) } } } }PullToRefreshBox.kt

This video shows the custom indicator from the preceding code:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Lazily load data with lists and Paging Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/lazily-load-list

**Contents:**
- Lazily load data with lists and Paging Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Display paged content
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components
  - Compose basics (video collection)

With lazy loading and Paging, you can support large lists of items—including an infinite list—in your app by loading and displaying data incrementally. This technique enables you to reduce initial load times and optimize memory usage, enhancing performance.

The following video shows the resulting behavior of a large list fetching data as the user scrolls.

This implementation requires that your project minSDK be set to API level 21 or higher.

With the Paging library, you can load and display pages of data from a larger dataset acquired from local storage or over a network. Use the following code to display a paginated list that shows a progress bar to indicate to the user that more data is being fetched:

@Composable fun MessageList( modifier: Modifier, pager: Pager<Int, Message> ) { val lazyPagingItems = pager.flow.collectAsLazyPagingItems() LazyColumn { items( lazyPagingItems.itemCount, key = lazyPagingItems.itemKey { it.id } ) { index -> val message = lazyPagingItems[index] if (message != null) { MessageRow(message) } else { MessagePlaceholder() } } } @Composable fun MessagePlaceholder(modifier: Modifier) { Box( Modifier .fillMaxWidth() .height(48.dp) ) { CircularProgressIndicator() } } @Composable fun MessageRow( modifier: Modifier, message: Message ) { Card(modifier = Modifier.padding(8.dp)) { Column( modifier = Modifier.padding(8.dp), verticalArrangement = Arrangement.Center ) { Text(message.sender) Text(message.text) } } } }LazyListSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Additional resources Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/additional-resources

**Contents:**
- Additional resources Stay organized with collections Save and categorize content based on your preferences.

See the following Glance samples and showcases to learn more:

Glance code samples: Code samples demonstrating how to build widgets with Jetpack Glance using Canonical Widget Layouts.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-09-23 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?doctype=video

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Auto-format a phone number in a text field Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/auto-format-phone-number

**Contents:**
- Auto-format a phone number in a text field Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Create the text field
  - Key points about the code
- Auto-format a number in the text field
  - Key points about the code
- Collections that contain this guide
  - Display text

You can auto format a phone number in a text field in your app, saving users time by formatting the phone number as they input digits. Follow this guidance to auto format a phone number:

This implementation requires that your project minSDK be set to API level 21 or higher.

First, configure the TextField. This example shows a phone number formatted per the North American Numbering Plan (NANP).NanpVisualTransformation formats a raw string of numbers to NANP, eg. 1234567890 to (123) 456-7890.

@Composable fun PhoneNumber() { var phoneNumber by rememberSaveable { mutableStateOf("") } val numericRegex = Regex("[^0-9]") TextField( value = phoneNumber, onValueChange = { // Remove non-numeric characters. val stripped = numericRegex.replace(it, "") phoneNumber = if (stripped.length >= 10) { stripped.substring(0..9) } else { stripped } }, label = { Text("Enter Phone Number") }, visualTransformation = NanpVisualTransformation(), keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number) ) }TextSnippets.kt

To format a raw string of numbers, use the implementation of the custom NanpVisualTransformation class:

class NanpVisualTransformation : VisualTransformation { override fun filter(text: AnnotatedString): TransformedText { val trimmed = if (text.text.length >= 10) text.text.substring(0..9) else text.text var out = if (trimmed.isNotEmpty()) "(" else "" for (i in trimmed.indices) { if (i == 3) out += ") " if (i == 6) out += "-" out += trimmed[i] } return TransformedText(AnnotatedString(out), phoneNumberOffsetTranslator) } private val phoneNumberOffsetTranslator = object : OffsetMapping { override fun originalToTransformed(offset: Int): Int = when (offset) { 0 -> offset // Add 1 for opening parenthesis. in 1..3 -> offset + 1 // Add 3 for both parentheses and a space. in 4..6 -> offset + 3 // Add 4 for both parentheses, space, and hyphen. else -> offset + 4 } override fun transformedToOriginal(offset: Int): Int = when (offset) { 0 -> offset // Subtract 1 for opening parenthesis. in 1..5 -> offset - 1 // Subtract 3 for both parentheses and a space. in 6..10 -> offset - 3 // Subtract 4 for both parentheses, space, and hyphen. else -> offset - 4 } } }TextSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Time pickers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/time-pickers

**Contents:**
- Time pickers Stay organized with collections Save and categorize content based on your preferences.
- Types
- API surface
  - State
  - Dialog
- Dial time picker
- Input time picker
- Use the state
- Additional resources

Time pickers provide a way for users to select a time. You can use the TimePicker and TimeInput composables to implement a time picker in your app.

There are two types of time picker:

The following image provides an example of a dial time picker on the left, and an input time picker on the right:

To implement a time picker, use either the TimePicker or TimeInput composable:

For both TimePicker and TimeInput, you must also pass a TimePickerState. This lets you set the default selected time that appears on the picker. It also captures the time that the user has selected using the picker.

Time pickers appear in dialogs. The examples in this guide don't use dialogs. For examples that do use dialogs, see the Dialogs for time pickers guide.

This snippet demonstrates how to implement a basic dial time picker.

@Composable fun DialExample( onConfirm: () -> Unit, onDismiss: () -> Unit, ) { val currentTime = Calendar.getInstance() val timePickerState = rememberTimePickerState( initialHour = currentTime.get(Calendar.HOUR_OF_DAY), initialMinute = currentTime.get(Calendar.MINUTE), is24Hour = true, ) Column { TimePicker( state = timePickerState, ) Button(onClick = onDismiss) { Text("Dismiss picker") } Button(onClick = onConfirm) { Text("Confirm selection") } } }TimePickers.kt

Consider the following in this snippet:

This implementation appears as follows:

This snippet demonstrates how to implement a basic input style time picker.

@Composable fun InputExample( onConfirm: () -> Unit, onDismiss: () -> Unit, ) { val currentTime = Calendar.getInstance() val timePickerState = rememberTimePickerState( initialHour = currentTime.get(Calendar.HOUR_OF_DAY), initialMinute = currentTime.get(Calendar.MINUTE), is24Hour = true, ) Column { TimeInput( state = timePickerState, ) Button(onClick = onDismiss) { Text("Dismiss picker") } Button(onClick = onConfirm) { Text("Confirm selection") } } }TimePickers.kt

Key points to note in this implementation:

This implementation appears as follows:

To make use of the time that the user has selected in a time picker, pass the appropriate TimePickerState to your onConfirm function. The parent composable can then access the selected time through TimePickerState.hour and TimePickerState.minute.

The following snippet demonstrates how to do this:

@Composable fun DialUseStateExample( onConfirm: (TimePickerState) -> Unit, onDismiss: () -> Unit, ) { val currentTime = Calendar.getInstance() val timePickerState = rememberTimePickerState( initialHour = currentTime.get(Calendar.HOUR_OF_DAY), initialMinute = currentTime.get(Calendar.MINUTE), is24Hour = true, ) Column { TimePicker( state = timePickerState, ) Button(onClick = onDismiss) { Text("Dismiss picker") } Button(onClick = { onConfirm(timePickerState) }) { Text("Confirm selection") } } }TimePickers.kt

You could then call the composable like this:

For more detail, see the full implementation in the snippets app.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (swift):
```swift
var selectedTime: TimePickerState? by remember { mutableStateOf(null) }

// ...

DialUseStateExample(
    onDismiss = {
        showDialExample = false
    },
    onConfirm = {
            time ->
        selectedTime = time
        showDialExample = false
    },
)

// ...

if (selectedTime != null) {
    val cal = Calendar.getInstance()
    cal.set(Calendar.HOUR_OF_DAY, selectedTime!!.hour)
    cal.set(Calendar.MINUTE, selectedTime!!.minute)
    cal.isLenient = false
    Text("Selected time = ${formatter.format(cal.time)}")
} else {
    Text("No time selected.")
}
```

---

## Navigation rail Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/navigation-rail

**Contents:**
- Navigation rail Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Example: Rail-based navigation
  - Key points
  - Result
- Additional resources

Rails provide access to destinations in apps that run on devices with large screens. You should use navigation rails for:

This page shows you how to display rails in your app with related screens and basic navigation.

Use the NavigationRail composable with NavigationRailItem to implement a rail in your application. The NavigationRailItem represents a single rail item in the rail column.

NavigationRailItem includes the following key parameters:

The following snippet implements a navigation rail so users can navigate between different screens in an app:

@Composable fun NavigationRailExample(modifier: Modifier = Modifier) { val navController = rememberNavController() val startDestination = Destination.SONGS var selectedDestination by rememberSaveable { mutableIntStateOf(startDestination.ordinal) } Scaffold(modifier = modifier) { contentPadding -> NavigationRail(modifier = Modifier.padding(contentPadding)) { Destination.entries.forEachIndexed { index, destination -> NavigationRailItem( selected = selectedDestination == index, onClick = { navController.navigate(route = destination.route) selectedDestination = index }, icon = { Icon( destination.icon, contentDescription = destination.contentDescription ) }, label = { Text(destination.label) } ) } } AppNavHost(navController, startDestination) } }Navigation.kt

The following image shows the result of the previous snippet:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## About window insets Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/insets

**Contents:**
- About window insets Stay organized with collections Save and categorize content based on your preferences.
- Inset fundamentals
- Recommended for you

The Android platform is responsible for drawing the system UI, such as the status bar and navigation bar. This system UI is displayed regardless of which app the user is using.

WindowInsets provides information about the system UI to ensure that your app draws in the correct area and your UI isn't obscured by the system UI.

On Android 14 (API level 34) and lower, your app's UI does not draw underneath the system bars and display cutouts by default.

On Android 15 (API level 35) and higher, your app draws underneath the system bars and display cutouts once your app targets SDK 35. This results in a more seamless user experience and allows your app to take full advantage of the window space available to it.

Displaying content behind the system UI is called going edge-to-edge. On this page, you learn about the different types of insets, how to go edge-to-edge, and how to use the inset APIs to animate your UI and ensure your app's content isn't obscured by system UI elements.

When an app goes edge-to-edge, you need to ensure that important content and interactions are not obscured by the system UI. For example, if a button is placed behind the navigation bar, the user may not be able to click it.

The size of the system UI and information about where it is placed is specified via insets.

Each portion of the system UI has a corresponding type of inset that describes its size and where it is placed. For example, status bar insets provide the size and position of the status bar, whereas the navigation bar insets provide the size and position of the navigation bar. Each type of inset consists of four pixel dimensions: top, left, right, and bottom. These dimensions specify how far the system UI extends from the corresponding sides of the app's window. To avoid overlapping with that type of system UI, therefore, app UI must be inset by that amount.

These built-in Android inset types are available through WindowInsets:

WindowInsets.statusBars

The insets describing the status bars. These are the top system UI bars containing notification icons and other indicators.

WindowInsets.statusBarsIgnoringVisibility

The status bar insets for when they are visible. If the status bars are currently hidden (due to entering immersive full screen mode), then the main status bar insets will be empty, but these insets will be non-empty.

WindowInsets.navigationBars

The insets describing the navigation bars. These are the system UI bars on the left, right, or bottom side of the device, describing the taskbar or navigation icons. These can change at runtime based on the user's preferred navigation method and interacting with the taskbar.

WindowInsets.navigationBarsIgnoringVisibility

The navigation bar insets for when they are visible. If the navigation bars are currently hidden (due to entering immersive full screen mode), then the main navigation bar insets will be empty, but these insets will be non-empty.

WindowInsets.captionBar

The inset describing the system UI window decoration if in a freeform window, like top title bar.

WindowInsets.captionBarIgnoringVisibility

The caption bar insets for when they are visible. If the caption bars are currently hidden, then the main caption bar insets will be empty, but these insets will be non-empty.

WindowInsets.systemBars

The union of the system bar insets, which include the status bars, navigation bars, and caption bar.

WindowInsets.systemBarsIgnoringVisibility

The system bar insets for when they are visible. If the system bars are currently hidden (due to entering immersive full screen mode), then the main system bar insets will be empty, but these insets will be non-empty.

The insets describing the amount of space on the bottom that the software keyboard occupies.

WindowInsets.imeAnimationSource

The insets describing the amount of space that the software keyboard occupied before the current keyboard animation.

WindowInsets.imeAnimationTarget

The insets describing the amount of space that the software keyboard will occupy after the current keyboard animation.

WindowInsets.tappableElement

A type of insets describing more detailed information about the navigation UI, giving the amount of space where "taps" will be handled by the system, and not the app. For transparent navigation bars with gesture navigation, some app elements can be tappable through the system navigation UI.

WindowInsets.tappableElementIgnoringVisibility

The tappable element insets for when they are visible. If the tappable elements are currently hidden (due to entering immersive full screen mode), then the main tappable element insets will be empty, but these insets will be non-empty.

WindowInsets.systemGestures

The insets representing the amount of insets where the system will intercept gestures for navigation. Apps can manually specify handling a limited amount of these gestures via Modifier.systemGestureExclusion.

WindowInsets.mandatorySystemGestures

A subset of the system gestures that will always be handled by the system, and which can't be opted out via Modifier.systemGestureExclusion.

WindowInsets.displayCutout

The insets representing the amount of spacing needed to avoid overlapping with a display cutout (notch or pinhole).

WindowInsets.waterfall

The insets representing the curved areas of a waterfall display. A waterfall display has curved areas along the edges of the screen where the screen begins to wrap along the sides of the device.

These types are summarized by three "safe" inset types that ensure content isn't obscured:

These "safe" inset types protect content in different ways, based on the underlying platform insets:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Choose an animation API Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/choose-api

**Contents:**
- Choose an animation API Stay organized with collections Save and categorize content based on your preferences.

The following diagram helps you decide what API to use to implement your animation.

Figure 1. Decision tree describing how to choose the appropriate animation API.

Use the following decision tree to choose the most appropriate animation API for your use case:

Download the PDF version of the diagram.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Set up your app for PiP Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/pip-setup

**Contents:**
- Set up your app for PiP Stay organized with collections Save and categorize content based on your preferences.
- Add PiP on leave app for pre-Android 12
- Add PiP on leave app for post-Android 12
- Use setAspectRatio to set PiP window's aspect ratio

In the activity tag of your AndroidManifest.xml file, do the following:

In your Compose code, do the following:

To add PiP for pre-Android 12, use addOnUserLeaveHintProvider. Follow these steps to add PiP for pre-Android 12:

if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O && Build.VERSION.SDK_INT < Build.VERSION_CODES.S ) { val context = LocalContext.current DisposableEffect(context) { val onUserLeaveBehavior = Runnable { context.findActivity() .enterPictureInPictureMode(PictureInPictureParams.Builder().build()) } context.findActivity().addOnUserLeaveHintListener( onUserLeaveBehavior ) onDispose { context.findActivity().removeOnUserLeaveHintListener( onUserLeaveBehavior ) } } } else { Log.i("PiP info", "API does not support PiP") }PictureInPictureSnippets.kt

Post-Android 12, the PictureInPictureParams.Builder is added through a modifier that is passed to the app's video player.

val pipModifier = modifier.onGloballyPositioned { layoutCoordinates -> val builder = PictureInPictureParams.Builder() if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) { builder.setAutoEnterEnabled(true) } context.findActivity().setPictureInPictureParams(builder.build()) } VideoPlayer(pipModifier)PictureInPictureSnippets.kt

To set the aspect ratio of the PiP window, you can either choose a specific aspect ratio or use the width and height of the player's video size. If you are using a media3 player, check that the player is not null and that the player's video size is not equal to VideoSize.UNKNOWN before setting the aspect ratio.

val context = LocalContext.current val pipModifier = modifier.onGloballyPositioned { layoutCoordinates -> val builder = PictureInPictureParams.Builder() if (shouldEnterPipMode && player != null && player.videoSize != VideoSize.UNKNOWN) { val sourceRect = layoutCoordinates.boundsInWindow().toAndroidRectF().toRect() builder.setSourceRectHint(sourceRect) builder.setAspectRatio( Rational(player.videoSize.width, player.videoSize.height) ) } if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) { builder.setAutoEnterEnabled(shouldEnterPipMode) } context.findActivity().setPictureInPictureParams(builder.build()) } VideoPlayer(pipModifier)PictureInPictureSnippets.kt

If you're using a custom player, set the aspect ratio on the player's height and width using the syntax specific to your player. Be aware that if your player resizes during initialization, if it falls outside of the valid bounds of what the aspect ratio can be, your app will crash. You may need to add checks around when the aspect ratio can be calculated, similar to how it is done for a media3 player.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<activity
    android:name=".SnippetsActivity"
    android:exported="true"
    android:supportsPictureInPicture="true"
    android:configChanges="orientation|screenLayout|screenSize|smallestScreenSize"
    android:theme="@style/Theme.Snippets">
```

---

## 

**URL:** https://developer.android.com/develop/ui/compose/why-adopt

**Contents:**
  - Why adopt Compose
- Less code
- Intuitive
- Accelerate development
- Powerful
  - Have your team get started with adding Compose in your app

Writing less code affects all stages of development: as an author, you get to focus on the problem at hand, with less to test and debug and with less chances of bugs; as a reviewer or maintainer you have less code to read, understand, review and maintain.

Compose allows you to do more with less code, compared to using the Android View system: Buttons, lists or animation - whatever you need to build, now there’s less code to write. Here’s what some of our partners say:

The code you’re writing is written only in Kotlin, rather than having it split between Kotlin and XML: “It’s much easier to trace through code when it’s all written in the same language and often the same file, rather than jumping back and forth between Kotlin and XML” (Monzo)

Code written with Compose is simple and easy to maintain whatever you’re building. “The layout system of Compose is conceptually simpler so it’s easier to reason about. Reading the code for complicated components is easier as well.” (Square)

Compose uses a declarative API, which means that all you need to do is describe your UI - Compose takes care of the rest. The APIs are intuitive - easy to discover and use: “Our theming layer is vastly more intuitive and legible. We've been able to accomplish within a single Kotlin file what otherwise extended across multiple XML files that were responsible for attribute definitions and assignments via multiple layered theme overlays.” (Twitter)

With Compose, you build small, stateless components that are not tied to a specific activity or fragment. That makes them easy to reuse and test: “We set a goal for ourselves to deliver a new set of UI components that were stateless, easy to use and maintain, and intuitive to implement/extend/customize. Compose really provided a solid answer for us in this.” (Twitter)

In Compose, state is explicit and passed to the composable. That way there’s one single source of truth for the state, making it encapsulated and decoupled. Then, as app state changes, your UI automatically updates. “There’s less to hold in your head while you’re reasoning about something and less behavior that is outside of your control or poorly understood” (Cuvva)

Compose is compatible with all your existing code: you can call Compose code from Views and Views from Compose. Most common libraries like Navigation, ViewModel and Kotlin coroutines work with Compose, so you can start adopting when and where you want. “Interoperability was where we started integrating Compose, and what we found was it 'just works'. We found that we didn’t have to think about things like light and dark mode, and the whole experience was incredibly seamless.” (Cuvva)

Using the full Android Studio support, with features like live previews, you get to iterate and ship code faster: “Previews in Android Studio have been a big time saver. Being able to build out multiple previews also saves us time. Often we need to check a UI component in different states or with different settings -- like error states, or with a different font size, etc. With the ability to create multiple previews we can easily check this.” (Square)

Compose enables you to create beautiful apps with direct access to the Android platform APIs and built-in support for Material Design, Dark theme, animations, and more: “Compose has also solved more than declarative UI -- accessibility apis, layout, all kinds of stuff have been improved. There are fewer steps between the thing you want to make and actually making it” (Square).

With Compose, bringing movement and life to your apps through animations is quick and easy to implement: “animations are so easy to add in Compose that there’s very little reason not to animate things like color/size/elevation changes” (Monzo), “you can make animations without requiring anything special -- it’s not different than showing a static screen” (Square).

Whether you’re building with Material Design or your own design system, Compose gives you the flexibility to implement the design you want: “Having Material Design separated from the foundation has been really useful to us as we’re building our own design system that often requires differing design requirements from Material.” (Square)

Check out the in-depth case studies to learn more about how Twitter, Square, Monzo and Cuvva are using Compose.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-09-19 UTC.

---

## Use window size classes Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/use-window-size-classes

**Contents:**
- Use window size classes Stay organized with collections Save and categorize content based on your preferences.
- Manage layouts with window size classes
- Test window size classes
- Next steps

Window size classes are a set of opinionated viewport breakpoints that help you design, develop, and test responsive/adaptive layouts. The breakpoints balance layout simplicity with the flexibility of optimizing your app for unique cases.

Window size classes categorize the display area available to your app as compact, medium, expanded, large, or extra large. Available width and height are classified separately, so at any point in time, your app has two window size classes—one for width, one for height. Available width is usually more important than available height due to the ubiquity of vertical scrolling, so the width window size class is likely more relevant to your app's UI.

As visualized in the figures, the breakpoints allow you to continue thinking about layouts in terms of devices and configurations. Each size class breakpoint represents a majority case for typical device scenarios, which can be a helpful frame of reference as you think about the design of your breakpoint-based layouts.

most large unfolded inner displays in portrait

most large unfolded inner displays in landscape are at least expanded width

97.59% of phones in portrait

Although visualizing size classes as physical devices can be useful, window size classes are explicitly not determined by the size of the device screen. Window size classes are not intended for isTablet‑type logic. Rather, window size classes are determined by the window size available to your application regardless of the type of device the app is running on, which has two important implications:

Physical devices do not guarantee a specific window size class. The screen space available to your app can differ from the screen size of the device for many reasons. On mobile devices, split‑screen mode can partition the screen between two applications. On ChromeOS, Android apps can be presented in desktop‑type windows that are arbitrarily resizable. Foldables can have two different‑sized screens individually accessed by folding or unfolding the device.

The window size class can change throughout the lifetime of your app. While your app is running, device orientation changes, multitasking, and folding/unfolding can change the amount of screen space available. As a result, the window size class is dynamic, and your app's UI should adapt accordingly.

Window size classes map to the compact, medium, and expanded breakpoints in the Material Design layout guidance. Additionally, large and extra-large breakpoints have been added to better target desktop and connected displays.

Use window size classes to make high‑level application layout decisions, such as deciding whether to use a specific canonical layout to take advantage of additional screen space.

Compute the current WindowSizeClass using the currentWindowAdaptiveInfo() top‑level function of the androidx.compose.material3.adaptive library. The function returns an instance of WindowAdaptiveInfo, which contains windowSizeClass. To support large and extra-large breakpoints, add the supportLargeAndXLargeWidth parameter set to true to the function call. The following example shows how to calculate the window size class and receive updates whenever the window size class changes:

val windowSizeClass = currentWindowAdaptiveInfo().windowSizeClassAdaptiveLayoutSnippets.kt

Window size classes enable you to change your app layout as the display space available to your app changes, for example, when a device folds or unfolds, the device orientation changes, or the app window is resized in multi‑window mode.

Localize the logic for handling display size changes by passing window size classes down as state to nested composables just like any other app state:

@Composable fun MyApp( windowSizeClass: WindowSizeClass = currentWindowAdaptiveInfo(supportLargeAndXLargeWidth = true).windowSizeClass ) { // Decide whether to show the top app bar based on window size class. val showTopAppBar = windowSizeClass.isHeightAtLeastBreakpoint(WindowSizeClass.HEIGHT_DP_MEDIUM_LOWER_BOUND) // MyScreen logic is based on the showTopAppBar boolean flag. MyScreen( showTopAppBar = showTopAppBar, /* ... */ ) }AdaptiveLayoutSnippets.kt

As you make layout changes, test the layout behavior across all window sizes, especially at the compact, medium, and expanded breakpoint widths.

If you have an existing layout for compact screens, first optimize your layout for the expanded width size class, since this size class provides the most space for additional content and UI changes. Then decide what layout makes sense for the medium width size class; consider adding a specialized layout.

To learn more about how to use window size classes to create responsive/adaptive layouts, see the following:

For Compose-based layouts: Support different display sizes

For view-based layouts: Responsive/adaptive design with views

To learn more about what makes an app great on all devices and screen sizes, see:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-14 UTC.

---

## Build a list-detail layout Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/list-detail

**Contents:**
- Build a list-detail layout Stay organized with collections Save and categorize content based on your preferences.
- Implement the List-Detail Pattern with NavigableListDetailPaneScaffold
  - Declare dependencies
  - Kotlin
  - Groovy
  - Opt-in to the predictive back gesture
  - Basic usage
    - BackNavigationBehavior options
      - PopUntilScaffoldValueChange (Default and recommended in most cases)
      - PopUntilContentChange

List-detail is a UI pattern that consists of a dual-pane layout where one pane presents a list of items and another pane displays the details of items selected from the list.

The pattern is particularly useful for applications that provide in-depth information about elements of large collections, for example, an email client that has a list of emails and the detailed content of each email message. List-detail can also be used for less critical paths such as dividing app preferences into a list of categories with the preferences for each category in the detail pane.

NavigableListDetailPaneScaffold is a composable that simplifies implementing a list-detail layout in Jetpack Compose. It wraps ListDetailPaneScaffold and adds built-in navigation and predictive back animations.

A list-detail scaffold supports up to three panes:

The scaffold adapts based on window size:

NavigableListDetailPaneScaffold is part of the Material 3 adaptive navigation library.

Add the following three related dependencies to the build.gradle file of your app or module:

Ensure your project includes compose-material3-adaptive version 1.1.0-beta1 or higher.

To enable predictive back animations in Android 15 or lower, you must opt-in to support the predictive back gesture. To opt-in, add android:enableOnBackInvokedCallback="true" to the <application> tag or individual <activity> tags within your AndroidManifest.xml file. For more information, see Opt-in to the predictive back gesture.

Once your app targets Android 16 (API level 36) or higher, predictive back is enabled by default.

Implement NavigableListDetailPaneScaffold as follows:

This navigator is used to move between the list, detail, and extra panes. By declaring a generic type, the navigator also tracks the state of the scaffold (that is, which MyItem is being displayed). Since this type is parcelable, the state can be saved and restored by the navigator to automatically handle configuration changes.

Pass the navigator to the NavigableListDetailPaneScaffold composable.

Supply your list pane implementation to the NavigableListDetailPaneScaffold. Use AnimatedPane to apply the default pane animations during navigation. Then use ThreePaneScaffoldNavigator to navigate to the detail pane, ListDetailPaneScaffoldRole.Detail, and display the passed item.

Include your detail pane implementation in NavigableListDetailPaneScaffold.

When navigation is complete, currentDestination contains the pane your app has navigated to, including the content displayed in the pane. The contentKey property is the same type specified in the original call so you can access any data that you need to display.

If your app requires a different back navigation pattern, you can override this behavior by specifying another BackNavigationBehavior option.

The following section uses the example of an email app with a list of emails in one pane and a detailed view in the other.

This behavior focuses on changes to the overall layout structure. In a multi-pane setup, changing the email content in the detailed pane doesn't alter the underlying layout structure. Therefore, the back button might exit the app or the current navigation graph because there's no layout change to revert to within the current context. In a single-pane layout, pressing back will skip through content changes within the detail view and return to the list view, as this represents a clear layout change.

Consider the following examples:

Use this when you want users to perceive distinct layout transitions with each back action.

This behavior prioritizes the content displayed. If you view Item 1 and then Item 2, pressing back will revert to Item 1, regardless of the layout.

Consider the following examples:

Use this when your user expects to return to the previously viewed content with the back action.

This behavior pops the back stack until the current navigation destination changes. This applies equally to single and multi-pane layouts.

Consider the following examples:

Regardless of whether you're in a single or multi-pane layout, pressing back will always move the focus from the highlighted navigation element to the previous destination. In our email app, this means the visual indication of the selected pane will shift.

Use this when maintaining a clear visual indication of the current navigation is crucial for the user experience.

This option removes only the most recent destination from the backstack. Use this option for back navigation without skipping intermediate states.

After you implement these steps, your code should look similar to the following:

val scaffoldNavigator = rememberListDetailPaneScaffoldNavigator<MyItem>() val scope = rememberCoroutineScope() val backNavigationBehavior = BackNavigationBehavior.PopUntilScaffoldValueChange NavigableListDetailPaneScaffold( navigator = scaffoldNavigator, listPane = { AnimatedPane { MyList( onItemClick = { item -> // Navigate to the detail pane with the passed item scope.launch { scaffoldNavigator.navigateTo( ListDetailPaneScaffoldRole.Detail, item ) } }, ) } }, detailPane = { AnimatedPane { // Show the detail pane content if selected item is available scaffoldNavigator.currentDestination?.contentKey?.let { Column { // Allow users to dismiss the detail pane. Use back navigation to // hide an expanded detail pane. if (scaffoldNavigator.scaffoldValue[ListDetailPaneScaffoldRole.Detail] == PaneAdaptedValue.Expanded) { // Material design principles promote the usage of a right-aligned // close (X) button. IconButton( modifier = Modifier.align(Alignment.End).padding(16.dp), onClick = { scope.launch { scaffoldNavigator.navigateBack(backNavigationBehavior) } } ) { Icon(Icons.Default.Close, contentDescription = "Close") } } MyDetails(it) } } } }, )SampleListDetailPaneScaffold.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Tooltip Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/tooltip

**Contents:**
- Tooltip Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Display a plain tooltip
  - Key points about the code
  - Result
- Display a rich tooltip
  - Key points about the code
  - Result
- Customize a rich tooltip
  - Key points about the code

Use tooltips to add context to a button or other UI element. There are two types of tooltips:

You can use the TooltipBox composable to implement tooltips in your app. You control TooltipBox appearance with these main parameters:

Use a plain tooltip to briefly describe a UI element. This code snippet displays a plain tooltip on top of an icon button, labeled "Add to favorites":

@Composable fun PlainTooltipExample( modifier: Modifier = Modifier, plainTooltipText: String = "Add to favorites" ) { TooltipBox( modifier = modifier, positionProvider = TooltipDefaults.rememberPlainTooltipPositionProvider(), tooltip = { PlainTooltip { Text(plainTooltipText) } }, state = rememberTooltipState() ) { IconButton(onClick = { /* Do something... */ }) { Icon( imageVector = Icons.Filled.Favorite, contentDescription = "Add to favorites" ) } } } Tooltips.kt

This example produces a plain tooltip on top of an icon:

Use a rich tooltip to provide additional context about a UI element. This example creates a multi-line rich tooltip with a title that is anchored to an Icon:

@Composable fun RichTooltipExample( modifier: Modifier = Modifier, richTooltipSubheadText: String = "Rich Tooltip", richTooltipText: String = "Rich tooltips support multiple lines of informational text." ) { TooltipBox( modifier = modifier, positionProvider = TooltipDefaults.rememberRichTooltipPositionProvider(), tooltip = { RichTooltip( title = { Text(richTooltipSubheadText) } ) { Text(richTooltipText) } }, state = rememberTooltipState() ) { IconButton(onClick = { /* Icon button's click event */ }) { Icon( imageVector = Icons.Filled.Info, contentDescription = "Show more information" ) } } }Tooltips.kt

This example produces a rich tooltip with a title attached to an information icon:

This code snippet displays a rich tooltip with a title, custom actions, and a custom caret (arrow) displayed on top of a camera icon button:

@Composable fun AdvancedRichTooltipExample( modifier: Modifier = Modifier, richTooltipSubheadText: String = "Custom Rich Tooltip", richTooltipText: String = "Rich tooltips support multiple lines of informational text.", richTooltipActionText: String = "Dismiss" ) { val tooltipState = rememberTooltipState() val coroutineScope = rememberCoroutineScope() TooltipBox( modifier = modifier, positionProvider = TooltipDefaults.rememberRichTooltipPositionProvider(), tooltip = { RichTooltip( title = { Text(richTooltipSubheadText) }, action = { Row { TextButton(onClick = { coroutineScope.launch { tooltipState.dismiss() } }) { Text(richTooltipActionText) } } }, ) { Text(richTooltipText) } }, state = tooltipState ) { IconButton(onClick = { coroutineScope.launch { tooltipState.show() } }) { Icon( imageVector = Icons.Filled.Camera, contentDescription = "Open camera" ) } } }Tooltips.kt

This example produces the following:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## About window insets Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/insets

**Contents:**
- About window insets Stay organized with collections Save and categorize content based on your preferences.
- Inset fundamentals
- Recommended for you

The Android platform is responsible for drawing the system UI, such as the status bar and navigation bar. This system UI is displayed regardless of which app the user is using.

WindowInsets provides information about the system UI to ensure that your app draws in the correct area and your UI isn't obscured by the system UI.

On Android 14 (API level 34) and lower, your app's UI does not draw underneath the system bars and display cutouts by default.

On Android 15 (API level 35) and higher, your app draws underneath the system bars and display cutouts once your app targets SDK 35. This results in a more seamless user experience and allows your app to take full advantage of the window space available to it.

Displaying content behind the system UI is called going edge-to-edge. On this page, you learn about the different types of insets, how to go edge-to-edge, and how to use the inset APIs to animate your UI and ensure your app's content isn't obscured by system UI elements.

When an app goes edge-to-edge, you need to ensure that important content and interactions are not obscured by the system UI. For example, if a button is placed behind the navigation bar, the user may not be able to click it.

The size of the system UI and information about where it is placed is specified via insets.

Each portion of the system UI has a corresponding type of inset that describes its size and where it is placed. For example, status bar insets provide the size and position of the status bar, whereas the navigation bar insets provide the size and position of the navigation bar. Each type of inset consists of four pixel dimensions: top, left, right, and bottom. These dimensions specify how far the system UI extends from the corresponding sides of the app's window. To avoid overlapping with that type of system UI, therefore, app UI must be inset by that amount.

These built-in Android inset types are available through WindowInsets:

WindowInsets.statusBars

The insets describing the status bars. These are the top system UI bars containing notification icons and other indicators.

WindowInsets.statusBarsIgnoringVisibility

The status bar insets for when they are visible. If the status bars are currently hidden (due to entering immersive full screen mode), then the main status bar insets will be empty, but these insets will be non-empty.

WindowInsets.navigationBars

The insets describing the navigation bars. These are the system UI bars on the left, right, or bottom side of the device, describing the taskbar or navigation icons. These can change at runtime based on the user's preferred navigation method and interacting with the taskbar.

WindowInsets.navigationBarsIgnoringVisibility

The navigation bar insets for when they are visible. If the navigation bars are currently hidden (due to entering immersive full screen mode), then the main navigation bar insets will be empty, but these insets will be non-empty.

WindowInsets.captionBar

The inset describing the system UI window decoration if in a freeform window, like top title bar.

WindowInsets.captionBarIgnoringVisibility

The caption bar insets for when they are visible. If the caption bars are currently hidden, then the main caption bar insets will be empty, but these insets will be non-empty.

WindowInsets.systemBars

The union of the system bar insets, which include the status bars, navigation bars, and caption bar.

WindowInsets.systemBarsIgnoringVisibility

The system bar insets for when they are visible. If the system bars are currently hidden (due to entering immersive full screen mode), then the main system bar insets will be empty, but these insets will be non-empty.

The insets describing the amount of space on the bottom that the software keyboard occupies.

WindowInsets.imeAnimationSource

The insets describing the amount of space that the software keyboard occupied before the current keyboard animation.

WindowInsets.imeAnimationTarget

The insets describing the amount of space that the software keyboard will occupy after the current keyboard animation.

WindowInsets.tappableElement

A type of insets describing more detailed information about the navigation UI, giving the amount of space where "taps" will be handled by the system, and not the app. For transparent navigation bars with gesture navigation, some app elements can be tappable through the system navigation UI.

WindowInsets.tappableElementIgnoringVisibility

The tappable element insets for when they are visible. If the tappable elements are currently hidden (due to entering immersive full screen mode), then the main tappable element insets will be empty, but these insets will be non-empty.

WindowInsets.systemGestures

The insets representing the amount of insets where the system will intercept gestures for navigation. Apps can manually specify handling a limited amount of these gestures via Modifier.systemGestureExclusion.

WindowInsets.mandatorySystemGestures

A subset of the system gestures that will always be handled by the system, and which can't be opted out via Modifier.systemGestureExclusion.

WindowInsets.displayCutout

The insets representing the amount of spacing needed to avoid overlapping with a display cutout (notch or pinhole).

WindowInsets.waterfall

The insets representing the curved areas of a waterfall display. A waterfall display has curved areas along the edges of the screen where the screen begins to wrap along the sides of the device.

These types are summarized by three "safe" inset types that ensure content isn't obscured:

These "safe" inset types protect content in different ways, based on the underlying platform insets:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Resources in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/resources

**Contents:**
- Resources in Compose Stay organized with collections Save and categorize content based on your preferences.
- Strings
  - String plurals (experimental)
- Dimensions
- Colors
- Vector assets and image resources
- Animated Vector Drawables
- Icons
- Fonts
- Recommended for you

Jetpack Compose can access the resources defined in your Android project. This document explains some of the APIs Compose offers to do so.

Resources are the additional files and static content that your code uses, such as bitmaps, layout definitions, user interface strings, animation instructions, and more. If you're not familiar with resources in Android, check out the App resources overview guide.

The most common type of resource are your Strings. Use the stringResource API to retrieve a string statically defined in your XML resources.

// In the res/values/strings.xml file // <string name="compose">Jetpack Compose</string> // In your Compose code Text( text = stringResource(R.string.compose) )ResourcesSnippets.kt

stringResource also works with positional formatting.

// In the res/values/strings.xml file // <string name="congratulate">Happy %1$s %2$d</string> // In your Compose code Text( text = stringResource(R.string.congratulate, "New Year", 2021) )ResourcesSnippets.kt

Use the pluralStringResource API to load up a plural with a certain quantity.

// In the res/strings.xml file // <plurals name="runtime_format"> // <item quantity="one">%1$d minute</item> // <item quantity="other">%1$d minutes</item> // </plurals> // In your Compose code Text( text = pluralStringResource( R.plurals.runtime_format, quantity, quantity ) )ResourcesSnippets.kt

When using the pluralStringResource method, you need to pass the count twice if your string includes string formatting with a number. For example, for the string %1$d minutes, the first count parameter selects the appropriate plural string and the second count parameter is inserted into the %1$d placeholder. If your plural strings don't include string formatting, you don't need to pass the third parameter to pluralStringResource.

For more information on plurals, check out the quantity string documentation.

Similarly, use the dimensionResource API to get dimensions from a resource XML file.

// In the res/values/dimens.xml file // <dimen name="padding_small">8dp</dimen> // In your Compose code val smallPadding = dimensionResource(R.dimen.padding_small) Text( text = "...", modifier = Modifier.padding(smallPadding) )ResourcesSnippets.kt

If you're adopting Compose incrementally in your app, use the colorResource API to get colors from a resource XML file.

// In the res/colors.xml file // <color name="purple_200">#FFBB86FC</color> // In your Compose code HorizontalDivider(color = colorResource(R.color.purple_200))ResourcesSnippets.kt

colorResource works as expected with static colors, but it flattens color state list resources.

Use the painterResource API to load either vector drawables or rasterized asset formats like PNGs. You don't need to know the type of the drawable, simply use painterResource in Image composables or paint modifiers.

// Files in res/drawable folders. For example: // - res/drawable-nodpi/ic_logo.xml // - res/drawable-xxhdpi/ic_logo.png // In your Compose code Icon( painter = painterResource(id = R.drawable.ic_logo), contentDescription = null // decorative element )ResourcesSnippets.kt

painterResource decodes and parses the content of the resource on the main thread.

Use the AnimatedImageVector.animatedVectorResource API to load an animated vector drawable XML. The method returns an AnimatedImageVector instance. In order to display the animated image, use the rememberAnimatedVectorPainter method to create a Painter that can be used in Image and Icon composables. The boolean atEnd parameter of the rememberAnimatedVectorPainter method indicates whether the image should be drawn at the end of all the animations. If used with a mutable state, changes to this value trigger the corresponding animation.

// Files in res/drawable folders. For example: // - res/drawable/ic_hourglass_animated.xml // In your Compose code val image = AnimatedImageVector.animatedVectorResource(R.drawable.ic_hourglass_animated) val atEnd by remember { mutableStateOf(false) } Icon( painter = rememberAnimatedVectorPainter(image, atEnd), contentDescription = null // decorative element )ResourcesSnippets.kt

Jetpack Compose comes with the Icons object that is the entry point for using Material Icons in Compose. There are five distinct icon themes: Filled, Outlined, Rounded, TwoTone, and Sharp. Each theme contains the same icons, but with a distinct visual style. You should typically choose one theme and use it across your application for consistency.

To draw an icon, you can use the Icon composable which applies tint and provides layout size matching the icon.

Icon(Icons.Rounded.Menu, contentDescription = "Localized description")ResourcesSnippets.kt

Some of the most commonly used icons are available as part of the androidx.compose.material dependency. To use any of the other Material icons, add the material-icons-extended dependency to the build.gradle file.

To use fonts in Compose, download and bundle the font files directly in your APKs by placing them in the res/font folder.

Load each font using the Font API and create a FontFamily with them that you can use in TextStyle instances to create your own Typography. The following is code taken from the Crane compose sample and its Typography.kt file.

// Define and load the fonts of the app private val light = Font(R.font.raleway_light, FontWeight.W300) private val regular = Font(R.font.raleway_regular, FontWeight.W400) private val medium = Font(R.font.raleway_medium, FontWeight.W500) private val semibold = Font(R.font.raleway_semibold, FontWeight.W600) // Create a font family to use in TextStyles private val craneFontFamily = FontFamily(light, regular, medium, semibold) // Use the font family to define a custom typography val craneTypography = Typography( titleLarge = TextStyle( fontFamily = craneFontFamily ) /* ... */ ) // Pass the typography to a MaterialTheme that will create a theme using // that typography in the part of the UI hierarchy where this theme is used @Composable fun CraneTheme(content: @Composable () -> Unit) { MaterialTheme(typography = craneTypography) { content() } }ResourcesSnippets.kt

For using downloadable fonts in Compose, see the Downloadable fonts page.

Learn more about typography in the Theming in Compose documentation

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
dependencies {
  def composeBom = platform('androidx.compose:compose-bom:2025.12.00')
  implementation composeBom

  implementation 'androidx.compose.material:material-icons-extended'
}
```

---

## Implement a Glance theme Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/theme

**Contents:**
- Implement a Glance theme Stay organized with collections Save and categorize content based on your preferences.
- Add colors
- Add shapes

Glance provides an API to manage the color theme. For other style attributes, such as TextStyle, declare top-level variables.

Glance provides an implementation of Material colors out of the box. To use the built-in theme, wrap your top level composable with GlanceTheme, as shown in the following example.

On devices that support dynamic colors, this theme is derived from the user-specific platform colors. On other devices, this falls back to the Material baseline theme. Use GlanceTheme.colors to style with colors from the wrapped theme. You can use these values from the theme anywhere a color is needed.

override suspend fun provideGlance(context: Context, id: GlanceId) { provideContent { GlanceTheme { MyContent() } } } @Composable private fun MyContent() { Image( colorFilter = ColorFilter.tint(GlanceTheme.colors.secondary), // ... ) }GlanceSnippets.kt

To customize the theme, you can pass the colors to the GlanceTheme. Glance provides the androidx.glance:glance-material interoperability library for Material 2, and androidx.glance:glance-material3 for Material 3 colors support.

For example, provide your app's existing material colors to the ColorProviders API to create a Glance color scheme, as shown in the following snippet:

// Remember, use the Glance imports // import androidx.glance.material3.ColorProviders // Example Imports from your own app // import com.example.myapp.ui.theme.DarkColors // import com.example.myapp.ui.theme.LightColors object MyAppWidgetGlanceColorScheme { val colors = ColorProviders( light = LightColors, dark = DarkColors ) }GlanceSnippets.kt

Provide the colors from the scheme to the GlanceTheme that wraps all your composables, as shown in the following example:

override suspend fun provideGlance(context: Context, id: GlanceId) { // ... provideContent { GlanceTheme(colors = MyAppWidgetGlanceColorScheme.colors) { MyContent() } } } @Composable private fun MyContent() { Image( colorFilter = ColorFilter.tint(GlanceTheme.colors.secondary), // ... ) }GlanceSnippets.kt

If you prefer to use dynamic colors from the wallpaper when supported, and your app's color scheme otherwise, you can conditionally pass your app's color scheme in the GlanceTheme. This is shown in the following snippet:

override suspend fun provideGlance(context: Context, id: GlanceId) { provideContent { GlanceTheme( if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) GlanceTheme.colors else MyAppWidgetGlanceColorScheme.colors ) { MyContent() } } } @Composable private fun MyContent() { // ... Image( colorFilter = ColorFilter.tint(GlanceTheme.colors.secondary), // ... ) }GlanceSnippets.kt

To provide special shapes or shadows to your app widget, use the Android Drawables API.

For example, the following snippet shows how to create a drawable (a shape):

Provide it to the target composable:

GlanceModifier.background( imageProvider = ImageProvider(R.drawable.button_outline) )GlanceSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<shape xmlns:android="http://schemas.android.com/apk/res/android">
    <corners android:radius="16dp"/>
    <stroke android:color="@color/outline_color" android:width="1dp"/>
</shape>button_outline.xml
```

---

## Show or hide password based on a user toggle Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/show-hide-password

**Contents:**
- Show or hide password based on a user toggle Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Show or hide a password based on user toggle
  - Key points about the code
- Collections that contain this guide
  - Display text
  - Request user input
- Have questions or feedback

You can create an icon to hide or show a password based on a user toggle to improve security and enhance the user experience.

Figure 1. Toggling the show-and-hide password icon.

This implementation requires that your project minSDK be set to API level 21 or higher.

To show or hide a password based on a user toggle, create an input field for entering information and use a clickable icon for the toggle:

@Composable fun PasswordTextField() { val state = remember { TextFieldState() } var showPassword by remember { mutableStateOf(false) } BasicSecureTextField( state = state, textObfuscationMode = if (showPassword) { TextObfuscationMode.Visible } else { TextObfuscationMode.RevealLastTyped }, modifier = Modifier .fillMaxWidth() .padding(6.dp) .border(1.dp, Color.LightGray, RoundedCornerShape(6.dp)) .padding(6.dp), decorator = { innerTextField -> Box(modifier = Modifier.fillMaxWidth()) { Box( modifier = Modifier .align(Alignment.CenterStart) .padding(start = 16.dp, end = 48.dp) ) { innerTextField() } Icon( if (showPassword) { Icons.Filled.Visibility } else { Icons.Filled.VisibilityOff }, contentDescription = "Toggle password visibility", modifier = Modifier .align(Alignment.CenterEnd) .requiredSize(48.dp).padding(16.dp) .clickable { showPassword = !showPassword } ) } } ) }TextSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Migrate existing View-based apps Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate

**Contents:**
- Migrate existing View-based apps Stay organized with collections Save and categorize content based on your preferences.
    - Samples

Jetpack Compose was designed with View interoperability right from the start—you can use Compose within Views, and vice versa. This functionality allows you to adopt Compose in your existing View-based app without having to rewrite it from scratch.

The following pages describe how to integrate Compose with your existing apps:

The following samples demonstrate Jetpack Compose and View interoperability:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Interoperability APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/interop

**Contents:**
- Interoperability APIs Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Incrementally migrating your app to Compose means that Compose and Views will co-exist in your app.

The following pages describe the specific APIs you need to use to integrate Compose within your existing View-based app:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Make your app fold aware Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/make-your-app-fold-aware

**Contents:**
- Make your app fold aware Stay organized with collections Save and categorize content based on your preferences.
- Window information
  - Kotlin flows
  - Java callbacks
  - RxJava support
- Features of foldable displays
  - Kotlin
  - Java
  - Tabletop posture
  - Kotlin

Large unfolded displays and unique folded states enable new user experiences on foldable devices. To make your app fold aware, use the Jetpack WindowManager library, which provides an API surface for foldable device window features such as folds and hinges. When your app is fold aware, it can adapt its layout to avoid placing important content in the area of folds or hinges and use folds and hinges as natural separators.

Understanding whether a device supports configurations such as tabletop or book posture can guide decisions about supporting different layouts or providing specific features.

The WindowInfoTracker interface in Jetpack WindowManager exposes window layout information. The interface's windowLayoutInfo() method returns a stream of WindowLayoutInfo data that informs your app about a foldable device's fold state. The WindowInfoTracker#getOrCreate() method creates an instance of WindowInfoTracker.

WindowManager provides support for collecting WindowLayoutInfo data using Kotlin flows and Java callbacks.

To start and stop WindowLayoutInfo data collection, you can use a restartable lifecycle-aware coroutine in which the repeatOnLifecycle code block is executed when the lifecycle is at least STARTED and is stopped when the lifecycle is STOPPED. Execution of the code block is automatically restarted when the lifecycle is STARTED again. In the following example, the code block collects and uses WindowLayoutInfo data:

The callback compatibility layer included in the androidx.window:window-java dependency enables you to collect WindowLayoutInfo updates without using a Kotlin flow. The artifact includes the WindowInfoTrackerCallbackAdapter class, which adapts a WindowInfoTracker to support registering (and unregistering) callbacks to receive WindowLayoutInfo updates, for example:

If you're already using RxJava (version 2 or 3), you can take advantage of artifacts that enable you to use an Observable or Flowable to collect WindowLayoutInfo updates without using a Kotlin flow.

The compatibility layer provided by the androidx.window:window-rxjava2 and androidx.window:window-rxjava3 dependencies includes the WindowInfoTracker#windowLayoutInfoFlowable() and WindowInfoTracker#windowLayoutInfoObservable() methods, which enable your app to receive WindowLayoutInfo updates, for example:

The WindowLayoutInfo class of Jetpack WindowManager makes the features of a display window available as a list of DisplayFeature elements.

A FoldingFeature is a type of DisplayFeature that provides information about foldable displays, including the following properties:

state: The folded state of the device, FLAT or HALF_OPENED

orientation: The orientation of the fold or hinge, HORIZONTAL or VERTICAL

occlusionType: Whether the fold or hinge conceals part of the display, NONE or FULL

isSeparating: Whether the fold or hinge creates two logical display areas, true or false

A foldable device that is HALF_OPENED always reports isSeparating as true because the screen is separated into two display areas. Also, isSeparating is always true on a dual‑screen device when the application spans both screens.

The FoldingFeature bounds property (inherited from DisplayFeature) represents the bounding rectangle of a folding feature such as a fold or hinge. The bounds can be used to position elements on screen relative to the feature:

Using the information included in the FoldingFeature object, your app can support postures like tabletop, where the phone sits on a surface, the hinge is in a horizontal position, and the foldable screen is half opened.

Tabletop posture offers users the convenience of operating their phones without holding the phone in their hands. Tabletop posture is great for watching media, taking photos, and making video calls.

Use FoldingFeature.State and FoldingFeature.Orientation to determine whether the device is in tabletop posture:

Once you know the device is in tabletop posture, update your app layout accordingly. For media apps, that typically means placing the playback above the fold and positioning controls and supplementary content just following for a hands‑free viewing or listening experience.

On Android 15 (API level 35) and higher, you can invoke a synchronous API to detect whether a device supports tabletop posture regardless of the current state of the device.

The API provides a list of postures supported by the device. If the list contains tabletop posture, you can split your app layout to support the posture and run A/B tests on your app UI for tabletop and full‑screen layouts.

MediaPlayerActivity app: See how to use Media3 Exoplayer and WindowManager to create a fold‑aware video player.

Optimize your camera app on foldable devices with Jetpack WindowManager codelab: Learn how to implement tabletop posture for photography apps. Show the viewfinder on the top half of the screen (above the fold) and the controls on the bottom half (below the fold).

Another unique foldable feature is book posture, where the device is half opened and the hinge is vertical. Book posture is great for reading e‑books. With a two‑page layout on a large screen foldable open like a bound book, book posture captures the experience of reading a real book.

It can also be used for photography if you want to capture a different aspect ratio while taking pictures hands‑free.

Implement book posture with the same techniques used for tabletop posture. The only difference is the code should check that the folding feature orientation is vertical instead of horizontal:

An app's display area can change as a result of a device configuration change, for example, when the device is folded or unfolded, rotated, or a window is resized in multi‑window mode.

The Jetpack WindowManager WindowMetricsCalculator class enables you to retrieve the current and maximum window metrics. Like the platform WindowMetrics introduced in API level 30, the WindowManager WindowMetrics provide the window bounds, but the API is backward compatible down to API level 14.

See Use window size classes.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-10-21 UTC.

**Examples:**

Example 1 (swift):
```swift
class DisplayFeaturesActivity : AppCompatActivity() {

    private lateinit var binding: ActivityDisplayFeaturesBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        binding = ActivityDisplayFeaturesBinding.inflate(layoutInflater)
        setContentView(binding.root)

        lifecycleScope.launch(Dispatchers.Main) {
            lifecycle.repeatOnLifecycle(Lifecycle.State.STARTED) {
                WindowInfoTracker.getOrCreate(this@DisplayFeaturesActivity)
                    .windowLayoutInfo(this@DisplayFeaturesActivity)
                    .collect { newLayoutInfo ->
                        // Use newLayoutInfo to update the layout.
                    }
            }
        }
    }
}
```

Example 2 (java):
```java
public class SplitLayoutActivity extends AppCompatActivity {

    private WindowInfoTrackerCallbackAdapter windowInfoTracker;
    private ActivitySplitLayoutBinding binding;
    private final LayoutStateChangeCallback layoutStateChangeCallback =
            new LayoutStateChangeCallback();

   @Override
   protected void onCreate(@Nullable Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);

       binding = ActivitySplitLayoutBinding.inflate(getLayoutInflater());
       setContentView(binding.getRoot());

       windowInfoTracker =
                new WindowInfoTrackerCallbackAdapter(WindowInfoTracker.getOrCreate(this));
   }

   @Override
   protected void onStart() {
       super.onStart();
       windowInfoTracker.addWindowLayoutInfoListener(
                this, Runnable::run, layoutStateChangeCallback);
   }

   @Override
   protected void onStop() {
       super.onStop();
       windowInfoTracker
           .removeWindowLayoutInfoListener(layoutStateChangeCallback);
   }

   class LayoutStateChangeCallback implements Consumer<WindowLayoutInfo> {
       @Override
       public void accept(WindowLayoutInfo newLayoutInfo) {
           SplitLayoutActivity.this.runOnUiThread( () -> {
               // Use newLayoutInfo to update the layout.
           });
       }
   }
}
```

Example 3 (swift):
```swift
class RxActivity: AppCompatActivity {

    private lateinit var binding: ActivityRxBinding

    private var disposable: Disposable? = null
    private lateinit var observable: Observable<WindowLayoutInfo>

   @Override
   protected void onCreate(@Nullable Bundle savedInstanceState) {
       super.onCreate(savedInstanceState);

       binding = ActivitySplitLayoutBinding.inflate(getLayoutInflater());
       setContentView(binding.getRoot());

        // Create a new observable.
        observable = WindowInfoTracker.getOrCreate(this@RxActivity)
            .windowLayoutInfoObservable(this@RxActivity)
   }

   @Override
   protected void onStart() {
       super.onStart();

        // Subscribe to receive WindowLayoutInfo updates.
        disposable?.dispose()
        disposable = observable
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe { newLayoutInfo ->
            // Use newLayoutInfo to update the layout.
        }
   }

   @Override
   protected void onStop() {
       super.onStop();

        // Dispose of the WindowLayoutInfo observable.
        disposable?.dispose()
   }
}
```

Example 4 (swift):
```swift
override fun onCreate(savedInstanceState: Bundle?) {
    // ...
    lifecycleScope.launch(Dispatchers.Main) {
        lifecycle.repeatOnLifecycle(Lifecycle.State.STARTED) {
            // Safely collects from WindowInfoTracker when the lifecycle is
            // STARTED and stops collection when the lifecycle is STOPPED.
            WindowInfoTracker.getOrCreate(this@MainActivity)
                .windowLayoutInfo(this@MainActivity)
                .collect { layoutInfo ->
                    // New posture information.
                    val foldingFeature = layoutInfo.displayFeatures
                        .filterIsInstance<FoldingFeature>()
                        .firstOrNull()
                    // Use information from the foldingFeature object.
                }
        }
    }
}
```

---

## Support desktop windowing Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/support-desktop-windowing

**Contents:**
- Support desktop windowing Stay organized with collections Save and categorize content based on your preferences.
- Resizability and compatibility mode
- Customizable header insets
- Multitasking and multi-instance support
- Manage app instances with dragging gestures
- Additional optimizations
  - Specify default size and position
  - Enter full-screen from the desktop space

Desktop windowing enables users to run multiple apps simultaneously in resizable app windows for a versatile, desktop-like experience.

In figure 1, you can see the organization of the screen with desktop windowing enabled. Things to note:

By default, apps open full screen on Android tablets. To launch an app in desktop windowing, press and hold the window handle at the top of the screen and drag the handle within the UI, as seen in figure 2.

When an app is open in desktop windowing, other apps open in desktop windows as well.

Users can also invoke desktop windowing from the menu that shows up below the window handle when you tap or click the handle or use the keyboard shortcut Meta key (Windows, Command, or Search) + Ctrl + Down.

Users exit desktop windowing by closing all active windows or by grabbing the window handle at the top of a desktop window and dragging the app to the top of the screen. The Meta + H keyboard shortcut also exits desktop windowing and runs apps full screen again.

To return to desktop windowing, tap or click the desktop space tile in the Recents screen.

In desktop windowing, apps with locked orientation are freely resizable. That means even if an activity is locked to portrait orientation, users can still resize the app to a landscape orientation window.

Apps declared as nonresizable (that is, resizeableActivity = false) have their UI scaled while keeping the same aspect ratio.

Camera apps that lock the orientation or are declared as nonresizable have a special treatment for their camera viewfinders: the window is fully resizable, but the viewfinder keeps the same aspect ratio. By assuming apps always run in portrait or landscape, the apps hardcode or otherwise make assumptions that lead to miscalculations of the preview or captured image orientation or aspect ratio resulting in stretched, sideways, or upside-down images.

Until apps are ready to implement fully responsive camera viewfinders, the special treatment provides a more basic user experience that mitigates the effects wrong assumptions may cause.

To learn more about compatibility mode for camera apps, see Device compatibility mode.

All apps running in desktop windowing have a header bar, even in immersive mode. Ensure your app's content isn't obscured by the header bar. The header bar is a caption bar inset type: androidx.compose.foundation.layout.WindowInsets.Companion.captionBar(); in views, WindowInsets.Type.captionBar(), which is part of the system bars.

You can learn more about handling insets in Display content edge-to-edge in your app and handle window insets in Compose.

The header bar is also customizable. Android 15 introduced the appearance type APPEARANCE_TRANSPARENT_CAPTION_BAR_BACKGROUND to make the header bar transparent to allow apps to draw custom content inside the header bar.

Apps then become responsible for styling the top portion of their content to look like the caption bar (background, custom content, and so forth) with the exception of the system caption elements (close and maximize buttons), which are drawn by the system on the transparent caption bar on top of the app.

Apps can toggle the appearance of the system elements inside the caption for light and dark themes using APPEARANCE_LIGHT_CAPTION_BARS, similar to how the status bar and navbar are toggled.

Android 15 also introduced the WindowInsets#getBoundingRects() method which enables apps to introspect caption bar insets in more detail. Apps can differentiate between areas where the system draws system elements and unutilized areas where apps can place custom content without overlapping system elements.

The list of Rect objects returned by the API indicates system regions that should be avoided. Any remaining space (calculated by subtracting the rectangles from the caption bar Insets) is where the app can draw without overlapping system elements and with the ability to receive input.

Multitasking is at the core of desktop windowing, and allowing multiple instances of your app can highly increase users productivity.

Android 15 introduces PROPERTY_SUPPORTS_MULTI_INSTANCE_SYSTEM_UI, which apps can set to specify that system UI should be shown for the app to allow it to be launched as multiple instances.

In multi-window mode, users can start a new app instance by dragging a view element out of the app's window. Users can also move elements between instances of the same app.

Android 15 introduces two flags to customize drag and drop behavior:

DRAG_FLAG_START_INTENT_SENDER_ON_UNHANDLED_DRAG: Indicates that an unhandled drag should be delegated to the system to be started if no visible window handles the drop. When using this flag, the caller must provide ClipData with an Item that contains an immutable IntentSender to an activity to be launched (see ClipData.Item.Builder#setIntentSender()). The system can launch the intent or not based on factors like the current screen size or windowing mode. If the system does not launch the intent, the intent is canceled by means of the normal drag and drop flow.

DRAG_FLAG_GLOBAL_SAME_APPLICATION: Indicates that a drag operation can cross window boundaries (for multiple instances of the same application).

When startDragAndDrop() is called with this flag set, only visible windows belonging to the same application are able to participate in the drag operation and receive the dragged content.

Customize app launches and transition apps from desktop windowing to full screen.

Not all apps, even if resizable, need a large window to offer user value. You can use the ActivityOptions#setLaunchBounds() method to specify a default size and position when an activity is launched.

Apps can go full-screen by calling Activity#requestFullScreenMode(). The method displays the app full screen directly from desktop windowing.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-11-18 UTC.

---

## Animation tooling support Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/tooling

**Contents:**
- Animation tooling support Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Android Studio supports inspection of animate*AsState, CrossFade, rememberInfiniteTransition, AnimatedContent, updateTransition, and animatedVisibility in Animation Preview. You can do the following:

When you start Animation Preview, you see the Animations pane, where you can run any transition included in the preview. The transition, as well as each of its animation values, is labeled with a default name. You can customize the label by specifying the label parameter in the updateTransition and the AnimatedVisibility functions. For more information, see Animation Preview.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## About adaptive layouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive

**Contents:**
- About adaptive layouts Stay organized with collections Save and categorize content based on your preferences.
- Topics
- Prerequisites

Android apps run on a wide variety of devices—from foldable flip phones to wall‑mounted TVs. To provide a great user experience on all types of devices, adapt your app's UI to different display sizes and configurations. The best Android apps make the most of the screen space they occupy and handle changes to that space at runtime, including orientation changes and window resizing in split‑screen and desktop windowing modes.

The adaptive layouts documentation provides guidance on how to:

The adaptive layouts guidance assumes you understand the following concepts:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-06-10 UTC.

---

## Set up Predictive back Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/predictive-back-setup

**Contents:**
- Set up Predictive back Stay organized with collections Save and categorize content based on your preferences.
- Enable default system animations
- Enable predictive back with Navigation Compose
- Integrate with shared element transitions
- Support predictive back with Material Compose components
- Test the predictive back gesture animation
- Additional resources

Predictive back and system animations are enabled by default. If your app intercepts the back event and you haven't migrated to predictive back, update your app to use supported back navigation APIs

The back-to-home, cross-activity, and cross-task system animations are available on Android 15 and later devices for apps that have migrated to the supported back handling APIs.

These animations are enabled by default on Android 15 and higher. On devices running Android 13 or 14, users can enable them through the Developer options.

To get the system animations, update your AndroidX Activity dependency to 1.6.0 or higher.

To use predictive back in Navigation Compose, ensure you're using the navigation-compose 2.8.0 library or higher.

Navigation Compose automatically cross-fades between screens when the user swipes back:

When navigating, you can create custom transitions with popEnterTransition and popExitTransition. When applied to your NavHost, these modifiers let you define how the enter and exit screens animate. You can use them to create a variety of effects, such as scaling, fading, or sliding.

In this example, scaleOut is used within popExitTransition to scale down the exiting screen as the user navigates back. Additionally, the transformOrigin parameter determines the point around which the scaling animation occurs. By default, it's the center of the screen (0.5f, 0.5f). You can adjust this value to make the scaling originate from a different point.

NavHost( navController = navController, startDestination = Home, popExitTransition = { scaleOut( targetScale = 0.9f, transformOrigin = TransformOrigin(pivotFractionX = 0.5f, pivotFractionY = 0.5f) ) }, popEnterTransition = { EnterTransition.None }, modifier = modifier, )PredictiveBackSnippets.kt

This code produces the following result:

popEnterTransition and popExitTransition specifically control animations when popping the back stack, with a back gesture, for example. You can also use enterTransition and exitTransition to define animations for entering and exiting composables in general, not only for predictive back. If you only set enterTransition and exitTransition, they are used for both regular navigation and popping the back stack. However, using popEnterTransition and popExitTransition lets you create distinct animations for back navigation.

Shared element transitions provide a smooth visual connection between composables with shared content, often used for navigation.

To use shared elements with Navigation Compose, see Predictive back with shared elements.

Many components in the Material Compose library are designed to work seamlessly with predictive back gestures. To enable predictive back animations in these components, include the latest Material3 dependency (androidx.compose.material3:material3-*:1.3.0 or higher) in your project.

The Material components that support predictive back animations include:

SearchBar and ModalBottomSheet automatically animate with predictive back gestures. ModalNavigationDrawer, ModalDrawerSheet, DismissibleDrawerSheet, and DismissibleNavigationDrawer require you to pass the drawerState to their respective sheet content composables.

If you still use Android 13 or Android 14, you can test the back-to-home animation.

To test this animation, follow these steps:

On Android 15 and later, this feature is enabled by default.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Create an app widget with Glance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/create-app-widget

**Contents:**
- Create an app widget with Glance Stay organized with collections Save and categorize content based on your preferences.
- Declare the AppWidget in the Manifest
- Add the AppWidgetProviderInfo metadata
- Define GlanceAppWidget
- Create UI

The following sections describe how to create a simple app widget with Glance.

After completing the setup steps, declare the AppWidget and its metadata in your app.

Extend the AppWidget receiver from GlanceAppWidgetReceiver:

class MyAppWidgetReceiver : GlanceAppWidgetReceiver() { override val glanceAppWidget: GlanceAppWidget = TODO("Create GlanceAppWidget") }GlanceSnippets.kt

Register the provider of the app widget in your AndroidManifest.xml file and the associated metadata file:

Next, follow the Create a simple widget guide to create and define the app widget info in the @xml/my_app_widget_info file.

The only difference for Glance is that there is no initialLayout XML, but you must define one. You can use the predefined loading layout provided in the library:

Create a new class that extends from GlanceAppWidget and overrides the provideGlance method. This is the method where you can load data that is needed to render your widget:

class MyAppWidget : GlanceAppWidget() { override suspend fun provideGlance(context: Context, id: GlanceId) { // In this method, load data needed to render the AppWidget. // Use `withContext` to switch to another thread for long running // operations. provideContent { // create your AppWidget here Text("Hello World") } } }GlanceSnippets.kt

Instantiate it in the glanceAppWidget on your GlanceAppWidgetReceiver:

class MyAppWidgetReceiver : GlanceAppWidgetReceiver() { // Let MyAppWidgetReceiver know which GlanceAppWidget to use override val glanceAppWidget: GlanceAppWidget = MyAppWidget() }GlanceSnippets.kt

You've now configured an AppWidget using Glance.

The following snippet demonstrates how to create the UI:

/* Import Glance Composables In the event there is a name clash with the Compose classes of the same name, you may rename the imports per https://kotlinlang.org/docs/packages.html#imports using the `as` keyword. import androidx.glance.Button import androidx.glance.layout.Column import androidx.glance.layout.Row import androidx.glance.text.Text */ class MyAppWidget : GlanceAppWidget() { override suspend fun provideGlance(context: Context, id: GlanceId) { // Load data needed to render the AppWidget. // Use `withContext` to switch to another thread for long running // operations. provideContent { // create your AppWidget here MyContent() } } @Composable private fun MyContent() { Column( modifier = GlanceModifier.fillMaxSize(), verticalAlignment = Alignment.Top, horizontalAlignment = Alignment.CenterHorizontally ) { Text(text = "Where to?", modifier = GlanceModifier.padding(12.dp)) Row(horizontalAlignment = Alignment.CenterHorizontally) { Button( text = "Home", onClick = actionStartActivity<MyActivity>() ) Button( text = "Work", onClick = actionStartActivity<MyActivity>() ) } } } }GlanceSnippets.kt

The preceding code sample does the following:

You can change the alignment values or apply different modifier values (such as padding) to change the placement and size of the components. See the reference documentation for a full list of components, parameters, and available modifiers for each class.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<receiver android:name=".glance.MyReceiver"
    android:exported="true">
    <intent-filter>
        <action android:name="android.appwidget.action.APPWIDGET_UPDATE" />
    </intent-filter>
    <meta-data
        android:name="android.appwidget.provider"
        android:resource="@xml/my_app_widget_info" />
</receiver>AndroidManifest.xml
```

Example 2 (typescript):
```typescript
<appwidget-provider xmlns:android="http://schemas.android.com/apk/res/android"
    android:initialLayout="@layout/glance_default_loading_layout">
</appwidget-provider>my_app_widget_info.xml
```

---

## Jetpack Glance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance

**Contents:**
- Jetpack Glance Stay organized with collections Save and categorize content based on your preferences.
- Additional resources

Jetpack Glance is a framework built on top of the Jetpack Compose runtime that lets you develop and design app widgets using Kotlin APIs. App widgets are miniature application views that can be embedded in other applications and receive periodic updates.

Glance provides a set of composables to help you build responsive widgets for the home screen quickly and with less code. The pages in this doc set describe how to use Glance to build app widgets.

For more information on canonical layouts visit Canonical widget layouts. For more information on making your widget high quality and discoverable, see Widget quality.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-05-13 UTC.

---

## Design systems in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems

**Contents:**
- Design systems in Compose Stay organized with collections Save and categorize content based on your preferences.
  - Samples
- Recommended for you

Jetpack Compose makes it easy to implement a design system and give your app a consistent look and feel with theming, components, and more.

The following pages provide details on how to design and implement your design system:

The following samples demonstrate design systems in Jetpack Compose:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Validate input as the user types Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/validate-input

**Contents:**
- Validate input as the user types Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Validate input as the user types
  - Key points about the code
- Collections that contain this guide
  - Display text
  - Request user input
- Have questions or feedback

You can validate input as the user types in a text field, such as entering a name, email, address, or other contact information. This validation reduces errors and saves your users time.

This implementation requires that your project minSDK be set to API level 21 or higher.

Use the following code to display the field input and validate the text while the user types. If the information is not validated, an error message helps the user correct the input.

class EmailViewModel : ViewModel() { var email by mutableStateOf("") private set val emailHasErrors by derivedStateOf { if (email.isNotEmpty()) { // Email is considered erroneous until it completely matches EMAIL_ADDRESS. !android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches() } else { false } } fun updateEmail(input: String) { email = input } } @Composable fun ValidatingInputTextField( email: String, updateState: (String) -> Unit, validatorHasErrors: Boolean ) { OutlinedTextField( modifier = Modifier .fillMaxWidth() .padding(10.dp), value = email, onValueChange = updateState, label = { Text("Email") }, isError = validatorHasErrors, supportingText = { if (validatorHasErrors) { Text("Incorrect email format.") } } ) } @Preview @Composable fun ValidateInput() { val emailViewModel: EmailViewModel = viewModel<EmailViewModel>() ValidatingInputTextField( email = emailViewModel.email, updateState = { input -> emailViewModel.updateEmail(input) }, validatorHasErrors = emailViewModel.emailHasErrors ) }TextSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Support foldable display modes Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/support-foldable-display-modes

**Contents:**
- Support foldable display modes Stay organized with collections Save and categorize content based on your preferences.
- Rear display mode
- Dual-screen mode
- Enable the modes programmatically
  - Groovy
  - Kotlin
  - Kotlin
  - Java
  - Kotlin
  - Java

Foldable devices offer unique viewing experiences. Rear display mode and dual‑screen mode enable you to build special display features for foldable devices such as rear‑camera selfie preview and simultaneous but different displays on inner and outer screens.

Typically when a foldable device is unfolded, only the inner screen is active. Rear display mode lets you move an activity to the outer screen of a foldable device, which usually faces away from the user while the device is unfolded. The inner display automatically turns off.

A novel application is to display the camera preview on the outer screen, so users can take selfies with the rear camera, which usually provides much better picture‑taking performance than the front camera.

To activate rear display mode, users respond to a dialog to allow the app to switch screens, for example:

The system creates the dialog, so no development on your part is required. Different dialogs appear depending on the device state; for example, the system directs users to unfold the device if the device is closed. You can't customize the dialog, and it can vary on devices from different OEMs.

You can try out rear display mode with the Pixel Fold camera app. See a sample implementation in the codelab Optimize your camera app on foldable devices with Jetpack WindowManager.

Dual-screen mode lets you show content on both displays of a foldable at the same time. Dual‑screen mode is available on Pixel Fold running Android 14 (API level 34) or higher.

An example use case is the dual-screen interpreter.

You can access rear display mode and dual‑screen mode through the Jetpack WindowManager APIs, starting from library version 1.2.0-beta03.

Add the WindowManager dependency to your app's module build.gradle file:

The entry point is the WindowAreaController, which provides the information and behavior related to moving windows between displays or between display areas on a device. WindowAreaController lets you query the list of available WindowAreaInfo objects.

Use WindowAreaInfo to access the WindowAreaSession, an interface that represents an active window area feature. Use WindowAreaSession to determine the availability of a specific WindowAreaCapability.

Each capability is related to a particular WindowAreaCapability.Operation. In version 1.2.0-beta03, Jetpack WindowManager supports two kinds of operations:

Here's an example of how to declare variables for rear display mode and dual‑screen mode in your app's main activity:

Here's how to initialize the variables in the onCreate() method of your activity:

Before starting an operation, check the availability of the particular capability:

The following example closes the session if the capability is already active, or otherwise calls the presentContentOnWindowArea() function:

Notice the use of the app's main activity as the WindowAreaPresentationSessionCallback argument.

The API uses a listener approach: when you make a request to present the content to the other display of a foldable, you initiate a session that is returned through the listener's onSessionStarted() method. When you close the session, you get a confirmation in the onSessionEnded() method.

To create the listener, implement the WindowAreaPresentationSessionCallback interface:

The listener needs to implement the onSessionStarted(), onSessionEnded(), and onContainerVisibilityChanged() methods. The callback methods notify you of the session status and enable you to update the app accordingly.

The onSessionStarted() callback receives a WindowAreaSessionPresenter as an argument. The argument is the container that lets you access a window area and show content. The presentation can be automatically dismissed by the system when the user leaves the primary application window, or the presentation can be closed by calling WindowAreaSessionPresenter#close().

For the other callbacks, for simplicity, just check in the function body for any errors, and log the state:

To maintain consistency across the ecosystem, use the Dual Screen official icon to indicate to users how to enable or disable dual‑screen mode.

For a working sample, see DualScreenActivity.kt.

Similar to the dual‑screen mode example, the following example of a toggleRearDisplayMode() function closes the session if the capability is already active, or otherwise calls the transferActivityToWindowArea() function:

In this case, the activity displayed is used as a WindowAreaSessionCallback, which is simpler to implement because the callback doesn't receive a presenter that allows showing content on a window area but instead transfers the whole activity to another area:

To maintain consistency across the ecosystem, use the Rear Camera official icon to indicate to users how to enable or disable rear display mode.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-10-24 UTC.

**Examples:**

Example 1 (bash):
```bash
dependencies {
    // TODO: Define window_version in your project's build configuration.
    implementation "androidx.window:window:$window_version"
}
```

Example 2 (bash):
```bash
dependencies {
    // Define window_version in your project's build configuration.
    implementation("androidx.window:window:$window_version")
}
```

Example 3 (swift):
```swift
private lateinit var windowAreaController: WindowAreaController
private lateinit var displayExecutor: Executor
private var windowAreaSession: WindowAreaSession? = null
private var windowAreaInfo: WindowAreaInfo? = null
private var capabilityStatus: WindowAreaCapability.Status =
    WindowAreaCapability.Status.WINDOW_AREA_STATUS_UNSUPPORTED

private val dualScreenOperation = WindowAreaCapability.Operation.OPERATION_PRESENT_ON_AREA
private val rearDisplayOperation = WindowAreaCapability.Operation.OPERATION_TRANSFER_ACTIVITY_TO_AREA
```

Example 4 (java):
```java
private WindowAreaControllerCallbackAdapter windowAreaController = null;
private Executor displayExecutor = null;
private WindowAreaSessionPresenter windowAreaSession = null;
private WindowAreaInfo windowAreaInfo = null;
private WindowAreaCapability.Status capabilityStatus  =
        WindowAreaCapability.Status.WINDOW_AREA_STATUS_UNSUPPORTED;

private WindowAreaCapability.Operation dualScreenOperation =
        WindowAreaCapability.Operation.OPERATION_PRESENT_ON_AREA;
private WindowAreaCapability.Operation rearDisplayOperation =
        WindowAreaCapability.Operation.OPERATION_TRANSFER_ACTIVITY_TO_AREA;
```

---

## Use window size classes Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/window-size-classes

**Contents:**
- Use window size classes Stay organized with collections Save and categorize content based on your preferences.
- Manage layouts with window size classes
- Test window size classes
- Next steps

Window size classes are a set of opinionated viewport breakpoints that help you design, develop, and test responsive/adaptive layouts. The breakpoints balance layout simplicity with the flexibility of optimizing your app for unique cases.

Window size classes categorize the display area available to your app as compact, medium, expanded, large, or extra large. Available width and height are classified separately, so at any point in time, your app has two window size classes—one for width, one for height. Available width is usually more important than available height due to the ubiquity of vertical scrolling, so the width window size class is likely more relevant to your app's UI.

As visualized in the figures, the breakpoints allow you to continue thinking about layouts in terms of devices and configurations. Each size class breakpoint represents a majority case for typical device scenarios, which can be a helpful frame of reference as you think about the design of your breakpoint-based layouts.

most large unfolded inner displays in portrait

most large unfolded inner displays in landscape are at least expanded width

97.59% of phones in portrait

Although visualizing size classes as physical devices can be useful, window size classes are explicitly not determined by the size of the device screen. Window size classes are not intended for isTablet‑type logic. Rather, window size classes are determined by the window size available to your application regardless of the type of device the app is running on, which has two important implications:

Physical devices do not guarantee a specific window size class. The screen space available to your app can differ from the screen size of the device for many reasons. On mobile devices, split‑screen mode can partition the screen between two applications. On ChromeOS, Android apps can be presented in desktop‑type windows that are arbitrarily resizable. Foldables can have two different‑sized screens individually accessed by folding or unfolding the device.

The window size class can change throughout the lifetime of your app. While your app is running, device orientation changes, multitasking, and folding/unfolding can change the amount of screen space available. As a result, the window size class is dynamic, and your app's UI should adapt accordingly.

Window size classes map to the compact, medium, and expanded breakpoints in the Material Design layout guidance. Additionally, large and extra-large breakpoints have been added to better target desktop and connected displays.

Use window size classes to make high‑level application layout decisions, such as deciding whether to use a specific canonical layout to take advantage of additional screen space.

Compute the current WindowSizeClass using the currentWindowAdaptiveInfo() top‑level function of the androidx.compose.material3.adaptive library. The function returns an instance of WindowAdaptiveInfo, which contains windowSizeClass. To support large and extra-large breakpoints, add the supportLargeAndXLargeWidth parameter set to true to the function call. The following example shows how to calculate the window size class and receive updates whenever the window size class changes:

val windowSizeClass = currentWindowAdaptiveInfo().windowSizeClassAdaptiveLayoutSnippets.kt

Window size classes enable you to change your app layout as the display space available to your app changes, for example, when a device folds or unfolds, the device orientation changes, or the app window is resized in multi‑window mode.

Localize the logic for handling display size changes by passing window size classes down as state to nested composables just like any other app state:

@Composable fun MyApp( windowSizeClass: WindowSizeClass = currentWindowAdaptiveInfo(supportLargeAndXLargeWidth = true).windowSizeClass ) { // Decide whether to show the top app bar based on window size class. val showTopAppBar = windowSizeClass.isHeightAtLeastBreakpoint(WindowSizeClass.HEIGHT_DP_MEDIUM_LOWER_BOUND) // MyScreen logic is based on the showTopAppBar boolean flag. MyScreen( showTopAppBar = showTopAppBar, /* ... */ ) }AdaptiveLayoutSnippets.kt

As you make layout changes, test the layout behavior across all window sizes, especially at the compact, medium, and expanded breakpoint widths.

If you have an existing layout for compact screens, first optimize your layout for the expanded width size class, since this size class provides the most space for additional content and UI changes. Then decide what layout makes sense for the medium width size class; consider adding a specialized layout.

To learn more about how to use window size classes to create responsive/adaptive layouts, see the following:

For Compose-based layouts: Support different display sizes

For view-based layouts: Responsive/adaptive design with views

To learn more about what makes an app great on all devices and screen sizes, see:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-14 UTC.

---

## About system bar protection Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/system-bars

**Contents:**
- About system bar protection Stay organized with collections Save and categorize content based on your preferences.
- Create transparent system bars
- Create translucent system bars

Once your app targets SDK 35 or later, edge-to-edge is enforced. The system status bar and gesture navigation bars are transparent, but the three-button navigation bar is translucent. Call enableEdgeToEdge to make this backwards compatible.

However, the system defaults might not work for all use cases. Consult the Android system bars design guidance and edge-to-edge design guidance for an overview of when to consider having transparent or translucent system bars.

Create a transparent gesture navigation bar by targeting Android 15 or later or by calling enableEdgeToEdge() with default arguments for earlier versions. For three-button navigation bar, set Window.setNavigationBarContrastEnforced to false otherwise there will be a translucent scrim applied.

To create a translucent status bar, create a custom composable that overlaps the main content and draws a gradient in the area covered by insets.

class SystemBarProtectionSnippets : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) // enableEdgeToEdge sets window.isNavigationBarContrastEnforced = true // which is used to add a translucent scrim to three-button navigation enableEdgeToEdge() setContent { MyTheme { // Main content MyContent() // After drawing main content, draw status bar protection StatusBarProtection() } } } } @Composable private fun StatusBarProtection( color: Color = MaterialTheme.colorScheme.surfaceContainer, heightProvider: () -> Float = calculateGradientHeight(), ) { Canvas(Modifier.fillMaxSize()) { val calculatedHeight = heightProvider() val gradient = Brush.verticalGradient( colors = listOf( color.copy(alpha = 1f), color.copy(alpha = .8f), Color.Transparent ), startY = 0f, endY = calculatedHeight ) drawRect( brush = gradient, size = Size(size.width, calculatedHeight), ) } } @Composable fun calculateGradientHeight(): () -> Float { val statusBars = WindowInsets.statusBars val density = LocalDensity.current return { statusBars.getTop(density).times(1.2f) } }SystemBarProtectionSnippets.kt

For adaptive apps, insert a custom composable that matches the colors of each pane, as seen in the Edge-to–edge design. To create a translucent navigation bar, set Window.setNavigationBarContrastEnforced to true.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Navigation bar Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/navigation-bar

**Contents:**
- Navigation bar Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Example: Bottom navigation bar
  - Key points
  - Result
- Additional resources

The navigation bar allows users to switch between destinations in an app. You should use navigation bars for:

This page shows you how to display a navigation bar in your app with related screens and basic navigation.

Use the NavigationBar and NavigationBarItem composables to implement destination switching logic. Each NavigationBarItem represents a singular destination.

NavigationBarItem includes the following key parameters:

The following snippet implements a bottom navigation bar with items so users can navigate between different screens in an app:

@Composable fun NavigationBarExample(modifier: Modifier = Modifier) { val navController = rememberNavController() val startDestination = Destination.SONGS var selectedDestination by rememberSaveable { mutableIntStateOf(startDestination.ordinal) } Scaffold( modifier = modifier, bottomBar = { NavigationBar(windowInsets = NavigationBarDefaults.windowInsets) { Destination.entries.forEachIndexed { index, destination -> NavigationBarItem( selected = selectedDestination == index, onClick = { navController.navigate(route = destination.route) selectedDestination = index }, icon = { Icon( destination.icon, contentDescription = destination.contentDescription ) }, label = { Text(destination.label) } ) } } } ) { contentPadding -> AppNavHost(navController, startDestination, modifier = Modifier.padding(contentPadding)) } }Navigation.kt

The following image shows the navigation bar resulting from the previous snippet:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Lists in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/video/lists-in-compose

**Contents:**
- Lists in Compose Stay organized with collections Save and categorize content based on your preferences.
- Key points
- Collections that contain this guide
  - Display a list or grid
  - Compose basics (video collection)
- Have questions or feedback

Explore Compose's lazy components, which make it easy to display lists of items. Learn how to show different item types, implement sticky headers, and programmatically control or react to the scroll-position changes.

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/collections/display-a-list-or-grid

**Contents:**
- Quick Guides Collection Display a list or grid
  - Create a finite scrollable list
  - Create a scrollable grid
  - Build a list using multiple item types
  - Lazily load data with lists and Paging
  - Create a parallax scrolling effect
  - Display nested scrolling items in a list
  - Create a button to enable snap scrolling
  - Display a paging list
  - Lists in Compose

Explore Compose's Lazy components that make it easy to display lists of items. Learn how to show different item types and even how to implement sticky headers. See how to programmatically control or react to the scroll-position changes.

Compose gives you a simpler and more-performant way of creating scrolling lists, using fewer lines of code than RecyclerView. Learn how to use lazy layouts to create lists that let add content to lists, on demand.

See how to build complex designs for your Compose layouts, focusing on layout phase and constraints, subcompose layouts, and intrinsic measurements.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/collections/display-text

**Contents:**
- Quick Guides CollectionDisplay text
  - Style parts of text
  - Support multiple links in a single string of text
  - Animate text character-by-character as the user type
  - Display pop-up messages or request user input
  - Draw text in Compose
  - Accessibility in Compose
- Have questions or feedback

See how to use Compose APIs specifically designed to draw text on a canvas. This segment shows the code to draw an emoji font in a rounded rectangle.

Add accessibility features to your app, transforming what's shown on screen to a more fitting format for users with specific needs. See how to increase your app's reach and versatility with a small amount of work.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=text

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Support different display sizes Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/support-different-display-sizes

**Contents:**
- Support different display sizes Stay organized with collections Save and categorize content based on your preferences.
- Make large layout changes for content-level composables explicit
- Flexible nested composables are reusable
- Make all data available for different display sizes
- Learn more

Support for different display sizes enables access to your app by the widest variety of devices and greatest number of users.

To support as many display sizes as possible—whether different device screens or different app windows in multi-window mode—design your app layouts to be responsive and adaptive. Responsive/adaptive layouts provide an optimized user experience regardless of display size, enabling your app to accommodate phones, tablets, foldables, ChromeOS devices, portrait and landscape orientations, and resizable display configurations such as split‑screen mode and desktop windowing.

Responsive/adaptive layouts change based on available display space. Changes range from small layout adjustments that fill up space (responsive design) to completely replacing one layout with another so your app can best accommodate different display sizes (adaptive design).

As a declarative UI toolkit, Jetpack Compose is ideal for designing and implementing layouts that dynamically change to render content differently on different display sizes.

App-level and content-level composables occupy all of the display space available to your app. For these types of composables, it might make sense to change the overall layout of your app on large displays.

Avoid using physical hardware values for making layout decisions. It might be tempting to make decisions based on a fixed tangible value (Is the device a tablet? Does the physical screen have a certain aspect ratio?), but the answers to these questions may not be useful for determining the space available for your UI.

On tablets, an app might be running in multi-window mode, which means the app may be splitting the screen with another app. In desktop windowing mode or on ChromeOS, an app might be in a resizable window. There might even be more than one physical screen, such as with a foldable device. In all of these cases, the physical screen size isn't relevant for deciding how to display content.

Instead, make decisions based on the actual portion of the screen allocated to your app described by the current window metrics provided by the Jetpack WindowManager library. For an example of how to use WindowManager in a Compose app, see the JetNews sample.

Making your layouts adaptive to the available display space also reduces the amount of special handling needed to support platforms like ChromeOS and form factors like tablets and foldables.

When you've determined the metrics of the space available for your app, convert the raw size into a window size class as described in Use window size classes. Window size classes are breakpoints designed to balance app logic simplicity with the flexibility to optimize your app for most display sizes.

Window size classes refer to the overall window of your app, so use the classes for layout decisions that affect your overall app layout. You can pass window size classes down as state, or you can perform additional logic to create derived state to pass down to nested composables.

@Composable fun MyApp( windowSizeClass: WindowSizeClass = currentWindowAdaptiveInfo(supportLargeAndXLargeWidth = true).windowSizeClass ) { // Decide whether to show the top app bar based on window size class. val showTopAppBar = windowSizeClass.isHeightAtLeastBreakpoint(WindowSizeClass.HEIGHT_DP_MEDIUM_LOWER_BOUND) // MyScreen logic is based on the showTopAppBar boolean flag. MyScreen( showTopAppBar = showTopAppBar, /* ... */ ) }AdaptiveLayoutSnippets.kt

A layered approach confines display size logic to a single location instead of scattering it across your app in many places that need to be kept in sync. A single location produces state, which can be explicitly passed down to other composables just like any other app state. Explicitly passing state simplifies individual composables because the composables take the window size class or specified configuration along with other data.

Composables are more reusable when they can be placed in a wide variety of places. If a composable must be placed in a specific location with a specific size, the composable is unlikely to be reusable in other contexts. This also means that individual, reusable composables should avoid implicitly depending on global display size information.

Imagine a nested composable that implements a list-detail layout, which may show either a single pane or two panes side by side:

The list-detail decision should be part of the overall layout for the app, so the decision is passed down from a content-level composable:

@Composable fun AdaptivePane( showOnePane: Boolean, /* ... */ ) { if (showOnePane) { OnePane(/* ... */) } else { TwoPane(/* ... */) } }AdaptiveLayoutSnippets.kt

What if you instead want a composable to independently change its layout based on the display space available, for example, a card that shows additional details if space allows? You want to perform some logic based on some available display size, but which size specifically?

Avoid trying to use the size of the device's actual screen. This won't be accurate for different types of screens and also won't be accurate if the app isn't fullscreen.

Because the composable is not a content-level composable, don't use the current window metrics directly.

If the component is placed with padding (such as with insets), or if the app includes components such as navigation rails or app bars, the amount of display space available to the composable may differ significantly from the overall space available to the app.

Use the width that the composable is actually given to render itself. You have two options to get that width:

If you want to change where or how content is displayed, use a collection of modifiers or a custom layout to make the layout responsive. This could be as straightforward as having a child fill all of the available space, or laying out children with multiple columns if there's enough room.

If you want to change what you show, use BoxWithConstraints as a more powerful alternative. BoxWithConstraints provides measurement constraints that you can use to call different composables based on the available display space. However, this comes at some expense, as BoxWithConstraints defers composition until the layout phase, when these constraints are known, causing more work to be performed during layout.

@Composable fun Card(/* ... */) { BoxWithConstraints { if (maxWidth < 400.dp) { Column { Image(/* ... */) Title(/* ... */) } } else { Row { Column { Title(/* ... */) Description(/* ... */) } Image(/* ... */) } } } }AdaptiveLayoutSnippets.kt

When implementing a composable that takes advantage of extra display space, you might be tempted to be efficient and load data as a side effect of the current display size.

However, doing so goes against the principle of unidirectional data flow, where data can be hoisted and provided to composables to render appropriately. Enough data should be provided to the composable so that the composable always has enough content for any display size, even if some portion of the content might not always be used.

@Composable fun Card( imageUrl: String, title: String, description: String ) { BoxWithConstraints { if (maxWidth < 400.dp) { Column { Image(imageUrl) Title(title) } } else { Row { Column { Title(title) Description(description) } Image(imageUrl) } } } }AdaptiveLayoutSnippets.kt

Building on the Card example, note that the description is always passed to the Card. Even though the description is only used when the width permits displaying it, Card always requires the description, regardless of the available width.

Always passing sufficient content makes adaptive layouts simpler by making them less stateful and avoids triggering side effects when switching between display sizes (which may occur due to a window resize, orientation change, or folding and unfolding a device).

This principle also allows preserving state across layout changes. By hoisting information that may not be used at all display sizes, you can preserve app state as the layout size changes.

For example, you can hoist a showMore boolean flag so that the app state is preserved when display resizing causes the layout to switch between hiding and showing content:

@Composable fun Card( imageUrl: String, title: String, description: String ) { var showMore by remember { mutableStateOf(false) } BoxWithConstraints { if (maxWidth < 400.dp) { Column { Image(imageUrl) Title(title) } } else { Row { Column { Title(title) Description( description = description, showMore = showMore, onShowMoreToggled = { newValue -> showMore = newValue } ) } Image(imageUrl) } } } }AdaptiveLayoutSnippets.kt

To learn more about adaptive layouts in Compose, see the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Create a parallax scrolling effect Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/parallax-scrolling

**Contents:**
- Create a parallax scrolling effect Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Create a parallax effect
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display images
  - Display text

Parallax scrolling is a technique in which the background content and foreground content scroll at different speeds. You can implement this technique to enhance your app's UI, creating a more dynamic experience as your users scroll.

Figure 1. A scrolling list with a parallax effect.

This implementation requires that your project minSDK be set to API level 21 or higher.

To achieve the parallax effect, you apply a fraction of the scrolling value from the scrolling composable to the composable that needs the parallax effect. The following snippet takes two nested visual elements—an image and a block of text—and scrolls them in the same direction at different speeds:

@Composable fun ParallaxEffect() { fun Modifier.parallaxLayoutModifier(scrollState: ScrollState, rate: Int) = layout { measurable, constraints -> val placeable = measurable.measure(constraints) val height = if (rate > 0) scrollState.value / rate else scrollState.value layout(placeable.width, placeable.height) { placeable.place(0, height) } } val scrollState = rememberScrollState() Column( modifier = Modifier .fillMaxWidth() .verticalScroll(scrollState), ) { Image( painterResource(id = R.drawable.cupcake), contentDescription = "Android logo", contentScale = ContentScale.Fit, // Reduce scrolling rate by half. modifier = Modifier.parallaxLayoutModifier(scrollState, 2) ) Text( text = stringResource(R.string.detail_placeholder), modifier = Modifier .background(Color.White) .padding(horizontal = 8.dp), ) } }ParallaxEffect.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Support connected displays Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/support-connected-displays

**Contents:**
- Support connected displays Stay organized with collections Save and categorize content based on your preferences.
- Handle dynamic display changes
  - Use the right context
  - Get display information
- Manage activity launch and configuration
- Avoid device allowlists
- Support external peripherals
- Enhance user productivity

Connected displays extend the desktop windowing experience to standard phones, giving users access to large screens from their mobile devices. This capability opens up new possibilities for app interaction and user productivity.

All the unique features of desktop windowing apply to connected displays. When you connect a phone to a display, the phone's state remains unchanged, and a blank desktop session starts on the connected display. The device and the display act as two individual systems, with apps specific to each display.

If you connect a desktop windowing–enabled device, such as a tablet, to an external monitor, the desktop session extends across both displays. The two displays then function as one continuous system. This setup allows windows, content, and the cursor to move freely between the two displays.

Supporting connected displays effectively requires attention to several aspects of your app's design and implementation. The following best practices ensure a smooth and productive user experience.

Many apps are built with the assumption that the Display object and its characteristics won’t change during the app's lifecycle. However, when a user connects or disconnects an external monitor, or even moves an app window between displays, the underlying Display object associated with your app's context or window can change. The display’s properties, such as size, resolution, refresh rate, HDR support, and density, can all be different. If you hardcode values based on the phone's screen, for example, your layouts will likely break on an external display.

External displays can also have vastly different pixel densities. You need to make sure your app responds correctly to density changes. This involves using density-independent pixels (dp) for layouts, providing density-specific resources, and ensuring your UI scales appropriately.

Using the right context is crucial in multi-display environments. When accessing resources, the activity context (which is displayed) is different from the application context (which is not displayed).

The activity context contains information about the display and is always adjusted for the display area in which the activity appears. This enables you to get the correct information about the display density or window metrics of your app. Always use the activity context (or another UI-based context) to get information about the current window or display. This also affects some system APIs that use information from the context.

You can use the Display class to get information about a particular display, such as its size or flags that indicate whether a display is secure. To get the available displays, use the DisplayManager system service:

With connected displays, apps can specify which display an app should run on when it launches or when it creates another activity. This behavior depends on the activity launch mode defined in the manifest file and on the intent flags and options set by the entity launching the activity.

When an activity moves to a secondary display, your app can experience a context update, window resizing, and configuration and resource changes. If the activity handles the configuration change, it is notified in onConfigurationChanged(). Otherwise, the activity is relaunched.

If the selected launch mode for an activity allows multiple instances, launching on a secondary screen can create a new instance of the activity. Both activities are resumed at the same time, which can be beneficial for certain multitasking scenarios.

You can launch an activity on a particular display using ActivityOptions:

Apps sometimes restrict large screen UI and features to select devices through an allowlist or by checking the built-in display size. With connected displays, this approach is no longer effective, as virtually any new device can be connected to a large screen. Design your app to be responsive and adaptable to various screen sizes and densities.

When users connect to an external display, they often create a more desktop-like environment. This frequently involves using external keyboards, mice, trackpads, webcams, microphones, and speakers. You need to ensure your app works seamlessly with these peripherals. This includes handling keyboard shortcuts, managing mouse pointer interactions, correctly supporting external cameras or microphones, and respecting audio output routing. For more details, see Input compatibility on large screens.

Connected displays provide a significant opportunity to improve user productivity. You now have the tools to build mobile apps that can offer experiences comparable to desktop applications. Consider implementing the following features to boost user productivity:

By following these guidelines and utilizing the provided code examples, you can create apps that seamlessly adapt to connected displays, offering users a richer and more productive experience.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-06-10 UTC.

**Examples:**

Example 1 (typescript):
```typescript
val displayManager = getSystemService(Context.DISPLAY_SERVICE) as DisplayManager
val displays = displayManager.getDisplays()
```

Example 2 (unknown):
```unknown
val options = ActivityOptions.makeBasic()
options.setLaunchDisplayId(targetDisplay.displayId)
startActivity(intent, options.toBundle())
```

---

## Slider Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/slider

**Contents:**
- Slider Stay organized with collections Save and categorize content based on your preferences.
- Basic implementation
- Advanced implementation
- Range slider
- Additional resources

The Slider composable allows users to make selections from a range of values. You might use a slider to let the user do the following:

The slider contains a track, thumb, value label, and tick marks:

See the Slider reference for a full API definition. Some of the key parameters for the Slider composable are the following:

The following example is a straightforward slider. That allows the user to select a value from 0.0 to 1.0. Because the user can select any value in that range, the slider is continuous.

@Preview @Composable fun SliderMinimalExample() { var sliderPosition by remember { mutableFloatStateOf(0f) } Column { Slider( value = sliderPosition, onValueChange = { sliderPosition = it } ) Text(text = sliderPosition.toString()) } }Slider.kt

This implementation appears as follows:

When implementing a more complex slider, you can additionally make use of the following parameters.

The following snippet implements a slider that has three steps, with a range from 0.0 to 50.0. Because the thumb snaps to each step, this slider is discrete.

@Preview @Composable fun SliderAdvancedExample() { var sliderPosition by remember { mutableFloatStateOf(0f) } Column { Slider( value = sliderPosition, onValueChange = { sliderPosition = it }, colors = SliderDefaults.colors( thumbColor = MaterialTheme.colorScheme.secondary, activeTrackColor = MaterialTheme.colorScheme.secondary, inactiveTrackColor = MaterialTheme.colorScheme.secondaryContainer, ), steps = 3, valueRange = 0f..50f ) Text(text = sliderPosition.toString()) } }Slider.kt

The implementation appears as follows:

You can also use the dedicated RangeSlider composable. This allows the user to select two values. This can be useful in cases such as when the user wishes to select a minimum and maximum price.

The following example is a relatively straightforward example of a continuous range slider.

@Preview @Composable fun RangeSliderExample() { var sliderPosition by remember { mutableStateOf(0f..100f) } Column { RangeSlider( value = sliderPosition, steps = 5, onValueChange = { range -> sliderPosition = range }, valueRange = 0f..100f, onValueChangeFinished = { // launch some business logic update with the state you hold // viewModel.updateSelectedSliderValue(sliderPosition) }, ) Text(text = sliderPosition.toString()) } }Slider.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Unit testing with Glance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/testing

**Contents:**
- Unit testing with Glance Stay organized with collections Save and categorize content based on your preferences.
- Setup
- Test structure
- Set context and size for the test

The Glance unit test API let you test your Glance code without inflating views or needing a UI automator. For example, the unit test API lets you verify conditions, such as whether elements are in a list or whether boxes have been checked, using matchers such as hasContentDescriptionEqualTo or isChecked.

This API is lightweight and requires less setup, so you can perform test driven development as you develop individual pieces of your widget and organize them to improve code reuse.

The dependencies required to use the unit test library are shown in the following examples:

Organize composable functions outside of the GlanceAppWidget class to enable code reuse and unit testing. Reduce the complexity of your units under test as much as possible.

You can target a test Composable with provideComposable and run your unit tests on one or multiple Glance nodes with onNode or onAllNodes respectively.

private const val FAKE_HEADLINE = "EXTRA! EXTRA! READ ALL ABOUT IT!" class MyGlanceComposableTest { @Test fun myNewsItemComposable_largeSize_hasHeadline() = runGlanceAppWidgetUnitTest { // Set the composable to test provideComposable { MyNewsItemComposable(FAKE_HEADLINE) } // Perform assertions onNode(hasTestTag("headline")) .assertHasText(FAKE_HEADLINE) } @Composable fun MyNewsItemComposable(headline: String) { Row { Text( text = headline, modifier = GlanceModifier.semantics { testTag = "headline" }, ) } } }GlanceUnitTest.kt

If your composable function reads context using the LocalContext.current() method, you must set a context using setContext(). Otherwise, this step is optional.

You can use any JVM-based Android unit testing framework, such as Roboletric, to provide the context.

If your composable function accesses LocalSize, set the intended size for the test before providing a composable in the test. The default size is 349.dp x 455.dp, which is equivalent to a 5x4 widget shown on a Pixel 4 device in portrait mode.

The default duration for a test timeout is 1 second, but you can pass a custom duration as an argument to the runGlanceAppWidgetUnitTest method if your test infrastructure enforces a different timeout.

For more information and code samples, see the reference documentation for runGlanceAppWidgetUnitTest.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (powershell):
```powershell
// Other Glance and Compose runtime dependencies.
...
testImplementation 'androidx.glance:glance-testing:1.1.1'
testImplementation 'androidx.glance:glance-appwidget-testing:1.1.1'
...
// You may include additional dependencies, such as Robolectric, if your test
// needs to set a LocalContext.
```

---

## Use Material 3 insets Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/material-insets

**Contents:**
- Use Material 3 insets Stay organized with collections Save and categorize content based on your preferences.
- Inset handling composables
- App bars
  - Content containers
  - Scaffold
- Override default insets

For ease of use, many of the built-in Material 3 composables (androidx.compose.material3) handle insets themselves, based on how the composables are placed in your app according to the Material specifications.

The following is a list of the Material Components that automatically handle insets.

By default, Scaffold provides insets as parameter PaddingValues for you to consume and use. Scaffold does not apply the insets to content; this responsibility is yours. For example, to consume these insets with a LazyColumn inside a Scaffold:

Scaffold { innerPadding -> // innerPadding contains inset information for you to use and apply LazyColumn( // consume insets as scaffold doesn't do it by default modifier = Modifier.consumeWindowInsets(innerPadding), contentPadding = innerPadding ) { // .. } }InsetsSnippets.kt

The following video shows a LazyColumn within a Scaffold with edge-to-edge display disabled and enabled:

Using the PaddingValues parameter in Scaffold is generally enough to inset your UI away from the system UI and display cutouts. Avoid using additional inset handling approaches like rulers, padding modifiers, or inset size modifiers if using Scaffold to avoid applying too much padding to your UI.

You can change the windowInsets parameter passed to the composable to configure the composable's behavior. This parameter can be a different type of window inset to apply instead, or disabled by passing an empty instance: WindowInsets(0, 0, 0, 0).

For example, to disable the inset handling on LargeTopAppBar, set the windowInsets parameter to an empty instance:

LargeTopAppBar( windowInsets = WindowInsets(0, 0, 0, 0), title = { Text("Hi") } )InsetsSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?keywords=collection

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## App orientation, aspect ratio, and resizability Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/app-orientation-aspect-ratio-resizability

**Contents:**
- App orientation, aspect ratio, and resizability Stay organized with collections Save and categorize content based on your preferences.
- Changes
- Exceptions
- Opt out
- Tests
- Common problems
- Timeline
- Additional resources

Android apps run on devices of all kinds: phones, tablets, foldables, ChromeOS devices, cars, TVs, and even XR. To adapt to this varied environment, your app should support all device form factors and display sizes.

Android 16 (API level 36) enables apps to adapt to different form factors and display sizes by overriding app restrictions for screen orientation, aspect ratio, and resizability. The overrides apply to devices with smallest width >= 600dp which defines the following:

Apps that target API level 36 are resizable and able to enter multi‑window mode (equivalent to resizeableActivity="true") if the display's smallest width is >= 600dp.

Android 16 enforces a consistent model of adaptive app design that optimizes the user experience by respecting user preferences for device orientation, aspect ratio, and display size.

The following manifest attributes and APIs are ignored for apps targeting Android 16 (API level 36) on large screens:

Exceptions to the Android 16 changes include the following:

Displays smaller than sw600dp (most phones, flippables, and outer displays of large screen foldables)

Games, based on the android:appCategory flag

Publish your game using Android App Bundles and Play App Signing, allowing Google Play to manage the flag and provide the benefits of app bundles automatically. See also App manifest overview.

User opt in to app's default behavior in the aspect ratio settings

To opt out of the API level 36 behavior, declare the PROPERTY_COMPAT_ALLOW_RESTRICTED_RESIZABILITY manifest property.

To opt out for a specific activity, set the property in the <activity> element:

To opt out for your entire app, set the property in the <application> element:

To test whether your app is impacted by the Android 16 changes, use the Pixel Tablet and Pixel Fold series emulators in Android Studio and set targetSdkPreview = "Baklava" in your app's module build.gradle file.

Or use the app compatibility framework on your test devices by enabling the UNIVERSAL_RESIZABLE_BY_DEFAULT flag (see Compatibility framework tools).

You can automate testing with the Espresso testing framework and Jetpack Compose testing APIs.

Apps that restrict device orientation, aspect ratio, or app resizability might have display issues on Android 16, such as overlapping layouts.

To provide an optimal user experience on phones, foldables, tablets, ChromeOS devices, car displays, and XR, build your app to be responsive and adaptive:

Avoid stretched UI components: Layouts designed for standard, portrait phone screens will likely fail to accommodate other aspect ratios. For example, UI elements that fill the entire width of the display will appear stretched in landscape orientation. Add a maximum width to components to avoid stretching.

Enable layouts to scroll: If layouts don't scroll, users might not be able to access buttons or other UI elements that are off screen in landscape orientation. Enable app layouts to scroll to verify all content is reachable regardless of the height of the display.

Verify camera compatibility in portrait and landscape: Camera viewfinder previews that assume a specific aspect ratio and orientation relative to the camera sensor can result in stretched or flipped previews on nonconforming displays. Verify that viewfinders rotate properly with orientation changes. Enable viewfinders to adjust to UI aspect ratios that differ from the sensor aspect ratio.

Preserve state during window size changes: The removal of orientation and aspect ratio restrictions can result in frequent app window size changes in response to how users prefer to use an app, for example, by rotating, folding, or unfolding a device or by resizing an app in multi-window or desktop windowing mode. Configuration changes such as orientation changes and window resizing cause activity recreation (by default). To help provide an optimal user experience, preserve app state during configuration changes so your app retains data (such as form input) and users can maintain context.

Use window size classes: Support different window sizes and aspect ratios without device‑specific customizations. Assume window sizes will change frequently. Use window size classes to characterize the window dimensions, and then apply an appropriate adaptive layout.

Build responsive layouts: Within window size classes, responsive layouts adjust to changes in display dimensions to always create an optimal app presentation.

The deadlines for targeting specific API levels are app store specific. Google Play will require apps to target API level 36 as of August 2026.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-10-21 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<activity ...>
    <property
        android:name="android.window.PROPERTY_COMPAT_ALLOW_RESTRICTED_RESIZABILITY"
        android:value="true" />
    ...
</activity>
```

Example 2 (jsx):
```jsx
<application ...>
    <property
        android:name="android.window.PROPERTY_COMPAT_ALLOW_RESTRICTED_RESIZABILITY"
        android:value="true" />
    ...
</application>
```

---

## Animated vector images in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/vectors

**Contents:**
- Animated vector images in Compose Stay organized with collections Save and categorize content based on your preferences.
- Animated vector drawables (experimental)
- Recommended for you

Animating vectors in Compose is possible in a few different ways. You can use any of the following:

To use an AnimatedVectorDrawable resource, load up the drawable file using animatedVectorResource and pass in a boolean to switch between the start and end state of your drawable, performing the animation.

@Composable fun AnimatedVectorDrawable() { val image = AnimatedImageVector.animatedVectorResource(R.drawable.ic_hourglass_animated) var atEnd by remember { mutableStateOf(false) } Image( painter = rememberAnimatedVectorPainter(image, atEnd), contentDescription = "Timer", modifier = Modifier.clickable { atEnd = !atEnd }, contentScale = ContentScale.Crop ) }AnimationSnippets.kt

For more information about the format of your drawable file, see Animate drawable graphics.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=input

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Style guidelines for Jetpack Compose APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/api-guidelines

**Contents:**
- Style guidelines for Jetpack Compose APIs Stay organized with collections Save and categorize content based on your preferences.
- Audience
- Contribute to framework and guidelines

If you're writing Compose code for your app or building Compose libraries and APIs, follow best practices to make your code scalable, more performant, and consistent with the rest of the ecosystem.

The following documents provide guidelines for anyone writing code that uses Compose:

These guidelines are written for the following three audiences:

Depending on the group you're in, you have different strictness levels for each style guideline. For example, Jetpack Compose framework development generally adheres most strictly to these guidelines.

For more information about the requirements for each developer audience, see RFC2119.

We welcome contributions to select libraries in the androidx codebase and the style guidelines (Compose API guidelines and @Composable components guidelines) themselves.

To contribute, follow the instructions in the androidx contribution guide.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Additional samples Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/shared-elements/additional-samples

**Contents:**
- Additional samples Stay organized with collections Save and categorize content based on your preferences.

Basic shared element with caller managed visibility:

Two boxes moving around

SharedElementWithCallerManagedVisibility

Shifts remaining content to the left and grows to full size

Nested shared element

NestedSharedElementDemo

Container Transform and a demo from Lazy List to Details with nesting

ContainerTransformDemo

Complex Example - Jetsnack

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/collections/request-user-input

**Contents:**
- Quick Guides CollectionRequest user input
  - Validate input as a user types
  - Show or hide password based on a user toggle
  - Auto-format a phone number in a text field
  - Display pop-up messages or requests for user input
  - Add a switch that users can toggle
  - Create a slider for a range of values
  - Create a chip to represent complex entities
  - Filter a list while typing
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/collections/compose-basics

**Contents:**
- Quick Guides Collection Compose basics
  - Debugging Compose recomposition
  - Testing in Compose
  - Accessibility in Compose
  - Animation in Compose
  - Lists in Compose
  - State in Compose
- Have questions or feedback

A look into debugging a performance issue in Jetsnack and how to fix it in Jetpack Compose. Learn why deferring state reads by using a lambda means composition can be skipped.

Build your first Jetpack Compose tests. Learn how to use Compose's testing artifacts to write UI tests, use test rules, finders, and assertions.

Add accessibility features to your app. See how to increase your app's reach and versatility with a small amount of work.

See how to animate state values, using transitions, animating visibility or size changes and crossfades by using the Compose animation APIs.

Explore Compose's lazy components, which make it easy to display lists of items. Learn how to show different item types, implement sticky headers, and programmatically control or react to the scroll-position changes.

Learn how state flows through your Compose-based app and how the framework can automatically update UI to display new values. See how to create observable states, how to retain state across recompositions or configuration changes, and how to structure your composables for optimal data flow.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Badges Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/badges

**Contents:**
- Badges Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Basic example
- Detailed example
- Additional resources

Use a badge to display a small visual element to denote status or a numeric value on another composable. Here are a few common scenarios where you might use a badge:

Use the BadgedBox composable to implement badges in your application. It is ultimately a container. You control its appearance with these two main parameters:

This code snippet shows a basic implementation of BadgedBox:

@Composable fun BadgeExample() { BadgedBox( badge = { Badge() } ) { Icon( imageVector = Icons.Filled.Mail, contentDescription = "Email" ) } }Badges.kt

This example displays a badge that overlaps the provided Icon composable. Note the following in the code:

This is how it appears:

The following snippet demonstrates how you can display values in the badge that respond to user actions.

@Composable fun BadgeInteractiveExample() { var itemCount by remember { mutableIntStateOf(0) } Column( verticalArrangement = Arrangement.spacedBy(16.dp) ) { BadgedBox( badge = { if (itemCount > 0) { Badge( containerColor = Color.Red, contentColor = Color.White ) { Text("$itemCount") } } } ) { Icon( imageVector = Icons.Filled.ShoppingCart, contentDescription = "Shopping cart", ) } Button(onClick = { itemCount++ }) { Text("Add item") } } }Badges.kt

This example implements a shopping cart icon with a badge that displays the number of items in the user's cart.

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Progress indicators Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/progress

**Contents:**
- Progress indicators Stay organized with collections Save and categorize content based on your preferences.
- API Surface
- Determinate indicators
- Indeterminate indicators
- Additional resources

Progress indicators visually surface the status of an operation. They use motion to bring to the user's attention how near completion the process is, such as loading or processing data. They can also signify that processing is taking place, without reference to how close to completion it might be.

Consider these three use cases where you might use a progress indicator:

In Material Design, there are two types of progress indicator:

Likewise, a progress indicator can take one of the two following forms:

Although there are several composables you can use to create progress indicators consistent with Material Design, their parameters don't differ greatly. Among the key parameters you should keep in mind are the following:

A determinate indicator reflects exactly how complete an action is. Use either the LinearProgressIndicator or CircularProgressIndicator composables and pass a value for the progress parameter.

The following snippet provides a relatively detailed example. When the user presses the button, the app both displays the progress indicator, and launches a coroutine that gradually increases the value of progress. This causes the progress indicator to iterate up in turn.

@Composable fun LinearDeterminateIndicator() { var currentProgress by remember { mutableFloatStateOf(0f) } var loading by remember { mutableStateOf(false) } val scope = rememberCoroutineScope() // Create a coroutine scope Column( verticalArrangement = Arrangement.spacedBy(12.dp), horizontalAlignment = Alignment.CenterHorizontally, modifier = Modifier.fillMaxWidth() ) { Button(onClick = { loading = true scope.launch { loadProgress { progress -> currentProgress = progress } loading = false // Reset loading when the coroutine finishes } }, enabled = !loading) { Text("Start loading") } if (loading) { LinearProgressIndicator( progress = { currentProgress }, modifier = Modifier.fillMaxWidth(), ) } } } /** Iterate the progress value */ suspend fun loadProgress(updateProgress: (Float) -> Unit) { for (i in 1..100) { updateProgress(i.toFloat() / 100) delay(100) } }ProgressIndicator.kt

When loading is partially complete, the linear indicator in the preceding example appears as follows:

Likewise, the circular indicator appears as follows:

An indeterminate indicator does not reflect how close to completion an operation is. Rather, it uses motion to indicate to the user that processing is ongoing, though without specifying any degree of completion.

To create an indeterminate progress indicator, use the LinearProgressIndicator or CircularProgressIndicator composable, but don't pass in a value for progress. The following example demonstrates how you can toggle an indeterminate indicator with a button press.

@Composable fun IndeterminateCircularIndicator() { var loading by remember { mutableStateOf(false) } Button(onClick = { loading = true }, enabled = !loading) { Text("Start loading") } if (!loading) return CircularProgressIndicator( modifier = Modifier.width(64.dp), color = MaterialTheme.colorScheme.secondary, trackColor = MaterialTheme.colorScheme.surfaceVariant, ) }ProgressIndicator.kt

The following is an example of this implementation when the indicator is active:

The following is an example of the same implementation but with LinearProgressIndicator instead of CircularProgressIndicator.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Handle user interaction Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/user-interaction

**Contents:**
- Handle user interaction Stay organized with collections Save and categorize content based on your preferences.
- Launch an activity
- Launch a service
- Send a broadcast event
- Perform custom actions
  - Run lambda actions
  - Run ActionCallback
- Provide parameters to actions

Glance simplifies handling user interaction via the Action classes. Glance's Action classes define the actions a user can take, and you can specify the operation performed in response to the action. You can apply an Action to any component with the GlanceModifier.clickable method.

App widgets live on a remote process, so the actions are defined at creation time and the execution happens in the remote process. In native RemoteViews, this is done via PendingIntents.

The following actions are described on this page:

To launch an activity on user interaction, provide the actionStartActivity function to a Button or other composable via the GlanceModifier.clickable(..) modifier.

Provide one of the following in actionStartActivity:

Glance translates the Action into a PendingIntent with the provided target and parameters. In the following example, the NavigationActivity is launched when a user clicks the button:

@Composable fun MyContent() { // .. Button( text = "Go Home", onClick = actionStartActivity<MyActivity>() ) }GlanceSnippets.kt

Similar to launching an activity, launch a service on user interaction using one of the actionStartService methods.

Provide one of the following in actionStartService:

@Composable fun MyButton() { // .. Button( text = "Sync", onClick = actionStartService<SyncService>( isForegroundService = true // define how the service is launched ) ) }GlanceSnippets.kt

Send a broadcast event on user interaction using one of the actionSendBroadcast methods:

Provide one of the following in actionSendBroadcast:

@Composable fun MyButton() { // .. Button( text = "Send", onClick = actionSendBroadcast<MyReceiver>() ) }GlanceSnippets.kt

Instead of launching a specific target, Glance can use a lambda action or an actionRunCallback to perform an action, such as updating the UI or state on user interaction.

You can use lambda functions as callbacks to the UI interactions.

For example, pass the lambda function to the GlanceModifier.clickable modifier:

Text( text = "Submit", modifier = GlanceModifier.clickable { submitData() } )GlanceSnippets.kt

Or, pass it to the onClick parameter on composables that support it:

Button( text = "Submit", onClick = { submitData() } )GlanceSnippets.kt

Alternatively, use the actionRunCallback methods to perform an action on user interaction. To do this, provide a custom implementation of the ActionCallback:

@Composable private fun MyContent() { // .. Image( provider = ImageProvider(R.drawable.ic_hourglass_animated), modifier = GlanceModifier.clickable( onClick = actionRunCallback<RefreshAction>() ), contentDescription = "Refresh" ) } class RefreshAction : ActionCallback { override suspend fun onAction( context: Context, glanceId: GlanceId, parameters: ActionParameters ) { // TODO implement } }GlanceSnippets.kt

On the user click, the suspend onAction method of the provided ActionCallback is called, executing the defined logic (i.e., requesting refresh data).

To update the widget after the action is performed, create a new instance and call update(..). For more details, see the Manage GlanceAppWidget state section.

class RefreshAction : ActionCallback { override suspend fun onAction( context: Context, glanceId: GlanceId, parameters: ActionParameters ) { // do some work but offset long-term tasks (e.g a Worker) MyAppWidget().update(context, glanceId) } } GlanceSnippets.kt

To provide additional information to an action, use the ActionParameters API to create a typed key-value pair. For example, to define the clicked destination:

private val destinationKey = ActionParameters.Key<String>( NavigationActivity.KEY_DESTINATION ) class MyAppWidget : GlanceAppWidget() { // .. @Composable private fun MyContent() { // .. Button( text = "Home", onClick = actionStartActivity<NavigationActivity>( actionParametersOf(destinationKey to "home") ) ) Button( text = "Work", onClick = actionStartActivity<NavigationActivity>( actionParametersOf(destinationKey to "work") ) ) } override suspend fun provideGlance(context: Context, id: GlanceId) { provideContent { MyContent() } } }GlanceSnippets.kt

Underneath, the parameters are included in the intent used to launch the activity, allowing the target Activity to retrieve it.

override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) val destination = intent.extras?.getString(KEY_DESTINATION) ?: return // ... } }GlanceSnippets.kt

The parameters are also provided to the ActionCallback. Use the defined Parameters.Key to retrieve the value:

class RefreshAction : ActionCallback { private val destinationKey = ActionParameters.Key<String>( NavigationActivity.KEY_DESTINATION ) override suspend fun onAction( context: Context, glanceId: GlanceId, parameters: ActionParameters ) { val destination: String = parameters[destinationKey] ?: return // ... } }GlanceSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=grid

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Set up Edge-to-edge Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/setup-e2e

**Contents:**
- Set up Edge-to-edge Stay organized with collections Save and categorize content based on your preferences.

To allow your app full control over where it draws content, follow these setup steps. Without these steps, your app may draw black or solid colors behind the system UI, or not animate synchronously with the software keyboard.

Set android:windowSoftInputMode="adjustResize" in your Activity's AndroidManifest.xml entry. This setting allows your app to receive the size of the software IME as insets, which helps you apply the appropriate layout and padding when the IME appears and disappears in your app.

Handle insets so your critical UI doesn't overlap with the system bars or display cutout. You can handle insets using either rulers, padding modifiers or inset size modifiers. Some Material Components automatically handle insets or have parameters to facilitate handling insets like Scaffold's PaddingValues parameter. Choose one inset handling approach. For example, use either Scaffold, Modifier.safeDrawingPadding(), or Modifier.fitInside(WindowInsetsRulers.SafeDrawing.current) as these approaches are often interchangeable.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<!-- In your AndroidManifest.xml file: -->
<activity
  android:name=".ui.MainActivity"
  android:label="@string/app_name"
  android:windowSoftInputMode="adjustResize"
  android:theme="@style/Theme.MyApplication"
  android:exported="true">
```

---

## Test the predictive back gesture animation Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/predictive-back-test

**Contents:**
- Test the predictive back gesture animation Stay organized with collections Save and categorize content based on your preferences.
- Additional resources

If you still use Android 13 or Android 14, you can test the back-to-home animation.

To test this animation, follow these steps:

On Android 15 and later, this feature is enabled by default.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Use insets in Views and Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/insets-views-compose

**Contents:**
- Use insets in Views and Compose Stay organized with collections Save and categorize content based on your preferences.
- Overriding default insets
- Backward compatible inset dispatching for views
- System bar icons
  - Kotlin
  - Java

If your app contains both Compose and View code, you may need to be explicit about which system insets each one should consume and ensure that insets are dispatched to sibling views.

You may need to override default insets when your screen has both Views and Compose code in the same hierarchy. In this case, you need to be explicit in which one should consume the insets, and which one should ignore them.

For example, if your outermost layout is an Android View layout, you should consume the insets in the View system and ignore them for Compose. Alternatively, if your outermost layout is a composable, you should consume the insets in Compose, and pad the AndroidView composables accordingly.

By default, each ComposeView consumes all insets at the WindowInsetsCompat level of consumption. To change this default behavior, set AbstractComposeView.consumeWindowInsets to false.

If your app contains Views code, you may need to confirm that insets are dispatched to sibling views on devices that run Android 10 (API level 29) or lower. See the edge-to-edge Views guide for more information.

Calling enableEdgeToEdge ensures system bar icon colors update when the device theme changes.

While going edge-to-edge, you might need to manually update the system bar icon colors so they contrast with your app's background. For example, to create light status bar icons:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Add PiP through a button Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/pip-add

**Contents:**
- Add PiP through a button Stay organized with collections Save and categorize content based on your preferences.

To enter PiP mode through a button click, call enterPictureInPictureMode() on findActivity().

The parameters are already set by previous calls to the PictureInPictureParams.Builder, so you do not need to set new parameters on the builder. However, if you do want to change any parameters on button click, you can set them here.

val context = LocalContext.current Button(onClick = { if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { context.findActivity().enterPictureInPictureMode( PictureInPictureParams.Builder().build() ) } else { Log.i(PIP_TAG, "API does not support PiP") } }) { Text(text = "Enter PiP mode!") }PictureInPictureSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Test animations Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/testing

**Contents:**
- Test animations Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Compose offers ComposeTestRule that allows you to write tests for animations in a deterministic manner with full control over the test clock. This allows you to verify intermediate animation values. In addition, a test can run quicker than the actual duration of the animation.

ComposeTestRule exposes its test clock as mainClock. You can set the autoAdvance property to false to control the clock in your test code. After initiating the animation you want to test, the clock can be moved forward with advanceTimeBy.

One thing to note here is that advanceTimeBy doesn't move the clock exactly by the specified duration. Rather, it rounds it up to the nearest duration that is a multiplier of the frame duration.

@get:Rule val rule = createComposeRule() @Test fun testAnimationWithClock() { // Pause animations rule.mainClock.autoAdvance = false var enabled by mutableStateOf(false) rule.setContent { val color by animateColorAsState( targetValue = if (enabled) Color.Red else Color.Green, animationSpec = tween(durationMillis = 250) ) Box(Modifier.size(64.dp).background(color)) } // Initiate the animation. enabled = true // Let the animation proceed. rule.mainClock.advanceTimeBy(50L) // Compare the result with the image showing the expected result. // `assertAgainGolden` needs to be implemented in your code. rule.onRoot().captureToImage().assertAgainstGolden() }AnimationTestingSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Migrate RecyclerView to Lazy list Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/migration-scenarios/recycler-view

**Contents:**
- Migrate RecyclerView to Lazy list Stay organized with collections Save and categorize content based on your preferences.
- Migration steps
- Common use cases
  - Item decorations
  - Item animations
- Additional resources
- Recommended for you

RecyclerView is a View component that makes it easy to efficiently display large sets of data. Instead of creating views for each item in the data set, RecyclerView improves the performance of your app by keeping a small pool of views and recycling through them as you scroll through those items.

In Compose, you can use Lazy lists to accomplish the same thing. This page describes how you can migrate your RecyclerView implementation to use Lazy lists in Compose.

To migrate your RecyclerView implementation to Compose, follow these steps:

Comment out or remove the RecyclerView from your UI hierarchy and add a ComposeView to replace it if none is present in the hierarchy yet. This is the container for the Lazy list that you'll add:

Determine what type of Lazy list composable you need based on your RecyclerView’s layout manager (see table below). The composable you select will be the top-level composable of the ComposeView you added in the previous step.

LazyColumn or LazyRow

LazyVerticalGrid or LazyHorizontalGrid

StaggeredGridLayoutManager

LazyVerticalStaggeredGrid or LazyHorizontalStaggeredGrid

// recyclerView.layoutManager = LinearLayoutManager(context) composeView.setContent { LazyColumn(Modifier.fillMaxSize()) { // We use a LazyColumn since the layout manager of the RecyclerView is a vertical LinearLayoutManager } }MigrationCommonScenariosSnippets.kt

Create a corresponding composable for each view type in your RecyclerView.Adapter implementation. Each view type typically maps to a ViewHolder subclass, though this may not always be the case. These composables will be used as the UI representation for different types of elements in your list:

@Composable fun ListItem(data: MyData, modifier: Modifier = Modifier) { Row(modifier.fillMaxWidth()) { Text(text = data.name) // … other composables required for displaying `data` } }MigrationCommonScenariosSnippets.kt

The logic in your RecyclerView.Adapter’s onCreateViewHolder() and onBindViewHolder() methods will be replaced by these composables and the state that you provide them with. In Compose, there is no separation between creating a composable for an item and binding data into it—these concepts are coalesced.

Within the content slot of the Lazy list (the trailing lambda parameter), use the items() function (or an equivalent overload) to iterate through the data for your list. In the itemContent lambda, invoke the appropriate composable item for your data:

val data = listOf<MyData>(/* ... */) composeView.setContent { LazyColumn(Modifier.fillMaxSize()) { items(data) { ListItem(it) } } }MigrationCommonScenariosSnippets.kt

RecyclerView has the concept of an ItemDecoration, which you can use to add a special drawing for items in the list. For example, you can add an ItemDecoration to add dividers between items:

val itemDecoration = DividerItemDecoration(recyclerView.context, LinearLayoutManager.VERTICAL) recyclerView.addItemDecoration(itemDecoration)MigrationCommonScenariosSnippets.kt

Compose does not have an equivalent concept of item decorations. Instead, you can add any UI decorations in the list directly in the composition. For example, to add dividers to the list, you can use the Divider composable after each item:

LazyColumn(Modifier.fillMaxSize()) { itemsIndexed(data) { index, d -> ListItem(d) if (index != data.size - 1) { HorizontalDivider() } } }MigrationCommonScenariosSnippets.kt

An ItemAnimator can be set on a RecyclerView to animate the appearance of items as changes are made to the adapter. By default, RecyclerView uses DefaultItemAnimator which provides basic animations on remove, add, and move events.

Lazy lists have a similar concept through the animateItemPlacement modifier. See Item animations to learn more.

For more information about migrating a RecyclerView to Compose, see the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<FrameLayout
          android:layout_width="match_parent"
          android:layout_height="match_parent">

  <!--    <androidx.recyclerview.widget.RecyclerView-->
  <!--            android:id="@+id/recycler_view"-->
  <!--            android:layout_width="match_parent"-->
  <!--            android:layout_height="match_parent />"-->

          <androidx.compose.ui.platform.ComposeView
              android:id="@+id/compose_view"
              android:layout_width="match_parent"
              android:layout_height="match_parent" />

      </FrameLayout>
```

---

## Restrict app orientation on phones and foldable outer screens but not on larger displays Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/restrict-app-orientation-on-phones

**Contents:**
- Restrict app orientation on phones and foldable outer screens but not on larger displays Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
  - Kotlin
  - Groovy
- Manage app orientation
  - 1. Specify orientation setting in the app manifest
  - 2. Determine screen size
  - Kotlin

Your app works great on phones in portrait orientation, so you've restricted the app to portrait only. But you see an opportunity to do more on large screens in landscape orientation or unfolded foldables.

How can you have it both ways—restrict the app to portrait orientation on a foldable's outer screen, but enable landscape on the inner screen?

This guide is a temporary measure until you can improve your app to provide full support for all device configurations.

Your app remains in portrait orientation on small screens regardless of device rotation. On large screens, the app supports landscape and portrait orientations.

This implementation is compatible with all API levels.

To enable landscape orientation on large screens, set your app manifest to handle orientation changes by default. At runtime, determine the app window size. If the app window is small, restrict the app's orientation by overriding the manifest orientation setting.

You can either avoid declaring the screenOrientation element of the app manifest (in which case orientation defaults to unspecified) or set screen orientation to fullUser. If the user has not locked sensor-based rotation, your app will support all device orientations.

The difference between unspecified and fullUser is subtle but important. If you don't declare a screenOrientation value, the system chooses the orientation, and the policy the system uses to define the orientation might differ from device to device.

On the other hand, specifying fullUser matches more closely the behavior the user defined for the device: if the user has locked sensor-based rotation, the app follows the user preference; otherwise, the system allows any of the four possible screen orientations (portrait, landscape, reverse portrait, or reverse landscape).

Additionally, you can use nosensor to determine the orientation without taking into account the sensor data, but the following code will work in the same way. See screenOrientation.

With the manifest set to support all user‑permitted orientations, you can specify app orientation programmatically based on screen size.

Add the Jetpack WindowManager libraries to the module's build.gradle or build.gradle.kts file:

Use the Jetpack WindowManager WindowMetricsCalculator#computeMaximumWindowMetrics() method to obtain the device screen size as a WindowMetrics object. The window metrics can be compared to window size classes to decide when to restrict orientation.

Windows size classes provide the breakpoints between small and large screens.

Use the WindowSizeClass#minWidthDp and WindowSizeClass#minHeightDp breakpoints to determine the screen size:

See WindowManager for instructions about declaring dependencies to make the computeMaximumWindowMetrics() method available in your app.

When you've determined that the device has compact screen size, you can call Activity#setRequestedOrientation() to override the manifest's screenOrientation setting:

By adding the logic to the onCreate() and View.onConfigurationChanged() methods, you're able to obtain the maximum window metrics and override the orientation setting whenever the activity is resized or moved between displays, such as after a device rotation or when a foldable device is folded or unfolded. For more information about when configuration changes occur and when they cause activity recreation, refer to Handle configuration changes.

If you are using Jetpack Compose, you can use the same compactScreen() function in your app's root Composable to achieve the same result.

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-17 UTC.

**Examples:**

Example 1 (json):
```json
implementation("androidx.window:window:1.5.1")
implementation("androidx.window:window-core:1.5.1")
```

Example 2 (json):
```json
implementation "androidx.window:window:1.5.1"
implementation "androidx.window:window-core:1.5.1"
```

Example 3 (jsx):
```jsx
<activity
    android:name=".MyActivity"
    android:screenOrientation="fullUser">
```

Example 4 (unknown):
```unknown
/** Determines whether the device has a compact screen. **/
fun compactScreen() : Boolean {
    val metrics = WindowMetricsCalculator.getOrCreate().computeMaximumWindowMetrics(this)
    val width = metrics.bounds.width()
    val height = metrics.bounds.height()
    val density = resources.displayMetrics.density
    val windowSizeClass =
        BREAKPOINTS_V1.computeWindowSizeClass(width / density, height / density)
    return windowSizeClass.minWidthDp == 0
}
```

---

## About picture-in-picture (PiP) Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/picture-in-picture

**Contents:**
- About picture-in-picture (PiP) Stay organized with collections Save and categorize content based on your preferences.
- Handle your UI in PiP mode

Picture-in-picture (PiP) is a special type of multi-window mode mostly used for video playback. It lets the user watch a video in a small window pinned to a corner of the screen while navigating between apps or browsing content on the main screen.

PiP leverages the multi-window APIs made available in Android 7.0 to provide the pinned video overlay window. To add PiP to your app, you need to register your activity, switch your activity to PiP mode as needed, and make sure UI elements are hidden and video playback continues when the activity is in PiP mode.

When you enter PiP mode, your app's entire UI enters the PiP window unless you specify how your UI should look in and out of PiP mode.

First, you need to know when your app is in PiP mode or not. You can use OnPictureInPictureModeChangedProvider to achieve this. The code below tells you if your app is in PiP mode.

@Composable fun rememberIsInPipMode(): Boolean { if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { val activity = LocalContext.current.findActivity() var pipMode by remember { mutableStateOf(activity.isInPictureInPictureMode) } DisposableEffect(activity) { val observer = Consumer<PictureInPictureModeChangedInfo> { info -> pipMode = info.isInPictureInPictureMode } activity.addOnPictureInPictureModeChangedListener( observer ) onDispose { activity.removeOnPictureInPictureModeChangedListener(observer) } } return pipMode } else { return false } }PictureInPictureSnippets.kt

Now, you can use rememberIsInPipMode() to toggle which UI elements to show when the app enters PiP mode:

val inPipMode = rememberIsInPipMode() Column(modifier = modifier) { // This text will only show up when the app is not in PiP mode if (!inPipMode) { Text( text = "Picture in Picture", ) } VideoPlayer() }PictureInPictureSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/adopt/for-large-teams

**Contents:**
  - Technical decision makers: Adopt Compose for your teams
- How to get started
  - Start with Kotlin
  - Migrate to a declarative approach
  - Start learning as a team
  - Design to high standards
  - Three approaches to integrating Compose
  - Write new features in Compose.
  - Replace simple screens.
  - Redesign your UI.

Jetpack Compose is a declarative UI toolkit that accelerates Android app development, helps developers be more productive, eliminates common bugs, and enables intuitive app design.

Android is invested in the continued development of Jetpack Compose as many developers love it, including Googlers. Several Google teams are using Jetpack Compose, including the Play Store.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-01-14 UTC.

---

## Search bar Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/search-bar

**Contents:**
- Search bar Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Search bar with suggestions
  - Key points about the code
  - Result
- Search bar with filtered list
  - Key points about the code
  - Result
- Additional resources

Use a search bar to implement search functionality. A search bar is a persistent search field that lets users enter a keyword or phrase to display relevant results within your app, and is recommended when search is the primary focus of your app.

Use the SearchBar composable to implement search bars. Key parameters for this composable include the following:

onExpandedChange: Lambda to handle changes in the dropdown's expanded state.

content: The content of this search bar to display search results below the inputField.

This snippet shows a basic implementation of SearchBar with suggestions:

@OptIn(ExperimentalMaterial3Api::class) @Composable fun SimpleSearchBar( textFieldState: TextFieldState, onSearch: (String) -> Unit, searchResults: List<String>, modifier: Modifier = Modifier ) { // Controls expansion state of the search bar var expanded by rememberSaveable { mutableStateOf(false) } Box( modifier .fillMaxSize() .semantics { isTraversalGroup = true } ) { SearchBar( modifier = Modifier .align(Alignment.TopCenter) .semantics { traversalIndex = 0f }, inputField = { SearchBarDefaults.InputField( query = textFieldState.text.toString(), onQueryChange = { textFieldState.edit { replace(0, length, it) } }, onSearch = { onSearch(textFieldState.text.toString()) expanded = false }, expanded = expanded, onExpandedChange = { expanded = it }, placeholder = { Text("Search") } ) }, expanded = expanded, onExpandedChange = { expanded = it }, ) { // Display search results in a scrollable column Column(Modifier.verticalScroll(rememberScrollState())) { searchResults.forEach { result -> ListItem( headlineContent = { Text(result) }, modifier = Modifier .clickable { textFieldState.edit { replace(0, length, result) } expanded = false } .fillMaxWidth() ) } } } } }SearchBar.kt

This example shows a SearchBar that filters a list based on the user's search query:

@OptIn(ExperimentalMaterial3Api::class) @Composable fun CustomizableSearchBar( query: String, onQueryChange: (String) -> Unit, onSearch: (String) -> Unit, searchResults: List<String>, onResultClick: (String) -> Unit, modifier: Modifier = Modifier, // Customization options placeholder: @Composable () -> Unit = { Text("Search") }, leadingIcon: @Composable (() -> Unit)? = { Icon(Icons.Default.Search, contentDescription = "Search") }, trailingIcon: @Composable (() -> Unit)? = null, supportingContent: (@Composable (String) -> Unit)? = null, leadingContent: (@Composable () -> Unit)? = null, ) { // Track expanded state of search bar var expanded by rememberSaveable { mutableStateOf(false) } Box( modifier .fillMaxSize() .semantics { isTraversalGroup = true } ) { SearchBar( modifier = Modifier .align(Alignment.TopCenter) .semantics { traversalIndex = 0f }, inputField = { // Customizable input field implementation SearchBarDefaults.InputField( query = query, onQueryChange = onQueryChange, onSearch = { onSearch(query) expanded = false }, expanded = expanded, onExpandedChange = { expanded = it }, placeholder = placeholder, leadingIcon = leadingIcon, trailingIcon = trailingIcon ) }, expanded = expanded, onExpandedChange = { expanded = it }, ) { // Show search results in a lazy column for better performance LazyColumn { items(count = searchResults.size) { index -> val resultText = searchResults[index] ListItem( headlineContent = { Text(resultText) }, supportingContent = supportingContent?.let { { it(resultText) } }, leadingContent = leadingContent, colors = ListItemDefaults.colors(containerColor = Color.Transparent), modifier = Modifier .clickable { onResultClick(resultText) expanded = false } .fillMaxWidth() .padding(horizontal = 16.dp, vertical = 4.dp) ) } } } } }SearchBar.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Support trifolds and landscape foldables Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/foldables/trifolds-and-landscape-foldables

**Contents:**
- Support trifolds and landscape foldables Stay organized with collections Save and categorize content based on your preferences.
- Build adaptive
  - Adaptive layouts
  - Window size classes
  - Large screen app quality
  - Android 16 and higher
- Special considerations
  - Camera preview
    - Mismatched assumptions
    - Solution 1: Jetpack CameraX (Best)

Developers often encounter unique difficulties when creating applications for foldables—especially devices like the Samsung Trifold or the original Pixel Fold, which opens in landscape format (rotation_0 = landscape). Developer mistakes include:

Specific device-related issues include:

To deliver a high-quality user experience on foldable devices, focus on the following critical areas:

If your app is already adaptive and adheres to the optimized level (Tier 2) outlined in the Large screen app quality guidelines, the app should function well on foldable devices. Otherwise, before double-checking the specific details of trifold and landscape foldables, review the following foundational Android adaptive development concepts.

Your UI must handle not just different screen sizes, but real-time changes in aspect ratio, such as unfolding and entering multi-window or desktop windowing modes. See About adaptive layouts for further guidance on how to:

Foldable devices, including landscape foldables and trifolds, can shift between compact, medium, and expanded window size classes instantly. Understanding and implementing these classes ensures your app displays the correct navigation components and content density for the current device state.

The following example uses the Material 3 adaptive library to determine how much space the app has available by first invoking the currentWindowAdaptiveInfo() function, then using the corresponding layouts for the three window size classes:

For more information, see Use window size classes.

Adhering to Tier 2 (Large screen optimized) or Tier 1 (Large screen differentiated) of the Large screen app quality guidelines ensures your app provides a compelling user experience on trifold devices, landscape foldables, and other large-screen devices. The guidelines cover critical checks across multiple tier levels to go from adaptive ready to a differentiated experience.

For apps targeting Android 16 (API level 36) and higher, the system ignores orientation, resizability, and aspect ratio restrictions on displays with smallest width >= 600dp. Apps fill the entire display window, regardless of aspect ratio or a user's preferred orientation, and the letterboxing compatibility mode isn't used anymore.

Trifolds and landscape foldables introduce unique hardware behaviors that require specific handling, particularly regarding sensors, camera preview, and configuration continuity (retaining state when folding, unfolding, or resizing).

A common problem on landscape foldables or aspect ratio calculations (in scenarios like multi-window, desktop windowing, or connected displays), is when the camera preview appears stretched, sideways, cropped, or rotated.

This issue often happens on large screen and foldable devices because apps can assume fixed relationships between camera features—like aspect ratio and sensor orientation—and device features—like device orientation and natural orientation.

New form factors challenge this assumption. A foldable device can change its display size and aspect ratio without device rotation changing. For example, unfolding a device changes the aspect ratio, but if the user doesn't rotate the device, its rotation stays the same. If an app assumes that aspect ratio correlates to device rotation, it may incorrectly rotate or scale the camera preview. The same can happen if an app assumes camera sensor orientation matches a portrait device orientation, which isn't always true for landscape foldables.

The simplest and most robust solution is to use the Jetpack CameraX library. Its PreviewView UI element is designed to handle all preview complexities automatically:

For more information, see Implement a preview in the CameraX documentation.

If you are using an existing Camera2 codebase, the CameraViewfinder library (backward compatible to API level 21) is another modern solution. It simplifies displaying the camera feed by using a TextureView or SurfaceView and applying all the necessary transformations (aspect ratio, scale, and rotation) for you.

For more information, see the Introducing Camera Viewfinder blog post and Camera preview developer guide.

If you can't use CameraX or CameraViewfinder, you must manually calculate the orientation and aspect ratio and ensure the calculations are updated on each configuration change:

For more information, see the Camera preview developer guide and Your Camera app on different form factors video.

If you don't need many camera features, a simple and straightforward solution is to perform basic camera actions like capturing a photo or video using the device's default camera application. You don't need to integrate with a camera library; instead, use an Intent.

For more information, see Camera intents.

Foldable devices enhance UI versatility but can initiate more configuration changes than nonfoldable. Your app must manage these configuration changes and their combinations, such as device rotation, folding/unfolding, and window resizing in multi-window or desktop modes, while retaining or restoring app state. For example, apps must maintain the following continuity:

The configuration changes that are frequently triggered include screenSize, smallestScreenSize, screenLayout, orientation, density, fontScale, touchscreen, and keyboard.

See android:configChanges and Handle configuration changes. For additional information about managing app state, see Save UI states.

The outer and inner screens of trifolds and landscape foldable devices might feature different pixel densities. Therefore, managing the configuration change for density requires extra attention. Android typically restarts the activity when display density changes, which can cause data loss. To prevent the system from restarting the activity, declare density handling in your manifest and manage the configuration change programmatically in your app.

Declaring density (and other config changes) prevents the system from restarting the activity and instead calls onConfigurationChanged().

When a density change occurs, you must update your resources (like reloading bitmaps or recalculating layout sizes) in the callback:

Never rely on the physical device rotation when building adaptive, because it will be ignored on large screen devices and an app in multi-window mode could have a different orientation than the device. Instead, use Configuration.orientation or WindowMetrics to identify if your app is currently in landscape or portrait orientation based on the window size.

This property identifies the orientation in which your app is currently displayed.

You can get the app's current display bounds and check its width and height to determine orientation.

If you need to limit app orientation on phones (or the outer screens of foldables) but not on large screen devices, see Restrict app orientation on phones.

Foldable postures and states such as tabletop and HALF_OPENED are supported by both portrait foldables and landscape foldables. Trifolds, however, do not support tabletop posture and cannot be used HALF_OPENED. Trifolds instead offer a larger screen for a unique user experience when fully unfolded.

To differentiate your app on foldables that support HALF_OPENED, use Jetpack WindowManager APIs such as FoldingFeature.

Learn more about foldable postures, states, and support for camera preview in the following developer guides:

Foldables offer unique viewing experiences. Rear display mode and dual‑screen mode enable you to build special display features for foldable devices such as rear‑camera selfie preview and simultaneous but different displays on inner and outer screens. For more information see:

For very specific use cases—in particular, apps that need to take over the whole screen unrelated to the folded state of the device—the nosensor flag allows you to lock the app to the natural orientation of the device. For example, on a Pixel Fold, the natural orientation of the device when folded is portrait, while the natural orientation when unfolded is landscape. Adding the nosensor flag forces the app to be locked in portrait when running on the outer display and locked to landscape when running on the inner display.

For games and XR apps, raw sensor data (like gyroscope or accelerometer) is provided in the device-fixed coordinate system. If the user rotates the device to play a game in landscape, the sensor axes do not rotate with the screen, leading to incorrect game controls.

To fix this issue, check the current Display.getRotation() and remap the axes accordingly:

For rotation vectors (used in compass or XR apps), use SensorManager.remapCoordinateSystem() to map the camera lens direction or top of the screen to the new axes based on the current rotation.

Applications must follow the app quality guidelines to guarantee compatibility across all form factors and connected displays. If an application cannot comply with the guidelines, device manufacturers can implement compatibility treatments, although this may degrade the user experience.

For additional information, review the comprehensive list of compatibility workarounds provided in the platform, specifically those related to camera preview, overrides, and Android 16 API changes that could change your app behavior.

To learn more about building adaptive apps, see Large screen app quality.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (php):
```php
val adaptiveInfo = currentWindowAdaptiveInfo()
val windowSizeClass = adaptiveInfo.windowSizeClass

when {
  windowSizeClass.isWidthAtLeastBreakpoint(WIDTH_DP_EXPANDED_LOWER_BOUND) -> // Expanded
  windowSizeClass.isWidthAtLeastBreakpoint(WIDTH_DP_MEDIUM_LOWER_BOUND) -> // Medium
  else -> // Compact
}
```

Example 2 (jsx):
```jsx
<activity
  android:name=".MainActivity"
  android:screenOrientation="nosensor">
```

---

## About WindowInsetsRulers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/evaluate-rulers

**Contents:**
- About WindowInsetsRulers Stay organized with collections Save and categorize content based on your preferences.
- Advantages of WindowInsetsRulers
- Disadvantages of WindowInsetsRulers
- Align your content with Modifier methods
  - Avoid the IME with Modifier.fitInside
  - Avoid the status bar and caption bar with Modifier.fitInside
- Create custom WindowInsetsRulers
  - Verify that parents are constrained

WindowInsets is the standard API in Jetpack Compose for handling areas of the screen that are partially or fully obscured by the system UI. These areas include the status bar, navigation bar, and on-screen keyboard. You can alternatively pass predefined WindowInsetsRulers like SafeDrawing to Modifier.fitInside or Modifier.fitOutside to align your content with the system bars and the display cutout or create custom WindowInsetsRulers.

Modifier.fitInside allows apps to align content to system bars and display cutouts. It can be used instead of WindowInsets. Modifier.fitOutside is usually the inverse of Modifier.fitInside.

For example, to verify that app content avoids the system bars and display cutout, you can use fitInside(WindowInsetsRulers.safeDrawing.current).

@Composable fun FitInsideDemo(modifier: Modifier) { Box( modifier = modifier .fillMaxSize() // Or DisplayCutout, Ime, NavigationBars, StatusBar, etc... .fitInside(WindowInsetsRulers.SafeDrawing.current) ) }InsetsSnippets.kt

The following table shows what your app content would look like with predefined rulers with either Modifier.fitInside or Modifier.fitOutside.

N/A (use StatusBar, CaptionBar, NavigationBar instead)

Using Modifier.fitInside and Modifier.fitOutside requires that the composables are constrained. This means you must define modifiers like Modifier.size or Modifier.fillMaxSize.

Some rulers like Modifier.fitOutside on SafeDrawing and SystemBars return multiple rulers. In this case, Android places the Composable using one ruler from left, top, right, bottom.

To handle bottom elements with an IME with Modifier.fitInside, pass in a RectRuler that takes the innermost value of NavigationBar and Ime.

@Composable fun FitInsideWithImeDemo(modifier: Modifier) { Box( modifier = modifier .fillMaxSize() .fitInside( RectRulers.innermostOf( WindowInsetsRulers.NavigationBars.current, WindowInsetsRulers.Ime.current ) ) ) { TextField( value = "Demo IME Insets", onValueChange = {}, modifier = modifier.align(Alignment.BottomStart).fillMaxWidth() ) } }InsetsSnippets.kt

Similarly, to verify top elements avoid the status bar and caption bar together with Modifier.fitInsider, pass a RectRuler that takes the innermost value of StatusBars and CaptionBar.

@Composable fun FitInsideWithStatusAndCaptionBarDemo(modifier: Modifier) { Box( modifier = modifier .fillMaxSize() .fitInside( RectRulers.innermostOf( WindowInsetsRulers.StatusBars.current, WindowInsetsRulers.CaptionBar.current ) ) ) }InsetsSnippets.kt

You can align content to custom rulers. For example, consider the use case where a parent composable improperly handles insets causing padding issues in a downstream child. While this issue can be solved in other ways, including by using Modifier.fitInside, you can also create a custom ruler to precisely align the child composable without having the fix the issue in the parent upstream as shown in the following example and video:

@Composable fun WindowInsetsRulersDemo(modifier: Modifier) { Box( contentAlignment = BottomCenter, modifier = modifier .fillMaxSize() // The mistake that causes issues downstream, as .padding doesn't consume insets. // While it's correct to instead use .windowInsetsPadding(WindowInsets.navigationBars), // assume it's difficult to identify this issue to see how WindowInsetsRulers can help. .padding(WindowInsets.navigationBars.asPaddingValues()) ) { TextField( value = "Demo IME Insets", onValueChange = {}, modifier = modifier // Use alignToSafeDrawing() instead of .imePadding() to precisely place this child // Composable without having to fix the parent upstream. .alignToSafeDrawing() // .imePadding() // .fillMaxWidth() ) } } fun Modifier.alignToSafeDrawing(): Modifier { return layout { measurable, constraints -> if (constraints.hasBoundedWidth && constraints.hasBoundedHeight) { val placeable = measurable.measure(constraints) val width = placeable.width val height = placeable.height layout(width, height) { val bottom = WindowInsetsRulers.SafeDrawing.current.bottom .current(0f).roundToInt() - height val right = WindowInsetsRulers.SafeDrawing.current.right .current(0f).roundToInt() val left = WindowInsetsRulers.SafeDrawing.current.left .current(0f).roundToInt() measurable.measure(Constraints.fixed(right - left, height)) .place(left, bottom) } } else { val placeable = measurable.measure(constraints) layout(placeable.width, placeable.height) { placeable.place(0, 0) } } } }InsetsSnippets.kt

The following video shows an example of problematic IME inset consumption caused by an upstream parent in the image on the left, and using custom rulers to fix the issue on the right. Extra padding is shown underneath the TextField Composable as the navigation bar padding wasn't consumed by the parent. The child is placed in the correct location in the right image using a custom ruler as seen in the preceding code sample.

In order to safely use WindowInsetsRulers, make sure the parent provides valid constraints. Parents must have a defined size and can't depend on the size of a child that uses WindowInsetsRulers. Use fillMaxSize or other size modifiers on parent Composables.

Similarly, placing a composable that uses WindowInsetsRulers inside a scrolling container like verticalScroll can cause unexpected behavior as the scrolling container provides unbounded height constraints, which are incompatible with the ruler's logic.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Common shared element use cases Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/shared-elements/common-use-cases

**Contents:**
- Common shared element use cases Stay organized with collections Save and categorize content based on your preferences.
- Asynchronous images
- Text

When animating shared elements, there are some particular use cases that have specific recommendations.

It's common to use a library to load up an image asynchronously, such as when using Coil's AsyncImage composable. For it to work seamlessly between two composables, its recommended to set the placeholderMemoryCacheKey() and memoryCacheKey() to the same key as a string derived from the shared element key, such that the cache key is the same for the matched shared elements. The new shared element will be using its match's cache as the placeholder until it loads the new image.

The typical usage for AsyncImage is as follows:

AsyncImage( model = ImageRequest.Builder(LocalContext.current) .data("your-image-url") .crossfade(true) .placeholderMemoryCacheKey("image-key") // same key as shared element key .memoryCacheKey("image-key") // same key as shared element key .build(), placeholder = null, contentDescription = null, modifier = Modifier .size(120.dp) .sharedBounds( rememberSharedContentState( key = "image-key" ), animatedVisibilityScope = this ) )SharedElementCommonUseCaseSnippets.kt

To animate fontSize changes, use Modifier.sharedBounds(), resizeMode = ScaleToBounds(). This transition makes the size change relatively fluid. The contentScale parameter can be tweaked to animate a specific font weight or style.

Text( text = "This is an example of how to share text", modifier = Modifier .wrapContentWidth() .sharedBounds( rememberSharedContentState( key = "shared Text" ), animatedVisibilityScope = this, enter = fadeIn(), exit = fadeOut(), resizeMode = SharedTransitionScope.ResizeMode.scaleToBounds() ) )SharedElementCommonUseCaseSnippets.kt

TextAlign changes are not animated by default. Instead, use Modifier.wrapContentSize() or Modifier.wrapContentWidth() over using different TextAlign for shared transitions.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Manage and update GlanceAppWidget Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/glance-app-widget

**Contents:**
- Manage and update GlanceAppWidget Stay organized with collections Save and categorize content based on your preferences.
- Manage GlanceAppWidget state
  - Use application state
- Update GlanceAppWidget
  - When to update widgets
- Resources

The following sections describe how to update GlanceAppWidget and manage its state.

The provided GlanceAppWidget class is instantiated whenever the widget is created or requires an update, so it should be stateless and passive.

The concept of state can be divided into the following:

App widgets should be passive. Each application is responsible for managing the data layer and handling the states, such as idle, loading, and error reflecting in the widget UI.

For example, the following code retrieves the destinations from the in-memory cache from the repository layer, provides the stored list of destinations, and displays a different UI depending on its state:

class DestinationAppWidget : GlanceAppWidget() { // ... @Composable fun MyContent() { val repository = remember { DestinationsRepository.getInstance() } // Retrieve the cache data everytime the content is refreshed val destinations by repository.destinations.collectAsState(State.Loading) when (destinations) { is State.Loading -> { // show loading content } is State.Error -> { // show widget error content } is State.Completed -> { // show the list of destinations } } } }GlanceSnippets.kt

Whenever the state or the data changes, it is the app's responsibility to notify and update the widget. See Update GlanceAppWidget for more information.

You can request to update your widget content using GlanceAppWidget. As explained in the Manage GlanceAppWidget state section, app widgets are hosted in a different process. Glance translates the content into actual RemoteViews and sends them to the host. To update the content, Glance must recreate the RemoteViews and send them again.

To send the update, call the update method of the GlanceAppWidget instance, providing the context and the glanceId:

MyAppWidget().update(context, glanceId)GlanceSnippets.kt

To obtain the glanceId, query the GlanceAppWidgetManager:

val manager = GlanceAppWidgetManager(context) val widget = GlanceSizeModeWidget() val glanceIds = manager.getGlanceIds(widget.javaClass) glanceIds.forEach { glanceId -> widget.update(context, glanceId) }GlanceSnippets.kt

Alternatively, use one of the GlanceAppWidget update extensions:

// Updates all placed instances of MyAppWidget MyAppWidget().updateAll(context) // Iterate over all placed instances of MyAppWidget and update if the state of // the instance matches the given predicate MyAppWidget().updateIf<State>(context) { state -> state == State.Completed }GlanceSnippets.kt

These methods can be called from any part of your application. Because they are suspend functions, we recommend launching them outside of the main thread scope. In the following example, they are launched in a CoroutineWorker:

class DataSyncWorker( val context: Context, val params: WorkerParameters, ) : CoroutineWorker(context, params) { override suspend fun doWork(): Result { // Fetch data or do some work and then update all instance of your widget MyAppWidget().updateAll(context) return Result.success() } }GlanceSnippets.kt

See Kotlin Coroutines on Android for more details on coroutines.

Update widgets either immediately or periodically.

Your widget can update immediately when your app is awake. For example:

In these cases, call the update method as described in this guide.

Your widget can update periodically when your app isn't awake. For example:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Display nested scrolling items in a list Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/display-nested-list

**Contents:**
- Display nested scrolling items in a list Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Implement nested horizontal scrolling in vertical list
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components
  - Compose basics (video collection)
- Have questions or feedback

You can display nested scrolling items within a list to present complex layouts, such as product catalogs, media galleries, news feeds, and more.

The following video shows the resulting behaviors of nested horizontal lists within a vertical scrolling list.

This implementation requires that your project minSDK be set to API level 21 or higher.

The following code produces a list that scrolls two ways. The rows of the list scroll horizontally; the list as a whole—a single column—scrolls vertically.

@Composable fun NestedScrollingRowsList(urls: List<String>) { LazyColumn { items(10) { LazyRow { item { Text("Row: $it") } items(urls.size) { index -> // AsyncImage provided by Coil. AsyncImage( model = urls[index], modifier = Modifier.size(150.dp), contentDescription = null ) } } } } }NestedScrollingItem.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Animations additional resources Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/resources

**Contents:**
- Animations additional resources Stay organized with collections Save and categorize content based on your preferences.
- Animation cheat sheet
- Samples
- Blog posts
- Codelabs
- Videos

To learn more about animation in Jetpack Compose, consult the following additional resources:

The Compose animation cheat sheet is a quick reference of some of the most useful Compose animation APIs. The cheat sheet is also downloadable in PDF format.

Five quick animations in Compose

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Divider Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/divider

**Contents:**
- Divider Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Horizontal divider example
- Vertical divider example
- Additional resources

Dividers are thin lines that separate items in lists or other containers. You can implement dividers in your app using the HorizontalDivider and VerticalDivider composables.

Both components provide parameters for modifying their appearance:

The following example demonstrates an implementation of the HorizontalDivider component. It uses the thickness parameter to control the height of the line:

@Composable fun HorizontalDividerExample() { Column( verticalArrangement = Arrangement.spacedBy(8.dp), ) { Text("First item in list") HorizontalDivider(thickness = 2.dp) Text("Second item in list") } }Divider.kt

This implementation renders a thin horizontal line between two text components:

The following example demonstrates an implementation of the VerticalDivider component. It uses the color parameter to provide a custom color for the line:

@Composable fun VerticalDividerExample() { Row( modifier = Modifier .fillMaxWidth() .height(IntrinsicSize.Min), horizontalArrangement = Arrangement.SpaceEvenly ) { Text("First item in row") VerticalDivider(color = MaterialTheme.colorScheme.secondary) Text("Second item in row") } }Divider.kt

This implementation renders a thin vertical line between two text components:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Switch Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/switch

**Contents:**
- Switch Stay organized with collections Save and categorize content based on your preferences.
- Basic implementation
- Advanced implementation
  - Custom thumb
  - Custom colors
- Additional resources

The Switch component allows users to toggle between two states: checked and unchecked. In your app you may use a switch to let the user to do one of the following:

The component has two parts: the thumb and the track. The thumb is the draggable part of the switch, and the track is the background. The user can drag the thumb to the left or right to change the state of the switch. They can also tap the switch to check and clear it.

See the Switch reference for a full API definition. The following are some of the key parameters you might need to use:

The following example is a minimal implementation of the Switch composable.

@Composable fun SwitchMinimalExample() { var checked by remember { mutableStateOf(true) } Switch( checked = checked, onCheckedChange = { checked = it } ) }Switch.kt

This implementation appears as follows when unchecked:

This is the appearance when checked:

The primary parameters you might want to use when implementing a more advanced switch are the following:

You can pass any composable for the thumbContent parameter to create a custom thumb. The following is an example of a switch that uses a custom icon for its thumb:

@Composable fun SwitchWithIconExample() { var checked by remember { mutableStateOf(true) } Switch( checked = checked, onCheckedChange = { checked = it }, thumbContent = if (checked) { { Icon( imageVector = Icons.Filled.Check, contentDescription = null, modifier = Modifier.size(SwitchDefaults.IconSize), ) } } else { null } ) }Switch.kt

In this implementation, the unchecked appearance is the same as the example in the preceding section. However, when checked, this implementation appears as follows:

The following example demonstrates how you can use the colors parameter to change the color of a switch's thumb and track, taking into account whether the switch is checked.

@Composable fun SwitchWithCustomColors() { var checked by remember { mutableStateOf(true) } Switch( checked = checked, onCheckedChange = { checked = it }, colors = SwitchDefaults.colors( checkedThumbColor = MaterialTheme.colorScheme.primary, checkedTrackColor = MaterialTheme.colorScheme.primaryContainer, uncheckedThumbColor = MaterialTheme.colorScheme.secondary, uncheckedTrackColor = MaterialTheme.colorScheme.secondaryContainer, ) ) }Switch.kt

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=list

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Glance setup Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/setup

**Contents:**
- Glance setup Stay organized with collections Save and categorize content based on your preferences.
- Activate Compose compiler

This page describes how to set up your development environment to use Glance. You can get the latest available version from the release page.

Add the specific Glance dependency in your app's module based on the type of "glanceable" you want to build.

Set the following options to ensure that the Compose compiler is available for Glance:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
dependencies {
   // For AppWidgets support
   implementation "androidx.glance:glance-appwidget:1.1.1"

   // For interop APIs with Material 3
   implementation "androidx.glance:glance-material3:1.1.1"

   // For interop APIs with Material 2
   implementation "androidx.glance:glance-material:1.1.1"
}
```

Example 2 (unknown):
```unknown
android {
   buildFeatures {
      compose true
   }

   composeOptions {
      kotlinCompilerExtensionVersion = "1.5.15"
   }

   kotlinOptions {
      jvmTarget = "1.8"
   }
}
```

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?text=image

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Segmented button Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/segmented-button

**Contents:**
- Segmented button Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Create a single-select segmented button
  - Key points about the code
  - Result
- Create a multi-select segmented button
  - Key points about the code
  - Result
- Additional resources

Use a segmented button to let users choose from a set of options, side-by-side. There are two types of segmented buttons:

Use the SingleChoiceSegmentedButtonRow and MultiChoiceSegmentedButtonRow layouts to create segmented buttons. These layouts position and size SegmentedButtons correctly, and share the following key parameters:

This example shows how to create a single-select segmented button:

@Composable fun SingleChoiceSegmentedButton(modifier: Modifier = Modifier) { var selectedIndex by remember { mutableIntStateOf(0) } val options = listOf("Day", "Month", "Week") SingleChoiceSegmentedButtonRow { options.forEachIndexed { index, label -> SegmentedButton( shape = SegmentedButtonDefaults.itemShape( index = index, count = options.size ), onClick = { selectedIndex = index }, selected = index == selectedIndex, label = { Text(label) } ) } } }SegmentedButton.kt

This example shows how to create a multi-choice segmented button with icons that lets users select multiple options:

@Composable fun MultiChoiceSegmentedButton(modifier: Modifier = Modifier) { val selectedOptions = remember { mutableStateListOf(false, false, false) } val options = listOf("Walk", "Ride", "Drive") MultiChoiceSegmentedButtonRow { options.forEachIndexed { index, label -> SegmentedButton( shape = SegmentedButtonDefaults.itemShape( index = index, count = options.size ), checked = selectedOptions[index], onCheckedChange = { selectedOptions[index] = !selectedOptions[index] }, icon = { SegmentedButtonDefaults.Icon(selectedOptions[index]) }, label = { when (label) { "Walk" -> Icon( imageVector = Icons.AutoMirrored.Filled.DirectionsWalk, contentDescription = "Directions Walk" ) "Ride" -> Icon( imageVector = Icons.Default.DirectionsBus, contentDescription = "Directions Bus" ) "Drive" -> Icon( imageVector = Icons.Default.DirectionsCar, contentDescription = "Directions Car" ) } } ) } } }SegmentedButton.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Use keyboard IME animations Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/keyboard-animations

**Contents:**
- Use keyboard IME animations Stay organized with collections Save and categorize content based on your preferences.

You can apply Modifier.imeNestedScroll() to a scrolling container to open and close the IME automatically when scrolling to the bottom of the container.

class WindowInsetsExampleActivity : AppCompatActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) WindowCompat.setDecorFitsSystemWindows(window, false) setContent { MaterialTheme { MyScreen() } } } } @OptIn(ExperimentalLayoutApi::class) @Composable fun MyScreen() { Box { LazyColumn( modifier = Modifier .fillMaxSize() // fill the entire window .imePadding() // padding for the bottom for the IME .imeNestedScroll(), // scroll IME at the bottom content = { } ) FloatingActionButton( modifier = Modifier .align(Alignment.BottomEnd) .padding(16.dp) // normal 16dp of padding for FABs .navigationBarsPadding() // padding for navigation bar .imePadding(), // padding for when IME appears onClick = { } ) { Icon(imageVector = Icons.Filled.Add, contentDescription = "Add") } } }MigrationOtherConsiderationsSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Floating action button Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/fab

**Contents:**
- Floating action button Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Floating action button
- Small button
- Large button
- Extended button
- Additional resources

A Floating Action Button (FAB) is a high-emphasis button that lets the user perform a primary action in an application. It promotes a single, focused action that is the most common pathway a user might take and is typically found anchored to the bottom right of the screen.

Consider these three use cases where you might use a FAB:

In Material Design, there are four types of FAB:

Although there are several composables you can use to create floating action buttons consistent with Material Design, their parameters don't differ greatly. Among the key parameters you should keep in mind are the following:

To create a general floating action button, use the basic FloatingActionButton composable. The following example demonstrates a basic implementation of a FAB:

@Composable fun Example(onClick: () -> Unit) { FloatingActionButton( onClick = { onClick() }, ) { Icon(Icons.Filled.Add, "Floating action button.") } }FloatingActionButton.kt

This implementation appears as follows:

To create a small floating action button, use the SmallFloatingActionButton composable. The following example demonstrates how to do so, with the addition of custom colors.

@Composable fun SmallExample(onClick: () -> Unit) { SmallFloatingActionButton( onClick = { onClick() }, containerColor = MaterialTheme.colorScheme.secondaryContainer, contentColor = MaterialTheme.colorScheme.secondary ) { Icon(Icons.Filled.Add, "Small floating action button.") } }FloatingActionButton.kt

This implementation appears as follows:

To create a large floating action button, use the LargeFloatingActionButton composable. This composable is not significantly different from the other examples aside from the fact that it results in a bigger button.

The following is a straightforward implementation of a large FAB.

@Composable fun LargeExample(onClick: () -> Unit) { LargeFloatingActionButton( onClick = { onClick() }, shape = CircleShape, ) { Icon(Icons.Filled.Add, "Large floating action button") } }FloatingActionButton.kt

This implementation appears as follows:

You can create more complex floating action buttons with the ExtendedFloatingActionButton composable. The key difference between it and FloatingActionButton is that it has dedicated icon and text parameters. They let you create a button with more complex content that scales to fit its content appropriately.

The following snippet demonstrates how to implement ExtendedFloatingActionButton, with example values passed for icon and text.

@Composable fun ExtendedExample(onClick: () -> Unit) { ExtendedFloatingActionButton( onClick = { onClick() }, icon = { Icon(Icons.Filled.Edit, "Extended floating action button.") }, text = { Text(text = "Extended FAB") }, ) }FloatingActionButton.kt

This implementation appears as follows:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Access progress manually Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/predictive-back-progress

**Contents:**
- Access progress manually Stay organized with collections Save and categorize content based on your preferences.
- Example: Integrate with a navigation drawer

The PredictiveBackHandler composable in Jetpack Compose lets you intercept the back gesture and access its progress. You can react to the user's back gesture in real-time, creating custom animations or behaviors based on how far the user swipes.

To use the PredictiveBackHandler, ensure you are using androidx.activity:activity:1.6.0 or higher.

PredictiveBackHandler provides a Flow<BackEventCompat> that emits events representing the progress of the back gesture. Each event contains information such as:

The following snippet shows basic usage of PredictiveBackHandler:

PredictiveBackHandler(true) { progress: Flow<BackEventCompat> -> // code for gesture back started try { progress.collect { backEvent -> // code for progress boxScale = 1F - (1F * backEvent.progress) } // code for completion boxScale = 0F } catch (e: CancellationException) { // code for cancellation boxScale = 1F throw e } }PredictiveBackSnippets.kt

This example demonstrates how to implement a custom in-app animation using PredictiveBackHandler to create a smooth interaction with a navigation drawer in response to back gestures in JetLagged:

In this example, PredictiveBackHandler is used to:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Tabs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/tabs

**Contents:**
- Tabs Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Example: Tab-based navigation
  - Key points
  - Result
- Additional resources

Tabs allow you to organize groups of related content. There are two types of tabs:

This page shows how to display primary tabs in your app with related screens and basic navigation.

Use the Tab, PrimaryTabRow, and SecondaryTabRow composables to implement tabs. The Tab composable represents an individual tab within the row, and is typically used inside of a PrimaryTabRow (for primary indicator tabs) or SecondaryTabRow (for secondary indicator tabs).

Tab includes the following key parameters:

The following snippet implements a top navigation bar with tabs to navigate between different screens in an app:

@Composable fun NavigationTabExample(modifier: Modifier = Modifier) { val navController = rememberNavController() val startDestination = Destination.SONGS var selectedDestination by rememberSaveable { mutableIntStateOf(startDestination.ordinal) } Scaffold(modifier = modifier) { contentPadding -> PrimaryTabRow(selectedTabIndex = selectedDestination, modifier = Modifier.padding(contentPadding)) { Destination.entries.forEachIndexed { index, destination -> Tab( selected = selectedDestination == index, onClick = { navController.navigate(route = destination.route) selectedDestination = index }, text = { Text( text = destination.label, maxLines = 2, overflow = TextOverflow.Ellipsis ) } ) } } AppNavHost(navController, startDestination) } }Navigation.kt

The following image shows the result of the previous snippet:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Shared elements with Navigation Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/shared-elements/navigation

**Contents:**
- Shared elements with Navigation Compose Stay organized with collections Save and categorize content based on your preferences.
- Predictive back with shared elements

To use shared elements with the navigation-compose dependency, use the Modifier.sharedElement() that takes an AnimatedVisibilityScope as a parameter. You can use this to decide what should be visible and when.

The following is an example of using navigation-compose with shared elements:

@Preview @Composable fun SharedElement_PredictiveBack() { SharedTransitionLayout { val navController = rememberNavController() NavHost( navController = navController, startDestination = "home" ) { composable("home") { HomeScreen( this@SharedTransitionLayout, this@composable, { navController.navigate("details/$it") } ) } composable( "details/{item}", arguments = listOf(navArgument("item") { type = NavType.IntType }) ) { backStackEntry -> val id = backStackEntry.arguments?.getInt("item") val snack = listSnacks[id!!] DetailsScreen( id, snack, this@SharedTransitionLayout, this@composable, { navController.navigate("home") } ) } } } } @Composable fun DetailsScreen( id: Int, snack: Snack, sharedTransitionScope: SharedTransitionScope, animatedContentScope: AnimatedContentScope, onBackPressed: () -> Unit ) { with(sharedTransitionScope) { Column( Modifier .fillMaxSize() .clickable { onBackPressed() } ) { Image( painterResource(id = snack.image), contentDescription = snack.description, contentScale = ContentScale.Crop, modifier = Modifier .sharedElement( sharedTransitionScope.rememberSharedContentState(key = "image-$id"), animatedVisibilityScope = animatedContentScope ) .aspectRatio(1f) .fillMaxWidth() ) Text( snack.name, fontSize = 18.sp, modifier = Modifier .sharedElement( sharedTransitionScope.rememberSharedContentState(key = "text-$id"), animatedVisibilityScope = animatedContentScope ) .fillMaxWidth() ) } } } @Composable fun HomeScreen( sharedTransitionScope: SharedTransitionScope, animatedContentScope: AnimatedContentScope, onItemClick: (Int) -> Unit, ) { LazyColumn( modifier = Modifier .fillMaxSize() .padding(8.dp), verticalArrangement = Arrangement.spacedBy(8.dp) ) { itemsIndexed(listSnacks) { index, item -> Row( Modifier.clickable { onItemClick(index) } ) { Spacer(modifier = Modifier.width(8.dp)) with(sharedTransitionScope) { Image( painterResource(id = item.image), contentDescription = item.description, contentScale = ContentScale.Crop, modifier = Modifier .sharedElement( sharedTransitionScope.rememberSharedContentState(key = "image-$index"), animatedVisibilityScope = animatedContentScope ) .size(100.dp) ) Spacer(modifier = Modifier.width(8.dp)) Text( item.name, fontSize = 18.sp, modifier = Modifier .align(Alignment.CenterVertically) .sharedElement( sharedTransitionScope.rememberSharedContentState(key = "text-$index"), animatedVisibilityScope = animatedContentScope, ) ) } } } } } data class Snack( val name: String, val description: String, @DrawableRes val image: Int )SharedElementsWithNavigationSnippets.kt

To use predictive back with shared elements, follow these steps:

Use the latest navigation-compose dependency, using the snippet from the preceding section.

Enable the Predictive back setting in developer options.

Add android:enableOnBackInvokedCallback="true" to your AndroidManifest.xml file:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (bash):
```bash
dependencies {
    def nav_version = "2.8.0-beta02"

    implementation "androidx.navigation:navigation-compose:$nav_version"
}
```

Example 2 (jsx):
```jsx
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"
      android:maxSdkVersion="28" />

  <application
      android:allowBackup="true"
      android:icon="@mipmap/ic_launcher"
      android:label="@string/app_name"
      android:roundIcon="@mipmap/ic_launcher_round"
      android:supportsRtl="true"
      android:enableOnBackInvokedCallback="true"
      android:theme="@style/Theme.Snippets">
```

---

## Add generated previews to your widget picker Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/generated-previews

**Contents:**
- Add generated previews to your widget picker Stay organized with collections Save and categorize content based on your preferences.
- Set up your app for generated widget previews
  - Generate updated preview with Jetpack Glance
    - Troubleshooting Generated Previews
  - Generate updated preview without Jetpack Glance
- Add scalable widget previews to the widget picker
- Backward compatibility with widget previews
- Build accurate previews that include dynamic items
  - Complex list items

Generated widget previews allow you to create dynamic, personalized previews for your widgets that accurately reflect how they will appear on a user's home screen. They are provided through a push API, meaning your app provides the preview at any point during its lifecycle without receiving an explicit request from the widget host.

To improve your app's widget picker experience, provide a generated widget preview on Android 15 and later devices, a scaled widget preview (by specifying a previewLayout) for Android 12 to Android 14 devices, and a previewImage for earlier versions.

For more information, see Enrich your app with live updates and widgets on YouTube.

To show Generated Widget Previews on Android 15 or later device, first set the compileSdk value to 35 or later in the module build.gradle file to have the ability to provide RemoteViews to the widget picker

Apps can then use setWidgetPreview in either GlanceAppWidgetManager or AppWidgetManager. To prevent abuse and mitigate system health concerns, setWidgetPreview is a rate-limited API. The default limit is approximately two calls per hour.

For widgets built with Jetpack Glance, do the following:

Override the GlanceAppWidget.providePreview function to provide the composable content for the preview. As you would in provideGlance, load your app's data and pass it to the widget's content composable, to ensure the preview shows accurate data. Unlike provideGlance, this is a single composition with no recomposition or effects.

Call GlanceAppWidgetManager.setWidgetPreviews to generate and publish the preview.

There isn't a callback from the system to provide previews, so your app must decide when to call setWidgetPreviews. The update strategy depends on your widget's use case:

A common issue is that after you generate a preview, images, icons, or other composables might be missing from the preview image, relative to the widget's drop size. This drop size is defined by the targetCellWidth and targetCellHeight if specified, or by the minWidth and minHeight in the app widget provider info file.

This occurs because Android, by default, renders only composables visible at the widget's minimum size. In other words, Android sets the previewSizeMode to SizeMode.Single by default. It uses android:minHeight and android:minWidth in the app widget provider info XML to determine which composables to draw.

To fix this, override previewSizeMode in your GlanceAppWidget and set it to SizeMode.Responsive, providing a set of DpSize values. This tells Android all the layout sizes it needs to render for the preview, which ensures all elements display correctly.

Optimize for specific form factors. Supply 1 or 2 sizes starting from the minimum and following your widget's breakpoints. Specify at least one image for backward compatibility. You can find the appropriate minimum DP values for different grid sizes in the widget design guidance.

You can use RemoteViews without Glance. The following example loads an XML widget layout resource and sets it as the preview. A compileSdk build setting of 35 or later is required for setWidgetPreview to show as a method in this snippet.

Starting in Android 12, the widget preview displayed in the widget picker is scalable. You provide it as an XML layout set to the widget's default size. Previously, the widget preview was a static drawable resource, in some cases leading to previews inaccurately reflecting how widgets appear when they are added to the home screen.

To implement scalable widget previews, use the previewLayout attribute of the appwidget-provider element to provide an XML layout instead:

We recommend using the same layout as the actual widget, with realistic default or test values. Most apps use the same previewLayout and initialLayout. For guidance on creating accurate preview layouts, see the following section in this page.

We recommend specifying both the previewLayout and previewImage attributes, so that your app can fall back to using previewImage if the user's device doesn't support previewLayout. The previewLayout attribute takes precedence over the previewImage attribute.

To let widget pickers on Android 11 (API level 30) or lower show previews of your widget, or as a fallback for Generated previews, specify the previewImage attribute.

If you change the widget's appearance, update the preview image.

This section explains the recommended approach for displaying multiple items in a widget preview for a widget with a collection view—that is, a widget that uses a ListView, GridView, or StackView. This doesn't apply to generated widget previews.

If your widget uses one of these views, creating a scalable preview by directly providing the actual widget layout degrades the experience when the widget preview displays no items. This occurs because collection view data is set dynamically at runtime, and it looks similar to the image shown in figure 1.

To make previews of widgets with collection views display properly in the widget picker, we recommend maintaining a separate layout file designated for only the preview. This separate layout file should include the following:

To illustrate an example for a ListView, start with a separate layout file:

Specify the preview layout file when providing the previewLayout attribute of the AppWidgetProviderInfo metadata. You still specify the actual widget layout for the initialLayout attribute and use the actual widget layout when constructing a RemoteViews at runtime.

The example in the previous section provides fake list items, because the list items are TextView objects. It can be more complex to provide fake items if the items are complex layouts.

Consider a list item that is defined in widget_list_item.xml and consists of two TextView objects:

To provide fake list items, you can include the layout multiple times, but this causes each list item to be identical. To provide unique list items, follow these steps:

Create a set of attributes for the text values:

Use these attributes to set the text:

Create as many styles as required for the preview. Redefine the values in each style:

Apply the styles on the fake items in the preview layout:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (julia):
```julia
AppWidgetManager.getInstance(appContext).setWidgetPreview(
    ComponentName(
        appContext,
        ExampleAppWidgetReceiver::class.java
    ),
    AppWidgetProviderInfo.WIDGET_CATEGORY_HOME_SCREEN,
    RemoteViews("com.example", R.layout.widget_preview)
)AppWidgetSnippets.kt
```

Example 2 (typescript):
```typescript
<appwidget-provider
    android:previewLayout="@layout/my_widget_preview">
</appwidget-provider>
```

Example 3 (jsx):
```jsx
// res/layout/widget_preview.xml

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   android:layout_width="match_parent"
   android:layout_height="wrap_content"
   android:background="@drawable/widget_background"
   android:orientation="vertical">

    // Include the actual widget layout that contains ListView.
    <include
        layout="@layout/widget_view"
        android:layout_width="match_parent"
        android:layout_height="wrap_content" />

    // The number of fake items you include depends on the values you provide
    // for minHeight or targetCellHeight in the AppWidgetProviderInfo
    // definition.

    <TextView android:text="@string/fake_item1"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginVertical="?attr/appWidgetInternalPadding" />

    <TextView android:text="@string/fake_item2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginVertical="?attr/appWidgetInternalPadding" />

</LinearLayout>
```

Example 4 (powershell):
```powershell
<appwidget-provider
    previewLayout="@layout/widget_previe"
    initialLayout="@layout/widget_view" />
```

---

## Checkbox Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/checkbox

**Contents:**
- Checkbox Stay organized with collections Save and categorize content based on your preferences.
- Anatomy
- States
- Implementation
  - Explanation
  - Result
- Advanced example
  - Explanation
  - Result
  - Additional resources

Checkboxes let users select one or more items from a list. You might use a checkbox to let the user do the following:

A checkbox consists of the following elements:

A checkbox can be in one of three states:

The following image demonstrates the three states of a checkbox.

You can use the Checkbox composable to create a checkbox in your app. There are just a few key parameters to keep in mind:

The following snippet demonstrates how to use the Checkbox composable:

@Composable fun CheckboxMinimalExample() { var checked by remember { mutableStateOf(true) } Row( verticalAlignment = Alignment.CenterVertically, ) { Text( "Minimal checkbox" ) Checkbox( checked = checked, onCheckedChange = { checked = it } ) } Text( if (checked) "Checkbox is checked" else "Checkbox is unchecked" ) }Checkbox.kt

This code creates a checkbox that is initially unchecked. When the user clicks on the checkbox, the onCheckedChange lambda updates the checked state.

This example produces the following component when unchecked:

And this is how the same checkbox appears when checked:

The following is a more complex example of how you can implement checkboxes in your app. In this snippet, there is a parent checkbox and a series of child checkboxes. When the user taps the parent checkbox, the app checks all child checkboxes.

@Composable fun CheckboxParentExample() { // Initialize states for the child checkboxes val childCheckedStates = remember { mutableStateListOf(false, false, false) } // Compute the parent state based on children's states val parentState = when { childCheckedStates.all { it } -> ToggleableState.On childCheckedStates.none { it } -> ToggleableState.Off else -> ToggleableState.Indeterminate } Column { // Parent TriStateCheckbox Row( verticalAlignment = Alignment.CenterVertically, ) { Text("Select all") TriStateCheckbox( state = parentState, onClick = { // Determine new state based on current state val newState = parentState != ToggleableState.On childCheckedStates.forEachIndexed { index, _ -> childCheckedStates[index] = newState } } ) } // Child Checkboxes childCheckedStates.forEachIndexed { index, checked -> Row( verticalAlignment = Alignment.CenterVertically, ) { Text("Option ${index + 1}") Checkbox( checked = checked, onCheckedChange = { isChecked -> // Update the individual child state childCheckedStates[index] = isChecked } ) } } } if (childCheckedStates.all { it }) { Text("All options selected") } }Checkbox.kt

The following are several points you should note from this example:

This example produces the following component when all checkboxes are unchecked.

Likewise, this is how the component appears when all options are checked, as when the user taps select all:

When only one option is checked the parent checkbox display the indeterminate state:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Create a finite scrollable list Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/finite-scrolling-list

**Contents:**
- Create a finite scrollable list Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Create a vertical scrolling list
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components
  - Compose basics (video collection)

Scrollable lists can help manage datasets, create responsive designs, and facilitate navigation. You can display smaller sets of items in your app by using a finite scrolling list. To avoid performance issues with larger datasets or a list of unknown length, see Lazily load data with lists and Paging.

Figure 1. A vertical scrolling list.

This implementation requires that your project minSDK be set to API level 21 or higher.

Use the following code to create a vertical scrolling list:

@Composable private fun ScrollBoxes() { Column( modifier = Modifier .background(Color.LightGray) .size(100.dp) .verticalScroll(rememberScrollState()) ) { repeat(10) { Text("Item $it", modifier = Modifier.padding(2.dp)) } } }GesturesSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Navigation drawer Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/drawer

**Contents:**
- Navigation drawer Stay organized with collections Save and categorize content based on your preferences.
- Example
- Control behavior
- Create groups within a navigation drawer
  - Key points about the code
  - Result
- Additional resources

The navigation drawer component is a slide-in menu that lets users navigate to various sections of your app. Users can activate it by swiping from the side or tapping a menu icon.

Consider these three use cases for implementing a Navigation Drawer:

In Material Design, there are two types of navigation drawers:

You can use the ModalNavigationDrawer composable to implement a navigation drawer.

Use the drawerContent slot to provide a ModalDrawerSheet and provide the drawer's contents, as in the following example:

ModalNavigationDrawer( drawerContent = { ModalDrawerSheet { Text("Drawer title", modifier = Modifier.padding(16.dp)) HorizontalDivider() NavigationDrawerItem( label = { Text(text = "Drawer Item") }, selected = false, onClick = { /*TODO*/ } ) // ...other drawer items } } ) { // Screen content }MaterialLayoutSnippets.kt

ModalNavigationDrawer accepts a number of additional drawer parameters. For example, you can toggle whether or not the drawer responds to drags with the gesturesEnabled parameter as in the following example:

ModalNavigationDrawer( drawerContent = { ModalDrawerSheet { // Drawer contents } }, gesturesEnabled = false ) { // Screen content }MaterialLayoutSnippets.kt

To control how the drawer opens and closes, use DrawerState. You should pass a DrawerState to ModalNavigationDrawer using the drawerState parameter.

DrawerState provides access to the open and close functions, as well as properties related to the current drawer state. These suspending functions require a CoroutineScope, which you can instantiate using rememberCoroutineScope. You can also call the suspending functions in response to UI events.

val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed) val scope = rememberCoroutineScope() ModalNavigationDrawer( drawerState = drawerState, drawerContent = { ModalDrawerSheet { /* Drawer content */ } }, ) { Scaffold( floatingActionButton = { ExtendedFloatingActionButton( text = { Text("Show drawer") }, icon = { Icon(Icons.Filled.Add, contentDescription = "") }, onClick = { scope.launch { drawerState.apply { if (isClosed) open() else close() } } } ) } ) { contentPadding -> // Screen content } }MaterialLayoutSnippets.kt

The following snippet shows how to create a detailed navigation drawer, with sections and dividers:

@Composable fun DetailedDrawerExample( content: @Composable (PaddingValues) -> Unit ) { val drawerState = rememberDrawerState(initialValue = DrawerValue.Closed) val scope = rememberCoroutineScope() ModalNavigationDrawer( drawerContent = { ModalDrawerSheet { Column( modifier = Modifier.padding(horizontal = 16.dp) .verticalScroll(rememberScrollState()) ) { Spacer(Modifier.height(12.dp)) Text("Drawer Title", modifier = Modifier.padding(16.dp), style = MaterialTheme.typography.titleLarge) HorizontalDivider() Text("Section 1", modifier = Modifier.padding(16.dp), style = MaterialTheme.typography.titleMedium) NavigationDrawerItem( label = { Text("Item 1") }, selected = false, onClick = { /* Handle click */ } ) NavigationDrawerItem( label = { Text("Item 2") }, selected = false, onClick = { /* Handle click */ } ) HorizontalDivider(modifier = Modifier.padding(vertical = 8.dp)) Text("Section 2", modifier = Modifier.padding(16.dp), style = MaterialTheme.typography.titleMedium) NavigationDrawerItem( label = { Text("Settings") }, selected = false, icon = { Icon(Icons.Outlined.Settings, contentDescription = null) }, badge = { Text("20") }, // Placeholder onClick = { /* Handle click */ } ) NavigationDrawerItem( label = { Text("Help and feedback") }, selected = false, icon = { Icon(Icons.AutoMirrored.Outlined.Help, contentDescription = null) }, onClick = { /* Handle click */ }, ) Spacer(Modifier.height(12.dp)) } } }, drawerState = drawerState ) { Scaffold( topBar = { TopAppBar( title = { Text("Navigation Drawer Example") }, navigationIcon = { IconButton(onClick = { scope.launch { if (drawerState.isClosed) { drawerState.open() } else { drawerState.close() } } }) { Icon(Icons.Default.Menu, contentDescription = "Menu") } } ) } ) { innerPadding -> content(innerPadding) } } }NavigationDrawer.kt

The following image shows how the drawer appears when opened, with sections and items displayed:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Glance interoperability Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/interoperability

**Contents:**
- Glance interoperability Stay organized with collections Save and categorize content based on your preferences.

In some cases, you may want to use XML and RemoteViews to provide a view. Perhaps you have already implemented a feature without Glance, or the feature is not yet available or possible with the current Glance API. For these situations, Glance provides AndroidRemoteViews, an interoperability API.

The AndroidRemoteViews composable allows RemoteViews to be placed together with your other composables:

val packageName = LocalContext.current.packageName Column(modifier = GlanceModifier.fillMaxSize()) { Text("Isn't that cool?") AndroidRemoteViews(RemoteViews(packageName, R.layout.example_layout)) }GlanceSnippets.kt

Create and define the RemoteViews as you would without Glance, and simply pass it as a parameter.

In addition, you can create RemoteViews containers for your composables:

AndroidRemoteViews( remoteViews = RemoteViews(packageName, R.layout.my_container_view), containerViewId = R.id.example_view ) { Column(modifier = GlanceModifier.fillMaxSize()) { Text("My title") Text("Maybe a long content...") } }GlanceSnippets.kt

In this case, a layout that contains the "container" is passed with the defined ID. This container must be a ViewGroup, since it is used to place the defined content.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Support multi-window mode Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/support-multi-window-mode

**Contents:**
- Support multi-window mode Stay organized with collections Save and categorize content based on your preferences.
- Version-specific multi-window features
- Split-screen mode
  - Launch adjacent
- Activity lifecycle in multi-window mode
  - Multi-resume
  - Android 9
  - Configuration changes
- Exclusive resource access
- Window metrics

Multi-window mode enables multiple apps to share the same screen simultaneously. Apps can be side by side or one above the other (split-screen mode), one app in a small window overlaying other apps (picture-in-picture mode), or individual apps in separate movable, resizable windows (desktop windowing mode).

For user instructions about how to access split‑screen mode on phones, go to See two apps at the same time on Pixel phone.

The multi-window user experience depends on the version of Android and type of device:

Android 7.0 (API level 24) introduced split-screen mode on small screen devices and picture-in-picture mode on select devices.

Split-screen mode fills the screen with two apps, showing them either side by side or one above the other. Users can drag the divider separating the two apps to make one app larger and the other smaller.

Picture-in-picture mode enables users to continue video playback while interacting with another app (see Picture-in-picture support).

Desktop windowing mode, in which users can freely resize each activity, can be enabled by manufacturers of large screen devices.

You can configure how your app handles multi-window mode by specifying your activity's minimum allowable dimensions. You can also disable multi-window mode for your app by setting resizeableActivity="false" to ensure the system always shows your app full screen.

Android 8.0 (API level 26) extends picture-in-picture mode to small screen devices.

Android 12 (API level 31) makes multi-window mode standard behavior.

On large screens (medium or expanded window size class), the platform supports all apps in multi-window mode regardless of app configuration. If resizeableActivity="false", the app is put into compatibility mode when necessary to accommodate display dimensions.

On small screens (compact window size class), the system checks an activity's minWidth and minHeight to determine whether the activity can run in multi-window mode. If resizeableActivity="false", the app is prevented from running in multi-window mode regardless of minimum width and height.

Android 16 (API level 36) overrides screen orientation, aspect ratio, and resizability restrictions.

On large screens (smallest width >= 600dp) the system ignores manifest attributes and runtime APIs used to restrict an app's orientation, aspect ratio, and resizability, optimizing the user experience on all device form factors.

To learn how to exclude games from the Android 16 changes, see App orientation, aspect ratio, and resizability exceptions.

Users activate split-screen mode by doing the following:

Users exit split-screen mode by dragging the window divider to the edge of the screen—up or down, left or right.

If your app needs to access content through an intent, you can use FLAG_ACTIVITY_LAUNCH_ADJACENT to open the content in an adjacent split-screen window.

FLAG_ACTIVITY_LAUNCH_ADJACENT was introduced in Android 7.0 (API level 24) to enable apps running in split-screen mode to launch activities in the adjacent window.

Android 12L (API level 32) and higher have extended the flag's definition to enable apps running full screen to activate split-screen mode and then launch activities in the adjacent window.

To launch an adjacent activity, use FLAG_ACTIVITY_LAUNCH_ADJACENT in conjunction with FLAG_ACTIVITY_NEW_TASK, for example:

Multi-window mode does not change the activity lifecycle. However, the resumed state of apps in multiple windows differs on different versions of Android.

Android 10 (API level 29) and higher versions support multi-resume—all activities remain in the RESUMED state when the device is in multi-window mode. An activity can be paused if a transparent activity is on top of the activity or the activity is not focusable, for example, the activity is in picture-in-picture mode. It's also possible that no activity has focus at a given time, for example, if the notification drawer is open. The onStop() method works as usual: the method is called any time an activity is taken off the screen.

Multi-resume is also available on select devices running Android 9 (API level 28). To opt in to multi-resume on Android 9 devices, add the following manifest metadata:

To verify that a given device supports this manifest metadata, refer to the device specifications.

In multi-window mode on Android 9 (API level 28) and lower, only the activity the user has most recently interacted with is active at a given time. This activity is considered topmost, and is the only activity in the RESUMED state. All other visible activities are STARTED but are not RESUMED. However, the system gives these visible but not resumed activities higher priority than activities that are not visible. If the user interacts with one of the visible activities, that activity is resumed, and the previously topmost activity enters the STARTED state.

When there are multiple activities within a single active app process, the activity with the highest z-order is resumed, and the others are paused.

When the user puts an app into multi-window mode, the system notifies the activity of a configuration change as specified in Handle configuration changes. This also happens when the user resizes the app or puts the app back into full screen mode.

Essentially, this change has the same activity lifecycle implications as when the system notifies the app that the device has switched from portrait to landscape orientation, except that the app dimensions are changed instead of just being swapped. Your activity can handle the configuration change itself, or your app can allow the system to destroy the activity and recreate it with the new dimensions.

If the user is resizing a window and makes it larger in either dimension, the system resizes the activity to match the user action and issues configuration changes as needed. If the app lags behind in drawing in newly exposed areas, the system temporarily fills those areas with the color specified by the windowBackground attribute or by the default windowBackgroundFallback style attribute.

To help support the multi-resume feature, use the onTopResumedActivityChanged() lifecycle callback.

The callback is invoked when an activity gains or loses the top resumed activity position, which is important when an activity uses a shared singleton resource, such as the microphone or camera:

Note that an app can lose resources for other reasons, such as removal of a shared piece of hardware.

In any case, an app should gracefully handle events and state changes that affect available resources.

For apps that use a camera, CameraManager.AvailabilityCallback#onCameraAccessPrioritiesChanged() provides a hint that it might be a good time to try to get access to the camera. This method is available as of Android 10 (API level 29).

Remember that resizeableActivity=false is not a guarantee of exclusive camera access, since other apps that use the camera can be opened on other displays.

Your app does not necessary have to release the camera when the app loses focus. For example, you might want to continue camera preview while the user interacts with the newly focused topmost resumed app. It's fine for your app to keep running the camera when it's not the topmost resumed app, but it has to handle the disconnect case properly. When the topmost resumed app wants to use the camera, it can open it, and your app will lose access. Your app can reopen the camera when the app gets the focus back.

After an app receives a CameraDevice.StateCallback#onDisconnected() callback, subsequent calls on the camera device will throw a CameraAccessException.

Android 11 (API level 30) introduced the following WindowManager methods to provide the bounds of apps running in multi-window mode:

The Jetpack WindowManager library methods computeCurrentWindowMetrics() and computeMaximumWindowMetrics() offer similar functionality respectively, but with backward compatibility to API level 14.

To obtain metrics for displays other than the current display, do the following (as shown in the code snippet):

Display methods getSize() and getMetrics() were deprecated in API level 30 in favor of the new WindowManager methods.

Android 12 (API level 31) deprecates Display methods getRealSize() and getRealMetrics() and updates their behavior to more closely match the behavior of getMaximumWindowMetrics().

If your app targets Android 7.0 (API level 24) or higher, you can configure how and whether your app's activities support multi-window mode. You can set attributes in your manifest to control both size and layout. A root activity's attribute settings apply to all activities within its task stack. For example, if the root activity has android:resizeableActivity="true", then all activities in the task stack are resizable. On some larger devices, such as Chromebooks, your app might run in a resizable window even if you specify android:resizeableActivity="false". If this breaks your app, you can use filters on Google Play to restrict your app's availability on such devices.

Android 12 (API level 31) defaults to multi-window mode. On large screens (medium or expanded window size class), all apps run in multi-window mode regardless of app configuration. On small screens, the system checks an activity's minWidth, minHeight, and resizeableActivity settings to determine whether the activity can run in multi-window mode.

Set this attribute in your manifest's <activity> or <application> element to enable or disable multi-window mode for API level 30 and lower:

If this attribute is set to true, the activity can be launched in split-screen and desktop windowing modes. If the attribute is set to false, the activity does not support multi-window mode. If the value is false, and the user attempts to launch the activity in multi-window mode, the activity takes over the full screen.

If your app targets API level 24 or higher, but you don't specify a value for this attribute, the attribute's value defaults to true.

If your app targets API level 31 or higher, this attribute works differently on small and large screens:

If your app targets API level 36 or higher, this attribute is ignored on displays having smallest width >= 600dp. However, the app fully respects the user's choice of aspect ratio (see User per-app overrides).

If you're building a game, see App orientation, aspect ratio, and resizability to learn how you can exclude your game from the Android 16 (API level 36) changes.

Set this attribute in your manifest's <activity> node to indicate whether the activity supports picture-in-picture mode.

To handle multi-window configuration changes yourself, such as when a user resizes a window, add the android:configChanges attribute to your app manifest <activity> node with at least the following values:

After adding android:configChanges, your activity and fragments receive a callback to onConfigurationChanged() instead of being destroyed and recreated. You can then manually update your views, reload resources, and perform other operations as needed.

On Android 7.0 (API level 24) and higher, the <layout> manifest element supports several attributes that affect how an activity behaves in multi-window mode:

android:defaultHeight, android:defaultWidth: Default height and width of the activity when launched in desktop windowing mode.

android:gravity: Initial placement of the activity when launched in desktop windowing mode. See the Gravity class for suitable values.

android:minHeight, android:minWidth: Minimum height and minimum width for the activity in both split-screen and desktop windowing modes. If the user moves the divider in split-screen mode to make an activity smaller than the specified minimum, the system crops the activity to the size the user requests.

The following code shows how to specify an activity's default size and location and its minimum size when the activity is displayed in desktop windowing mode:

Beginning with Android 7.0, the system offers functionality to support apps that can run in multi-window mode.

In multi-window mode, Android might disable or ignore features that don't apply to an activity that is sharing the device screen with other activities or apps.

Additionally, some system UI customization options are disabled. For example, apps cannot hide the status bar if they are running in multi-window mode (see Control the system UI visibility).

The system ignores changes to the android:screenOrientation attribute.

The Activity class offers the following methods to support multi-window mode:

isInMultiWindowMode(): Indicates whether the activity is in multi-window mode.

isInPictureInPictureMode(): Indicates whether the activity is in picture-in-picture mode.

onMultiWindowModeChanged(): The system calls this method whenever the activity goes into or out of multi-window mode. The system passes the method a value of true if the activity is entering multi-window mode or false if the activity is leaving multi-window mode.

onPictureInPictureModeChanged(): The system calls this method whenever the activity goes into or out of picture-in-picture mode. The system passes the method a value of true if the activity is entering picture-in-picture mode or false if the activity is leaving picture-in-picture mode.

The Fragment class exposes versions of many of these methods; for example, Fragment.onMultiWindowModeChanged().

To put an activity in picture-in-picture mode, call enterPictureInPictureMode() This method has no effect if the device does not support picture-in-picture mode. For more information, see Add videos using picture-in-picture (PiP).

When you launch a new activity, you can indicate that the new activity should be displayed adjacent to the current one if possible. Use the intent flag FLAG_ACTIVITY_LAUNCH_ADJACENT, which tells the system to try to create the new activity in an adjacent window, so the two activities share the screen. The system makes a best effort to do this, but it is not guaranteed to happen.

If a device is in desktop windowing mode and you are launching a new activity, you can specify the new activity's dimensions and screen location by calling ActivityOptions.setLaunchBounds(). The method has no effect if the device is not in multi-window mode.

On API level 30 and lower, if you launch an activity within a task stack, the activity replaces the activity on the screen, inheriting all of its multi-window properties. If you want to launch the new activity as a separate window in multi-window mode, you must launch it in a new task stack.

Android 12 (API level 31) enables apps to split an application's task window among multiple activities. You determine how your app displays its activities—full screen, side by side, or stacked—by creating an XML configuration file or making Jetpack WindowManager API calls.

Users can drag and drop data from one activity to another while the two activities are sharing the screen. (Prior to Android 7.0, users could only drag and drop data within a single activity.) To quickly add support for accepting dropped content see the DropHelper API. For comprehensive drag-and-drop guidance, see Enable drag and drop.

Each root activity has its own task, which is displayed in its own window. To launch a new instance of your app in a separate window, start new activities with the FLAG_ACTIVITY_NEW_TASK flag. You can combine this setting with multi-window attributes to request a specific location for the new window. For example, a shopping app can display multiple adjacent windows to compare products.

Android 12 (API level 31) and higher enable you to launch two instances of an activity side by side in the same task window in activity embedding.

If you want to allow users to start another instance of your application from the application launcher or the taskbar, set android:resizeableActivity="true" in your launcher activity's manifest and don't use a launch mode that prevents multiple instances. For example, a singleInstancePerTask activity can be instantiated multiple times in different tasks when FLAG_ACTIVITY_MULTIPLE_TASK or FLAG_ACTIVITY_NEW_DOCUMENT is set.

On Android 15 (API level 35) and higher, PROPERTY_SUPPORTS_MULTI_INSTANCE_SYSTEM_UI enables you to declare support for multi‑instance. The property is an explicit signal for system UI to expose controls to the user to create multiple instances of the app. The property is independent of launch mode but should only be used when the launch mode for an activity or application is compatible with the property, for example, when the launch mode is not singleInstance.

When multiple instances of an app are running in separate windows on a foldable device, one or more instances might be sent to the background if the device posture changes. For example, assume a device is unfolded and has two app instances running in separate windows on each side of the fold. If the device is folded, one of the instances might be terminated instead of trying to fit the windows for both instances on a smaller screen.

Whether or not your app targets API level 24 or higher, you should verify how it behaves in multi-window mode in case a user tries to launch it in multi-window mode on a device running Android 7.0 or higher.

Devices that run Android 7.0 (API level 24) or higher support multi-window mode.

When users attempt to use the app in multi-window mode, the system forcibly resizes the app unless the app declares a fixed orientation.

If your app does not declare a fixed orientation, you should launch your app on a device running Android 7.0 or higher and attempt to put the app in split-screen mode. Verify that the user experience is acceptable when the app is forcibly resized.

If the app declares a fixed orientation, you should attempt to put the app in multi-window mode. Verify that when you do so, the app remains in full screen mode.

If your app targets API levels 24 through 30 and does not disable multi-window support, verify the following behavior under both split-screen and desktop windowing modes:

Launch the app full screen, then switch to multi-window mode by long-pressing the Recents button. Verify that the app switches properly.

Launch the app directly in multi-window mode and verify that the app launches properly. You can launch an app in multi-window mode by pressing the Recents button, then long-pressing the title bar of your app and dragging it to one of the highlighted areas on the screen.

Resize your app in split-screen mode by dragging the screen divider. Verify that the app resizes without crashing and that necessary UI elements are visible.

If you have specified minimum dimensions for your app, attempt to resize the app so that its window size is smaller than those dimensions. Verify that you can't resize the app to be smaller than the specified minimum dimensions.

Through all tests, verify that your app's performance is acceptable. For example, verify that there is not too long a lag to update the UI after the app is resized.

If your app targets API level 31 or higher and the main activity's minimum width and minimum height are less than or equal to the respective dimensions of the available display area, verify all the behaviors listed for API levels 24 through 30.

To verify your app's performance in multi-window mode, try the following operations. You should try these operations in both split-screen and desktop windowing mode, except where otherwise noted.

Enter and leave multi-window mode.

Switch from your app to another app, and verify that the app behaves properly while it is visible but not active. For example, if your app is playing video, verify that the video continues to play while the user is interacting with another app.

In split-screen mode, try moving the screen divider to make your app both larger and smaller. Try these operations in both side by side and one above the other configurations. Verify that the app does not crash, essential functionality is visible, and the resize operation doesn't take too long.

Perform several resize operations in rapid succession. Verify that your app doesn't crash or leak memory. Android Studio's Memory Profiler provides information about your app's memory usage (see Inspect your app's memory usage with Memory Profiler).

Use your app normally in a number of different window configurations, and verify that the app behaves properly. Verify that text is readable and that UI elements aren't too small to interact with.

On API levels 24 through 30, if you disabled multi-window support by setting android:resizeableActivity="false", you should launch your app on a device running Android 7.0 through 11 and attempt to put the app in split-screen and desktop windowing modes. Verify that when you do so, the app remains in full-screen mode.

For further information about multi-window support in Android, see:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-06-12 UTC.

**Examples:**

Example 1 (php):
```php
fun openUrlInAdjacentWindow(url: String) {
    Intent(Intent.ACTION_VIEW).apply { data = Uri.parse(url)
       addFlags(Intent.FLAG_ACTIVITY_LAUNCH_ADJACENT or Intent.FLAG_ACTIVITY_NEW_TASK)
    }.also { intent -> startActivity(intent) }
}
```

Example 2 (typescript):
```typescript
<meta-data android:name="android.allow_multiple_resumed_activities" android:value="true" />
```

Example 3 (unknown):
```unknown
override fun onTopResumedActivityChanged(topResumed: Boolean) {
    if (topResumed) {
        // Top resumed activity.
        // Can be a signal to re-acquire exclusive resources.
    } else {
        // No longer the top resumed activity.
    }
}
```

Example 4 (julia):
```julia
val windowMetrics = context.createDisplayContext(display)
                           .createWindowContext(WindowManager.LayoutParams.TYPE_APPLICATION, null)
                           .getSystemService(WindowManager::class.java)
                           .maximumWindowMetrics
```

---

## Build a list using multiple item types Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/build-list-multiple-item-types

**Contents:**
- Build a list using multiple item types Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Add multiple item types
  - Key points about the code
- Collections that contain this guide
  - Display a list or grid
  - Display interactive components
  - Compose basics (video collection)

You can use a list with multiple item types to display mixed content types such as text, images, and interactive elements.

This implementation requires that your project minSDK be set to API level 21 or higher.

You can specify the content type for each item of the layout when you compose a list or a grid with multiple types of items:

@Composable fun ListWithMultipleItems(messages: List<Any>) { LazyColumn { items( messages.size, contentType = { it } ) { for (message in messages) when (message) { is MediaStore.Audio -> AudioMessage(message) is Text -> TextMessage(message) } } } } @Composable fun AudioMessage(message: MediaStore.Audio) { TODO("Not yet implemented.") } @Composable fun TextMessage(message: Text) { TODO("Not yet implemented.") } data class SampleMessage(val text: String, val content: Any)ListWithMultipleItemTypes.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Date pickers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/datepickers

**Contents:**
- Date pickers Stay organized with collections Save and categorize content based on your preferences.
- Types
- State
- Docked date picker
  - Key points about the code
  - Results
- Modal date picker
  - Key points about the code
  - Results
- Input modal date picker

Date pickers let users select a date, a date range, or both. They use a calendar dialog or text input to let users select dates.

There are three types of date pickers:

You can implement these date pickers in your app using the following composables:

The key parameter that the different date picker composables share in common is state, which takes either a DatePickerState or DateRangePickerState object. Their properties capture information about the user's selection using the date picker, such as the current selected date.

For more information on how you can make use of the selected date, see the Use selected date section.

In the following example, there is a text field that prompts the user to input their date of birth. When they click the calendar icon in the field, it opens a docked date picker below the input field.

@Composable fun DatePickerDocked() { var showDatePicker by remember { mutableStateOf(false) } val datePickerState = rememberDatePickerState() val selectedDate = datePickerState.selectedDateMillis?.let { convertMillisToDate(it) } ?: "" Box( modifier = Modifier.fillMaxWidth() ) { OutlinedTextField( value = selectedDate, onValueChange = { }, label = { Text("DOB") }, readOnly = true, trailingIcon = { IconButton(onClick = { showDatePicker = !showDatePicker }) { Icon( imageVector = Icons.Default.DateRange, contentDescription = "Select date" ) } }, modifier = Modifier .fillMaxWidth() .height(64.dp) ) if (showDatePicker) { Popup( onDismissRequest = { showDatePicker = false }, alignment = Alignment.TopStart ) { Box( modifier = Modifier .fillMaxWidth() .offset(y = 64.dp) .shadow(elevation = 4.dp) .background(MaterialTheme.colorScheme.surface) .padding(16.dp) ) { DatePicker( state = datePickerState, showModeToggle = false ) } } } } } @Composable fun DatePickerFieldToModal(modifier: Modifier = Modifier) { var selectedDate by remember { mutableStateOf<Long?>(null) } var showModal by remember { mutableStateOf(false) } OutlinedTextField( value = selectedDate?.let { convertMillisToDate(it) } ?: "", onValueChange = { }, label = { Text("DOB") }, placeholder = { Text("MM/DD/YYYY") }, trailingIcon = { Icon(Icons.Default.DateRange, contentDescription = "Select date") }, modifier = modifier .fillMaxWidth() .pointerInput(selectedDate) { awaitEachGesture { // Modifier.clickable doesn't work for text fields, so we use Modifier.pointerInput // in the Initial pass to observe events before the text field consumes them // in the Main pass. awaitFirstDown(pass = PointerEventPass.Initial) val upEvent = waitForUpOrCancellation(pass = PointerEventPass.Initial) if (upEvent != null) { showModal = true } } } ) if (showModal) { DatePickerModal( onDateSelected = { selectedDate = it }, onDismiss = { showModal = false } ) } } fun convertMillisToDate(millis: Long): String { val formatter = SimpleDateFormat("MM/dd/yyyy", Locale.getDefault()) return formatter.format(Date(millis)) }DatePickers.kt

After clicking the calendar icon, this implementation appears as follows:

A modal date picker displays a dialog that floats over the screen. To implement it, create a DatePickerDialog and pass it a DatePicker.

@Composable fun DatePickerModal( onDateSelected: (Long?) -> Unit, onDismiss: () -> Unit ) { val datePickerState = rememberDatePickerState() DatePickerDialog( onDismissRequest = onDismiss, confirmButton = { TextButton(onClick = { onDateSelected(datePickerState.selectedDateMillis) onDismiss() }) { Text("OK") } }, dismissButton = { TextButton(onClick = onDismiss) { Text("Cancel") } } ) { DatePicker(state = datePickerState) } }DatePickers.kt

This implementation appears as follows:

A modal date picker with input displays a dialog that floats over the screen and allows the user to input a date.

@Composable fun DatePickerModalInput( onDateSelected: (Long?) -> Unit, onDismiss: () -> Unit ) { val datePickerState = rememberDatePickerState(initialDisplayMode = DisplayMode.Input) DatePickerDialog( onDismissRequest = onDismiss, confirmButton = { TextButton(onClick = { onDateSelected(datePickerState.selectedDateMillis) onDismiss() }) { Text("OK") } }, dismissButton = { TextButton(onClick = onDismiss) { Text("Cancel") } } ) { DatePicker(state = datePickerState) } }DatePickers.kt

This is very much the same as the modal date picker example. The primary difference is the following:

You can create a date picker that lets the user select a range between a start and end date. To do so, use DateRangePicker.

The use of DateRangePicker is essentially the same as DatePicker. You can use it for a docked picker as a child of PopUp, or you can use it as a modal picker and pass it to DatePickerDialog. The primary difference is that you use DateRangePickerState instead of DatePickerState.

The following snippet demonstrates how to create a modal date picker with a range:

@Composable fun DateRangePickerModal( onDateRangeSelected: (Pair<Long?, Long?>) -> Unit, onDismiss: () -> Unit ) { val dateRangePickerState = rememberDateRangePickerState() DatePickerDialog( onDismissRequest = onDismiss, confirmButton = { TextButton( onClick = { onDateRangeSelected( Pair( dateRangePickerState.selectedStartDateMillis, dateRangePickerState.selectedEndDateMillis ) ) onDismiss() } ) { Text("OK") } }, dismissButton = { TextButton(onClick = onDismiss) { Text("Cancel") } } ) { DateRangePicker( state = dateRangePickerState, title = { Text( text = "Select date range" ) }, showModeToggle = false, modifier = Modifier .fillMaxWidth() .height(500.dp) .padding(16.dp) ) } }DatePickers.kt

This implementation appears as follows:

To capture the selected date, track it in the parent composable as a Long and pass the value to the DatePicker in onDateSelected. The following snippet demonstrates this, though you can see the full implementation in the official snippets app.

// ... var selectedDate by remember { mutableStateOf<Long?>(null) } // ... if (selectedDate != null) { val date = Date(selectedDate!!) val formattedDate = SimpleDateFormat("MMM dd, yyyy", Locale.getDefault()).format(date) Text("Selected date: $formattedDate") } else { Text("No date selected") } // ... DatePickerModal( onDateSelected = { selectedDate = it showModal = false }, onDismiss = { showModal = false } ) } // ...DatePickers.kt

Essentially the same applies for range date pickers, though you need to use a Pair<Long?, Long?> or a data class to capture the start and end values.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Alignment lines in Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/alignment-lines

**Contents:**
- Alignment lines in Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.
- Create custom alignment lines
- Recommended for you

The Compose layout model lets you use AlignmentLine to create custom alignment lines that can be used by parent layouts to align and position their children. For example, Row can use its children's custom alignment lines to align them.

When a layout provides a value for a particular AlignmentLine, the layout's parents can read this value after measuring, using the Placeable.get operator on the corresponding Placeable instance. Based on the position of the AlignmentLine, the parents can then decide the positioning of the children.

Some composables in Compose already come with alignment lines. For example, the BasicText composable exposes the FirstBaseline and LastBaseline alignment lines.

In the following example, a custom LayoutModifier called firstBaselineToTop reads the FirstBaseline to add padding to the Text starting from its first baseline.

fun Modifier.firstBaselineToTop( firstBaselineToTop: Dp, ) = layout { measurable, constraints -> // Measure the composable val placeable = measurable.measure(constraints) // Check the composable has a first baseline check(placeable[FirstBaseline] != AlignmentLine.Unspecified) val firstBaseline = placeable[FirstBaseline] // Height of the composable with padding - first baseline val placeableY = firstBaselineToTop.roundToPx() - firstBaseline val height = placeable.height + placeableY layout(placeable.width, height) { // Where the composable gets placed placeable.placeRelative(0, placeableY) } } @Preview @Composable private fun TextWithPaddingToBaseline() { MaterialTheme { Text("Hi there!", Modifier.firstBaselineToTop(32.dp)) } }AlignmentLinesSnippets.kt

In order to read the FirstBaseline in the example, placeable [FirstBaseline] is used in the measurement phase.

When creating a custom Layout composable or a custom LayoutModifier, you can provide custom alignment lines so that other parent composables can use them to align and position their children accordingly.

The following example shows a custom BarChart composable that exposes two alignment lines, MaxChartValue and MinChartValue, so that other composables can align to the maximum and minimum data value of the chart. Two text elements, Max and Min, have been aligned to the center of the custom alignment lines.

Custom alignment lines are defined as top level variables in your project.

/** * AlignmentLine defined by the maximum data value in a [BarChart] */ private val MaxChartValue = HorizontalAlignmentLine(merger = { old, new -> min(old, new) }) /** * AlignmentLine defined by the minimum data value in a [BarChart] */ private val MinChartValue = HorizontalAlignmentLine(merger = { old, new -> max(old, new) })AlignmentLinesSnippets.kt

The custom alignment lines to create our example are of type HorizontalAlignmentLine, as they're used to align children vertically. A merge policy is passed as a parameter in case multiple layouts provide a value for these alignment lines. As the Compose layout system coordinates and the Canvas coordinates represent [0, 0], the top left corner and the x and y axis are positive downwards, so the MaxChartValue value will always be smaller than MinChartValue. Therefore, the merger policy is min for the maximum chart data value baseline, and max for the minimum chart data value baseline.

When creating a custom Layout or LayoutModifier, specify custom alignment lines in the MeasureScope.layout method, which takes an alignmentLines: Map<AlignmentLine, Int> parameter.

@Composable private fun BarChart( dataPoints: List<Int>, modifier: Modifier = Modifier, ) { val maxValue: Float = remember(dataPoints) { dataPoints.maxOrNull()!! * 1.2f } BoxWithConstraints(modifier = modifier) { val density = LocalDensity.current with(density) { // ... // Calculate baselines val maxYBaseline = // ... val minYBaseline = // ... Layout( content = {}, modifier = Modifier.drawBehind { // ... } ) { _, constraints -> with(constraints) { layout( width = if (hasBoundedWidth) maxWidth else minWidth, height = if (hasBoundedHeight) maxHeight else minHeight, // Custom AlignmentLines are set here. These are propagated // to direct and indirect parent composables. alignmentLines = mapOf( MinChartValue to minYBaseline.roundToInt(), MaxChartValue to maxYBaseline.roundToInt() ) ) {} } } } } }AlignmentLinesSnippets.kt

Direct and indirect parents of this composable can consume the alignment lines. The following composable creates a custom layout that takes as a parameter two Text slots and data points, and aligns the two texts to the maximum and minimum chart data values. The preview of this composable is what's shown in Figure 2.

@Composable private fun BarChartMinMax( dataPoints: List<Int>, maxText: @Composable () -> Unit, minText: @Composable () -> Unit, modifier: Modifier = Modifier, ) { Layout( content = { maxText() minText() // Set a fixed size to make the example easier to follow BarChart(dataPoints, Modifier.size(200.dp)) }, modifier = modifier ) { measurables, constraints -> check(measurables.size == 3) val placeables = measurables.map { it.measure(constraints.copy(minWidth = 0, minHeight = 0)) } val maxTextPlaceable = placeables[0] val minTextPlaceable = placeables[1] val barChartPlaceable = placeables[2] // Obtain the alignment lines from BarChart to position the Text val minValueBaseline = barChartPlaceable[MinChartValue] val maxValueBaseline = barChartPlaceable[MaxChartValue] layout(constraints.maxWidth, constraints.maxHeight) { maxTextPlaceable.placeRelative( x = 0, y = maxValueBaseline - (maxTextPlaceable.height / 2) ) minTextPlaceable.placeRelative( x = 0, y = minValueBaseline - (minTextPlaceable.height / 2) ) barChartPlaceable.placeRelative( x = max(maxTextPlaceable.width, minTextPlaceable.width) + 20, y = 0 ) } } } @Preview @Composable private fun ChartDataPreview() { MaterialTheme { BarChartMinMax( dataPoints = listOf(4, 24, 15), maxText = { Text("Max") }, minText = { Text("Min") }, modifier = Modifier.padding(24.dp) ) } }AlignmentLinesSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Filter a list while typing Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/filter-list-while-typing

**Contents:**
- Filter a list while typing Stay organized with collections Save and categorize content based on your preferences.
- Version compatibility
  - Dependencies
- Filter a list based on text input
  - Key points about the code
  - Key points about the code
  - Result
- Collections that contain this guide
  - Request user input
- Have questions or feedback

This guide explains how to filter through a list of strings based on text input in Jetpack Compose. Use this approach to dynamically update a list based on user search queries.

This implementation works with Compose versions 1.2.0 and higher.

Include the following dependencies in your build.gradle:

Together, the following snippets produce a list that updates in real time as the user types. This example uses a ViewModel to hold the list data and filtering logic, while the FilterTextView() function creates the UI that updates automatically whenever the filter text changes.

class FilterTextViewModel : ViewModel() { private val items = listOf( "Cupcake", "Donut", "Eclair", "Froyo", "Gingerbread", "Honeycomb", "Ice Cream Sandwich" ) private val _filteredItems = MutableStateFlow(items) var filteredItems: StateFlow<List<String>> = _filteredItems fun filterText(input: String) { // This filter returns the full items list when input is an empty string. _filteredItems.value = items.filter { it.contains(input, ignoreCase = true) } } }FilterText.kt

@Composable fun FilterTextView(modifier: Modifier = Modifier, viewModel: FilterTextViewModel = viewModel()) { val filteredItems by viewModel.filteredItems.collectAsStateWithLifecycle() var text by rememberSaveable { mutableStateOf("") } Column( modifier = Modifier .fillMaxSize() .padding(all = 10.dp) ) { OutlinedTextField( value = text, onValueChange = { text = it viewModel.filterText(text) }, label = { Text("Filter Text") }, modifier = Modifier.fillMaxWidth() ) LazyColumn { items( count = filteredItems.size, key = { index -> filteredItems[index] } ) { ListItem( headlineContent = { Text(filteredItems[it]) }, modifier = Modifier .fillParentMaxWidth() .padding(10.dp) ) } } } }FilterText.kt

Figure 1. A filtered list that updates as new text is entered.

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/collections/display-interactive-components

**Contents:**
- Quick guides collectionDisplay interactive components
  - Create a scaffold component to hold the UI together
  - Display an app bar
  - Create a button
  - Create a floating action button (FAB
  - Create a card as a container
  - Create a chip to represent complex entities
  - Display pop-up messages or requests for user input
  - Create a progress indicator
  - Create a slider for a range of values

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Migrate Jetpack Navigation to Navigation Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/migration-scenarios/navigation

**Contents:**
- Migrate Jetpack Navigation to Navigation Compose Stay organized with collections Save and categorize content based on your preferences.
- Migration prerequisites
- Migration steps
- Common use cases
  - Retrieve complex data when navigating
- Limitations
  - Incremental migration to Navigation Compose
  - Transition animations
- Learn more
- Recommended for you

The Navigation Compose API lets you navigate between composables in a Compose app, while taking advantage of the Jetpack Navigation's component, infrastructure, and features.

This page describes how to migrate from a Fragment-based Jetpack Navigation to Navigation Compose, as part of the larger, View-based UI migration to Jetpack Compose.

You can migrate to Navigation Compose once you're able to replace all of your Fragments with corresponding screen composables. Screen composables can contain a mix of Compose and View content, but all navigation destinations must be composables to enable Navigation Compose migration. Until then, you should continue using Fragment-based Navigation component in your interop View and Compose codebase. See the navigation interop documentation for more information.

Using Navigation Compose in a Compose-only app is not a prerequisite. You can continue using Fragment-based Navigation component, as long as you keep Fragments for hosting your composable content.

Whether you are following our recommended migration strategy or taking another approach, you'll reach a point where all navigation destinations are screen composables, with Fragments acting only as composable containers. At this stage, you can migrate to Navigation Compose.

If your app is already following a UDF design pattern and our guide to architecture, migrating to Jetpack Compose and Navigation Compose shouldn't require major refactors of other layers of your app, apart from the UI layer.

To migrate to Navigation Compose, follow these steps:

Create an App-level composable and add it to your Activity as your Compose entry point, replacing the setup of the View layout:

class SampleActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) // setContentView<ActivitySampleBinding>(this, R.layout.activity_sample) setContent { SampleApp(/* ... */) } } }MigrationCommonScenariosSnippets.kt

Create types for each navigation destination. Use a data object for destinations which don't require any data and data class or class for destinations which require data.

@Serializable data object First @Serializable data class Second(val id: String) @Serializable data object Third MigrationCommonScenariosSnippets.kt

Set up the NavController in a place where all composables that need to reference it have access to it (this is usually inside your App composable). This approach follows the principles of state hoisting and lets you use the NavController as the source of truth for navigating between composable screens and maintaining the back stack:

@Composable fun SampleApp() { val navController = rememberNavController() // ... }MigrationCommonScenariosSnippets.kt

Create your app's NavHost inside the App composable and pass the navController:

@Composable fun SampleApp() { val navController = rememberNavController() SampleNavHost(navController = navController) } @Composable fun SampleNavHost( navController: NavHostController ) { NavHost(navController = navController, startDestination = First) { // ... } }MigrationCommonScenariosSnippets.kt

Add the composable destinations to build your navigation graph. If each screen has been previously migrated to Compose, this step only consists of extracting these screen composables from your Fragments into the composable destinations:

class FirstFragment : Fragment() { override fun onCreateView( inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle? ): View { return ComposeView(requireContext()).apply { setContent { // FirstScreen(...) EXTRACT FROM HERE } } } } @Composable fun SampleNavHost( navController: NavHostController ) { NavHost(navController = navController, startDestination = First) { composable<First> { FirstScreen(/* ... */) // EXTRACT TO HERE } composable<Second> { SecondScreen(/* ... */) } // ... } }MigrationCommonScenariosSnippets.kt

If you followed the guidance on architecting your Compose UI, specifically how ViewModels and navigation events should be passed to composables, the next step is to change how you provide the ViewModel to each screen composable. You can often use Hilt injection and its integration point with Compose and Navigation via hiltViewModel:

@Composable fun FirstScreen( // viewModel: FirstViewModel = viewModel(), viewModel: FirstViewModel = hiltViewModel(), onButtonClick: () -> Unit = {}, ) { // ... }MigrationCommonScenariosSnippets.kt

Replace all findNavController() navigation calls with the navController ones and pass these as navigation events to each composable screen, rather than passing the entire navController. This approach follows the best practices of exposing events from composable functions to callers and keeps the navController as the single source of truth.

Data can be passed to a destination by creating an instance of the route class defined for that destination. It can then be obtained either directly from the back stack entry at the destination or from a ViewModel using SavedStateHandle.toRoute().

@Composable fun SampleNavHost( navController: NavHostController ) { NavHost(navController = navController, startDestination = First) { composable<First> { FirstScreen( onButtonClick = { // findNavController().navigate(firstScreenToSecondScreenAction) navController.navigate(Second(id = "ABC")) } ) } composable<Second> { backStackEntry -> val secondRoute = backStackEntry.toRoute<Second>() SecondScreen( id = secondRoute.id, onIconClick = { // findNavController().navigate(secondScreenToThirdScreenAction) navController.navigate(Third) } ) } // ... } }MigrationCommonScenariosSnippets.kt

Remove all Fragments, relevant XML layouts, unnecessary navigation and other resources, and stale Fragment and Jetpack Navigation dependencies.

You can find the same steps with more Navigation Compose-related details in the Setup documentation.

No matter which Navigation component you're using, the same principles of navigation apply.

Common use cases when migrating include the following:

For more detailed information about these use cases, see Navigating with Compose.

We strongly recommend not passing around complex data objects when navigating. Instead, pass the minimum necessary information, such as a unique identifier or other form of ID, as arguments when performing navigation actions. You should store complex objects as data in a single source of truth, such as the data layer. For more information, see Retrieving complex data when navigating.

If your Fragments are passing complex objects as arguments, consider refactoring your code first, in a way that allows storing and fetching these objects from the data layer. See the Now in Android repository for examples.

This section describes current limitations for Navigation Compose.

Currently, you cannot use Navigation Compose while still using Fragments as destinations in your code. To start using Navigation Compose, all of your destinations need to be composables. You can track this feature request on the Issue Tracker.

Starting with Navigation 2.7.0-alpha01, support for setting custom transitions, previously from AnimatedNavHost, is now directly supported in NavHost. Read through the release notes for more information.

For more information about migrating to Navigation Compose, see the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## About cutouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/cutouts

**Contents:**
- About cutouts Stay organized with collections Save and categorize content based on your preferences.
- Default case
- Handle cutout information manually
- Recommended for you

A display cutout is an area on some devices that extends into the display surface. It allows for an edge-to-edge experience while providing space for important sensors on the front of the device.

Android supports display cutouts on devices running Android 9 (API level 28) and higher. However, device manufacturers can also support display cutouts on devices running Android 8.1 or lower.

This page describes how to implement support for devices with cutouts in Compose, including how to work with the cutout area— that is, the edge-to-edge rectangle on the display surface that contains the cutout.

Apps targeting API level 34 or lower, or Activities that don't call enableEdgeToEdge, won't draw into the cutout region by default unless the app draws into a system bar containing the display cutout.

Apps targeting API level 35 or higher on devices running Android 15 or higher, or Activities that call enableEdgeToEdge, draw into the cutout region.

In other words, LAYOUT_IN_DISPLAY_CUTOUT_MODE_DEFAULT, LAYOUT_IN_DISPLAY_CUTOUT_MODE_SHORT_EDGES, and LAYOUT_IN_DISPLAY_CUTOUT_MODE_NEVER are interpreted as LAYOUT_IN_DISPLAY_CUTOUT_MODE_ALWAYS for non-floating windows in apps targeting API level 35 or higher on devices running Android 15 or higher.

You must handle cutout information to prevent the cutout area from obscuring important text, controls, or interactive elements requiring fine-touch recognition (touch sensitivity may be lower in the cutout area). While handling cutouts, don't hardcode the status bar height, as this can lead to overlapping or cut-off content. Instead, handle cutouts in any of the following ways:

Using WindowInsets.displayCutout, WindowInsets.safeContent, or WindowInsets.safeDrawing

Accessing the cutout Path object with LocalView.current.rootWindowInsets.displayCutout

For Compose, we recommend that you use displayCutout, safeContent, or safeDrawing to handle cutout insets in your composables. This approach lets you respect the display cutout padding where required, or ignore it where it is not required.

Canvas(modifier = Modifier.fillMaxSize().windowInsetsPadding(WindowInsets.displayCutout)) { drawRect(Color.Red, style = Stroke(2.dp.toPx())) }CutoutSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Track metrics for your widget Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/metrics

**Contents:**
- Track metrics for your widget Stay organized with collections Save and categorize content based on your preferences.

Android 16 includes additional metrics APIs that are more granular. These metrics track tap actions such as a button click, scroll, impressions, and the widget's size and position.

The main API is AppWidgetEvent. Use WorkManager to create a periodic worker that captures widget engagement, whether daily, weekly, or whenever your app is opened.

See the following snippet for an example of tracking clicks, scrolls and impression length.

@RequiresApi(Build.VERSION_CODES_FULL.BAKLAVA_1) fun getWidgetEngagementMetrics(context: Context) { val manager = AppWidgetManager.getInstance(context) val endTime = System.currentTimeMillis() val startTime = endTime - (24 * 60 * 60 * 1000) // a day ago val events = manager.queryAppWidgetEvents(startTime, endTime) if (events.isEmpty()) { Log.d(TAG, "No events found for the given time range.") } val metrics = hashMapOf( "clicks" to 0L, "scrolls" to 0L, "totalImpressionLength" to 0L ) for (event in events) { Log.d(TAG, "Event Start: ${event.start}") Log.d(TAG, "Event End: ${event.end}") val widgetId = event.appWidgetId // Tap actions val clickedIds = event.clickedIds if (clickedIds?.isNotEmpty() == true) { metrics["clicks"] = metrics.getValue("clicks") + clickedIds.size // Log or analyze which components were clicked. for (id in clickedIds) { Log.d(TAG, "Widget $widgetId: Tap event on component with ID $id") } } // Scroll events val scrolledIds = event.scrolledIds if (scrolledIds?.isNotEmpty() == true) { metrics["scrolls"] = metrics.getValue("scrolls") + scrolledIds.size // Log or analyze which lists were scrolled. for (id in scrolledIds) { Log.d(TAG, "Widget $widgetId: Scroll event in list with ID/tag $id") } } // Impressions metrics["totalImpressionLength"] = metrics.getValue("totalImpressionLength") + event.visibleDuration.toMillis() Log.d( TAG, "Widget $widgetId: Impression event with duration " + event.visibleDuration.toMillis() + "ms" ) // Position val position = event.position if (position != null) { Log.d( TAG, "Widget $widgetId: left=${position.left}, right=${position.right}, top=${position.top}, bottom=${position.bottom}" ) } } Log.d("WidgetMetrics", "Metrics: $metrics") }GlanceMetrics.kt

To preserve system health, events are reported once an hour by default although device manufacturers may change the reporting window. For example, on Pixel devices if a user scrolls the same list in your widget 10 times in an hour, only 1 scroll event will be counted for that hour.

For testing, you can set the following attribute to a specified time and restart your test device. In the following example, the report window is set to 0 ms and events are reported immediately.

In order to set a custom tag for reporting clicks and scrolls, you can use RemoteViews.setAppWidgetEventTag on a view within your RemoteViews layout. This integer tag is used when you query for AppWidgetEvents that include clicks or scrolls on this view.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (unknown):
```unknown
adb shell device_config override systemui widget_events_report_interval_ms 0
```

---

## About Predictive back Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/predictive-back

**Contents:**
- About Predictive back Stay organized with collections Save and categorize content based on your preferences.

Predictive back, a gesture navigation feature, lets users preview where the back swipe takes them. Predictive back seamlessly integrates with Compose to enhance your app's navigation experience. Users enjoy smoother and more intuitive transitions when navigating back within your app. Figure 1 shows how this works in the SociaLite sample app.

See our guides to do the following with predictive back:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Other considerations Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/other-considerations

**Contents:**
- Other considerations Stay organized with collections Save and categorize content based on your preferences.
- Migrating your app's theme
- Navigation
- Test your mixed Compose/Views UI
- Integrating Compose with your existing app architecture
  - Using a ViewModel in Compose
  - State source of truth
  - Compose as the source of truth
  - View system as the source of truth
- Migrating shared UI

While migrating from Views to Compose is purely UI-related, there are a lot of things to take into account to perform a safe and incremental migration. This page contains some considerations while migrating your View-based app to Compose.

Material Design is the recommended design system for theming Android apps.

For View-based apps, there are three versions of Material available:

For Compose apps, there are two versions of Material available:

We recommend using the latest version (Material 3) if your app's design system is in a position to do so. There are migration guides available for both Views and Compose:

When creating new screens in Compose, regardless of which version of Material Design you're using, ensure that you apply a MaterialTheme before any composables that emit UI from the Compose Material libraries. The Material components (Button, Text, etc.) depend on a MaterialTheme being in place and their behaviour is undefined without it.

All Jetpack Compose samples use a custom Compose theme built on top of MaterialTheme.

See Design systems in Compose and Migrating XML themes to Compose to learn more.

If you use the Navigation component in your app, see the Navigating with Compose - Interoperability and Migrate Jetpack Navigation to Navigation Compose for more information.

After migrating parts of your app to Compose, testing is critical to make sure you haven't broken anything.

When an activity or fragment uses Compose, you need to use createAndroidComposeRule instead of using ActivityScenarioRule. createAndroidComposeRule integrates ActivityScenarioRule with a ComposeTestRule that lets you test Compose and View code at the same time.

class MyActivityTest { @Rule @JvmField val composeTestRule = createAndroidComposeRule<MyActivity>() @Test fun testGreeting() { val greeting = InstrumentationRegistry.getInstrumentation() .targetContext.resources.getString(R.string.greeting) composeTestRule.onNodeWithText(greeting).assertIsDisplayed() } }AddingComposeToYourAppSnippets.kt

See Testing your Compose layout to learn more about testing. For interoperability with UI testing frameworks, see interoperability with Espresso and interoperability with UiAutomator.

Unidirectional Data Flow (UDF) architecture patterns work seamlessly with Compose. If the app uses other types of architecture patterns instead, like Model View Presenter (MVP), we recommend you migrate that part of the UI to UDF before or whilst adopting Compose.

If you use the Architecture Components ViewModel library, you can access a ViewModel from any composable by calling the viewModel() function, as explained in Compose and other libraries.

When adopting Compose, be careful about using the same ViewModel type in different composables as ViewModel elements follow View-lifecycle scopes. The scope will be either the host activity, fragment, or the navigation graph if the Navigation library is used.

For example, if the composables are hosted in an activity, viewModel() always returns the same instance that is only cleared when the activity finishes. In the following example, the same user ("user1") is greeted twice because the same GreetingViewModel instance is reused in all composables under the host activity. The first ViewModel instance created is reused in other composables.

class GreetingActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContent { MaterialTheme { Column { GreetingScreen("user1") GreetingScreen("user2") } } } } } @Composable fun GreetingScreen( userId: String, viewModel: GreetingViewModel = viewModel( factory = GreetingViewModelFactory(userId) ) ) { val messageUser by viewModel.message.observeAsState("") Text(messageUser) } class GreetingViewModel(private val userId: String) : ViewModel() { private val _message = MutableLiveData("Hi $userId") val message: LiveData<String> = _message } class GreetingViewModelFactory(private val userId: String) : ViewModelProvider.Factory { @Suppress("UNCHECKED_CAST") override fun <T : ViewModel> create(modelClass: Class<T>): T { return GreetingViewModel(userId) as T } }ComposeExistingArchSnippets.kt

As navigation graphs also scope ViewModel elements, composables that are a destination in a navigation graph have a different instance of the ViewModel. In this case, the ViewModel is scoped to the lifecycle of the destination, and it is cleared when the destination is removed from the backstack. In the following example, when the user navigates to the Profile screen, a new instance of GreetingViewModel is created.

@Composable fun MyApp() { NavHost(rememberNavController(), startDestination = "profile/{userId}") { /* ... */ composable("profile/{userId}") { backStackEntry -> GreetingScreen(backStackEntry.arguments?.getString("userId") ?: "") } } }ComposeExistingArchSnippets.kt

When you adopt Compose in one part of the UI, it's possible that Compose and the View system code need to share data. When possible, we recommend you encapsulate that shared state in another class that follows UDF best practices used by both platforms; for example, in a ViewModel that exposes a stream of the shared data to emit data updates.

However, that's not always possible if the data to be shared is mutable or is tightly bound to a UI element. In that case, one system must be the source of truth, and that system needs to share any data updates to the other system. As a general rule of thumb, the source of truth should be owned by whichever element is closer to the root of the UI hierarchy.

Use the SideEffect composable to publish Compose state to non-Compose code. In this case, the source of truth is kept in a composable, which sends state updates.

As an example, your analytics library might allow you to segment your user population by attaching custom metadata (user properties in this example) to all subsequent analytics events. To communicate the user type of the current user to your analytics library, use SideEffect to update its value.

@Composable fun rememberFirebaseAnalytics(user: User): FirebaseAnalytics { val analytics: FirebaseAnalytics = remember { FirebaseAnalytics() } // On every successful composition, update FirebaseAnalytics with // the userType from the current User, ensuring that future analytics // events have this metadata attached SideEffect { analytics.setUserProperty("userType", user.userType) } return analytics }MigrationOtherConsiderationsSnippets.kt

For more information, see Side-effects in Compose.

If the View system owns the state and shares it with Compose, we recommend that you wrap the state in mutableStateOf objects to make it thread-safe for Compose. If you use this approach, composable functions are simplified because they no longer have the source of truth, but the View system needs to update the mutable state and the Views that use that state.

In the following example, a CustomViewGroup contains a TextView and a ComposeView with a TextField composable inside. The TextView needs to show the content of what the user types in the TextField.

class CustomViewGroup @JvmOverloads constructor( context: Context, attrs: AttributeSet? = null, defStyle: Int = 0 ) : LinearLayout(context, attrs, defStyle) { // Source of truth in the View system as mutableStateOf // to make it thread-safe for Compose private var text by mutableStateOf("") private val textView: TextView init { orientation = VERTICAL textView = TextView(context) val composeView = ComposeView(context).apply { setContent { MaterialTheme { TextField(value = text, onValueChange = { updateState(it) }) } } } addView(textView) addView(composeView) } // Update both the source of truth and the TextView private fun updateState(newValue: String) { text = newValue textView.text = newValue } }ComposeExistingArchSnippets.kt

If you are migrating gradually to Compose, you might need to use shared UI elements in both Compose and the View system. For example, if your app has a custom CallToActionButton component, you might need to use it in both Compose and View-based screens.

In Compose, shared UI elements become composables that can be reused across the app, regardless of the element being styled using XML or being a custom view. For example, you'd create a CallToActionButton composable for your custom call to action Button component.

To use the composable in View-based screens, create a custom view wrapper that extends from AbstractComposeView. In its overridden Content composable, place the composable you created wrapped in your Compose theme, as shown in the example below:

@Composable fun CallToActionButton( text: String, onClick: () -> Unit, modifier: Modifier = Modifier, ) { Button( colors = ButtonDefaults.buttonColors( containerColor = MaterialTheme.colorScheme.secondary ), onClick = onClick, modifier = modifier, ) { Text(text) } } class CallToActionViewButton @JvmOverloads constructor( context: Context, attrs: AttributeSet? = null, defStyle: Int = 0 ) : AbstractComposeView(context, attrs, defStyle) { var text by mutableStateOf("") var onClick by mutableStateOf({}) @Composable override fun Content() { YourAppTheme { CallToActionButton(text, onClick) } } }ComposeExistingUISnippets.kt

Notice that the composable parameters become mutable variables inside the custom view. This makes the custom CallToActionViewButton view inflatable and usable, like a traditional view. See an example of this with View Binding below:

class ViewBindingActivity : ComponentActivity() { private lateinit var binding: ActivityExampleBinding override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) binding = ActivityExampleBinding.inflate(layoutInflater) setContentView(binding.root) binding.callToAction.apply { text = getString(R.string.greeting) onClick = { /* Do something */ } } } }ComposeExistingUISnippets.kt

If the custom component contains mutable state, see State source of truth.

Traditionally, a View is stateful. A View manages fields that describe what to display, in addition to how to display it. When you convert a View to Compose, look to separate the data being rendered to achieve a unidirectional data flow, as explained further in state hoisting.

For example, a View has a visibility property that describes if it is visible, invisible, or gone. This is an inherent property of the View. While other pieces of code may change the visibility of a View, only the View itself really knows what its current visibility is. The logic for ensuring that a View is visible can be error prone, and is often tied to the View itself.

By contrast, Compose makes it easy to display entirely different composables using conditional logic in Kotlin:

@Composable fun MyComposable(showCautionIcon: Boolean) { if (showCautionIcon) { CautionIcon(/* ... */) } }MigrationOtherConsiderationsSnippets.kt

By design, CautionIcon doesn’t need to know or care why it is being displayed, and there is no concept of visibility: it either is in the Composition, or it isn’t.

By cleanly separating state management and presentation logic, you can more freely change how you display content as a conversion of state to UI. Being able to hoist state when needed also makes composables more reusable, since state ownership is more flexible.

View elements often have some idea of where they live: inside an Activity, a Dialog, a Fragment or somewhere inside another View hierarchy. Because they are often inflated from static layout files, the overall structure of a View tends to be very rigid. This results in tighter coupling, and makes it harder for a View to be changed or reused.

For example, a custom View might assume that it has a child view of a certain type with a certain id, and change its properties directly in response to some action. This tightly couples those View elements together: the custom View may crash or be broken if it can’t find the child, and the child likely can’t be reused without the custom View parent.

This is less of a problem in Compose with reusable composables. Parents can easily specify state and callbacks, so you can write reusable composables without having to know the exact place where they'll be used.

@Composable fun AScreen() { var isEnabled by rememberSaveable { mutableStateOf(false) } Column { ImageWithEnabledOverlay(isEnabled) ControlPanelWithToggle( isEnabled = isEnabled, onEnabledChanged = { isEnabled = it } ) } }MigrationOtherConsiderationsSnippets.kt

In the example above, all three parts are more encapsulated and less coupled:

ImageWithEnabledOverlay only needs to know what the current isEnabled state is. It doesn’t need to know that ControlPanelWithToggle exists, or even how it is controllable.

ControlPanelWithToggle doesn’t know that ImageWithEnabledOverlay exists. There could be zero, one, or more ways that isEnabled is displayed, and ControlPanelWithToggle wouldn’t have to change.

To the parent, it doesn’t matter how deeply nested ImageWithEnabledOverlay or ControlPanelWithToggle are. Those children could be animating changes, swapping out content, or passing content on to other children.

This pattern is known as the inversion of control, which you can read more about in the CompositionLocal documentation.

Having different resources for different window sizes is one of the main ways to create responsive View layouts. While qualified resources are still an option for screen-level layout decisions, Compose makes it much easier to change layouts entirely in code with normal conditional logic. See Use window size classes to learn more.

Additionally, refer to Support different display sizes to learn about the techniques Compose offers to build adaptive UIs.

For more information on how to enable nested scrolling interop between scrollable View elements and scrollable composables, nested in both directions, read through Nested scrolling interop.

Composables in RecyclerView are performant since RecyclerView version 1.3.0-alpha02. Make sure you are on at least version 1.3.0-alpha02 of RecyclerView to see those benefits.

You may need to override default insets when your screen has both Views and Compose code in the same hierarchy. In this case, you need to be explicit in which one should consume the insets, and which one should ignore them.

For example, if your outermost layout is an Android View layout, you should consume the insets in the View system and ignore them for Compose. Alternatively, if your outermost layout is a composable, you should consume the insets in Compose, and pad the AndroidView composables accordingly.

By default, each ComposeView consumes all insets at the WindowInsetsCompat level of consumption. To change this default behavior, set ComposeView.consumeWindowInsets to false.

For more information, read the WindowInsets in Compose documentation.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Pin Glance widgets in-app Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/pin-in-app

**Contents:**
- Pin Glance widgets in-app Stay organized with collections Save and categorize content based on your preferences.
- Create a Pin Request
- Handle the Pin Request Response

With Android 8.0 (API level 26) and higher, you can let users pin your widgets to their home screen within your app. Promoting widgets directly within your app is a great way to increase user engagement, especially after a user completes a related task, or when a user repeatedly accesses a feature in your app.

To initiate widget pinning, use the requestPinGlanceAppWidget method from the GlanceAppWidgetManager class. For apps running on lower versions of Android, this returns false. However if the request is successfully sent to the system, this returns true.

Here is an example of how you can create a pin request:

@Composable fun AnInAppComposable() { val context = LocalContext.current val coroutineScope = rememberCoroutineScope() Button( onClick = { coroutineScope.launch { GlanceAppWidgetManager(context).requestPinGlanceAppWidget( receiver = MyWidgetReceiver::class.java, preview = MyWidget(), previewState = DpSize(245.dp, 115.dp) ) } } ) {} }GlancePinAppWidget.kt

In this example, MyWidgetReceiver is the class that receives the widget's callbacks, and MyWidget is the Glance widget you want to pin. The successCallback is a PendingIntent that is triggered when the widget is successfully pinned.

When a user responds to the pin request dialog, your app receives a response. If the user accepts the request, your widget is pinned to their home screen, and the successCallback PendingIntent is triggered. If the user denies the request, nothing happens.

It is important to note that the successCallback is only triggered if the widget is successfully added to the home screen. It is not triggered if the user denies the request or if the launcher does not support pinning.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Set up window insets Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/insets-ui

**Contents:**
- Set up window insets Stay organized with collections Save and categorize content based on your preferences.
- Handle insets using padding or size modifiers
  - Padding modifiers
  - Inset size modifiers
- Inset consumption
- Insets and Jetpack Compose phases

Once your Activity has taken control of handling all insets, you can use Compose APIs to verify that content isn't obscured and interactable elements don't overlap with the system UI. These APIs also synchronize your app's layout with inset changes.

For example, this is the most basic method of applying the insets to the content of your entire app:

override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) enableEdgeToEdge() setContent { Box(Modifier.safeDrawingPadding()) { // the rest of the app } } }InsetsSnippets.kt

This snippet applies the safeDrawing window insets as padding around the entire content of the app. While this ensures that interactable elements don't overlap with the system UI, it also means that none of the app will draw behind the system UI to achieve an edge-to-edge effect. To make full use of the entire window, you need to fine-tune where the insets are applied on a screen-by-screen or component-by-component basis.

All of these inset types are animated automatically with IME animations backported to API 21. By extension, all of your layouts using these insets are also automatically animated as the inset values change.

There are three ways to handle insets to adjust your Composable layouts:

Modifier.windowInsetsPadding(windowInsets: WindowInsets) applies the given window insets as padding, acting just like Modifier.padding would. For example, Modifier.windowInsetsPadding(WindowInsets.safeDrawing) applies the safe drawing insets as padding on all 4 sides.

There are also several built-in utility methods for the most common inset types. Modifier.safeDrawingPadding() is one such method, equivalent to Modifier.windowInsetsPadding(WindowInsets.safeDrawing). There are analogous modifiers for the other inset types.

The following modifiers apply an amount of window insets by setting the size of the component to be the size of the insets:

Modifier.windowInsetsStartWidth(windowInsets: WindowInsets)

Applies the start side of windowInsets as the width (like Modifier.width)

Modifier.windowInsetsEndWidth(windowInsets: WindowInsets)

Applies the end side of windowInsets as the width (like Modifier.width)

Modifier.windowInsetsTopHeight(windowInsets: WindowInsets)

Applies the top side of windowInsets as the height (like Modifier.height)

Modifier.windowInsetsBottomHeight(windowInsets: WindowInsets)

Applies the bottom side of windowInsets as the height (like Modifier.height)

These modifiers are especially useful for sizing a Spacer that takes up the space of insets:

LazyColumn( Modifier.imePadding() ) { // Other content item { Spacer( Modifier.windowInsetsBottomHeight( WindowInsets.systemBars ) ) } }InsetsSnippets.kt

The inset padding modifiers (windowInsetsPadding and helpers like safeDrawingPadding) automatically consume the portion of the insets that are applied as padding. While going deeper into the composition tree, nested inset padding modifiers and the inset size modifiers know that some portion of the insets have already been consumed by outer inset padding modifiers, and avoid using the same portion of the insets more than once which would result in too much extra space.

Inset size modifiers also avoid using the same portion of insets more than once if insets have already been consumed. However, since they are changing their size directly, they don't consume insets themselves.

As a result, nesting padding modifiers automatically change the amount of padding applied to each composable.

Looking at the same LazyColumn example as before, the LazyColumn is being resized by the imePadding modifier. Inside the LazyColumn, the last item is sized to be the height of the bottom of the system bars:

LazyColumn( Modifier.imePadding() ) { // Other content item { Spacer( Modifier.windowInsetsBottomHeight( WindowInsets.systemBars ) ) } }InsetsSnippets.kt

When the IME is closed, the imePadding() modifier applies no padding, since the IME has no height. Since the imePadding() modifier is applying no padding, no insets are being consumed, and the height of the Spacer will be the size of the bottom side of the system bars.

When the IME opens, the IME insets animate to match the size of the IME, and the imePadding() modifier begins applying bottom padding to resize the LazyColumn as the IME opens. As the imePadding() modifier begins applying bottom padding, it also starts consuming that amount of insets. Therefore, the height of the Spacer starts to decrease, as part of the spacing for the system bars has already been applied by the imePadding() modifier. Once the imePadding() modifier is applying an amount of bottom padding that is larger than the system bars, the height of the Spacer is zero.

When the IME closes, the changes happen in reverse: The Spacer starts to expand from a height of zero once the imePadding() is applying less than the bottom side of the system bars, until finally the Spacer matches the height of the bottom side of the system bars once the IME is completely animated out.

This behavior is accomplished through communication between all windowInsetsPadding modifiers, and can be influenced in a couple of other ways.

Modifier.consumeWindowInsets(insets: WindowInsets) also consumes insets in the same way as Modifier.windowInsetsPadding, but it doesn't apply the consumed insets as padding. This is useful in combination with the inset size modifiers, to indicate to siblings that a certain amount of insets have already been consumed:

Column(Modifier.verticalScroll(rememberScrollState())) { Spacer(Modifier.windowInsetsTopHeight(WindowInsets.systemBars)) Column( Modifier.consumeWindowInsets( WindowInsets.systemBars.only(WindowInsetsSides.Vertical) ) ) { // content Spacer(Modifier.windowInsetsBottomHeight(WindowInsets.ime)) } Spacer(Modifier.windowInsetsBottomHeight(WindowInsets.systemBars)) }InsetsSnippets.kt

Modifier.consumeWindowInsets(paddingValues: PaddingValues) behaves very similarly to the version with a WindowInsets argument, but takes an arbitrary PaddingValues to consume. This is useful for informing children when padding or spacing is provided by some other mechanism than the inset padding modifiers, such as an ordinary Modifier.padding or fixed height spacers:

Column(Modifier.padding(16.dp).consumeWindowInsets(PaddingValues(16.dp))) { // content Spacer(Modifier.windowInsetsBottomHeight(WindowInsets.ime)) }InsetsSnippets.kt

In cases where the raw window insets are needed without consumption, use the WindowInsets values directly, or use WindowInsets.asPaddingValues() to return a PaddingValues of the insets that are unaffected by consumption. However, due to the following caveats, prefer to use the window insets padding modifiers and window insets size modifiers wherever possible.

Compose uses the underlying AndroidX core APIs to update and animate insets, which use the underlying platform APIs managing insets. Because of that platform behavior, insets have a special relationship with the phases of Jetpack Compose.

The value of insets are updated after the composition phase, but before the layout phase. This means that reading the value of insets in composition generally uses a value of the insets that is one frame late. The built-in modifiers described on this page are built to delay using the values of the insets until the layout phase, which ensures that the inset values are used on the same frame as they are updated.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Build adaptive apps Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/build-adaptive-apps

**Contents:**
- Build adaptive apps Stay organized with collections Save and categorize content based on your preferences.
- What are adaptive apps?
- Why build adaptive UIs?
  - Google Play
- How to get started
  - Window size classes
  - Content panes
  - Compose Material 3 Adaptive
  - Configuration and continuity
    - Posture

More than 300 million Android large screen devices, including tablets, foldables, ChromeOS devices, car displays, and TVs, are in use today, with more coming continually. To provide an optimal user experience on the growing number and diversity of large screen devices—as well as on standard phones—build adaptive apps.

Adaptive apps change layouts based on changes in the app display, primarily the size of the app window. But adaptive apps also accommodate changes in the posture of foldable devices, such as tabletop or book posture, and changes in screen density and font size.

Instead of just stretching or shrinking UI elements in response to different window sizes, adaptive apps replace layout components and show or hide content. For example, on standard phones, an adaptive app might display a bottom navigation bar, but on large screens, a navigation rail. On large screens, adaptive apps display more content, such as a two-pane, list-detail layout; on small screens, less content, either the list or the detail.

In the past, apps typically ran full screen. Today, apps run in multi-window mode in arbitrarily sized windows independent of the device screen size. Users can change the window size at any time. So even on a single device type, apps must be adaptive.

Adaptive apps look great and work well on any device in any configuration.

Users expect your app to function flawlessly on all their devices and provide enhanced capabilities on large screens. Users multitask in multi-window mode for an enhanced app experience and increased productivity.

Apps limited to single-tasking on standard phones miss out on an expanding user base of diverse possibilities.

Google Play provides tablet- and foldable-specific app collections and recommendations, which enable users to discover high-quality apps.

Play ranks apps and games optimized for large screens higher than unoptimized apps. Play bases the ranking on the large screen app quality guidelines. Higher ranking increases discoverability by enabling multi‑device users to see large screen–specific ratings and reviews on their phones.

Apps that don't meet the Play Store's large screen quality standards display a warning on the app details page. The warning advises users that the app might not perform well on their large screen devices.

Build adaptive apps to broaden discoverability on Google Play and maximize the number of devices that can download your app.

Think about adaptive design in all phases of app development from planning to deployment. Inform graphic designers about adaptive design. Design your app to be adaptive, and you'll build an app that's manageable, extensible, and ready for future form factors and windowing modes.

To create an adaptive app that supports all display sizes and configurations, do the following:

App window dimensions can be different on different devices—or on the same device in the case of foldables—even when the app is full screen. Different device orientations produce different aspect ratios. In multi‑window mode, app window size, aspect ratio, and orientation can differ from that of the device screen.

Adaptive apps simplify and generalize the problem of determining and managing window size, aspect ratio, and orientation by considering only the app window when rendering layouts, which also works when the app window is the full screen.

Window size classes categorize app windows as compact, medium, or expanded based on window width or height.

Compute your app's WindowSizeClass using the currentWindowAdaptiveInfo() top‑level function of the Compose Material 3 Adaptive library. The function returns an instance of WindowAdaptiveInfo, which contains windowSizeClass. Your app receives updates whenever the window size class changes:

val windowSizeClass = currentWindowAdaptiveInfo().windowSizeClassAdaptiveLayoutSnippets.kt

An activity's layout is sometimes referred to as a screen. For example, your app might have a home screen, a list screen, and an item detail screen. The terminology implies that each activity fills the device screen.

However, on device screens that are large enough to support the expanded width window size class, multiple activity screens can be onscreen at the same time. Pane is a more precise term for content displays of individual activities.

Window size classes enable you to determine how many content panes to show in multi‑pane layouts, as specified in Material Design.

Panes are navigable. On compact and medium window size classes, apps display a single pane; and so, navigation to any destination displays one pane.

On the expanded window size class, apps can display related content in multiple panes, such as a list‑detail layout. Navigation to either pane displays the two‑pane layout. If the window size changes to compact or medium, adaptive apps show only one pane, the navigation destination, either the list or the detail.

Jetpack Compose is the modern, declarative approach to building adaptive apps without the duplication and maintenance burden of multiple layout files.

The Compose Material 3 Adaptive library contains composables that manage window size classes, navigation components, multi‑pane layouts, and foldable postures and hinge location, for example:

NavigationSuiteScaffold: Automatically switches between navigation bar and navigation rail depending on app window size class and device posture.

ListDetailPaneScaffold: Implements the list-detail canonical layout.

Adapts the layout to the app window size. Presents a list and the detail of a list item in side‑by‑side panes on the expanded window size class, but just the list or the detail on compact and medium window size classes.

SupportingPaneScaffold: Implements the supporting pane canonical layout.

Presents the main content pane and a supporting pane on the expanded window size class, but just the main content pane on compact and medium window size classes.

The Compose Material 3 Adaptive library is a key dependency for developing adaptive apps.

Adaptive apps retain continuity during configuration changes.

A configuration change occurs when the app window is resized, the posture of a foldable changes, or the screen density or font changes.

By default, configuration changes recreate the app activity, and all activity state is lost. To maintain continuity, adaptive apps save state in the activity's onSaveInstanceState() method or in a ViewModel.

Adaptive apps respond to changes in the posture of foldable devices. Postures include tabletop and book posture.

The WindowInfoTracker interface in Jetpack WindowManager lets you to obtain a list of DisplayFeature objects for the device. Among the display features is FoldingFeature.State, which indicates whether the device is fully or half open.

The Compose Material 3 Adaptive library provides the currentWindowAdaptiveInfo() top‑level function, which returns an instance of WindowAdaptiveInfo containing windowPosture.

Users often connect external keyboards, trackpads, mice, and styluses to large screen devices. The peripherals enhance user productivity, input precision, personal expression, and accessibility. Most ChromeOS devices come with keyboards and trackpads built in.

Adaptive apps support external input devices, but the Android framework does much of the work for you:

Jetpack Compose 1.7 and higher: Keyboard tab navigation and mouse or trackpad click, select, and scroll are supported by default.

Jetpack androidx.compose.material3 library: Enables users to write into any TextField component using a stylus.

Keyboard Shortcuts Helper: Makes Android platform and app keyboard shortcuts discoverable by users. Publish your app's keyboard shortcuts in Keyboard Shortcuts Helper by overriding the onProvideKeyboardShortcuts() window callback.

To fully support form factors of all sizes, adaptive apps support input of all types.

Test different screen and window sizes and different device configurations. Use host‑side screenshots and Compose previews to check your app layouts. Run your app on Android Studio emulators and remote Android devices hosted in Google data centers.

The large screen app quality guidelines help you verify that your adaptive app works well on tablets, foldables, and ChromeOS devices. The guidelines include tests that enable you to verify app functionality for critical user journeys. Although the guidelines focus on large screens, they're compatible with all screen sizes.

The DeviceConfigurationOverride interface in Compose 1.7 and higher lets you to override various aspects of device configuration. The API simulates different device configurations in a localized way for whatever composable content you want to test. For example, you can test multiple, arbitrary UI sizes in a single run of your test suite on a single device or emulator.

With the DeviceConfigurationOverride.then() extension function, you can test multiple configuration parameters, such as font size, locale, theme, and layout size, all at the same time.

Host-side screenshot tests are a fast and scalable way of verifying the visual appearance of your app layouts. Use host-side screenshots to test your UI for a variety of display sizes.

For more information, see Compose Preview Screenshot Testing.

Compose previews let you check your app's UI in the design view of Android Studio. Previews use annotations, such as @PreviewScreenSizes, @PreviewFontScale, and @PreviewLightDark to let you see composable content in various configurations. You can even interact with the previews.

Android Studio also highlights common usability issues in previews, such as buttons or text fields that are too wide.

For more information, see Preview your UI with composable previews.

Android Studio offers a variety of emulators for testing different layout sizes:

Securely connect to remote Android devices hosted in Google data centers and run your app on the latest Pixel and Samsung devices. Install and debug apps, run ADB commands, and rotate and fold devices to verify that your app works well on a variety of real devices.

Remote device streaming is integrated into Android Studio. For more information, see Android Device Streaming, powered by Firebase.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Build a supporting pane layout Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/build-a-supporting-pane-layout

**Contents:**
- Build a supporting pane layout Stay organized with collections Save and categorize content based on your preferences.
- Implement a supporting pane with a scaffold
- Add dependencies
  - Kotlin
  - Groovy
- Opt-in to the predictive back gesture
- Create a navigator
- Pass the navigator to the scaffold
- Extract pane composables
- Add ThreePaneScaffoldPredictiveBackHandler

The supporting pane layout keeps the user's focus on the app's main content while displaying relevant supporting information. For example, the main pane might show details about a movie, while the supporting pane lists similar movies, films by the same director, or works featuring the same actors.

For more details, see the Material 3 supporting pane guidelines.

NavigableSupportingPaneScaffold is a composable that simplifies implementing a supporting pane layout in Jetpack Compose. It wraps SupportingPaneScaffold and adds built-in navigation and predictive back handling.

A supporting pane scaffold supports up to three panes:

The scaffold adapts based on window size:

In small windows, only one pane is visible at a time, switching as users navigate.

NavigableSupportingPaneScaffold is part of the Material 3 adaptive layout library.

Add the following three, related dependencies to the build.gradle file of your app or module:

adaptive: Low-level building blocks such as HingeInfo and Posture

adaptive-layout: Adaptive layouts such as ListDetailPaneScaffold and SupportingPaneScaffold

adaptive-navigation: Composables for navigating within and between panes, as well as adaptive layouts that support navigation by default such as NavigableListDetailPaneScaffold and NavigableSupportingPaneScaffold

Verify that your project includes compose-material3-adaptive version 1.1.0-beta1 or higher.

To enable predictive back animations in Android 15 or lower, you must opt-in to support the predictive back gesture. To opt-in, add android:enableOnBackInvokedCallback="true" to the <application> tag or individual <activity> tags within your AndroidManifest.xml file.

Once your app targets Android 16 (API level 36) or higher, predictive back is enabled by default.

In small windows, only one pane displays at a time, so use a ThreePaneScaffoldNavigator to move to and from panes. Create an instance of the navigator with rememberSupportingPaneScaffoldNavigator.

val scaffoldNavigator = rememberSupportingPaneScaffoldNavigator() val scope = rememberCoroutineScope() SampleSupportingPaneScaffold.kt

The scaffold requires a ThreePaneScaffoldNavigator which is an interface representing the state of the scaffold, the ThreePaneScaffoldValue and a PaneScaffoldDirective.

NavigableSupportingPaneScaffold( navigator = scaffoldNavigator, mainPane = { /*...*/ }, supportingPane = { /*...*/ }, )SampleSupportingPaneScaffold.kt

The main pane and supporting pane are composables containing your content. Use AnimatedPane to apply the default pane animations during navigation. Use the scaffold value to check whether the supporting pane is hidden; if so, display a button that calls navigateTo(SupportingPaneScaffoldRole.Supporting) to display the supporting pane.

For large screens, use the ThreePaneScaffoldNavigator.navigateBack() method to dismiss the supporting pane, passing in the BackNavigationBehavior.PopUntilScaffoldValueChange constant. Calling this method forces a recomposition of the NavigableSupportingPaneScaffold. During recomposition, check the ThreePaneScaffoldNavigator.currentDestination property to determine whether to show the supporting pane.

Here's a complete implementation of the scaffold:

val scaffoldNavigator = rememberSupportingPaneScaffoldNavigator() val scope = rememberCoroutineScope() val backNavigationBehavior = BackNavigationBehavior.PopUntilScaffoldValueChange NavigableSupportingPaneScaffold( navigator = scaffoldNavigator, mainPane = { AnimatedPane( modifier = Modifier .safeContentPadding() .background(Color.Red) ) { if (scaffoldNavigator.scaffoldValue[SupportingPaneScaffoldRole.Supporting] == PaneAdaptedValue.Hidden) { Button( modifier = Modifier .wrapContentSize(), onClick = { scope.launch { scaffoldNavigator.navigateTo(SupportingPaneScaffoldRole.Supporting) } } ) { Text("Show supporting pane") } } else { Text("Supporting pane is shown") } } }, supportingPane = { AnimatedPane(modifier = Modifier.safeContentPadding()) { Column { // Allow users to dismiss the supporting pane. Use back navigation to // hide an expanded supporting pane. if (scaffoldNavigator.scaffoldValue[SupportingPaneScaffoldRole.Supporting] == PaneAdaptedValue.Expanded) { // Material design principles promote the usage of a right-aligned // close (X) button. IconButton( modifier = Modifier.align(Alignment.End).padding(16.dp), onClick = { scope.launch { scaffoldNavigator.navigateBack(backNavigationBehavior) } } ) { Icon(Icons.Default.Close, contentDescription = "Close") } } Text("Supporting pane") } } } )SampleSupportingPaneScaffold.kt

Extract the individual panes of a SupportingPaneScaffold into their own composables to make them reusable and testable. Use ThreePaneScaffoldScope to access AnimatedPane if you want the default animations:

@OptIn(ExperimentalMaterial3AdaptiveApi::class) @Composable fun ThreePaneScaffoldPaneScope.MainPane( shouldShowSupportingPaneButton: Boolean, onNavigateToSupportingPane: () -> Unit, modifier: Modifier = Modifier, ) { AnimatedPane( modifier = modifier.safeContentPadding() ) { // Main pane content if (shouldShowSupportingPaneButton) { Button(onClick = onNavigateToSupportingPane) { Text("Show supporting pane") } } else { Text("Supporting pane is shown") } } } @OptIn(ExperimentalMaterial3AdaptiveApi::class) @Composable fun ThreePaneScaffoldPaneScope.SupportingPane( scaffoldNavigator: ThreePaneScaffoldNavigator<Any>, modifier: Modifier = Modifier, backNavigationBehavior: BackNavigationBehavior = BackNavigationBehavior.PopUntilScaffoldValueChange, ) { val scope = rememberCoroutineScope() AnimatedPane(modifier = Modifier.safeContentPadding()) { Column { // Allow users to dismiss the supporting pane. Use back navigation to // hide an expanded supporting pane. if (scaffoldNavigator.scaffoldValue[SupportingPaneScaffoldRole.Supporting] == PaneAdaptedValue.Expanded) { // Material design principles promote the usage of a right-aligned // close (X) button. IconButton( modifier = modifier.align(Alignment.End).padding(16.dp), onClick = { scope.launch { scaffoldNavigator.navigateBack(backNavigationBehavior) } } ) { Icon(Icons.Default.Close, contentDescription = "Close") } } Text("Supporting pane") } } }SampleSupportingPaneScaffold.kt

Extracting the panes into composables simplifies the use of the SupportingPaneScaffold (compare the following to the complete implementation of the scaffold in the previous section):

val scaffoldNavigator = rememberSupportingPaneScaffoldNavigator() val scope = rememberCoroutineScope() NavigableSupportingPaneScaffold( navigator = scaffoldNavigator, mainPane = { MainPane( shouldShowSupportingPaneButton = scaffoldNavigator.scaffoldValue.secondary == PaneAdaptedValue.Hidden, onNavigateToSupportingPane = { scope.launch { scaffoldNavigator.navigateTo(ThreePaneScaffoldRole.Secondary) } } ) }, supportingPane = { SupportingPane(scaffoldNavigator = scaffoldNavigator) }, )SampleSupportingPaneScaffold.kt

If you need more control over specific aspects of the scaffold, consider using SupportingPaneScaffold instead of NavigableSupportingPaneScaffold. This accepts a PaneScaffoldDirective and ThreePaneScaffoldValue or ThreePaneScaffoldState separately. This flexibility lets you implement custom logic for pane spacing and determine how many panes should be displayed simultaneously. You can also enable predictive back support by adding ThreePaneScaffoldPredictiveBackHandler.

Attach the predictive back handler that takes a scaffold navigator instance and specify the backBehavior. This determines how destinations are popped from the backstack during back navigation. Then pass the scaffoldDirective and scaffoldState to SupportingPaneScaffold. Use the overload that accepts a ThreePaneScaffoldState, passing in scaffoldNavigator.scaffoldState.

Define the main and supporting panes within SupportingPaneScaffold. Use AnimatedPane for default pane animations.

After you implement these steps, your code should look similar to the following:

val scaffoldNavigator = rememberSupportingPaneScaffoldNavigator() val scope = rememberCoroutineScope() ThreePaneScaffoldPredictiveBackHandler( navigator = scaffoldNavigator, backBehavior = BackNavigationBehavior.PopUntilScaffoldValueChange ) SupportingPaneScaffold( directive = scaffoldNavigator.scaffoldDirective, scaffoldState = scaffoldNavigator.scaffoldState, mainPane = { MainPane( shouldShowSupportingPaneButton = scaffoldNavigator.scaffoldValue.secondary == PaneAdaptedValue.Hidden, onNavigateToSupportingPane = { scope.launch { scaffoldNavigator.navigateTo(ThreePaneScaffoldRole.Secondary) } } ) }, supportingPane = { SupportingPane(scaffoldNavigator = scaffoldNavigator) }, )SampleSupportingPaneScaffold.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (unknown):
```unknown
implementation("androidx.compose.material3.adaptive:adaptive")
implementation("androidx.compose.material3.adaptive:adaptive-layout")
implementation("androidx.compose.material3.adaptive:adaptive-navigation")
```

Example 2 (unknown):
```unknown
implementation 'androidx.compose.material3.adaptive:adaptive'
implementation 'androidx.compose.material3.adaptive:adaptive-layout'
implementation 'androidx.compose.material3.adaptive:adaptive-navigation'
```

---

## Carousel Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/carousel

**Contents:**
- Carousel Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Example: Multi-browse carousel
  - Key points about the code
  - Result
- Example: Uncontained carousel
  - Key points about the code
  - Result

A carousel displays a scrollable list of items that adapt dynamically based on window size. Use carousels to showcase a collection of related content. Carousel items emphasize visuals, but can also contain brief text that adapts to the item size.

There are four carousel layouts available to suit different use cases:

This page shows you how to implement the multi-browse and uncontained carousel layouts. See the Carousel Material 3 guidelines for more information about the layout types.

To implement multi-browse and uncontained carousels, use the HorizontalMultiBrowseCarousel and HorizontalUncontainedCarousel composables. These composables share the following key parameters:

These composables differ in how they specify item sizing:

This snippet implements a multi-browse carousel:

@Composable fun CarouselExample_MultiBrowse() { data class CarouselItem( val id: Int, @DrawableRes val imageResId: Int, val contentDescription: String ) val items = remember { listOf( CarouselItem(0, R.drawable.cupcake, "cupcake"), CarouselItem(1, R.drawable.donut, "donut"), CarouselItem(2, R.drawable.eclair, "eclair"), CarouselItem(3, R.drawable.froyo, "froyo"), CarouselItem(4, R.drawable.gingerbread, "gingerbread"), ) } HorizontalMultiBrowseCarousel( state = rememberCarouselState { items.count() }, modifier = Modifier .fillMaxWidth() .wrapContentHeight() .padding(top = 16.dp, bottom = 16.dp), preferredItemWidth = 186.dp, itemSpacing = 8.dp, contentPadding = PaddingValues(horizontal = 16.dp) ) { i -> val item = items[i] Image( modifier = Modifier .height(205.dp) .maskClip(MaterialTheme.shapes.extraLarge), painter = painterResource(id = item.imageResId), contentDescription = item.contentDescription, contentScale = ContentScale.Crop ) } }Carousel.kt

The following image shows the result from the preceding snippet:

The following snippet implements an uncontained carousel:

@Composable fun CarouselExample() { data class CarouselItem( val id: Int, @DrawableRes val imageResId: Int, val contentDescription: String ) val carouselItems = remember { listOf( CarouselItem(0, R.drawable.cupcake, "cupcake"), CarouselItem(1, R.drawable.donut, "donut"), CarouselItem(2, R.drawable.eclair, "eclair"), CarouselItem(3, R.drawable.froyo, "froyo"), CarouselItem(4, R.drawable.gingerbread, "gingerbread"), ) } HorizontalUncontainedCarousel( state = rememberCarouselState { carouselItems.count() }, modifier = Modifier .fillMaxWidth() .wrapContentHeight() .padding(top = 16.dp, bottom = 16.dp), itemWidth = 186.dp, itemSpacing = 8.dp, contentPadding = PaddingValues(horizontal = 16.dp) ) { i -> val item = carouselItems[i] Image( modifier = Modifier .height(205.dp) .maskClip(MaterialTheme.shapes.extraLarge), painter = painterResource(id = item.imageResId), contentDescription = item.contentDescription, contentScale = ContentScale.Crop ) } }Carousel.kt

The following image shows the result from the preceding snippet:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Enter PiP at correct times Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/pip-enter

**Contents:**
- Enter PiP at correct times Stay organized with collections Save and categorize content based on your preferences.
- Toggle state based on if video is playing
- Toggle state based on if player is released
- Use state to define if PiP mode is entered (pre-Android 12)
- Use state to define if PiP mode is entered (post-Android 12)
- Use setSourceRectHint to implement a smooth animation

Your app should not enter PiP mode in the following situations:

To control when your app enters PiP mode, add a variable that tracks the state of the video player using a mutableStateOf.

To toggle the state based on if the video player is playing, add a listener on the video player. Toggle the state of your state variable based on if the player is playing or not:

player.addListener(object : Player.Listener { override fun onIsPlayingChanged(isPlaying: Boolean) { shouldEnterPipMode = isPlaying } })PictureInPictureSnippets.kt

When the player is released, set your state variable to false:

fun releasePlayer() { shouldEnterPipMode = false }PictureInPictureSnippets.kt

In the lambda that defines the behavior when the OnUserLeaveHintListener is triggered, add an if statement with the state variable around the call to enterPictureInPictureMode():

val currentShouldEnterPipMode by rememberUpdatedState(newValue = shouldEnterPipMode) if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O && Build.VERSION.SDK_INT < Build.VERSION_CODES.S ) { val context = LocalContext.current DisposableEffect(context) { val onUserLeaveBehavior = Runnable { if (currentShouldEnterPipMode) { context.findActivity() .enterPictureInPictureMode(PictureInPictureParams.Builder().build()) } } context.findActivity().addOnUserLeaveHintListener( onUserLeaveBehavior ) onDispose { context.findActivity().removeOnUserLeaveHintListener( onUserLeaveBehavior ) } } } else { Log.i("PiP info", "API does not support PiP") }PictureInPictureSnippets.kt

Pass your state variable into setAutoEnterEnabled so that your app only enters PiP mode at the right time:

val pipModifier = modifier.onGloballyPositioned { layoutCoordinates -> val builder = PictureInPictureParams.Builder() // Add autoEnterEnabled for versions S and up if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) { builder.setAutoEnterEnabled(shouldEnterPipMode) } context.findActivity().setPictureInPictureParams(builder.build()) } VideoPlayer(pipModifier)PictureInPictureSnippets.kt

The setSourceRectHint API creates a smoother animation for entering PiP mode. In Android 12+, it also creates a smoother animation for exiting PiP mode. Add this API to the PiP builder to indicate the area of the activity that is visible following the transition into PiP.

val context = LocalContext.current val pipModifier = modifier.onGloballyPositioned { layoutCoordinates -> val builder = PictureInPictureParams.Builder() if (shouldEnterPipMode) { val sourceRect = layoutCoordinates.boundsInWindow().toAndroidRectF().toRect() builder.setSourceRectHint(sourceRect) } if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.S) { builder.setAutoEnterEnabled(shouldEnterPipMode) } context.findActivity().setPictureInPictureParams(builder.build()) } VideoPlayer(pipModifier)PictureInPictureSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/quick-guides?keywords=individualguide

**Contents:**
- Have questions or feedback

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-02-06 UTC.

---

## Icon buttons Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/components/icon-button

**Contents:**
- Icon buttons Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Basic example: Toggle icon button
  - Key points about the code
  - Result
- Advanced example: Repeated actions on press
  - Key points about the code
  - Key points about the code
  - Result
- Additional resources

Icon buttons display actions that users can take. Icon buttons must use an icon with a clear meaning, and typically represent common or frequently used actions.

There are two types of icon buttons:

Use the IconButton composable to implement standard icon buttons. To create different visual styles like filled, filled tonal, or outlined, use FilledIconButton, FilledTonalIconButton, and OutlinedIconButton, respectively.

The key parameters for IconButton include:

This example shows you how to implement a toggle icon button. A toggle icon button changes its appearance based on whether it's selected or unselected.

@Preview @Composable fun ToggleIconButtonExample() { // isToggled initial value should be read from a view model or persistent storage. var isToggled by rememberSaveable { mutableStateOf(false) } IconButton( onClick = { isToggled = !isToggled } ) { Icon( painter = if (isToggled) painterResource(R.drawable.favorite_filled) else painterResource(R.drawable.favorite), contentDescription = if (isToggled) "Selected icon button" else "Unselected icon button." ) } }IconButton.kt

The following image shows the toggle icon button from the preceding snippet in its unselected state:

This section demonstrates how to create icon buttons that continuously trigger an action while the user presses and holds them, rather than just triggering once per click.

@Composable fun MomentaryIconButton( unselectedImage: Int, selectedImage: Int, contentDescription: String, modifier: Modifier = Modifier, stepDelay: Long = 100L, // Minimum value is 1L milliseconds. onClick: () -> Unit ) { val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() val pressedListener by rememberUpdatedState(onClick) LaunchedEffect(isPressed) { while (isPressed) { delay(stepDelay.coerceIn(1L, Long.MAX_VALUE)) pressedListener() } } IconButton( modifier = modifier, onClick = onClick, interactionSource = interactionSource ) { Icon( painter = if (isPressed) painterResource(id = selectedImage) else painterResource(id = unselectedImage), contentDescription = contentDescription, ) } }IconButton.kt

Next, use this MomentaryIconButton in an example. The following snippet demonstrates two icon buttons controlling a counter:

@Preview() @Composable fun MomentaryIconButtonExample() { var pressedCount by remember { mutableIntStateOf(0) } Row( modifier = Modifier.fillMaxWidth(), verticalAlignment = Alignment.CenterVertically ) { MomentaryIconButton( unselectedImage = R.drawable.fast_rewind, selectedImage = R.drawable.fast_rewind_filled, stepDelay = 100L, onClick = { pressedCount -= 1 }, contentDescription = "Decrease count button" ) Spacer(modifier = Modifier) Text("advanced by $pressedCount frames") Spacer(modifier = Modifier) MomentaryIconButton( unselectedImage = R.drawable.fast_forward, selectedImage = R.drawable.fast_forward_filled, contentDescription = "Increase count button", stepDelay = 100L, onClick = { pressedCount += 1 } ) } }IconButton.kt

The following video shows the UI with the icon buttons and the counter:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Add remote actions to PiP Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/pip-remote-actions

**Contents:**
- Add remote actions to PiP Stay organized with collections Save and categorize content based on your preferences.
- Next steps

If you want to add controls (play, pause, etc.) to your PiP window, create a RemoteAction for each control you want to add.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Migrate CoordinatorLayout to Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/migrate/migration-scenarios/coordinator-layout

**Contents:**
- Migrate CoordinatorLayout to Compose Stay organized with collections Save and categorize content based on your preferences.
- Migration steps
- Common use cases
  - Collapse and expand toolbars
    - Customize the collapsing/expanding scroll effect
  - Drawers
  - Snackbars
- Learn more
- Recommended for you

CoordinatorLayout is a ViewGroup that enables complex, overlapping, and nested layouts. It's used as a container to enable specific Material Design interactions, such as expanding/collapsing toolbars and bottom sheets, for Views contained within it.

In Compose, the closest equivalent of a CoordinatorLayout is a Scaffold. A Scaffold provides content slots for combining Material Components into common screen patterns and interactions. This page describes how you can migrate your CoordinatorLayout implementation to use Scaffold in Compose.

To migrate CoordinatorLayout to Scaffold, follow these steps:

In the snippet below, the CoordinatorLayout contains an AppBarLayout for containing a ToolBar, a ViewPager, and a FloatingActionButton. Comment out the CoordinatorLayout and its children from your UI hierarchy and add a ComposeView to replace it.

In your Fragment or Activity, obtain a reference to the ComposeView you just added and call the setContent method on it. In the body of the method, set a Scaffold as its content:

composeView.setContent { Scaffold(Modifier.fillMaxSize()) { contentPadding -> // Scaffold contents // ... } }MigrationCommonScenariosSnippets.kt

In the content of your Scaffold, add your screen's primary content within it. Because the primary content in the XML above is a ViewPager2, we'll use a HorizontalPager, which is the Compose equivalent of it. The content lambda of the Scaffold also receives an instance of PaddingValues that should be applied to the content root. You can use Modifier.padding to apply the same PaddingValues to the HorizontalPager.

composeView.setContent { Scaffold(Modifier.fillMaxSize()) { contentPadding -> val pagerState = rememberPagerState { 10 } HorizontalPager( state = pagerState, modifier = Modifier.padding(contentPadding) ) { /* Page contents */ } } }MigrationCommonScenariosSnippets.kt

Use other content slots that Scaffold provides to add more screen elements and migrate remaining child Views. You can use the topBar slot to add a TopAppBar, and the floatingActionButton slot to provide a FloatingActionButton.

composeView.setContent { Scaffold( Modifier.fillMaxSize(), topBar = { TopAppBar( title = { Text("My App") } ) }, floatingActionButton = { FloatingActionButton( onClick = { /* Handle click */ } ) { Icon( Icons.Filled.Add, contentDescription = "Add Button" ) } } ) { contentPadding -> val pagerState = rememberPagerState { 10 } HorizontalPager( state = pagerState, modifier = Modifier.padding(contentPadding) ) { /* Page contents */ } } }MigrationCommonScenariosSnippets.kt

In the View system, to collapse and expand the toolbar with CoordinatorLayout, you use an AppBarLayout as a container for the toolbar. You can then specify a Behavior through layout_behavior in XML on the associated scrollable View (like RecyclerView or NestedScrollView) to declare how the toolbar collapses/expands as you scroll.

In Compose, you can achieve a similar effect through a TopAppBarScrollBehavior. For example, to implement a collapsing/expanding toolbar so that the toolbar appears when you scroll up, follow these steps:

Connect the NestedScrollConnection via Modifier.nestedScroll on the Scaffold so that the Scaffold can receive nested scroll events as the scrollable content scrolls up/down. This way, the contained app bar can appropriately collapse/expand as the content scrolls.

// 1. Create the TopAppBarScrollBehavior val scrollBehavior = TopAppBarDefaults.enterAlwaysScrollBehavior() Scaffold( topBar = { TopAppBar( title = { Text("My App") }, // 2. Provide scrollBehavior to TopAppBar scrollBehavior = scrollBehavior ) }, // 3. Connect the scrollBehavior.nestedScrollConnection to the Scaffold modifier = Modifier .fillMaxSize() .nestedScroll(scrollBehavior.nestedScrollConnection) ) { contentPadding -> /* Contents */ // ... }MigrationCommonScenariosSnippets.kt

You can provide several parameters for enterAlwaysScrollBehavior to customize the collapsing/expanding animation effect. TopAppBarDefaults also provides other TopAppBarScrollBehavior such as exitUntilCollapsedScrollBehavior, which only expands the app bar when the content is scrolled all the way down.

To create a completely custom effect (for example, a parallax effect), you can also create your own NestedScrollConnection and offset the toolbar manually as the content scrolls. See the Nested scroll sample on AOSP for a code example.

With Views, you implement a navigation drawer by using a DrawerLayout as the root view. In turn, your CoordinatorLayout is a child view of the DrawerLayout. The DrawerLayout also contains another child view, such as a NavigationView, to display the navigation options in the drawer.

In Compose, you can implement a navigation drawer using the ModalNavigationDrawer composable. ModalNavigationDrawer offers a drawerContent slot for the drawer and a content slot for the screen's content.

ModalNavigationDrawer( drawerContent = { ModalDrawerSheet { Text("Drawer title", modifier = Modifier.padding(16.dp)) HorizontalDivider() NavigationDrawerItem( label = { Text(text = "Drawer Item") }, selected = false, onClick = { /*TODO*/ } ) // ...other drawer items } } ) { Scaffold(Modifier.fillMaxSize()) { contentPadding -> // Scaffold content // ... } }MigrationCommonScenariosSnippets.kt

See Drawers to learn more.

Scaffold provides a snackbarHost slot, which can accept a SnackbarHost composable to display a Snackbar.

val scope = rememberCoroutineScope() val snackbarHostState = remember { SnackbarHostState() } Scaffold( snackbarHost = { SnackbarHost(hostState = snackbarHostState) }, floatingActionButton = { ExtendedFloatingActionButton( text = { Text("Show snackbar") }, icon = { Icon(Icons.Filled.Image, contentDescription = "") }, onClick = { scope.launch { snackbarHostState.showSnackbar("Snackbar") } } ) } ) { contentPadding -> // Screen content // ... }MigrationCommonScenariosSnippets.kt

See Snackbars to learn more.

For more information about migrating a CoordinatorLayout to Compose, see the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (typescript):
```typescript
<!--  <androidx.coordinatorlayout.widget.CoordinatorLayout-->
<!--      android:id="@+id/coordinator_layout"-->
<!--      android:layout_width="match_parent"-->
<!--      android:layout_height="match_parent"-->
<!--      android:fitsSystemWindows="true">-->

<!--    <androidx.compose.ui.platform.ComposeView-->
<!--        android:id="@+id/compose_view"-->
<!--        android:layout_width="match_parent"-->
<!--        android:layout_height="match_parent"-->
<!--        app:layout_behavior="@string/appbar_scrolling_view_behavior" />-->

<!--    <com.google.android.material.appbar.AppBarLayout-->
<!--        android:id="@+id/app_bar_layout"-->
<!--        android:layout_width="match_parent"-->
<!--        android:layout_height="wrap_content"-->
<!--        android:fitsSystemWindows="true"-->
<!--        android:theme="@style/Theme.Sunflower.AppBarOverlay">-->

    <!-- AppBarLayout contents here -->

<!--    </com.google.android.material.appbar.AppBarLayout>-->

<!--  </androidx.coordinatorlayout.widget.CoordinatorLayout>-->

<androidx.compose.ui.platform.ComposeView
    android:id="@+id/compose_view"
    android:layout_width="match_parent"
    android:layout_height="match_parent" />
```

---

## Test how your content renders with cutouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/system/test-cutouts

**Contents:**
- Test how your content renders with cutouts Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Be sure to test all of your app's screens and experiences. Test on devices with different types of cutouts, if possible. If you don't have a device with a cutout, you can simulate common cutout configurations on any device or emulator running Android 9 or higher by doing the following:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Build adaptive navigation Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/adaptive/build-adaptive-navigation

**Contents:**
- Build adaptive navigation Stay organized with collections Save and categorize content based on your preferences.
- Add dependencies
  - Kotlin
  - Groovy
- Create a scaffold
- Change colors
- Customize navigation types
- Additional resources

Most apps have a few top-level destinations that are accessible through the app's primary navigation UI. In compact windows, such as a standard phone display, the destinations are typically displayed in a navigation bar at the bottom of the window. In an expanded window, such as a full screen app on a tablet, a navigation rail alongside the app is usually a better choice since the navigation controls are easier to reach while holding the left and right sides of the device.

NavigationSuiteScaffold simplifies switching between navigation UIs by displaying the appropriate navigation UI composable based on WindowSizeClass. This includes dynamically changing the UI during runtime window size changes. The default behavior is to show either of the following UI components:

NavigationSuiteScaffold is part of the Material3 adaptive navigation suite library. Add a dependency for the library in the build.gradle file of your app or module:

implementation("androidx.compose.material3:material3-adaptive-navigation-suite")

implementation 'androidx.compose.material3:material3-adaptive-navigation-suite'

The two main parts of NavigationSuiteScaffold are the navigation suite items and the content for the selected destination. You can directly define the navigation suite items in a composable, but it's common to have these defined elsewhere, for example, in an enum:

enum class AppDestinations( @StringRes val label: Int, val icon: ImageVector, @StringRes val contentDescription: Int ) { HOME(R.string.home, Icons.Default.Home, R.string.home), FAVORITES(R.string.favorites, Icons.Default.Favorite, R.string.favorites), SHOPPING(R.string.shopping, Icons.Default.ShoppingCart, R.string.shopping), PROFILE(R.string.profile, Icons.Default.AccountBox, R.string.profile), }SampleNavigationSuiteScaffold.kt

To use NavigationSuiteScaffold, you must track the current destination, which you can do by using rememberSaveable:

var currentDestination by rememberSaveable { mutableStateOf(AppDestinations.HOME) }SampleNavigationSuiteScaffold.kt

In the following example, the navigationSuiteItems parameter (type NavigationSuiteScope) uses its item function to define the navigation UI for an individual destination. The destination UI is used across navigation bars, rails, and drawers. To create navigation items, you loop over your AppDestinations (defined in the preceding snippet):

NavigationSuiteScaffold( navigationSuiteItems = { AppDestinations.entries.forEach { item( icon = { Icon( it.icon, contentDescription = stringResource(it.contentDescription) ) }, label = { Text(stringResource(it.label)) }, selected = it == currentDestination, onClick = { currentDestination = it } ) } } ) { // TODO: Destination content. }SampleNavigationSuiteScaffold.kt

Within the destination content lambda, use the currentDestination value to decide what UI to display. If you use a navigation library in your app, use it here to display the appropriate destination. A when statement can suffice:

NavigationSuiteScaffold( navigationSuiteItems = { /*...*/ } ) { // Destination content. when (currentDestination) { AppDestinations.HOME -> HomeDestination() AppDestinations.FAVORITES -> FavoritesDestination() AppDestinations.SHOPPING -> ShoppingDestination() AppDestinations.PROFILE -> ProfileDestination() } }SampleNavigationSuiteScaffold.kt

NavigationSuiteScaffold creates a Surface over the entire area the scaffold occupies, typically the full window. On top of that, the scaffold draws the particular navigation UI, such as a NavigationBar. Both the surface and the navigation UI use the values specified in your app's theme, but you can override the theme values.

The containerColor parameter specifies the color of the surface. The default is the background color of your color scheme. The contentColor parameter specifies the color of content on that surface. The default is the "on" color of whatever is specified for containerColor. For example, if containerColor uses the background color, then contentColor uses the onBackground color. See Material Design 3 theming in Compose for more details about how the color system works. When overriding these values, use values defined in your theme so your app supports dark and light display modes:

NavigationSuiteScaffold( navigationSuiteItems = { /* ... */ }, containerColor = MaterialTheme.colorScheme.primary, contentColor = MaterialTheme.colorScheme.onPrimary, ) { // Content... }SampleNavigationSuiteScaffold.kt

The navigation UI is drawn in front of the NavigationSuiteScaffold surface. The default values for the UI colors are provided by NavigationSuiteDefaults.colors(), but you can override these values as well. For example, if you want the background of the navigation bar to be transparent but the other values to be the defaults, override navigationBarContainerColor:

NavigationSuiteScaffold( navigationSuiteItems = { /* ... */ }, navigationSuiteColors = NavigationSuiteDefaults.colors( navigationBarContainerColor = Color.Transparent, ) ) { // Content... }SampleNavigationSuiteScaffold.kt

Ultimately, you can customize each item in the navigation UI. When calling the item function, you can pass in an instance of NavigationSuiteItemColors. The class specifies the colors for items in a navigation bar, navigation rail, and navigation drawer. That means you can have identical colors across each navigation UI type, or you can vary the colors based on your needs. Define the colors at the NavigationSuiteScaffold level to use the same object instance for all items and call the NavigationSuiteDefaults.itemColors() function to override only the ones you want to change:

val myNavigationSuiteItemColors = NavigationSuiteDefaults.itemColors( navigationBarItemColors = NavigationBarItemDefaults.colors( indicatorColor = MaterialTheme.colorScheme.primaryContainer, selectedIconColor = MaterialTheme.colorScheme.onPrimaryContainer ), ) NavigationSuiteScaffold( navigationSuiteItems = { AppDestinations.entries.forEach { item( icon = { Icon( it.icon, contentDescription = stringResource(it.contentDescription) ) }, label = { Text(stringResource(it.label)) }, selected = it == currentDestination, onClick = { currentDestination = it }, colors = myNavigationSuiteItemColors, ) } }, ) { // Content... }SampleNavigationSuiteScaffold.kt

The default behavior of NavigationSuiteScaffold changes the navigation UI based on window size classes. However, you may want to override this behavior. For example, if your app shows a single large pane of content for a feed, the app could use a permanent navigation drawer for expanded windows but still fall back to the default behavior for compact and medium window size classes:

val adaptiveInfo = currentWindowAdaptiveInfo() val customNavSuiteType = with(adaptiveInfo) { if (windowSizeClass.isWidthAtLeastBreakpoint(WIDTH_DP_EXPANDED_LOWER_BOUND)) { NavigationSuiteType.NavigationDrawer } else { NavigationSuiteScaffoldDefaults.calculateFromAdaptiveInfo(adaptiveInfo) } } NavigationSuiteScaffold( navigationSuiteItems = { /* ... */ }, layoutType = customNavSuiteType, ) { // Content... }SampleNavigationSuiteScaffold.kt

Principles of navigation

Material Design guidance:

androidx.compose.material3 library components:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/components

**Contents:**
  - Material components in Compose
- Actions
  - Buttons
  - Floating action button
  - Icon button
  - Segmented button
- Communication
  - Badges
  - Progress indicators
  - Snackbar

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-07-11 UTC.

---

## Compose Compiler Gradle plugin Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/compiler

**Contents:**
- Compose Compiler Gradle plugin Stay organized with collections Save and categorize content based on your preferences.
- Set up with Gradle version catalogs
- Set up without Gradle version catalogs
- Configuration options with the Compose Compiler Gradle Plugin

For Gradle users, you can use the Compose Compiler Gradle plugin to make setting up and configuring Compose easier.

The following instructions outline how you can set up the Compose Compiler Gradle plugin:

Your app should now build and compile if you are using the default set up. If you had configured custom options on the Compose compiler, see the following section.

To set up the Compose Compiler Gradle plugin without version catalogs, add the following plugin to build.gradle.kts files associated with modules you use Compose:

You might also need to add this classpath to your top-level project build.gradle.kts file:

To configure the Compose compiler using the Gradle plugin, add the composeCompiler block to the module's build.gradle.kts file at the top level.

For the full list of available options, see the documentation.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-11-10 UTC.

**Examples:**

Example 1 (json):
```json
[versions]
kotlin = "2.0.0"

[plugins]
org-jetbrains-kotlin-android = { id = "org.jetbrains.kotlin.android", version.ref = "kotlin" }

// Add this line
compose-compiler = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
```

Example 2 (unknown):
```unknown
plugins {
   // Existing plugins
   alias(libs.plugins.compose.compiler) apply false
}
```

Example 3 (unknown):
```unknown
plugins {
   // Existing plugins
   alias(libs.plugins.compose.compiler)
}
```

Example 4 (unknown):
```unknown
plugins {
    id("org.jetbrains.kotlin.plugin.compose") version "2.0.0" // this version matches your Kotlin version
}
```

---

## Support multiple links in a single string of text Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/quick-guides/content/support-multiple-links

**Contents:**
- Support multiple links in a single string of text Stay organized with collections Save and categorize content based on your preferences.
- Results
- Version compatibility
  - Dependencies
- Display multiple links in a single string
  - Key points about the code
- Collections that contain this guide
  - Display text
- Have questions or feedback

You can support multiple links in a single string of text to perform different actions when clicking a subsection of text.

This implementation requires that your project minSDK be set to API level 21 or higher.

This snippet embeds multiple clickable links into a single string of text:

@Composable fun AnnotatedStringWithLinkSample() { // Display multiple links in the text Text( buildAnnotatedString { append("Go to the ") withLink( LinkAnnotation.Url( "https://developer.android.com/", TextLinkStyles(style = SpanStyle(color = Color.Blue)) ) ) { append("Android Developers ") } append("website, and check out the") withLink( LinkAnnotation.Url( "https://developer.android.com/jetpack/compose", TextLinkStyles(style = SpanStyle(color = Color.Green)) ) ) { append("Compose guidance") } append(".") } ) }TextSnippets.kt

This guide is part of these curated Quick Guide collections that cover broader Android development goals:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Customize shared element transition Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/shared-elements/customize

**Contents:**
- Customize shared element transition Stay organized with collections Save and categorize content based on your preferences.
- Animation spec
- Resize mode
- Dynamically enable and disable shared elements
- Skip to final layout
- Clip and overlays
- Notify sibling layouts of changes to shared element size

To customize how the shared element transition animation runs, there are a few parameters that can be used to change how the shared elements transition.

To change the animation spec used for the size and position movement, you can specify a different boundsTransform parameter on Modifier.sharedElement(). This provides the initial Rect position and target Rect position.

For example, to make the text in the preceding example to move with an arc motion, specify the boundsTransform parameter to use a keyframes spec:

val textBoundsTransform = BoundsTransform { initialBounds, targetBounds -> keyframes { durationMillis = boundsAnimationDurationMillis initialBounds at 0 using ArcMode.ArcBelow using FastOutSlowInEasing targetBounds at boundsAnimationDurationMillis } } Text( "Cupcake", fontSize = 28.sp, modifier = Modifier.sharedBounds( rememberSharedContentState(key = "title"), animatedVisibilityScope = animatedVisibilityScope, boundsTransform = textBoundsTransform ) )CustomizeSharedElementsSnippets.kt

You can use any AnimationSpec. This example uses a keyframes spec.

When animating between two shared bounds, you can set the resizeMode parameter to either RemeasureToBounds or ScaleToBounds. This parameter determines how the shared element transitions between the two states. ScaleToBounds first measures the child layout with the lookahead (or target) constraints. Then, the child's stable layout is scaled to fit in the shared bounds. ScaleToBounds can be thought of as a "graphical scale" between the states.

In contrast, RemeasureToBounds re-measures and re-layouts the child layout of sharedBounds with animated fixed constraints based on the target size. The re-measurement is triggered by the bounds size change, which could potentially be every frame.

For Text composables, ScaleToBounds is recommended, as it avoids relayout and reflowing of text onto different lines. RemeasureToBounds is recommended for bounds that are different aspect ratios, and if you'd like fluid continuity between the two shared elements.

The difference between the two resize modes can be seen in the examples that follow:

By default, sharedElement() and sharedBounds() are configured to animate the layout changes whenever a matching key is found in the target state. However, you may want to disable this animation dynamically based on specific conditions, such as the direction of navigation or the current UI state.

To control whether the shared element transition occurs, you can customize the SharedContentConfig passed to rememberSharedContentState(). The isEnabled property determines if the shared element is active.

The following example demonstrates how to define a configuration that only enables the shared transition when navigating between specific screens (e.g., only from A to B), while disabling it for others.

SharedTransitionLayout { val transition = updateTransition(currentState) transition.AnimatedContent { targetState -> // Create the configuration that depends on state changing. fun animationConfig() : SharedTransitionScope.SharedContentConfig { return object : SharedTransitionScope.SharedContentConfig { override val SharedTransitionScope.SharedContentState.isEnabled: Boolean // For this example, we only enable the transition in one direction // from A -> B and not the other way around. get() = transition.currentState == "A" && transition.targetState == "B" } } when (targetState) { "A" -> Box( modifier = Modifier .sharedElement( rememberSharedContentState( key = "shared_box", config = animationConfig() ), animatedVisibilityScope = this ) // ... ) { // Your content } "B" -> { Box( modifier = Modifier .sharedElement( rememberSharedContentState( key = "shared_box", config = animationConfig() ), animatedVisibilityScope = this ) // ... ) { // Your content } } } } }CustomizeSharedElementsSnippets.kt

By default, if a shared element is disabled during an ongoing animation, it still completes the current in-progress animation to prevent accidentally removing in-flight animations. If you need to remove the element whilst the animation is in progress, you can override shouldKeepEnabledForOngoingAnimation in the SharedContentConfig interface to return false.

By default, when transitioning between two layouts, the layout size animates between its start and final state. This may be undesirable behavior when animating content such as text.

The following example illustrates the description text "Lorem Ipsum" entering the screen in two different ways. In the first example, the text reflows as it enters as the container grows in size. In the second example the text does not reflow as it grows. Adding Modifier.skipToLookaheadSize() prevents the reflow as it grows.

No Modifier.skipToLookaheadSize() - notice the "Lorem Ipsum" text reflowing

Modifier.skipToLookaheadSize() - notice the "Lorem Ipsum" text keeps its final state at the start of the animation

In order for shared elements to share between different composables, the rendering of the composable is elevated into a layer overlay when the transition is started to its match in the destination. The effect of this is that it'll escape the parent's bounds and its layer transformations (for example, the alpha and scale).

It will render on top of other non-shared UI elements. Once the transition is finished, the element will be dropped from the overlay to its own DrawScope.

To clip a shared element to a shape, use the standard Modifier.clip() function. Place it after the sharedElement():

Image( painter = painterResource(id = R.drawable.cupcake), contentDescription = "Cupcake", modifier = Modifier .size(100.dp) .sharedElement( rememberSharedContentState(key = "image"), animatedVisibilityScope = this@AnimatedContent ) .clip(RoundedCornerShape(16.dp)), contentScale = ContentScale.Crop )CustomizeSharedElementsSnippets.kt

If you need to ensure that a shared element never renders outside of a parent container, you can set clipInOverlayDuringTransition on sharedElement(). By default, for nested shared bounds, clipInOverlayDuringTransition uses the clip path from the parent sharedBounds().

To support keeping specific UI elements, such as a bottom bar or floating action button, always on top during a shared element transition, use Modifier.renderInSharedTransitionScopeOverlay(). By default, this modifier keeps the content in the overlay during the time when the shared transition is active.

For example, in Jetsnack, the BottomAppBar needs to be placed on top of the shared element until such time as the screen is not visible. Adding the modifier onto the composable keeps it elevated.

Without Modifier.renderInSharedTransitionScopeOverlay()

With Modifier.renderInSharedTransitionScopeOverlay()

You might want your non-shared composable to animate away as well as remain on top of the other composables before the transition. In such cases, use renderInSharedTransitionScopeOverlay().animateEnterExit() to animate the composable out as the shared element transition runs:

JetsnackBottomBar( modifier = Modifier .renderInSharedTransitionScopeOverlay( zIndexInOverlay = 1f, ) .animateEnterExit( enter = fadeIn() + slideInVertically { it }, exit = fadeOut() + slideOutVertically { it } ) )CustomizeSharedElementsSnippets.kt

In the rare case that you'd like your shared element to not render in an overlay, you can set the renderInOverlayDuringTransition on sharedElement() to false.

By default, sharedBounds() and sharedElement() don't notify the parent container of any size changes as the layout transitions.

In order to propagate size changes to the parent container as it transitions, change the placeholderSize parameter to PlaceholderSize.AnimatedSize. Doing so causes the item to grow or shrink. All other items in the layout respond to the change.

PlaceholderSize.ContentSize (default)

PlaceholderSize.AnimatedSize

(Notice how the other items in the list move down in response to the one item growing)

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Handle errors with Glance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/glance/error-handling

**Contents:**
- Handle errors with Glance Stay organized with collections Save and categorize content based on your preferences.
- Use a try-catch block around non-composable components
- Default error layout
- Add actions to the default error UI

API features for improving error handling on Glance are included beginning in Android 15. This page provides best practices regarding these APIs.

Compose doesn't allow try-catch blocks around composables, but lets you wrap your app's other logic in these blocks. This lets you use Compose for your error view, as shown in the following example:

If there is an uncaught exception or a Compose error, Glance displays a default error layout:

Glance lets developers provide an XML layout as a fallback if composition fails. This means that there was an error in the Compose code. This error UI also appears if you have an uncaught error in your app's code.

This layout is a static layout that your user can't interact with, but is good in emergency cases.

As of Glance 1.1.0, Glance lets you override the default error handling code. This way, you can add action callbacks in the event of an uncaught exception or error in composition.

To use this feature, override the onCompositionError() function:

In this function, Glance falls back to the RemoteViews API for error handling. This lets you specify layouts and action handlers using XML.

The following examples show you, step-by-step, how to create an error UI that includes a button to send feedback:

Write the error_layout.xml file:

Override the onCompositionError function:

Create a pending intent that references your GlanceAppWidgetReceiver:

Handle the intent in your GlanceAppWidgetReceiver:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (gdscript):
```gdscript
provideContent {
       var isError = false;
       var data = null
       try {
           val repository = (context.applicationContext as MyApplication).myRepository
           data = repository.loadData()
       } catch (e: Exception) {
           isError = true;
           //handleError
       }

       if (isError) {
           ErrorView()
       } else {
           Content(data)
       }
   }
```

Example 2 (swift):
```swift
class UpgradeWidget : GlanceAppWidget(errorUiLayout = R.layout.error_layout)
```

Example 3 (yaml):
```yaml
GlanceAppWidget.onCompositionError(
    context: Context,
    glanceId: GlanceId,
    appWidgetId: Int,
    throwable: Throwable
)
```

Example 4 (jsx):
```jsx
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
   style="@style/Widget.MyApplication.AppWidget.Error"
   android:id="@android:id/background"
   android:layout_width="match_parent"
   android:textSize="24sp"
   android:layout_height="match_parent"
   android:orientation="vertical">

   <TextView
       android:id="@+id/error_title_view"
       android:layout_width="match_parent"
       android:textColor="@color/white"
       android:textFontWeight="800"
       android:layout_height="wrap_content"
       android:text="Example Widget Error" />

   <LinearLayout
       android:layout_width="match_parent"
       android:orientation="horizontal"
       android:paddingTop="4dp"
       android:layout_height="match_parent">

       <ImageButton
        android:layout_width="64dp"
        android:layout_height="64dp"
        android:layout_gravity="center"
        android:tint="@color/white"
        android:id="@+id/error_icon"
        android:src="@drawable/heart_broken_fill0_wght400_grad0_opsz24"
       />
       <TextView
           android:id="@+id/error_text_view"
           android:layout_width="wrap_content"
           android:textColor="@color/white"
           android:layout_height="wrap_content"
           android:layout_gravity="center"
           android:padding="8dp"
           android:textSize="16sp"
           android:layout_weight="1"
           android:text="Useful Error Message!" />
   </LinearLayout>

</LinearLayout>
```

---
