# Jetpack-Compose - Layouts

**Pages:** 6

---

## Pager in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/pager

**Contents:**
- Pager in Compose Stay organized with collections Save and categorize content based on your preferences.
- HorizontalPager
- VerticalPager
- Lazy creation
  - Load more pages offscreen
- Scroll to an item in the pager
- Get notified about page state changes
- Add a page indicator
- Apply item scroll effects to content
- Custom page sizes

To flip through content horizontally or vertically, you can use the HorizontalPager and VerticalPager composables. These have similar functions to ViewPager in the view system. By default, HorizontalPager takes up the full screen width and VerticalPager takes up the full height. The pagers also only fling one page at a time. All of these defaults are configurable.

To create a pager that scrolls horizontally left and right, use HorizontalPager:

// Display 10 items val pagerState = rememberPagerState(pageCount = { 10 }) HorizontalPager(state = pagerState) { page -> // Our page content Text( text = "Page: $page", modifier = Modifier.fillMaxWidth() ) }PagerSnippets.kt

To create a pager that scrolls up and down, use VerticalPager: Figure 2. Demo of VerticalPager

// Display 10 items val pagerState = rememberPagerState(pageCount = { 10 }) VerticalPager(state = pagerState) { page -> // Our page content Text( text = "Page: $page", modifier = Modifier.fillMaxWidth() ) }PagerSnippets.kt

Pages in both HorizontalPager and VerticalPager are lazily composed and laid-out when required. As the user scrolls through pages, the composable removes any pages which are no longer required.

By default, the pager only loads the visible pages on-screen. To load more pages offscreen, set beyondBoundsPageCount to a value higher than zero.

To scroll to a certain page in the pager, create a PagerState object using rememberPagerState() and pass it as the state parameter to the pager. You can call PagerState#scrollToPage() on this state, inside a CoroutineScope:

