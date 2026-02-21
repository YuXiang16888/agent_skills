# Jetpack-Compose - Accessibility

**Pages:** 8

---

## API defaults Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/api-defaults

**Contents:**
- API defaults Stay organized with collections Save and categorize content based on your preferences.
- Minimum touch target sizes
- Graphic elements
- Interactive elements
- Custom components
- Recommended for you

Material, Compose UI, and Foundation APIs implement and offer many accessible practices by default. They contain built-in semantics that follow their specific role and function. This means most accessibility support is provided with little or no additional work.

Using the appropriate APIs for the appropriate purpose means that components usually come with predefined accessibility behaviors that cover standard use cases. However, always double-check whether these defaults fit your accessibility needs. If not, Compose provides ways to cover more specific requirements.

Understanding the default accessibility semantics and patterns in Compose APIs helps you use them with accessibility in mind. It also helps you support accessibility in more custom components.

Any on-screen element that someone can click, touch, or interact with must be large enough for reliable interaction. When sizing these elements, make sure to set the minimum size to 48dp to correctly follow the Material Design accessibility guidelines.

Material components—like Checkbox, RadioButton, Switch, Slider, and Surface—set this minimum size internally, but only when the component can receive user actions. For example, when a Checkbox has its onCheckedChange parameter set to a non-null value, the checkbox includes padding to have a width and height of at least 48 dp.

@Composable private fun CheckableCheckbox() { Checkbox(checked = true, onCheckedChange = {}) }AccessibilitySnippets.kt

When the onCheckedChange parameter is set to null, the padding is not included, because the component cannot be interacted with directly.

@Composable private fun NonClickableCheckbox() { Checkbox(checked = true, onCheckedChange = null) }AccessibilitySnippets.kt

When implementing selection controls like Switch, RadioButton, or Checkbox, you typically lift the clickable behavior to a parent container by setting the click callback on the composable to null, and adding a toggleable or selectable modifier to the parent composable.

@Composable private fun CheckableRow() { MaterialTheme { var checked by remember { mutableStateOf(false) } Row( Modifier .toggleable( value = checked, role = Role.Checkbox, onValueChange = { checked = !checked } ) .padding(16.dp) .fillMaxWidth() ) { Text("Option", Modifier.weight(1f)) Checkbox(checked = checked, onCheckedChange = null) } } }AccessibilitySnippets.kt

When the size of a clickable composable is smaller than the minimum touch target size, Compose still increases the touch target size. It does so by expanding the touch target size outside of the boundaries of the composable.

The following example contains a very small clickable Box. The touch target area is automatically expanded beyond the boundaries of the Box, so tapping next to the Box still triggers the click event.

@Composable private fun SmallBox() { var clicked by remember { mutableStateOf(false) } Box( Modifier .size(100.dp) .background(if (clicked) Color.DarkGray else Color.LightGray) ) { Box( Modifier .align(Alignment.Center) .clickable { clicked = !clicked } .background(Color.Black) .size(1.dp) ) } }AccessibilitySnippets.kt

To prevent possible overlap between touch areas of different composables, always use a large enough minimum size for the composable. In the example, that would mean using the sizeIn modifier to set the minimum size for the inner box:

@Composable private fun LargeBox() { var clicked by remember { mutableStateOf(false) } Box( Modifier .size(100.dp) .background(if (clicked) Color.DarkGray else Color.LightGray) ) { Box( Modifier .align(Alignment.Center) .clickable { clicked = !clicked } .background(Color.Black) .sizeIn(minWidth = 48.dp, minHeight = 48.dp) ) } }AccessibilitySnippets.kt

When you define an Image or Icon composable, there is no automatic way for the Android framework to understand what the app is displaying. You need to pass a textual description of the graphic element.

Imagine a screen where the user can share the current page with friends. This screen contains a clickable share icon:

Based on the icon alone, the Android framework can't describe it to a visually impaired user. The Android framework needs an additional textual description of the icon.

The contentDescription parameter describes a graphic element. Use a localized string, as it is visible to the user.

@Composable private fun ShareButton(onClick: () -> Unit) { IconButton(onClick = onClick) { Icon( imageVector = Icons.Filled.Share, contentDescription = stringResource(R.string.label_share) ) } }AccessibilitySnippets.kt

Some graphic elements are purely decorative and you might not want to communicate them to the user. When you set the contentDescription parameter to null, you indicate to the Android framework that this element does not have associated actions or state.

