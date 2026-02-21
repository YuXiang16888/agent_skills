# Jetpack-Compose - Gestures

**Pages:** 30

---

## Drag and drop Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/user-interactions/drag-and-drop

**Contents:**
- Drag and drop Stay organized with collections Save and categorize content based on your preferences.
- Specify a drag source
- Receive drop data
- Handle drag-and-drop events
- Additional resources

Jetpack Compose supports drag and drop with two modifiers:

For example, to enable users to drag an image in your app, create an image composable and add the dragAndDropSource modifier. To set up a drop target, create another image composable and add the dragAndDropTarget modifier.

The modifiers can be applied to multiple drag sources and multiple drop targets.

The modifiers enable apps to share data between two or more composables using ClipData, which is interoperable with View implementations.

To enable drag events inside a component, add the dragAndDropSource modifier. This takes a function as a parameter. Inside this function, use DragAndDropTransferData to represent the transferable data. The data can be a remote URI, rich text data on the clipboard, a local file, or more, but they all need to be wrapped in a ClipData object. Provide plain text, for example, as follows:

Modifier.dragAndDropSource { _ -> DragAndDropTransferData( ClipData.newPlainText( "image Url", url ) ) }DragAndDropSnippets.kt

To allow the drag action to cross the borders of the app, the DragAndDropTransferData constructor accepts a flags argument. In the following example, the DRAG_FLAG_GLOBAL constant specifies that data can be dragged from one app into another:

Modifier.dragAndDropSource { _ -> DragAndDropTransferData( ClipData.newPlainText( "image Url", url ), flags = View.DRAG_FLAG_GLOBAL ) }DragAndDropSnippets.kt

DragAndDropTransferData accepts flags supported by the Android View system. See the list of View constants for an exhaustive list of available flags.

Assign the dragAndDropTarget modifier to a composable to enable the composable to receive drag-and-drop events. The modifier has two parameters: the first acts as a filter and specifies the kind of data the modifier can accept, and the second delivers the data in a callback.

Note that the callback instance should be remembered. The following snippet shows how to remember the callback:

val callback = remember { object : DragAndDropTarget { override fun onDrop(event: DragAndDropEvent): Boolean { // Parse received data return true } } }DragAndDropSnippets.kt

To accept data from other apps, use the requestDragAndDropPermission to enable the reception, and release it once done:

val externalAppCallback = remember { object : DragAndDropTarget { override fun onDrop(event: DragAndDropEvent): Boolean { val permission = activity.requestDragAndDropPermissions(event.toAndroidDragEvent()) // Parse received data permission?.release() return true } } }DragAndDropSnippets.kt

It's important to remember that the DragAndDropEvent returned from the Compose callback is different from the one expected by the requestDragAndDropPermission method, so you need to call the toAndroidDragEvent() extension function on the parameter before passing it to the permission request.

The next snippet demonstrates how to handle dropped plain text:

Modifier.dragAndDropTarget( shouldStartDragAndDrop = { event -> event.mimeTypes().contains(ClipDescription.MIMETYPE_TEXT_PLAIN) }, target = callback // or externalAppCallback )DragAndDropSnippets.kt

The callback function should return true if the event is consumed, or false if the event is refused and does not propagate to the parent component.

Override callbacks in the DragAndDropTarget interface to observe when a drag-and-drop event starts, ends, or enters or exits a component for precise control of the UI and the app's behavior:

object : DragAndDropTarget { override fun onStarted(event: DragAndDropEvent) { // When the drag event starts } override fun onEntered(event: DragAndDropEvent) { // When the dragged object enters the target surface } override fun onEnded(event: DragAndDropEvent) { // When the drag event stops } override fun onExited(event: DragAndDropEvent) { // When the dragged object exits the target surface } override fun onDrop(event: DragAndDropEvent): Boolean = true }DragAndDropSnippets.kt

Codelab: Drag and drop in Compose

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Change focus traversal order Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/focus/change-focus-traversal-order

**Contents:**
- Change focus traversal order Stay organized with collections Save and categorize content based on your preferences.
- Override one-dimensional traversal order
- Override two-dimensional traversal order
- Recommended for you

The Default focus traversal order section described how Compose automatically adds focus traversal behavior to your elements, for both one-dimensional (tab key) and two-dimensional (arrow keys) navigation. In some cases, you might need to override this default behavior and be more explicit about the required traversal order.

To change the default focus traversal order for one-dimensional navigation, you create a set of references, one for each focusable composable:

val (first, second, third, fourth) = remember { FocusRequester.createRefs() }FocusSnippets.kt

Then, use the focusRequester modifier to associate each of them with a composable:

Column { Row { TextButton({}, Modifier.focusRequester(first)) { Text("First field") } TextButton({}, Modifier.focusRequester(third)) { Text("Third field") } } Row { TextButton({}, Modifier.focusRequester(second)) { Text("Second field") } TextButton({}, Modifier.focusRequester(fourth)) { Text("Fourth field") } } }FocusSnippets.kt

You can now use the focusProperties modifier to specify a custom traversal order:

Column { Row { TextButton( {}, Modifier .focusRequester(first) .focusProperties { next = second } ) { Text("First field") } TextButton( {}, Modifier .focusRequester(third) .focusProperties { next = fourth } ) { Text("Third field") } } Row { TextButton( {}, Modifier .focusRequester(second) .focusProperties { next = third } ) { Text("Second field") } TextButton( {}, Modifier .focusRequester(fourth) .focusProperties { next = first } ) { Text("Fourth field") } } }FocusSnippets.kt

It is also possible to add fine-grained control over the focus traversal order for two-dimensional navigation with the arrow keys. For each element, you can override the default navigation destination for each of the directions by adding the focusProperties modifier and specifying the item that would come up, down, or any other direction:

TextButton( onClick = {}, modifier = Modifier .focusRequester(fourth) .focusProperties { down = third right = second } ) {}FocusSnippets.kt

This technique not only effectively uses keyboard arrows, but would work with D-Pads and sticks on wired and wireless controllers.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Multitouch: Panning, zooming, rotating Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/multi-touch

**Contents:**
- Multitouch: Panning, zooming, rotating Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

To detect multitouch gestures used for panning, zooming and rotating, you can use the transformable modifier. This modifier does not transform elements by itself, it only detects the gestures.

@Composable private fun TransformableSample() { // set up all transformation states var scale by remember { mutableFloatStateOf(1f) } var rotation by remember { mutableFloatStateOf(0f) } var offset by remember { mutableStateOf(Offset.Zero) } val state = rememberTransformableState { zoomChange, offsetChange, rotationChange -> scale *= zoomChange rotation += rotationChange offset += offsetChange } Box( Modifier // apply other transformations like rotation and zoom // on the pizza slice emoji .graphicsLayer( scaleX = scale, scaleY = scale, rotationZ = rotation, translationX = offset.x, translationY = offset.y ) // add transformable to listen to multitouch transformation events // after offset .transformable(state = state) .background(Color.Blue) .fillMaxSize() ) }GesturesSnippets.kt

If you need to combine zooming, panning and rotation with other gestures, you can use the PointerInputScope.detectTransformGestures detector.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## About stylus input Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input

**Contents:**
- About stylus input Stay organized with collections Save and categorize content based on your preferences.
- Topics
- Prerequisites

A stylus enables users to interact with apps comfortably and with pinpoint accuracy for note‑taking, sketching, working with productivity apps, and just relaxing and having fun with games and entertainment apps.

The stylus input documentation provides guidance on the following topics:

The stylus input guidance assumes a basic understanding of the following Android APIs and components:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-05-09 UTC.

---

## Tap and press Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/tap-and-press

**Contents:**
- Tap and press Stay organized with collections Save and categorize content based on your preferences.
- Respond to tap or click
- Long-press to show a contextual context menu
- Dismiss a composable by tapping a scrim
- Double tap to zoom
- Recommended for you

Many composables have built-in support for taps or clicks and include an onClick lambda. For example, you can create a clickable Surface that includes all Material Design behavior appropriate for interaction with surfaces:

Surface(onClick = { /* handle click */ }) { Text("Click me!", Modifier.padding(24.dp)) }TapAndPress.kt

But clicks are not the only way a user can interact with composables. This page focuses on gestures that involve a single pointer, where the position of that pointer is not significant for the handling of that event. The following table lists these types of gestures:

Pointer goes down and then up

Pointer goes down, up, down, up

Pointer goes down, and is held for a longer time

clickable is a commonly used modifier that makes a composable react to taps or clicks. This modifier also adds additional features, such as support for focus, mouse and stylus hovering, and a customizable visual indication when pressed. The modifier responds to "clicks" in the widest sense of the word-- not only with mouse or finger, but also click events through keyboard input or when using accessibility services.

Imagine a grid of images, where an image shows full-screen when a user clicks on it:

You can add the clickable modifier to each item in the grid to implement this behavior:

@Composable private fun ImageGrid(photos: List<Photo>) { var activePhotoId by rememberSaveable { mutableStateOf<Int?>(null) } LazyVerticalGrid(columns = GridCells.Adaptive(minSize = 128.dp)) { items(photos, { it.id }) { photo -> ImageItem( photo, Modifier.clickable { activePhotoId = photo.id } ) } } if (activePhotoId != null) { FullScreenImage( photo = photos.first { it.id == activePhotoId }, onDismiss = { activePhotoId = null } ) } }TapAndPress.kt

The clickable modifier also adds additional behavior:

combinedClickable lets you add double tap or long-press behavior in addition to normal click behavior. You can use combinedClickable to show a context menu when a user touches and holds a grid image:

var contextMenuPhotoId by rememberSaveable { mutableStateOf<Int?>(null) } val haptics = LocalHapticFeedback.current LazyVerticalGrid(columns = GridCells.Adaptive(minSize = 128.dp)) { items(photos, { it.id }) { photo -> ImageItem( photo, Modifier .combinedClickable( onClick = { activePhotoId = photo.id }, onLongClick = { haptics.performHapticFeedback(HapticFeedbackType.LongPress) contextMenuPhotoId = photo.id }, onLongClickLabel = stringResource(R.string.open_context_menu) ) ) } } if (contextMenuPhotoId != null) { PhotoActionsSheet( photo = photos.first { it.id == contextMenuPhotoId }, onDismissSheet = { contextMenuPhotoId = null } ) }TapAndPress.kt

As a best practice, you should include haptic feedback when the user long-presses elements, which is why the snippet includes the performHapticFeedback invocation.

In the examples above, clickable and combinedClickable add useful functionality to your composables. They show a visual indication on interaction, respond to hovering, and include focus, keyboard, and accessibility support. But this extra behavior is not always desirable.

Let's look at the image detail screen. The background should be semi-transparent and the user should be able to tap that background to dismiss the detail screen:

In this case, that background should not have any visual indication on interaction, should not respond to hovering, should not be focusable, and its response to keyboard and accessibility events differ from that of a typical composable. Instead of trying to adapt the clickable behavior, you can drop down to a lower abstraction level and directly use the pointerInput modifier in combination with the detectTapGestures method:

@Composable private fun Scrim(onClose: () -> Unit, modifier: Modifier = Modifier) { val strClose = stringResource(R.string.close) Box( modifier // handle pointer input .pointerInput(onClose) { detectTapGestures { onClose() } } // handle accessibility services .semantics(mergeDescendants = true) { contentDescription = strClose onClick { onClose() true } } // handle physical keyboard input .onKeyEvent { if (it.key == Key.Escape) { onClose() true } else { false } } // draw scrim .background(Color.DarkGray.copy(alpha = 0.75f)) ) }TapAndPress.kt

As the key of the pointerInput modifier you pass the onClose lambda. This automatically re-executes the lambda, making sure the right callback is called when the user taps the scrim.

Sometimes clickable and combinedClickable do not include enough information to respond to the interaction in the correct way. For example, composables might need access to the position within the composable's bounds where the interaction took place.

Let's look at the image detail screen again. A best practice is to make it possible to zoom in on the image by double tapping:

As you can see in the video, zooming in occurs around the position of the tap event. The result is different when we zoom in on the left part of the image versus the right part. We can use the pointerInput modifier in combination with the detectTapGestures to incorporate the tap position into our calculation:

var zoomed by remember { mutableStateOf(false) } var zoomOffset by remember { mutableStateOf(Offset.Zero) } Image( painter = rememberAsyncImagePainter(model = photo.highResUrl), contentDescription = null, modifier = modifier .pointerInput(Unit) { detectTapGestures( onDoubleTap = { tapOffset -> zoomOffset = if (zoomed) Offset.Zero else calculateOffset(tapOffset, size) zoomed = !zoomed } ) } .graphicsLayer { scaleX = if (zoomed) 2f else 1f scaleY = if (zoomed) 2f else 1f translationX = zoomOffset.x translationY = zoomOffset.y } )TapAndPress.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Keyboard Shortcuts Helper Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/keyboard-input/keyboard-shortcuts-helper

**Contents:**
- Keyboard Shortcuts Helper Stay organized with collections Save and categorize content based on your preferences.
- Provide shortcuts to Keyboard Shortcuts Helper
- Organize keyboard shortcuts with groups
- Open Keyboard Shortcuts Helper from code

Keyboard Shortcuts Helper enables users to discover keyboard shortcuts for the platform and open apps. Publish your app's shortcuts in Keyboard Shortcuts Helper to improve user productivity and ease of use.

Users press Meta+/ to open the keyboard shortcuts screen, which is available on Android 7.0 (API level 24) and higher.

You can provide available keyboard shortcut lists to Keyboard Shortcuts Helper by overriding the onProvideKeyboardShortcuts() window callback. The following snippet demonstrates an implementation of onProvideKeyboardShortcuts() to add a group of four shortcuts:

KeyboardShortcutInfo describes a keyboard shortcut. The list of keyboard shortcuts are wrapped as a KeyboardShortcutGroup object. Apps notify available keyboard shortcuts to Keyboard Shortcuts Helper by adding the KeyboardShortcutGroup objects to the mutable list passed as the first parameter of the method.

Keyboard Shortcuts Helper displays keyboard shortcuts in separate groups so users can find shortcuts by use case or for screens of your app. Figure 2 shows the keyboard shortcuts categorized into two groups: cursor movement and message editing.

Your app registers two or more groups of keyboard shortcuts by creating a KeyboardShortcutGroup object for each group. In the following snippet, two KeyboardShortCutGroup objects are added to the mutable list passed to the onProvideKeyboardShortcuts() method. The objects are displayed as categories in Keyboard Shortcuts Helper as figure 2 shows.

Apps display the keyboard shortcuts screen by calling the requestShowKeyboardShortcuts() method. In the following snippet, Keyboard Shortcuts Helper opens when users tap or click the button or press the Enter key.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (swift):
```swift
class MainActivity : ComponentActivity() {
    // Activity codes such as overridden onStart method.

    override fun onProvideKeyboardShortcuts(
        data: MutableList<KeyboardShortcutGroup>?,
        menu: Menu?,
        deviceId: Int
    ) {
        val shortcutGroup = KeyboardShortcutGroup(
            "Cursor movement",
            listOf(
                KeyboardShortcutInfo("Up", KeyEvent.KEYCODE_P, KeyEvent.META_CTRL_ON),
                KeyboardShortcutInfo("Down", KeyEvent.KEYCODE_N, KeyEvent.META_CTRL_ON),
                KeyboardShortcutInfo("Forward", KeyEvent.KEYCODE_F, KeyEvent.META_CTRL_ON),
                KeyboardShortcutInfo("Backward", KeyEvent.KEYCODE_B, KeyEvent.META_CTRL_ON),
            )
        )
        data?.add(shortcutGroup)
    }
}
```

Example 2 (swift):
```swift
override fun onProvideKeyboardShortcuts(
    data: MutableList<KeyboardShortcutGroup>?,
    menu: Menu?,
    deviceId: Int
) {
    val cursorMovement = KeyboardShortcutGroup(
        "Cursor movement",
        listOf(
            KeyboardShortcutInfo("Up", KeyEvent.KEYCODE_P, KeyEvent.META_CTRL_ON),
            KeyboardShortcutInfo("Down", KeyEvent.KEYCODE_N, KeyEvent.META_CTRL_ON),
            KeyboardShortcutInfo("Forward", KeyEvent.KEYCODE_F, KeyEvent.META_CTRL_ON),
            KeyboardShortcutInfo("Backward", KeyEvent.KEYCODE_B, KeyEvent.META_CTRL_ON),
        )
    )

    val messageEdit = KeyboardShortcutGroup(
        "Message editing",
        listOf(
            KeyboardShortcutInfo("Select All", KeyEvent.KEYCODE_A, KeyEvent.META_CTRL_ON),
            KeyboardShortcutInfo("Send a message", KeyEvent.KEYCODE_ENTER, KeyEvent.META_SHIFT_ON)
        )
    )

    data?.add(cursorMovement)
    data?.add(messageEdit)
}
```

Example 3 (swift):
```swift
val activity = LocalActivity.current

Button(onClick = { activity.requestShowKeyboardShortcuts() }) {
    Text(text = "Show keyboard shortcuts")
}
```

---

## Drag, swipe, and fling Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/drag-swipe-fling

**Contents:**
- Drag, swipe, and fling Stay organized with collections Save and categorize content based on your preferences.
- Swiping
- Recommended for you

The draggable modifier is the high-level entry point to drag gestures in a single orientation, and reports the drag distance in pixels.

It's important to note that this modifier is similar to scrollable, in that it only detects the gesture. You need to hold the state and represent it on screen by, for example, moving the element via the offset modifier:

@Composable private fun DraggableText() { var offsetX by remember { mutableFloatStateOf(0f) } Text( modifier = Modifier .offset { IntOffset(offsetX.roundToInt(), 0) } .draggable( orientation = Orientation.Horizontal, state = rememberDraggableState { delta -> offsetX += delta } ), text = "Drag me!" ) }GesturesSnippets.kt

If you need to control the whole dragging gesture, consider using the drag gesture detector instead, via the pointerInput modifier.

@Composable private fun DraggableTextLowLevel() { Box(modifier = Modifier.fillMaxSize()) { var offsetX by remember { mutableFloatStateOf(0f) } var offsetY by remember { mutableFloatStateOf(0f) } Box( Modifier .offset { IntOffset(offsetX.roundToInt(), offsetY.roundToInt()) } .background(Color.Blue) .size(50.dp) .pointerInput(Unit) { detectDragGestures { change, dragAmount -> change.consume() offsetX += dragAmount.x offsetY += dragAmount.y } } ) } }GesturesSnippets.kt

The swipeable modifier lets you drag elements which, when released, animate towards typically two or more anchor points defined in an orientation. A common usage for this is to implement a ‘swipe-to-dismiss’ pattern.

It's important to note that this modifier does not move the element, it only detects the gesture. You need to hold the state and represent it on screen by, for example, moving the element via the offset modifier.

The swipeable state is required in the swipeable modifier, and can be created and remembered with rememberSwipeableState(). This state also provides a set of useful methods to programmatically animate to anchors (see snapTo, animateTo, performFling, and performDrag) as well as properties to observe the dragging progress.

The swipe gesture can be configured to have different threshold types, such as FixedThreshold(Dp) and FractionalThreshold(Float), and they can be different for each anchor point from-to combination.

For more flexibility, you can configure the resistance when swiping past the bounds and, also, the velocityThreshold which will animate a swipe to the next state, even if the positional thresholdshave not been reached.

@OptIn(ExperimentalMaterialApi::class) @Composable private fun SwipeableSample() { val width = 96.dp val squareSize = 48.dp val swipeableState = rememberSwipeableState(0) val sizePx = with(LocalDensity.current) { squareSize.toPx() } val anchors = mapOf(0f to 0, sizePx to 1) // Maps anchor points (in px) to states Box( modifier = Modifier .width(width) .swipeable( state = swipeableState, anchors = anchors, thresholds = { _, _ -> FractionalThreshold(0.3f) }, orientation = Orientation.Horizontal ) .background(Color.LightGray) ) { Box( Modifier .offset { IntOffset(swipeableState.offset.value.roundToInt(), 0) } .size(squareSize) .background(Color.DarkGray) ) } }GesturesSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Geometry APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-geometry-apis

**Contents:**
- Geometry APIs Stay organized with collections Save and categorize content based on your preferences.

The Geometry APIs let you create interactive tools such as selection mechanisms and erasers.

This section shows how to use the Geometry APIs to implement an eraser.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

**Examples:**

Example 1 (typescript):
```typescript
private fun eraseIntersectingStrokes(
  currentX: Float,
  currentY: Float,
  currentStrokes: MutableList<Stroke>,
  ): Unit {
    val prev = previousPoint
    previousPoint = MutableVec(currentX, currentY)
    if (prev == null) return

    val segment = MutableSegment(prev, MutableVec(currentX, currentY))
    val parallelogram = MutableParallelogram().populateFromSegmentAndPadding(
      segment,
      eraserPadding
    )
    currentStrokes.removeAll {
        it.shape.intersects(parallelogram, AffineTransform.IDENTITY)
    }
}
```

---

## Copy and paste Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/copy-and-paste

**Contents:**
- Copy and paste Stay organized with collections Save and categorize content based on your preferences.
- Work with text
  - Copy with ClipboardManager
  - Paste with ClipboardManager
- Work with rich content
  - Copy rich content
- Paste a rich content
- Work with complex data
- Feedback to copying content
  - Sensitive content

The Android clipboard-based framework for copying and pasting supports primitive and complex data types, including:

Simple text data is stored directly in the clipboard, while complex data is stored as a reference that the pasting application resolves with a content provider.

Copying and pasting works both within an application and between applications that implement the framework.

Because part of the framework uses content providers, this document assumes some familiarity with the Android Content Provider API.

Some components support copying and pasting text out of the box, as shown in the following table.

For example, you can copy the text in the card to the clipboard in the following snippet and paste the copied text to the TextField. You display the menu to paste the text by a touch & hold on the TextField, or by tapping the cursor handle.

You can paste the text with the following keyboard shortcut: Ctrl+V . The keyboard shortcut is also available by default. Refer to Handle keyboard actions for details.

You can copy texts to the clipboard with ClipboardManager. Its setText() method copies the passed String object to the clipboard. The following snippet copies "Hello, clipboard" to the clipboard when the user clicks the button.

The following snippet does the same thing, but gives you more granular control. A common use case is copying sensitive content, such as password. ClipEntry describes an item on the clipboard. It contains a ClipData object that describes data on the clipboard. ClipData.newPlainText() method is a convenience method to create a ClipData object from a String object. You can set the created ClipEntry object to clipboard by calling the setClip() method over the ClipboardManager object.

You can access the text copied to the clipboard by calling getText() method over the ClipboardManager. Its getText() method returns an AnnotatedString object when a text is copied in the clipboard. The following snippet appends text in the clipboard to the text in the TextField.

Users love images, videos, and other expressive content. Your app can enable the user to copy rich content with ClipboardManager and ClipEntry. The contentReceiver modifier helps you to implement pasting rich content.

Your app can't copy rich content directly to the clipboard. Instead, your app passes a URI object to the clipboard and provides access to the content with a ContentProvider. The following code snippet shows how to copy a JPEG image to clipboard. Refer to Copy data streams for details.

With the contentReceiver modifier, you can handle pasting rich content to BasicTextField in the modified component. The following code snippet adds the pasted URI of an image data to a list of Uri objects.

The contentReceiver modifier takes a ReceiveContentListener object as its argument and calls onReceive method of the passed object when the user pastes data to the BasicTextField inside the modified component.

A TransferableContent object is passed to the onReceive method, which describes the data that can be transferred between apps by pasting in this case. You can access the ClipEntry object by referring to the clipEntry attribute.

A ClipEntry object can have several ClipData.Item objects when the user selects several images and copies them to the clipboard for example. You should mark consumed or ignored for each ClipData.Item object, and return a TransferableContent containing the ignored ClipData.Item objects so that the closest ancestor contentReceiver modifier can receive it.

The TransferableContent.hasMediaType() method can help you determine whether the TransferableContent object can provide an item with the media type. For example, the following method call returns true if the TransferableContent object can provide an image.

You can copy complex data to the clipboard in the same manner you do for the rich content. Refer to Use content providers to copy complex data for details.

You can also handle the pastes of complex data in the same manner for the rich content. You can receive a URI of the pasted data. The actual data can be retrieved from a ContentProvider. Refer to Retrieve data from the provider for more information.

Users expect feedback when they copy content to the clipboard, so in addition to the framework that powers copy and paste, Android shows a default UI to users when they copy in Android 13 (API level 33) and higher. Due to this feature, there is a risk of duplicate notification. You can learn more about this edge case in Avoid duplicate notifications.

Manually provide feedback to users when copying in Android 12L (API level 32) and lower. See the recommendation.

If you choose to have your app let the user copy sensitive content to clipboard, such as passwords, your app must let the system know so that the system can avoid displaying the copied sensitive content in the UI (figure 2).

You must add a flag to ClipDescription in ClipData before calling setClip() method over the ClipboardManager object:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (swift):
```swift
val textFieldState = rememberTextFieldState()

Column {
    Card {
        SelectionContainer {
            Text("You can copy this text")
        }
    }
    BasicTextField(state = textFieldState)
}
```

Example 2 (swift):
```swift
// Retrieve a ClipboardManager object
val clipboardManager = LocalClipboardManager.current

Button(
    onClick = {
        // Copy "Hello, clipboard" to the clipboard
        clipboardManager.setText("Hello, clipboard")
    }
) {
   Text("Click to copy a text")
}
```

Example 3 (swift):
```swift
// Retrieve a ClipboardManager object
val clipboardManager = LocalClipboardManager.current

Button(
    onClick = {
        val clipData = ClipData.newPlainText("plain text", "Hello, clipboard")
        val clipEntry = ClipEntry(clipData)
        clipboardManager.setClip(clipEntry)
    }
) {
   Text("Click to copy a text")
}
```

Example 4 (swift):
```swift
var textFieldState = rememberTextFieldState()

Column {
    TextField(state = textFieldState)

    Button(
        onClick = {
            // The getText method returns an AnnotatedString object or null
            val annotatedString = clipboardManager.getText()
            if(annotatedString != null) {
                // The pasted text is placed on the tail of the TextField
                textFieldState.edit {
                    append(text.toString())
                }
            }
        }
    ) {
        Text("Click to paste the text in the clipboard")
    }
}
```

---

## Stylus input in text fields Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/stylus-input-in-text-fields

**Contents:**
- Stylus input in text fields Stay organized with collections Save and categorize content based on your preferences.
  - Kotlin
  - Groovy
- TextField
- Input delegation
  - Stylus input delegation
- Testing
- Additional resources

The Jetpack androidx.compose.material3 library enables users to write into any TextField component in any app using a stylus.

To enable stylus input by default, add the library dependency to your app's build.gradle file:

Stylus handwriting is enabled for all TextField components by default on Android 14 and higher and the androidx.compose.foundation:foundation:1.7.0 dependency. Handwriting mode is started for a TextField when a stylus motion event is detected within the handwriting bounds of the component.

The handwriting bounds include 40 dp of vertical padding and 10 dp of horizontal padding around the input field.

Stylus handwriting is not supported for TextField fields when the input method editor is requested with KeyboardType.Password.

Apps can display placeholder UI elements that appear to be text input fields but are actually just static UI elements with no text input capability. Search fields are a common example. Tapping the static UI element triggers a transition to a new UI that contains a functional text input field focused for input.

Use the handwriting delegation APIs to support stylus handwriting input for placeholder input fields (see handwritingDetector and handwritingHandler). The placeholder UI element is configured to delegate handwriting to a functional input field. For an example implementation, see HandwritingDetectorSample.kt.

Stylus handwriting mode starts when the functional input field gains focus and creates an InputConnection.

Stylus handwriting is supported on Android 14 and higher devices with a compatible stylus input device and an input method editor (IME) that supports the Android 14 stylus handwriting APIs.

If you don't have a stylus input device, simulate stylus input on any device with root access (including emulators) using the following Android Debug Bridge (adb) commands:

Use the Gboard beta for testing if you are using a device that doesn't support stylus.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (typescript):
```typescript
// Android 14
adb shell setprop persist.debug.input.simulate_stylus_with_touch true && adb shell stop && adb shell start

// Android 15 and higher
// Property takes effect after screen reconfiguration such as orientation change.
adb shell setprop debug.input.simulate_stylus_with_touch true
```

---

## Draw a stroke Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-draw-stroke

**Contents:**
- Draw a stroke Stay organized with collections Save and categorize content based on your preferences.

To help you author strokes in an idiomatic Compose way, the Ink API provides Compose interoperability modules for authoring, brush, and geometry.

To draw a stroke in Compose, use the InProgressStrokes composable that requires a default brush instance, a way to override the default brush, and a callback that handles finished strokes.

Handle finished strokes

When wet strokes become dry, they are passed to the application through the onStrokesFinished callback argument of InProgressStrokes.

Your app must pass the finished strokes to another Composable within the same UI thread to commit them to the screen.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

**Examples:**

Example 1 (unknown):
```unknown
InProgressStrokes(
  defaultBrush = currentBrush,
  nextBrush = onGetNextBrush,
  onStrokesFinished = onStrokesFinished,
)
```

Example 2 (typescript):
```typescript
@Composable
fun DrawingScreen(
  finishedStrokes: List<Strokes>,
  onStrokesFinished: (List<Stroke>) -> Unit,
  currentBrush: Brush,
  onGetNextBrush: () -> Brush,
  modifier: Modifier = Modifier
) {
  val canvasStrokeRenderer = remember { CanvasStrokeRenderer.create() }

  Box(modifier = Modifier.fillMaxSize()) {
      // The Canvas for drawing the permanent, dry strokes.
      Canvas(modifier = Modifier.fillMaxSize()) {
          finishedStrokes.forEach { stroke ->
              canvasStrokeRenderer.draw(
                  stroke = stroke,
                  canvas = this,
                  strokeToScreenTransform = Matrix()
              )
          }
      }

      //The wet ink layer for live drawing.
      // The InProgressStrokes composable for the wet ink layer goes here.
  }
}
```

---

## Modules Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-modules

**Contents:**
- Modules Stay organized with collections Save and categorize content based on your preferences.
- Strokes
- Geometry
- Brush
- Authoring
- Rendering
- Storage

Ink API is modularized, so you can use only what you need.

The strokes module serves as the foundation of the Ink API. Key data types within this module are:

The Geometry module supports geometric operations on primitive shapes (using dedicated classes like Box and Vec), as well as arbitrary shapes (using PartitionedMesh), including intersection detection and transformation. PartitionedMesh can also hold additional data to support rendering.

The brush module defines the style of strokes. It consists of two main parts:

The Compose Authoring module lets you capture user touch input and render it as low-latency strokes on the screen in real time. This is achieved through the InProgressStrokes composable, which processes motion events and displays the strokes as they are drawn.

Once a stroke is completed, the composable notifies the client application using an InProgressStrokesFinishedListener callback. This allows the application to retrieve the finished strokes for rendering or storage.

In Compose, InProgressStrokes takes this callback in the onStrokesFinished parameter. Pass the finished strokes to another composable to commit them to the screen using the rendering module.

The Rendering module simplifies drawing ink strokes onto an Android Canvas. It provides CanvasStrokeRenderer for Compose and ViewStrokeRenderer for view-based layouts. These renderers optimize rendering performance and help deliver high-quality visuals, including antialiasing.

To render strokes, call the create() method to get a CanvasStrokeRenderer instance, and then call the draw() method to render either finished (Stroke) or in-progress (InProgressStroke) strokes onto a Canvas.

You can transform the canvas when you draw a stroke. Examples include panning, zooming, and rotating. To render the stroke correctly, you must also pass the canvas transform to CanvasStrokeRenderer.draw.

To avoid tracking the canvas transform separately, use ViewStrokeRenderer instead.

The storage module provides utilities for efficiently serializing and deserializing stroke data, primarily focusing on StrokeInputBatch.

The module uses protocol buffers and optimized delta compression techniques, resulting in significant storage savings compared to naive methods.

The storage module simplifies saving, loading, and sharing strokes.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

---

## Pointer input in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input

**Contents:**
- Pointer input in Compose Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

Compose provides a variety of APIs to help you detect gestures that are generated from user interactions. The APIs cover a wide range of use cases:

Some of them are high-level and designed to cover the most commonly used gestures. For example, the clickable modifier allows easy detection of a click, and it also provides accessibility features and displays visual indicators when tapped (such as ripples).

There are also less commonly used gesture detectors that offer more flexibility on a lower level, like PointerInputScope.detectTapGestures or PointerInputScope.detectDragGestures but don't include the extra features.

Learn more about pointer input on the following pages:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Change focus behavior Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/focus/change-focus-behavior

**Contents:**
- Change focus behavior Stay organized with collections Save and categorize content based on your preferences.
- Provide coherent navigation with focus groups
- Making a composable focusable
- Making a composable unfocusable
- Request keyboard focus with FocusRequester
- Capture and release focus
- Precedence of focus modifiers
- Redirect focus upon entry or exit
- Change focus advancing direction
- Recommended for you

It is sometimes necessary to override the default focus behavior of the elements on your screen. For example, you might want to group composables, prevent focus on a certain composable, explicitly request focus on one, capture or release focus, or redirect focus on entry or exit. This section describes how to change focus behavior when the defaults aren't what you need.

Sometimes, Jetpack Compose doesn't immediately guess the correct next item for tabbed navigation, especially when complex parent Composables like tabs and lists come into play.

While focus search usually follows the declaration order of the Composables, this is impossible in some cases, like when one of the Composables in the hierarchy is a horizontal scrollable that is not fully visible. This is shown in the example below.

Jetpack Compose may decide to focus the next item closest to the start of the screen, as shown below, rather than continuing on the path you expect for one-directional navigation:

In this example, it is clear that the developers did not intend for the focus to jump from the Chocolates tab to the first image below, and then back up to the Pastries tab. Instead, they wanted the focus to continue on the tabs until the last tab, and then focus on the inner content:

In situations where it's important that a group of composables gains focus sequentially, like in the Tab row from the previous example, you need to wrap the Composable in a parent that has the focusGroup() modifier:

LazyVerticalGrid(columns = GridCells.Fixed(4)) { item(span = { GridItemSpan(maxLineSpan) }) { Row(modifier = Modifier.focusGroup()) { FilterChipA() FilterChipB() FilterChipC() } } items(chocolates) { SweetsCard(sweets = it) } }FocusSnippets.kt

Bi-directional navigation looks for the closest composable for the given direction— if an element from another group is closer than a non-fully visible item in the current group, navigation picks the closest one. To avoid this behavior, you can apply the focusGroup() modifier.

FocusGroup makes a whole group appear like a single entity in terms of focus, but the group itself will not get the focus— instead, the closest child will gain focus instead. In this way, navigation knows to go to the non-fully visible item before leaving the group.

In this case, the three instances of FilterChip will be focused before the SweetsCard items, even when the SweetsCards are completely visible to the user and some FilterChip might be hidden. This happens because the focusGroup modifier tells the focus manager to adjust the order in which items are focused so that navigation is easier and more coherent with the UI.

Without the focusGroup modifier, if the FilterChipC was not visible, focus navigation would pick it up last. However, adding such a modifier makes it not only discoverable, but it will also acquire focus right after FilterChipB, as users would expect.

Some composables are focusable by design, such as a Button or a composable with the clickable modifier attached to it. If you want to specifically add focusable behavior to a composable, you use the focusable modifier:

var color by remember { mutableStateOf(Green) } Box( Modifier .background(color) .onFocusChanged { color = if (it.isFocused) Blue else Green } .focusable() ) { Text("Focusable 1") }FocusSnippets.kt

There might be situations in which some of your elements should not participate in the focus. In these rare occasions, you can leverage the canFocus property to exclude a Composable from being focusable.

var checked by remember { mutableStateOf(false) } Switch( checked = checked, onCheckedChange = { checked = it }, // Prevent component from being focused modifier = Modifier .focusProperties { canFocus = false } )FocusSnippets.kt

In some cases, you might want to explicitly request focus as a response to a user interaction. For example, you might ask a user if they want to restart filling in a form, and if they press "yes" you want to refocus the first field of that form.

The first thing to do is associate a FocusRequester object with the composable you want to move the keyboard focus to. In the following code snippet, a FocusRequester object is associated with a TextField by setting a modifier called Modifier.focusRequester:

val focusRequester = remember { FocusRequester() } var text by remember { mutableStateOf("") } TextField( value = text, onValueChange = { text = it }, modifier = Modifier.focusRequester(focusRequester) )FocusSnippets.kt

You can call FocusRequester's requestFocus method to send actual focus requests. You should invoke this method outside of a Composable context (otherwise, it is re-executed at every recomposition). The following snippet shows how to request the system to move the keyboard focus when the button is clicked:

val focusRequester = remember { FocusRequester() } var text by remember { mutableStateOf("") } TextField( value = text, onValueChange = { text = it }, modifier = Modifier.focusRequester(focusRequester) ) Button(onClick = { focusRequester.requestFocus() }) { Text("Request focus on TextField") }FocusSnippets.kt

You can leverage focus to guide your users to provide the right data your app needs to perform its task— for instance, getting a valid email address or phone number. Although error states inform your users about what is going on, you might need the field with erroneous information to stay focused until it gets fixed.

In order to capture the focus, you can invoke the captureFocus() method, and release it afterwards with the freeFocus() method instead, as in the following example:

val textField = remember { FocusRequester() } TextField( value = text, onValueChange = { text = it if (it.length > 3) { textField.captureFocus() } else { textField.freeFocus() } }, modifier = Modifier.focusRequester(textField) )FocusSnippets.kt

Modifiers can be seen as elements that only have one child, so when you queue them, each Modifier on the left (or top) wraps the Modifier that follows on the right (or below). This means that the second Modifier is contained inside the first one, so that when declaring two focusProperties, only the topmost one works, as the following ones are contained in the topmost.

To clarify the concept more, see the following code:

Modifier .focusProperties { right = item1 } .focusProperties { right = item2 } .focusable()FocusSnippets.kt

In this case, the focusProperties indicating item2 as the right focus will not be used, as it is contained in the preceding one; thus, item1 will be the one used.

Leveraging this approach, a parent can also reset the behavior to default by using FocusRequester.Default:

Modifier .focusProperties { right = Default } .focusProperties { right = item1 } .focusProperties { right = item2 } .focusable()FocusSnippets.kt

The parent does not have to be part of the same modifier chain. A parent composable can overwrite a focus property of a child composable. For example, consider this FancyButton that makes the button not focusable:

@Composable fun FancyButton(modifier: Modifier = Modifier) { Row(modifier.focusProperties { canFocus = false }) { Text("Click me") Button(onClick = { }) { Text("OK") } } }FocusSnippets.kt

A user can make this button focusable again by setting canFocus to true:

FancyButton(Modifier.focusProperties { canFocus = true })FocusSnippets.kt

Like every Modifier, focus-related ones behave differently based on the order you declare them. For instance, code like the following makes the Box focusable, but the FocusRequester isn't associated with this focusable since it is declared after the focusable.

Box( Modifier .focusable() .focusRequester(Default) .onFocusChanged {} )FocusSnippets.kt

It's important to remember that a focusRequester is associated with the first focusable below it in the hierarchy, so this focusRequester points to the first focusable child. In case none is available, it won't point to anything. However, since the Box is focusable (thanks to the focusable() modifier), you can navigate to it using two-directional navigation.

As another example, either of the following would work, as the onFocusChanged() modifier refers to the first focusable element that appears after the focusable() or focusTarget() modifiers.

Sometimes, you need to provide a very specific kind of navigation, like the one shown in the animation below:

Before we dive into how to create this, it's important to understand the default behavior of the focus search. Without any modification, once the focus search reaches the Clickable 3 item, pressing DOWN on the D-Pad (or the equivalent arrow key) would move the focus to whatever is displayed below the Column, exiting the group and ignoring the one on the right. If there are no focusable items available, the focus doesn't move anywhere, but stays on Clickable 3.

To alter this behavior and provide the intended navigation, you can leverage the focusProperties modifier, which helps you manage what happens when the focus search enters or exits the Composable:

val otherComposable = remember { FocusRequester() } Modifier.focusProperties { exit = { focusDirection -> when (focusDirection) { Right -> Cancel Down -> otherComposable else -> Default } } }FocusSnippets.kt

It is possible to direct the focus to a specific Composable whenever it enters or exits a certain portion of the hierarchy— for example, when your UI has two columns and you want to make sure that whenever the first one is processed, focus switches to the second:

In this gif, once the focus reaches the Clickable 3 Composable in Column 1, the next item being focused is Clickable 4 in another Column. This behavior can be achieved by combining the focusDirection with the enter and exit values inside the focusProperties modifier. They both need a lambda that takes as a parameter the direction from which the focus is coming from and returns a FocusRequester. This lambda can behave in three different ways: returning FocusRequester.Cancel stops the focus from continuing, while FocusRequester.Default does not alter its behavior. Providing instead the FocusRequester attached to another Composable makes the focus jump to that specific Composable.

To advance the focus to the next item or towards a precise direction, you can leverage the onPreviewKey modifier and imply the LocalFocusManager to advance the focus with the moveFocus Modifier.

The following example shows the default behavior of the focus mechanism: when a tab keypress is detected, the focus advances to the next element in the focus list. While this is not something you usually need to configure, it's important to know the inner workings of the system to be able to change the default behavior.

val focusManager = LocalFocusManager.current var text by remember { mutableStateOf("") } TextField( value = text, onValueChange = { text = it }, modifier = Modifier.onPreviewKeyEvent { when { KeyEventType.KeyUp == it.type && Key.Tab == it.key -> { focusManager.moveFocus(FocusDirection.Next) true } else -> false } } )FocusSnippets.kt

In this sample, the focusManager.moveFocus() function advances the focus to the item specified, or to the direction implied in the function parameter.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## React to focus Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/focus/react-to-focus

**Contents:**
- React to focus Stay organized with collections Save and categorize content based on your preferences.
- Provide visual cues for easier focus visualization
  - Implement advanced visual cues
- Understand the state of the focus
- Recommended for you

While all the focusable elements from Material Theme already have a focus style that matches the theme, you might need to add some visual elements to make the focused element easier to spot. A good solution would be to change the border of your element with a color that has a good contrast with the background:

var color by remember { mutableStateOf(Color.White) } Card( modifier = Modifier .onFocusChanged { color = if (it.isFocused) Red else White } .border(5.dp, color) ) {}FocusSnippets.kt

In this example, remember is used to store the color of the border across recompositions, and the outline of the element is updated every time the element gains or loses focus.

With Jetpack Compose, you can also create more sophisticated and advanced visual cues that match better with your UI.

Generally, every time a state of the focus changes, a FocusEvent is fired up the tree, and the parents of a focusable() modifier can listen to it using the onFocusChanged() modifier.

If you need to know the state of the focus,you can use these APIs in conjunction with the onFocusChanged modifier:

These fields are shown below:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (css):
```css
Modifier.onFocusChanged {
    val isFocused = it.isFocused
    val hasFocus = it.hasFocus
    val isCaptured= it.isCaptured
}
```

---

## Brush APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-brush-apis

**Contents:**
- Brush APIs Stay organized with collections Save and categorize content based on your preferences.
- Create a brush
- Modify brush properties
- Custom Brushes

The Brush APIs provide you with the tools to define the visual style of your strokes. You can create brushes with different colors, sizes, and families to achieve a variety of looks.

To create a brush, use the Compose Brush companion methods with named arguments such as Brush.Companion.createWithComposeColor. This class lets you set the following properties:

You can create a copy of an existing brush using the copyWithComposeColor() method, which lets you change any of the brush's properties.

While StockBrushes provides a versatile set of common brushes, Ink API also offers an advanced path for creating entirely new brush behaviors for unique artistic effects or to replicate specific existing brushes for backward compatibility.

A custom BrushFamily is loaded from its serialized format. The required format is the gzipped binary encoding of the BrushFamily protocol buffer. This lets you load and use custom brush files today. Once deserialized, the custom BrushFamily can be used to create a new Brush with a specific color and size, just like any of the StockBrushes families.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

**Examples:**

Example 1 (unknown):
```unknown
val brush = Brush.createWithComposeColor(
  family = StockBrushes.pressure(),
  colorIntArgb = Color.Black,
  size = 5F,
  epsilon = 0.1F
)
```

Example 2 (unknown):
```unknown
val redBrush = Brush.createWithComposeColor(
  family = StockBrushes.pressurePen(),
  colorIntArgb = Color.RED,
  size = 5F,
  epsilon = 0.1F
)

val blueBrush = redBrush.copyWithComposeColor(color = Color.BLUE)
```

Example 3 (php):
```php
class CustomBrushes(val context: Context) {

  private const val TAG = "CustomBrushes"

  val brushes by lazy { loadCustomBrushes(context) }

  @OptIn(ExperimentalInkCustomBrushApi::class)
  private fun loadCustomBrushes(): List<CustomBrush> {
    val brushFiles = mapOf(
        "Calligraphy" to (R.raw.calligraphy to R.drawable.draw_24px),
        "Flag Banner" to (R.raw.flag_banner to R.drawable.flag_24px),
        "Graffiti" to (R.raw.graffiti to R.drawable.format_paint_24px),
    // ...
    )

    val loadedBrushes = brushFiles.mapNotNull { (name, pair) ->
      val (resourceId, icon) = pair
      val brushFamily = context.resources.openRawResource(resourceId).use
      { inputStream ->
          BrushFamily.decode(inputStream)
      }
      CustomBrush(name, icon, brushFamily.copy(clientBrushFamilyId = name))     
    }
    return loadedBrushes
  }
}

data class CustomBrush(
  val name: String,
  val icon: Int,
  val brushFamily: BrushFamily
)
```

---

## Handle keyboard actions Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/keyboard-input/commands

**Contents:**
- Handle keyboard actions Stay organized with collections Save and categorize content based on your preferences.
- Default keyboard shortcuts
- Key events
  - Modifier keys
  - Spacebar and Enter key click events
  - Unconsumed key events
- onKeyPreviewEvent modifier
- Additional resources

When the user gives focus to an editable text component, such as a TextField, and the device has a hardware keyboard attached, all input is handled by the system. You can provide keyboard shortcuts by handling key events.

The following keyboard shortcuts are available out of the box.

In Compose, you can handle an individual keystroke with the onKeyEvent modifier. The modifier accepts a lambda that's called when the modified component receives a key event. A key event is described as a KeyEvent object. You can get the information of each key event by referring to the object in the lambda passed to the onKeyEvent modifier.

A keystroke sends two key events. One is triggered when the user presses the key; the other is triggered when the key is released. You can distinguish the two key events by referring to the type attribute of the KeyEvent object.

The return value of the onKeyEvent lambda indicates whether the key event is handled or not. Return true if your app handles the key event, which stops propagation of the event.

The following snippet shows how to call a doSomething() function when the user releases the S key on the Box component:

A KeyEvent object has the following attributes which indicate whether modifier keys are pressed or not:

Be specific in describing the key events your app handles. The following snippet calls a doSomething() function only if the user releases just the S key. If the user presses any modifier key, such as the Shift key, the app does not call the function.

The Spacebar and Enter key trigger click events as well. For example, users can toggle (play or pause) media playback with the Spacebar or the Enter key by handling click events as follows:

The clickable modifier intercepts key events and calls the onClick() callback when the Spacebar or Enter key is pressed. That's why the togglePausePlay() function is called by pressing the Spacebar or Enter key in the snippet.

Unconsumed key events are propagated from the component where the event occurred to the enclosing outer component. In the example below, the InnerComponent consumes key events when the S key is released, and so the OuterComponent does not receive any key events triggered by releasing the S key. That's why the actionB() function is never called.

Other key events on InnerComponent, such as releasing the D key, can be handled by the OuterComponent. The actionC() function is called because the key event for releasing the D key is propagated to the OuterComponent.

In some use cases, you want to intercept a key event before it triggers the default action. Adding custom shortcuts to a TextField is a typical one. The following snippet enables users to move to the next focusable component by pressing the tab key.

By default, the TextField component adds a tab character every time users press the Tab key, even if the key event is handled with the onKeyEvent modifier. To move the keyboard focus without adding any tab characters, handle the key event before triggering the actions associated with the key event, as in the snippet. The onKeyPreviewEvent() lambda intercepts the key event by returning true.

The parent component can intercept the key event happening on its children. In the following snippet, the previewSKey() function is called when users press the S key, instead of calling the actionForPreview() function.

The onPreviewKeyEvent() lambda for the Box component is not triggered when users press the Tab key either. The onPreviewKeyEvent() lambda is called on the parent component first, then onPreviewKeyEvent() in the child component is called. You can implement screen-wide keyboard shortcuts by utilizing this behavior.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-10-09 UTC.

**Examples:**

Example 1 (swift):
```swift
Box(
    modifier = Modifier.focusable().onKeyEvent {
        if(
            it.type == KeyEventType.KeyUp &&
            it.key == Key.S
        ) {
            doSomething()
            true
        } else {
            false
        }
    }
)  {
    Text("Press S key")
}
```

Example 2 (swift):
```swift
Box(
  modifier = Modifier.focusable().onKeyEvent{
     if(
       it.type == KeyEventType.KeyUp &&
       it.key == Key.S &&
       !it.isAltPressed &&
       !it.isCtrlPressed &&
       !it.isMetaPressed &&
       !it.isShiftPressed
     ) {
       doSomething()
       true
     } else {
       false
     }
  }
)  {
    Text("Press S key with a modifier key")
}
```

Example 3 (css):
```css
MoviePlayer(
   modifier = Modifier.clickable { togglePausePlay() }
)
```

Example 4 (php):
```php
OuterComponent(
    modifier = Modifier.onKeyEvent {
        when {
           it.type == KeyEventType.KeyUp && it.key == Key.S -> {
               actionB() // This function is never called.
               true
           }
           it.type == KeyEventType.KeyUp && it.key == Key.D -> {
               actionC()
               true
           }
           else -> false
        }
    }
) {
    InnerComponent(
        modifier = Modifier.onKeyEvent {
            if(it.type == KeyEventType.KeyUp && it.key == Key.S) {
                actionA()
                true
            } else {
                false
            }
        }
    )
}
```

---

## Nested scrolling Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/nested-scroll

**Contents:**
- Nested scrolling Stay organized with collections Save and categorize content based on your preferences.
- Automatic nested scrolling
- Using the nestedScroll modifier
- Nested scrolling cycle
  - Phases of nested scrolling cycle
  - Participate in the nested scrolling cycle
- Resize an image on scroll
  - Resize an image based on scroll position
  - Key points about the code
  - Result

Nested scrolling is a system where multiple scrolling components contained within each other work together by reacting to a single scroll gesture and communicating their scrolling deltas (changes).

The nested scrolling system allows coordination between components that are scrollable and hierarchically linked (most often by sharing the same parent). This system links scrolling containers and allows interaction with the scrolling deltas that are being propagated and shared between.

Compose provides multiple ways of handling nested scrolling between composables. A typical example of nested scrolling is a list inside another list, and a more complex case is a collapsing toolbar.

Simple nested scrolling requires no action on your part. Gestures that initiate a scrolling action are propagated from children to parents automatically, such that when the child can't scroll any further, the gesture is handled by its parent element.

Automatic nested scrolling is supported and provided out of the box by some of Compose's components and modifiers: verticalScroll, horizontalScroll, scrollable, Lazy APIs and TextField. This means that when the user scrolls an inner child of nested components, the previous modifiers propagate the scrolling deltas to the parents that have nested scrolling support.

The following example shows elements with a verticalScroll modifier applied to them inside a container that also has a verticalScroll modifier applied to it.

@Composable private fun AutomaticNestedScroll() { val gradient = Brush.verticalGradient(0f to Color.Gray, 1000f to Color.White) Box( modifier = Modifier .background(Color.LightGray) .verticalScroll(rememberScrollState()) .padding(32.dp) ) { Column { repeat(6) { Box( modifier = Modifier .height(128.dp) .verticalScroll(rememberScrollState()) ) { Text( "Scroll here", modifier = Modifier .border(12.dp, Color.DarkGray) .background(brush = gradient) .padding(24.dp) .height(150.dp) ) } } } } }GesturesSnippets.kt

If you need to create an advanced coordinated scroll between multiple elements, the nestedScroll modifier gives you more flexibility by defining a nested scrolling hierarchy. As mentioned in the previous section, some components have built-in nested scroll support. However, for composables that aren't scrollable automatically, such as Box or Column, scroll deltas on such components won't propagate in the nested scroll system and the deltas won't reach the NestedScrollConnection nor the parent component. To resolve this, you can use nestedScroll to confer such support to other components, including custom components.

Nested scroll cycle is the flow of scroll deltas that are dispatched up and down the hierarchy tree through all components (or nodes) that are part of the nested scrolling system, for example by using scrollable components and modifiers, or nestedScroll.

When a trigger event (for example, a gesture) is detected by a scrollable component, before the actual scrolling action is even triggered, the generated deltas are sent to the nested scroll system and go through three phases: pre-scroll, node consumption, and post-scroll.

In the first, pre-scroll phase, the component that received the trigger event deltas will dispatch those events up, through the hierarchy tree, to the topmost parent. The delta events will then bubble down, meaning that deltas will be propagated from the root-most parent down towards the child that started the nested scroll cycle.

This gives the nested scroll parents (composables using nestedScroll or scrollable modifiers) the opportunity to do something with the delta before the node itself can consume it.

In the node consumption phase, the node itself will use whatever delta was not used by its parents. This is when the scrolling movement is actually done and is visible.

During this phase, the child may choose to consume all or part of the remaining scroll. Anything left will be sent back up to go through the post-scroll phase.

Finally, in the post-scroll phase, anything that the node itself didn't consume will be sent up again to its ancestors for consumption.

The post-scroll phase works in a similar way as the pre-scroll phase, where any of the parents may choose to consume or not.

Similarly to scroll, when a drag gesture finishes, the user's intention may be translated into a velocity that is used to fling (scroll using an animation) the scrollable container. The fling is also part of the nested scroll cycle, and the velocities generated by the drag event go through similar phases: pre-fling, node consumption, and post-fling. Note that fling animation is only associated with touch gesture and won't be triggered by other events, such as a11y or hardware scroll.

Participation in the cycle means intercepting, consuming, and reporting the consumption of deltas along the hierarchy. Compose provides a set of tools to influence how the nested scrolling system works and how to interact directly with it, for example when you need to do something with the scroll deltas before a scrollable component even starts scrolling.

If the nested scroll cycle is a system acting on a chain of nodes, the nestedScroll modifier is a way of intercepting and inserting into these changes, and influencing the data (scroll deltas) that are propagated in the chain. This modifier can be placed anywhere in the hierarchy, and it communicates with nested scroll modifier instances up the tree so it can share information through this channel. The building blocks of this modifier are NestedScrollConnection and NestedScrollDispatcher.

NestedScrollConnection provides a way to respond to the phases of the nested scroll cycle and influence the nested scroll system. It's composed of four callback methods, each representing one of the consumption phases: pre/post-scroll and pre/post-fling:

val nestedScrollConnection = object : NestedScrollConnection { override fun onPreScroll(available: Offset, source: NestedScrollSource): Offset { println("Received onPreScroll callback.") return Offset.Zero } override fun onPostScroll( consumed: Offset, available: Offset, source: NestedScrollSource ): Offset { println("Received onPostScroll callback.") return Offset.Zero } }GesturesSnippets.kt

Each callback also gives information about the delta being propagated: available delta for that particular phase, and consumed delta consumed in the previous phases. If at any point you want to stop propagating deltas up the hierarchy, you can use the nested scroll connection to do so:

val disabledNestedScrollConnection = remember { object : NestedScrollConnection { override fun onPostScroll( consumed: Offset, available: Offset, source: NestedScrollSource ): Offset { return if (source == NestedScrollSource.SideEffect) { available } else { Offset.Zero } } } }GesturesSnippets.kt

All callbacks provide information on the NestedScrollSource type.

NestedScrollDispatcher initializes the nested scroll cycle. Using a dispatcher and calling its methods triggers the cycle. Scrollable containers have a built-in dispatcher that sends deltas captured during gestures into the system. For this reason, most use cases of customizing nested scrolling involve using NestedScrollConnection instead of a dispatcher, to react to already existing deltas rather than send new ones. See NestedScrollDispatcherSample for more usages.

As the user scrolls, you can create a dynamic visual effect where the image changes size based on the scroll position.

This snippet demonstrates resizing an image within a LazyColumn based on vertical scroll position. The image shrinks as the user scrolls down, and grows as they scroll up, remaining within the defined minimum and maximum size bounds:

@Composable fun ImageResizeOnScrollExample( modifier: Modifier = Modifier, maxImageSize: Dp = 300.dp, minImageSize: Dp = 100.dp ) { var currentImageSize by remember { mutableStateOf(maxImageSize) } var imageScale by remember { mutableFloatStateOf(1f) } val nestedScrollConnection = remember { object : NestedScrollConnection { override fun onPreScroll(available: Offset, source: NestedScrollSource): Offset { // Calculate the change in image size based on scroll delta val delta = available.y val newImageSize = currentImageSize + delta.dp val previousImageSize = currentImageSize // Constrain the image size within the allowed bounds currentImageSize = newImageSize.coerceIn(minImageSize, maxImageSize) val consumed = currentImageSize - previousImageSize // Calculate the scale for the image imageScale = currentImageSize / maxImageSize // Return the consumed scroll amount return Offset(0f, consumed.value) } } } Box(Modifier.nestedScroll(nestedScrollConnection)) { LazyColumn( Modifier .fillMaxWidth() .padding(15.dp) .offset { IntOffset(0, currentImageSize.roundToPx()) } ) { // Placeholder list items items(100, key = { it }) { Text( text = "Item: $it", style = MaterialTheme.typography.bodyLarge ) } } Image( painter = ColorPainter(Color.Red), contentDescription = "Red color image", Modifier .size(maxImageSize) .align(Alignment.TopCenter) .graphicsLayer { scaleX = imageScale scaleY = imageScale // Center the image vertically as it scales translationY = -(maxImageSize.toPx() - currentImageSize.toPx()) / 2f } ) } }AnimateImageSnippets.kt

The preceding snippet results in a scaling image effect on scroll:

When you try to nest scrollable View elements in scrollable composables, or the other way around, you might encounter issues. Most noticeable ones would happen when you scroll the child and reach its start or end bounds and expect the parent to take the scrolling over. However, this expected behaviour either might not happen or might not work as expected.

This issue is a result of the expectations built in scrollable composables. Scrollable composables have a "nested-scroll-by-default" rule, which means that any scrollable container must participate in the nested scroll chain, both as a parent via NestedScrollConnection, and as a child via NestedScrollDispatcher. The child would then drive a nested scroll for the parent when the child is at the bound. As an example, this rule allows Compose Pager and Compose LazyRow to work well together. However, when interoperability scrolling is being done with ViewPager2 or RecyclerView, since these don’t implement NestedScrollingParent3, the continuous scrolling from child to parent is not possible.

To enable nested scrolling interop API between scrollable View elements and scrollable composables, nested in both directions, you can use the nested scrolling interop API to mitigate these issues, in the following scenarios.

A cooperating parent View is one that already implements NestedScrollingParent3 and therefore is able to receive scrolling deltas from a cooperating nested child composable. ComposeView would act as a child in this case and would need to (indirectly) implement NestedScrollingChild3. One example of a cooperating parent is androidx.coordinatorlayout.widget.CoordinatorLayout.

If you need nested scrolling interoperability between scrollable View parent containers and nested scrollable child composables, you can use rememberNestedScrollInteropConnection().

rememberNestedScrollInteropConnection() allows and remembers the NestedScrollConnection that enables nested scroll interoperability between a View parent that implements NestedScrollingParent3 and a Compose child. This should be used in conjunction with a nestedScroll modifier. Since nested scrolling is enabled by default on the Compose side, you can use this connection to enable both nested scroll on the View side and add the necessary glue logic between Views and composables.

A frequent use case is using CoordinatorLayout, CollapsingToolbarLayout and a child composable, shown in this example:

In your Activity or Fragment, you need to set up your child composable and the required NestedScrollConnection:

open class MainActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContentView(R.layout.activity_main) findViewById<ComposeView>(R.id.compose_view).apply { setContent { val nestedScrollInterop = rememberNestedScrollInteropConnection() // Add the nested scroll connection to your top level @Composable element // using the nestedScroll modifier. LazyColumn(modifier = Modifier.nestedScroll(nestedScrollInterop)) { items(20) { item -> Box( modifier = Modifier .padding(16.dp) .height(56.dp) .fillMaxWidth() .background(Color.Gray), contentAlignment = Alignment.Center ) { Text(item.toString()) } } } } } } }GesturesSnippets.kt

This scenario covers the implementation of nested scrolling interop API on the Compose side - when you have a parent composable containing a child AndroidView. The AndroidView implements NestedScrollDispatcher, since it acts as a child to a Compose scrolling parent, as well as NestedScrollingParent3 , since it acts as a parent to a View scrolling child. Compose parent will then be able to receive nested scroll deltas from a nested scrollable child View.

The following example shows how you can achieve nested scrolling interop in this scenario, along with a Compose collapsing toolbar:

This example shows how you can use the API with a scrollable modifier:

And finally, this example shows how nested scrolling interop API is used with BottomSheetDialogFragment to achieve a successful drag and dismiss behaviour:

Note that rememberNestedScrollInteropConnection() will install a NestedScrollConnection in the element you attach it to. NestedScrollConnection is responsible for transmitting the deltas from the Compose level to the View level. This enables the element to participate in nested scrolling, but it doesn't enable scrolling of elements automatically. To composables that aren't scrollable automatically, such as Box or Column, scroll deltas on such components won't propagate in the nested scroll system and the deltas won't reach the NestedScrollConnection provided by rememberNestedScrollInteropConnection(), therefore those deltas won't reach the parent View component. To resolve this, make sure you also set scrollable modifiers to these types of nested composables. You can refer to the previous section on nested scrolling for more detailed information.

A non-cooperating View is one that does not implement the necessary NestedScrolling interfaces on the View side. Note that this means that nested scrolling interoperability with these Views doesn't work out of the box. Non-cooperating Views are RecyclerView and ViewPager2.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (php):
```php
@Composable
private fun NestedScrollInteropComposeParentWithAndroidChildExample() {
    val toolbarHeightPx = with(LocalDensity.current) { ToolbarHeight.roundToPx().toFloat() }
    val toolbarOffsetHeightPx = remember { mutableStateOf(0f) }

    // Sets up the nested scroll connection between the Box composable parent
    // and the child AndroidView containing the RecyclerView
    val nestedScrollConnection = remember {
        object : NestedScrollConnection {
            override fun onPreScroll(available: Offset, source: NestedScrollSource): Offset {
                // Updates the toolbar offset based on the scroll to enable
                // collapsible behaviour
                val delta = available.y
                val newOffset = toolbarOffsetHeightPx.value + delta
                toolbarOffsetHeightPx.value = newOffset.coerceIn(-toolbarHeightPx, 0f)
                return Offset.Zero
            }
        }
    }

    Box(
        Modifier
            .fillMaxSize()
            .nestedScroll(nestedScrollConnection)
    ) {
        TopAppBar(
            modifier = Modifier
                .height(ToolbarHeight)
                .offset { IntOffset(x = 0, y = toolbarOffsetHeightPx.value.roundToInt()) }
        )

        AndroidView(
            { context ->
                LayoutInflater.from(context)
                    .inflate(R.layout.view_in_compose_nested_scroll_interop, null).apply {
                        with(findViewById<RecyclerView>(R.id.main_list)) {
                            layoutManager = LinearLayoutManager(context, VERTICAL, false)
                            adapter = NestedScrollInteropAdapter()
                        }
                    }.also {
                        // Nested scrolling interop is enabled when
                        // nested scroll is enabled for the root View
                        ViewCompat.setNestedScrollingEnabled(it, true)
                    }
            },
            // ...
        )
    }
}

private class NestedScrollInteropAdapter :
    Adapter<NestedScrollInteropAdapter.NestedScrollInteropViewHolder>() {
    val items = (1..10).map { it.toString() }

    override fun onCreateViewHolder(
        parent: ViewGroup,
        viewType: Int
    ): NestedScrollInteropViewHolder {
        return NestedScrollInteropViewHolder(
            LayoutInflater.from(parent.context)
                .inflate(R.layout.list_item, parent, false)
        )
    }

    override fun onBindViewHolder(holder: NestedScrollInteropViewHolder, position: Int) {
        // ...
    }

    class NestedScrollInteropViewHolder(view: View) : ViewHolder(view) {
        fun bind(item: String) {
            // ...
        }
    }
    // ...
}
```

Example 2 (json):
```json
@Composable
fun ViewInComposeNestedScrollInteropExample() {
    Box(
        Modifier
            .fillMaxSize()
            .scrollable(rememberScrollableState {
                // View component deltas should be reflected in Compose
                // components that participate in nested scrolling
                it
            }, Orientation.Vertical)
    ) {
        AndroidView(
            { context ->
                LayoutInflater.from(context)
                    .inflate(android.R.layout.list_item, null)
                    .apply {
                        // Nested scrolling interop is enabled when
                        // nested scroll is enabled for the root View
                        ViewCompat.setNestedScrollingEnabled(this, true)
                    }
            }
        )
    }
}
```

Example 3 (swift):
```swift
class BottomSheetFragment : BottomSheetDialogFragment() {

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View {
        val rootView: View = inflater.inflate(R.layout.fragment_bottom_sheet, container, false)

        rootView.findViewById<ComposeView>(R.id.compose_view).apply {
            setContent {
                val nestedScrollInterop = rememberNestedScrollInteropConnection()
                LazyColumn(
                    Modifier
                        .nestedScroll(nestedScrollInterop)
                        .fillMaxSize()
                ) {
                    item {
                        Text(text = "Bottom sheet title")
                    }
                    items(10) {
                        Text(
                            text = "List item number $it",
                            modifier = Modifier.fillMaxWidth()
                        )
                    }
                }
            }
            return rootView
        }
    }
}
```

---

## Handling user interactions Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/user-interactions/handling-interactions

**Contents:**
- Handling user interactions Stay organized with collections Save and categorize content based on your preferences.
- Interactions
- Interaction state
- Consume and emit Interaction
  - Consuming modifier example
  - Producing modifier example
  - Build components that consume and produce
- Work with InteractionSource
- Example: Build component with custom interaction handling
- Create and apply a reusable custom effect with Indication

User interface components give feedback to the device user by the way they respond to user interactions. Every component has its own way of responding to interactions, which helps the user know what their interactions are doing. For example, if a user touches a button on a device's touchscreen, the button is likely to change in some way, perhaps by adding a highlight color. This change lets the user know that they touched the button. If the user didn't want to do that, they'll know to drag their finger away from the button before releasing--otherwise, the button will activate.

The Compose Gestures documentation covers how Compose components handle low-level pointer event, such as pointer moves and clicks. Out of the box, Compose abstracts those low-level events into higher-level interactions–for example, a series of pointer events might add up to a button press-and-release. Understanding those higher-level abstractions can help you customize how your UI responds to the user. For example, you might want to customize how a component's appearance changes when the user interacts with it, or maybe you just want to maintain a log of those user actions. This document gives you the information you need to modify the standard UI elements, or design your own.

In many cases, you don't need to know just how your Compose component is interpreting user interactions. For example, Button relies on Modifier.clickable to figure out whether the user clicked the button. If you're adding a typical button to your app, you can define the button's onClick code, and Modifier.clickable runs that code when appropriate. That means you don't need to know whether the user tapped the screen or selected the button with a keyboard; Modifier.clickable figures out that the user performed a click, and responds by running your onClick code.

However, if you want to customize your UI component's response to user behavior, you may need to know more of what's going on under the hood. This section gives you some of that information.

When a user interacts with a UI component, the system represents their behavior by generating a number of Interaction events. For example, if a user touches a button, the button generates PressInteraction.Press. If the user lifts their finger inside the button, it generates a PressInteraction.Release, letting the button know that the click was finished. On the other hand, if the user drags their finger outside the button, then lifts their finger, the button generates PressInteraction.Cancel, to indicate that the press on the button was canceled, not completed.

These interactions are unopinionated. That is, these low-level interaction events don't intend to interpret the meaning of the user actions, or their sequence. They also don't interpret which user actions might take priority over other actions.

These interactions generally come in pairs, with a start and an end. The second interaction contains a reference to the first one. For example, if a user touches a button then lifts their finger, the touch generates a PressInteraction.Press interaction, and the release generates a PressInteraction.Release; the Release has a press property identifying the initial PressInteraction.Press.

You can see the interactions for a particular component by observing its InteractionSource. InteractionSource is built on top of Kotlin flows, so you can collect the interactions from it the same way you'd work with any other flow. For more information about this design decision, see the Illuminating Interactions blog post.

You might want to extend the built-in functionality of your components by also tracking the interactions yourself. For example, perhaps you want a button to change color when it's pressed. The simplest way to track the interactions is to observe the appropriate interaction state. InteractionSource offers a number of methods that reveal various interaction statuses as state. For example, if you want to see whether a particular button is pressed, you can call its InteractionSource.collectIsPressedAsState() method:

val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() Button( onClick = { /* do something */ }, interactionSource = interactionSource ) { Text(if (isPressed) "Pressed!" else "Not pressed") }Interactions.kt

Besides collectIsPressedAsState(), Compose also provides collectIsFocusedAsState(), collectIsDraggedAsState(), and collectIsHoveredAsState(). These methods are actually convenience methods built on top of lower-level InteractionSource APIs. In some cases, you may want to use those lower-level functions directly.

For example, suppose you need to know whether a button is being pressed, and also whether it's being dragged. If you use both collectIsPressedAsState() and collectIsDraggedAsState(), Compose does a lot of duplicate work, and there's no guarantee you'll get all the interactions in the right order. For situations like this, you might want to work directly with the InteractionSource. For more information about tracking the interactions yourself with InteractionSource, see Work with InteractionSource.

The following section describes how to consume and emit interactions with InteractionSource and MutableInteractionSource, respectively.

InteractionSource represents a read-only stream of Interactions — it is not possible to emit an Interaction to an InteractionSource. To emit Interactions, you need to use a MutableInteractionSource, which extends from InteractionSource.

Modifiers and components can consume, emit, or consume and emit Interactions. The following sections describe how to consume and emit interactions from both modifiers and components.

For a modifier that draws a border for focused state, you only need to observe Interactions, so you can accept an InteractionSource:

fun Modifier.focusBorder(interactionSource: InteractionSource): Modifier { // ... }Interactions.kt

It is clear from the function signature that this modifier is a consumer — it can consume Interactions, but cannot emit them.

For a modifier that handles hover events like Modifier.hoverable, you need to emit Interactions, and accept a MutableInteractionSource as a parameter instead:

fun Modifier.hover(interactionSource: MutableInteractionSource, enabled: Boolean): Modifier { // ... }Interactions.kt

This modifier is a producer — it can use the provided MutableInteractionSource to emit HoverInteractions when it is hovered or unhovered.

High-level components such as a Material Button act as both producers and consumers. They handle input and focus events, and also change their appearance in response to these events, such as showing a ripple or animating their elevation. As a result, they directly expose MutableInteractionSource as a parameter, so that you can provide your own remembered instance:

@Composable fun Button( onClick: () -> Unit, modifier: Modifier = Modifier, enabled: Boolean = true, // exposes MutableInteractionSource as a parameter interactionSource: MutableInteractionSource? = null, elevation: ButtonElevation? = ButtonDefaults.elevatedButtonElevation(), shape: Shape = MaterialTheme.shapes.small, border: BorderStroke? = null, colors: ButtonColors = ButtonDefaults.buttonColors(), contentPadding: PaddingValues = ButtonDefaults.ContentPadding, content: @Composable RowScope.() -> Unit ) { /* content() */ }Interactions.kt

This allows hoisting the MutableInteractionSource out of the component and observing all the Interactions produced by the component. You can use this to control the appearance of that component, or any other component in your UI.

If you are building your own interactive high level components, we recommend that you expose MutableInteractionSource as a parameter in this way. Besides following state hoisting best practices, this also makes it easy to read and control the visual state of a component in the same way that any other sort of state (such as enabled state) can be read and controlled.

Compose follows a layered architectural approach, so high-level Material components are built on top of foundational building blocks that produce the Interactions they need to control ripples and other visual effects. The foundation library provides high-level interaction modifiers such as Modifier.hoverable, Modifier.focusable, and Modifier.draggable.

To build a component that responds to hover events, you can simply use Modifier.hoverable and pass a MutableInteractionSource as a parameter. Whenever the component is hovered, it emits HoverInteractions, and you can use this to change how the component appears.

// This InteractionSource will emit hover interactions val interactionSource = remember { MutableInteractionSource() } Box( Modifier .size(100.dp) .hoverable(interactionSource = interactionSource), contentAlignment = Alignment.Center ) { Text("Hello!") }Interactions.kt

To also make this component focusable, you can add Modifier.focusable and pass the same MutableInteractionSource as a parameter. Now, both HoverInteraction.Enter/Exit and FocusInteraction.Focus/Unfocus are emitted through the same MutableInteractionSource, and you can customize the appearance for both types of interaction in the same place:

// This InteractionSource will emit hover and focus interactions val interactionSource = remember { MutableInteractionSource() } Box( Modifier .size(100.dp) .hoverable(interactionSource = interactionSource) .focusable(interactionSource = interactionSource), contentAlignment = Alignment.Center ) { Text("Hello!") }Interactions.kt

Modifier.clickable is an even higher level abstraction than hoverable and focusable — for a component to be clickable, it is implicitly hoverable, and components that can be clicked should also be focusable. You can use Modifier.clickable to create a component that handles hover, focus, and press interactions, without needing to combine lower level APIs. If you want to make your component clickable as well, you can replace hoverable and focusable with a clickable:

// This InteractionSource will emit hover, focus, and press interactions val interactionSource = remember { MutableInteractionSource() } Box( Modifier .size(100.dp) .clickable( onClick = {}, interactionSource = interactionSource, // Also show a ripple effect indication = ripple() ), contentAlignment = Alignment.Center ) { Text("Hello!") }Interactions.kt

If you need low-level information about interactions with a component, you can use standard flow APIs for that component's InteractionSource. For example, suppose you want to maintain a list of the press and drag interactions for an InteractionSource. This code does half the job, adding the new presses to the list as they come in:

val interactionSource = remember { MutableInteractionSource() } val interactions = remember { mutableStateListOf<Interaction>() } LaunchedEffect(interactionSource) { interactionSource.interactions.collect { interaction -> when (interaction) { is PressInteraction.Press -> { interactions.add(interaction) } is DragInteraction.Start -> { interactions.add(interaction) } } } }Interactions.kt

But besides adding the new interactions, you also have to remove interactions when they end (for example, when the user lifts their finger back off the component). That's easy to do, since the end interactions always carry a reference to the associated start interaction. This code shows how you'd remove the interactions that have ended:

val interactionSource = remember { MutableInteractionSource() } val interactions = remember { mutableStateListOf<Interaction>() } LaunchedEffect(interactionSource) { interactionSource.interactions.collect { interaction -> when (interaction) { is PressInteraction.Press -> { interactions.add(interaction) } is PressInteraction.Release -> { interactions.remove(interaction.press) } is PressInteraction.Cancel -> { interactions.remove(interaction.press) } is DragInteraction.Start -> { interactions.add(interaction) } is DragInteraction.Stop -> { interactions.remove(interaction.start) } is DragInteraction.Cancel -> { interactions.remove(interaction.start) } } } }Interactions.kt

Now, if you want to know if the component is currently being pressed or dragged, all you have to do is check whether interactions is empty:

val isPressedOrDragged = interactions.isNotEmpty()Interactions.kt

If you want to know what the most recent interaction was, just look at the last item in the list. For example, this is how the Compose ripple implementation figures out the appropriate state overlay to use for the most recent interaction:

val lastInteraction = when (interactions.lastOrNull()) { is DragInteraction.Start -> "Dragged" is PressInteraction.Press -> "Pressed" else -> "No state" }Interactions.kt

Because all Interactions follow the same structure, there is not much of a difference in code when working with different types of user interactions — the overall pattern is the same.

Note that the previous examples in this section represent the Flow of interactions using State — this makes it easy to observe updated values, as reading the state value will automatically cause recompositions. However, composition is batched pre-frame. This means that if the state changes, and then changes back within the same frame, components observing the state won't see the change.

This is important for interactions, as interactions can regularly start and end within the same frame. For example, using the previous example with Button:

val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() Button(onClick = { /* do something */ }, interactionSource = interactionSource) { Text(if (isPressed) "Pressed!" else "Not pressed") }Interactions.kt

If a press starts and ends within the same frame, the text will never display as "Pressed!". In most cases, this is not an issue — showing a visual effect for such a small amount of time will result in flickering, and won't be very noticeable to the user. For some cases, such as showing a ripple effect or a similar animation, you may want to show the effect for at least a minimum amount of time, instead of immediately stopping if the button is no longer pressed. To do this, you can directly start and stop animations from inside the collect lambda, instead of writing to a state. There is an example of this pattern in the Build an advanced Indication with animated border section.

To see how you can build components with a custom response to input, here's an example of a modified button. In this case, suppose you want a button that responds to presses by changing its appearance:

To do this, build a custom composable based on Button, and have it take an additional icon parameter to draw the icon (in this case, a shopping cart). You call collectIsPressedAsState() to track whether the user is hovering over the button; when they are, you add the icon. Here's what the code looks like:

@Composable fun PressIconButton( onClick: () -> Unit, icon: @Composable () -> Unit, text: @Composable () -> Unit, modifier: Modifier = Modifier, interactionSource: MutableInteractionSource? = null ) { val isPressed = interactionSource?.collectIsPressedAsState()?.value ?: false Button( onClick = onClick, modifier = modifier, interactionSource = interactionSource ) { AnimatedVisibility(visible = isPressed) { if (isPressed) { Row { icon() Spacer(Modifier.size(ButtonDefaults.IconSpacing)) } } } text() } }Interactions.kt

And here's what it looks like to use that new composable:

PressIconButton( onClick = {}, icon = { Icon(Icons.Filled.ShoppingCart, contentDescription = null) }, text = { Text("Add to cart") } )Interactions.kt

Because this new PressIconButton is built on top of the existing Material Button, it reacts to user interactions in all the usual ways. When the user presses the button, it changes its opacity slightly, just like an ordinary Material Button.

In previous sections, you learned how to change part of a component in response to different Interactions, such as showing an icon when pressed. This same approach can be used for changing the value of parameters you provide to a component, or changing the content displayed inside a component, but this is only applicable on a per-component basis. Often, an application or design system will have a generic system for stateful visual effects — an effect that should be applied to all components in a consistent manner.

If you are building this kind of design system, customizing one component and reusing this customization for other components can be difficult for the following reasons:

To avoid these issues and easily scale a custom component across your system, you can use Indication. Indication represents a reusable visual effect that can be applied across components in an application or design system. Indication is split into two parts:

IndicationNodeFactory: A factory that creates Modifier.Node instances that render visual effects for a component. For simpler implementations that do not change across components, this can be a singleton (object) and reused across the entire application.

These instances can be stateful or stateless. Since they are created per component, they can retrieve values from a CompositionLocal to change how they appear or behave inside a particular component, as with any other Modifier.Node.

Modifier.indication: A modifier that draws Indication for a component. Modifier.clickable and other high level interaction modifiers accept an indication parameter directly, so they not only emit Interactions, but can also draw visual effects for the Interactions they emit. So, for simple cases, you can just use Modifier.clickable without needing Modifier.indication.

This section describes how to replace a manual scale effect applied to one specific button with an indication equivalent that can be reused across multiple components.

The following code creates a button that scales downwards on press:

val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() val scale by animateFloatAsState(targetValue = if (isPressed) 0.9f else 1f, label = "scale") Button( modifier = Modifier.scale(scale), onClick = { }, interactionSource = interactionSource ) { Text(if (isPressed) "Pressed!" else "Not pressed") }Interactions.kt

To convert the scale effect in the snippet above to an Indication, follow these steps:

Create the Modifier.Node responsible for applying the scale effect. When attached, the node observes the interaction source, similar to previous examples. The only difference here is that it directly launches animations instead of converting the incoming Interactions to state.

The node needs to implement DrawModifierNode so it can override ContentDrawScope#draw(), and render a scale effect using the same drawing commands as with any other graphics API in Compose.

Calling drawContent() available from the ContentDrawScope receiver will draw the actual component that the Indication should be applied to, so you just need to call this function within a scale transformation. Make sure your Indication implementations always call drawContent() at some point; otherwise, the component you are applying the Indication to will not be drawn.

private class ScaleNode(private val interactionSource: InteractionSource) : Modifier.Node(), DrawModifierNode { var currentPressPosition: Offset = Offset.Zero val animatedScalePercent = Animatable(1f) private suspend fun animateToPressed(pressPosition: Offset) { currentPressPosition = pressPosition animatedScalePercent.animateTo(0.9f, spring()) } private suspend fun animateToResting() { animatedScalePercent.animateTo(1f, spring()) } override fun onAttach() { coroutineScope.launch { interactionSource.interactions.collectLatest { interaction -> when (interaction) { is PressInteraction.Press -> animateToPressed(interaction.pressPosition) is PressInteraction.Release -> animateToResting() is PressInteraction.Cancel -> animateToResting() } } } } override fun ContentDrawScope.draw() { scale( scale = animatedScalePercent.value, pivot = currentPressPosition ) { this@draw.drawContent() } } }Interactions.kt

Create the IndicationNodeFactory. Its only responsibility is to create a new node instance for a provided interaction source. As there are no parameters to configure the indication, the factory can be an object:

object ScaleIndication : IndicationNodeFactory { override fun create(interactionSource: InteractionSource): DelegatableNode { return ScaleNode(interactionSource) } override fun equals(other: Any?): Boolean = other === ScaleIndication override fun hashCode() = 100 }Interactions.kt

Modifier.clickable uses Modifier.indication internally, so to make a clickable component with ScaleIndication, all you need to do is provide the Indication as a parameter to clickable:

Box( modifier = Modifier .size(100.dp) .clickable( onClick = {}, indication = ScaleIndication, interactionSource = null ) .background(Color.Blue), contentAlignment = Alignment.Center ) { Text("Hello!", color = Color.White) }Interactions.kt

This also makes it easy to build high level, reusable components using a custom Indication — a button could look like:

@Composable fun ScaleButton( onClick: () -> Unit, modifier: Modifier = Modifier, enabled: Boolean = true, interactionSource: MutableInteractionSource? = null, shape: Shape = CircleShape, content: @Composable RowScope.() -> Unit ) { Row( modifier = modifier .defaultMinSize(minWidth = 76.dp, minHeight = 48.dp) .clickable( enabled = enabled, indication = ScaleIndication, interactionSource = interactionSource, onClick = onClick ) .border(width = 2.dp, color = Color.Blue, shape = shape) .padding(horizontal = 16.dp, vertical = 8.dp), horizontalArrangement = Arrangement.Center, verticalAlignment = Alignment.CenterVertically, content = content ) }Interactions.kt

You can then use the button in the following way:

ScaleButton(onClick = {}) { Icon(Icons.Filled.ShoppingCart, "") Spacer(Modifier.padding(10.dp)) Text(text = "Add to cart!") }Interactions.kt

Indication is not just limited to transformation effects, such as scaling a component. Because IndicationNodeFactory returns a Modifier.Node, you can draw any kind of effect above or below the content as with other drawing APIs. For example, you can draw an animated border around the component and an overlay on top of the component when it is pressed:

The Indication implementation here is very similar to the previous example — it just creates a node with some parameters. Since the animated border depends on the shape and the border of the component the Indication is used for, the Indication implementation also requires shape and border width to be provided as parameters:

data class NeonIndication(private val shape: Shape, private val borderWidth: Dp) : IndicationNodeFactory { override fun create(interactionSource: InteractionSource): DelegatableNode { return NeonNode( shape, // Double the border size for a stronger press effect borderWidth * 2, interactionSource ) } }Interactions.kt

The Modifier.Node implementation is also conceptually the same, even if the drawing code is more complicated. As before, it observes InteractionSource when attached, launches animations, and implements DrawModifierNode to draw the effect on top of the content:

private class NeonNode( private val shape: Shape, private val borderWidth: Dp, private val interactionSource: InteractionSource ) : Modifier.Node(), DrawModifierNode { var currentPressPosition: Offset = Offset.Zero val animatedProgress = Animatable(0f) val animatedPressAlpha = Animatable(1f) var pressedAnimation: Job? = null var restingAnimation: Job? = null private suspend fun animateToPressed(pressPosition: Offset) { // Finish any existing animations, in case of a new press while we are still showing // an animation for a previous one restingAnimation?.cancel() pressedAnimation?.cancel() pressedAnimation = coroutineScope.launch { currentPressPosition = pressPosition animatedPressAlpha.snapTo(1f) animatedProgress.snapTo(0f) animatedProgress.animateTo(1f, tween(450)) } } private fun animateToResting() { restingAnimation = coroutineScope.launch { // Wait for the existing press animation to finish if it is still ongoing pressedAnimation?.join() animatedPressAlpha.animateTo(0f, tween(250)) animatedProgress.snapTo(0f) } } override fun onAttach() { coroutineScope.launch { interactionSource.interactions.collect { interaction -> when (interaction) { is PressInteraction.Press -> animateToPressed(interaction.pressPosition) is PressInteraction.Release -> animateToResting() is PressInteraction.Cancel -> animateToResting() } } } } override fun ContentDrawScope.draw() { val (startPosition, endPosition) = calculateGradientStartAndEndFromPressPosition( currentPressPosition, size ) val brush = animateBrush( startPosition = startPosition, endPosition = endPosition, progress = animatedProgress.value ) val alpha = animatedPressAlpha.value drawContent() val outline = shape.createOutline(size, layoutDirection, this) // Draw overlay on top of content drawOutline( outline = outline, brush = brush, alpha = alpha * 0.1f ) // Draw border on top of overlay drawOutline( outline = outline, brush = brush, alpha = alpha, style = Stroke(width = borderWidth.toPx()) ) } /** * Calculates a gradient start / end where start is the point on the bounding rectangle of * size [size] that intercepts with the line drawn from the center to [pressPosition], * and end is the intercept on the opposite end of that line. */ private fun calculateGradientStartAndEndFromPressPosition( pressPosition: Offset, size: Size ): Pair<Offset, Offset> { // Convert to offset from the center val offset = pressPosition - size.center // y = mx + c, c is 0, so just test for x and y to see where the intercept is val gradient = offset.y / offset.x // We are starting from the center, so halve the width and height - convert the sign // to match the offset val width = (size.width / 2f) * sign(offset.x) val height = (size.height / 2f) * sign(offset.y) val x = height / gradient val y = gradient * width // Figure out which intercept lies within bounds val intercept = if (abs(y) <= abs(height)) { Offset(width, y) } else { Offset(x, height) } // Convert back to offsets from 0,0 val start = intercept + size.center val end = Offset(size.width - start.x, size.height - start.y) return start to end } private fun animateBrush( startPosition: Offset, endPosition: Offset, progress: Float ): Brush { if (progress == 0f) return TransparentBrush // This is *expensive* - we are doing a lot of allocations on each animation frame. To // recreate a similar effect in a performant way, it would be better to create one large // gradient and translate it on each frame, instead of creating a whole new gradient // and shader. The current approach will be janky! val colorStops = buildList { when { progress < 1 / 6f -> { val adjustedProgress = progress * 6f add(0f to Blue) add(adjustedProgress to Color.Transparent) } progress < 2 / 6f -> { val adjustedProgress = (progress - 1 / 6f) * 6f add(0f to Purple) add(adjustedProgress * MaxBlueStop to Blue) add(adjustedProgress to Blue) add(1f to Color.Transparent) } progress < 3 / 6f -> { val adjustedProgress = (progress - 2 / 6f) * 6f add(0f to Pink) add(adjustedProgress * MaxPurpleStop to Purple) add(MaxBlueStop to Blue) add(1f to Blue) } progress < 4 / 6f -> { val adjustedProgress = (progress - 3 / 6f) * 6f add(0f to Orange) add(adjustedProgress * MaxPinkStop to Pink) add(MaxPurpleStop to Purple) add(MaxBlueStop to Blue) add(1f to Blue) } progress < 5 / 6f -> { val adjustedProgress = (progress - 4 / 6f) * 6f add(0f to Yellow) add(adjustedProgress * MaxOrangeStop to Orange) add(MaxPinkStop to Pink) add(MaxPurpleStop to Purple) add(MaxBlueStop to Blue) add(1f to Blue) } else -> { val adjustedProgress = (progress - 5 / 6f) * 6f add(0f to Yellow) add(adjustedProgress * MaxYellowStop to Yellow) add(MaxOrangeStop to Orange) add(MaxPinkStop to Pink) add(MaxPurpleStop to Purple) add(MaxBlueStop to Blue) add(1f to Blue) } } } return linearGradient( colorStops = colorStops.toTypedArray(), start = startPosition, end = endPosition ) } companion object { val TransparentBrush = SolidColor(Color.Transparent) val Blue = Color(0xFF30C0D8) val Purple = Color(0xFF7848A8) val Pink = Color(0xFFF03078) val Orange = Color(0xFFF07800) val Yellow = Color(0xFFF0D800) const val MaxYellowStop = 0.16f const val MaxOrangeStop = 0.33f const val MaxPinkStop = 0.5f const val MaxPurpleStop = 0.67f const val MaxBlueStop = 0.83f } }Interactions.kt

The main difference here is that there is now a minimum duration for the animation with the animateToResting() function, so even if the press is immediately released, the press animation will continue. There is also handling for multiple quick presses at the start of animateToPressed — if a press happens during an existing press or resting animation, the previous animation is canceled, and the press animation starts from the beginning. To support multiple concurrent effects (such as with ripples, where a new ripple animation will draw on top of other ripples), you can track the animations in a list, instead of canceling existing animations and starting new ones.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Epsilon and coordinate system Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-coordinate-system

**Contents:**
- Epsilon and coordinate system Stay organized with collections Save and categorize content based on your preferences.
- What are reasonable world unit sizes and epsilon values?

Your choice of a coordinate system—in other words, your choice of what 1 world unit means—goes hand in hand with your choice of brush epsilon value. The coordinate system is reflected in the inputToWorld transform matrix value you pass to InProgressStrokesView.startStroke() and the similar transform you apply to the Canvas when drawing dry strokes.

A typical app can consider only world units and leave the strokeToWorldTransform argument of startStroke() as the default identity matrix.

If your app supports panning, zooming, or rotating a drawing surface, then the exact value of those transform matrices will change over time, but those changes reflect a change in how your world coordinate system is being viewed by the camera that the user views the drawing surface through.

The world coordinate system can be envisioned as a grid with each cell being the size of a world unit. Epsilon is a size in that grid, a floating point number of world units.

As users zoom in on your content, they also zoom in on the world unit grid, so the choice of world unit and epsilon size in terms of a default 100% zoom level is important.

The world unit size definition and the epsilon value in world units must be fixed values throughout the lifetime of your app.

The internal implementation uses epsilon to determine how close two points must be to each other to be treated as the same point. In other words, any distance less than epsilon is treated as zero distance. This is used to quantize and round internal calculations.

For portability across different screen sizes and device densities, the world unit size should be density independent. The classic unit for such scenarios is density-independent pixels (dp). Typically choose 1 dp as your world unit size.

When choosing a fixed epsilon value, avoid the internal implementation rounding numbers to any distance that would be larger than a pixel. With a world unit size of 1 dp, epsilon should be at most 1/4 (0.25 world units) to be the size of a pixel on high display density devices where 1 dp can be 4 px. However, if you want to support the user zooming in on their content by up to 10x and still keep epsilon-related rounding to 1 px or less, then epsilon should be 0.25 divided by 10, which equals 0.025 world units.

This doesn't mean that you can't zoom in further than 10x, but you might start seeing some imprecision and artifacts in the stroke rendering at that point.

Choosing an epsilon value is a balance between precise detail when zooming in and computational resources such as:

These guidelines are reasonable defaults, but you can use them to choose coordinate system and epsilon values that are more suited to your needs.

Deviating too far from these recommended values can adversely affect your application. For example, increased resource consumption can make your application run slowly. In some cases, floating-point precision issues can also manifest as strange visual artifacts.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-22 UTC.

---

## Setup Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/ink-api-setup

**Contents:**
- Setup Stay organized with collections Save and categorize content based on your preferences.
  - Kotlin
  - Groovy

To integrate the Ink API, add the dependencies to your app.

The Ink API library is available from the Google Maven repository.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-17 UTC.

**Examples:**

Example 1 (bash):
```bash
dependencies {
    val ink_version = "1.0.0"

    implementation "androidx.ink:ink-nativeloader:$ink_version"
    implementation "androidx.ink:ink-rendering:$ink_version"
    implementation "androidx.ink:ink-strokes:$ink_version"
    implementation "androidx.ink:ink-authoring-compose:$ink_version"
    implementation "androidx.ink:ink-brush-compose:$ink_version"
    implementation "androidx.ink:ink-geometry-compose:$ink_version"
    implementation "androidx.ink:ink-storage:$ink_version"
}
```

Example 2 (bash):
```bash
dependencies {
    def ink_version = "1.0.0"

    implementation("androidx.ink:ink-nativeloader:$ink_version")
    implementation("androidx.ink:ink-rendering:$ink_version")
    implementation("androidx.ink:ink-strokes:$ink_version")
    implementation("androidx.ink:ink-authoring-compose:$ink_version")
    implementation("androidx.ink:ink-brush-compose:$ink_version")
    implementation("androidx.ink:ink-geometry-compose:$ink_version")
    implementation("androidx.ink:ink-storage:$ink_version")
}
```

---

## Focus in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/focus

**Contents:**
- Focus in Compose Stay organized with collections Save and categorize content based on your preferences.
- Default focus traversal order
- Recommended for you

When a user interacts with your app, they often do so by touching elements on their screen. However, this is not the only form of interaction. Other forms of interaction could include the following:

In these cases, it is important to track which component is active at any given point in time, which is what we call focus. Elements on the screen should be focused in a logical order. Jetpack Compose has a default way of handling focus that is correct in most cases. However, in some cases, you might need to modify this default behavior.

The following pages describe how to use focus in your app:

Before we dive into the default behavior of the focus search, it's important to understand the concept of level in the hierarchy: generally speaking, we can say that two Composables are at the same level when they are siblings, meaning that they have the same parents. For instance, elements inside a Column are at the same level. Getting up a level means going from a child to its Composable parent, or, keeping the same example, going back from an item to a Column that contains it. Going down a level is the other way around, from the Column parent to the contained items. This concept can be applied to every Composable that can contain other Composables.

UI navigation can happen in multiple ways, some of which most users will already know:

Take as an example the screenshot below, where you have four buttons, one below the other, and you want to cycle through them all in order of appearance. Jetpack Compose delivers this behavior out of the box: the toolkit lets you cycle through each composable in vertical order from top to bottom using the tab key, or move focus by pressing the up or down arrow.

When you switch to a different kind of layout, things change a bit. If your layout has more than one column, like the layout below, Jetpack Compose lets you navigate through them without having to add any code. If you press the tab key, Jetpack Compose automatically highlights the items in order of declaration, from First to Fourth. Using arrow keys on your keyboard makes the selection follow the desired direction in 2D space.

Column { Row { TextButton({ }) { Text("First field") } TextButton({ }) { Text("Second field") } } Row { TextButton({ }) { Text("Third field") } TextButton({ }) { Text("Fourth field") } } }FocusSnippets.kt

The Composables are declared in two Rows, and the focus elements are declared in order, from first to fourth. When you press the tab key, this produces the following focus order:

In the snippet below, you declare the items in Columns rather than in Rows:

Row { Column { TextButton({ }) { Text("First field") } TextButton({ }) { Text("Second field") } } Column { TextButton({ }) { Text("Third field") } TextButton({ }) { Text("Fourth field") } } }FocusSnippets.kt

This layout traverses the items vertically, from top to bottom, from the start of the screen towards the end:

The previous two samples, while differing in one-directional navigation, provide the same experience when it comes to two-dimensional navigation. This is usually because the items on the screen have the same geographic placement in both examples. Navigating right from the first Column moves the focus to the second, and navigating down from the first Row moves the focus to the one below it.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Advanced animation example: Gestures Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/advanced

**Contents:**
- Advanced animation example: Gestures Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

There are several things we have to take into consideration when we are working with touch events and animations, compared to when we are working with animations alone. First of all, we might need to interrupt an ongoing animation when touch events begin as user interaction should have the highest priority.

In the example below, we use an Animatable to represent the offset position of a circle component. Touch events are processed with the pointerInput modifier. When we detect a new tap event, we call animateTo to animate the offset value to the tap position. A tap event can happen during the animation too, and in that case, animateTo interrupts the ongoing animation and starts the animation to the new target position while maintaining the velocity of the interrupted animation.

@Composable fun Gesture() { val offset = remember { Animatable(Offset(0f, 0f), Offset.VectorConverter) } Box( modifier = Modifier .fillMaxSize() .pointerInput(Unit) { coroutineScope { while (true) { // Detect a tap event and obtain its position. awaitPointerEventScope { val position = awaitFirstDown().position launch { // Animate to the tap position. offset.animateTo(position) } } } } } ) { Circle(modifier = Modifier.offset { offset.value.toIntOffset() }) } } private fun Offset.toIntOffset() = IntOffset(x.roundToInt(), y.roundToInt())AdvancedAnimationSnippets.kt

Another frequent pattern is we need to synchronize animation values with values coming from touch events, such as drag. In the example below, we see "swipe to dismiss" implemented as a Modifier (rather than using the SwipeToDismiss composable). The horizontal offset of the element is represented as an Animatable. This API has a characteristic useful in gesture animation. Its value can be changed by touch events as well as the animation. When we receive a touch down event, we stop the Animatable by the stop method so that any ongoing animation is intercepted.

During a drag event, we use snapTo to update the Animatable value with the value calculated from touch events. For fling, Compose provides VelocityTracker to record drag events and calculate velocity. The velocity can be fed directly to animateDecay for the fling animation. When we want to slide the offset value back to the original position, we specify the target offset value of 0f with the animateTo method.

fun Modifier.swipeToDismiss( onDismissed: () -> Unit ): Modifier = composed { val offsetX = remember { Animatable(0f) } pointerInput(Unit) { // Used to calculate fling decay. val decay = splineBasedDecay<Float>(this) // Use suspend functions for touch events and the Animatable. coroutineScope { while (true) { val velocityTracker = VelocityTracker() // Stop any ongoing animation. offsetX.stop() awaitPointerEventScope { // Detect a touch down event. val pointerId = awaitFirstDown().id horizontalDrag(pointerId) { change -> // Update the animation value with touch events. launch { offsetX.snapTo( offsetX.value + change.positionChange().x ) } velocityTracker.addPosition( change.uptimeMillis, change.position ) } } // No longer receiving touch events. Prepare the animation. val velocity = velocityTracker.calculateVelocity().x val targetOffsetX = decay.calculateTargetValue( offsetX.value, velocity ) // The animation stops when it reaches the bounds. offsetX.updateBounds( lowerBound = -size.width.toFloat(), upperBound = size.width.toFloat() ) launch { if (targetOffsetX.absoluteValue <= size.width) { // Not enough velocity; Slide back. offsetX.animateTo( targetValue = 0f, initialVelocity = velocity ) } else { // The element was swiped away. offsetX.animateDecay(velocity, decay) onDismissed() } } } } } .offset { IntOffset(offsetX.value.roundToInt(), 0) } }AdvancedAnimationSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Understand gestures Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/pointer-input/understand-gestures

**Contents:**
- Understand gestures Stay organized with collections Save and categorize content based on your preferences.
- Definitions
- Different levels of abstraction
  - Component support
  - Add specific gestures to arbitrary composables with modifiers
  - Add custom gesture to arbitrary composables with pointerInput modifier
    - Detect full gestures
    - Handle events per gesture
    - Wait for a specific event or sub-gesture
    - Apply calculations for multi-touch events

There are several terms and concepts that are important to understand when working on gesture handling in an application. This page explains the terms pointers, pointer events, and gestures, and introduces the different abstraction levels for gestures. It also dives deeper into event consumption and propagation.

To understand the various concepts on this page, you need to understand some of the terminology used:

Jetpack Compose provides different levels of abstraction for handling gestures. On the top level is component support. Composables like Button automatically include gesture support. To add gesture support to custom components, you can add gesture modifiers like clickable to arbitrary composables. Finally, if you need a custom gesture, you can use the pointerInput modifier.

As a rule, build on the highest level of abstraction that offers the functionality you need. This way, you benefit from the best practices included in the layer. For example, Button contains more semantic information, used for accessibility, than clickable, which contains more information than a raw pointerInput implementation.

Many out-of-the-box components in Compose include some sort of internal gesture handling. For example, a LazyColumn responds to drag gestures by scrolling its content, a Button shows a ripple when you press down on it, and the SwipeToDismiss component contains swiping logic to dismiss an element. This type of gesture handling works automatically.

Next to internal gesture handling, many components also require the caller to handle the gesture. For example, a Button automatically detects taps and triggers a click event. You pass an onClick lambda to the Button to react to the gesture. Similarly, you add an onValueChange lambda to a Slider to react to the user dragging the slider handle.

When it fits your use case, prefer gestures included in components, as they include out-of-the-box support for focus and accessibility, and they are well-tested. For example, a Button is marked in a special way so that accessibility services correctly describe it as a button, instead of just any clickable element:

// Talkback: "Click me!, Button, double tap to activate" Button(onClick = { /* TODO */ }) { Text("Click me!") } // Talkback: "Click me!, double tap to activate" Box(Modifier.clickable { /* TODO */ }) { Text("Click me!") }UnderstandGesturesSnippets.kt

To learn more about accessibility in Compose, see Accessibility in Compose.

You can apply gesture modifiers to any arbitrary composable to make the composable listen to gestures. For example, you can let a generic Box handle tap gestures by making it clickable, or let a Column handle vertical scroll by applying verticalScroll.

There are many modifiers to handle different types of gestures:

As a rule, prefer out-of-the-box gesture modifiers over custom gesture handling. The modifiers add more functionality on top of the pure pointer event handling. For example, the clickable modifier not only adds detection of presses and taps, but also adds semantic information, visual indications on interactions, hovering, focus, and keyboard support. You can check the source code of clickable to see how the functionality is being added.

Not every gesture is implemented with an out-of-the-box gesture modifier. For example, you cannot use a modifier to react to a drag after long-press, a control-click, or a three-finger tap. Instead, you can write your own gesture handler to identify these custom gestures. You can create a gesture handler with the pointerInput modifier, which gives you access to the raw pointer events.

The following code listens to raw pointer events:

@Composable private fun LogPointerEvents(filter: PointerEventType? = null) { var log by remember { mutableStateOf("") } Column { Text(log) Box( Modifier .size(100.dp) .background(Color.Red) .pointerInput(filter) { awaitPointerEventScope { while (true) { val event = awaitPointerEvent() // handle pointer event if (filter == null || event.type == filter) { log = "${event.type}, ${event.changes.first().position}" } } } } ) } }UnderstandGesturesSnippets.kt

If you break this snippet up, the core components are:

Although listening to raw input events is powerful, it is also complex to write a custom gesture based on this raw data. To simplify the creation of custom gestures, many utility methods are available.

Instead of handling the raw pointer events, you can listen for specific gestures to occur and respond appropriately. The AwaitPointerEventScope provides methods for listening for:

These are top-level detectors, so you can't add multiple detectors within one pointerInput modifier. The following snippet only detects the taps, not the drags:

var log by remember { mutableStateOf("") } Column { Text(log) Box( Modifier .size(100.dp) .background(Color.Red) .pointerInput(Unit) { detectTapGestures { log = "Tap!" } // Never reached detectDragGestures { _, _ -> log = "Dragging" } } ) }UnderstandGesturesSnippets.kt

Internally, the detectTapGestures method blocks the coroutine, and the second detector is never reached. If you need to add more than one gesture listener to a composable, use separate pointerInput modifier instances instead:

var log by remember { mutableStateOf("") } Column { Text(log) Box( Modifier .size(100.dp) .background(Color.Red) .pointerInput(Unit) { detectTapGestures { log = "Tap!" } } .pointerInput(Unit) { // These drag events will correctly be triggered detectDragGestures { _, _ -> log = "Dragging" } } ) }UnderstandGesturesSnippets.kt

By definition, gestures start with a pointer down event. You can use the awaitEachGesture helper method instead of the while(true) loop that passes through each raw event. The awaitEachGesture method restarts the containing block when all pointers have been lifted, indicating the gesture is completed:

@Composable private fun SimpleClickable(onClick: () -> Unit) { Box( Modifier .size(100.dp) .pointerInput(onClick) { awaitEachGesture { awaitFirstDown().also { it.consume() } val up = waitForUpOrCancellation() if (up != null) { up.consume() onClick() } } } ) }UnderstandGesturesSnippets.kt

In practice, you almost always want to use awaitEachGesture unless you're responding to pointer events without identifying gestures. An example of this is hoverable, which does not respond to pointer down or up events— it just needs to know when a pointer enters or exits its bounds.

There's a set of methods that helps identify common parts of gestures:

When a user is performing a multi-touch gesture using more than one pointer, it's complex to understand the required transformation based on the raw values. If the transformable modifier or the detectTransformGestures methods aren't giving enough fine-grained control for your use case, you can listen to the raw events and apply calculations on those. These helper methods are calculateCentroid, calculateCentroidSize, calculatePan, calculateRotation, and calculateZoom.

Not every pointer event is sent to every pointerInput modifier. Event dispatching works as follows:

Hover events, triggered by a mouse or stylus hovering, are an exception to the rules defined here. Hover events are sent to any composable that they hit. So when a user hovers a pointer from the bounds of one composable to the next, instead of sending the events to that first composable, events are sent to the new composable.

When more than one composable has a gesture handler assigned to it, those handlers shouldn't conflict. For example, take a look at this UI:

When a user taps the bookmark button, the button's onClick lambda handles that gesture. When a user taps on any other part of the list item, the ListItem handles that gesture and navigates to the article. In terms of pointer input, the Button must consume this event, so that its parent knows not to react to it anymore. Gestures included in out-of-the-box components and the common gesture modifiers include this consumption behavior, but if you are writing your own custom gesture, you must consume events manually. You do this with the PointerInputChange.consume method:

Modifier.pointerInput(Unit) { awaitEachGesture { while (true) { val event = awaitPointerEvent() // consume all changes event.changes.forEach { it.consume() } } } }UnderstandGesturesSnippets.kt

Consuming an event does not stop the event propagation to other composables. A composable needs to explicitly ignore consumed events instead. When writing custom gestures, you should check if an event was already consumed by another element:

Modifier.pointerInput(Unit) { awaitEachGesture { while (true) { val event = awaitPointerEvent() if (event.changes.any { it.isConsumed }) { // A pointer is consumed by another gesture handler } else { // Handle unconsumed event } } } }UnderstandGesturesSnippets.kt

As mentioned before, pointer changes are passed to each composable that it hits. But if more than one such composable exists, in what order do the events propagate? If you take the example from the last section, this UI translates to the following UI tree, where only the ListItem and the Button respond to pointer events:

Pointer events flow through each of these composables three times, during three "passes":

Visually, the event flow can be represented as follows:

Once an input change is consumed, this information is passed from that point in the flow onwards:

In code, you can specify the pass that you're interested in:

Modifier.pointerInput(Unit) { awaitPointerEventScope { val eventOnInitialPass = awaitPointerEvent(PointerEventPass.Initial) val eventOnMainPass = awaitPointerEvent(PointerEventPass.Main) // default val eventOnFinalPass = awaitPointerEvent(PointerEventPass.Final) } }UnderstandGesturesSnippets.kt

In this code snippet, the same identical event is returned by each of these await method calls, although the data about the consumption might have changed.

In your test methods, you can manually send pointer events using the performTouchInput method. This lets you perform either higher-level full gestures (such as pinch or long click) or low level gestures (such as moving the cursor by a certain amount of pixels):

composeTestRule.onNodeWithTag("MyList").performTouchInput { swipeUp() swipeDown() click() }UnderstandGesturesSnippets.kt

See the performTouchInput documentation for more examples.

You can learn more about gestures in Jetpack Compose from the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Touch and input in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input

**Contents:**
- Touch and input in Compose Stay organized with collections Save and categorize content based on your preferences.

Users interact with Android apps in various ways. For example, they can:

Compose has a lot of built-in support for these use cases, but in some scenarios you need to customize or extend the default behavior. In this section, you learn more about pointer input, focus, and handling interactions.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Swipe to dismiss or update Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/user-interactions/swipe-to-dismiss

**Contents:**
- Swipe to dismiss or update Stay organized with collections Save and categorize content based on your preferences.
- API surface
- Basic example: Update or dismiss on swipe
  - Key points about the code
  - Key points about the code
  - Result
- Advanced example: Animate background color on swipe
  - Key points about the code
  - Key points about the code
  - Result

The SwipeToDismissBox component allows a user to dismiss or update an item by swiping it to the left or right.

Use the SwipeToDismissBox composable to implement actions that are triggered by swipe gestures. Key parameters include:

The snippets in this example show a swipe implementation that either updates the item when swiped from start to end, or dismisses the item when swiped from end to start.

data class TodoItem( val itemDescription: String, var isItemDone: Boolean = false )SwipeToDismissBox.kt

@Composable fun TodoListItem( todoItem: TodoItem, onToggleDone: (TodoItem) -> Unit, onRemove: (TodoItem) -> Unit, modifier: Modifier = Modifier, ) { val swipeToDismissBoxState = rememberSwipeToDismissBoxState( confirmValueChange = { if (it == StartToEnd) onToggleDone(todoItem) else if (it == EndToStart) onRemove(todoItem) // Reset item when toggling done status it != StartToEnd } ) SwipeToDismissBox( state = swipeToDismissBoxState, modifier = modifier.fillMaxSize(), backgroundContent = { when (swipeToDismissBoxState.dismissDirection) { StartToEnd -> { Icon( if (todoItem.isItemDone) Icons.Default.CheckBox else Icons.Default.CheckBoxOutlineBlank, contentDescription = if (todoItem.isItemDone) "Done" else "Not done", modifier = Modifier .fillMaxSize() .background(Color.Blue) .wrapContentSize(Alignment.CenterStart) .padding(12.dp), tint = Color.White ) } EndToStart -> { Icon( imageVector = Icons.Default.Delete, contentDescription = "Remove item", modifier = Modifier .fillMaxSize() .background(Color.Red) .wrapContentSize(Alignment.CenterEnd) .padding(12.dp), tint = Color.White ) } Settled -> {} } } ) { ListItem( headlineContent = { Text(todoItem.itemDescription) }, supportingContent = { Text("swipe me to update or remove.") } ) } }SwipeToDismissBox.kt

@Composable private fun SwipeItemExample() { val todoItems = remember { mutableStateListOf( TodoItem("Pay bills"), TodoItem("Buy groceries"), TodoItem("Go to gym"), TodoItem("Get dinner") ) } LazyColumn { items( items = todoItems, key = { it.itemDescription } ) { todoItem -> TodoListItem( todoItem = todoItem, onToggleDone = { todoItem -> todoItem.isItemDone = !todoItem.isItemDone }, onRemove = { todoItem -> todoItems -= todoItem }, modifier = Modifier.animateItem() ) } } }SwipeToDismissBox.kt

The following video demonstrates the basic swipe-to-dismiss functionality from the preceding snippets:

See the GitHub source file for the full sample code.

The following snippets show how to incorporate a positional threshold to animate an item's background color on swipe.

data class TodoItem( val itemDescription: String, var isItemDone: Boolean = false )SwipeToDismissBox.kt

@Composable fun TodoListItemWithAnimation( todoItem: TodoItem, onToggleDone: (TodoItem) -> Unit, onRemove: (TodoItem) -> Unit, modifier: Modifier = Modifier, ) { val swipeToDismissBoxState = rememberSwipeToDismissBoxState( confirmValueChange = { if (it == StartToEnd) onToggleDone(todoItem) else if (it == EndToStart) onRemove(todoItem) // Reset item when toggling done status it != StartToEnd } ) SwipeToDismissBox( state = swipeToDismissBoxState, modifier = modifier.fillMaxSize(), backgroundContent = { when (swipeToDismissBoxState.dismissDirection) { StartToEnd -> { Icon( if (todoItem.isItemDone) Icons.Default.CheckBox else Icons.Default.CheckBoxOutlineBlank, contentDescription = if (todoItem.isItemDone) "Done" else "Not done", modifier = Modifier .fillMaxSize() .drawBehind { drawRect(lerp(Color.LightGray, Color.Blue, swipeToDismissBoxState.progress)) } .wrapContentSize(Alignment.CenterStart) .padding(12.dp), tint = Color.White ) } EndToStart -> { Icon( imageVector = Icons.Default.Delete, contentDescription = "Remove item", modifier = Modifier .fillMaxSize() .background(lerp(Color.LightGray, Color.Red, swipeToDismissBoxState.progress)) .wrapContentSize(Alignment.CenterEnd) .padding(12.dp), tint = Color.White ) } Settled -> {} } } ) { OutlinedCard(shape = RectangleShape) { ListItem( headlineContent = { Text(todoItem.itemDescription) }, supportingContent = { Text("swipe me to update or remove.") } ) } } }SwipeToDismissBox.kt

@Composable private fun SwipeItemWithAnimationExample() { val todoItems = remember { mutableStateListOf( TodoItem("Pay bills"), TodoItem("Buy groceries"), TodoItem("Go to gym"), TodoItem("Get dinner") ) } LazyColumn { items( items = todoItems, key = { it.itemDescription } ) { todoItem -> TodoListItemWithAnimation( todoItem = todoItem, onToggleDone = { todoItem -> todoItem.isItemDone = !todoItem.isItemDone }, onRemove = { todoItem -> todoItems -= todoItem }, modifier = Modifier.animateItem() ) } } }SwipeToDismissBox.kt

The following video shows the advanced functionality with animated background color:

See the GitHub source file for the full sample code.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Input compatibility on large screens Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/input-compatibility-on-large-screens

**Contents:**
- Input compatibility on large screens Stay organized with collections Save and categorize content based on your preferences.
- Keyboard
  - Navigation
  - Keystrokes
  - Shortcuts
- Stylus
  - Historical points
  - Palm rejection
    - Android 12
    - Android 13

On large screen devices, users often interact with apps using a keyboard, mouse, trackpad, stylus, or gamepad. To enable your app to accept input from external devices, do the following:

The way your app responds to keyboard input contributes to the large screen user experience. There are three kinds of keyboard input: navigation, keystrokes, and shortcuts.

Keyboard navigation is rarely implemented in touch‑centric apps, but users expect it when they're using an app and have their hands on a keyboard. Keyboard navigation can be essential on phones, tablets, foldables, and desktop devices for users with accessibility needs.

For many apps, arrow key and Tab key navigation are handled automatically by the Android framework. For example, some composables are focusable by default, such as a Button or a composable with the clickable modifier; keyboard navigation should generally work without any additional code. To enable keyboard navigation for custom composables that are not focusable by default, add the focusable modifier:

var color by remember { mutableStateOf(Green) } Box( Modifier .background(color) .onFocusChanged { color = if (it.isFocused) Blue else Green } .focusable() ) { Text("Focusable 1") }FocusSnippets.kt

For more information, see Making a composable focusable.

When focus is enabled, the Android framework creates a navigational mapping for all focusable components based on their position. This usually works as expected and no further development is needed.

However, Compose doesn't always determine the correct next item for tabbed navigation for complex composables like tabs and lists, for example, when one of the composables is a horizontal scrollable that is not fully visible.

To control focus behavior, add the focusGroup modifier to the parent composable of a collection of composables. Focus moves to the group, then through the group before moving to the next focusable component, for example:

For more information, see Provide coherent navigation with focus groups.

Test access to every UI element of your app using the keyboard only. Frequently used elements should be accessible without mouse or touch input.

Remember, keyboard support might be essential for users with accessibility needs.

For text input that would be handled by an on screen virtual keyboard (IME), such as for a TextField , apps should behave as expected on large screen devices with no additional development work. For keystrokes that cannot be anticipated by the framework, apps need to handle the behavior themselves. This is especially true for apps with custom views.

Some examples are chat apps that use the Enter key to send a message, media apps that start and stop playback with the Spacebar, and games that control movement with the w, a, s, and d keys.

You can handle individual keystrokes with the onKeyEvent modifier, which accepts a lambda that's called when the modified component receives a key event. The KeyEvent#type property enables you to determine whether the event is a key press (KeyDown) or key release (KeyUp):

Alternatively, you can override the onKeyUp() callback and add the expected behavior for each received keycode:

An onKeyUp event occurs when a key is released. Using the callback prevents apps from needing to process multiple onKeyDown events if a key is held down or released slowly. Games and apps that need to detect the moment a key is pressed or whether the user is holding a key down can listen for the onKeyDown event and handle repeated onKeyDown events themselves.

For more information, see Handle keyboard actions.

Common keyboard shortcuts that include the Ctrl, Alt, Shift, and Meta keys are expected when using a hardware keyboard. If an app doesn't implement shortcuts, the experience can feel frustrating to users. Advanced users also appreciate shortcuts for frequently used app‑specific tasks. Shortcuts make an app easier to use and differentiate it from apps that don't have shortcuts.

Some common shortcuts include Ctrl+S (save), Ctrl+Z (undo), and Ctrl+Shift+Z (redo). For a list of default shortcuts, see Handle keyboard actions.

A KeyEvent object has the following attributes which indicate whether modifier keys are pressed:

For more information, see the following:

Many large screen devices come with a stylus. Android apps handle styluses as touchscreen input. Some devices might also have a USB or Bluetooth drawing table, like the Wacom Intuos. Android apps can receive bluetooth input but not USB input.

To access stylus MotionEvent objects, add the pointerInteropFilter modifier to a drawing surface. Implement a ViewModel class with a method that processes motion events; pass the method as the onTouchEvent lambda of the pointerInteropFilter modifier:

The MotionEvent object contains information about the event:

Android batches input events and delivers them once per frame. A stylus can report events at much higher frequencies than the display. When creating drawing apps, check for events that may be in the recent past by using the getHistorical APIs:

When users draw, write, or interact with your app using a stylus, they sometimes touch the screen with the palm of their hands. The touch event (set to ACTION_DOWN or ACTION_POINTER_DOWN) can be reported to your app before the system recognizes and disregards the inadvertent palm touch.

Android cancels palm touch events by dispatching a MotionEvent. If your app receives ACTION_CANCEL, cancel the gesture. If your app receives ACTION_POINTER_UP, check whether FLAG_CANCELED is set. If so, cancel the gesture.

Do not check for just FLAG_CANCELED. On Android 13 (API level 33) and higher, the system sets FLAG_CANCELED for ACTION_CANCEL events, but the system does not set the flag on lower Android versions.

On Android 12 (API level 32) and lower, detection of palm rejection is possible only for single‑pointer touch events. If a palm touch is the only pointer, the system cancels the event by setting ACTION_CANCEL on the motion event object. If other pointers are down, the system sets ACTION_POINTER_UP, which is insufficient for detecting palm rejection.

On Android 13 (API level 33) and higher, if a palm touch is the only pointer, the system cancels the event by setting ACTION_CANCEL and FLAG_CANCELED on the motion event object. If other pointers are down, the system sets ACTION_POINTER_UP and FLAG_CANCELED.

Whenever your app receives a motion event with ACTION_POINTER_UP, check for FLAG_CANCELED to determine whether the event indicates palm rejection (or other event cancellation).

ChromeOS has a special intent that surfaces registered note‑taking apps to users. To register an app as a note‑taking app, add the following to your app manifest:

When an app is registered with the system, the user can select it as the default note‑taking app. When a new note is requested, the app should create an empty note ready for stylus input. When the user wishes to annotate an image (such as a screenshot or downloaded image), the app launches with ClipData containing one or more items with content:// URIs. The app should create a note that uses the first attached image as a background image and enter a mode where the user can draw on the screen with a stylus.

To test whether an app responds correctly to note‑taking intents without an active stylus, use the following method to display the note‑taking options on ChromeOS:

A stylus menu should now be on the shelf:

Most apps generally need to handle only three large screen–centric events: right-click, hover, and drag and drop.

Any actions that cause an app to show a context menu, like touch & hold on a list item, should also react to right‑click events.

To handle right‑click events, apps should register a View.OnContextClickListener:

For details on constructing context menus, see Create a contextual menu.

You can make your app layouts feel polished and easier to use by handling hover events. This is especially true for custom components:

The two most common examples of this are:

In a multi-window environment, users expect to be able to drag and drop items between apps. This is true for desktop devices as well as tablets, phones, and foldables in split‑screen mode.

Consider whether users are likely to drag items into your app. For example, photo editors should expect to receive photos, audio players should expect to receive audio files, and drawing programs should expect to receive photos.

To add drag and drop support, see Drag and drop , and take a look at the Android on ChromeOS — Implementing Drag & Drop blog post.

Special considerations for ChromeOS

An item must have the View.DRAG_FLAG_GLOBAL flag in order to be dragged out to other applications

See Start a drag event

Apps that do advanced handling of mouse and touchpad input should implement a pointerInput modifier to obtain a PointerEvent:

@Composable private fun LogPointerEvents(filter: PointerEventType? = null) { var log by remember { mutableStateOf("") } Column { Text(log) Box( Modifier .size(100.dp) .background(Color.Red) .pointerInput(filter) { awaitPointerEventScope { while (true) { val event = awaitPointerEvent() // handle pointer event if (filter == null || event.type == filter) { log = "${event.type}, ${event.changes.first().position}" } } } } ) } }UnderstandGesturesSnippets.kt

Examine the PointerEvent object to determine the following:

Some large screen Android devices support up to four game controllers. Use the standard Android game controller APIs to handle game controllers (see Support game controllers).

Game controller buttons are mapped to common values following a common mapping. But not all game controller manufacturers follow the same mapping conventions. You can provide a much better experience if you allow users to select different popular controller mappings. See Process gamepad button presses for more information.

ChromeOS enables an input translation mode by default. For most Android apps, this mode helps apps work as expected in a desktop environment. Some examples include automatically enabling two‑finger scrolling on the touchpad, mouse wheel scrolling, and mapping raw display coordinates to window coordinates. Generally, app developers don't need to implement any of these behaviors themselves.

If an app implements custom input behavior, for example defining a custom two‑finger touchpad pinch action, or these input translations don't provide the input events expected by the app, you can disable the input translation mode by adding the following tag to the Android manifest:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (swift):
```swift
Row {
    Column(Modifier.focusGroup()) {
        Button({}) { Text("Row1 Col1") }
        Button({}) { Text("Row2 Col1") }
        Button({}) { Text("Row3 Col1") }
    }
    Column(Modifier.focusGroup()) {
        Button({}) { Text("Row1 Col2") }
        Button({}) { Text("Row2 Col2") }
        Button({}) { Text("Row3 Col2") }
    }
}
```

Example 2 (swift):
```swift
Box(
    modifier = Modifier.focusable().onKeyEvent {
        if(
            it.type == KeyEventType.KeyUp &&
            it.key == Key.S
        ) {
            doSomething()
            true
        } else {
            false
        }
    }
)  {
    Text("Press S key")
}
```

Example 3 (css):
```css
Box(
    Modifier.onKeyEvent {
        if (it.isAltPressed && it.key == Key.A) {
            println("Alt + A is pressed")
            true
        } else {
            false
        }
    }
    .focusable()
)
```

Example 4 (typescript):
```typescript
@Composable
@OptIn(ExperimentalComposeUiApi::class)
fun DrawArea(modifier: Modifier = Modifier) {
   Canvas(modifier = modifier
       .clipToBounds()
       .pointerInteropFilter {
           viewModel.processMotionEvent(it)
       }

   ) {
       // Drawing code here.
   }
}
```

---

## Advanced stylus features Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/advanced-stylus-features

**Contents:**
- Advanced stylus features Stay organized with collections Save and categorize content based on your preferences.
- MotionEvent
  - Event data
  - Actions
  - Pointers
  - Stylus inputs
  - Stylus axis data
    - Position
    - Pressure
    - Orientation

Android and ChromeOS provide a variety of APIs to help you build apps that offer users an exceptional stylus experience. The MotionEvent class exposes information about stylus interaction with the screen, including stylus pressure, orientation, tilt, hover, and palm detection. Low-latency graphics and motion prediction libraries enhance stylus on‑screen rendering to provide a natural, pen‑and‑paper‑like experience.

The MotionEvent class represents user input interactions such as the position and movement of touch pointers on the screen. For stylus input, MotionEvent also exposes pressure, orientation, tilt, and hover data.

To access stylus MotionEvent objects, add the pointerInteropFilter modifier to a drawing surface. Implement a ViewModel class with a method that processes motion events; pass the method as the onTouchEvent lambda of the pointerInteropFilter modifier:

@Composable @OptIn(ExperimentalComposeUiApi::class) fun DrawArea(modifier: Modifier = Modifier) { Canvas( modifier = modifier .clipToBounds() .pointerInteropFilter { viewModel.processMotionEvent(it) } ) { // Drawing code here. } }StylusSnippets.kt

A MotionEvent object provides data related to the following aspects of a UI event:

To implement stylus support, you need to understand what action the user is performing.

MotionEvent provides a wide variety of ACTION constants that define motion events. The most important actions for stylus include the following:

Your app can perform tasks like starting a new stroke when ACTION_DOWN happens, drawing the stroke with ACTION_MOVE, and finishing the stroke when ACTION_UP is triggered.

The set of MotionEvent actions from ACTION_DOWN to ACTION_UP for a given pointer is called a motion set.

Most screens are multi-touch: the system assigns a pointer for each finger, stylus, mouse, or other pointing object interacting with the screen. A pointer index enables you to get axis information for a specific pointer, like the position of the first finger touching the screen or the second.

Pointer indexes range from zero to the number of pointers returned by MotionEvent#pointerCount() minus 1.

Axis values of the pointers can be accessed with the getAxisValue(axis, pointerIndex) method. When the pointer index is omitted, the system returns the value for the first pointer, pointer zero (0).

MotionEvent objects contain information about the type of pointer in use. You can get the pointer type by iterating through the pointer indexes and calling the getToolType(pointerIndex) method.

To learn more about pointers, see Handle multi-touch gestures.

You can filter for stylus inputs with TOOL_TYPE_STYLUS:

The stylus can also report that it is used as an eraser with TOOL_TYPE_ERASER:

ACTION_DOWN and ACTION_MOVE provide axis data about the stylus, namely x and y coordinates, pressure, orientation, tilt, and hover.

To enable access to this data, the MotionEvent API provides getAxisValue(int), where the parameter is any of the following axis identifiers:

For example, MotionEvent.getAxisValue(AXIS_X) returns the x coordinate for the first pointer.

See also Handle multi-touch gestures.

You can retrieve the x and y coordinates of a pointer with the following calls:

You can retrieve the pointer pressure with MotionEvent#getAxisValue(AXIS_PRESSURE) or, for the first pointer, MotionEvent#getPressure().

The pressure value for touchscreens or touchpads is a value between 0 (no pressure) and 1, but higher values can be returned depending on the screen calibration.

Orientation indicates which direction the stylus is pointing.

Pointer orientation can be retrieved using getAxisValue(AXIS_ORIENTATION) or getOrientation() (for the first pointer).

For a stylus, the orientation is returned as a radian value between 0 to pi (𝛑) clockwise or 0 to -pi counterclockwise.

Orientation enables you to implement a real-life brush. For example, if the stylus represents a flat brush, the width of the flat brush depends on the stylus orientation.

Tilt measures the inclination of the stylus relative to the screen.

Tilt returns the positive angle of the stylus in radians, where zero is perpendicular to the screen and 𝛑/2 is flat on the surface.

The tilt angle can be retrieved using getAxisValue(AXIS_TILT) (no shortcut for the first pointer).

Tilt can be used to reproduce as close as possible real-life tools, like mimicking shading with a tilted pencil.

The distance of the stylus from the screen can be obtained with getAxisValue(AXIS_DISTANCE). The method returns a value from 0.0 (contact with the screen) to higher values as the stylus moves away from the screen. The hover distance between the screen and the nib (point) of the stylus depends on the manufacturer of both the screen and the stylus. Because implementations can vary, don't rely on precise values for app-critical functionality.

Stylus hover can be used to preview the size of the brush or indicate that a button is going to be selected.

Note: Compose provides modifiers that affect the interactive state of UI elements:

Sometimes multi-touch screens can register unwanted touches, for example, when a user naturally rests their hand on the screen for support while handwriting. Palm rejection is a mechanism that detects this behavior and notifies you that the last MotionEvent set should be canceled.

As a result, you must keep a history of user inputs so that the unwanted touches can be removed from the screen and the legitimate user inputs can be re-rendered.

ACTION_CANCEL and FLAG_CANCELED are both designed to inform you that the previous MotionEvent set should be canceled from the last ACTION_DOWN, so you can, for example, undo the last stroke for a drawing app for a given pointer.

Added in Android 1.0 (API level 1)

ACTION_CANCEL indicates the previous set of motion events should be canceled.

ACTION_CANCEL is triggered when any of the following is detected:

When ACTION_CANCEL is triggered, you should identify the active pointer with getPointerId(getActionIndex()). Then remove the stroke created with that pointer from the input history, and re-render the scene.

Added in Android 13 (API level 33)

FLAG_CANCELED indicates that the pointer going up was an unintentional user touch. The flag is typically set when the user accidentally touches the screen, such as by gripping the device or placing the palm of the hand on the screen.

You access the flag value as follows:

If the flag is set, you need to undo the last MotionEvent set, from the last ACTION_DOWN from this pointer.

Like ACTION_CANCEL, the pointer can be found with getPointerId(actionIndex).

If an app is full screen and has actionable elements near the edge, such as the canvas of a drawing or note-taking app, swiping from the bottom of the screen to display the navigation or move the app to the background might result in an unwanted touch on the canvas.

To prevent gestures from triggering unwanted touches in your app, you can take advantage of insets and ACTION_CANCEL.

See also the Palm rejection, navigation, and unwanted inputs section.

Use the setSystemBarsBehavior() method and BEHAVIOR_SHOW_TRANSIENT_BARS_BY_SWIPE of WindowInsetsController to prevent navigation gestures from causing unwanted touch events:

To learn more about inset and gesture management, see:

Latency is the time required by the hardware, system, and application to process and render user input.

Latency = hardware and OS input processing + app processing + system compositing

The Jetpack low-latency graphics library reduces the processing time between user input and on-screen rendering.

The library reduces processing time by avoiding multi-buffer rendering and leveraging a front-buffer rendering technique, which means writing directly to the screen.

The front buffer is the memory the screen uses for rendering. It is the closest apps can get to drawing directly to the screen. The low-latency library enables apps to render directly to the front buffer. This improves performance by preventing buffer swapping, which can happen for regular multi-buffer rendering or double-buffer rendering (the most common case).

While front-buffer rendering is a great technique to render a small area of the screen, it is not designed to be used for refreshing the entire screen. With front-buffer rendering, the app is rendering content into a buffer from which the display is reading. As a result, there is the possibility of rendering artifacts or tearing (see below).

The low-latency library is available from Android 10 (API level 29) and higher and on ChromeOS devices running Android 10 (API level 29) and higher.

The low-latency library provides the components for front-buffer rendering implementation. The library is added as a dependency in the app's module build.gradle file:

The low-latency library includes the GLFrontBufferRenderer.Callback interface, which defines the following methods:

The low-latency library is not opinionated about the type of data you use with GLFrontBufferRenderer.

However, the library processes the data as a stream of hundreds of data points; and so, design your data to optimize memory usage and allocation.

To enable rendering callbacks, implement GLFrontBufferedRenderer.Callback and override onDrawFrontBufferedLayer() and onDrawDoubleBufferedLayer(). GLFrontBufferedRenderer uses the callbacks to render your data in the most optimized way possible.

Prepare the GLFrontBufferedRenderer by providing the SurfaceView and callbacks you created earlier. GLFrontBufferedRenderer optimizes the rendering to the front and double buffer using your callbacks:

Front-buffer rendering starts when you call the renderFrontBufferedLayer() method, which triggers the onDrawFrontBufferedLayer() callback.

Double-buffer rendering resumes when you call the commit() function, which triggers the onDrawMultiDoubleBufferedLayer() callback.

In the example that follows, the process renders to the front buffer (fast rendering) when the user starts drawing on the screen (ACTION_DOWN) and moves the pointer around (ACTION_MOVE). The process renders to the double buffer when the pointer leaves the surface of the screen (ACTION_UP).

You can use requestUnbufferedDispatch() to ask that the input system doesn't batch motion events but instead delivers them as soon as they're available:

Small portions of the screen, handwriting, drawing, sketching.

Fullscreen update, panning, zooming. Can result in tearing.

Tearing happens when the screen refreshes while the screen buffer is being modified at the same time. A part of the screen shows new data, while another shows old data.

The Jetpack motion prediction library reduces perceived latency by estimating the user's stroke path and providing temporary, artificial points to the renderer.

The motion prediction library gets real user inputs as MotionEvent objects. The objects contain information about x and y coordinates, pressure, and time, which are leveraged by the motion predictor to predict future MotionEvent objects.

Predicted MotionEvent objects are only estimates. Predicted events can reduce perceived latency, but predicted data must be replaced with actual MotionEvent data once it is received.

The motion prediction library is available from Android 4.4 (API level 19) and higher and on ChromeOS devices running Android 9 (API level 28) and higher.

The motion prediction library provides the implementation of prediction. The library is added as a dependency in the app's module build.gradle file:

The motion prediction library includes the MotionEventPredictor interface, which defines the following methods:

Remove prediction points when a new predicted point is added.

Don't use prediction points for final rendering.

ChromeOS enables your app to declare some note-taking actions.

To register an app as a note-taking app on ChromeOS, see Input compatibility.

To register an app as a note-taking on Android, see Create a note-taking app.

Android 14 (API level 34), introduced the ACTION_CREATE_NOTE intent, which enables your app to start a note-taking activity on the lock screen.

With the ML Kit digital ink recognition, your app can recognize handwritten text on a digital surface in hundreds of languages. You can also classify sketches.

ML Kit provides the Ink.Stroke.Builder class to create Ink objects that can be processed by machine learning models to convert handwriting to text.

In addition to handwriting recognition, the model is able to recognize gestures, such as delete and circle.

See Digital ink recognition to learn more.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

**Examples:**

Example 1 (unknown):
```unknown
val isStylus = TOOL_TYPE_STYLUS == event.getToolType(pointerIndex)
```

Example 2 (unknown):
```unknown
val isEraser = TOOL_TYPE_ERASER == event.getToolType(pointerIndex)
```

Example 3 (unknown):
```unknown
val cancel = (event.flags and FLAG_CANCELED) == FLAG_CANCELED
```

Example 4 (unknown):
```unknown
// Configure the behavior of the hidden system bars.
windowInsetsController.systemBarsBehavior =
    WindowInsetsControllerCompat.BEHAVIOR_SHOW_TRANSIENT_BARS_BY_SWIPE
```

---

## Create a note-taking app Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/stylus-input/create-a-note-taking-app

**Contents:**
- Create a note-taking app Stay organized with collections Save and categorize content based on your preferences.
- Notes role
- App manifest
- App features
  - Stylus support
  - Lockscreen access
  - Floating windows
  - Content capture
- Additional resources

Note-taking is a core capability of Android that enhances user productivity on large screen devices. Note‑taking apps enable users to write and sketch in a floating window or on the full screen, capture and annotate screen content, and save notes for later review and revision.

Users can access note-taking apps from the lock screen or while running other apps.

Stylus support for note-taking provides an exceptional user experience.

The RoleManager.ROLE_NOTES role identifies note‑taking apps and grants them the LAUNCH_CAPTURE_CONTENT_ACTIVITY_FOR_NOTE permission.

To acquire the notes role for your app, do the following:

Only one app can possess the notes role.

The app opens in response to an implicit ACTION_CREATE_NOTE intent action. If invoked from the device lock screen, the app opens full screen; if invoked while the screen is unlocked, in a floating window.

To qualify for the notes role, your app must include the following declaration in the app manifest:

The declaration enables users to assign the notes role to your app, making it the default note‑taking application:

ACTION_CREATE_NOTE sets the intent action to which your app responds

showWhenLocked makes your app accessible from the device lock screen

turnScreenOn enables your app to turn the device screen on when the app runs

A large screen differentiated note‑taking app provides a full complement of note‑taking capabilities.

When your app is invoked with the EXTRA_USE_STYLUS_MODE intent extra set to true, the app should open a note that accepts stylus (or finger-touch) input.

If the intent extra is set to false, your app should open a note that accepts keyboard input.

Your app must provide a full‑screen activity that runs when the app is opened from the device lock screen.

Your app should show only historical notes if the user has consented (in the unlocked device state) to showing past notes. Otherwise, when opened from the lock screen, your app should always create a new note.

You can check whether your app has been launched from the lock screen with KeyguardManager#isKeyguardLocked(). To ask the user to authenticate and unlock the device, call KeyguardManager#requestDismissKeyguard():

For contextual note-taking, your app must provide an activity that opens in a floating window when another application is running.

Your app should support multi-instance mode so that users can create multiple notes in multiple floating windows even when your note‑taking app is launched full screen or in split‑screen mode.

Content capture is a key capability of note‑taking apps. With content capture, users can take screenshots of the display behind the note‑taking app's floating window. Users can capture all or part of the display, paste the content into their note, and annotate or highlight the captured content.

Your note-taking app should provide a UI affordance that launches an ActivityResultLauncher created by registerForActivityResult(). The ACTION_LAUNCH_CAPTURE_CONTENT_ACTIVITY_FOR_NOTE intent action is provided to the launcher either directly or through an ActivityResultContract.

A system activity captures the content, saves it on the device, and returns the content URI to your app in the callback argument of registerForActivityResult().

The following example uses a generic StartActivityForResult contract:

Your app should handle all result codes:

When content capture succeeds, paste the captured image into the note, for example:

The content capture feature should be exposed through a UI affordance only when your note‑taking app is running in a floating window—not when running full screen, launched from the device lock screen. (Users can take screenshots of the note‑taking app itself with device screenshot capabilities.)

To determine whether your app is in a floating window (or bubble), call the following methods:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-07-22 UTC.

**Examples:**

Example 1 (jsx):
```jsx
<activity
    android:name="YourActivityName"
    android:exported="true"
    android:showWhenLocked="true"
    android:turnScreenOn="true">
    <intent-filter>
        <action android:name="android.intent.action.CREATE_NOTE" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```

Example 2 (json):
```json
val keyguardManager =
getSystemService(KEYGUARD_SERVICE) as KeyguardManager
keyguardManager.requestDismissKeyguard(  this, object :
KeyguardDismissCallback() {  override fun onDismissError() {  // Unlock failed.
Dismissing keyguard is not feasible.  }  override fun onDismissSucceeded() {  //
Unlock succeeded. Device is now unlocked.  }  override fun onDismissCancelled()
{  // Unlock failed. User cancelled operation or request otherwise cancelled.  }
 } )
```

Example 3 (swift):
```swift
private val startForResult =
registerForActivityResult(  ActivityResultContracts.StartActivityForResult()) {
 result: ActivityResult ->  if (result.resultCode ==
Intent.CAPTURE_CONTENT_FOR_NOTE_SUCCESS) {  val uri = result.data?.data  // Use
the URI to paste the captured content into the note.  }  } override fun
onCreate(savedInstanceState: Bundle?) {  super.onCreate(savedInstanceState)
setContent {  NotesTheme {  Surface(color =
MaterialTheme.colorScheme.background) {  CaptureButton(  onClick = {
Log.i("ContentCapture", "Launching intent...")
startForResult.launch(Intent(ACTION_LAUNCH_CAPTURE_CONTENT_ACTIVITY_FOR_NOTE))
})  }  }  } } @Composable fun CaptureButton(onClick: () -> Unit) {
Button(onClick = onClick)
 {Text("Capture Content")} }
```

Example 4 (css):
```css
registerForActivityResult(ActivityResultContracts.StartActivityForResult()) {
 result: ActivityResult ->  if (result.resultCode ==
Intent.CAPTURE_CONTENT_FOR_NOTE_SUCCESS) {  val uri = result.data?data  // Use
the URI to paste the captured content into the note.  } }
```

---

## Migrate to Indication and Ripple APIs Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/touch-input/user-interactions/migrate-indication-ripple

**Contents:**
- Migrate to Indication and Ripple APIs Stay organized with collections Save and categorize content based on your preferences.
- Behavior change
- Migrate from rememberRipple to ripple
  - Using a Material library
  - Implementing custom design system
- Migrate from RippleTheme
  - Using RippleTheme to disable a ripple for a given component
  - Using RippleTheme to change the color/alpha of a ripple for a given component
  - Using RippleTheme to globally change all ripples in an application
- Migrate from Indication to IndicationNodeFactory

To improve composition performance of interactive components that use Modifier.clickable, we've introduced new APIs. These APIs allow for more efficient Indication implementations, such as ripples.

androidx.compose.foundation:foundation:1.7.0+ and androidx.compose.material:material-ripple:1.7.0+ include the following API changes:

Indication#rememberUpdatedInstance

IndicationNodeFactory

New ripple() APIs provided in Material libraries instead.

Note: In this context, "Material libraries" refers to androidx.compose.material:material, androidx.compose.material3:material3, androidx.wear.compose:compose-material and androidx.wear.compose:compose-material3.

This page describes the behavior change impact and instructions for migrating to the new APIs.

The following library versions include a ripple behavior change:

These versions of Material libraries no longer use rememberRipple(); instead, they use the new ripple APIs. As a result, they do not query LocalRippleTheme. Therefore, if you set LocalRippleTheme in your application, Material components will not use these values.

The following sections describe how to migrate to the new APIs.

If you are using a Material library, directly replace rememberRipple() with a call to ripple() from the corresponding library. This API creates a ripple using values derived from the Material theme APIs. Then, pass the returned object to Modifier.clickable and/or other components.

For example, the following snippet uses the deprecated APIs:

Box( Modifier.clickable( onClick = {}, interactionSource = remember { MutableInteractionSource() }, indication = rememberRipple() ) ) { // ... }UserInteractions.kt

You should modify the above snippet to:

@Composable private fun RippleExample() { Box( Modifier.clickable( onClick = {}, interactionSource = remember { MutableInteractionSource() }, indication = ripple() ) ) { // ... } }UserInteractions.kt

Note that ripple() is no longer a composable function and does not need to be remembered. It can also be reused across multiple components, similar to modifiers, so consider extracting the ripple creation to a top-level value to save allocations.

If you're implementing your own design system, and you were previously using rememberRipple() along with a custom RippleTheme to configure the ripple, you should instead provide your own ripple API that delegates to the ripple node APIs exposed in material-ripple. Then, your components can use your own ripple that consumes your theme values directly. For more information, see Migrate fromRippleTheme.

The material and material3 libraries expose RippleConfiguration and LocalRippleConfiguration, which allow you to configure the appearance of ripples within a subtree. Note that RippleConfiguration and LocalRippleConfiguration are experimental, and only intended for per-component customization. Global/theme-wide customization is not supported with these APIs; see Using RippleTheme to globally change all ripples in an application for more information on that use case.

For example, the following snippet uses the deprecated APIs:

private object DisabledRippleTheme : RippleTheme { @Composable override fun defaultColor(): Color = Color.Transparent @Composable override fun rippleAlpha(): RippleAlpha = RippleAlpha(0f, 0f, 0f, 0f) } // ... CompositionLocalProvider(LocalRippleTheme provides DisabledRippleTheme) { Button { // ... } }UserInteractions.kt

You should modify the above snippet to:

CompositionLocalProvider(LocalRippleConfiguration provides null) { Button { // ... } }UserInteractions.kt

As described in the previous section, RippleConfiguration and LocalRippleConfiguration are experimental APIs and are only intended for per-component customization.

For example, the following snippet uses the deprecated APIs:

private object DisabledRippleThemeColorAndAlpha : RippleTheme { @Composable override fun defaultColor(): Color = Color.Red @Composable override fun rippleAlpha(): RippleAlpha = MyRippleAlpha } // ... CompositionLocalProvider(LocalRippleTheme provides DisabledRippleThemeColorAndAlpha) { Button { // ... } }UserInteractions.kt

You should modify the above snippet to:

@OptIn(ExperimentalMaterialApi::class) private val MyRippleConfiguration = RippleConfiguration(color = Color.Red, rippleAlpha = MyRippleAlpha) // ... CompositionLocalProvider(LocalRippleConfiguration provides MyRippleConfiguration) { Button { // ... } }UserInteractions.kt

Previously, you could use LocalRippleTheme to define ripple behavior at a theme-wide level. This was essentially an integration point between custom design system composition locals and ripple. Instead of exposing a generic theming primitive, material-ripple now exposes a createRippleModifierNode() function. This function allows for design system libraries to create higher order wrapper implementation, that query their theme values and then delegate the ripple implementation to the node created by this function.

This allows for design systems to directly query what they need, and expose any required user-configurable theming layers on top without having to conform to what is provided at the material-ripple layer. This change also makes more explicit what theme/specification the ripple is conforming to, as it is the ripple API itself that defines that contract, rather than being implicitly derived from the theme.

For guidance, see the ripple API implementation in Material libraries, and replace the calls to Material composition locals as needed for your own design system.

If you are just creating an Indication to pass around, such as creating a ripple to pass to Modifier.clickable or Modifier.indication, you don't need to make any changes. IndicationNodeFactory inherits from Indication, so everything will continue to compile and work.

If you are creating your own Indication implementation, the migration should be simple in most cases. For example, consider an Indication that applies a scale effect on press:

object ScaleIndication : Indication { @Composable override fun rememberUpdatedInstance(interactionSource: InteractionSource): IndicationInstance { // key the remember against interactionSource, so if it changes we create a new instance val instance = remember(interactionSource) { ScaleIndicationInstance() } LaunchedEffect(interactionSource) { interactionSource.interactions.collectLatest { interaction -> when (interaction) { is PressInteraction.Press -> instance.animateToPressed(interaction.pressPosition) is PressInteraction.Release -> instance.animateToResting() is PressInteraction.Cancel -> instance.animateToResting() } } } return instance } } private class ScaleIndicationInstance : IndicationInstance { var currentPressPosition: Offset = Offset.Zero val animatedScalePercent = Animatable(1f) suspend fun animateToPressed(pressPosition: Offset) { currentPressPosition = pressPosition animatedScalePercent.animateTo(0.9f, spring()) } suspend fun animateToResting() { animatedScalePercent.animateTo(1f, spring()) } override fun ContentDrawScope.drawIndication() { scale( scale = animatedScalePercent.value, pivot = currentPressPosition ) { this@drawIndication.drawContent() } } }UserInteractions.kt

You can migrate this in two steps:

Migrate ScaleIndicationInstance to be a DrawModifierNode. The API surface for DrawModifierNode is very similar to IndicationInstance: it exposes a ContentDrawScope#draw() function that is functionally equivalent to IndicationInstance#drawContent(). You need to change that function, and then implement the collectLatest logic inside the node directly, instead of the Indication.

For example, the following snippet uses the deprecated APIs:

private class ScaleIndicationInstance : IndicationInstance { var currentPressPosition: Offset = Offset.Zero val animatedScalePercent = Animatable(1f) suspend fun animateToPressed(pressPosition: Offset) { currentPressPosition = pressPosition animatedScalePercent.animateTo(0.9f, spring()) } suspend fun animateToResting() { animatedScalePercent.animateTo(1f, spring()) } override fun ContentDrawScope.drawIndication() { scale( scale = animatedScalePercent.value, pivot = currentPressPosition ) { this@drawIndication.drawContent() } } }UserInteractions.kt

You should modify the above snippet to:

private class ScaleIndicationNode( private val interactionSource: InteractionSource ) : Modifier.Node(), DrawModifierNode { var currentPressPosition: Offset = Offset.Zero val animatedScalePercent = Animatable(1f) private suspend fun animateToPressed(pressPosition: Offset) { currentPressPosition = pressPosition animatedScalePercent.animateTo(0.9f, spring()) } private suspend fun animateToResting() { animatedScalePercent.animateTo(1f, spring()) } override fun onAttach() { coroutineScope.launch { interactionSource.interactions.collectLatest { interaction -> when (interaction) { is PressInteraction.Press -> animateToPressed(interaction.pressPosition) is PressInteraction.Release -> animateToResting() is PressInteraction.Cancel -> animateToResting() } } } } override fun ContentDrawScope.draw() { scale( scale = animatedScalePercent.value, pivot = currentPressPosition ) { this@draw.drawContent() } } }UserInteractions.kt

Migrate ScaleIndication to implement IndicationNodeFactory. Because the collection logic is now moved into the node, this is a very simple factory object whose only responsibility is to create a node instance.

For example, the following snippet uses the deprecated APIs:

object ScaleIndication : Indication { @Composable override fun rememberUpdatedInstance(interactionSource: InteractionSource): IndicationInstance { // key the remember against interactionSource, so if it changes we create a new instance val instance = remember(interactionSource) { ScaleIndicationInstance() } LaunchedEffect(interactionSource) { interactionSource.interactions.collectLatest { interaction -> when (interaction) { is PressInteraction.Press -> instance.animateToPressed(interaction.pressPosition) is PressInteraction.Release -> instance.animateToResting() is PressInteraction.Cancel -> instance.animateToResting() } } } return instance } }UserInteractions.kt

You should modify the above snippet to:

object ScaleIndicationNodeFactory : IndicationNodeFactory { override fun create(interactionSource: InteractionSource): DelegatableNode { return ScaleIndicationNode(interactionSource) } override fun hashCode(): Int = -1 override fun equals(other: Any?) = other === this }UserInteractions.kt

In most cases, you should use Modifier.indication to display Indication for a component. However, in the rare case that you are manually creating an IndicationInstance using rememberUpdatedInstance, you need to update your implementation to check if the Indication is an IndicationNodeFactory so you can use a lighter implementation. For example, Modifier.indication will internally delegate to the created node if it is an IndicationNodeFactory. If not, it will use Modifier.composed to call rememberUpdatedInstance.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---
