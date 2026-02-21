# Jetpack-Compose - Fundamentals

**Pages:** 22

---

## Compose phases and performance Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/performance/phases

**Contents:**
- Compose phases and performance Stay organized with collections Save and categorize content based on your preferences.
- General principles
- Additional Resources

When Compose updates a frame, it goes through three phases:

Compose can intelligently skip any of those phases if they aren't needed. For example, suppose a single graphic element swaps between two icons of the same size. Since this element isn't changing size, and no elements of the UI tree are being added or removed, Compose can skip over the composition and layout phases and redraw this one element.

However, coding mistakes can make it hard for Compose to know which phases it can safely skip, in which case Compose runs all three phases, which can slow down your UI. So, many of the performance best practices are to help Compose skip the phases it doesn't need to do.

For more information, see the Jetpack Compose Phases guide.

There are a couple of broad principles to follow that can improve performance in general:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-04-11 UTC.

---

## Graphics modifiers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/draw/modifiers

**Contents:**
- Graphics modifiers Stay organized with collections Save and categorize content based on your preferences.
- Drawing modifiers
  - Modifier.drawWithContent: Choose drawing order
  - Modifier.drawBehind: Drawing behind a composable
  - Modifier.drawWithCache: Drawing and caching draw objects
- Graphics modifiers
  - Modifier.graphicsLayer: Apply transformations to composables
    - Transformations
      - Scale - increase size
      - Translation

In addition to the Canvas composable, Compose has several useful graphics Modifiers which aid in drawing custom content. These modifiers are useful because they can be applied to any composable.

All drawing commands are done with a drawing modifier in Compose. There are three main drawing modifiers in Compose:

The base modifier for drawing is drawWithContent, where you can decide the drawing order of your Composable and the drawing commands issued inside the modifier. drawBehind is a convenient wrapper around drawWithContent which has the drawing order set to behind the content of the composable. drawWithCache calls either onDrawBehind or onDrawWithContent inside of it - and provides a mechanism for caching the objects created in them.

Modifier.drawWithContent lets you execute DrawScope operations before or after the content of the composable. Be sure to call drawContent to then render the actual content of the composable. With this modifier, you can decide the order of operations, if you want your content to be drawn before or after your custom drawing operations.

For example, if you wished to render a radial gradient on top of your content to create a flashlight keyhole effect on the UI, you could do the following:

var pointerOffset by remember { mutableStateOf(Offset(0f, 0f)) } Column( modifier = Modifier .fillMaxSize() .pointerInput("dragging") { detectDragGestures { change, dragAmount -> pointerOffset += dragAmount } } .onSizeChanged { pointerOffset = Offset(it.width / 2f, it.height / 2f) } .drawWithContent { drawContent() // draws a fully black area with a small keyhole at pointerOffset that’ll show part of the UI. drawRect( Brush.radialGradient( listOf(Color.Transparent, Color.Black), center = pointerOffset, radius = 100.dp.toPx(), ) ) } ) { // Your composables here }GraphicsModifiersSnippets.kt

Modifier.drawBehind lets you perform DrawScope operations behind the composable content that is drawn on screen. If you take a look at the implementation of Canvas, you might notice that it is just a convenient wrapper around Modifier.drawBehind.

To draw a rounded rectangle behind Text:

Text( "Hello Compose!", modifier = Modifier .drawBehind { drawRoundRect( Color(0xFFBBAAEE), cornerRadius = CornerRadius(10.dp.toPx()) ) } .padding(4.dp) )GraphicsModifiersSnippets.kt

Which produces the following result:

Modifier.drawWithCache keeps the objects that are created inside of it cached. The objects are cached as long as the size of the drawing area is the same, or any state objects that are read have not changed. This modifier is useful for improving performance of drawing calls as it avoids the need to reallocate objects (such as: Brush, Shader, Path etc.) that are created on draw.

Alternatively, you could also cache objects using remember, outside of the modifier. However, this is not always possible as you don't always have access to the composition. It can be more performant to use drawWithCache if the objects are only used for drawing.

For example, if you create a Brush to draw a gradient behind a Text, using drawWithCache caches the Brush object until the size of the drawing area changes:

Text( "Hello Compose!", modifier = Modifier .drawWithCache { val brush = Brush.linearGradient( listOf( Color(0xFF9E82F0), Color(0xFF42A5F5) ) ) onDrawBehind { drawRoundRect( brush, cornerRadius = CornerRadius(10.dp.toPx()) ) } } )GraphicsModifiersSnippets.kt

Modifier.graphicsLayer is a modifier that makes the content of the composable draw into a draw layer. A layer provides a few different functions, such as:

Modifier.graphicsLayer provides isolation for its drawing instructions; for instance, various transformations can be applied using Modifier.graphicsLayer. These can be animated or modified without needing to re-execute the drawing lambda.

Modifier.graphicsLayer does not change the measured size or placement of your composable, as it only affects the draw phase. This means that your composable might overlap others if it ends up drawing outside of its layout bounds.

The following transformations can be applied with this modifier:

scaleX and scaleY enlarges or shrinks content in the horizontal or vertical direction, respectively. A value of 1.0f indicates no change in scale, a value of 0.5f means half of the dimension.

Image( painter = painterResource(id = R.drawable.sunset), contentDescription = "Sunset", modifier = Modifier .graphicsLayer { this.scaleX = 1.2f this.scaleY = 0.8f } )GraphicsModifiersSnippets.kt

translationX and translationY can be changed with graphicsLayer, translationX moves the composable left or right. translationY moves the composable up or down.

Image( painter = painterResource(id = R.drawable.sunset), contentDescription = "Sunset", modifier = Modifier .graphicsLayer { this.translationX = 100.dp.toPx() this.translationY = 10.dp.toPx() } )GraphicsModifiersSnippets.kt

Set rotationX to rotate horizontally, rotationY to rotate vertically and rotationZ to rotate on the Z axis (standard rotation). This value is specified in degrees (0-360).

Image( painter = painterResource(id = R.drawable.sunset), contentDescription = "Sunset", modifier = Modifier .graphicsLayer { this.rotationX = 90f this.rotationY = 275f this.rotationZ = 180f } )GraphicsModifiersSnippets.kt

A transformOrigin can be specified. It is then used as the point from which transformations take place. All the examples so far have used TransformOrigin.Center, which is at (0.5f, 0.5f). If you specify the origin at (0f, 0f), the transformations then start from the top-left corner of the composable.

If you change the origin with a rotationZ transformation, you can see that the item rotates around the top left of the composable:

Image( painter = painterResource(id = R.drawable.sunset), contentDescription = "Sunset", modifier = Modifier .graphicsLayer { this.transformOrigin = TransformOrigin(0f, 0f) this.rotationX = 90f this.rotationY = 275f this.rotationZ = 180f } )GraphicsModifiersSnippets.kt

Shape specifies the outline that the content clips to when clip = true. In this example, we set two boxes to have two different clips - one using graphicsLayer clip variable, and the other using the convenient wrapper Modifier.clip.

Column(modifier = Modifier.padding(16.dp)) { Box( modifier = Modifier .size(200.dp) .graphicsLayer { clip = true shape = CircleShape } .background(Color(0xFFF06292)) ) { Text( "Hello Compose", style = TextStyle(color = Color.Black, fontSize = 46.sp), modifier = Modifier.align(Alignment.Center) ) } Box( modifier = Modifier .size(200.dp) .clip(CircleShape) .background(Color(0xFF4DB6AC)) ) }GraphicsModifiersSnippets.kt

The contents of the first box (the text saying “Hello Compose”) are clipped to the circle shape:

If you then apply a translationY to the top pink circle, you see that the bounds of the Composable are still the same, but the circle draws underneath the bottom circle (and outside of its bounds).

To clip the composable to the region it's drawn in, you can add another Modifier.clip(RectangleShape) at the start of the modifier chain. The content then remains inside of the original bounds.

Column(modifier = Modifier.padding(16.dp)) { Box( modifier = Modifier .clip(RectangleShape) .size(200.dp) .border(2.dp, Color.Black) .graphicsLayer { clip = true shape = CircleShape translationY = 50.dp.toPx() } .background(Color(0xFFF06292)) ) { Text( "Hello Compose", style = TextStyle(color = Color.Black, fontSize = 46.sp), modifier = Modifier.align(Alignment.Center) ) } Box( modifier = Modifier .size(200.dp) .clip(RoundedCornerShape(500.dp)) .background(Color(0xFF4DB6AC)) ) }GraphicsModifiersSnippets.kt

Modifier.graphicsLayer can be used to set an alpha (opacity) for the whole layer. 1.0f is fully opaque and 0.0f is invisible.

Image( painter = painterResource(id = R.drawable.sunset), contentDescription = "clock", modifier = Modifier .graphicsLayer { this.alpha = 0.5f } )GraphicsModifiersSnippets.kt

Working with alpha and transparency might not be as simple as changing a single alpha value. In addition to changing an alpha, there is also the option to set a CompositingStrategy on a graphicsLayer. A CompositingStrategy determines how the content of the composable is composited (put together) with the other content already drawn on screen.

The different strategies are:

The compositing strategy is determined by the rest of the graphicsLayer parameters. It renders the layer into an offscreen buffer if alpha is less than 1.0f or a RenderEffect is set. Whenever the alpha is less than 1f, a compositing layer is created automatically to render the contents and then draw this offscreen buffer to the destination with the corresponding alpha. Setting a RenderEffect or overscroll always renders content into an offscreen buffer regardless of the CompositingStrategy set.

The contents of the composable are always rasterized to an offscreen texture or bitmap before rendering to the destination. This is useful for applying BlendMode operations to mask content, and for performance when rendering complex sets of drawing instructions.

An example of using CompositingStrategy.Offscreen is with BlendModes. Taking a look at the example below, say you want to remove parts of an Image composable by issuing a draw command that uses BlendMode.Clear. If you do not set the compositingStrategy to CompositingStrategy.Offscreen, the BlendMode interacts with all the contents below it.

Image( painter = painterResource(id = R.drawable.dog), contentDescription = "Dog", contentScale = ContentScale.Crop, modifier = Modifier .size(120.dp) .aspectRatio(1f) .background( Brush.linearGradient( listOf( Color(0xFFC5E1A5), Color(0xFF80DEEA) ) ) ) .padding(8.dp) .graphicsLayer { compositingStrategy = CompositingStrategy.Offscreen } .drawWithCache { val path = Path() path.addOval( Rect( topLeft = Offset.Zero, bottomRight = Offset(size.width, size.height) ) ) onDrawWithContent { clipPath(path) { // this draws the actual image - if you don't call drawContent, it wont // render anything this@onDrawWithContent.drawContent() } val dotSize = size.width / 8f // Clip a white border for the content drawCircle( Color.Black, radius = dotSize, center = Offset( x = size.width - dotSize, y = size.height - dotSize ), blendMode = BlendMode.Clear ) // draw the red circle indication drawCircle( Color(0xFFEF5350), radius = dotSize * 0.8f, center = Offset( x = size.width - dotSize, y = size.height - dotSize ) ) } } )GraphicsModifiersSnippets.kt

By setting the CompositingStrategy to Offscreen, it creates an offscreen texture to execute the commands to (applying the BlendMode only to the contents of this composable). It then renders it on top of what is already rendered on screen, not affecting the content already drawn.

If you didn't use CompositingStrategy.Offscreen, the results of applying BlendMode.Clear clears all the pixels in the destination, regardless of what was already set– leaving the window’s rendering buffer (black) visible. Many of the BlendModes that involve alpha won't work as expected without an offscreen buffer. Note the black ring around the red circle indicator:

To understand this a bit further: if the app had a translucent window background, and you did not use the CompositingStrategy.Offscreen, the BlendMode would interact with the whole app. It would clear all of the pixels to show the app or wallpaper underneath, as in this example:

It's worth noting that when using CompositingStrategy.Offscreen, an offscreen texture that is the size of the drawing area is created and rendered back on screen. Any drawing commands that are done with this strategy, are by default be clipped to this region. The below code snippet illustrates the differences when switching to using offscreen textures:

@Composable fun CompositingStrategyExamples() { Column( modifier = Modifier .fillMaxSize() .wrapContentSize(Alignment.Center) ) { // Does not clip content even with a graphics layer usage here. By default, graphicsLayer // does not allocate + rasterize content into a separate layer but instead is used // for isolation. That is draw invalidations made outside of this graphicsLayer will not // re-record the drawing instructions in this composable as they have not changed Canvas( modifier = Modifier .graphicsLayer() .size(100.dp) // Note size of 100 dp here .border(2.dp, color = Color.Blue) ) { // ... and drawing a size of 200 dp here outside the bounds drawRect(color = Color.Magenta, size = Size(200.dp.toPx(), 200.dp.toPx())) } Spacer(modifier = Modifier.size(300.dp)) /* Clips content as alpha usage here creates an offscreen buffer to rasterize content into first then draws to the original destination */ Canvas( modifier = Modifier // force to an offscreen buffer .graphicsLayer(compositingStrategy = CompositingStrategy.Offscreen) .size(100.dp) // Note size of 100 dp here .border(2.dp, color = Color.Blue) ) { /* ... and drawing a size of 200 dp. However, because of the CompositingStrategy.Offscreen usage above, the content gets clipped */ drawRect(color = Color.Red, size = Size(200.dp.toPx(), 200.dp.toPx())) } } }GraphicsModifiersSnippets.kt

This composition strategy modulates the alpha for each of the drawing instructions recorded within the graphicsLayer. It won't create an offscreen buffer for alpha below 1.0f unless a RenderEffect is set, so it can be more efficient for alpha rendering. However, it can provide different results for overlapping content. For use cases where it is known in advance that content is not overlapping, this can provide better performance than CompositingStrategy.Auto with alpha values less than 1.

Another example of different composition strategies is below - applying different alphas to different parts of the composables, and applying a Modulate strategy:

@Preview @Composable fun CompositingStrategy_ModulateAlpha() { Column( modifier = Modifier .fillMaxSize() .padding(32.dp) ) { // Base drawing, no alpha applied Canvas( modifier = Modifier.size(200.dp) ) { drawSquares() } Spacer(modifier = Modifier.size(36.dp)) // Alpha 0.5f applied to whole composable Canvas( modifier = Modifier .size(200.dp) .graphicsLayer { alpha = 0.5f } ) { drawSquares() } Spacer(modifier = Modifier.size(36.dp)) // 0.75f alpha applied to each draw call when using ModulateAlpha Canvas( modifier = Modifier .size(200.dp) .graphicsLayer { compositingStrategy = CompositingStrategy.ModulateAlpha alpha = 0.75f } ) { drawSquares() } } } private fun DrawScope.drawSquares() { val size = Size(100.dp.toPx(), 100.dp.toPx()) drawRect(color = Red, size = size) drawRect( color = Purple, size = size, topLeft = Offset(size.width / 4f, size.height / 4f) ) drawRect( color = Yellow, size = size, topLeft = Offset(size.width / 4f * 2f, size.height / 4f * 2f) ) } val Purple = Color(0xFF7E57C2) val Yellow = Color(0xFFFFCA28) val Red = Color(0xFFEF5350)GraphicsModifiersSnippets.kt

A common use case is to create a Bitmap from a composable. To copy the contents of your composable to a Bitmap, create a GraphicsLayer using rememberGraphicsLayer().

Redirect the drawing commands to the new layer using drawWithContent() and graphicsLayer.record{}. Then draw the layer in the visible canvas using drawLayer:

val coroutineScope = rememberCoroutineScope() val graphicsLayer = rememberGraphicsLayer() Box( modifier = Modifier .drawWithContent { // call record to capture the content in the graphics layer graphicsLayer.record { // draw the contents of the composable into the graphics layer this@drawWithContent.drawContent() } // draw the graphics layer on the visible canvas drawLayer(graphicsLayer) } .clickable { coroutineScope.launch { val bitmap = graphicsLayer.toImageBitmap() // do something with the newly acquired bitmap } } .background(Color.White) ) { Text("Hello Android", fontSize = 26.sp) }AdvancedGraphicsSnippets.kt

You can save the bitmap to disk and share it. For more details, see the full example snippet. Be sure to check for on device permissions before trying to save to disk.

To create your own custom modifier, implement the DrawModifier interface. This gives you access to a ContentDrawScope, which is the same as what is exposed when using Modifier.drawWithContent(). You can then extract common drawing operations to custom drawing modifiers to clean up the code and provide convenient wrappers; for example, Modifier.background() is a convenient DrawModifier.

For example, if you wanted to implement a Modifier that vertically flips content, you can create one as follows:

class FlippedModifier : DrawModifier { override fun ContentDrawScope.draw() { scale(1f, -1f) { this@draw.drawContent() } } } fun Modifier.flipped() = this.then(FlippedModifier())GraphicsModifiersSnippets.kt

Then use this flipped modifier applied on Text:

Text( "Hello Compose!", modifier = Modifier .flipped() )GraphicsModifiersSnippets.kt

For more examples using graphicsLayer and custom drawing, check out the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Scroll Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/scroll

**Contents:**
- Scroll Stay organized with collections Save and categorize content based on your preferences.
- Scroll modifiers
- Scrollable area modifier
  - Difference between scrollableArea and scrollable modifiers
  - When to use scrollableArea modifier
  - Create custom wheel-like lists using scrollableArea
- Scrollable modifier
- Recommended for you

The verticalScroll and horizontalScroll modifiers provide the simplest way to allow the user to scroll an element when the bounds of its contents are larger than its maximum size constraints. With the verticalScroll and horizontalScroll modifiers you don't need to translate or offset the contents.

@Composable private fun ScrollBoxes() { Column( modifier = Modifier .background(Color.LightGray) .size(100.dp) .verticalScroll(rememberScrollState()) ) { repeat(10) { Text("Item $it", modifier = Modifier.padding(2.dp)) } } }GesturesSnippets.kt

The ScrollState lets you change the scroll position or get its current state. To create it with default parameters, use rememberScrollState().

@Composable private fun ScrollBoxesSmooth() { // Smoothly scroll 100px on first composition val state = rememberScrollState() LaunchedEffect(Unit) { state.animateScrollTo(100) } Column( modifier = Modifier .background(Color.LightGray) .size(100.dp) .padding(horizontal = 8.dp) .verticalScroll(state) ) { repeat(10) { Text("Item $it", modifier = Modifier.padding(2.dp)) } } }GesturesSnippets.kt

The scrollableArea modifier is a fundamental building block for creating custom scrollable containers. It provides a higher-level abstraction over the scrollable modifier, handling common requirements like gesture delta interpretation, content clipping, and overscroll effects.

While scrollableArea is used for custom implementations, you should generally prefer ready-made solutions like verticalScroll, horizontalScroll, or composables like LazyColumn for standard scrolling lists. These higher-level components are simpler for common use cases and are themselves built by using scrollableArea.

The main difference between scrollableArea and scrollable lies in how they interpret user scroll gestures:

Think of it like this: scrollable tells you how the pointer moved, while scrollableArea translates that pointer movement into how the content should move within a typical scrollable view. This inversion is why scrollableArea feels more natural when implementing a standard scrollable container.

The following table summarizes the delta signs for common scenarios:

delta reported to dispatchRawDelta by scrollable

delta reported to dispatchRawDelta by scrollableArea*

Positive (Negative for RTL)

Negative (Positive for RTL)

(*) Note on scrollableArea delta sign: The sign of the delta from scrollableArea is not just a simple inversion. It intelligently considers:

In addition to inverting the scroll delta, scrollableArea also clips the content to the bounds of the layout and handles the rendering of overscroll effects. By default, it uses the effect provided by LocalOverscrollFactory. You can customize or disable this by using the scrollableArea overload that accepts an OverscrollEffect parameter.

You should use the scrollableArea modifier when you need to build a custom scrolling component that isn't adequately served by the horizontalScroll or verticalScroll modifiers or Lazy layouts. This often involves cases with:

The following sample demonstrates using scrollableArea to build a custom vertical list where items scale down as they move away from the center, creating a "wheel-like" visual effect. This kind of scroll-dependent transformation is a perfect use case for scrollableArea.

@Composable private fun ScrollableAreaSample() { // ... Layout( modifier = Modifier .size(150.dp) .scrollableArea(scrollState, Orientation.Vertical) .background(Color.LightGray), // ... ) { measurables, constraints -> // ... // Update the maximum scroll value to not scroll beyond limits and stop when scroll // reaches the end. scrollState.maxValue = (totalHeight - viewportHeight).coerceAtLeast(0) // Position the children within the layout. layout(constraints.maxWidth, viewportHeight) { // The current vertical scroll position, in pixels. val scrollY = scrollState.value val viewportCenterY = scrollY + viewportHeight / 2 var placeableLayoutPositionY = 0 placeables.forEach { placeable -> // This sample applies a scaling effect to items based on their distance // from the center, creating a wheel-like effect. // ... // Place the item horizontally centered with a layer transformation for // scaling to achieve wheel-like effect. placeable.placeRelativeWithLayer( x = constraints.maxWidth / 2 - placeable.width / 2, // Offset y by the scroll position to make placeable visible in the viewport. y = placeableLayoutPositionY - scrollY, ) { scaleX = scaleFactor scaleY = scaleFactor } // Move to the next item's vertical position. placeableLayoutPositionY += placeable.height } } } } // ...GesturesSnippets.kt

The scrollable modifier differs from the scroll modifiers in that scrollable detects the scroll gestures and captures the deltas, but does not offset its contents automatically. This is instead delegated to the user through ScrollableState , which is required for this modifier to work correctly.

When constructing ScrollableState you must provide a consumeScrollDelta function which will be invoked on each scroll step (by gesture input, smooth scrolling or flinging) with the delta in pixels. This function must return the amount of scrolling distance consumed, to ensure the event is properly propagated in cases where there are nested elements that have the scrollable modifier.

The following snippet detects the gestures and displays a numerical value for an offset, but does not offset any elements:

@Composable private fun ScrollableSample() { // actual composable state var offset by remember { mutableFloatStateOf(0f) } Box( Modifier .size(150.dp) .scrollable( orientation = Orientation.Vertical, // Scrollable state: describes how to consume // scrolling delta and update offset state = rememberScrollableState { delta -> offset += delta delta } ) .background(Color.LightGray), contentAlignment = Alignment.Center ) { Text(offset.toString()) } }GesturesSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Where to hoist state Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/state-hoisting

**Contents:**
- Where to hoist state Stay organized with collections Save and categorize content based on your preferences.
- Best practice
- Types of UI state and UI logic
  - UI state
  - Logic
- UI logic
  - Composables as state owner
  - No state hoisting needed
  - Hoisting within composables
  - Plain state holder class as state owner

In a Compose application, where you hoist UI state depends on whether UI logic or business logic requires it. This document lays out these two main scenarios.

You should hoist UI state to the lowest common ancestor between all the composables that read and write it. You should keep state closest to where it is consumed. From the state owner, expose to consumers immutable state and events to modify the state.

The lowest common ancestor can also be outside of the Composition. For example, when hoisting state in a ViewModel because business logic is involved.

This page explains this best practice in detail and a caveat to keep in mind.

Below there are definitions for types of UI state and logic that are used throughout this document.

UI state is the property that describes the UI. There are two types of UI state:

Logic in an application can be either business logic or UI logic:

When UI logic needs to read or write state, you should scope the state to the UI, following its lifecycle. To achieve this, you should hoist the state at the correct level in a composable function. Alternatively, you can do so in a plain state holder class, also scoped to the UI lifecycle.

Below is a description of both solutions and explanation of when to use which.

Having UI logic and UI element state in composables is a good approach if the state and logic is simple. You can leave your state internal to a composable or hoist as required.

Hoisting state isn't always required. State can be kept internal in a composable when no other composable need to control it. In this snippet, there is a composable that expands and collapses on tap:

@Composable fun ChatBubble( message: Message ) { var showDetails by rememberSaveable { mutableStateOf(false) } // Define the UI element expanded state Text( text = AnnotatedString(message.content), modifier = Modifier.clickable { showDetails = !showDetails // Apply UI logic } ) if (showDetails) { Text(message.timestamp) } }StateHoistingSnippets.kt

The variable showDetails is the internal state for this UI element. It's only read and modified in this composable and the logic applied to it is very simple. Hoisting the state in this case therefore wouldn't bring much benefit, so you can leave it internal. Doing so makes this composable the owner and single source of truth of the expanded state.

If you need to share your UI element state with other composables and apply UI logic to it in different places, you can hoist it higher in the UI hierarchy. This also makes your composables more reusable and easier to test.

The following example is a chat app that implements two pieces of functionality:

The composable hierarchy is as follows:

The LazyColumn state is hoisted to the conversation screen so the app can perform UI logic and read the state from all composables that require it:

So finally the composables are:

The code is as follows:

@Composable private fun ConversationScreen(/*...*/) { val scope = rememberCoroutineScope() val lazyListState = rememberLazyListState() // State hoisted to the ConversationScreen MessagesList(messages, lazyListState) // Reuse same state in MessageList UserInput( onMessageSent = { // Apply UI logic to lazyListState scope.launch { lazyListState.scrollToItem(0) } }, ) } @Composable private fun MessagesList( messages: List<Message>, lazyListState: LazyListState = rememberLazyListState() // LazyListState has a default value ) { LazyColumn( state = lazyListState // Pass hoisted state to LazyColumn ) { items(messages, key = { message -> message.id }) { item -> Message(/*...*/) } } val scope = rememberCoroutineScope() JumpToBottom(onClicked = { scope.launch { lazyListState.scrollToItem(0) // UI logic being applied to lazyListState } }) }StateHoistingSnippets.kt

LazyListState is hoisted as high as required for the UI logic that has to be applied. Since it is initialized in a composable function, it is stored in the Composition, following its lifecycle.

Note that lazyListState is defined in the MessagesList method, with the default value of rememberLazyListState(). This is a common pattern in Compose. It makes composables more reusable and flexible. You can then use the composable in different parts of the app which might not need to control the state. This is usually the case while testing or previewing a composable. This is exactly how LazyColumn defines its state.

When a composable contains complex UI logic that involves one or multiple state fields of a UI element, it should delegate that responsibility to state holders, like a plain state holder class. This makes the composable's logic more testable in isolation, and reduces its complexity. This approach favors the separation of concerns principle: the composable is in charge of emitting UI elements, and the state holder contains the UI logic and UI element state.

Plain state holder classes provide convenient functions to callers of your composable function, so they don't have to write this logic themselves.

These plain classes are created and remembered in the Composition. Because they follow the composable's lifecycle, they can take types provided by the Compose library such as rememberNavController() or rememberLazyListState().

An example of this is the LazyListState plain state holder class, implemented in Compose to control the UI complexity of LazyColumn or LazyRow.

// LazyListState.kt @Stable class LazyListState constructor( firstVisibleItemIndex: Int = 0, firstVisibleItemScrollOffset: Int = 0 ) : ScrollableState { /** * The holder class for the current scroll position. */ private val scrollPosition = LazyListScrollPosition( firstVisibleItemIndex, firstVisibleItemScrollOffset ) suspend fun scrollToItem(/*...*/) { /*...*/ } override suspend fun scroll() { /*...*/ } suspend fun animateScrollToItem() { /*...*/ } }StateHoistingSnippets.kt

LazyListState encapsulates the state of the LazyColumn storing the scrollPosition for this UI element. It also exposes methods to modify the scroll position by for instance scrolling to a given item.

As you can see, incrementing a composable's responsibilities increases the need for a state holder. The responsibilities could be in UI logic, or just in the amount of state to keep track of.

Another common pattern is using a plain state holder class to handle the complexity of root composable functions in the app. You can use such a class to encapsulate app-level state like navigation state and screen sizing. A complete description of this can be found in the UI logic and its state holder page.

If composables and plain state holders classes are in charge of the UI logic and UI element state, a screen level state holder is in charge of the following tasks:

The benefits of AAC ViewModels in Android development make them suitable for providing access to the business logic and preparing the application data for presentation on the screen.

When you hoist UI state in the ViewModel, you move it outside of the Composition.

ViewModels aren't stored as part of the Composition. They're provided by the framework and they're scoped to a ViewModelStoreOwner which can be an Activity, Fragment, navigation graph, or destination of a navigation graph. For more information on ViewModel scopes you can review the documentation.

Then, the ViewModel is the source of truth and lowest common ancestor for UI state.

As per the definitions above, screen UI state is produced by applying business rules. Given that the screen level state holder is responsible for it, this means the screen UI state is typically hoisted in the screen level state holder, in this case a ViewModel.

Consider the ConversationViewModel of a chat app and how it exposes the screen UI state and events to modify it:

class ConversationViewModel( channelId: String, messagesRepository: MessagesRepository ) : ViewModel() { val messages = messagesRepository .getLatestMessages(channelId) .stateIn( scope = viewModelScope, started = SharingStarted.WhileSubscribed(5_000), initialValue = emptyList() ) // Business logic fun sendMessage(message: Message) { /* ... */ } }StateHoistingSnippets.kt

Composables consume the screen UI state hoisted in the ViewModel. You should inject the ViewModel instance in your screen-level composables to provide access to business logic.

The following is an example of a ViewModel used in a screen-level composable. Here, the composable ConversationScreen() consumes the screen UI state hoisted in the ViewModel:

@Composable private fun ConversationScreen( conversationViewModel: ConversationViewModel = viewModel() ) { val messages by conversationViewModel.messages.collectAsStateWithLifecycle() ConversationScreen( messages = messages, onSendMessage = { message: Message -> conversationViewModel.sendMessage(message) } ) } @Composable private fun ConversationScreen( messages: List<Message>, onSendMessage: (Message) -> Unit ) { MessagesList(messages, onSendMessage) /* ... */ }StateHoistingSnippets.kt

“Property drilling” refers to passing data through several nested children components to the location where they’re read.

A typical example of where property drilling can appear in Compose is when you inject the screen level state holder at the top level and pass down state and events to children composables. This might additionally generate an overload of composable functions signatures.

Even though exposing events as individual lambda parameters could overload the function signature, it maximizes the visibility of what the composable function responsibilities are. You can see what it does at a glance.

Property drilling is preferable over creating wrapper classes to encapsulate state and events in one place because this reduces the visibility of the composable responsibilities. By not having wrapper classes you’re also more likely to pass composables only the parameters they need, which is a best practice.

The same best practice applies if these events are navigation events, you can learn more about that in the navigation docs.

If you have identified a performance issue, you may also choose to defer reading of state. You can check the performance docs to learn more.

You can hoist UI element state to the screen level state holder if there is business logic that needs to read or write it.

Continuing the example of a chat app, the app displays user suggestions in a group chat when the user types @ and a hint. Those suggestions come from the data layer and the logic to calculate a list of user suggestions is considered business logic. The feature looks like this:

The ViewModel implementing this feature would look as follows:

class ConversationViewModel(/*...*/) : ViewModel() { // Hoisted state var inputMessage by mutableStateOf("") private set val suggestions: StateFlow<List<Suggestion>> = snapshotFlow { inputMessage } .filter { hasSocialHandleHint(it) } .mapLatest { getHandle(it) } .mapLatest { repository.getSuggestions(it) } .stateIn( scope = viewModelScope, started = SharingStarted.WhileSubscribed(5_000), initialValue = emptyList() ) fun updateInput(newInput: String) { inputMessage = newInput } }StateHoistingSnippets.kt

inputMessage is a variable storing the TextField state. Every time the user types in new input, the app calls business logic to produce suggestions.

suggestions is screen UI state and is consumed from Compose UI by collecting from the StateFlow.

For some Compose UI element state, hoisting to the ViewModel might require special considerations. For example, some state holders of Compose UI elements expose methods to modify the state. Some of them might be suspend functions that trigger animations. These suspend functions can throw exceptions if you call them from a CoroutineScope that is not scoped to the Composition.

Let’s say the app drawer’s content is dynamic and you need to fetch and refresh it from the data layer after it’s closed. You should hoist the drawer state to the ViewModel so you can call both the UI and business logic on this element from the state owner.

However, calling DrawerState's close() method using the viewModelScope from Compose UI causes a runtime exception of type IllegalStateException with a message reading “a MonotonicFrameClock is not available in this CoroutineContext”.

To fix this, use a CoroutineScope scoped to the Composition. It provides a MonotonicFrameClock in the CoroutineContext that is necessary for the suspend functions to work.

To fix this crash, switch the CoroutineContext of the coroutine in the ViewModel to one that is scoped to the Composition. It could look like this:

class ConversationViewModel(/*...*/) : ViewModel() { val drawerState = DrawerState(initialValue = DrawerValue.Closed) private val _drawerContent = MutableStateFlow(DrawerContent.Empty) val drawerContent: StateFlow<DrawerContent> = _drawerContent.asStateFlow() fun closeDrawer(uiScope: CoroutineScope) { viewModelScope.launch { withContext(uiScope.coroutineContext) { // Use instead of the default context drawerState.close() } // Fetch drawer content and update state _drawerContent.update { content } } } } // in Compose @Composable private fun ConversationScreen( conversationViewModel: ConversationViewModel = viewModel() ) { val scope = rememberCoroutineScope() ConversationScreen(onCloseDrawer = { conversationViewModel.closeDrawer(uiScope = scope) }) }StateHoistingSnippets.kt

To learn more about state and Jetpack Compose, consult the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Constraints and modifier order Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/constraints-modifiers

**Contents:**
- Constraints and modifier order Stay organized with collections Save and categorize content based on your preferences.
- Modifiers in the UI tree
- Constraints in the layout phase
  - Types of constraints
  - How constraints are passed from parent to child
- Modifiers that affect constraints
  - The size modifier
  - The requiredSize modifier
  - The width and height modifiers
  - The sizeIn modifier

In Compose, you can chain multiple modifiers together to change the look and feel of a composable. These modifier chains can affect the constraints passed to composables, which define width and height bounds.

This page describes how chained modifiers affect constraints and, in turn, the measurement and placement of composables.

To understand how modifiers influence each other, it's helpful to visualize how they appear in the UI tree, which is generated during the composition phase. For more information, see the Composition section.

In the UI tree, you can visualize modifiers as wrapper nodes for the layout nodes:

Adding more than one modifier to a composable creates a chain of modifiers. When you chain multiple modifiers, each modifier node wraps the rest of the chain and the layout node within. For example, when you chain a clip and a size modifier, the clip modifier node wraps the size modifier node, which then wraps the Image layout node.

In the layout phase, the algorithm that walks the tree stays the same, but each modifier node is visited as well. This way, a modifier can change the size requirements and placement of the modifier or layout node that it wraps.

As shown in Figure 2, the implementation of the Image and Text composables themselves consists of a chain of modifiers wrapping a single layout node.

The implementations of Row and Column are layout nodes that describe how to lay out their children.

The following sections describe how to use this mental model to reason about modifier chaining and how it influences the size of composables.

The layout phase follows a three-step algorithm to find each layout node's width, height, and x, y coordinate:

Constraints help find the right sizes for the nodes during the first two steps of the algorithm. Constraints define the minimum and maximum bounds for a node's width and height. When the node decides on its size, its measured size should fall within this size range.

A constraint can be one of the following:

The next section describes how these constraints are passed from a parent to a child.

During the first step of the algorithm described in Constraints in the layout phase, constraints are passed down from parent to child in the UI tree.

When a parent node measures its children, it provides these constraints to each child to let them know how big or small they're allowed to be. Then, when it decides its own size, it also adheres to the constraints that were passed in by its own parents.

At a high level, the algorithm works in the following way:

For an in-depth example, see the Constraints and modifier order video.

You learned in the previous section that some modifiers can affect constraint size. The following sections describe specific modifiers that impact constraints.

The size modifier declares the preferred size of the content.

For example, the following UI tree should be rendered in a container of 300dp by 200dp. The constraints are bounded, allowing widths between 100dp and 300dp, and heights between 100dp and 200dp:

The size modifier adapts incoming constraints to match the value passed to it. In this example, the value is 150dp:

If the width and height are smaller than the smallest constraint bound, or larger than the largest constraint bound, the modifier matches the passed constraints as closely as it can while still adhering to the constraints passed in:

Note that chaining multiple size modifiers does not work. The first size modifier sets both the minimum and maximum constraints to a fixed value. Even if the second size modifier requests a smaller or larger size, it still needs to adhere to the exact bounds passed in, so it won't override those values:

Use the requiredSize modifier instead of size if you need your node to override the incoming constraints. The requiredSize modifier replaces the incoming constraints and passes the size you specify as exact bounds.

When the size is passed back up the tree, the child node will be centered in the available space:

The size modifier adapts both the width and height of the constraints. With the width modifier, you can set a fixed width but leave the height undecided. Similarly, with the height modifier, you can set a fixed height, but leave the width undecided:

The sizeIn modifier lets you set exact minimum and maximum constraints for width and height. Use the sizeIn modifier if you need fine-grained control over the constraints.

This section shows and explains the output from several code snippets with chained modifiers.

Image( painterResource(R.drawable.hero), contentDescription = null, Modifier .fillMaxSize() .size(50.dp) )ConstraintsModifiersSnippets.kt

This snippet produces the following output:

Image( painterResource(R.drawable.hero), contentDescription = null, Modifier .fillMaxSize() .wrapContentSize() .size(50.dp) )ConstraintsModifiersSnippets.kt

This snippet produces the following output:

By combining just three modifiers, you can define a size for the composable and center it in its parent.

Image( painterResource(R.drawable.hero), contentDescription = null, Modifier .clip(CircleShape) .padding(10.dp) .size(100.dp) )ConstraintsModifiersSnippets.kt

This snippet produces the following output:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## State and Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/state

**Contents:**
- State and Jetpack Compose Stay organized with collections Save and categorize content based on your preferences.
- State and composition
- State in composables
- Other supported types of state
  - Kotlin
  - Groovy
  - Kotlin
  - Groovy
  - Kotlin
  - Groovy

State in an app is any value that can change over time. This is a very broad definition and encompasses everything from a Room database to a variable in a class.

All Android apps display state to the user. A few examples of state in Android apps:

Jetpack Compose helps you be explicit about where and how you store and use state in an Android app. This guide focuses on the connection between state and composables, and on the APIs that Jetpack Compose offers to work with state more easily.

Compose is declarative and as such the only way to update it is by calling the same composable with new arguments. These arguments are representations of the UI state. Any time a state is updated a recomposition takes place. As a result, things like TextField don’t automatically update like they do in imperative XML based views. A composable has to explicitly be told the new state in order for it to update accordingly.

@Composable private fun HelloContent() { Column(modifier = Modifier.padding(16.dp)) { Text( text = "Hello!", modifier = Modifier.padding(bottom = 8.dp), style = MaterialTheme.typography.bodyMedium ) OutlinedTextField( value = "", onValueChange = { }, label = { Text("Name") } ) } }StateOverviewSnippets.kt

If you run this and try to enter text, you'll see that nothing happens. That's because the TextField doesn't update itself—it updates when its value parameter changes. This is due to how composition and recomposition work in Compose.

Key Term: Composition: a description of the UI built by Jetpack Compose when it executes composables.

Initial composition: creation of a Composition by running composables the first time.

Recomposition: re-running composables to update the Composition when data changes.

To learn more about initial composition and recomposition, see Thinking in Compose.

Composable functions can use the remember API to store an object in memory. A value computed by remember is stored in the Composition during initial composition, and the stored value is returned during recomposition. remember can be used to store both mutable and immutable objects.

mutableStateOf creates an observable MutableState<T>, which is an observable type integrated with the compose runtime.

Any changes to value schedules recomposition of any composable functions that read value.

There are three ways to declare a MutableState object in a composable:

These declarations are equivalent, and are provided as syntax sugar for different uses of state. You should pick the one that produces the easiest-to-read code in the composable you're writing.

The by delegate syntax requires the following imports:

You can use the remembered value as a parameter for other composables or even as logic in statements to change which composables are displayed. For example, if you don't want to display the greeting if the name is empty, use the state in an if statement:

@Composable fun HelloContent() { Column(modifier = Modifier.padding(16.dp)) { var name by remember { mutableStateOf("") } if (name.isNotEmpty()) { Text( text = "Hello, $name!", modifier = Modifier.padding(bottom = 8.dp), style = MaterialTheme.typography.bodyMedium ) } OutlinedTextField( value = name, onValueChange = { name = it }, label = { Text("Name") } ) } }StateOverviewSnippets.kt

While remember helps you retain state across recompositions, the state is not retained across configuration changes. For this, you must use rememberSaveable. rememberSaveable automatically saves any value that can be saved in a Bundle. For other values, you can pass in a custom saver object.

Compose doesn't require that you use MutableState<T> to hold state; it supports other observable types. Before reading another observable type in Compose, you must convert it to a State<T> so that composables can automatically recompose when the state changes.

Compose ships with functions to create State<T> from common observable types used in Android apps. Before using these integrations, add the appropriate artifact(s) as outlined below:

Flow: collectAsStateWithLifecycle()

collectAsStateWithLifecycle() collects values from a Flow in a lifecycle-aware manner, allowing your app to conserve app resources. It represents the latest emitted value from the Compose State. Use this API as the recommended way to collect flows on Android apps.

The following dependency is required in the build.gradle file (it should be 2.6.0-beta01 or newer):

Flow: collectAsState()

collectAsState is similar to collectAsStateWithLifecycle, because it also collects values from a Flow and transforms it into Compose State.

Use collectAsState for platform-agnostic code instead of collectAsStateWithLifecycle, which is Android-only.

Additional dependencies are not required for collectAsState, because it is available in compose-runtime.

LiveData: observeAsState()

observeAsState() starts observing this LiveData and represents its values via State.

The following dependency is required in the build.gradle file:

RxJava2: subscribeAsState()

subscribeAsState() are extension functions that transform RxJava2’s reactive streams (e.g. Single, Observable, Completable) into Compose State.

The following dependency is required in the build.gradle file:

RxJava3: subscribeAsState()

subscribeAsState() are extension functions that transform RxJava3’s reactive streams (e.g. Single, Observable, Completable) into Compose State.

The following dependency is required in the build.gradle file:

A composable that uses remember to store an object creates internal state, making the composable stateful. HelloContent is an example of a stateful composable because it holds and modifies its name state internally. This can be useful in situations where a caller doesn't need to control the state and can use it without having to manage the state themselves. However, composables with internal state tend to be less reusable and harder to test.

A stateless composable is a composable that doesn't hold any state. An easy way to achieve stateless is by using state hoisting.

As you develop reusable composables, you often want to expose both a stateful and a stateless version of the same composable. The stateful version is convenient for callers that don't care about the state, and the stateless version is necessary for callers that need to control or hoist the state.

State hoisting in Compose is a pattern of moving state to a composable's caller to make a composable stateless. The general pattern for state hoisting in Jetpack Compose is to replace the state variable with two parameters:

However, you are not limited to onValueChange. If more specific events are appropriate for the composable, you should define them using lambdas.

State that is hoisted this way has some important properties:

In the example case, you extract the name and the onValueChange out of HelloContent and move them up the tree to a HelloScreen composable that calls HelloContent.

@Composable fun HelloScreen() { var name by rememberSaveable { mutableStateOf("") } HelloContent(name = name, onNameChange = { name = it }) } @Composable fun HelloContent(name: String, onNameChange: (String) -> Unit) { Column(modifier = Modifier.padding(16.dp)) { Text( text = "Hello, $name", modifier = Modifier.padding(bottom = 8.dp), style = MaterialTheme.typography.bodyMedium ) OutlinedTextField(value = name, onValueChange = onNameChange, label = { Text("Name") }) } }StateOverviewSnippets.kt

By hoisting the state out of HelloContent, it's easier to reason about the composable, reuse it in different situations, and test. HelloContent is decoupled from how its state is stored. Decoupling means that if you modify or replace HelloScreen, you don't have to change how HelloContent is implemented.

The pattern where the state goes down, and events go up is called a unidirectional data flow. In this case, the state goes down from HelloScreen to HelloContent and events go up from HelloContent to HelloScreen. By following unidirectional data flow, you can decouple composables that display state in the UI from the parts of your app that store and change state.

Key Point: When hoisting state, there are three rules to help you figure out where state should go:

You can hoist state higher than these rules require, but underhoisting state makes it difficult or impossible to follow unidirectional data flow.

See the Where to hoist state page to learn more.

The rememberSaveable API behaves similarly to remember because it retains state across recompositions, and also across activity or process recreation using the saved instance state mechanism. For example, this happens, when the screen is rotated.

All data types that are added to the Bundle are saved automatically. If you want to save something that cannot be added to the Bundle, there are several options.

The simplest solution is to add the @Parcelize annotation to the object. The object becomes parcelable, and can be bundled. For example, this code makes a parcelable City data type and saves it to the state.

@Parcelize data class City(val name: String, val country: String) : Parcelable @Composable fun CityScreen() { var selectedCity = rememberSaveable { mutableStateOf(City("Madrid", "Spain")) } }StateOverviewSnippets.kt

If for some reason @Parcelize is not suitable, you can use mapSaver to define your own rule for converting an object into a set of values that the system can save to the Bundle.

data class City(val name: String, val country: String) val CitySaver = run { val nameKey = "Name" val countryKey = "Country" mapSaver( save = { mapOf(nameKey to it.name, countryKey to it.country) }, restore = { City(it[nameKey] as String, it[countryKey] as String) } ) } @Composable fun CityScreen() { var selectedCity = rememberSaveable(stateSaver = CitySaver) { mutableStateOf(City("Madrid", "Spain")) } }StateOverviewSnippets.kt

To avoid needing to define the keys for the map, you can also use listSaver and use its indices as keys:

data class City(val name: String, val country: String) val CitySaver = listSaver<City, Any>( save = { listOf(it.name, it.country) }, restore = { City(it[0] as String, it[1] as String) } ) @Composable fun CityScreen() { var selectedCity = rememberSaveable(stateSaver = CitySaver) { mutableStateOf(City("Madrid", "Spain")) } }StateOverviewSnippets.kt

Simple state hoisting can be managed in the composable functions itself. However, if the amount of state to keep track of increases, or the logic to perform in composable functions arises, it's a good practice to delegate the logic and state responsibilities to other classes: state holders.

Key Term: State holders manage logic and state of composables.

Note that in other materials, state holders are also called hoisted state objects.

See the state hoisting in Compose documentation or, more generally, the State holders and UI State page in the architecture guide to learn more.

The remember API is frequently used together with MutableState:

var name by remember { mutableStateOf("") }StateOverviewSnippets.kt

Here, using the remember function makes the MutableState value survive recompositions.

In general, remember takes a calculation lambda parameter. When remember is first run, it invokes the calculation lambda and stores its result. During recomposition, remember returns the value that was last stored.

Apart from caching state, you can also use remember to store any object or result of an operation in the Composition that is expensive to initialize or calculate. You might not want to repeat this calculation in every recomposition. An example is creating this ShaderBrush object, which is an expensive operation:

val brush = remember { ShaderBrush( BitmapShader( ImageBitmap.imageResource(res, avatarRes).asAndroidBitmap(), Shader.TileMode.REPEAT, Shader.TileMode.REPEAT ) ) }StateOverviewSnippets.kt

remember stores the value until it leaves the Composition. However, there is a way to invalidate the cached value. The remember API also takes a key or keys parameter. If any of these keys change, the next time the function recomposes, remember invalidates the cache and executes the calculation lambda block again. This mechanism gives you control over the lifetime of an object in the Composition. The calculation remains valid until the inputs change, instead of until the remembered value leaves the Composition.

The following examples show how this mechanism works.

In this snippet, a ShaderBrush is created and used as the background paint of a Box composable. remember stores the ShaderBrush instance because it is expensive to recreate, as explained earlier. remember takes avatarRes as the key1 parameter, which is the selected background image. If avatarRes changes, the brush recomposes with the new image, and reapplies to the Box. This can occur when the user selects another image to be the background from a picker.

@Composable private fun BackgroundBanner( @DrawableRes avatarRes: Int, modifier: Modifier = Modifier, res: Resources = LocalContext.current.resources ) { val brush = remember(key1 = avatarRes) { ShaderBrush( BitmapShader( ImageBitmap.imageResource(res, avatarRes).asAndroidBitmap(), Shader.TileMode.REPEAT, Shader.TileMode.REPEAT ) ) } Box( modifier = modifier.background(brush) ) { /* ... */ } }StateOverviewSnippets.kt

In the next snippet, state is hoisted to a plain state holder class MyAppState. It exposes a rememberMyAppState function to initialize an instance of the class using remember. Exposing such functions to create an instance that survives recompositions is a common pattern in Compose. The rememberMyAppState function receives windowSizeClass, which serves as the key parameter for remember. If this parameter changes, the app needs to recreate the plain state holder class with the latest value. This may occur if, for example, the user rotates the device.

@Composable private fun rememberMyAppState( windowSizeClass: WindowSizeClass ): MyAppState { return remember(windowSizeClass) { MyAppState(windowSizeClass) } } @Stable class MyAppState( private val windowSizeClass: WindowSizeClass ) { /* ... */ }StateOverviewSnippets.kt

Compose uses the class's equals implementation to decide if a key has changed and invalidate the stored value.

The rememberSaveable API is a wrapper around remember that can store data in a Bundle. This API allows state to survive not only recomposition, but also activity recreation and system-initiated process death. rememberSaveable receives input parameters for the same purpose that remember receives keys. The cache is invalidated when any of the inputs change. The next time the function recomposes, rememberSaveable re-executes the calculation lambda block.

In the following example, rememberSaveable stores userTypedQuery until typedQuery changes:

var userTypedQuery by rememberSaveable(typedQuery, stateSaver = TextFieldValue.Saver) { mutableStateOf( TextFieldValue(text = typedQuery, selection = TextRange(typedQuery.length)) ) }StateOverviewSnippets.kt

To learn more about state and Jetpack Compose, consult the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (swift):
```swift
interface MutableState<T> : State<T> {
    override var value: T
}
```

Example 2 (python):
```python
import androidx.compose.runtime.getValue
import androidx.compose.runtime.setValue
```

Example 3 (json):
```json
dependencies {
      ...
      implementation("androidx.lifecycle:lifecycle-runtime-compose:2.9.4")
}
```

Example 4 (json):
```json
dependencies {
      ...
      implementation "androidx.lifecycle:lifecycle-runtime-compose:2.9.4"
}
```

---

## Create custom modifiers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/custom-modifiers

**Contents:**
- Create custom modifiers Stay organized with collections Save and categorize content based on your preferences.
- Chain existing modifiers together
- Create a custom modifier using a composable modifier factory
    - CompositionLocal values are resolved at the call site of the modifier factory
    - Composable function modifiers are never skipped
    - Composable function modifiers must be called within a composable function
- Implement custom modifier behavior using Modifier.Node
  - Implement a custom modifier using Modifier.Node
    - Modifier.Node
    - ModifierNodeElement

Compose provides many modifiers for common behaviors right out of the box, but you can also create your own custom modifiers.

Modifiers have multiple parts:

There are multiple ways to implement a custom modifier depending on the functionality needed. Often, the simplest way to implement a custom modifier is to implement a custom modifier factory that combines other already defined modifier factories. If you need more custom behavior, implement the modifier element using the Modifier.Node APIs, which are lower level but provide more flexibility.

It is often possible to create custom modifiers by using existing modifiers. For example, Modifier.clip() is implemented using the graphicsLayer modifier. This strategy uses existing modifier elements, and you provide your own custom modifier factory.

Before implementing your own custom modifier, see if you can use the same strategy.

fun Modifier.clip(shape: Shape) = graphicsLayer(shape = shape, clip = true)CustomModifierSnippets.kt

Or, if you find you are repeating the same group of modifiers often, you can wrap them into your own modifier:

fun Modifier.myBackground(color: Color) = padding(16.dp) .clip(RoundedCornerShape(8.dp)) .background(color)CustomModifierSnippets.kt

You can also create a custom modifier using a composable function to pass values to an existing modifier. This is known as a composable modifier factory.

Using a composable modifier factory to create a modifier also lets you use higher level compose APIs, such as animate*AsState and other Compose state backed animation APIs. For example, the following snippet shows a modifier that animates an alpha change when enabled/disabled:

@Composable fun Modifier.fade(enable: Boolean): Modifier { val alpha by animateFloatAsState(if (enable) 0.5f else 1.0f) return this then Modifier.graphicsLayer { this.alpha = alpha } }CustomModifierSnippets.kt

If your custom modifier is a convenience method to provide default values from a CompositionLocal, the easiest way to implement this is to use a composable modifier factory:

@Composable fun Modifier.fadedBackground(): Modifier { val color = LocalContentColor.current return this then Modifier.background(color.copy(alpha = 0.5f)) }CustomModifierSnippets.kt

This approach has some caveats, which are detailed in the following sections.

When creating a custom modifier using a composable modifier factory, composition locals take the value from the composition tree where they are created, not used. This can lead to unexpected results. For example, consider the composition local modifier example mentioned previously, implemented slightly differently using a composable function:

@Composable fun Modifier.myBackground(): Modifier { val color = LocalContentColor.current return this then Modifier.background(color.copy(alpha = 0.5f)) } @Composable fun MyScreen() { CompositionLocalProvider(LocalContentColor provides Color.Green) { // Background modifier created with green background val backgroundModifier = Modifier.myBackground() // LocalContentColor updated to red CompositionLocalProvider(LocalContentColor provides Color.Red) { // Box will have green background, not red as expected. Box(modifier = backgroundModifier) } } }CustomModifierSnippets.kt

If this is not how you expect your modifier to work, use a custom Modifier.Node instead, as composition locals will be correctly resolved at the usage site and can be safely hoisted.

Composable factory modifiers are never skipped because composable functions that have return values cannot be skipped. This means your modifier function will be called on every recomposition, which may be expensive if it recomposes frequently.

Like all composable functions, a composable factory modifier must be called from within composition. This limits where a modifier can be hoisted to, as it can never be hoisted out of composition. In comparison, non-composable modifier factories can be hoisted out of composable functions to allow easier reuse and improve performance:

val extractedModifier = Modifier.background(Color.Red) // Hoisted to save allocations @Composable fun Modifier.composableModifier(): Modifier { val color = LocalContentColor.current.copy(alpha = 0.5f) return this then Modifier.background(color) } @Composable fun MyComposable() { val composedModifier = Modifier.composableModifier() // Cannot be extracted any higher }CustomModifierSnippets.kt

Modifier.Node is a lower level API for creating modifiers in Compose. It is the same API that Compose implements its own modifiers in and is the most performant way to create custom modifiers.

There are three parts to implementing a custom modifier using Modifier.Node:

ModifierNodeElement classes are stateless and new instances are allocated each recomposition, whereas Modifier.Node classes can be stateful and will survive across multiple recompositions, and can even be reused.

The following section describes each part and shows an example of building a custom modifier to draw a circle.

The Modifier.Node implementation (in this example, CircleNode) implements the functionality of your custom modifier.

// Modifier.Node private class CircleNode(var color: Color) : DrawModifierNode, Modifier.Node() { override fun ContentDrawScope.draw() { drawCircle(color) } }CustomModifierSnippets.kt

In this example, it draws the circle with the color passed in to the modifier function.

A node implements Modifier.Node as well as zero or more node types. There are different node types based on the functionality your modifier requires. The preceding example needs to be able to draw, so it implements DrawModifierNode, which lets it override the draw method.

The available types are as follows:

A Modifier.Node that changes how its wrapped content is measured and laid out.

A Modifier.Node that draws into the space of the layout.

CompositionLocalConsumerModifierNode

Implementing this interface lets your Modifier.Node read composition locals.

SemanticsModifierNode

A Modifier.Node that adds semantics key/value for use in testing, accessibility, and similar use cases.

PointerInputModifierNode

A Modifier.Node that receives PointerInputChanges.

ParentDataModifierNode

A Modifier.Node that provides data to the parent layout.

LayoutAwareModifierNode

A Modifier.Node which receives onMeasured and onPlaced callbacks.

GlobalPositionAwareModifierNode

A Modifier.Node which receives an onGloballyPositioned callback with the final LayoutCoordinates of the layout when the global position of the content may have changed.

Modifier.Nodes that implement ObserverNode can provide their own implementation of onObservedReadsChanged that will be called in response to changes to snapshot objects read within an observeReads block.

A Modifier.Node which is able to delegate work to other Modifier.Node instances.

This can be useful to compose multiple node implementations into one.

Allows Modifier.Node classes to traverse up/down the node tree for classes of the same type or for a particular key.

Nodes are automatically invalidated when update is called on their corresponding element. Because our example is a DrawModifierNode, any time update is called on the element, the node triggers a redraw and its color correctly updates. It is possible to opt out of auto-invalidation, as detailed in the Opt out of node auto-invalidation section.

A ModifierNodeElement is an immutable class that holds the data to create or update your custom modifier:

// ModifierNodeElement private data class CircleElement(val color: Color) : ModifierNodeElement<CircleNode>() { override fun create() = CircleNode(color) override fun update(node: CircleNode) { node.color = color } }CustomModifierSnippets.kt

ModifierNodeElement implementations need to override the following methods:

Additionally, ModifierNodeElement implementations also need to implement equals and hashCode. update will only get called if an equals comparison with the previous element returns false.

The preceding example uses a data class to achieve this. These methods are used to check if a node needs updating or not. If your element has properties that don't contribute to whether a node needs to be updated, or you want to avoid data classes for binary compatibility reasons, then you can manually implement equals and hashCode, for example, the padding modifier element.

This is the public API surface of your modifier. Most implementations create the modifier element and add it to the modifier chain:

// Modifier factory fun Modifier.circle(color: Color) = this then CircleElement(color)CustomModifierSnippets.kt

These three parts come together to create the custom modifier to draw a circle using the Modifier.Node APIs:

// Modifier factory fun Modifier.circle(color: Color) = this then CircleElement(color) // ModifierNodeElement private data class CircleElement(val color: Color) : ModifierNodeElement<CircleNode>() { override fun create() = CircleNode(color) override fun update(node: CircleNode) { node.color = color } } // Modifier.Node private class CircleNode(var color: Color) : DrawModifierNode, Modifier.Node() { override fun ContentDrawScope.draw() { drawCircle(color) } }CustomModifierSnippets.kt

When creating custom modifiers with Modifier.Node, here are some common situations you might encounter.

If your modifier has no parameters, then it never needs to update and, furthermore, doesn't need to be a data class. The following is a sample implementation of a modifier that applies a fixed amount of padding to a composable:

fun Modifier.fixedPadding() = this then FixedPaddingElement data object FixedPaddingElement : ModifierNodeElement<FixedPaddingNode>() { override fun create() = FixedPaddingNode() override fun update(node: FixedPaddingNode) {} } class FixedPaddingNode : LayoutModifierNode, Modifier.Node() { private val PADDING = 16.dp override fun MeasureScope.measure( measurable: Measurable, constraints: Constraints ): MeasureResult { val paddingPx = PADDING.roundToPx() val horizontal = paddingPx * 2 val vertical = paddingPx * 2 val placeable = measurable.measure(constraints.offset(-horizontal, -vertical)) val width = constraints.constrainWidth(placeable.width + horizontal) val height = constraints.constrainHeight(placeable.height + vertical) return layout(width, height) { placeable.place(paddingPx, paddingPx) } } }CustomModifierSnippets.kt

Modifier.Node modifiers don't automatically observe changes to Compose state objects, like CompositionLocal. The advantage Modifier.Node modifiers have over modifiers that are just created with a composable factory is that they can read the value of the composition local from where the modifier is used in your UI tree, not where the modifier is allocated, using currentValueOf.

However, modifier node instances don't automatically observe state changes. To automatically react to a composition local changing, you can read its current value inside a scope:

This example observes the value of LocalContentColor to draw a background based on its color. As ContentDrawScope does observe snapshot changes, this automatically redraws when the value of LocalContentColor changes:

class BackgroundColorConsumerNode : Modifier.Node(), DrawModifierNode, CompositionLocalConsumerModifierNode { override fun ContentDrawScope.draw() { val currentColor = currentValueOf(LocalContentColor) drawRect(color = currentColor) drawContent() } }CustomModifierSnippets.kt

To react to state changes outside of a scope and automatically update your modifier, use an ObserverModifierNode.

For example, Modifier.scrollable uses this technique to observe changes in LocalDensity. A simplified example is shown in the following example:

class ScrollableNode : Modifier.Node(), ObserverModifierNode, CompositionLocalConsumerModifierNode { // Place holder fling behavior, we'll initialize it when the density is available. val defaultFlingBehavior = DefaultFlingBehavior(splineBasedDecay(UnityDensity)) override fun onAttach() { updateDefaultFlingBehavior() observeReads { currentValueOf(LocalDensity) } // monitor change in Density } override fun onObservedReadsChanged() { // if density changes, update the default fling behavior. updateDefaultFlingBehavior() } private fun updateDefaultFlingBehavior() { val density = currentValueOf(LocalDensity) defaultFlingBehavior.flingDecay = splineBasedDecay(density) } }CustomModifierSnippets.kt

Modifier.Node implementations have access to a coroutineScope. This allows for use of the Compose Animatable APIs. For example, this snippet modifies the CircleNode shown previously to fade in and out repeatedly:

class CircleNode(var color: Color) : Modifier.Node(), DrawModifierNode { private lateinit var alpha: Animatable<Float, AnimationVector1D> override fun ContentDrawScope.draw() { drawCircle(color = color, alpha = alpha.value) drawContent() } override fun onAttach() { alpha = Animatable(1f) coroutineScope.launch { alpha.animateTo( 0f, infiniteRepeatable(tween(1000), RepeatMode.Reverse) ) { } } } }CustomModifierSnippets.kt

Modifier.Node modifiers can delegate to other nodes. There are many use cases for this, such as extracting common implementations across different modifiers, but it can also be used to share common state across modifiers.

For example, a basic implementation of a clickable modifier node that shares interaction data:

class ClickableNode : DelegatingNode() { val interactionData = InteractionData() val focusableNode = delegate( FocusableNode(interactionData) ) val indicationNode = delegate( IndicationNode(interactionData) ) }CustomModifierSnippets.kt

Modifier.Node nodes automatically invalidate when their corresponding ModifierNodeElement calls update. For complex modifiers, you might want to opt out of this behavior to gain more fine-grained control over when your modifier invalidates phases.

This is particularly useful if your custom modifier modifies both layout and draw. Opting out of auto-invalidation lets you just invalidate draw when only draw-related properties, such as color, change. This avoids invalidating layout and can improve your modifier's performance.

A hypothetical example of this is shown in the following example with a modifier that has a color, size, and onClick lambda as properties. This modifier invalidates only what is required, skipping any unneeded invalidation:

class SampleInvalidatingNode( var color: Color, var size: IntSize, var onClick: () -> Unit ) : DelegatingNode(), LayoutModifierNode, DrawModifierNode { override val shouldAutoInvalidate: Boolean get() = false private val clickableNode = delegate( ClickablePointerInputNode(onClick) ) fun update(color: Color, size: IntSize, onClick: () -> Unit) { if (this.color != color) { this.color = color // Only invalidate draw when color changes invalidateDraw() } if (this.size != size) { this.size = size // Only invalidate layout when size changes invalidateMeasurement() } // If only onClick changes, we don't need to invalidate anything clickableNode.update(onClick) } override fun ContentDrawScope.draw() { drawRect(color) } override fun MeasureScope.measure( measurable: Measurable, constraints: Constraints ): MeasureResult { val size = constraints.constrain(size) val placeable = measurable.measure(constraints) return layout(size.width, size.height) { placeable.place(0, 0) } } }CustomModifierSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Locally scoped data with CompositionLocal Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/compositionlocal

**Contents:**
- Locally scoped data with CompositionLocal Stay organized with collections Save and categorize content based on your preferences.
- Introduction to CompositionLocal
- Create your own CompositionLocal
  - Decide whether to use CompositionLocal
  - Create a CompositionLocal
  - Provide values to a CompositionLocal
  - Consuming the CompositionLocal
- Alternatives to consider
  - Pass explicit parameters
  - Inversion of control

CompositionLocal is a tool for passing data down through the Composition implicitly. In this page, you'll learn what a CompositionLocal is in more detail, how to create your own CompositionLocal, and know if a CompositionLocal is a good solution for your use case.

Usually in Compose, data flows down through the UI tree as parameters to each composable function. This makes a composable's dependencies explicit. However, this can be cumbersome for data that is very frequently and widely used, such as colors or type styles. See the following example:

@Composable fun MyApp() { // Theme information tends to be defined near the root of the application val colors = colors() } // Some composable deep in the hierarchy @Composable fun SomeTextLabel(labelText: String) { Text( text = labelText, color = colors.onPrimary // ← need to access colors here ) }CompositionLocalSnippets.kt

To support not needing to pass the colors as an explicit parameter dependency to most composables, Compose offers CompositionLocal, which allows you to create tree-scoped named objects that can be used as an implicit way to have data flow through the UI tree.

CompositionLocal elements are usually provided with a value in a certain node of the UI tree. That value can be used by its composable descendants without declaring the CompositionLocal as a parameter in the composable function.

Key terms: In this guide, we use the terms Composition, UI tree, and UI hierarchy. Although they might be used interchangeably in other guides, they have different meanings:

The Composition is the record of the call graph of composable functions.

The UI tree or UI hierarchy is the tree of LayoutNode constructed, updated, and maintained by the composition process.

CompositionLocal is what the Material theme uses under the hood. MaterialTheme is an object that provides three CompositionLocal instances: colorScheme, typography and shapes, allowing you to retrieve them later in any descendant part of the Composition. Specifically, these are the LocalColorScheme, LocalShapes, and LocalTypography properties that you can access through the MaterialTheme colorScheme, shapes, and typography attributes.

@Composable fun MyApp() { // Provides a Theme whose values are propagated down its `content` MaterialTheme { // New values for colorScheme, typography, and shapes are available // in MaterialTheme's content lambda. // ... content here ... } } // Some composable deep in the hierarchy of MaterialTheme @Composable fun SomeTextLabel(labelText: String) { Text( text = labelText, // `primary` is obtained from MaterialTheme's // LocalColors CompositionLocal color = MaterialTheme.colorScheme.primary ) }CompositionLocalSnippets.kt

A CompositionLocal instance is scoped to a part of the Composition so you can provide different values at different levels of the tree. The current value of a CompositionLocal corresponds to the closest value provided by an ancestor in that part of the Composition.

To provide a new value to a CompositionLocal, use the CompositionLocalProvider and its provides infix function that associates a CompositionLocal key to a value. The content lambda of the CompositionLocalProvider will get the provided value when accessing the current property of the CompositionLocal. When a new value is provided, Compose recomposes parts of the Composition that read the CompositionLocal.

As an example of this, the LocalContentColor CompositionLocal contains the preferred content color used for text and iconography to ensure it contrasts against the current background color. In the following example, CompositionLocalProvider is used to provide different values for different parts of the Composition.

@Composable fun CompositionLocalExample() { MaterialTheme { // Surface provides contentColorFor(MaterialTheme.colorScheme.surface) by default // This is to automatically make text and other content contrast to the background // correctly. Surface { Column { Text("Uses Surface's provided content color") CompositionLocalProvider(LocalContentColor provides MaterialTheme.colorScheme.primary) { Text("Primary color provided by LocalContentColor") Text("This Text also uses primary as textColor") CompositionLocalProvider(LocalContentColor provides MaterialTheme.colorScheme.error) { DescendantExample() } } } } } } @Composable fun DescendantExample() { // CompositionLocalProviders also work across composable functions Text("This Text uses the error color now") }CompositionLocalSnippets.kt

In the last example, the CompositionLocal instances were internally used by Material composables. To access the current value of a CompositionLocal, use its current property. In the following example, the current Context value of the LocalContext CompositionLocal that is commonly used in Android apps is used to format the text:

@Composable fun FruitText(fruitSize: Int) { // Get `resources` from the current value of LocalContext val resources = LocalContext.current.resources val fruitText = remember(resources, fruitSize) { resources.getQuantityString(R.plurals.fruit_title, fruitSize) } Text(text = fruitText) }CompositionLocalSnippets.kt

CompositionLocal is a tool for passing data down through the Composition implicitly.

Another key signal for using CompositionLocal is when the parameter is cross-cutting and intermediate layers of implementation should not be aware it exists, because making those intermediate layers aware would limit the utility of the composable. For example, querying for Android permissions is afforded by a CompositionLocal under the hood. A media picker composable can add new functionality to access permission-protected content on the device without changing its API and requiring callers of the media picker to be aware of this added context used from the environment.

However, CompositionLocal is not always the best solution. We discourage overusing CompositionLocal as it comes with some downsides:

CompositionLocal makes a composable's behavior harder to reason about. As they create implicit dependencies, callers of composables that use them need to make sure that a value for every CompositionLocal is satisfied.

Furthermore, there might be no clear source of truth for this dependency as it can mutate in any part of the Composition. Thus, debugging the app when a problem occurs can be more challenging as you need to navigate up the Composition to see where the current value was provided. Tools such as Find usages in the IDE or the Compose layout inspector provide enough information to mitigate this issue.

There are certain conditions that can make CompositionLocal a good solution for your use case:

A CompositionLocal should have a good default value. If there's no default value, you must guarantee that it is exceedingly difficult for a developer to get into a situation where a value for the CompositionLocal isn't provided. Not providing a default value can cause problems and frustration when creating tests or previewing a composable that uses that CompositionLocal will always require it to be explicitly provided.

Avoid CompositionLocal for concepts that aren't thought as tree-scoped or sub-hierarchy scoped. A CompositionLocal makes sense when it can be potentially used by any descendant, not by a few of them.

If your use case doesn't meet these requirements, check out the Alternatives to consider section before creating a CompositionLocal.

An example of a bad practice is creating a CompositionLocal that holds the ViewModel of a particular screen so that all composables in that screen can get a reference to the ViewModel to perform some logic. This is a bad practice because not all composables below a particular UI tree need to know about a ViewModel. The good practice is to pass to composables only the information that they need following the pattern that state flows down and events flow up. This approach will make your composables more reusable and easier to test.

There are two APIs to create a CompositionLocal:

compositionLocalOf: Changing the value provided during recomposition invalidates only the content that reads its current value.

staticCompositionLocalOf: Unlike compositionLocalOf, reads of a staticCompositionLocalOf are not tracked by Compose. Changing the value causes the entirety of the content lambda where the CompositionLocal is provided to be recomposed, instead of just the places where the current value is read in the Composition.

If the value provided to the CompositionLocal is highly unlikely to change or will never change, use staticCompositionLocalOf to get performance benefits.

For example, an app's design system might be opinionated in the way composables are elevated using a shadow for the UI component. Since the different elevations for the app should propagate throughout the UI tree, we use a CompositionLocal. As the CompositionLocal value is derived conditionally based on the system theme, we use the compositionLocalOf API:

// LocalElevations.kt file data class Elevations(val card: Dp = 0.dp, val default: Dp = 0.dp) // Define a CompositionLocal global object with a default // This instance can be accessed by all composables in the app val LocalElevations = compositionLocalOf { Elevations() }CompositionLocalSnippets.kt

The CompositionLocalProvider composable binds values to CompositionLocal instances for the given hierarchy. To provide a new value to a CompositionLocal, use the provides infix function that associates a CompositionLocal key to a value as follows:

// MyActivity.kt file class MyActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContent { // Calculate elevations based on the system theme val elevations = if (isSystemInDarkTheme()) { Elevations(card = 1.dp, default = 1.dp) } else { Elevations(card = 0.dp, default = 0.dp) } // Bind elevation as the value for LocalElevations CompositionLocalProvider(LocalElevations provides elevations) { // ... Content goes here ... // This part of Composition will see the `elevations` instance // when accessing LocalElevations.current } } } }CompositionLocalSnippets.kt

CompositionLocal.current returns the value provided by the nearest CompositionLocalProvider that provides a value to that CompositionLocal:

@Composable fun SomeComposable() { // Access the globally defined LocalElevations variable to get the // current Elevations in this part of the Composition MyCard(elevation = LocalElevations.current.card) { // Content } }CompositionLocalSnippets.kt

A CompositionLocal might be an excessive solution for some use cases. If your use case doesn't meet the criteria specified in the Deciding whether to use CompositionLocal section, another solution might likely be better suited for your use case.

Being explicit about composable's dependencies is a good habit. We recommend that you pass composables only what they need. To encourage decoupling and reuse of composables, each composable should hold the least amount of information possible.

@Composable fun MyComposable(myViewModel: MyViewModel = viewModel()) { // ... MyDescendant(myViewModel.data) } // Don't pass the whole object! Just what the descendant needs. // Also, don't pass the ViewModel as an implicit dependency using // a CompositionLocal. @Composable fun MyDescendant(myViewModel: MyViewModel) { /* ... */ } // Pass only what the descendant needs @Composable fun MyDescendant(data: DataToDisplay) { // Display data }CompositionLocalSnippets.kt

Another way to avoid passing unnecessary dependencies to a composable is using inversion of control. Instead of the descendant taking in a dependency to execute some logic, the parent does that instead.

See the following example where a descendant needs to trigger the request to load some data:

@Composable fun MyComposable(myViewModel: MyViewModel = viewModel()) { // ... MyDescendant(myViewModel) } @Composable fun MyDescendant(myViewModel: MyViewModel) { Button(onClick = { myViewModel.loadData() }) { Text("Load data") } }CompositionLocalSnippets.kt

Depending on the case, MyDescendant might have a lot of responsibility. Also, passing MyViewModel as a dependency makes MyDescendant less reusable since they're now coupled together. Consider the alternative that doesn't pass the dependency into the descendant and uses inversion of control principles that makes the ancestor responsible for executing the logic:

@Composable fun MyComposable(myViewModel: MyViewModel = viewModel()) { // ... ReusableLoadDataButton( onLoadClick = { myViewModel.loadData() } ) } @Composable fun ReusableLoadDataButton(onLoadClick: () -> Unit) { Button(onClick = onLoadClick) { Text("Load data") } }CompositionLocalSnippets.kt

This approach can be better suited for some use cases as it decouples the child from its immediate ancestors. Ancestor composables tend to become more complex in favor of having more flexible lower-level composables.

Similarly, @Composable content lambdas can be used in the same way to get the same benefits:

@Composable fun MyComposable(myViewModel: MyViewModel = viewModel()) { // ... ReusablePartOfTheScreen( content = { Button( onClick = { myViewModel.loadData() } ) { Text("Confirm") } } ) } @Composable fun ReusablePartOfTheScreen(content: @Composable () -> Unit) { Column { // ... content() } }CompositionLocalSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Side-effects in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/side-effects

**Contents:**
- Side-effects in Compose Stay organized with collections Save and categorize content based on your preferences.
- State and effect use cases
  - LaunchedEffect: run suspend functions in the scope of a composable
  - rememberCoroutineScope: obtain a composition-aware scope to launch a coroutine outside a composable
  - rememberUpdatedState: reference a value in an effect that shouldn't restart if the value changes
  - DisposableEffect: effects that require cleanup
  - SideEffect: publish Compose state to non-Compose code
  - produceState: convert non-Compose state into Compose state
  - derivedStateOf: convert one or multiple state objects into another state
    - Correct usage

A side-effect is a change to the state of the app that happens outside the scope of a composable function. Due to composables' lifecycle and properties such as unpredictable recompositions, executing recompositions of composables in different orders, or recompositions that can be discarded, composables should ideally be side-effect free.

However, sometimes side-effects are necessary, for example, to trigger a one-off event such as showing a snackbar or navigate to another screen given a certain state condition. These actions should be called from a controlled environment that is aware of the lifecycle of the composable. In this page, you'll learn about the different side-effect APIs Jetpack Compose offers.

As covered in the Thinking in Compose documentation, composables should be side-effect free. When you need to make changes to the state of the app (as described in the Managing state documentation doc), you should use the Effect APIs so that those side effects are executed in a predictable manner.

Due to the different possibilities effects open up in Compose, they can be easily overused. Make sure that the work you do in them is UI related and doesn't break unidirectional data flow as explained in the Managing state documentation.

To perform work over the life of a composable and have the ability to call suspend functions, use the LaunchedEffect composable. When LaunchedEffect enters the Composition, it launches a coroutine with the block of code passed as a parameter. The coroutine will be cancelled if LaunchedEffect leaves the composition. If LaunchedEffect is recomposed with different keys (see the Restarting Effects section below), the existing coroutine will be cancelled and the new suspend function will be launched in a new coroutine.

For example, here is an animation that pulses the alpha value with a configurable delay:

// Allow the pulse rate to be configured, so it can be sped up if the user is running // out of time var pulseRateMs by remember { mutableLongStateOf(3000L) } val alpha = remember { Animatable(1f) } LaunchedEffect(pulseRateMs) { // Restart the effect when the pulse rate changes while (isActive) { delay(pulseRateMs) // Pulse the alpha every pulseRateMs to alert the user alpha.animateTo(0f) alpha.animateTo(1f) } }SideEffectsSnippets.kt

In the code above, the animation uses the suspending function delay to wait the set amount of time. Then, it sequentially animates the alpha to zero and back again using animateTo. This will repeat for the life of the composable.

As LaunchedEffect is a composable function, it can only be used inside other composable functions. In order to launch a coroutine outside of a composable, but scoped so that it will be automatically canceled once it leaves the composition, use rememberCoroutineScope. Also use rememberCoroutineScope whenever you need to control the lifecycle of one or more coroutines manually, for example, cancelling an animation when a user event happens.

rememberCoroutineScope is a composable function that returns a CoroutineScope bound to the point of the Composition where it's called. The scope will be cancelled when the call leaves the Composition.

Following the previous example, you could use this code to show a Snackbar when the user taps on a Button:

@Composable fun MoviesScreen(snackbarHostState: SnackbarHostState) { // Creates a CoroutineScope bound to the MoviesScreen's lifecycle val scope = rememberCoroutineScope() Scaffold( snackbarHost = { SnackbarHost(hostState = snackbarHostState) } ) { contentPadding -> Column(Modifier.padding(contentPadding)) { Button( onClick = { // Create a new coroutine in the event handler to show a snackbar scope.launch { snackbarHostState.showSnackbar("Something happened!") } } ) { Text("Press me") } } } }SideEffectsSnippets.kt

LaunchedEffect restarts when one of the key parameters changes. However, in some situations you might want to capture a value in your effect that, if it changes, you do not want the effect to restart. In order to do this, it is required to use rememberUpdatedState to create a reference to this value which can be captured and updated. This approach is helpful for effects that contain long-lived operations that may be expensive or prohibitive to recreate and restart.

For example, suppose your app has a LandingScreen that disappears after some time. Even if LandingScreen is recomposed, the effect that waits for some time and notifies that the time passed shouldn't be restarted:

@Composable fun LandingScreen(onTimeout: () -> Unit) { // This will always refer to the latest onTimeout function that // LandingScreen was recomposed with val currentOnTimeout by rememberUpdatedState(onTimeout) // Create an effect that matches the lifecycle of LandingScreen. // If LandingScreen recomposes, the delay shouldn't start again. LaunchedEffect(true) { delay(SplashWaitTimeMillis) currentOnTimeout() } /* Landing screen content */ }SideEffectsSnippets.kt

To create an effect that matches the lifecycle of the call site, a never-changing constant like Unit or true is passed as a parameter. In the code above, LaunchedEffect(true) is used. To make sure that the onTimeout lambda always contains the latest value that LandingScreen was recomposed with, onTimeout needs to be wrapped with the rememberUpdatedState function. The returned State, currentOnTimeout in the code, should be used in the effect.

For side effects that need to be cleaned up after the keys change or if the composable leaves the Composition, use DisposableEffect. If the DisposableEffect keys change, the composable needs to dispose (do the cleanup for) its current effect, and reset by calling the effect again.

As an example, you might want to send analytics events based on Lifecycle events by using a LifecycleObserver. To listen for those events in Compose, use a DisposableEffect to register and unregister the observer when needed.

@Composable fun HomeScreen( lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current, onStart: () -> Unit, // Send the 'started' analytics event onStop: () -> Unit // Send the 'stopped' analytics event ) { // Safely update the current lambdas when a new one is provided val currentOnStart by rememberUpdatedState(onStart) val currentOnStop by rememberUpdatedState(onStop) // If `lifecycleOwner` changes, dispose and reset the effect DisposableEffect(lifecycleOwner) { // Create an observer that triggers our remembered callbacks // for sending analytics events val observer = LifecycleEventObserver { _, event -> if (event == Lifecycle.Event.ON_START) { currentOnStart() } else if (event == Lifecycle.Event.ON_STOP) { currentOnStop() } } // Add the observer to the lifecycle lifecycleOwner.lifecycle.addObserver(observer) // When the effect leaves the Composition, remove the observer onDispose { lifecycleOwner.lifecycle.removeObserver(observer) } } /* Home screen content */ }SideEffectsSnippets.kt

In the code above, the effect will add the observer to the lifecycleOwner. If lifecycleOwner changes, the effect is disposed and restarted with the new lifecycleOwner.

A DisposableEffect must include an onDispose clause as the final statement in its block of code. Otherwise, the IDE displays a build-time error.

To share Compose state with objects not managed by compose, use the SideEffect composable. Using a SideEffect guarantees that the effect executes after every successful recomposition. On the other hand, it is incorrect to perform an effect before a successful recomposition is guaranteed, which is the case when writing the effect directly in a composable.

For example, your analytics library might allow you to segment your user population by attaching custom metadata ("user properties" in this example) to all subsequent analytics events. To communicate the user type of the current user to your analytics library, use SideEffect to update its value.

@Composable fun rememberFirebaseAnalytics(user: User): FirebaseAnalytics { val analytics: FirebaseAnalytics = remember { FirebaseAnalytics() } // On every successful composition, update FirebaseAnalytics with // the userType from the current User, ensuring that future analytics // events have this metadata attached SideEffect { analytics.setUserProperty("userType", user.userType) } return analytics }SideEffectsSnippets.kt

produceState launches a coroutine scoped to the Composition that can push values into a returned State. Use it to convert non-Compose state into Compose state, for example bringing external subscription-driven state such as Flow, LiveData, or RxJava into the Composition.

The producer is launched when produceState enters the Composition, and will be cancelled when it leaves the Composition. The returned State conflates; setting the same value won't trigger a recomposition.

Even though produceState creates a coroutine, it can also be used to observe non-suspending sources of data. To remove the subscription to that source, use the awaitDispose function.

The following example shows how to use produceState to load an image from the network. The loadNetworkImage composable function returns a State that can be used in other composables.

@Composable fun loadNetworkImage( url: String, imageRepository: ImageRepository = ImageRepository() ): State<Result<Image>> { // Creates a State<T> with Result.Loading as initial value // If either `url` or `imageRepository` changes, the running producer // will cancel and will be re-launched with the new inputs. return produceState<Result<Image>>(initialValue = Result.Loading, url, imageRepository) { // In a coroutine, can make suspend calls val image = imageRepository.load(url) // Update State with either an Error or Success result. // This will trigger a recomposition where this State is read value = if (image == null) { Result.Error } else { Result.Success(image) } } }SideEffectsSnippets.kt

Key Point: Under the hood, produceState makes use of other effects! It holds a result variable using remember { mutableStateOf(initialValue) }, and triggers the producer block in a LaunchedEffect. Whenever value is updated in the producer block, the result state is updated to the new value.

You can easily create your own effects building on top of the existing APIs.

In Compose, recomposition occurs each time an observed state object or composable input changes. A state object or input may be changing more often than the UI actually needs to update, leading to unnecessary recomposition.

You should use the derivedStateOf function when your inputs to a composable are changing more often than you need to recompose. This often occurs when something is frequently changing, such as a scroll position, but the composable only needs to react to it once it crosses a certain threshold. derivedStateOf creates a new Compose state object you can observe that only updates as much as you need. In this way, it acts similarly to the Kotlin Flows distinctUntilChanged() operator.

The following snippet shows an appropriate use case for derivedStateOf:

@Composable // When the messages parameter changes, the MessageList // composable recomposes. derivedStateOf does not // affect this recomposition. fun MessageList(messages: List<Message>) { Box { val listState = rememberLazyListState() LazyColumn(state = listState) { // ... } // Show the button if the first visible item is past // the first item. We use a remembered derived state to // minimize unnecessary compositions val showButton by remember { derivedStateOf { listState.firstVisibleItemIndex > 0 } } AnimatedVisibility(visible = showButton) { ScrollToTopButton() } } }SideEffectsSnippets.kt

In this snippet, firstVisibleItemIndex changes any time the first visible item changes. As you scroll, the value becomes 0, 1, 2, 3, 4, 5, etc. However, recomposition only needs to occur if the value is greater than 0. This mismatch in update frequency means that this is a good use case for derivedStateOf.

A common mistake is to assume that, when you combine two Compose state objects, you should use derivedStateOf because you are "deriving state". However, this is purely overhead and not required, as shown in the following snippet:

// DO NOT USE. Incorrect usage of derivedStateOf. var firstName by remember { mutableStateOf("") } var lastName by remember { mutableStateOf("") } val fullNameBad by remember { derivedStateOf { "$firstName $lastName" } } // This is bad!!! val fullNameCorrect = "$firstName $lastName" // This is correctSideEffectsSnippets.kt

In this snippet, fullName needs to update just as often as firstName and lastName. Therefore, no excess recomposition is occurring, and using derivedStateOf is not necessary.

Use snapshotFlow to convert State<T> objects into a cold Flow. snapshotFlow runs its block when collected and emits the result of the State objects read in it. When one of the State objects read inside the snapshotFlow block mutates, the Flow will emit the new value to its collector if the new value is not equal to the previous emitted value (this behavior is similar to that of Flow.distinctUntilChanged).

The following example shows a side effect that records when the user scrolls past the first item in a list to analytics:

In the code above, listState.firstVisibleItemIndex is converted to a Flow that can benefit from the power of Flow's operators.

Some effects in Compose, like LaunchedEffect, produceState, or DisposableEffect, take a variable number of arguments, keys, that are used to cancel the running effect and start a new one with the new keys.

The typical form for these APIs is:

Due to the subtleties of this behavior, problems can occur if the parameters used to restart the effect are not the right ones:

As a rule of thumb, mutable and immutable variables used in the effect block of code should be added as parameters to the effect composable. Apart from those, more parameters can be added to force restarting the effect. If the change of a variable shouldn't cause the effect to restart, the variable should be wrapped in rememberUpdatedState. If the variable never changes because it's wrapped in a remember with no keys, you don't need to pass the variable as a key to the effect.

In the DisposableEffect code shown above, the effect takes as a parameter the lifecycleOwner used in its block, because any change to them should cause the effect to restart.

currentOnStart and currentOnStop are not needed as DisposableEffect keys, because their value never change in Composition due to the usage of rememberUpdatedState. If you don't pass lifecycleOwner as a parameter and it changes, HomeScreen recomposes, but the DisposableEffect isn't disposed of and restarted. That causes problems because the wrong lifecycleOwner is used from that point onward.

You can use a constant like true as an effect key to make it follow the lifecycle of the call site. There are valid use cases for it, like the LaunchedEffect example shown above. However, before doing that, think twice and make sure that's what you need.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (swift):
```swift
val listState = rememberLazyListState()

LazyColumn(state = listState) {
    // ...
}

LaunchedEffect(listState) {
    snapshotFlow { listState.firstVisibleItemIndex }
        .map { index -> index > 0 }
        .distinctUntilChanged()
        .filter { it == true }
        .collect {
            MyAnalyticsService.sendScrolledPastFirstItemEvent()
        }
}
```

Example 2 (unknown):
```unknown
EffectName(restartIfThisKeyChanges, orThisKey, orThisKey, ...) { block }
```

Example 3 (typescript):
```typescript
@Composable
fun HomeScreen(
    lifecycleOwner: LifecycleOwner = LocalLifecycleOwner.current,
    onStart: () -> Unit, // Send the 'started' analytics event
    onStop: () -> Unit // Send the 'stopped' analytics event
) {
    // These values never change in Composition
    val currentOnStart by rememberUpdatedState(onStart)
    val currentOnStop by rememberUpdatedState(onStop)

    DisposableEffect(lifecycleOwner) {
        val observer = LifecycleEventObserver { _, event ->
            /* ... */
        }

        lifecycleOwner.lifecycle.addObserver(observer)
        onDispose {
            lifecycleOwner.lifecycle.removeObserver(observer)
        }
    }
}
```

---

## Animation modifiers and composables Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/composables-modifiers

**Contents:**
- Animation modifiers and composables Stay organized with collections Save and categorize content based on your preferences.
- Built-in animated composables
  - Animate appearance and disappearance
    - Enter and exit transition examples
    - Animate enter and exit for children
    - Add custom animation
  - Animate based on target state
    - Animate child enter and exit transitions
    - Add custom animation
  - Animate between two layouts

Compose comes with built-in composables and modifiers for handling common animation use cases.

Compose provides several composables that animate content appearance, disappearance, and layout changes.

The AnimatedVisibility composable animates the appearance and disappearance of its content.

var visible by remember { mutableStateOf(true) } // Animated visibility will eventually remove the item from the composition once the animation has finished. AnimatedVisibility(visible) { // your composable here // ... }AnimationQuickGuide.kt

By default, the content appears by fading in and expanding, and it disappears by fading out and shrinking. Customize this transition by specifying EnterTransition and ExitTransition objects.

var visible by remember { mutableStateOf(true) } val density = LocalDensity.current AnimatedVisibility( visible = visible, enter = slideInVertically { // Slide in from 40 dp from the top. with(density) { -40.dp.roundToPx() } } + expandVertically( // Expand from the top. expandFrom = Alignment.Top ) + fadeIn( // Fade in with the initial alpha of 0.3f. initialAlpha = 0.3f ), exit = slideOutVertically() + shrinkVertically() + fadeOut() ) { Text( "Hello", Modifier .fillMaxWidth() .height(200.dp) ) }AnimationSnippets.kt

As shown in the preceding example, you can combine multiple EnterTransition or ExitTransition objects with a + operator, and each accepts optional parameters to customize its behavior. See the reference pages for more information.

AnimatedVisibility also offers a variant that takes a MutableTransitionState argument. This lets you trigger an animation as soon as the AnimatedVisibility composable is added to the composition tree. It is also useful for observing the animation state.

// Create a MutableTransitionState<Boolean> for the AnimatedVisibility. val state = remember { MutableTransitionState(false).apply { // Start the animation immediately. targetState = true } } Column { AnimatedVisibility(visibleState = state) { Text(text = "Hello, world!") } // Use the MutableTransitionState to know the current animation state // of the AnimatedVisibility. Text( text = when { state.isIdle && state.currentState -> "Visible" !state.isIdle && state.currentState -> "Disappearing" state.isIdle && !state.currentState -> "Invisible" else -> "Appearing" } ) }AnimationSnippets.kt

Content within AnimatedVisibility (direct or indirect children) can use the animateEnterExit modifier to specify different animation behavior for each of them. The visual effect for each of these children is a combination of the animations specified at the AnimatedVisibility composable and the child's own enter and exit animations.

var visible by remember { mutableStateOf(true) } AnimatedVisibility( visible = visible, enter = fadeIn(), exit = fadeOut() ) { // Fade in/out the background and the foreground. Box( Modifier .fillMaxSize() .background(Color.DarkGray) ) { Box( Modifier .align(Alignment.Center) .animateEnterExit( // Slide in/out the inner box. enter = slideInVertically(), exit = slideOutVertically() ) .sizeIn(minWidth = 256.dp, minHeight = 64.dp) .background(Color.Red) ) { // Content of the notification… } } }AnimationSnippets.kt

In some cases, you may want to have AnimatedVisibility apply no animations at all so that children can each have their own distinct animations by animateEnterExit. To achieve this, specify EnterTransition.None and ExitTransition.None at the AnimatedVisibility composable.

If you want to add custom animation effects beyond the built-in enter and exit animations, access the underlying Transition instance using the transition property inside the content lambda for AnimatedVisibility. Any animation states added to the Transition instance will run simultaneously with the enter and exit animations of AnimatedVisibility. AnimatedVisibility waits until all animations in the Transition have finished before removing its content. For exit animations created independent of Transition (such as using animate*AsState), AnimatedVisibility wouldn't be able to account for them, and therefore might remove the content composable before they finish.

var visible by remember { mutableStateOf(true) } AnimatedVisibility( visible = visible, enter = fadeIn(), exit = fadeOut() ) { // this: AnimatedVisibilityScope // Use AnimatedVisibilityScope#transition to add a custom animation // to the AnimatedVisibility. val background by transition.animateColor(label = "color") { state -> if (state == EnterExitState.Visible) Color.Blue else Color.Gray } Box( modifier = Modifier .size(128.dp) .background(background) ) }AnimationSnippets.kt

To learn more about using Transition to manage animations, see Animate multiple properties simultaneously with a transition.

The AnimatedContent composable animates its content as it changes based on a target state.

Row { var count by remember { mutableIntStateOf(0) } Button(onClick = { count++ }) { Text("Add") } AnimatedContent( targetState = count, label = "animated content" ) { targetCount -> // Make sure to use `targetCount`, not `count`. Text(text = "Count: $targetCount") } }AnimationSnippets.kt

By default, the initial content fades out and then the target content fades in (this behavior is called fade through). You can customize this animation behavior by specifying a ContentTransform object to the transitionSpec parameter. You can create an instance of ContentTransform by combining an EnterTransition object with an ExitTransition object using the with infix function. You can apply SizeTransform to the ContentTransform object by attaching it with the using infix function.

AnimatedContent( targetState = count, transitionSpec = { // Compare the incoming number with the previous number. if (targetState > initialState) { // If the target number is larger, it slides up and fades in // while the initial (smaller) number slides up and fades out. slideInVertically { height -> height } + fadeIn() togetherWith slideOutVertically { height -> -height } + fadeOut() } else { // If the target number is smaller, it slides down and fades in // while the initial number slides down and fades out. slideInVertically { height -> -height } + fadeIn() togetherWith slideOutVertically { height -> height } + fadeOut() }.using( // Disable clipping since the faded slide-in/out should // be displayed out of bounds. SizeTransform(clip = false) ) }, label = "animated content" ) { targetCount -> Text(text = "$targetCount") }AnimationSnippets.kt

EnterTransition defines how the target content should appear, and ExitTransition defines how the initial content should disappear. In addition to all of the EnterTransition and ExitTransition functions available for AnimatedVisibility, AnimatedContent offers slideIntoContainer and slideOutOfContainer. These are convenient alternatives to slideInHorizontally/Vertically and slideOutHorizontally/Vertically that calculate the slide distance based on the sizes of the initial content and the target content of the AnimatedContent content.

SizeTransform defines how the size should animate between the initial and the target contents. You have access to both the initial size and the target size when you are creating the animation. SizeTransform also controls whether the content should be clipped to the component size during animations.

var expanded by remember { mutableStateOf(false) } Surface( color = MaterialTheme.colorScheme.primary, onClick = { expanded = !expanded } ) { AnimatedContent( targetState = expanded, transitionSpec = { fadeIn(animationSpec = tween(150, 150)) togetherWith fadeOut(animationSpec = tween(150)) using SizeTransform { initialSize, targetSize -> if (targetState) { keyframes { // Expand horizontally first. IntSize(targetSize.width, initialSize.height) at 150 durationMillis = 300 } } else { keyframes { // Shrink vertically first. IntSize(initialSize.width, targetSize.height) at 150 durationMillis = 300 } } } }, label = "size transform" ) { targetExpanded -> if (targetExpanded) { Expanded() } else { ContentIcon() } } }AnimationSnippets.kt

Just like AnimatedVisibility, the animateEnterExit modifier is available inside the content lambda of AnimatedContent. Use this to apply EnterAnimation and ExitAnimation to each of the direct or indirect children separately.

Just like AnimatedVisibility, the transition field is available inside the content lambda of AnimatedContent. Use this to create a custom animation effect that runs simultaneously with the AnimatedContent transition. See updateTransition for the details.

Crossfade animates between two layouts with a crossfade animation. By toggling the value passed to the current parameter, the content is switched with a crossfade animation.

var currentPage by remember { mutableStateOf("A") } Crossfade(targetState = currentPage, label = "cross fade") { screen -> when (screen) { "A" -> Text("Page A") "B" -> Text("Page B") } }AnimationSnippets.kt

Compose provides modifiers for animating specific changes directly on composables.

The animateContentSize modifier animates a size change.

var expanded by remember { mutableStateOf(false) } Box( modifier = Modifier .background(colorBlue) .animateContentSize() .height(if (expanded) 400.dp else 200.dp) .fillMaxWidth() .clickable( interactionSource = remember { MutableInteractionSource() }, indication = null ) { expanded = !expanded } ) { }AnimationQuickGuide.kt

If you are looking to animate item reorderings inside a Lazy list or grid, take a look at the Lazy layout item animation documentation.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Jetpack Compose architectural layering Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layering

**Contents:**
- Jetpack Compose architectural layering Stay organized with collections Save and categorize content based on your preferences.
- Layers
- Design principles
  - Control
  - Customization
  - Picking the right abstraction
  - Learn more
- Recommended for you

This page provides a high-level overview of the architectural layers that make up Jetpack Compose, and the core principles that inform this design.

Jetpack Compose is not a single monolithic project; it is created from a number of modules which are assembled together to form a complete stack. Understanding the different modules that make up Jetpack Compose enables you to:

The major layers of Jetpack Compose are:

Figure 1. The major layers of Jetpack Compose.

Each layer is built upon the lower levels, combining functionality to create higher level components. Each layer builds on public APIs of the lower layers to verify the module boundaries and enable you to replace any layer should you need to. Let's examine these layers from the bottom up.

A guiding principle for Jetpack Compose is to provide small, focused pieces of functionality that can be assembled (or composed) together, rather than a few monolithic components. This approach has a number of advantages.

Higher level components tend to do more for you, but limit the amount of direct control that you have. If you need more control, you can "drop down" to use a lower level component.

For example, if you want to animate the color of a component you might use the animateColorAsState API:

val color = animateColorAsState(if (condition) Color.Green else Color.Red)ArchitectureLayering.kt

However, if you needed the component to always start out grey, you cannot do it with this API. Instead, you can drop down to use the lower level Animatable API:

val color = remember { Animatable(Color.Gray) } LaunchedEffect(condition) { color.animateTo(if (condition) Color.Green else Color.Red) }ArchitectureLayering.kt

The higher level animateColorAsState API is itself built upon the lower level Animatable API. Using the lower level API is more complex but offers more control. Choose the level of abstraction that best suits your needs.

Assembling higher level components from smaller building blocks makes it far easier to customize components should you need to. For example, consider the implementation of Button provided by the Material layer:

@Composable fun Button( // … content: @Composable RowScope.() -> Unit ) { Surface(/* … */) { CompositionLocalProvider(/* … */) { // set LocalContentAlpha ProvideTextStyle(MaterialTheme.typography.button) { Row( // … content = content ) } } } }ArchitectureLayering.kt

A Button is assembled from 4 components:

A material Surface providing the background, shape, click handling, etc.

A CompositionLocalProvider which changes the content’s alpha when the button is enabled or disabled

A ProvideTextStyle sets the default text style to use

A Row provides the default layout policy for the button's content

We have omitted some parameters and comments to make the structure clearer, but the entire component is only around 40 lines of code because it simply assembles these 4 components to implement the button. Components like Button are opinionated about which parameters they expose, balancing enabling common customizations against an explosion of parameters that can make a component harder to use. Material components, for example, offer customizations specified in the Material Design system, making it easy to follow material design principles.

If, however, you wish to make a customization beyond a component's parameters, then you can "drop down" a level and fork a component. For example, Material Design specifies that buttons should have a solid colored background. If you need a gradient background, this option is not supported by the Button parameters. In this case you can use the Material Button implementation as a reference and build your own component:

@Composable fun GradientButton( // … background: List<Color>, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Row( // … modifier = modifier .clickable(onClick = {}) .background( Brush.horizontalGradient(background) ) ) { CompositionLocalProvider(/* … */) { // set material LocalContentAlpha ProvideTextStyle(MaterialTheme.typography.button) { content() } } } }ArchitectureLayering.kt

The above implementation continues to use components from the Material layer, such as Material’s concepts of current content alpha and the current text style. However, it replaces the material Surface with a Row and styles it to achieve the desired appearance.

If you do not want to use Material concepts at all, for example if building your own bespoke design system, then you can drop down to purely using foundation layer components:

@Composable fun BespokeButton( // … backgroundColor: Color, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Row( // … modifier = modifier .clickable(onClick = {}) .background(backgroundColor) ) { // No Material components used content() } }ArchitectureLayering.kt

Jetpack Compose reserves the simplest names for the highest level components. For example, androidx.compose.material.Text is built upon androidx.compose.foundation.text.BasicText. This makes it possible to provide your own implementation with the most discoverable name if you wish to replace higher levels.

Compose’s philosophy of building layered, reusable components means that you should not always reach for the lower level building blocks. Many higher level components not only offer more functionality but often implement best practices such as supporting accessibility.

For example, if you wanted to add gesture support to your custom component, you could build this from scratch using Modifier.pointerInput but there are other, higher level components built on top of this which may offer a better starting point, for example Modifier.draggable, Modifier.scrollable or Modifier.swipeable.

As a rule, prefer building on the highest-level component which offers the functionality you need in order to benefit from the best practices they include.

See the Jetsnack sample for an example of building a custom design system.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Lifecycle of composables Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/lifecycle

**Contents:**
- Lifecycle of composables Stay organized with collections Save and categorize content based on your preferences.
- Lifecycle overview
- Anatomy of a composable in Composition
  - Add extra information to help smart recompositions
  - Skipping if the inputs haven't changed
- Recommended for you

In this page, you'll learn about the lifecycle of a composable and how Compose decides whether a composable needs recomposition.

As mentioned in the Managing state documentation, a Composition describes the UI of your app and is produced by running composables. A Composition is a tree-structure of the composables that describe your UI.

When Jetpack Compose runs your composables for the first time, during initial composition, it will keep track of the composables that you call to describe your UI in a Composition. Then, when the state of your app changes, Jetpack Compose schedules a recomposition. Recomposition is when Jetpack Compose re-executes the composables that may have changed in response to state changes, and then updates the Composition to reflect any changes.

A Composition can only be produced by an initial composition and updated by recomposition. The only way to modify a Composition is through recomposition.

Recomposition is typically triggered by a change to a State<T> object. Compose tracks these and runs all composables in the Composition that read that particular State<T>, and any composables that they call that cannot be skipped.

If a composable is called multiple times, multiple instances are placed in the Composition. Each call has its own lifecycle in the Composition.

@Composable fun MyComposable() { Column { Text("Hello") Text("World") } }LifecycleSnippets.kt

The instance of a composable in Composition is identified by its call site. The Compose compiler considers each call site as distinct. Calling composables from multiple call sites will create multiple instances of the composable in Composition.

If during a recomposition a composable calls different composables than it did during the previous composition, Compose will identify which composables were called or not called and for the composables that were called in both compositions, Compose will avoid recomposing them if their inputs haven't changed.

Preserving identity is crucial to associate side effects with their composable, so that they can complete successfully rather than restart for every recomposition.

Consider the following example:

@Composable fun LoginScreen(showError: Boolean) { if (showError) { LoginError() } LoginInput() // This call site affects where LoginInput is placed in Composition } @Composable fun LoginInput() { /* ... */ } @Composable fun LoginError() { /* ... */ }LifecycleSnippets.kt

In the code snippet above, LoginScreen will conditionally call the LoginError composable and will always call the LoginInput composable. Each call has a unique call site and source position, which the compiler will use to uniquely identify it.

Even though LoginInput went from being called first to being called second, the LoginInput instance will be preserved across recompositions. Additionally, because LoginInput doesn’t have any parameters that have changed across recomposition, the call to LoginInput will be skipped by Compose.

Calling a composable multiple times will add it to Composition multiple times as well. When calling a composable multiple times from the same call site, Compose doesn’t have any information to uniquely identify each call to that composable, so the execution order is used in addition to the call site in order to keep the instances distinct. This behavior is sometimes all that is needed, but in some cases it can cause unwanted behavior.

@Composable fun MoviesScreen(movies: List<Movie>) { Column { for (movie in movies) { // MovieOverview composables are placed in Composition given its // index position in the for loop MovieOverview(movie) } } }LifecycleSnippets.kt

In the example above, Compose uses the execution order in addition to the call site to keep the instance distinct in the Composition. If a new movie is added to the bottom of the list, Compose can reuse the instances already in the Composition since their location in the list haven't changed and therefore, the movie input is the same for those instances.

However, if the movies list changes by either adding to the top or the middle of the list, removing or reordering items, it'll cause a recomposition in all MovieOverview calls whose input parameter has changed position in the list. That's extremely important if, for example, MovieOverview fetches a movie image using a side effect. If recomposition happens while the effect is in progress, it will be cancelled and will start again.

@Composable fun MovieOverview(movie: Movie) { Column { // Side effect explained later in the docs. If MovieOverview // recomposes, while fetching the image is in progress, // it is cancelled and restarted. val image = loadNetworkImage(movie.url) MovieHeader(image) /* ... */ } } LifecycleSnippets.kt

Ideally, we want to think of the identity of the MovieOverview instance as linked to the identity of the movie that is passed to it. If we reorder the list of movies, ideally we would similarly reorder the instances in the Composition tree instead of recomposing each MovieOverview composable with a different movie instance. Compose provides a way for you to tell the runtime what values you want to use to identify a given part of the tree: the key composable.

By wrapping a block of code with a call to the key composable with one or more values passed in, those values will be combined to be used to identify that instance in the composition. The value for a key does not need to be globally unique, it needs to only be unique amongst the invocations of composables at the call site. So in this example, each movie needs to have a key that's unique among the movies; it's fine if it shares that key with some other composable elsewhere in the app.

@Composable fun MoviesScreenWithKey(movies: List<Movie>) { Column { for (movie in movies) { key(movie.id) { // Unique ID for this movie MovieOverview(movie) } } } }LifecycleSnippets.kt

With the above, even if the elements on the list change, Compose recognizes individual calls to MovieOverview and can reuse them.

Some composables have built-in support for the key composable. For example, LazyColumn accepts specifying a custom key in the items DSL.

@Composable fun MoviesScreenLazy(movies: List<Movie>) { LazyColumn { items(movies, key = { movie -> movie.id }) { movie -> MovieOverview(movie) } } }LifecycleSnippets.kt

During recomposition, some eligible composable functions can have their execution be skipped entirely if their inputs have not changed from the previous composition.

A composable function is eligible for skipping unless:

There is an experimental compiler mode, Strong Skipping, which relaxes the last requirement.

In order for a type to be considered stable it must comply with the following contract:

There are some important common types that fall into this contract that the Compose compiler will treat as stable, even though they are not explicitly marked as stable by using the @Stable annotation:

All of these types are able to follow the contract of stable because they are immutable. Since immutable types never change, they never have to notify Composition of the change, so it is much easier to follow this contract.

One notable type that is stable but is mutable is Compose’s MutableState type. If a value is held in a MutableState, the state object overall is considered to be stable as Compose will be notified of any changes to the .value property of State.

When all types passed as parameters to a composable are stable, the parameter values are compared for equality based on the composable position in the UI tree. Recomposition is skipped if all the values are unchanged since the previous call.

Compose considers a type stable only if it can prove it. For example, an interface is generally treated as not stable, and types with mutable public properties whose implementation could be immutable are not stable either.

If Compose is not able to infer that a type is stable, but you want to force Compose to treat it as stable, mark it with the @Stable annotation.

// Marking the type as stable to favor skipping and smart recompositions. @Stable interface UiState<T : Result<T>> { val value: T? val exception: Throwable? val hasError: Boolean get() = exception != null }LifecycleSnippets.kt

In the code snippet above, since UiState is an interface, Compose could ordinarily consider this type to be not stable. By adding the @Stable annotation, you tell Compose that this type is stable, allowing Compose to favor smart recompositions. This also means that Compose will treat all its implementations as stable if the interface is used as the parameter type.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## State lifespans in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/state-lifespans

**Contents:**
- State lifespans in Compose Stay organized with collections Save and categorize content based on your preferences.
- Choose the correct lifespan
  - remember
  - rememberSaveable and rememberSerializable
  - retain
    - retain versus ViewModel
  - Combine retain and rememberSaveable or rememberSerializable
- Positional memoization and adaptive layouts
- Remember factory functions

In Jetpack Compose, composable functions often hold state using the remember function. Values that are remembered can be reused across recompositions, as explained in State and Jetpack Compose.

While remember serves as a tool to persist values across recompositions, state often needs to live beyond the lifetime of a composition. This page explains the difference between the remember, retain, rememberSaveable, and rememberSerializable APIs, when to choose which API, and what the best practices are for managing remembered and retained values in Compose.

In Compose, there are several functions you can use to persist state across compositions and beyond: remember, retain, rememberSaveable, and rememberSerializable. These functions differ in their lifespan and semantics, and are each suited for storing specific kinds of state. The differences are outlined in the following table:

rememberSaveable, rememberSerializable

Values survive recompositions?

Values survive activity recreations?

The same (===) instance will always be returned

An equivalent (==) object will be returned, possibly a deserialized copy

Values survive process death?

Must not reference any objects that would be leaked if the activity is destroyed

Must be serializable(either with a custom Saver or with kotlinx.serialization)

remember is the most common way to store state in Compose. When remember is called for the first time, the given calculation is executed and is remembered, meaning that it is stored by Compose for future reuse by the composable. When a composable recomposes, it executes its code again, but any calls to remember return their values from the previous composition instead of executing the calculation again.

Each instance of a composable function has its own set of remembered values, referred to as positional memoization. When remembered values are memoized for use across recompositions, they are tied to their position in the composition hierarchy. If a composable is used in different locations, each instance in the composition hierarchy has its own set of remembered values.

When a remembered value is no longer used, it is forgotten and its record is discarded. Remembered values are forgotten when they are removed from the composition hierarchy (Including when a value is removed and re-added to move to a different location without the use of the key composable or MovableContent), or called with different key parameters.

Of the choices available, remember has the shortest lifespan and forgets values the earliest of the four memoization functions described in this page. This makes it best-suited for:

However, you should avoid:

rememberSaveable and rememberSerializable build on top of remember. They have the longest lifespan of the memoization functions discussed in this guide. In addition to positionally memoizing objects across recompositions, it can also save values so that they can be restored across activity recreations, including from configuration changes and process death (when the system kills your app's process while it is in the background, usually either to free memory for foreground apps or if the user revokes permissions from your app while it is running).

rememberSerializable works in the same way as rememberSaveable, but automatically supports persisting complex types that are serializable with the kotlinx.serialization library. Choose rememberSerializable if your type is (or can be) marked with @Serializable, and rememberSaveable in all other cases.

This makes both rememberSaveable and rememberSerializable perfect candidates for storing state associated with user input, including text field entry, scroll position, toggle states, etc. You should save this state to ensure that the user never loses their place. In general, you should use rememberSaveable or rememberSerializable to memoize any state that your app isn't able to retrieve from another persistent data source, such as a database.

Note that rememberSaveable and rememberSerializable save their memoized values by serializing them into a Bundle. This has two consequences:

To store more complicated data types without using kotlinx.serialization, you can implement a custom Saver to serialize and deserialize your object into supported data types. Note that Compose understands common data types like State, List, Map, Set, etc. out-of-the-box, and automatically converts these into supported types on your behalf. The following is an example of a Saver for a Size class. It's implemented by packing all of Size's properties into a list using listSaver.

data class Size(val x: Int, val y: Int) { object Saver : androidx.compose.runtime.saveable.Saver<Size, Any> by listSaver( save = { listOf(it.x, it.y) }, restore = { Size(it[0], it[1]) } ) } @Composable fun rememberSize(x: Int, y: Int) { rememberSaveable(x, y, saver = Size.Saver) { Size(x, y) } }StateLifespansSnippets.kt

The retain API exists between remember and rememberSaveable/rememberSerializable in terms of how long it memoizes its values. It is named differently because retained values also experience a different lifecycle than their remembered counterparts.

When a value is retained, it is both positionally memoized and saved in a secondary data structure that has a separate lifespan that is tied to the app's lifespan. A retained value is able to survive configuration changes without being serialized, but cannot survive process death. If a value is not used after the composition hierarchy is recreated, the retained value is retired (which is retain's equivalent of being forgotten).

In exchange for this shorter-than-rememberSaveable lifecycle, retain is able to persist values that can't be serialized, like lambda expressions, flows, and large objects like bitmaps. For example, you can use retain to manage a media player (such as ExoPlayer) to prevent interruptions to media playback during a configuration change.

@Composable fun MediaPlayer() { // Use the application context to avoid a memory leak val applicationContext = LocalContext.current.applicationContext val exoPlayer = retain { ExoPlayer.Builder(applicationContext).apply { /* ... */ }.build() } // ... }StateLifespansSnippets.kt

Avoid retaining objects that are created and managed outside of your control, including Activity, View, Fragment, ViewModel, Context, Lifecycle, and any object that references one of these types. Generally speaking, if you wouldn't store an object in a ViewModel, you shouldn't retain it either.

To mark a class in your code as a type that shouldn't be retained, you can annotate it with @DoNotRetain.

We are also working on adding retain support to androidx.navigation and androidx.navigation3, which will be available in future releases of the libraries.

At their cores, both retain and ViewModel offer similar functionality in their most commonly used ability to persist object instances across configuration changes. The choice to reach for retain or ViewModel lies in the type of value you're persisting, how it should be scoped, and whether you need additional functionality.

ViewModels are objects that typically encapsulate the communication between your app's UI and data layers. They allow you to move logic out of your composable functions, which improves testability. ViewModels are managed as singletons within a ViewModelStore and have a different lifespan from retained values. While a ViewModel will remain active until its ViewModelStore is destroyed, retained values are retired when the content is permanently removed from the composition (for a configuration change, as an example, this means that a retained value is retired if the UI hierarchy is recreated and the retained value wasn't consumed after the composition is recreated).

ViewModel also includes out-of-the-box integrations for dependency injection with Dagger and Hilt, integration with SavedState, and built-in coroutine support for launching background tasks. This makes ViewModel an ideal place to launch background tasks and network requests, interact with other data sources in your project, and optionally capture and persist mission-critical UI state that should be both retained across configuration changes in the ViewModel and survive process death.

retain is best suited for objects that are scoped to specific composable instances and don't require reuse or sharing between sibling composables. Where ViewModel acts as a good place to store UI state and perform background tasks, retain is a good candidate for storing objects for UI plumbing like caches, impression tracking and analytics, dependencies on AndroidViews, and other objects that interact with the Android OS or manage third party libraries like payment processors or advertising.

For advanced users designing custom app architecture patterns outside of the Modern Android app architecture recommendations: retain can also be used to build an in-house "ViewModel-like" API. Although support for coroutines and saved-state is not offered out-of-the-box, retain can serve as the building block for the lifecycle of such ViewModel-look-alikes with these features built on top. The specifics of how to design such a component are outside the scope of this guide.

No shared values; each value is retained at and associated with a specific point in the composition hierarchy. Retaining the same type in a different location always acts on a new instance.

ViewModels are singletons within a ViewModelStore

When permanently leaving the composition hierarchy

When the ViewModelStore is cleared or destroyed

Additional functionality

Can receive callbacks when the object is in the composition hierarchy or not

Built-in coroutineScope, support for SavedStateHandle, can be injected using Hilt

Sometimes, an object needs to have a hybrid lifespan of both retained and rememberSaveable or rememberSerializable. This may be an indicator that your object should be a ViewModel, which can support saved state as described in the Saved State module for ViewModel guide.

it is possible to use retain and rememberSaveable or rememberSerializable simultaneously. Correctly combining both lifecycles adds significant complexity. We recommend employing this pattern as part of more advanced and custom architecture patterns, and only when all of the following are true:

When all of these are the case, we recommend splitting your class into three parts: The saved data, the retained data, and a "mediator" object that has no state of its own and delegates onto the retained and saved objects to update state accordingly. This pattern takes the following shape:

@Composable fun rememberAndRetain(): CombinedRememberRetained { val saveData = rememberSerializable(serializer = serializer<ExtractedSaveData>()) { ExtractedSaveData() } val retainData = retain { ExtractedRetainData() } return remember(saveData, retainData) { CombinedRememberRetained(saveData, retainData) } } @Serializable data class ExtractedSaveData( // All values that should persist process death should be managed by this class. var savedData: AnotherSerializableType = defaultValue() ) class ExtractedRetainData { // All values that should be retained should appear in this class. // It's possible to manage a CoroutineScope using RetainObserver. // See the full sample for details. var retainedData = Any() } class CombinedRememberRetained( private val saveData: ExtractedSaveData, private val retainData: ExtractedRetainData, ) { fun doAction() { // Manipulate the retained and saved state as needed. } }StateLifespansSnippets.kt

By separating the state by lifespan, the separation of responsibilities and storage becomes very explicit. It is intentional that save data cannot be manipulated by retain data, as this prevents a scenario where a save data update is attempted when the savedInstanceState bundle has already been captured and cannot be updated. It also allows for testing recreation scenarios by testing your constructors without calling into Compose or simulating an Activity recreation.

See the full sample (RetainAndSaveSample.kt) for a complete example of how this pattern may be implemented.

Retaining an object that has already been remembered does not properly retain and save the object, can cause the object to receive duplicate callbacks, and leads to unexpected bugs.

Android applications can support many form factors including phones, foldables, tablets, and desktops. Applications frequently need to transition between these form factors by using adaptive layouts. For example, an app running on a tablet may be able to show a two-column list-detail view, but may navigate between a list and detail page when presented on a smaller phone screen.

Because remembered and retained values are memoized positionally, they are only reused if they appear in the same point in the composition hierarchy. As your layouts adapt to different form factors, they may alter the structure of your composition hierarchy and lead to forgotten values.

For out-of-the-box components like ListDetailPaneScaffold and NavDisplay (from Jetpack Navigation 3), this is not an issue and your state will persist throughout layout changes. For custom components that adapt to form factors, ensure that state is unaffected by layout changes by doing one of the following:

Although Compose UIs are made up of composable functions, many objects go into the creation and organization of a composition. The most common example of this is complex composable objects that define their own state, like LazyList, which accepts a LazyListState.

When defining Compose-focused objects, we recommend creating a remember function to define the intended remembering behavior, including both lifespan and key inputs. This allows consumers of your state to confidently create instances in the composition hierarchy that will survive and be invalidated as expected. When defining a composable factory function, follow these guidelines:

@Composable fun rememberImageState( imageUri: String, initialZoom: Float = 1f, initialPanX: Int = 0, initialPanY: Int = 0 ): ImageState { return rememberSaveable(imageUri, saver = ImageState.Saver) { ImageState( imageUri, initialZoom, initialPanX, initialPanY ) } } data class ImageState( val imageUri: String, val zoom: Float, val panX: Int, val panY: Int ) { object Saver : androidx.compose.runtime.saveable.Saver<ImageState, Any> by listSaver( save = { listOf(it.imageUri, it.zoom, it.panX, it.panY) }, restore = { ImageState(it[0] as String, it[1] as Float, it[2] as Int, it[3] as Int) } ) }StateLifespansSnippets.kt

Allowing instances to be created outside of composition has numerous benefits for consumers of your class, including testing, more flexibility in app architecture, and customization of the default behavior.

Verify that consumers of your state work correctly regardless of where the object originates.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## List of Compose modifiers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/modifiers-list

**Contents:**
- List of Compose modifiers Stay organized with collections Save and categorize content based on your preferences.
- Actions
- Alignment
- Animation
- Border
- Drawing
- Focus
- Graphics
- Keyboard
- Layout

Enable drag gestures between a set of predefined values.

This function is deprecated. startDragImmediately has been removed without replacement.

Enable drag gestures between a set of predefined values.

This function is deprecated. startDragImmediately has been removed without replacement.

Configure component to receive clicks via input or accessibility "click" event.

Configure component to receive clicks via input or accessibility "click" event.

Configure component to receive clicks, double clicks and long clicks via input or accessibility "click" event.

Configure component to receive clicks, double clicks and long clicks via input or accessibility "click" event.

Configure touch dragging for the UI element in both orientations.

Configure touch dragging for the UI element in a single Orientation.

Use this modifier to group a list of selectable items like Tabs or RadioButtons together for accessibility purpose.

Configure component to be selectable, usually as a part of a mutually exclusive group, where only one item can be selected at any point in time.

Configure component to be selectable, usually as a part of a mutually exclusive group, where only one item can be selected at any point in time.

This function is deprecated. Material's Swipeable has been replaced by Foundation's AnchoredDraggable APIs.

Enable swipe gestures between a set of predefined states.

Configure component to make it toggleable via input and accessibility events

Configure component to make it toggleable via input and accessibility events.

Configure component to make it toggleable via input and accessibility events with three states: On, Off and Indeterminate.

Configure component to make it toggleable via input and accessibility events with three states: On, Off and Indeterminate.

The modifier that should be applied on a drag handle composable so the drag handle can be dragged and operate on the provided PaneExpansionState properly.

Align the element vertically within the Row.

Position the element vertically such that the alignment line for the content as determined by alignmentLineBlock aligns with sibling elements also configured to alignBy.

Position the element vertically such that its alignmentLine aligns with sibling elements also configured to alignBy.

Position the element vertically such that its first baseline aligns with sibling elements also configured to alignByBaseline or alignBy.

Align the element horizontally within the Column.

Position the element horizontally such that the alignment line for the content as determined by alignmentLineBlock aligns with sibling elements also configured to alignBy.

Position the element horizontally such that its alignmentLine aligns with sibling elements also configured to alignBy.

Pull the content element to a specific Alignment within the Box.

Modifier to animate layout changes (position and/or size) that occur within a LookaheadScope.

Apply this modifier to a FloatingActionButton to show or hide it with an animation, typically based on the app's main content scrolling.

animateEnterExit modifier can be used for any direct or indirect children of AnimatedVisibility to create a different enter/exit animation than what's specified in AnimatedVisibility.

This modifier animates the item appearance (fade in), disappearance (fade out) and placement changes (such as an item reordering).

This modifier animates the item appearance (fade in), disappearance (fade out) and placement changes (such as an item reordering).

This modifier animates the item appearance (fade in), disappearance (fade out) and placement changes (such as an item reordering).

Modify element to add border with appearance specified with a border and a shape and clip it.

Modify element to add border with appearance specified with a width, a brush and a shape and clip it.

Modify element to add border with appearance specified with a width, a color and a shape and clip it.

Draw content with modified alpha that may be less than 1.

Draws shape with a solid color behind the content.

Draws shape with brush behind the content.

Clip the content to shape.

Clip the content to the bounds of a layer defined at this modifier.

Draw into a Canvas behind the modified content.

Draw into a DrawScope with content that is persisted across draw calls as long as the size of the drawing area is the same or any state objects that are read have not changed.

Creates a DrawModifier that allows the developer to draw before or after the layout's contents.

Draws visual effects for this component when interactions occur.

Paint the content using painter.

Draws a drop shadow behind the rest of the content with the geometry specified by the given shape and the shadow properties defined the DropShadowScope.

Draws a drop shadow behind the rest of the content with the geometry specified by the given shape and the shadow properties defined by the Shadow.

Draws an inner shadow behind the rest of the content with the geometry specified by the given shape and the shadow properties defined the InnerShadowScope.

Draws an inner shadow on top of the rest of the content with the geometry specified by the given shape and the shadow properties defined by the Shadow.

Creates a graphicsLayer that draws a shadow.

Adds padding to accommodate the safe drawing insets.

Creates a modifier that controls the drawing order for the children of the same layout parent.

Add this modifier to a component to observe focus state events.

Add this modifier to a component to observe focus state events.

This function is deprecated. Replaced by focusTarget

Add this modifier to a component to make it focusable.

This function is deprecated. Use focusProperties() instead

This function is deprecated. Use focusRequester() instead

This function is deprecated. Use focusProperties() and focusRequester() instead

This modifier allows you to specify properties that are accessible to focusTargets further down the modifier chain or on child layout nodes.

Add this modifier to a component to request changes to focus.

This modifier can be used to save and restore focus to a focus group.

This function is deprecated. Use focusRestorer(FocusRequester) instead

Creates a focus group or marks this component as a focus group.

Configure component to be focusable via focus system or accessibility "focus" event.

Calls onPositioned whenever the bounds of the currently-focused area changes.

hierarchicalFocusGroup is used to annotate composables in an application, so we can keep track of what is the active part of the composition.

This Modifier is used in conjunction with hierarchicalFocusGroup and will request focus on the following focusable element when needed (i.e. this needs to be before that element in the Modifier chain).

A Modifier.Node that makes content draw into a draw layer.

A Modifier.Element that makes content draw into a draw layer.

A Modifier.Element that adds a draw layer such that tooling can identify an element in the drawn image.

Adding this modifier to the modifier parameter of a component will allow it to intercept hardware key events when it (or one of its children) is focused.

Adding this modifier to the modifier parameter of a component will allow it to intercept hardware key events when it (or one of its children) is focused.

Alternative to androidx.compose.ui.layout.layoutId that enables the use of tag.

Tag the element with layoutId to identify the element within its parent.

Creates a LayoutModifier that allows changing how the wrapped element is measured and laid out.

Invoke onGloballyPositioned with the LayoutCoordinates of the element when the global position of the content may have changed.

A Modifier that can add padding to position the content according to specified distances from its bounds to an alignment line.

A Modifier that can add padding to position the content according to specified distances from its bounds to an alignment line.

A Modifier that positions the content in a layout such that the distance from the top of the layout to the baseline of the first line of text in the content is top, and the distance from the baseline of the last line of text in the content to the bottom of the layout is bottom.

A Modifier that positions the content in a layout such that the distance from the top of the layout to the baseline of the first line of text in the content is top, and the distance from the baseline of the last line of text in the content to the bottom of the layout is bottom.

Apply additional space along each edge of the content in Dp: left, top, right and bottom.

Apply all dp of additional space along each edge of the content, left, top, right and bottom.

Apply PaddingValues to the component as additional space along each edge of the content's left, top, right and bottom.

Apply horizontal dp space along the left and right edges of the content, and vertical dp space along the top and bottom edges.

Apply additional space along each edge of the content in Dp: start, top, end and bottom.

Adds padding to accommodate the caption bar insets.

Adds padding to accommodate the display cutout.

Adds padding to accommodate the ime insets.

Adds padding to accommodate the mandatory system gestures insets.

Adds padding to accommodate the navigation bars insets.

Adds padding to accommodate the safe content insets.

Adds padding to accommodate the safe gestures insets.

Adds padding to accommodate the status bars insets.

Adds padding to accommodate the system bars insets.

Adds padding to accommodate the system gestures insets.

Adds padding to accommodate the waterfall insets.

Adds padding so that the content doesn't enter insets space.

A Modifier that listens for and detects high-level gestures from an IndirectPointerEvent source.

Modifier that lets a developer define a pointer icon to display when the cursor is hovered over the element.

A special PointerInputModifier that provides access to the underlying MotionEvents originally dispatched to Compose.

This function is deprecated. Modifier.pointerInput must provide one or more 'key' parameters that define the identity of the modifier and determine when its previous input processing coroutine should be cancelled and a new effect launched for the new key.

Create a modifier for processing pointer input within the region of the modified element.

Create a modifier for processing pointer input within the region of the modified element.

Create a modifier for processing pointer input within the region of the modified element.

Offset the content by offset px.

Offset the content by (x dp, y dp).

Offset the content by offset px.

Offset the content by (x dp, y dp).

Modifier that takes up all the available width inside the TabRow, and then animates the offset of the indicator it is applied to, depending on the currentTabPosition.

This function is deprecated. Solely for use alongside deprecated TabRowDefaults.Indicator method.

Contains the semantics required for an indeterminate progress indicator, that represents the fact of the in-progress operation.

Contains the semantics required for a determinate progress indicator or the progress part of a slider, that represents progress within valueRange.

Modifier to add semantics signifying progress of the Stepper/Slider.

Clears the semantics of all the descendant nodes and sets new semantics.

Add semantics key/value pairs to the layout node, for use in testing, accessibility, etc.

Clips bounds of scrollable container on main axis while leaving space for background effects (like shadows) on cross axis.

Modify element to make it participate in the nested scrolling hierarchy.

Renders overscroll from the provided overscrollEffect.

Adding this modifier to the modifier parameter of a component will allow it to intercept RotaryScrollEvents if it (or one of its children) is focused.

Adding this modifier to the modifier parameter of a component will allow it to intercept RotaryScrollEvents if it (or one of its children) is focused.

A modifier which connects rotary events with scrollable containers such as Column, LazyList and others.

Scroll an item vertically in/out of view based on scroll state provided by a scrolling list.

Scroll an item vertically in/out of view based on a ScrollState.

Scroll an item vertically in/out of view based on a LazyListState.

Scroll an item vertically in/out of view based on a ScalingLazyListState.

This function is deprecated. This overload is provided for backwards compatibility with Compose for Wear OS 1.1.A newer overload is available which uses ScalingLazyListState from wear.compose.foundation.lazy package

Modify element to allow to scroll horizontally when width of the content is bigger than max constraints allow.

Modify element to allow to scroll horizontally when width of the content is bigger than max constraints allow.

Modify element to allow to scroll vertically when height of the content is bigger than max constraints allow.

Modify element to allow to scroll vertically when height of the content is bigger than max constraints allow.

Configure touch scrolling and flinging for the UI element in both XY orientations.

Configure a component to act as a scrollable area.

Configure a component to act as a scrollable area.

Configure touch scrolling and flinging for the UI element in a single Orientation.

Configure touch scrolling and flinging for the UI element in a single Orientation.

Controls the soft keyboard as a nested scrolling on Android R and later.

This modifier animates its own size when its child modifier (or the child composable if it is already at the tail of the chain) changes size.

This modifier animates its own size when its child modifier (or the child composable if it is already at the tail of the chain) changes size.

Attempts to size the content to match a specified aspect ratio by trying to match one of the incoming constraints in the following order: Constraints.maxWidth, Constraints.maxHeight, Constraints.minWidth, Constraints.minHeight if matchHeightConstraintsFirst is false (which is the default), or Constraints.maxHeight, Constraints.maxWidth, Constraints.minHeight, Constraints.minWidth if matchHeightConstraintsFirst is true.

Reserves at least 48.dp in size to disambiguate touch interactions if the element would measure smaller.

Reserves at least 48.dp in size to disambiguate touch interactions if the element would measure smaller.

Reserves at least 48.dp in size to disambiguate touch interactions if the element would measure smaller.

Declare the preferred height of the content to be the same as the min or max intrinsic height of the content.

Declare the height of the content to be exactly the same as the min or max intrinsic height of the content.

Declare the width of the content to be exactly the same as the min or max intrinsic width of the content.

Declare the preferred width of the content to be the same as the min or max intrinsic width of the content.

Invoked with the size of the modified Compose UI element when the element is first measured or when the size of the element changes.

Constrain the size of the wrapped layout only when it would be otherwise unconstrained: the minWidth and minHeight constraints are only applied when the incoming corresponding constraint is 0.

Have the content fill (possibly only partially) the Constraints.maxHeight of the incoming measurement constraints, by setting the minimum height and the maximum height to be equal to the maximum height multiplied by fraction.

Have the content fill (possibly only partially) the Constraints.maxWidth and Constraints.maxHeight of the incoming measurement constraints, by setting the minimum width and the maximum width to be equal to the maximum width multiplied by fraction, as well as the minimum height and the maximum height to be equal to the maximum height multiplied by fraction.

Have the content fill (possibly only partially) the Constraints.maxWidth of the incoming measurement constraints, by setting the minimum width and the maximum width to be equal to the maximum width multiplied by fraction.

Declare the preferred height of the content to be exactly heightdp.

Constrain the height of the content to be between mindp and maxdp as permitted by the incoming measurement Constraints.

Declare the height of the content to be exactly heightdp.

Constrain the height of the content to be between mindp and maxdp.

Declare the size of the content to be exactly sizedp width and height.

Declare the size of the content to be exactly size.

Declare the size of the content to be exactly widthdp and heightdp.

Constrain the width of the content to be between minWidthdp and maxWidthdp, and the height of the content to be between minHeightdp and maxHeightdp.

Declare the width of the content to be exactly widthdp.

Constrain the width of the content to be between mindp and maxdp.

Declare the preferred size of the content to be exactly sizedp square.

Declare the preferred size of the content to be exactly size.

Declare the preferred size of the content to be exactly widthdp by heightdp.

Constrain the width of the content to be between minWidthdp and maxWidthdp and the height of the content to be between minHeightdp and maxHeightdp as permitted by the incoming measurement Constraints.

Declare the preferred width of the content to be exactly widthdp.

Constrain the width of the content to be between mindp and maxdp as permitted by the incoming measurement Constraints.

Allow the content to measure at its desired height without regard for the incoming measurement minimum height constraint, and, if unbounded is true, also without regard for the incoming measurement maximum height constraint.

Allow the content to measure at its desired size without regard for the incoming measurement minimum width or minimum height constraints, and, if unbounded is true, also without regard for the incoming maximum constraints.

Allow the content to measure at its desired width without regard for the incoming measurement minimum width constraint, and, if unbounded is true, also without regard for the incoming measurement maximum width constraint.

Modifier to set both the size and recommended touch target for IconButton and TextButton.

Convenience modifier to calculate transformed height using TransformationSpec.

Sets the height to that of insets at the bottom of the screen.

Sets the width to that of insets at the end of the screen, using either left or right, depending on the LayoutDirection.

Sets the width to that of insets at the start of the screen, using either left or right, depending on the LayoutDirection.

Sets the height to that of insets at the top of the screen.

Attempts to size the original content rectangle to be inscribed into a destination by applying a specified ContentScale type.

skipToLookaheadSize enables a layout to measure its child with the lookahead constraints, therefore laying out the child as if the transition has finished.

Size the element's width proportional to its weight relative to other weighted sibling elements in the Row.

Size the element's height proportional to its weight relative to other weighted sibling elements in the Column.

Have the item fill (possibly only partially) the max height of the tallest item in the row it was placed in, within the FlowRow.

Have the item fill (possibly only partially) the max width of the widest item in the column it was placed in, within the FlowColumn.

Size the element to match the size of the Box after all other content elements have been measured.

Have the content fill the Constraints.maxHeight of the incoming measurement constraints by setting the minimum height to be equal to the maximum height multiplied by fraction.

Have the content fill the Constraints.maxWidth and Constraints.maxHeight of the parent measurement constraints by setting the minimum width to be equal to the maximum width multiplied by fraction and the minimum height to be equal to the maximum height multiplied by fraction.

Have the content fill the Constraints.maxWidth of the parent measurement constraints by setting the minimum width to be equal to the maximum width multiplied by fraction.

Modifier which should be applied to an ExposedDropdownMenu placed inside the scope.

Modifier which should be applied to a menu placed inside the ExposedDropdownMenuBoxScope.

This modifier specifies the preferred height for a pane in Dps, and the pane scaffold implementation will try its best to respect this height when the associated pane is rendered as a reflowed or a levitated pane.

This modifier specifies the preferred height for a pane as a proportion of the overall scaffold height.

This modifier specifies the preferred width for a pane as a proportion of the overall scaffold width.

This modifier specifies the preferred width for a pane in Dps, and the pane scaffold implementation will try its best to respect this width when the associated pane is rendered as a fixed pane, i.e., a pane that are not stretching to fill the remaining spaces.

Applies a tag to allow modified element to be found in tests.

Sets the degrees the view is rotated around the center of the composable.

Scale the contents of both the horizontal and vertical axis uniformly by the same scale factor.

Scale the contents of the composable by the following scale factors along the horizontal and vertical axis respectively.

Enable transformation gestures of the modified UI element.

Enable transformation gestures of the modified UI element.

This function is deprecated. Replaced by overload with a callback for obtain a transfer data,start detection is performed by Compose itself

Set autofill hint with contentType.

Applies an animated marquee effect to the modified content if it's too wide to fit in the available space.

Handles swipe to dismiss from the edge of the viewport.

Draw content blurred with the specified radii.

Draw content blurred with the specified radii.

Modifier that can be used to send bringIntoView requests.

This function is deprecated. Use BringIntoViewModifierNode instead

Declare a just-in-time composition of a Modifier that will be composed for each element it modifies.

Declare a just-in-time composition of a Modifier that will be composed for each element it modifies.

Declare a just-in-time composition of a Modifier that will be composed for each element it modifies.

Declare a just-in-time composition of a Modifier that will be composed for each element it modifies.

Declare a just-in-time composition of a Modifier that will be composed for each element it modifies.

Provides contentColor for text and iconography to consume.

Renders shadows for the provided depth.

A Modifier that allows an element it is applied to be treated like a source for drag and drop operations.

This function is deprecated. Replaced by overload with a callback for obtain a transfer data,start detection is performed by Compose itself

A Modifier that allows an element it is applied to be treated like a source for drag and drop operations.

A modifier that allows for receiving from a drag and drop gesture.

This function is deprecated. Use systemGestureExclusion

This function is deprecated. Use systemGestureExclusion

Set a requested frame rate on Composable

Set a requested frame rate on Composable

Configures an element to act as a handwriting detector which detects stylus handwriting and delegates handling of the recognised text to another element.

Configures an element to act as a stylus handwriting handler which can handle text input from a handwriting session which was triggered by stylus handwriting on a handwriting detector.

Configure component to be hoverable via pointer enter/exit events.

This function is deprecated. This API will create more invalidations of your modifier than necessary, so it's use is discouraged.

A modifier that keeps the device screen on as long as it is part of the composition on supported platforms.

This will map the RelativeLayoutBounds of the modifier into the provided LayoutBoundsHolder.

Creates an approach layout intended to help gradually approach the destination layout calculated in the lookahead pass.

Shows a Magnifier widget that shows an enlarged version of the content at sourceCenter relative to the current layout node.

A Modifier that can be used to consume ModifierLocals that were provided by other modifiers to the left of this modifier, or above this modifier in the layout tree.

A Modifier that can be used to provide ModifierLocals that can be read by other modifiers to the right of this modifier, or modifiers that are children of the layout node that this modifier is attached to.

Registers a callback to monitor when the node is first inside of the viewport of the window or not.

Invokes callback with the position of this layout node relative to the coordinate system of the root of the composition, as well as in screen coordinates and window coordinates.

Invoke onPlaced after the parent LayoutModifier and parent layout has been placed and before child LayoutModifier is placed.

Registers a callback to monitor whether or not the node is inside of the viewport of the window or not.

Draws a placeholder shape over the top of a composable and animates a wipe off effect to remove the placeholder.

Modifier.placeholder draws a skeleton shape over a component, for situations when no provisional content (such as cached data) is available.

Modifier to draw a placeholder shimmer over a component.

Modifier.placeholderShimmer draws a periodic shimmer over content, indicating to the user that contents are loading or potentially out of date.

Modifier that lets a developer define a pointer icon to display when a stylus is hovered over the element.

Calls watcher with each MotionEvent that the layout area or any child pointerInput receives.

Mark the layout rectangle as preferring to stay clear of floating windows.

Mark a rectangle within the local layout coordinates preferring to stay clear of floating windows.

A modifier for translating the position and scaling the size of a pull-to-refresh indicator based on the given PullRefreshState.

A nested scroll modifier that provides scroll events to state.

A nested scroll modifier that provides onPull and onRelease callbacks to aid building custom pull refresh components.

A Modifier that adds nested scroll to a container to support a pull-to-refresh gesture.

Configures the current node and any children nodes as a Content Receiver.

Fits the contents within rulers.

If one of the Rulers in rulers has a value within the bounds of the Layout, this sizes the content to that Ruler and the edge.

This modifier hints that the composable renders sensitive content (i.e. username, password, credit card etc) on the screen, and the content should be protected during screen share in supported environments.

Adding this modifier to the modifier parameter of a component will allow it to intercept hardware key events before they are sent to the software keyboard.

Adding this modifier to the modifier parameter of a component will allow it to intercept hardware key events before they are sent to the software keyboard.

A surface is a fundamental building block in Glimmer.

A surface is a fundamental building block in Glimmer.

This function is deprecated. SwipeToDismiss has been migrated to androidx.wear.compose.foundation.

Excludes the layout rectangle from the system gesture.

Excludes a rectangle within the local layout coordinates from the system gesture.

Adds a builder to be run when the text context menu is shown within this hierarchy.

Adds a filter to be run when the text context menu is shown within this hierarchy.

Consume insets that haven't been consumed yet by other insets Modifiers similar to windowInsetsPadding without adding any padding.

Consume paddingValues as insets as if the padding was added irrespective of insets.

Calls block with the WindowInsets that have been consumed, either by consumeWindowInsets or one of the padding Modifiers, such as imePadding.

This recalculates the WindowInsets based on the size and position.

Renders the content in the SharedTransitionScope's overlay, where shared content (i.e. shared elements and shared bounds) is rendered by default.

sharedBounds is a modifier that tags a layout with a SharedContentState.key, such that entering and exiting shared bounds of the same key share the animated and continuously changing bounds during the layout change.

sharedElement is a modifier that tags a layout with a SharedContentState.key, such that entering and exiting shared elements of the same key share the animated and continuously changing bounds during the layout change.

sharedElementWithCallerManagedVisibility is a modifier that tags a layout with a SharedContentState.key, such that entering and exiting shared elements of the same key share the animated and continuously changing bounds during the layout change.

A modifier that anchors a layout at the target position obtained from the lookahead pass during shared element transitions.

This function is deprecated. Use overload that takes ExposedDropdownMenuAnchorType and enabled parameters

Modifier which should be applied to an element inside the ExposedDropdownMenuBoxScope, typically a text field or an icon within the text field.

This modifier specifies the associated pane's margins according to the provided RectRulers as insets.

This modifier specifies the associated pane's margins according to specified fixed margins and the provided RectRulers as insets, if any.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-09 UTC.

---

## Visibility tracking in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/layouts/visibility-modifiers

**Contents:**
- Visibility tracking in Compose Stay organized with collections Save and categorize content based on your preferences.
- Track visibility changes with onVisibilityChanged
  - Set a time limit on a composable before triggering visibility callback
  - Set a minimum visible fraction

Tracking when a UI element is visible on-screen is helpful for a variety of use cases, such as logging analytics, managing UI state, and optimizing resources by automatically playing or pausing video content. Compose offers several modifiers for tracking UI element visibility such as:

You can use these APIs with any composable as part of the modifier chain.

Understanding when an item is visible or partially visible to a user can help you track analytics (for example, viewer count), optimize performance (fetching or prefetching data from the network only when the item is visible), or even trigger events (playing or pausing videos).

To be notified when an item's visibility changes, use the onVisibilityChanged modifier, as shown in the following example:

Text( text = "Some text", modifier = Modifier .onVisibilityChanged { visible -> if (visible) { // Do something if visible } else { // Do something if not visible } } .padding(vertical = 8.dp) )VisibilitySnippets.kt

The onVisibilityChanged modifier provides a boolean value that reflects the current visibility state of the composable. Additionally, it offers parameters such as minFraction and minDurationMs, which give you finer control over when the visibility callback needs to be triggered.

Like every other modifier, sequencing matters with the onVisibilityChanged modifier. The preceding example shows a composable function that renders text with padding. To make sure that the modifier affects the entire composable along with the padding, add the onVisibilityChanged modifier before the padding modifier.

In some situations, you might want to trigger an action only after an item has been visible to the user for a certain amount of time. For example, you can autoplay a video if it's been visible to the user for some time.

To trigger an action after an item is visible for a defined period, use the minDurationMs parameter in the onVisibilityChanged modifier. This parameter specifies the minimum amount of time a composable needs to be continuously visible for the callback to be triggered. If the composable stops being visible before the duration is met, the timer is reset. The default value is 0 milliseconds.

The following snippet changes the background to purple after the composable has been visible to the user for 3 seconds:

var background by remember { mutableStateOf(PalePink) } Card( modifier = modifier // ... .onVisibilityChanged(minDurationMs = 3000) { if (it) { background = MutedPlum } } ) { Box( modifier = Modifier // ... .background(background), contentAlignment = Alignment.Center, ) { // ... } }VisibilitySnippets.kt

Setting a minimum visible fraction for the composable's visibility callback is useful when working with scrollable content (for example, LazyColumn) to optimize data fetching for items that exceed the screen size.

In such cases, use the minFractionVisible parameter in the onVisibilityChanged modifier to define the fraction that needs to be on screen for the composable to be marked as visible. It supports float values ranging from 0.0f to 1.0f, and is set as 1.0f by default. 1.0f means the composable needs to be completely visible on screen for the callback to be triggered.

LazyColumn( modifier = modifier.fillMaxSize() ) { item { Box( modifier = Modifier // ... // Here the visible callback gets triggered when 20% of the composable is visible .onVisibilityChanged( minFractionVisible = 0.2f, ) { visible -> if (visible) { // Call specific logic here // viewModel.fetchDataFromNetwork() } } .padding(vertical = 16.dp) ) { Text( text = "Sample Text", modifier = Modifier.padding(horizontal = 16.dp) ) } } }VisibilitySnippets.kt

The example used earlier preloads the Androidify Bots from the network before the composable is completely visible. In Figure 2, the third bot doesn't load, as the composable isn't completely visible. In Figure 3, minFractionVisible is set, and the third bot loads before it is completely visible on screen.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Jetpack Compose phases Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/phases

**Contents:**
- Jetpack Compose phases Stay organized with collections Save and categorize content based on your preferences.
- The three phases of a frame
  - Understand the phases
    - Composition
    - Layout
    - Drawing
- State reads
- Phased state reads
  - Phase 1: Composition
  - Phase 2: Layout

Like most other UI toolkits, Compose renders a frame through several distinct phases. For example, the Android View system has three main phases: measure, layout, and drawing. Compose is very similar but has an important additional phase called composition at the start.

The Compose documentation describes composition in Thinking in Compose and State and Jetpack Compose.

Compose has three main phases:

The order of these phases is generally the same, allowing data to flow in one direction from composition to layout to drawing to produce a frame (also known as unidirectional data flow). BoxWithConstraints, LazyColumn, and LazyRow are notable exceptions, where the composition of its children depends on the parent's layout phase.

Conceptually, each of these phases happens for every frame; however to optimize performance, Compose avoids repeating work that would compute the same results from the same inputs in all of these phases. Compose skips running a composable function if it can reuse a former result, and Compose UI doesn't re-layout or re-draw the entire tree if it doesn't have to. Compose performs only the minimum amount of work required to update the UI. This optimization is possible because Compose tracks state reads within the different phases.

This section describes how the three Compose phases are executed for composables in greater detail.

In the composition phase, the Compose runtime executes composable functions and outputs a tree structure that represents your UI. This UI tree consists of layout nodes that contain all the information needed for the next phases, as shown in the following video:

Figure 2. The tree representing your UI that is created in the composition phase.

A subsection of the code and UI tree looks like the following:

In these examples, each composable function in the code maps to a single layout node in the UI tree. In more complex examples, composables can contain logic and control flow, and produce a different tree given different states.

In the layout phase, Compose uses the UI tree produced in the composition phase as input. The collection of layout nodes contain all the information needed to decide on each node's size and location in 2D space.

Figure 4. The measurement and placement of each layout node in the UI tree during the layout phase.

During the layout phase, the tree is traversed using the following three step algorithm:

At the end of this phase, each layout node has:

Recall the UI tree from the previous section:

For this tree, the algorithm works as follows:

Note that each node was visited only once. The Compose runtime requires only one pass through the UI tree to measure and place all the nodes, which improves performance. When the number of nodes in the tree increases, the time spent traversing it increases in a linear fashion. In contrast, if each node was visited multiple times, the traversal time increases exponentially.

In the drawing phase, the tree is traversed again from top to bottom, and each node draws itself on the screen in turn.

Figure 5. The drawing phase draws the pixels on the screen.

Using the previous example, the tree content is drawn in the following way:

Figure 6. A UI tree and its drawn representation.

When you read the value of a snapshot state during one of the phases listed previously, Compose automatically tracks what it was doing when it read the value. This tracking allows Compose to re-execute the reader when the state's value changes, and is the basis of state observability in Compose.

You commonly create state using mutableStateOf() and then access it through one of two ways: by directly accessing the value property, or alternatively by using a Kotlin property delegate. You can read more about them in State in composables. For the purposes of this guide, a "state read" refers to either of those equivalent access methods.

// State read without property delegate. val paddingState: MutableState<Dp> = remember { mutableStateOf(8.dp) } Text( text = "Hello", modifier = Modifier.padding(paddingState.value) )PhasesSnippets.kt

// State read with property delegate. var padding: Dp by remember { mutableStateOf(8.dp) } Text( text = "Hello", modifier = Modifier.padding(padding) )PhasesSnippets.kt

Under the hood of the property delegate, "getter" and "setter" functions are used to access and update the State's value. These getter and setter functions are only invoked when you reference the property as a value, and not when it is created, which is why the two ways described previously are equivalent.

Each block of code that can be re-executed when a read state changes is a restart scope. Compose keeps track of state value changes and restart scopes in different phases.

As mentioned previously, there are three main phases in Compose, and Compose tracks what state is read within each of them. This allows Compose to notify only the specific phases that need to perform work for each affected element of your UI.

The following sections describe each phase and describe what happens when a state value is read within it.

State reads within a @Composable function or lambda block affect composition and potentially the subsequent phases. When the state's value changes, the recomposer schedules reruns of all the composable functions which read that state's value. Note that the runtime may decide to skip some or all of the composable functions if the inputs haven't changed. See Skipping if the inputs haven't changed for more information.

Depending on the result of composition, Compose UI runs the layout and drawing phases. It might skip these phases if the content remains the same and the size and the layout won't change.

var padding by remember { mutableStateOf(8.dp) } Text( text = "Hello", // The `padding` state is read in the composition phase // when the modifier is constructed. // Changes in `padding` will invoke recomposition. modifier = Modifier.padding(padding) )PhasesSnippets.kt

The layout phase consists of two steps: measurement and placement. The measurement step runs the measure lambda passed to the Layout composable, the MeasureScope.measure method of the LayoutModifier interface, among others. The placement step runs the placement block of the layout function, the lambda block of Modifier.offset { … }, and similar functions.

State reads during each of these steps affect the layout and potentially the drawing phase. When the state's value changes, Compose UI schedules the layout phase. It also runs the drawing phase if size or position has changed.

var offsetX by remember { mutableStateOf(8.dp) } Text( text = "Hello", modifier = Modifier.offset { // The `offsetX` state is read in the placement step // of the layout phase when the offset is calculated. // Changes in `offsetX` restart the layout. IntOffset(offsetX.roundToPx(), 0) } )PhasesSnippets.kt

State reads during drawing code affect the drawing phase. Common examples include Canvas(), Modifier.drawBehind, and Modifier.drawWithContent. When the state's value changes, Compose UI runs only the draw phase.

var color by remember { mutableStateOf(Color.Red) } Canvas(modifier = modifier) { // The `color` state is read in the drawing phase // when the canvas is rendered. // Changes in `color` restart the drawing. drawRect(color) }PhasesSnippets.kt

Because Compose performs localized state read tracking, you can minimize the amount of work performed by reading each state in an appropriate phase.

Consider the following example. This example has an Image() which uses the offset modifier to offset its final layout position, resulting in a parallax effect as the user scrolls.

Box { val listState = rememberLazyListState() Image( // ... // Non-optimal implementation! Modifier.offset( with(LocalDensity.current) { // State read of firstVisibleItemScrollOffset in composition (listState.firstVisibleItemScrollOffset / 2).toDp() } ) ) LazyColumn(state = listState) { // ... } }PhasesSnippets.kt

This code works, but results in suboptimal performance. As written, the code reads the value of the firstVisibleItemScrollOffset state and passes it to the Modifier.offset(offset: Dp) function. As the user scrolls, the firstVisibleItemScrollOffset's value will change. As you've learned, Compose tracks any state reads so that it can restart (re-invoke) the reading code, which in this example is the content of the Box.

This is an example of reading a state within the composition phase. This is not necessarily a bad thing, and in fact is the basis of recomposition, allowing data changes to emit new UI.

Key point: This example is suboptimal because every scroll event results in the entire composable content being reevaluated, measured, laid out, and finally drawn. You trigger the Compose phase on every scroll even though the content shown hasn't changed, only its position. You can optimize the state read to only re-trigger the layout phase.

There is another version of the offset modifier available: Modifier.offset(offset: Density.() -> IntOffset).

This version takes a lambda parameter, where the resulting offset is returned by the lambda block. Update the code to use it:

Box { val listState = rememberLazyListState() Image( // ... Modifier.offset { // State read of firstVisibleItemScrollOffset in Layout IntOffset(x = 0, y = listState.firstVisibleItemScrollOffset / 2) } ) LazyColumn(state = listState) { // ... } }PhasesSnippets.kt

So why is this more performant? The lambda block you provide to the modifier is invoked during the layout phase (specifically, during the layout phase's placement step), meaning that the firstVisibleItemScrollOffset state is no longer read during composition. Because Compose tracks when state is read, this change means that if the firstVisibleItemScrollOffset's value changes, Compose only has to restart the layout and drawing phases.

Of course, it is often absolutely necessary to read states in the composition phase. Even so, there are cases where you can minimize the number of recompositions by filtering state changes. For more information about this, see derivedStateOf: convert one or multiple state objects into another state.

This guide previously mentioned that the phases of Compose are always invoked in the same order, and that there is no way to go backwards while in the same frame. However, that doesn't prohibit apps getting into composition loops across different frames. Consider this example:

Box { var imageHeightPx by remember { mutableIntStateOf(0) } Image( painter = painterResource(R.drawable.rectangle), contentDescription = "I'm above the text", modifier = Modifier .fillMaxWidth() .onSizeChanged { size -> // Don't do this imageHeightPx = size.height } ) Text( text = "I'm below the image", modifier = Modifier.padding( top = with(LocalDensity.current) { imageHeightPx.toDp() } ) ) }PhasesSnippets.kt

This example implements a vertical column, with the image at the top, and then the text below it. It uses Modifier.onSizeChanged() to get the resolved size of the image, and then uses Modifier.padding() on the text to shift it down. The unnatural conversion from Px back to Dp already indicates that the code has some issue.

The issue with this example is that the code doesn't arrive at the "final" layout within a single frame. The code relies on multiple frames happening, which performs unnecessary work, and results in UI jumping around on screen for the user.

During the composition phase of the first frame, imageHeightPx is initially 0. Consequently, the code provides the text with Modifier.padding(top = 0). The subsequent layout phase invokes the onSizeChanged modifier's callback, which updates imageHeightPx to the image's actual height. Compose then schedules a recomposition for the next frame. However, during the current drawing phase, the text renders with a padding of 0, as the updated imageHeightPx value is not yet reflected.

Compose initiates the second frame, triggered by the change in imageHeightPx's value. In this frame's composition phase, the state is read within the Box content block. The text is now provided with padding that accurately matches the image's height. During the layout phase, imageHeightPx is set again; however, no further recomposition is scheduled because the value remains consistent.

This example may seem contrived, but be careful of this general pattern:

The fix for the preceding sample is to use the proper layout primitives. The preceding example can be implemented with a Column(), but you may have a more complex example which requires something custom, which will require writing a custom layout. See the Custom layouts guide for more inform

The general principle here is to have a single source of truth for multiple UI elements that should be measured and placed with regards to one another. Using a proper layout primitive or creating a custom layout means that the minimal shared parent serves as the source of truth that can coordinate the relation between multiple elements. Introducing a dynamic state breaks this principle.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Compose modifiers Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/modifiers

**Contents:**
- Compose modifiers Stay organized with collections Save and categorize content based on your preferences.
- Order of modifiers matters
- Built-in modifiers
  - padding and size
  - Offset
- Scope safety in Compose
  - matchParentSize in Box
  - weight in Row and Column
- Extracting and reusing modifiers
  - Best practices for reusing modifiers

Modifiers allow you to decorate or augment a composable. Modifiers let you do these sorts of things:

Modifiers are standard Kotlin objects. Create a modifier by calling one of the Modifier class functions:

@Composable private fun Greeting(name: String) { Column(modifier = Modifier.padding(24.dp)) { Text(text = "Hello,") Text(text = name) } }ModifierSnippets.kt

You can chain these functions together to compose them:

@Composable private fun Greeting(name: String) { Column( modifier = Modifier .padding(24.dp) .fillMaxWidth() ) { Text(text = "Hello,") Text(text = name) } }ModifierSnippets.kt

In the code above, notice different modifier functions used together.

It's a best practice to have all of your composables accept a modifier parameter, and pass that modifier to its first child that emits UI. Doing so makes your code more reusable and makes its behavior more predictable and intuitive. For more information, see the Compose API guidelines, Elements accept and respect a Modifier parameter.

The order of modifier functions is significant. Since each function makes changes to the Modifierreturned by the previous function, the sequence affects the final result. Let's see an example of this:

@Composable fun ArtistCard(/*...*/) { val padding = 16.dp Column( Modifier .clickable(onClick = onClick) .padding(padding) .fillMaxWidth() ) { // rest of the implementation } }ModifierSnippets.kt

In the code above the whole area is clickable, including the surrounding padding, because the padding modifier has been applied after the clickable modifier. If the modifiers order is reversed, the space added by padding does not react to user input:

@Composable fun ArtistCard(/*...*/) { val padding = 16.dp Column( Modifier .padding(padding) .clickable(onClick = onClick) .fillMaxWidth() ) { // rest of the implementation } }ModifierSnippets.kt

Jetpack Compose provides a list of built-in modifiers to help you decorate or augment a composable. Here are some common modifiers you'll use to adjust your layouts.

By default, layouts provided in Compose wrap their children. However, you can set a size by using the size modifier:

@Composable fun ArtistCard(/*...*/) { Row( modifier = Modifier.size(width = 400.dp, height = 100.dp) ) { Image(/*...*/) Column { /*...*/ } } }ModifierSnippets.kt

Note that the size you specified might not be respected if it does not satisfy the constraints coming from the layout's parent. If you require the composable size to be fixed regardless of the incoming constraints, use the requiredSize modifier:

@Composable fun ArtistCard(/*...*/) { Row( modifier = Modifier.size(width = 400.dp, height = 100.dp) ) { Image( /*...*/ modifier = Modifier.requiredSize(150.dp) ) Column { /*...*/ } } }ModifierSnippets.kt

In this example, even with the parent height set to 100.dp, the height of the Image will be 150.dp, as the requiredSize modifier takes precedence.

If you want a child layout to fill all the available height allowed by the parent, add the fillMaxHeight modifier (Compose also provides fillMaxSize and fillMaxWidth):

@Composable fun ArtistCard(/*...*/) { Row( modifier = Modifier.size(width = 400.dp, height = 100.dp) ) { Image( /*...*/ modifier = Modifier.fillMaxHeight() ) Column { /*...*/ } } }ModifierSnippets.kt

To add padding all around an element, set a padding modifier.

If you want to add padding above a text baseline such that you achieve a specific distance from the top of the layout to the baseline, use the paddingFromBaseline modifier:

@Composable fun ArtistCard(artist: Artist) { Row(/*...*/) { Column { Text( text = artist.name, modifier = Modifier.paddingFromBaseline(top = 50.dp) ) Text(artist.lastSeenOnline) } } }ModifierSnippets.kt

To position a layout relative to its original position, add the offset modifier and set the offset in the x and y axis. Offsets can be positive as well as non-positive. The difference between padding and offset is that adding an offset to a composable does not change its measurements:

@Composable fun ArtistCard(artist: Artist) { Row(/*...*/) { Column { Text(artist.name) Text( text = artist.lastSeenOnline, modifier = Modifier.offset(x = 4.dp) ) } } }ModifierSnippets.kt

The offset modifier is applied horizontally according to the layout direction. In a left-to-right context, a positive offset shifts the element to the right, while in a right-to-left context, it shifts the element to the left. If you need to set an offset without considering layout direction, see the absoluteOffset modifier, in which a positive offset value always shifts the element to the right.

The offset modifier provides two overloads - offset that takes the offsets as parameters and offset that takes in a lambda. For more in depth information on when to use each of these and how to optimize for performance, read through the Compose performance - Defer reads as long as possible section.

In Compose, there are modifiers that can only be used when applied to children of certain composables. Compose enforces this by means of custom scopes.

For example, if you want to make a child as big as the parent Box without affecting the Box size, use the matchParentSize modifier. matchParentSize is only available in BoxScope. Therefore, it can only be used on a child within a Box parent.

Scope safety prevents you from adding modifiers that wouldn't work in other composables and scopes and saves time from trial and error.

Scoped modifiers notify the parent about some information the parent should know about the child. These are also commonly referred to as parent data modifiers. Their internals are different from the general purpose modifiers, but from a usage perspective, these differences don't matter.

As mentioned above, if you want a child layout to be the same size as a parent Box without affecting the Box size, use the matchParentSize modifier.

Note that matchParentSize is only available within a Box scope, meaning that it only applies to direct children of Box composables.

In the example below, the child Spacer takes its size from its parent Box, which in turn takes its size from the biggest children, ArtistCard in this case.

@Composable fun MatchParentSizeComposable() { Box { Spacer( Modifier .matchParentSize() .background(Color.LightGray) ) ArtistCard() } }ModifierSnippets.kt

If fillMaxSize were used instead of matchParentSize, the Spacer would take all the available space allowed to the parent, in turn causing the parent to expand and fill all the available space.

As you have seen in the previous section on Padding and size, by default, a composable size is defined by the content it is wrapping. You can set a composable size to be flexible within its parent using the weight Modifier that is only available in RowScope, and ColumnScope.

Let’s take a Row that contains two Box composables. The first box is given twice the weight of the second, so it's given twice the width. Since the Row is 210.dp wide, the first Box is 140.dp wide, and the second is 70.dp:

@Composable fun ArtistCard(/*...*/) { Row( modifier = Modifier.fillMaxWidth() ) { Image( /*...*/ modifier = Modifier.weight(2f) ) Column( modifier = Modifier.weight(1f) ) { /*...*/ } } }ModifierSnippets.kt

Multiple modifiers can be chained together to decorate or augment a composable. This chain is created via the Modifier interface which represents an ordered, immutable list of single Modifier.Elements.

Each Modifier.Element represents an individual behavior, like layout, drawing and graphics behaviors, all gesture-related, focus and semantics behaviors, as well as device input events. Their ordering matters: modifier elements that are added first will be applied first.

Sometimes it can be beneficial to reuse the same modifier chain instances in multiple composables, by extracting them into variables and hoisting them into higher scopes. It can improve code readability or help improve your app's performance for a few reasons:

Create your own Modifier chains and extract them to reuse them on multiple composable components. It is completely fine to just save a modifier, as they are data-like objects:

val reusableModifier = Modifier .fillMaxWidth() .background(Color.Red) .padding(12.dp)ModifierSnippets.kt

When observing frequently changing states inside composables, like animation states or scrollState, there can be a significant amount of recompositions done. In this case, your modifiers will get allocated on every recomposition and potentially for every frame:

@Composable fun LoadingWheelAnimation() { val animatedState = animateFloatAsState(/*...*/) LoadingWheel( // Creation and allocation of this modifier will happen on every frame of the animation! modifier = Modifier .padding(12.dp) .background(Color.Gray), animatedState = animatedState ) }ModifierSnippets.kt

Instead, you can create, extract and reuse the same instance of the modifier and pass it to the composable like this:

// Now, the allocation of the modifier happens here: val reusableModifier = Modifier .padding(12.dp) .background(Color.Gray) @Composable fun LoadingWheelAnimation() { val animatedState = animateFloatAsState(/*...*/) LoadingWheel( // No allocation, as we're just reusing the same instance modifier = reusableModifier, animatedState = animatedState ) }ModifierSnippets.kt

Modifiers can be unscoped or scoped to a specific composable. In the case of unscoped modifiers, you can easily extract them outside of any composables as simple variables:

val reusableModifier = Modifier .fillMaxWidth() .background(Color.Red) .padding(12.dp) @Composable fun AuthorField() { HeaderText( // ... modifier = reusableModifier ) SubtitleText( // ... modifier = reusableModifier ) }ModifierSnippets.kt

This can be especially beneficial when combined with Lazy layouts. In most cases, you’d want all of your, potentially significant, amount of items to have the exact same modifiers:

val reusableItemModifier = Modifier .padding(bottom = 12.dp) .size(216.dp) .clip(CircleShape) @Composable private fun AuthorList(authors: List<Author>) { LazyColumn { items(authors) { AsyncImage( // ... modifier = reusableItemModifier, ) } } }ModifierSnippets.kt

When dealing with modifiers that are scoped to certain composables, you can extract them to the highest possible level and reuse where appropriate:

Column(/*...*/) { val reusableItemModifier = Modifier .padding(bottom = 12.dp) // Align Modifier.Element requires a ColumnScope .align(Alignment.CenterHorizontally) .weight(1f) Text1( modifier = reusableItemModifier, // ... ) Text2( modifier = reusableItemModifier // ... ) // ... }ModifierSnippets.kt

You should only be passing the extracted, scoped modifiers to the same-scoped, direct children. See the section Scope safety in Compose for more reference on why this matters:

Column(modifier = Modifier.fillMaxWidth()) { // Weight modifier is scoped to the Column composable val reusableItemModifier = Modifier.weight(1f) // Weight will be properly assigned here since this Text is a direct child of Column Text1( modifier = reusableItemModifier // ... ) Box { Text2( // Weight won't do anything here since the Text composable is not a direct child of Column modifier = reusableItemModifier // ... ) } }ModifierSnippets.kt

You can further chain or append your extracted modifier chains by calling the .then() function:

val reusableModifier = Modifier .fillMaxWidth() .background(Color.Red) .padding(12.dp) // Append to your reusableModifier reusableModifier.clickable { /*...*/ } // Append your reusableModifier otherModifier.then(reusableModifier)ModifierSnippets.kt

Just keep in mind that the order of modifiers matters!

We provide a full list of modifiers, with their parameters and scopes.

For more practice on how to use modifiers, you can also go through the Basic layouts in Compose codelab or refer to the Now in Android repository.

For more information on custom modifiers and how to create them, take a look at the documentation on Custom layouts - Using the layout modifier.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Thinking in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/mental-model

**Contents:**
- Thinking in Compose Stay organized with collections Save and categorize content based on your preferences.
- The declarative programming paradigm
- An example composable function
- The declarative paradigm shift
- Dynamic content
- Recomposition
  - Recomposition skips as much as possible
  - Recomposition is optimistic
  - Composable functions might run quite frequently
  - Composable functions could run in parallel

Jetpack Compose is a modern declarative UI Toolkit for Android. Compose simplifies writing and maintaining your app UI by providing a declarative API that lets you render your app UI without imperatively mutating frontend views. This terminology needs some explanation, but the implications are important for your app design.

Historically, an Android view hierarchy has been representable as a tree of UI widgets. As the state of the app changes because of things like user interactions, the UI hierarchy needs to be updated to display the current data. The most common way of updating the UI is to walk the tree using functions like findViewById(), and change nodes by calling methods like button.setText(String), container.addChild(View), or img.setImageBitmap(Bitmap). These methods change the internal state of the widget.

Manipulating views manually increases the likelihood of errors. If a piece of data is rendered in multiple places, you might forget to update one of the views that shows it. This can also lead to illegal states, when two updates conflict in an unexpected way. For example, an update might try to set a value of a node that was just removed from the UI. In general, the software maintenance complexity grows with the number of views that require updating.

Over the last several years, the entire industry has started shifting to a declarative UI model. This model simplifies the engineering associated with building and updating user interfaces. The technique works by conceptually regenerating the entire screen from scratch, then applying only the necessary changes. This approach avoids the complexity of manually updating a stateful view hierarchy. Compose is a declarative UI framework.

One challenge with regenerating the entire screen is that it is potentially expensive, in terms of time, computing power, and battery usage. To mitigate this cost, Compose intelligently chooses which parts of the UI need to be redrawn at any given time. This does have some implications for how you design your UI components, as discussed in Recomposition.

Using Compose, you can build your user interface by defining a set of composable functions that take in data and emit UI elements. An example is a Greeting widget, which takes in a String and emits a Text widget which displays a greeting message.

A few noteworthy things about this function:

Properties: This function is fast, idempotent, and free of side-effects.

In general, all composable functions must be written with these properties, for reasons discussed in Recomposition.

With many imperative object-oriented UI toolkits, you initialize the UI by instantiating a tree of widgets. You often do this by inflating an XML layout file. Each widget maintains its own internal state, and exposes getter and setter methods that let the app logic interact with the widget.

In Compose's declarative approach, widgets are relatively stateless and don't expose setter or getter functions. In fact, widgets are not exposed as objects. You update the UI by calling the same composable function with different arguments. This simplifies providing state to architectural patterns such as a ViewModel, as described in the Guide to app architecture. Then, your composables are responsible for transforming the current application state into a UI every time the observable data updates.

When the user interacts with the UI, the UI raises events such as onClick. Those events should notify the app logic, which can then change the app's state. When the state changes, the composable functions are called again with the new data. This causes the UI elements to be redrawn--this process is called recomposition.

Because composable functions are written in Kotlin instead of XML, they can be as dynamic as any other Kotlin code. For example, suppose you want to build a UI that greets a list of users:

@Composable fun Greeting(names: List<String>) { for (name in names) { Text("Hello $name") } }ThinkingInComposeSnippets.kt

This function takes in a list of names and generates a greeting for each user. Composable functions can be quite sophisticated. You can use if statements to decide if you want to show a particular UI element. You can use loops. You can call helper functions. You have the full flexibility of the underlying language. This power and flexibility is one of the key advantages of Jetpack Compose.

In an imperative UI model, to change a widget, you call a setter on the widget to change its internal state. In Compose, you call the composable function again with new data. Doing so causes the function to be recomposed--the widgets emitted by the function are redrawn, if necessary, with new data. The Compose framework can intelligently recompose only the components that changed.

For example, consider this composable function which displays a button:

@Composable fun ClickCounter(clicks: Int, onClick: () -> Unit) { Button(onClick = onClick) { Text("I've been clicked $clicks times") } }ThinkingInComposeSnippets.kt

Every time the button is clicked, the caller updates the value of clicks. Compose calls the lambda with the Text function again to show the new value; this process is called recomposition. Other functions that don't depend on the value are not recomposed.

As we discussed, recomposing the entire UI tree can be computationally expensive, which uses computing power and battery life. Compose solves this problem with this intelligent recomposition.

Recomposition is the process of calling your composable functions again when inputs change. When Compose recomposes based on new inputs, it calls only the functions or lambdas that might have changed, and skips the rest. By skipping functions or lambdas with unchanged parameters, Compose recomposes efficiently.

Never depend on side-effects from executing composable functions, since a function's recomposition may be skipped. If you do, users may experience strange and unpredictable behavior in your app. A side-effect is any change that is visible to the rest of your app. For example, these actions are all dangerous side-effects:

Composable functions might be re-executed as often as every frame, such as when an animation is being rendered. Composable functions should be fast to avoid jank during animations. If you need to do expensive operations, such as reading from shared preferences, do it in a background coroutine and pass the value result to the composable function as a parameter.

As an example, this code creates a composable to update a value in SharedPreferences. The composable shouldn't read or write from shared preferences itself. Instead, this code moves the read and write to a ViewModel in a background coroutine. The app logic passes the current value with a callback to trigger an update.

@Composable fun SharedPrefsToggle( text: String, value: Boolean, onValueChanged: (Boolean) -> Unit ) { Row { Text(text) Checkbox(checked = value, onCheckedChange = onValueChanged) } }ThinkingInComposeSnippets.kt

This document discusses a number of things to be aware of when you use Compose:

The following sections will cover how to build composable functions to support recomposition. In every case, the best practice is to keep your composable functions fast, idempotent, and side-effect free.

When portions of your UI are invalid, Compose does its best to recompose just the portions that need to be updated. This means it may skip to re-run a single Button's composable without executing any of the composables higher or lower in the UI tree.

Every composable function and lambda might recompose by itself. The following example demonstrates how recomposition can skip some elements when rendering a list:

/** * Display a list of names the user can click with a header */ @Composable fun NamePicker( header: String, names: List<String>, onNameClicked: (String) -> Unit ) { Column { // this will recompose when [header] changes, but not when [names] changes Text(header, style = MaterialTheme.typography.bodyLarge) HorizontalDivider() // LazyColumn is the Compose version of a RecyclerView. // The lambda passed to items() is similar to a RecyclerView.ViewHolder. LazyColumn { items(names) { name -> // When an item's [name] updates, the adapter for that item // will recompose. This will not recompose when [header] changes NamePickerItem(name, onNameClicked) } } } } /** * Display a single name the user can click. */ @Composable private fun NamePickerItem(name: String, onClicked: (String) -> Unit) { Text(name, Modifier.clickable(onClick = { onClicked(name) })) }ThinkingInComposeSnippets.kt

Each of these scopes might be the only thing to execute during a recomposition. Compose might skip to the Column lambda without executing any of its parents when the header changes. And when executing Column, Compose might choose to skip the LazyColumn's items if names didn't change.

Again, all composable functions or lambdas should be side-effect free. When you need to perform a side-effect, trigger it from a callback.

Recomposition starts whenever Compose thinks that the parameters of a composable might have changed. Recomposition is optimistic, which means Compose expects to finish recomposition before the parameters change again. If a parameter does change before recomposition finishes, Compose might cancel the recomposition and restart it with the new parameter.

When recomposition is canceled, Compose discards the UI tree from the recomposition. If you have any side-effects that depend on the UI being displayed, the side-effect will be applied even if composition is canceled. This can lead to inconsistent app state.

Verify that all composable functions and lambdas are idempotent and side-effect free to handle optimistic recomposition.

In some cases, a composable function might run for every frame of a UI animation. If the function performs expensive operations, like reading from device storage, the function can cause UI jank.

For example, if your widget tried to read device settings, it could potentially read those settings hundreds of times a second, with disastrous effects on your app's performance.

If a composable function needs data, define parameters for that data. You can then move expensive work to another thread, outside of composition, and pass the resulting value to the composable function as a parameter using mutableStateOf or LiveData.

Compose could optimize recomposition by running composable functions in parallel. This would let Compose take advantage of multiple cores, and run composable functions not on the screen at a lower priority.

This optimization would mean a composable function might execute within a pool of background threads. If a composable function calls a function on a ViewModel, Compose might call that function from several threads at the same time.

To verify your application behaves correctly, all composable functions should have no side-effects. Instead, trigger side-effects from callbacks such as onClick that always execute on the UI thread.

When a composable function is invoked, the invocation might occur on a different thread from the caller. That means code that modifies variables in a composable lambda should be avoided–both because such code is not thread-safe, and because it is an impermissible side-effect of the composable lambda.

Here's an example showing a composable that displays a list and its count:

@Composable fun ListComposable(myList: List<String>) { Row(horizontalArrangement = Arrangement.SpaceBetween) { Column { for (item in myList) { Text("Item: $item") } } Text("Count: ${myList.size}") } }ThinkingInComposeSnippets.kt

This code is side-effect free, and transforms the input list to UI. This is great code for displaying a small list. However, if the function writes to a local variable, this code won't be thread-safe or correct:

@Composable fun ListWithBug(myList: List<String>) { var items = 0 Row(horizontalArrangement = Arrangement.SpaceBetween) { Column { for (item in myList) { Card { Text("Item: $item") items++ // Avoid! Side-effect of the column recomposing. } } } Text("Count: $items") } }ThinkingInComposeSnippets.kt

In this example, items is modified with every recomposition. That could be every frame of an animation, or when the list updates. Either way, the UI will display the wrong count. Because of this, writes like this are not supported in Compose; by prohibiting those writes, we allow the framework to change threads to execute composable lambdas.

If you look at the code for a composable function, you might assume that the code is run in the order it appears. But this isn't guaranteed to be true. If a composable function contains calls to other composable functions, those functions might run in any order. Compose has the option of recognizing that some UI elements are higher priority than others, and drawing them first.

For example, suppose you have code like this to draw three screens in a tab layout:

@Composable fun ButtonRow() { MyFancyNavigation { StartScreen() MiddleScreen() EndScreen() } }ThinkingInComposeSnippets.kt

The calls to StartScreen, MiddleScreen, and EndScreen might happen in any order. This means you can't, for example, have StartScreen() set some global variable (a side-effect) and have MiddleScreen() take advantage of that change. Instead, each of those functions needs to be self-contained.

To learn more about how to think in Compose and composable functions, see the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## State preservation and persistent storage Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-state-preservation

**Contents:**
- State preservation and persistent storage Stay organized with collections Save and categorize content based on your preferences.
- State preservation
- Persistent Storage
    - Handle zoom, pan, and rotation
    - Export strokes
    - Data object and converter helpers
      - Data transfer objects
      - Converters

State preservation and persistent storage are non-trivial aspects of inking apps, especially in Compose. The core data objects, such as brush properties and the points that form a stroke, are complex and don't automatically persist. This requires a deliberate strategy for saving state during scenarios like configuration changes and permanently saving a user's drawings to a database.

In Jetpack Compose, UI state is typically managed using remember and rememberSaveable. While rememberSaveable offers automatic state preservation across configuration changes, its built-in capabilities are limited to primitive data types and objects that implement Parcelable or Serializable.

For custom objects that contain complex properties, such as Brush, you must define explicit serialization and deserialization mechanisms using a custom state saver. By defining a custom Saver for the Brush object, you can preserve the brush's essential attributes when configuration changes occur, as shown in the following brushStateSaver example.

You can then use the custom Saver to preserve the selected brush state:

To enable features such as document saving, loading, and potential real-time collaboration, store strokes and associated data in a serialized format. For the Ink API, manual serialization and deserialization are necessary.

To accurately restore a stroke, save its Brush and StrokeInputBatch.

The Storage module simplifies compactly serializing the most complex part: the StrokeInputBatch.

To load a stroke object:

Create the final stroke using the recreated brush and the deserialized StrokeInputBatch.

If your app supports zooming, panning, or rotation, you must provide the current transformation to InProgressStrokes. This helps newly drawn strokes match the position and scale of your existing strokes.

You do this by passing a Matrix to the pointerEventToWorldTransform parameter. The matrix should represent the inverse of the transformation you apply to your finished strokes canvas.

You might need to export your stroke scene as a static image file. This is useful for sharing the drawing with other applications, generating thumbnails, or saving a final, uneditable version of the content.

To export a scene, you can render your strokes to an offscreen bitmap instead of directly to the screen. Use Android's Picture API, which lets you record drawings on a canvas without needing a visible UI component.

The process involves creating a Picture instance, calling beginRecording() to get a Canvas, and then using your existing CanvasStrokeRenderer to draw each stroke onto that Canvas. After you record all the drawing commands, you can use the Picture to create a Bitmap, which you can then compress and save to a file.

Define a serialization object structure that mirrors needed Ink API objects.

Use the Ink API's storage module to encode and decode StrokeInputBatch.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

**Examples:**

Example 1 (typescript):
```typescript
fun brushStateSaver(converters: Converters): Saver<MutableState<Brush>, SerializedBrush> = Saver(
    save = { converters.serializeBrush(it.value) },
    restore = { mutableStateOf(converters.deserializeBrush(it)) },
)
```

Example 2 (unknown):
```unknown
val currentBrush = rememberSaveable(saver = brushStateSaver(Converters())) { mutableStateOf(defaultBrush) }
```

Example 3 (json):
```json
fun serializeStroke(stroke: Stroke): SerializedStroke {
  val serializedBrush = serializeBrush(stroke.brush)
  val encodedSerializedInputs = ByteArrayOutputStream().use
    {
      stroke.inputs.encode(it)
      it.toByteArray()
    }

  return SerializedStroke(
    inputs = encodedSerializedInputs,
    brush = serializedBrush
  )
}
```

Example 4 (css):
```css
fun deserializeStroke(serializedStroke: SerializedStroke): Stroke {
  val inputs = ByteArrayInputStream(serializedStroke.inputs).use {
    StrokeInputBatch.decode(it)
  }
  val brush = deserializeBrush(serializedStroke.brush)
  return Stroke(brush = brush, inputs = inputs)
}
```

---

## Save UI state in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/state-saving

**Contents:**
- Save UI state in Compose Stay organized with collections Save and categorize content based on your preferences.
- UI logic
  - Best practice
  - Verify state restoration
- Business logic
  - Best practice
  - SavedStateHandle APIs
    - Compose State
    - StateFlow
  - Summary

Depending on where your state is hoisted to and the logic that is required, you can use different APIs to store and restore your UI state. Every app uses a combination of APIs to best achieve this.

Any Android app could lose its UI state due to activity or process recreation. This loss of state can occur because of the following events:

Preserving the state after these events is essential for a positive user experience. Selecting which state to persist depends on your app's unique user flows. As a best practice, you should at least preserve user input and navigation-related state. Examples of this include the scroll position of a list, the ID of the item the user wants more detail about, the in-progress selection of user preferences, or input in text fields.

This page summarizes the APIs available to store UI state depending on where your state is hoisted to and the logic that needs it.

If your state is hoisted in the UI, either in composable functions or plain state holder classes scoped to the Composition, you can use rememberSaveable to retain state across activity and process recreation.

In the following snippet, rememberSaveable is used to store a single boolean UI element state:

@Composable fun ChatBubble( message: Message ) { var showDetails by rememberSaveable { mutableStateOf(false) } ClickableText( text = AnnotatedString(message.content), onClick = { showDetails = !showDetails } ) if (showDetails) { Text(message.timestamp) } }SavingUIStateSnippets.kt

Figure 1. Chat message bubble expands and collapses when tapped.

showDetails is a boolean variable that stores if the chat bubble is collapsed or expanded.

rememberSaveable stores UI element state in a Bundle through the saved instance state mechanism.

It is able to store primitive types to the bundle automatically. If your state is held in a type that is not primitive, like a data class, you can use different storing mechanisms, such as using the Parcelize annotation, using Compose APIs like listSaver and mapSaver, or implementing a custom saver class extending Compose runtime Saver class. See the Ways to store state documentation to learn more about these methods.

In the following snippet, the rememberLazyListState Compose API stores LazyListState, which consists of the scroll state of a LazyColumn or LazyRow, using rememberSaveable. It uses a LazyListState.Saver, which is a custom saver that is able to store and restore the scroll state. After an activity or process recreation (for example, after a configuration change like changing device orientation), the scroll state is preserved.

@Composable fun rememberLazyListState( initialFirstVisibleItemIndex: Int = 0, initialFirstVisibleItemScrollOffset: Int = 0 ): LazyListState { return rememberSaveable(saver = LazyListState.Saver) { LazyListState( initialFirstVisibleItemIndex, initialFirstVisibleItemScrollOffset ) } }SavingUIStateSnippets.kt

rememberSaveable uses a Bundle to store UI state, which is shared by other APIs that also write to it, like onSaveInstanceState() calls in your activity. However, the size of this Bundle is limited, and storing large objects could lead to TransactionTooLarge exceptions in runtime. This can be particularly problematic in single Activity apps where the same Bundle is being used across the app.

To avoid this type of crash, you should not store large complex objects or lists of objects in the bundle.

Instead, store the minimum state required, like IDs or keys, and use these to delegate restoring more complex UI state to other mechanisms, like persistent storage.

These design choices depend on the specific use cases for your app and how your users expect it to behave.

You can verify that the state stored with rememberSaveable in your Compose elements is correctly restored when the activity or process is recreated. There are specific APIs to achieve this, such as StateRestorationTester. Check out the Testing documentation to learn more.

If your UI element state is hoisted to the ViewModel because it is required by business logic, you can use ViewModel's APIs.

One of the main benefits of using a ViewModel in your Android application is that it handles configuration changes for free. When there is a configuration change, and the activity is destroyed and recreated, the UI state hoisted to the ViewModel is kept in memory. After the recreation, the old ViewModel instance is attached to the new activity instance.

However, a ViewModel instance does not survive system-initiated process death. To have the UI state survive this, use the Saved State module for ViewModel, which contains the SavedStateHandle API.

SavedStateHandle also uses the Bundle mechanism to store UI state, so you should only use it to store simple UI element state.

Screen UI state, which is produced by applying business rules and accessing layers of your application other than UI, should not be stored in SavedStateHandle due to its potential complexity and size. You can use different mechanisms to store complex or large data, like local persistent storage. After a process recreation, the screen is recreated with the restored transient state that was stored in SavedStateHandle (if any), and the screen UI state is produced again from the data layer.

SavedStateHandle has different APIs to store UI element state, most notably:

Use the saveable API of SavedStateHandle to read and write UI element state as MutableState, so it survives activity and process recreation with minimal code setup.

The saveable API supports primitive types out of the box and receives a stateSaver parameter to use custom savers, just like rememberSaveable().

In the following snippet, message stores the user input types into a TextField:

class ConversationViewModel( savedStateHandle: SavedStateHandle ) : ViewModel() { var message by savedStateHandle.saveable(stateSaver = TextFieldValue.Saver) { mutableStateOf(TextFieldValue("")) } private set fun update(newMessage: TextFieldValue) { message = newMessage } /*...*/ } val viewModel = ConversationViewModel(SavedStateHandle()) @Composable fun UserInput(/*...*/) { TextField( value = viewModel.message, onValueChange = { viewModel.update(it) } ) }SavingUIStateSnippets.kt

See the SavedStateHandle documentation for more information on using the saveable API.

Use getStateFlow() to store UI element state and consume it as a flow from the SavedStateHandle. The StateFlow is read only, and the API requires you to specify a key so you can replace the flow to emit a new value. With the key you configured, you can retrieve the StateFlow and collect the latest value.

In the following snippet, savedFilterType is a StateFlow variable that stores a filter type applied to a list of chat channels in a chat app:

private const val CHANNEL_FILTER_SAVED_STATE_KEY = "ChannelFilterKey" class ChannelViewModel( channelsRepository: ChannelsRepository, private val savedStateHandle: SavedStateHandle ) : ViewModel() { private val savedFilterType: StateFlow<ChannelsFilterType> = savedStateHandle.getStateFlow( key = CHANNEL_FILTER_SAVED_STATE_KEY, initialValue = ChannelsFilterType.ALL_CHANNELS ) private val filteredChannels: Flow<List<Channel>> = combine(channelsRepository.getAll(), savedFilterType) { channels, type -> filter(channels, type) }.onStart { emit(emptyList()) } fun setFiltering(requestType: ChannelsFilterType) { savedStateHandle[CHANNEL_FILTER_SAVED_STATE_KEY] = requestType } /*...*/ } enum class ChannelsFilterType { ALL_CHANNELS, RECENT_CHANNELS, ARCHIVED_CHANNELS }SavingUIStateSnippets.kt

Every time the user selects a new filter type, setFiltering is called. This saves a new value in SavedStateHandle stored with the key _CHANNEL_FILTER_SAVED_STATE_KEY_. savedFilterType is a flow emitting the latest value stored to the key. filteredChannels is subscribed to the flow to perform the channel filtering.

See the SavedStateHandle documentation for more information on the getStateFlow() API.

The following table summarizes the APIs covered in this section, and when to use each to save UI state:

The API to use depends on where the state is held and the logic that it requires. For state that is used in UI logic, use rememberSaveable. For state that is used in business logic, if you hold it in a ViewModel, save it using SavedStateHandle.

You should use the bundle APIs (rememberSaveable and SavedStateHandle) to store small amounts of UI state. This data is the minimum necessary to restore the UI back to its previous state, together with other storing mechanisms. For example, if you store the ID of a profile the user was looking at in the bundle, you can fetch heavy data, like profile details, from the data layer.

For more information on the different ways of saving UI state, see the general Saving UI State documentation and the data layer page of the architecture guide.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Migrate to state-based text fields Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/migrate-state-based

**Contents:**
- Migrate to state-based text fields Stay organized with collections Save and categorize content based on your preferences.
- Basic usage
  - Value-based
  - State-based
- Filtering through onValueChange
  - Value-based
  - State-based
- Credit card formatter TextField
  - Value-based
  - State-based

This page provides examples of how you can migrate value-based TextFields to state-based TextFields. See the Configure text fields page for information on the differences between value and state-based TextFields.

@Composable fun OldSimpleTextField() { var state by rememberSaveable { mutableStateOf("") } TextField( value = state, onValueChange = { state = it }, singleLine = true, ) }TextFieldMigrationSnippets.kt

@Composable fun NewSimpleTextField() { TextField( state = rememberTextFieldState(), lineLimits = TextFieldLineLimits.SingleLine ) }TextFieldMigrationSnippets.kt

@Composable fun OldNoLeadingZeroes() { var input by rememberSaveable { mutableStateOf("") } TextField( value = input, onValueChange = { newText -> input = newText.trimStart { it == '0' } } ) }TextFieldMigrationSnippets.kt

@Preview @Composable fun NewNoLeadingZeros() { TextField( state = rememberTextFieldState(), inputTransformation = InputTransformation { while (length > 0 && charAt(0) == '0') delete(0, 1) } ) }TextFieldMigrationSnippets.kt

@Composable fun OldTextFieldCreditCardFormatter() { var state by remember { mutableStateOf("") } TextField( value = state, onValueChange = { if (it.length <= 16) state = it }, visualTransformation = VisualTransformation { text -> // Making XXXX-XXXX-XXXX-XXXX string. var out = "" for (i in text.indices) { out += text[i] if (i % 4 == 3 && i != 15) out += "-" } TransformedText( text = AnnotatedString(out), offsetMapping = object : OffsetMapping { override fun originalToTransformed(offset: Int): Int { if (offset <= 3) return offset if (offset <= 7) return offset + 1 if (offset <= 11) return offset + 2 if (offset <= 16) return offset + 3 return 19 } override fun transformedToOriginal(offset: Int): Int { if (offset <= 4) return offset if (offset <= 9) return offset - 1 if (offset <= 14) return offset - 2 if (offset <= 19) return offset - 3 return 16 } } ) } ) }TextFieldMigrationSnippets.kt

@Composable fun NewTextFieldCreditCardFormatter() { val state = rememberTextFieldState() TextField( state = state, inputTransformation = InputTransformation.maxLength(16), outputTransformation = OutputTransformation { if (length > 4) insert(4, "-") if (length > 9) insert(9, "-") if (length > 14) insert(14, "-") }, ) }TextFieldMigrationSnippets.kt

@Composable fun OldTextFieldStateUpdate(userRepository: UserRepository) { var username by remember { mutableStateOf("") } LaunchedEffect(Unit) { username = userRepository.fetchUsername() } TextField( value = username, onValueChange = { username = it } ) }TextFieldMigrationSnippets.kt

@Composable fun NewTextFieldStateUpdate(userRepository: UserRepository) { val usernameState = rememberTextFieldState() LaunchedEffect(Unit) { usernameState.setTextAndPlaceCursorAtEnd(userRepository.fetchUsername()) } TextField(state = usernameState) }TextFieldMigrationSnippets.kt

@Composable fun OldTextFieldAddMarkdownEmphasis() { var markdownState by remember { mutableStateOf(TextFieldValue()) } Button(onClick = { // add ** decorations around the current selection, also preserve the selection markdownState = with(markdownState) { copy( text = buildString { append(text.take(selection.min)) append("**") append(text.substring(selection)) append("**") append(text.drop(selection.max)) }, selection = TextRange(selection.min + 2, selection.max + 2) ) } }) { Text("Bold") } TextField( value = markdownState, onValueChange = { markdownState = it }, maxLines = 10 ) }TextFieldMigrationSnippets.kt

@Composable fun NewTextFieldAddMarkdownEmphasis() { val markdownState = rememberTextFieldState() LaunchedEffect(Unit) { // add ** decorations around the current selection markdownState.edit { insert(originalSelection.max, "**") insert(originalSelection.min, "**") selection = TextRange(originalSelection.min + 2, originalSelection.max + 2) } } TextField( state = markdownState, lineLimits = TextFieldLineLimits.MultiLine(1, 10) ) }TextFieldMigrationSnippets.kt

In this use case, a button adds the Markdown decorations to make the text bold around the cursor or the current selection. It also maintains the selection position after the changes.

Many applications follow the Modern app development guidelines, which promote using a StateFlow to define the UI state of a screen or a component through a single immutable class that carries all the information.

In these types of applications, a form like a Login screen with text input is usually designed as follows:

class LoginViewModel : ViewModel() { private val _uiState = MutableStateFlow(UiState()) val uiState: StateFlow<UiState> get() = _uiState.asStateFlow() fun updateUsername(username: String) = _uiState.update { it.copy(username = username) } fun updatePassword(password: String) = _uiState.update { it.copy(password = password) } } data class UiState( val username: String = "", val password: String = "" ) @Composable fun LoginForm( loginViewModel: LoginViewModel, modifier: Modifier = Modifier ) { val uiState by loginViewModel.uiState.collectAsStateWithLifecycle() Column(modifier) { TextField( value = uiState.username, onValueChange = { loginViewModel.updateUsername(it) } ) TextField( value = uiState.password, onValueChange = { loginViewModel.updatePassword(it) }, visualTransformation = PasswordVisualTransformation() ) } }TextFieldMigrationSnippets.kt

This design perfectly fits with the TextFields that use the value, onValueChange state hoisting paradigm. However, there are unpredictable downsides to this approach when it comes to text input. The deep synchronization issues with this approach are explained in detail in the Effective state management for TextField in Compose blog post.

The problem is that the new TextFieldState design is not directly compatible with the StateFlow backed ViewModel UI state. It may look strange to replace username: String and password: String with username: TextFieldState and password: TextFieldState, since TextFieldState is an inherently mutable data structure.

A common recommendation is to avoid placing UI dependencies into a ViewModel. Although this is generally a good practice, it can sometimes be misinterpreted. This is particularly true for Compose dependencies that are purely data structures and don't carry any UI elements with them, like TextFieldState.

Classes like MutableState or TextFieldState are simple state holders that are backed by Compose's Snapshot state system. They are no different from dependencies like StateFlow or RxJava. Therefore,we encourage you to re-evaluate how you apply the "no UI dependencies in ViewModel" principle in your code. Keeping a reference to a TextFieldState within your ViewModel is not an inherently bad practice.

We recommend you extract values like username or password from UiState, and keep a separate reference for them in the ViewModel.

class LoginViewModel : ViewModel() { val usernameState = TextFieldState() val passwordState = TextFieldState() } @Composable fun LoginForm( loginViewModel: LoginViewModel, modifier: Modifier = Modifier ) { Column(modifier) { TextField(state = loginViewModel.usernameState,) SecureTextField(state = loginViewModel.passwordState) } }TextFieldMigrationSnippets.kt

These types of architectural changes are not always easy. You may not have the freedom to make these changes, or the time investment could outweigh the benefits of using the new TextFields. In this case, you can still use state-based text fields with a little tweak.

class LoginViewModel : ViewModel() { private val _uiState = MutableStateFlow(UiState()) val uiState: StateFlow<UiState> get() = _uiState.asStateFlow() fun updateUsername(username: String) = _uiState.update { it.copy(username = username) } fun updatePassword(password: String) = _uiState.update { it.copy(password = password) } } data class UiState( val username: String = "", val password: String = "" ) @Composable fun LoginForm( loginViewModel: LoginViewModel, modifier: Modifier = Modifier ) { val initialUiState = remember(loginViewModel) { loginViewModel.uiState.value } Column(modifier) { val usernameState = rememberTextFieldState(initialUiState.username) LaunchedEffect(usernameState) { snapshotFlow { usernameState.text.toString() }.collectLatest { loginViewModel.updateUsername(it) } } TextField(usernameState) val passwordState = rememberTextFieldState(initialUiState.password) LaunchedEffect(usernameState) { snapshotFlow { usernameState.text.toString() }.collectLatest { loginViewModel.updatePassword(it) } } SecureTextField(passwordState) } }TextFieldMigrationSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Compose state callbacks with RememberObserver and RetainObserver Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/state-callbacks

**Contents:**
- Compose state callbacks with RememberObserver and RetainObserver Stay organized with collections Save and categorize content based on your preferences.
- Initialization and cleanup with RememberObserver and RetainObserver
  - Run initialization side effects during onRemembered or onRetained, not construction
  - Teardown when forgotten, retired, or abandoned
- Keep RememberObserver and RetainObserver implementations private
- Considerations when remembering objects
  - Only remember objects once
  - Assume function arguments are already remembered
  - Don't retain an object that's already remembered

In Jetpack Compose, an object can implement RememberObserver to receive callbacks when it's used with remember to know when it starts and stops being remembered in the composition hierarchy. Similarly, you can use RetainObserver to receive information about the state of an object used with retain.

For objects that use this lifecycle information from the composition hierarchy, we recommend a few best practices to verify that your objects act as good citizens in the platform and defend against misuse. Specifically, use the onRemembered (or onRetained) callbacks to launch work instead of the constructor, cancel all work when objects stop being remembered or retained, and avoid leaking implementations of RememberObserver and RetainObserver to avoid accidental calls. The next section explains these recommendations in more depth.

The Thinking in Compose guide describes the mental model behind composition. When working with RememberObserver and RetainObserver, it's important to keep in mind two behaviors of composition:

When objects are remembered or retained, the calculation lambda runs as part of composition. For the same reasons that you wouldn't perform a side-effect or launch a coroutine during composition, you should also not perform side-effects in the calculation lambda passed to remember, retain, and their variations. This includes as part of the constructor for the remembered or retained objects.

Instead, when implementing RememberObserver, or RetainObserver, verify that all effects and launched jobs are dispatched in the onRemembered callback. This offers the same timing as the SideEffect APIs. It also guarantees that these effects only execute when the composition is applied, which prevents orphaned jobs and memory leaks if a recomposition is abandoned or deferred.

class MyComposeObject : RememberObserver { private val job = Job() private val coroutineScope = CoroutineScope(Dispatchers.Main + job) init { // Not recommended: This will cause work to begin during composition instead of // with other effects. Move this into onRemembered(). coroutineScope.launch { loadData() } } override fun onRemembered() { // Recommended: Move any cancellable or effect-driven work into the onRemembered // callback. If implementing RetainObserver, this should go in onRetained. coroutineScope.launch { loadData() } } private suspend fun loadData() { /* ... */ } // ... }RememberAndRetainObserverSnippets.kt

To avoid leaking resources or leaving background jobs orphaned, remembered objects must also be disposed of. For objects that implement RememberObserver, this means that anything initialized in onRemembered must have a matching release call in onForgotten.

Because composition can be canceled, objects that implement RememberObserver must also tidy up after themselves if they are abandoned in compositions. An object is abandoned when it is returned by remember in a composition that gets canceled or fails. (This happens most commonly when using PausableComposition, and can also occur when using hot reload with Android Studio's composable preview tooling.)

When a remembered object is abandoned, it receives only the call to onAbandoned (and no call to onRemembered). To implement the abandon method, dispose of anything created between when the object is initialized and when the object would have received the onRemembered callback.

class MyComposeObject : RememberObserver { private val job = Job() private val coroutineScope = CoroutineScope(Dispatchers.Main + job) // ... override fun onForgotten() { // Cancel work launched from onRemembered. If implementing RetainObserver, onRetired // should cancel work launched from onRetained. job.cancel() } override fun onAbandoned() { // If any work was launched by the constructor as part of remembering the object, // you must cancel that work in this callback. For work done as part of the construction // during retain, this code should will appear in onUnused. job.cancel() } }RememberAndRetainObserverSnippets.kt

When writing public APIs, use caution when extending RememberObserver and RetainObserver in creating classes that are returned publicly. A user may not remember your object when you expect them to, or may remember your object in a different way than you intended. Because of this, we recommend not exposing constructors or factory functions for objects that implement RememberObserver or RetainObserver. Note that this is dependent on the runtime type of a class, not the declared type — remembering an object that implements RememberObserver or RetainObserver but is casted to Any still causes the object to receive callbacks.

abstract class MyManager class MyComposeManager : MyManager() { // Callers that construct this object must manually call initialize and teardown fun initialize() { /*...*/ } fun teardown() { /*...*/ } } @Composable fun rememberMyManager(): MyManager { // Protect the RememberObserver implementation by never exposing it outside the library return remember { object : RememberObserver { val manager = MyComposeManager() override fun onRemembered() = manager.initialize() override fun onForgotten() = manager.teardown() override fun onAbandoned() { /* Nothing to do if manager hasn't initialized */ } } }.manager }RememberAndRetainObserverSnippets.kt

In addition to the previous recommendations surrounding RememberObserver and RetainObserver, we also recommend being mindful of and avoiding accidentally re-remembering objects both for performance and correctness. The following sections go into more depth on specific re-remembering scenarios and why they should be avoided.

Re-remembering an object can be dangerous. In the best case scenario, you may be wasting resources remembering a value that's already remembered. But if an object implements RememberObserver and is remembered twice unexpectedly, it will receive more callbacks than it expects. This can cause issues, as the onRemembered and onForgotten logic will execute twice, and most implementations of RememberObserver don't support this case. If a second remember call happens in a different scope that has a different lifespan from the original remember, many implementations of RememberObserver.onForgotten dispose the object before it is finished being used.

This advice does not apply to objects that are remembered again transitively (as in, remembered objects that consume another remembered object). It is common to write code that looks as follows, which is allowable because a different object is being remembered and therefore does not cause unexpected callback doubling.

val foo: Foo = rememberFoo() // Acceptable: val bar: Bar = remember { Bar(foo) } // Recommended key usage: val barWithKey: Bar = remember(foo) { Bar(foo) }RememberAndRetainObserverSnippets.kt

A function shouldn't remember any of its parameters both because it can lead to double callback invocations for RememberObserver and because it is unnecessary. If an input parameter must be remembered, either verify that it does not implement RememberObserver, or require callers to remember their argument.

This does not apply to objects remembered transitively. When remembering an object derived from a function's arguments, consider specifying it as one of the keys to remember:

@Composable fun MyComposable( parameter: Foo ) { // Acceptable: val derivedValue = remember { Bar(parameter) } // Also Acceptable: val derivedValueWithKey = remember(parameter) { Bar(parameter) } }RememberAndRetainObserverSnippets.kt

Similar to re-remembering an object, you should avoid retaining an object that's remembered to try and extend its lifespan. This is a fallout of the advice in State lifespans: retain shouldn't be used with objects that have a lifespan that doesn't match the lifespan retain offers. Since remembered objects have a shorter lifespan than retained objects, you shouldn't retain a remembered object. Instead, prefer retaining the object at the origin site instead of remembering it.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (swift):
```swift
abstract class MyManager

// Not Recommended: Exposing a public constructor (even implicitly) for an object implementing
// RememberObserver can cause unexpected invocations if it is remembered multiple times.
class MyComposeManager : MyManager(), RememberObserver { ... }

// Not Recommended: The return type may be an implementation of RememberObserver and should be
// remembered explicitly.
fun createFoo(): MyManager = MyComposeManager()
```

Example 2 (typescript):
```typescript
val first: RememberObserver = rememberFoo()

// Not Recommended: Re-remembered `Foo` now gets double callbacks
val second = remember { first }
```

Example 3 (python):
```python
@Composable
fun MyComposable(
    parameter: Foo
) {
    // Not Recommended: Input should be remembered by the caller.
    val rememberedParameter = remember { parameter }
}
```

---