@Composable private fun PostImage(post: Post, modifier: Modifier = Modifier) { val image = post.imageThumb ?: painterResource(R.drawable.placeholder_1_1) Image( painter = image, // Specify that this image has no semantic meaning contentDescription = null, modifier = modifier .size(40.dp, 40.dp) .clip(MaterialTheme.shapes.small) ) }AccessibilitySnippets.kt

contentDescription is mainly meant to be used for graphic elements, such as images. Material components, like Button or Text, and actionable behaviors, like clickable or toggleable, come with other predefined semantics that describe their intrinsic behavior, and can be changed through other Compose APIs.

Material and Foundation Compose APIs make UI elements that users can interact with through the clickable and toggleable modifier APIs. Because interactable components might consist of multiple elements, clickable and toggleable merge their children's semantics by default, so that the component is treated as one logical entity.

For example, a Material Button might consist of a child icon and some text. Instead of treating the children as individuals, a Material Button merges its children semantics by default, so that accessibility services can group them accordingly:

Similarly, using the clickable modifier also causes a composable to merge its descendants' semantics into a single entity, which is sent to accessibility services with a corresponding action representation:

Row( // Uses `mergeDescendants = true` under the hood modifier = Modifier.clickable { openArticle() } ) { Icon( painter = painterResource(R.drawable.ic_logo), contentDescription = "Open", ) Text("Accessibility in Compose") }AccessibilitySnippets.kt

You can also set a specific onClickLabel on the parent clickable to provide additional information to accessibility services and offer a more polished representation of the action:

Row( modifier = Modifier .clickable(onClickLabel = "Open this article") { openArticle() } ) { Icon( painter = painterResource(R.drawable.ic_logo), contentDescription = "Open" ) Text("Accessibility in Compose") }AccessibilitySnippets.kt

Using TalkBack as an example, this clickable modifier and its click label would enable TalkBack to provide an action hint of "Double tap to open this article", rather than the more generic default feedback of "Double tap to activate".

This feedback changes depending on the type of action. A long click would provide a TalkBack hint of "Double tap and hold to", followed by a label:

Row( modifier = Modifier .combinedClickable( onLongClickLabel = "Bookmark this article", onLongClick = { addToBookmarks() }, onClickLabel = "Open this article", onClick = { openArticle() }, ) ) {}AccessibilitySnippets.kt

In some cases, you may not have direct access to the clickable modifier (for example, when it's set somewhere in a lower nested layer),but still want to change the announcement label from the default. To do this, split setting the clickable from modifying the announcement by using the semantics modifier and setting the click label there, to modify the action representation:

@Composable private fun ArticleList(openArticle: () -> Unit) { NestedArticleListItem( // Clickable is set separately, in a nested layer: onClickAction = openArticle, // Semantics are set here: modifier = Modifier.semantics { onClick( label = "Open this article", action = { // Not needed here: openArticle() true } ) } ) }AccessibilitySnippets.kt

You don't need to pass the click action twice. Existing Compose APIs, such as clickable or Button, handle this for you. The merging logic verifies that the outermost modifier label and action are taken for the information that is present. In the previous example, the NestedArticleListItem automatically passes the openArticle() click action to its clickable semantics. You can leave the click action null in the second semantics modifier action. However, the click label is taken from the second semantics modifier onClick(label = "Open this document") because it wasn't present in the first.

You might run into scenarios where you expect children semantics to be merged into a parent one, but that doesn't happen. See Merging and clearing for more in-depth information.

When building a custom component, review the implementation of a similar component in the Material library or other Compose libraries. Then, mimic or modify its accessibility behavior as appropriate. For example, if you replace the Material Checkbox with your own implementation, looking at the existing Checkbox implementation will remind you to add the triStateToggleable modifier, which handles the accessibility properties for the component. Additionally, make heavy use of Foundation modifiers, as these include built-in accessibility considerations and existing Compose practices covered in this section.

You can also find an example of a custom toggle component in the Clear and set semantics section, as well as more detailed information on how to support accessibility in custom components in the API guidelines.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Testing Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/testing

**Contents:**
- Testing Stay organized with collections Save and categorize content based on your preferences.
- Accessibility checks
- Recommended for you

An essential way of testing accessibility is a form of manual testing: by turning accessibility services, like TalkBack or Switch Access, on, and checking if everything works as expected. This provides direct insight into how users with accessibility needs might experience your application.

In conjunction with manual verification, you should also use automated testing to flag any potential issues that could impact user experience as you make continual changes to your app.

Existing Compose testing APIs allow you to write automated tests that interact with semantic elements and to assert the properties defined in your UI.

For automated accessibility testing, you can also use the Accessibility Test Framework—the same underlying framework that powers Accessibility Scanner and accessibility checks in Espresso—to perform some accessibility-related checks automatically, starting with Compose 1.8.0.

To enable the checks, add the ui-test-junit4-accessibility dependency, call enableAccessibilityChecks() in the AndroidComposeTestRule, and trigger an action or tryPerformAccessibilityChecks:

@Rule @JvmField val composeTestRule = createAndroidComposeRule<ComponentActivity>() @Test fun noAccessibilityLabel() { composeTestRule.setContent { Box( modifier = Modifier .size(50.dp, 50.dp) .background(color = Color.Gray) .clickable { } .semantics { contentDescription = "" } ) } composeTestRule.enableAccessibilityChecks() // Any action (such as performClick) will perform accessibility checks too: composeTestRule.onRoot().tryPerformAccessibilityChecks() }AccessibilitySnippets.kt

This specific test fails with an exception and a message that the item may not have a label readable by accessibility services.

Other available checks look for accessibility issues with color contrast, small touch target size, or elements' traversal order.

If you're mixing Views with Compose and you're already using an AccessibilityValidator, or you need to configure one, you can set it in the rule:

@Test fun lowContrastScreen() { composeTestRule.setContent { Box( modifier = Modifier .fillMaxSize() .background(color = Color(0xFFFAFBFC)), contentAlignment = Alignment.Center ) { Text(text = "Hello", color = Color(0xFFB0B1B2)) } } // Optionally, set AccessibilityValidator manually val accessibilityValidator = AccessibilityValidator() .setThrowExceptionFor( AccessibilityCheckResult.AccessibilityCheckResultType.WARNING ) composeTestRule.enableAccessibilityChecks(accessibilityValidator) composeTestRule.onRoot().tryPerformAccessibilityChecks() }AccessibilitySnippets.kt

In combination with manual testing, automated tests using both Compose APIs as well as the Accessibility Test Framework can help you detect possible problems early on in the development process.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Semantics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/semantics

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

## Modify traversal order Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/traversal

**Contents:**
- Modify traversal order Stay organized with collections Save and categorize content based on your preferences.
- Group elements for traversal
- Customize traversal order
- API considerations
- Recommended for you

Traversal order is the order in which accessibility services navigate through UI elements. In a Compose app, elements are arranged in expected reading order, which is usually left-to-right, then top-to-bottom. However, there are some scenarios where Compose might need additional hints to determine the correct reading order.

isTraversalGroup and traversalIndex are semantic properties that let you influence the traversal order for accessibility services in scenarios where Compose's default sorting algorithm is not sufficient. isTraversalGroup identifies semantically important groups that need customization, while traversalIndex adjusts the order of individual elements within those groups. You can use isTraversalGroup alone to signify that all elements within a group should be selected together, or with traversalIndex for further customization.

Use isTraversalGroup and traversalIndex in your app to control screen reader traversal order.

isTraversalGroup is a boolean property that defines whether a semantics node is a traversal group. This type of node is one whose function is to serve as a boundary or border in organizing the node's children.

Setting isTraversalGroup = true on a node means that all children of that node are visited before moving to other elements. You can set isTraversalGroup on non-screen reader focusable nodes, such as columns, rows, or boxes.

The following example uses isTraversalGroup. It emits four text elements. The left two elements belong to one CardBox element, while the right two elements belong to another CardBox element:

// CardBox() function takes in top and bottom sample text. @Composable fun CardBox( topSampleText: String, bottomSampleText: String, modifier: Modifier = Modifier ) { Box(modifier) { Column { Text(topSampleText) Text(bottomSampleText) } } } @Composable fun TraversalGroupDemo() { val topSampleText1 = "This sentence is in " val bottomSampleText1 = "the left column." val topSampleText2 = "This sentence is " val bottomSampleText2 = "on the right." Row { CardBox( topSampleText1, bottomSampleText1 ) CardBox( topSampleText2, bottomSampleText2 ) } }AccessibilitySnippets.kt

The code produces output similar to the following:

Because no semantics have been set, the default behavior of the screen reader is to traverse elements from left to right and top to bottom. Because of this default, TalkBack reads out the sentence fragments in the wrong order:

"This sentence is in" → "This sentence is" → "the left column." → "on the right."

To order the fragments correctly, modify the original snippet to set isTraversalGroup to true:

@Composable fun TraversalGroupDemo2() { val topSampleText1 = "This sentence is in " val bottomSampleText1 = "the left column." val topSampleText2 = "This sentence is" val bottomSampleText2 = "on the right." Row { CardBox( // 1, topSampleText1, bottomSampleText1, Modifier.semantics { isTraversalGroup = true } ) CardBox( // 2, topSampleText2, bottomSampleText2, Modifier.semantics { isTraversalGroup = true } ) } }AccessibilitySnippets.kt

Because isTraversalGroup is set specifically on each CardBox, the CardBox boundaries apply when sorting their elements. In this case, the left CardBox is read first, followed by the right CardBox.

Now, TalkBack reads out the sentence fragments in the correct order:

"This sentence is in" → "the left column." → "This sentence is" → "on the right."

traversalIndex is a float property that lets you customize TalkBack traversal order. If grouping elements together is not enough for TalkBack to work correctly, use traversalIndex in conjunction with isTraversalGroup to further customize screen reader ordering.

The traversalIndex property has the following characteristics:

The following example shows how you can use traversalIndex and isTraversalGroup together.

A clock face is a common scenario where standard traversal ordering does not work. The example in this section is a time picker, where a user can traverse through the numbers on a clock face and select digits for the hour and minute slots.

In the following simplified snippet, there is a CircularLayout in which 12 numbers are drawn, starting with 12 and moving clockwise around the circle:

@Composable fun ClockFaceDemo() { CircularLayout { repeat(12) { hour -> ClockText(hour) } } } @Composable private fun ClockText(value: Int) { Box(modifier = Modifier) { Text((if (value == 0) 12 else value).toString()) } }AccessibilitySnippets.kt

Because the clock face is not read logically with the default left-to-right and top-to-bottom ordering, TalkBack reads the numbers out of order. To rectify this, use the incrementing counter value, as shown in the following snippet:

@Composable fun ClockFaceDemo() { CircularLayout(Modifier.semantics { isTraversalGroup = true }) { repeat(12) { hour -> ClockText(hour) } } } @Composable private fun ClockText(value: Int) { Box(modifier = Modifier.semantics { this.traversalIndex = value.toFloat() }) { Text((if (value == 0) 12 else value).toString()) } }AccessibilitySnippets.kt

To properly set the traversal ordering, first make the CircularLayout a traversal group and set isTraversalGroup = true. Then, as each clock text is drawn onto the layout, set its corresponding traversalIndex to the counter value.

Because the counter value continually increases, each clock value's traversalIndex is larger as numbers are added to the screen—the clock value 0 has a traversalIndex of 0, and the clock value 1 has a traversalIndex of 1. In this way, the order that TalkBack reads them in is set. Now, the numbers inside the CircularLayout are read in the expected order.

Because the traversalIndexes that have been set are only relative to other indexes within the same grouping, the rest of the screen ordering has been preserved. In other words, the semantic changes shown in the preceding code snippet only modify the ordering within the clock face that has isTraversalGroup = true set.

Note that, without setting CircularLayout's semantics to isTraversalGroup = true, the traversalIndex changes still apply. However, without the CircularLayout to bind them, the twelve digits of the clock face are read last, after all other elements on the screen have been visited. This occurs because all other elements have a default traversalIndex of 0f, and the clock text elements are read after all other 0f elements.

Consider the following when using the traversal APIs:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Merging and clearing Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/merging-clearing

**Contents:**
- Merging and clearing Stay organized with collections Save and categorize content based on your preferences.
- Merge semantics
- Clear and set semantics
- Hide semantics
- Breakdown of use cases
- Recommended for you

As accessibility services navigate through elements on the screen, it is important that these elements are grouped, separated, or even hidden at the right granularity. When every single low-level composable in your screen is highlighted independently, users have to interact a lot to move across the screen. If elements merge together too aggressively, users might not understand which elements logically belong together. If there are elements on the screen that are purely decorative, these could be hidden from accessibility services. In these cases, you can use Compose APIs for merging, clearing, and hiding semantics.

When you apply a clickable modifier to a parent composable, Compose automatically merges all children elements under it. To understand how interactive Compose Material and Foundation components use merging strategies by default, see the Interactive elements section.

It's common for a component to consist of multiple composables. These composables could form a logical group and each could contain important information, but you still might want accessibility services to view them as one element.

For example, think of a composable that shows a user's avatar, their name, and some extra information:

You can enable Compose to merge these elements by using the mergeDescendants parameter in the semantics modifier. This way, accessibility services treat the component as one entity, and all semantics properties of the descendants are merged:

@Composable private fun PostMetadata(metadata: Metadata) { // Merge elements below for accessibility purposes Row(modifier = Modifier.semantics(mergeDescendants = true) {}) { Image( imageVector = Icons.Filled.AccountCircle, contentDescription = null // decorative ) Column { Text(metadata.author.name) Text("${metadata.date} • ${metadata.readTimeMinutes} min read") } } }AccessibilitySnippets.kt

Accessibility services now focus on the whole container at once, and merge its contents:

Each semantics property has a defined merging strategy. For example, the ContentDescription property adds all descendant ContentDescription values to a list. You can check the merging strategy of a semantics property by checking its mergePolicy implementation in SemanticsProperties.kt. Properties can take on the parent or child value, merge the values into a list or string, not allow merging at all and throw an exception instead, or any other custom merging strategy.

There are other scenarios where you expect children semantics to be merged into a parent one, but that doesn't happen. In the following example, we have clickable list item parent with children elements, and we might expect the parent merges all of them:

@Composable private fun ArticleListItem( openArticle: () -> Unit, addToBookmarks: () -> Unit, ) { Row(modifier = Modifier.clickable { openArticle() }) { // Merges with parent clickable: Icon( painter = painterResource(R.drawable.ic_logo), contentDescription = "Article thumbnail" ) ArticleDetails() // Defies the merge due to its own clickable: BookmarkButton(onClick = addToBookmarks) } }AccessibilitySnippets.kt

When the user presses the clickable item Row, it opens the article. Nested inside, there is a BookmarkButton to bookmark the article. This nested button shows up as unmerged, while the rest of the children content inside the row is merged:

Some composables are not automatically merged under a parent, by design. A parent cannot merge its children when the children are also merging, either from setting mergeDescendants = true explicitly or by being components which merge themselves, like buttons or clickables. Knowing how certain APIs merge or defy merging can help you debug some potentially unexpected behaviors.

Use merging when children elements constitute a logical and sensible group under their parent. But if the nested children need manual adjusting or removal of their own semantics, other APIs might better suit your needs (for example, clearAndSetSemantics).

If semantic information needs to be completely cleared or overwritten, a powerful API to use is clearAndSetSemantics.

When a component needs its own and its descendant semantics cleared, use this API with an empty lambda. When its semantics must be overwritten, include your new content inside the lambda.

Note that when clearing with an empty lambda, the cleared semantics are not sent to any consumer that uses this information, like accessibility, autofill, or testing. When overwriting content with clearAndSetSemantics{/*semantic information*/}, the new semantics replace all previous semantics of the element and its descendants.

The following is an example of a custom toggle component, represented by an interactable row with an icon and text:

// Developer might intend this to be a toggleable. // Using `clearAndSetSemantics`, on the Row, a clickable modifier is applied, // a custom description is set, and a Role is applied. @Composable fun FavoriteToggle() { val checked = remember { mutableStateOf(true) } Row( modifier = Modifier .toggleable( value = checked.value, onValueChange = { checked.value = it } ) .clearAndSetSemantics { stateDescription = if (checked.value) "Favorited" else "Not favorited" toggleableState = ToggleableState(checked.value) role = Role.Switch }, ) { Icon( imageVector = Icons.Default.Favorite, contentDescription = null // not needed here ) Text("Favorite?") } }AccessibilitySnippets.kt

Although the icon and text have some semantic information, together they don't indicate that this component is a toggleable. Merging is not sufficient because you must provide additional information about the component.

Because the snippet above creates a custom toggle component, you need to add the toggle ability, as well as the stateDescription, toggleableState, and role semantics. This way, the component status and associated action are available—for example, TalkBack announces "Double tap to toggle" instead of "Double tap to activate."

By clearing the original semantics and setting new, more descriptive ones, accessibility services can now see that this is a toggleable component that can alternate state.

When using clearAndSetSemantics, consider the following:

In some scenarios, elements don't need to be sent to accessibility services—perhaps their additional information is redundant for accessibility, or it is purely visually decorative and non-interactive. In these cases, you can hide elements with the hideFromAccessibility API.

In the following examples are components that might need to be hidden: a redundant watermark that spans a component, and a character used to decoratively separate information.

@Composable fun WatermarkExample( watermarkText: String, content: @Composable () -> Unit, ) { Box { WatermarkedContent() // Mark the watermark as hidden to accessibility services. WatermarkText( text = watermarkText, color = Color.Gray.copy(alpha = 0.5f), modifier = Modifier .align(Alignment.BottomEnd) .semantics { hideFromAccessibility() } ) } } @Composable fun DecorativeExample() { Text( modifier = Modifier.semantics { hideFromAccessibility() }, text = "A dot character that is used to decoratively separate information, like •" ) }AccessibilitySnippets.kt

Using hideFromAccessibility here ensures the watermark and decoration are hidden from accessibility services, but still keep their semantics for other use cases, like testing.

The following is a summary of use cases to understand how to clearly differentiate between the previous APIs:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Support user-scalable content Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/scalable-content

**Contents:**
- Support user-scalable content Stay organized with collections Save and categorize content based on your preferences.
- Choose a scaling strategy
  - Scale either all elements or only text elements
  - Recommendations
- Detect scaling gestures in Jetpack Compose
- Example implementations
  - Density scaling
  - Font scaling
  - Shared demo UI
- Tips and considerations

Implement pinch-to-zoom gestures to support scalable content in your app. This is the standard, platform-consistent method for improving accessibility, allowing users to intuitively adjust the size of text and UI elements to fit their needs. Your app can define custom scaling behavior with granular control and contextual behavior that offers an experience that users often discover more quickly than a system-level feature like screen magnification.

The strategies covered in this guide cause the UI to reflow and reorganize to fit the screen's width. This provides a significant accessibility benefit by eliminating the need for horizontal panning and the frustrating "zig-zag" motion that would otherwise be required to read long lines of text.

Further Reading: Research confirms that for users with low vision, reflowing content is significantly more readable and easier to navigate than interfaces that require two-dimensional panning. For more details, see A Comparison of Pan-and-Scan and Reflowable Content on Mobile Devices.

The following table demonstrates the visual effect of each scaling strategy.

Scales everything proportionally. The content reflows to fit its container, so the user doesn't need to pan horizontally to see all content.

Only affects text elements. The overall layout and non-text components stay the same size.

All visual elements: Text, components (buttons, icons), images, and layout spacing (padding, margins)

Now that you've seen the visual differences, the following table helps you weigh the trade-offs and choose the best strategy for your content.

Reading-intensive layouts

Examples: News articles, messaging apps

Density or font scaling

Density scaling is preferred to scale the entire content area, including inline images.

Font scaling is a straightforward alternative if only text needs to be scaled.

Visually structured layouts

Examples: App stores, social media feeds

Preserves the visual relationships between images and text in carousels or grids. The reflowing nature avoids horizontal panning, which would conflict with nested scrolling elements.

To support user-scalable content, you must first detect multi-touch gestures. In Jetpack Compose, you can do this using the Modifier.transformable.

The transformable modifier is a high-level API that provides the zoomChange delta since the last gesture event. This simplifies the state update logic to direct accumulation (for example, scale *= zoomChange), making it ideal for the adaptive scaling strategies covered in this guide.

The following examples show how to implement the density scaling and font scaling strategies.

This approach scales the base density of a UI area. As a result, all layout-based measurements—including padding, spacing, and component sizes—are scaled, as if the screen size or resolution had changed. Because text size also relies on density, it also scales proportionally. This strategy is effective when you want to uniformly enlarge all elements within a specific area, maintaining the overall visual rhythm and proportions of your UI.

private class DensityScalingState( // Note: For accessibility, typical min/max values are ~0.75x and ~3.5x. private val minScale: Float = 0.75f, private val maxScale: Float = 3.5f, private val currentDensity: Density ) { val transformableState = TransformableState { zoomChange, _, _ -> scaleFactor.floatValue = (scaleFactor.floatValue * zoomChange).coerceIn(minScale, maxScale) } val scaleFactor = mutableFloatStateOf(1f) fun scaledDensity(): Density { return Density( currentDensity.density * scaleFactor.floatValue, currentDensity.fontScale ) } } ScalableContentSnippets.kt

This strategy is more targeted, modifying only the fontScale factor. The result is that only text elements grow or shrink, while all other layout components—such as containers, padding, and icons—remain a fixed size. This strategy is well-suited for improving text legibility in reading-intensive apps.

class FontScaleState( // Note: For accessibility, typical min/max values are ~0.75x and ~3.5x. private val minScale: Float = 0.75f, private val maxScale: Float = 3.5f, private val currentDensity: Density ) { val transformableState = TransformableState { zoomChange, _, _ -> scaleFactor.floatValue = (scaleFactor.floatValue * zoomChange).coerceIn(minScale, maxScale) } val scaleFactor = mutableFloatStateOf(1f) fun scaledFont(): Density { return Density( currentDensity.density, currentDensity.fontScale * scaleFactor.floatValue ) } } ScalableContentSnippets.kt

This is the shared DemoCard composable used by both of the preceding examples to highlight the different scaling behaviors.

@Composable private fun DemoCard() { Card( modifier = Modifier .width(360.dp) .padding(16.dp), shape = RoundedCornerShape(12.dp) ) { Column( modifier = Modifier.padding(16.dp), verticalArrangement = Arrangement.spacedBy(16.dp) ) { Text("Demo Card", style = MaterialTheme.typography.headlineMedium) var isChecked by remember { mutableStateOf(true) } Row(verticalAlignment = Alignment.CenterVertically) { Text("Demo Switch", Modifier.weight(1f), style = MaterialTheme.typography.bodyLarge) Switch(checked = isChecked, onCheckedChange = { isChecked = it }) } Row(verticalAlignment = Alignment.CenterVertically) { Icon(Icons.Filled.Person, "Icon", Modifier.size(32.dp)) Spacer(Modifier.width(8.dp)) Text("Demo Icon", style = MaterialTheme.typography.bodyLarge) } Row( Modifier.fillMaxWidth(), horizontalArrangement = Arrangement.SpaceBetween ) { Box( Modifier .width(100.dp) .weight(1f) .height(80.dp) .background(Color.Blue) ) Box( Modifier .width(100.dp) .weight(1f) .height(80.dp) .background(Color.Red) ) } Text( "Demo Text: Lorem ipsum dolor sit amet, consectetur adipiscing elit," + " sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.", style = MaterialTheme.typography.bodyMedium, textAlign = TextAlign.Justify ) } } }ScalableContentSnippets.kt

To create a more polished and accessible experience, consider the following recommendations:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Inspect and debug Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility/inspect-debug

**Contents:**
- Inspect and debug Stay organized with collections Save and categorize content based on your preferences.
- Inspect
- Debug
- Recommended for you

Several tools can help you quickly inspect your content from an accessibility point of view:

Between Compose, the semantics system, and Android accessibility services, you might run into unexpected accessibility behaviors that are difficult to trace. Semantic properties can help you understand why your components are behaving the way that they are.

You can debug accessibility behavior issues with the Layout Inspector in Android Studio, TreeDebug in TalkBack developer settings, or ComposeTestRule's printToLog. All of these tools can provide information about nodes (and their properties) that are exposed to accessibility services by Compose.

The following example uses Layout Inspector to debug a screen with three elements where, with accessibility services on, the first one isn't being selected, and the second one doesn't have any action feedback associated with it. You can examine the semantic properties to find potential issues.

The component tree in Layout Inspector contains information about an element's bounds, parameters, and other semantic information associated with it. In the tree, all three elements are recognized:

The first element has the hideFromAccessibility property applied. This indicates that the element may be marked as hidden somewhere in the semantics tree, or it is obscured by some decorative overlay.

The second element has a focus property, but no onClick like the previous element. Therefore, it might be missing a clickable modifier somewhere, which is why an accessibility service like TalkBack may not be announcing some action signal to the user:

The third text element has all the necessary properties—it is focusable, has an onClick, and other additional semantics applied—which is why it's interpreted as expected.

In this way, you can use debugging tools to investigate why certain announcements or selections aren't performed by accessibility services.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Accessibility in Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/accessibility

**Contents:**
- Accessibility in Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.
- Additional resources

Developing with accessibility in mind means making your apps usable for everyone, including people with accessibility needs, who may use Android devices in many different ways. Compose provides a foundation for building more accessible UIs with its declarative APIs and tools that help make your apps more inclusive.

There are several key and supporting concepts in Compose accessibility:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-11-19 UTC.

---