val pagerState = rememberPagerState(pageCount = { 10 }) HorizontalPager(state = pagerState) { page -> // Our page content Text( text = "Page: $page", modifier = Modifier .fillMaxWidth() .height(100.dp) ) } // scroll to page val coroutineScope = rememberCoroutineScope() Button(onClick = { coroutineScope.launch { // Call scroll to on pagerState pagerState.scrollToPage(5) } }, modifier = Modifier.align(Alignment.BottomCenter)) { Text("Jump to Page 5") }PagerSnippets.kt

If you want to animate to the page, use the PagerState#animateScrollToPage() function:

val pagerState = rememberPagerState(pageCount = { 10 }) HorizontalPager(state = pagerState) { page -> // Our page content Text( text = "Page: $page", modifier = Modifier .fillMaxWidth() .height(100.dp) ) } // scroll to page val coroutineScope = rememberCoroutineScope() Button(onClick = { coroutineScope.launch { // Call scroll to on pagerState pagerState.animateScrollToPage(5) } }, modifier = Modifier.align(Alignment.BottomCenter)) { Text("Jump to Page 5") }PagerSnippets.kt

PagerState has three properties with information about pages: currentPage, settledPage, and targetPage.

You can use the snapshotFlow function to observe changes to these variables and react to them. For example, to send an analytics event on each page change, you can do the following:

val pagerState = rememberPagerState(pageCount = { 10 }) LaunchedEffect(pagerState) { // Collect from the a snapshotFlow reading the currentPage snapshotFlow { pagerState.currentPage }.collect { page -> // Do something with each page change, for example: // viewModel.sendPageSelectedEvent(page) Log.d("Page change", "Page changed to $page") } } VerticalPager( state = pagerState, ) { page -> Text(text = "Page: $page") }PagerSnippets.kt

To add an indicator to a page, use the PagerState object to get information about which page is selected out of the number of pages, and draw your custom indicator.

For example, to create a circle indicator, you can repeat the number of circles and change the circle color based on if the page is selected, using pagerState.currentPage:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager( state = pagerState, modifier = Modifier.fillMaxSize() ) { page -> // Our page content Text( text = "Page: $page", ) } Row( Modifier .wrapContentHeight() .fillMaxWidth() .align(Alignment.BottomCenter) .padding(bottom = 8.dp), horizontalArrangement = Arrangement.Center ) { repeat(pagerState.pageCount) { iteration -> val color = if (pagerState.currentPage == iteration) Color.DarkGray else Color.LightGray Box( modifier = Modifier .padding(2.dp) .clip(CircleShape) .background(color) .size(16.dp) ) } }PagerSnippets.kt

A common use case is to use the scroll position to apply effects to your pager items. To find out how far a page is from the selected page, you can use PagerState.currentPageOffsetFraction. You can then apply transformation effects to your content based on the distance from the selected page.

For example, to adjust the opacity of items based on how far they are from the center, change the alpha using Modifier.graphicsLayer on an item inside the pager:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager(state = pagerState) { page -> Card( Modifier .size(200.dp) .graphicsLayer { // Calculate the absolute offset for the current page from the // scroll position. We use the absolute value which allows us to mirror // any effects for both directions val pageOffset = ( (pagerState.currentPage - page) + pagerState .currentPageOffsetFraction ).absoluteValue // We animate the alpha, between 50% and 100% alpha = lerp( start = 0.5f, stop = 1f, fraction = 1f - pageOffset.coerceIn(0f, 1f) ) } ) { // Card content } }PagerSnippets.kt

By default, HorizontalPager and VerticalPager takes up the full width or full height, respectively. You can set the pageSize variable to either have a Fixed, Fill (default), or a custom size calculation.

For example, to set a fixed width page of 100.dp:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager( state = pagerState, pageSize = PageSize.Fixed(100.dp) ) { page -> // page content }PagerSnippets.kt

To size the pages based on the viewport size, use a custom page size calculation. Create a custom PageSize object and divide the availableSpace by three, taking into account the spacing between the items:

private val threePagesPerViewport = object : PageSize { override fun Density.calculateMainAxisPageSize( availableSpace: Int, pageSpacing: Int ): Int { return (availableSpace - 2 * pageSpacing) / 3 } }PagerSnippets.kt

HorizontalPager and VerticalPager both support changing the content padding, which lets you influence the maximum size and alignment of pages.

For example, setting the start padding aligns the pages towards the end:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager( state = pagerState, contentPadding = PaddingValues(start = 64.dp), ) { page -> // page content }PagerSnippets.kt

Setting both the start and end padding to the same value centers the item horizontally:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager( state = pagerState, contentPadding = PaddingValues(horizontal = 32.dp), ) { page -> // page content }PagerSnippets.kt

Setting the end padding aligns the pages towards the start:

val pagerState = rememberPagerState(pageCount = { 4 }) HorizontalPager( state = pagerState, contentPadding = PaddingValues(end = 64.dp), ) { page -> // page content }PagerSnippets.kt

You can set the top and bottom values to achieve similar effects for VerticalPager. The value 32.dp is only used here as an example; you can set each of the padding dimensions to any value.

The default HorizontalPager and VerticalPager composables specify how scrolling gestures work with the pager. However, you can customize and change the defaults such as the pagerSnapDistance or the flingBehavior.

By default, HorizontalPager and VerticalPager set the maximum number of pages that a fling gesture can scroll past to one page at a time. To change this, set pagerSnapDistance on the flingBehavior:

val pagerState = rememberPagerState(pageCount = { 10 }) val fling = PagerDefaults.flingBehavior( state = pagerState, pagerSnapDistance = PagerSnapDistance.atMost(10) ) Column(modifier = Modifier.fillMaxSize()) { HorizontalPager( state = pagerState, pageSize = PageSize.Fixed(200.dp), beyondViewportPageCount = 10, flingBehavior = fling ) { PagerSampleItem(page = it) } }PagerSnippets.kt

This section describes how to create an auto-advancing pager with page indicators in Compose. The collection of items automatically scroll horizontally, but users can also manually swipe between items. If a user interacts with the pager, it stops the automatic progression.

Together, the following snippets create a basic auto-advancing pager implementation with a visual indicator, in which each page renders as a different color:

@Composable fun AutoAdvancePager(pageItems: List<Color>, modifier: Modifier = Modifier) { Box(modifier = Modifier.fillMaxSize()) { val pagerState = rememberPagerState(pageCount = { pageItems.size }) val pagerIsDragged by pagerState.interactionSource.collectIsDraggedAsState() val pageInteractionSource = remember { MutableInteractionSource() } val pageIsPressed by pageInteractionSource.collectIsPressedAsState() // Stop auto-advancing when pager is dragged or one of the pages is pressed val autoAdvance = !pagerIsDragged && !pageIsPressed if (autoAdvance) { LaunchedEffect(pagerState, pageInteractionSource) { while (true) { delay(2000) val nextPage = (pagerState.currentPage + 1) % pageItems.size pagerState.animateScrollToPage(nextPage) } } } HorizontalPager( state = pagerState ) { page -> Text( text = "Page: $page", textAlign = TextAlign.Center, modifier = modifier .fillMaxSize() .background(pageItems[page]) .clickable( interactionSource = pageInteractionSource, indication = LocalIndication.current ) { // Handle page click } .wrapContentSize(align = Alignment.Center) ) } PagerIndicator(pageItems.size, pagerState.currentPage) } }PagerSnippets.kt

@Composable fun PagerIndicator(pageCount: Int, currentPageIndex: Int, modifier: Modifier = Modifier) { Box(modifier = Modifier.fillMaxSize()) { Row( modifier = Modifier .wrapContentHeight() .fillMaxWidth() .align(Alignment.BottomCenter) .padding(bottom = 8.dp), horizontalArrangement = Arrangement.Center ) { repeat(pageCount) { iteration -> val color = if (currentPageIndex == iteration) Color.DarkGray else Color.LightGray Box( modifier = modifier .padding(2.dp) .clip(CircleShape) .background(color) .size(16.dp) ) } } } }PagerSnippets.kt

This video displays the basic auto-advancing pager from the preceding snippets:

Figure 8. An auto-advancing pager with a two second delay between each page progression.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Flow layouts in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/flow

**Contents:**
- Flow layouts in Compose Stay organized with collections Save and categorize content based on your preferences.
- Basic usage
- Features of flow layout
  - Main axis arrangement: horizontal or vertical arrangement
  - Cross axis arrangement
  - Individual item alignment
  - Max items in row or column
  - Item weights
  - Fractional sizing
  - fillMaxColumnWidth() and fillMaxRowHeight()

FlowRow and FlowColumn are composables that are similar to Row and Column, but differ in that items flow into the next line when the container runs out of space. This creates multiple rows or columns. The number of items in a line can also be controlled by setting maxItemsInEachRow or maxItemsInEachColumn. You can often use FlowRow and FlowColumn to build responsive layouts— content will not be cut off if items are too large for one dimension, and using a combination of maxItemsInEach* with Modifier.weight(weight) can help build layouts that fill/expand the width of a row or column when needed.

The typical example is for a chip or filtering UI:

To use FlowRow or FlowColumn, create these composables and place the items inside it that should follow the standard flow:

@Composable private fun FlowRowSimpleUsageExample() { FlowRow(modifier = Modifier.padding(8.dp)) { ChipItem("Price: High to Low") ChipItem("Avg rating: 4+") ChipItem("Free breakfast") ChipItem("Free cancellation") ChipItem("£50 pn") } }FlowLayoutSnippets.kt

This snippet results in the UI shown above, with items automatically flowing to the next row when there is no more space in the first row.

Flow layouts have the following features and properties that you can use to create different layouts in your app.

The main axis is the axis on which items are laid out (for example, in FlowRow, items are arranged horizontally). The horizontalArrangement parameter in FlowRow controls the way free space is distributed between items.

The following table shows examples of setting horizontalArrangement on items for FlowRow:

Horizontal arrangement set on FlowRow

Arrangement.Start (Default)

Arrangement.SpaceBetween

Arrangement.SpaceAround

Arrangement.spacedBy(8.dp)

For FlowColumn, similar options are available with verticalArrangement, with the default of Arrangement.Top.

The cross axis is the axis in the opposite direction to the main axis. For example, in FlowRow, this is the vertical axis. To change how the overall contents inside the container are arranged in the cross axis, use verticalArrangement for FlowRow, and horizontalArrangement for FlowColumn.

For FlowRow, the following table shows examples of setting different verticalArrangement on the items:

Vertical arrangement set on FlowRow

Arrangement.Top (Default)

For FlowColumn, similar options are available with horizontalArrangement. The default cross axis arrangement is Arrangement.Start.

You may want to position individual items within the row with different alignments. This is different from verticalArrangement and horizontalArrangement as it aligns items within the current line. You can apply this with Modifier.align().

For example, when items in a FlowRow are different heights, the row takes the height of the biggest item and applies Modifier.align(alignmentOption) to the items:

Vertical alignment set on FlowRow

Alignment.Top (Default)

Alignment.CenterVertically

For FlowColumn, similar options are available. The default alignment is Alignment.Start.

The parameters maxItemsInEachRow or maxItemsInEachColumn define the maximum items in the main axis to allow in one line before wrapping to the next. The default is Int.MAX_INT, which allows as many items as possible, as long as their sizes allow them to fit into the line.

For example, setting a maxItemsInEachRow forces the initial layout to only have 3 items:

maxItemsInEachRow = 3

Weight grows an item based on its factor and the available space on the line it was placed in. Importantly, there is a difference between FlowRow and Row with how weights are used to calculate the width of an item. For Rows, weight is based on all items in the Row. With FlowRow, weight is based on the items in the line that an item is placed in, not all the items in the FlowRow container.

For example, if you have 4 items that all fall on a line, each with different weights of 1f, 2f, 1f, and 3f, the total weight is 7f. The remaining space in a row or column will be divided by 7f. Then, each item width will be calculated using: weight * (remainingSpace / totalWeight).

You can use a combination of Modifier.weight and max items with FlowRow or FlowColumn to create a grid-like layout. This approach is useful for creating responsive layouts that adjust to the sizing of your device.

There are a few different examples of what you can achieve using weights. One example is a grid where items are equally sized, as shown below: Figure 2. Using FlowRow to create a grid

To create a grid of equal item sizes, you can do the following:

val rows = 3 val columns = 3 FlowRow( modifier = Modifier.padding(4.dp), horizontalArrangement = Arrangement.spacedBy(4.dp), maxItemsInEachRow = rows ) { val itemModifier = Modifier .padding(4.dp) .height(80.dp) .weight(1f) .clip(RoundedCornerShape(8.dp)) .background(MaterialColors.Blue200) repeat(rows * columns) { Spacer(modifier = itemModifier) } }FlowLayoutSnippets.kt

Importantly, if you add another item and repeat it 10 times instead of 9, the last item takes up the entire last column, as the total weight for the whole row is 1f:

You can combine weights with other Modifiers such as Modifier.width(exactDpAmount), Modifier.aspectRatio(aspectRatio), or Modifier.fillMaxWidth(fraction). These modifiers all work in conjunction to allow for responsive sizing of items within a FlowRow (or FlowColumn).

You can also create an alternating grid of different item sizes, where two items take up half the width each, and one item takes up the full width of the next column:

You can achieve this with the following code:

FlowRow( modifier = Modifier.padding(4.dp), horizontalArrangement = Arrangement.spacedBy(4.dp), maxItemsInEachRow = 2 ) { val itemModifier = Modifier .padding(4.dp) .height(80.dp) .clip(RoundedCornerShape(8.dp)) .background(Color.Blue) repeat(6) { item -> // if the item is the third item, don't use weight modifier, but rather fillMaxWidth if ((item + 1) % 3 == 0) { Spacer(modifier = itemModifier.fillMaxWidth()) } else { Spacer(modifier = itemModifier.weight(0.5f)) } } }FlowLayoutSnippets.kt

Using Modifier.fillMaxWidth(fraction), you can specify the size of the container that an item should take up. This is different from how Modifier.fillMaxWidth(fraction) works when applied to Row or Column, in that Row/Column items take up a percentage of the remaining width, rather than the whole container's width.

For example, the following code produces different results when using FlowRow vs Row:

FlowRow( modifier = Modifier.padding(4.dp), horizontalArrangement = Arrangement.spacedBy(4.dp), maxItemsInEachRow = 3 ) { val itemModifier = Modifier .clip(RoundedCornerShape(8.dp)) Box( modifier = itemModifier .height(200.dp) .width(60.dp) .background(Color.Red) ) Box( modifier = itemModifier .height(200.dp) .fillMaxWidth(0.7f) .background(Color.Blue) ) Box( modifier = itemModifier .height(200.dp) .weight(1f) .background(Color.Magenta) ) }FlowLayoutSnippets.kt

FlowRow: Middle item with 0.7 fraction of whole container width.

Row: Middle item taking up 0.7 percent of remaining Row width.

Applying either Modifier.fillMaxColumnWidth() or Modifier.fillMaxRowHeight() to an item inside a FlowColumn or FlowRow ensures that items in the same column or row take up the same width or height as the biggest item in the column/row.

For example, this example uses FlowColumn to display the list of Android desserts. You can see the difference in each items widths when Modifier.fillMaxColumnWidth() is applied to the items versus when its not and the items wrap.

FlowColumn( Modifier .padding(20.dp) .fillMaxHeight() .fillMaxWidth(), horizontalArrangement = Arrangement.spacedBy(8.dp), verticalArrangement = Arrangement.spacedBy(8.dp), maxItemsInEachColumn = 5, ) { repeat(listDesserts.size) { Box( Modifier .fillMaxColumnWidth() .border(1.dp, Color.DarkGray, RoundedCornerShape(8.dp)) .padding(8.dp) ) { Text( text = listDesserts[it], fontSize = 18.sp, modifier = Modifier.padding(3.dp) ) } } }FlowLayoutSnippets.kt

Modifier.fillMaxColumnWidth() applied to each item

No width changes set (wrapping items)

Except as otherwise noted, the content of this page is licensed under the Creative Commons Attribution 4.0 License, and code samples are licensed under the Apache 2.0 License. For details, see the Google Developers Site Policies. Java is a registered trademark of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Intrinsic measurements in Compose layouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/intrinsic-measurements

**Contents:**
- Intrinsic measurements in Compose layouts Stay organized with collections Save and categorize content based on your preferences.
- Intrinsics in action
  - Intrinsics in your custom layouts
- Recommended for you

One of the rules of Compose is that you should only measure your children once; measuring children twice throws a runtime exception. However, there are times when you need some information about your children before measuring them.

Intrinsics lets you query children before they're actually measured.

To a composable, you can ask for its IntrinsicSize.Min or IntrinsicSize.Max:

For example, if you ask the minIntrinsicHeight of a Text with infinite width constraints in a custom layout, it returns the height of the Text with the text drawn in a single line.

You can create a composable that displays two texts on the screen separated by a divider:

To do this, use a Row with two Text composables that fill the available space, and a Divider in the middle. The Divider should be as tall as the tallest Text, and it should be thin (width = 1.dp).

@Composable fun TwoTexts(modifier: Modifier = Modifier, text1: String, text2: String) { Row(modifier = modifier) { Text( modifier = Modifier .weight(1f) .padding(start = 4.dp) .wrapContentWidth(Alignment.Start), text = text1 ) VerticalDivider( color = Color.Black, modifier = Modifier.fillMaxHeight().width(1.dp) ) Text( modifier = Modifier .weight(1f) .padding(end = 4.dp) .wrapContentWidth(Alignment.End), text = text2 ) } }IntrinsicSnippets.kt

The Divider expands to the whole screen, which isn't the desired behavior:

This happens because Row measures each child individually, and the height of Text cannot be used to constrain the Divider.

To have the Divider fill the available space with a given height instead, use the height(IntrinsicSize.Min) modifier.

height(IntrinsicSize.Min) sizes its children to be as tall as their minimum intrinsic height. Because this modifier is recursive, it queries the minIntrinsicHeight of the Row and its children.

Applying this modifier to your code makes it work as expected:

@Composable fun TwoTexts(modifier: Modifier = Modifier, text1: String, text2: String) { Row(modifier = modifier.height(IntrinsicSize.Min)) { Text( modifier = Modifier .weight(1f) .padding(start = 4.dp) .wrapContentWidth(Alignment.Start), text = text1 ) VerticalDivider( color = Color.Black, modifier = Modifier.fillMaxHeight().width(1.dp) ) Text( modifier = Modifier .weight(1f) .padding(end = 4.dp) .wrapContentWidth(Alignment.End), text = text2 ) } } // @Preview @Composable fun TwoTextsPreview() { MaterialTheme { Surface { TwoTexts(text1 = "Hi", text2 = "there") } } }IntrinsicSnippets.kt

The Row's height is determined as follows:

When creating a custom Layout or layout modifier, intrinsic measurements are calculated automatically based on approximations. Therefore, the calculations might not be correct for all layouts. These APIs offer options to override these defaults.

To specify the intrinsics measurements of your custom Layout, override the minIntrinsicWidth, minIntrinsicHeight, maxIntrinsicWidth, and maxIntrinsicHeight of the MeasurePolicy interface when creating it.

@Composable fun MyCustomComposable( modifier: Modifier = Modifier, content: @Composable () -> Unit ) { Layout( content = content, modifier = modifier, measurePolicy = object : MeasurePolicy { override fun MeasureScope.measure( measurables: List<Measurable>, constraints: Constraints ): MeasureResult { // Measure and layout here // ... } override fun IntrinsicMeasureScope.minIntrinsicWidth( measurables: List<IntrinsicMeasurable>, height: Int ): Int { // Logic here // ... } // Other intrinsics related methods have a default value, // you can override only the methods that you need. } ) }IntrinsicSnippets.kt

When creating your custom layout modifier, override the related methods in the LayoutModifier interface.

fun Modifier.myCustomModifier(/* ... */) = this then object : LayoutModifier { override fun MeasureScope.measure( measurable: Measurable, constraints: Constraints ): MeasureResult { // Measure and layout here // ... } override fun IntrinsicMeasureScope.minIntrinsicWidth( measurable: IntrinsicMeasurable, height: Int ): Int { // Logic here // ... } // Other intrinsics related methods have a default value, // you can override only the methods that you need. }IntrinsicSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Custom layouts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/custom

**Contents:**
- Custom layouts Stay organized with collections Save and categorize content based on your preferences.
- Use the layout modifier
- Create custom layouts
- Layout direction
- Custom layouts in action
- Learn more
  - Videos
- Recommended for you

In Compose, UI elements are represented by the composable functions that emit a piece of UI when invoked, that is then added to a UI tree that gets rendered on the screen. Each UI element has one parent and potentially many children. Each element is also located within its parent, specified as an (x, y) position, and a size, specified as a width and a height.

Parents define the constraints for their child elements. An element is asked to define its size within those constraints. Constraints restrict the minimum and maximum width and height of an element. If an element has child elements, it may measure each child to help determine its size. Once an element determines and reports its own size, it has an opportunity to define how to place its child elements relative to itself, as described in detail in Creating custom layouts.

Laying out each node in the UI tree is a three step process. Each node must:

The use of scopes defines when you can measure and place your children. Measuring a layout can only be done during the measurement and layout passes, and a child can only be placed during the layout passes (and only after it has been measured). Due to Compose scopes such as MeasureScope, and PlacementScope, this is enforced at compile time.

You can use the layout modifier to modify how an element is measured and laid out. Layout is a lambda; its parameters include the element you can measure, passed as measurable, and that composable's incoming constraints, passed as constraints. A custom layout modifier can look like this:

fun Modifier.customLayoutModifier() = layout { measurable, constraints -> // ... }CustomLayoutSnippets.kt

Display a Text on the screen and control the distance from the top to the baseline of the first line of text. This is exactly what the paddingFromBaseline modifier does; you're implementing it here as an example. To do that, use the layout modifier to manually place the composable on the screen. Here's the resulting behavior where the Text top padding is set to 24.dp:

Here's the code to produce that spacing:

fun Modifier.firstBaselineToTop( firstBaselineToTop: Dp ) = layout { measurable, constraints -> // Measure the composable val placeable = measurable.measure(constraints) // Check the composable has a first baseline check(placeable[FirstBaseline] != AlignmentLine.Unspecified) val firstBaseline = placeable[FirstBaseline] // Height of the composable with padding - first baseline val placeableY = firstBaselineToTop.roundToPx() - firstBaseline val height = placeable.height + placeableY layout(placeable.width, height) { // Where the composable gets placed placeable.placeRelative(0, placeableY) } }CustomLayoutSnippets.kt

Here's what's going on in that code:

To verify this works as expected, use this modifier on a Text:

@Preview @Composable fun TextWithPaddingToBaselinePreview() { MyApplicationTheme { Text("Hi there!", Modifier.firstBaselineToTop(32.dp)) } } @Preview @Composable fun TextWithNormalPaddingPreview() { MyApplicationTheme { Text("Hi there!", Modifier.padding(top = 32.dp)) } }CustomLayoutSnippets.kt

The layout modifier only changes the calling composable. To measure and layout multiple composables, use the Layout composable instead. This composable lets you measure and lay out children manually. All higher-level layouts like Column and Row are built with the Layout composable.

This example builds a very basic version of Column. Most custom layouts follow this pattern:

@Composable fun MyBasicColumn( modifier: Modifier = Modifier, content: @Composable () -> Unit ) { Layout( modifier = modifier, content = content ) { measurables, constraints -> // measure and position children given constraints logic here // ... } }CustomLayoutSnippets.kt

Similarly to the layout modifier, measurables is the list of children that need to be measured and constraints are the constraints from the parent. Following the same logic as before, MyBasicColumn can be implemented like this:

@Composable fun MyBasicColumn( modifier: Modifier = Modifier, content: @Composable () -> Unit ) { Layout( modifier = modifier, content = content ) { measurables, constraints -> // Don't constrain child views further, measure them with given constraints // List of measured children val placeables = measurables.map { measurable -> // Measure each children measurable.measure(constraints) } // Set the size of the layout as big as it can layout(constraints.maxWidth, constraints.maxHeight) { // Track the y co-ord we have placed children up to var yPosition = 0 // Place children in the parent layout placeables.forEach { placeable -> // Position item on the screen placeable.placeRelative(x = 0, y = yPosition) // Record the y co-ord placed up to yPosition += placeable.height } } } }CustomLayoutSnippets.kt

The child composables are constrained by the Layout constraints (without the minHeight constraints), and they're placed based on the yPosition of the previous composable.

Here's how that custom composable would be used:

@Composable fun CallingComposable(modifier: Modifier = Modifier) { MyBasicColumn(modifier.padding(8.dp)) { Text("MyBasicColumn") Text("places items") Text("vertically.") Text("We've done it by hand!") } }CustomLayoutSnippets.kt

Change the layout direction of a composable by changing the LocalLayoutDirection composition local.

If you're placing composables manually on the screen, the LayoutDirection is part of the LayoutScope of the layout modifier or Layout composable.

When using layoutDirection, place composables using place. Unlike the placeRelative method, place doesn't change based on the layout direction (left-to-right versus right-to-left).

Learn more about layouts and modifiers in the Basic layouts in Compose, and see custom layouts in action in the Compose samples that create custom layouts.

To learn more about custom layouts in Compose, consult the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Compose layout basics Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/basics

**Contents:**
- Compose layout basics Stay organized with collections Save and categorize content based on your preferences.
- Goals of layouts in Compose
- Basics of composable functions
- Standard layout components
- The layout model
- Performance
- Using modifiers in your layouts
- Scrollable layouts
- Responsive layouts
  - Constraints

Jetpack Compose makes it much easier to design and build your app's UI. Compose transforms state into UI elements, via:

This document focuses on the layout of elements, explaining some of the building blocks Compose provides to help you lay out your UI elements.

The Jetpack Compose implementation of the layout system has two main goals:

Composable functions are the basic building block of Compose. A composable function is a function emitting Unit that describes some part of your UI. The function takes some input and generates what's shown on the screen. For more information about composables, take a look at the Compose mental model documentation.

A composable function might emit several UI elements. However, if you don't provide guidance on how they should be arranged, Compose might arrange the elements in a way you don't like. For example, this code generates two text elements:

@Composable fun ArtistCard() { Text("Alfred Sisley") Text("3 minutes ago") }LayoutBasicsSnippets.kt

Without guidance on how you want them arranged, Compose stacks the text elements on top of each other, making them unreadable:

Compose provides a collection of ready-to-use layouts to help you arrange your UI elements, and makes it easy to define your own, more-specialized layouts.

In many cases, you can just use Compose's standard layout elements.

Use Column to place items vertically on the screen.

@Composable fun ArtistCardColumn() { Column { Text("Alfred Sisley") Text("3 minutes ago") } }LayoutBasicsSnippets.kt

Similarly, use Row to place items horizontally on the screen. Both Column and Row support configuring the alignment of the elements they contain.

@Composable fun ArtistCardRow(artist: Artist) { Row(verticalAlignment = Alignment.CenterVertically) { Image(bitmap = artist.image, contentDescription = "Artist image") Column { Text(artist.name) Text(artist.lastSeenOnline) } } } LayoutBasicsSnippets.kt

Use Box to put elements on top of another. Box also supports configuring specific alignment of the elements it contains.

@Composable fun ArtistAvatar(artist: Artist) { Box { Image(bitmap = artist.image, contentDescription = "Artist image") Icon(Icons.Filled.Check, contentDescription = "Check mark") } }LayoutBasicsSnippets.kt

Often these building blocks are all you need. You can write your own composable function to combine these layouts into a more elaborate layout that suits your app.

To set children's position within a Row, set the horizontalArrangement and verticalAlignment arguments. For a Column, set the verticalArrangement and horizontalAlignment arguments:

@Composable fun ArtistCardArrangement(artist: Artist) { Row( verticalAlignment = Alignment.CenterVertically, horizontalArrangement = Arrangement.End ) { Image(bitmap = artist.image, contentDescription = "Artist image") Column { /*...*/ } } }LayoutBasicsSnippets.kt

In the layout model, the UI tree is laid out in a single pass. Each node is first asked to measure itself, then measure any children recursively, passing size constraints down the tree to children. Then, leaf nodes are sized and placed, with the resolved sizes and placement instructions passed back up the tree.

Briefly, parents measure before their children, but are sized and placed after their children.

Consider the following SearchResult function.

@Composable fun SearchResult() { Row { Image( // ... ) Column { Text( // ... ) Text( // ... ) } } }LayoutBasicsSnippets.kt

This function yields the following UI tree.

In the SearchResult example, the UI tree layout follows this order:

Compose achieves high performance by measuring children only once. Single-pass measurement is good for performance, allowing Compose to efficiently handle deep UI trees. If an element measured its child twice and that child measured each of its children twice and so on, a single attempt to lay out a whole UI would have to do a lot of work, making it hard to keep your app performant.

If your layout needs multiple measurements for some reason, Compose offers a special system, intrinsic measurements. You can read more about this feature in Intrinsic measurements in Compose layouts.

Since measurement and placement are distinct sub-phases of the layout pass, any changes that only affects placement of items, not measurement, can be executed separately.

As discussed in Compose modifiers, you can use modifiers to decorate or augment your composables. Modifiers are essential for customizing your layout. For example, here we chain several modifiers to customize the ArtistCard:

@Composable fun ArtistCardModifiers( artist: Artist, onClick: () -> Unit ) { val padding = 16.dp Column( Modifier .clickable(onClick = onClick) .padding(padding) .fillMaxWidth() ) { Row(verticalAlignment = Alignment.CenterVertically) { /*...*/ } Spacer(Modifier.size(padding)) Card( elevation = CardDefaults.cardElevation(defaultElevation = 4.dp), ) { /*...*/ } } }LayoutBasicsSnippets.kt

In the code above, notice different modifier functions used together.

Learn more about scrollable layouts in the Compose gestures documentation.

For lists and lazy lists, check out the Compose lists documentation.

A layout should be designed with consideration of different screen orientations and form factor sizes. Compose offers out of the box a few mechanisms to facilitate adapting your composable layouts to various screen configurations.

In order to know the constraints coming from the parent and design the layout accordingly, you can use a BoxWithConstraints. The measurement constraints can be found in the scope of the content lambda. You can use these measurement constraints to compose different layouts for different screen configurations:

@Composable fun WithConstraintsComposable() { BoxWithConstraints { Text("My minHeight is $minHeight while my maxWidth is $maxWidth") } }LayoutBasicsSnippets.kt

Compose provides a large variety of composables based on Material Design with the androidx.compose.material:material dependency (included when creating a Compose project in Android Studio) to make UI building easy. Elements like Drawer, FloatingActionButton, and TopAppBar are all provided.

Material components make heavy use of slot APIs, a pattern Compose introduces to bring in a layer of customization on top of composables. This approach makes components more flexible, as they accept a child element which can configure itself rather than having to expose every configuration parameter of the child. Slots leave an empty space in the UI for the developer to fill as they wish. For example, these are the slots that you can customize in a TopAppBar:

Composables usually take a content composable lambda ( content: @Composable () -> Unit). Slot APIs expose multiple content parameters for specific uses. For example, TopAppBar allows you to provide the content for title, navigationIcon, and actions.

For example, Scaffold allows you to implement a UI with the basic Material Design layout structure. Scaffoldprovides slots for the most common top-level Material components, such as TopAppBar, BottomAppBar, FloatingActionButton, and Drawer. By using Scaffold, it's easy to make sure these components are properly positioned and work together correctly.

@Composable fun HomeScreen(/*...*/) { ModalNavigationDrawer(drawerContent = { /* ... */ }) { Scaffold( topBar = { /*...*/ } ) { contentPadding -> // ... } } }LayoutBasicsSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (unknown):
```unknown
SearchResult
  Row
    Image
    Column
      Text
      Text
```

---

## ConstraintLayout in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/constraintlayout

**Contents:**
- ConstraintLayout in Compose Stay organized with collections Save and categorize content based on your preferences.
- Get started with ConstraintLayout
- Decoupled API
- ConstraintLayout concepts
  - Guidelines
  - Barriers
  - Chains
- Learn more
- Recommended for you

ConstraintLayout is a layout that lets you place composables relative to other composables on the screen. It is an alternative to using multiple nested Row, Column, Box, and other custom layout elements. ConstraintLayout is useful when implementing larger layouts with more complicated alignment requirements.

Consider using ConstraintLayout in the following scenarios:

In the View system, ConstraintLayout was the recommended way to create large and complex layouts, as a flat view hierarchy was better for performance than nested views are. However, this is not a concern in Compose, which is able to efficiently handle deep layout hierarchies.

To use ConstraintLayout in Compose, you need to add this dependency in your build.gradle (in addition to the Compose setup):

ConstraintLayout in Compose works in the following way using a DSL:

Here's an example of a composable using a ConstraintLayout:

@Composable fun ConstraintLayoutContent() { ConstraintLayout { // Create references for the composables to constrain val (button, text) = createRefs() Button( onClick = { /* Do something */ }, // Assign reference "button" to the Button composable // and constrain it to the top of the ConstraintLayout modifier = Modifier.constrainAs(button) { top.linkTo(parent.top, margin = 16.dp) } ) { Text("Button") } // Assign reference "text" to the Text composable // and constrain it to the bottom of the Button composable Text( "Text", Modifier.constrainAs(text) { top.linkTo(button.bottom, margin = 16.dp) } ) } }ConstraintLayoutSnippets.kt

This code constrains the top of the Button to the parent with a margin of 16.dp and a Text to the bottom of the Button also with a margin of 16.dp.

In the ConstraintLayout example, constraints are specified inline, with a modifier in the composable they're applied to. However, there are situations when it's preferable to decouple the constraints from the layouts they apply to. For example, you might want to change the constraints based on the screen configuration, or animate between two constraint sets.

For cases like these, you can use ConstraintLayout in a different way:

@Composable fun DecoupledConstraintLayout() { BoxWithConstraints { val constraints = if (minWidth < 600.dp) { decoupledConstraints(margin = 16.dp) // Portrait constraints } else { decoupledConstraints(margin = 32.dp) // Landscape constraints } ConstraintLayout(constraints) { Button( onClick = { /* Do something */ }, modifier = Modifier.layoutId("button") ) { Text("Button") } Text("Text", Modifier.layoutId("text")) } } } private fun decoupledConstraints(margin: Dp): ConstraintSet { return ConstraintSet { val button = createRefFor("button") val text = createRefFor("text") constrain(button) { top.linkTo(parent.top, margin = margin) } constrain(text) { top.linkTo(button.bottom, margin) } } }ConstraintLayoutSnippets.kt

Then, when you need to change the constraints, you can just pass a different ConstraintSet.

ConstraintLayout contains concepts such as guidelines, barriers, and chains that can help with positioning elements inside your composable.

Guidelines are small visual helpers to design layouts with. Composables can be constrained to a guideline. Guidelines are useful for positioning elements at a certain dp or percentage inside the parent composable.

There are two different kinds of guidelines, vertical and horizontal. The two horizontal ones are top and bottom, and the two vertical are start and end.

ConstraintLayout { // Create guideline from the start of the parent at 10% the width of the Composable val startGuideline = createGuidelineFromStart(0.1f) // Create guideline from the end of the parent at 10% the width of the Composable val endGuideline = createGuidelineFromEnd(0.1f) // Create guideline from 16 dp from the top of the parent val topGuideline = createGuidelineFromTop(16.dp) // Create guideline from 16 dp from the bottom of the parent val bottomGuideline = createGuidelineFromBottom(16.dp) }ConstraintLayoutSnippets.kt

To create a guideline, use createGuidelineFrom* with the type of guideline required. This creates a reference that can be used in the Modifier.constrainAs() block.

Barriers reference multiple composables to create a virtual guideline based on the most extreme widget on the specified side.

To create a barrier, use createTopBarrier() (or: createBottomBarrier(), createEndBarrier(), createStartBarrier()), and provide the references that should make up the barrier.

ConstraintLayout { val constraintSet = ConstraintSet { val button = createRefFor("button") val text = createRefFor("text") val topBarrier = createTopBarrier(button, text) } }ConstraintLayoutSnippets.kt

The barrier can then be used in a Modifier.constrainAs() block.

Chains provide group-like behavior in a single axis (horizontally or vertically). The other axis can be constrained independently.

To create a chain, use either createVerticalChain or createHorizontalChain:

ConstraintLayout { val constraintSet = ConstraintSet { val button = createRefFor("button") val text = createRefFor("text") val verticalChain = createVerticalChain(button, text, chainStyle = ChainStyle.Spread) val horizontalChain = createHorizontalChain(button, text) } }ConstraintLayoutSnippets.kt

The chain can then be used in the Modifier.constrainAs() block.

A chain can be configured with different ChainStyles, which decide how to deal with the space surrounding a composable, such as:

Learn more about ConstraintLayout in Compose from the APIs in action in the Compose samples that use ConstraintLayout.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (bash):
```bash
implementation "androidx.constraintlayout:constraintlayout-compose:$constraintlayout_compose_version"
```

---
