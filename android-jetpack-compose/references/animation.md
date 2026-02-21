# Jetpack-Compose - Animation

**Pages:** 4

---

## Customize animations Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/customize

**Contents:**
- Customize animations Stay organized with collections Save and categorize content based on your preferences.
- Customize animations with the AnimationSpec parameter
  - Create physics-based animation with spring
  - Animate between start and end values with easing curve with tween
  - Animate to specific values at certain timings with keyframes
  - Animate between keyframes smoothly with keyframesWithSplines
  - Repeat an animation with repeatable
  - Repeat an animation infinitely with infiniteRepeatable
  - Immediately snap to end value with snap
- Set a custom easing function

Many of the Animation APIs commonly accept parameters for customizing their behavior.

Most animation APIs allow developers to customize animation specifications by an optional AnimationSpec parameter.

val alpha: Float by animateFloatAsState( targetValue = if (enabled) 1f else 0.5f, // Configure the animation duration and easing. animationSpec = tween(durationMillis = 300, easing = FastOutSlowInEasing), label = "alpha" )AnimationSnippets.kt

There are different kinds of AnimationSpec for creating different types of animation.

spring creates a physics-based animation between start and end values. It takes 2 parameters: dampingRatio and stiffness.

dampingRatio defines how bouncy the spring should be. The default value is Spring.DampingRatioNoBouncy.

stiffness defines how fast the spring should move toward the end value. The default value is Spring.StiffnessMedium.

val value by animateFloatAsState( targetValue = 1f, animationSpec = spring( dampingRatio = Spring.DampingRatioHighBouncy, stiffness = Spring.StiffnessMedium ), label = "spring spec" )AnimationSnippets.kt

spring can handle interruptions more smoothly than duration-based AnimationSpec types because it guarantees the continuity of velocity when target value changes amid animations. spring is used as the default AnimationSpec by many animation APIs, such as animate*AsState and updateTransition.

For example, if we apply a spring config to the following animation that is driven by user touch, when interrupting the animation as its progressing, you can see that using tween doesn't respond as smoothly as using spring.

tween animates between start and end values over the specified durationMillis using an easing curve. tween is short for the word between - as it goes between two values.

You can also specify delayMillis to postpone the start of the animation.

val value by animateFloatAsState( targetValue = 1f, animationSpec = tween( durationMillis = 300, delayMillis = 50, easing = LinearOutSlowInEasing ), label = "tween delay" )AnimationSnippets.kt

See Easing for more information.

keyframes animates based on the snapshot values specified at different timestamps in the duration of the animation. At any given time, the animation value will be interpolated between two keyframe values. For each of these keyframes, Easing can be specified to determine the interpolation curve.

It is optional to specify the values at 0 ms and at the duration time. If you do not specify these values, they default to the start and end values of the animation, respectively.

val value by animateFloatAsState( targetValue = 1f, animationSpec = keyframes { durationMillis = 375 0.0f at 0 using LinearOutSlowInEasing // for 0-15 ms 0.2f at 15 using FastOutLinearInEasing // for 15-75 ms 0.4f at 75 // ms 0.4f at 225 // ms }, label = "keyframe" )AnimationSnippets.kt

To create an animation that follows a smooth curve as it transitions between values, you can use keyframesWithSplines instead of keyframes animation specs.

val offset by animateOffsetAsState( targetValue = Offset(300f, 300f), animationSpec = keyframesWithSpline { durationMillis = 6000 Offset(0f, 0f) at 0 Offset(150f, 200f) atFraction 0.5f Offset(0f, 100f) atFraction 0.7f } )AnimationSnippets.kt

Spline-based keyframes are particularly useful for 2D movement of items on screen.

The following videos showcase the differences between keyframes and keyframesWithSpline given the same set of x, y coordinates that a circle should follow.

As you can see, the spline-based keyframes offer smoother transitions between points, as they use bezier curves to smoothly animate between items. This spec is useful for a preset animation. However,if you're working with user-driven points, it's preferable to use springs to achieve a similar smoothness between points because those are interruptible.

repeatable runs a duration-based animation (such as tween or keyframes) repeatedly until it reaches the specified iteration count. You can pass the repeatMode parameter to specify whether the animation should repeat by starting from the beginning (RepeatMode.Restart) or from the end (RepeatMode.Reverse).

val value by animateFloatAsState( targetValue = 1f, animationSpec = repeatable( iterations = 3, animation = tween(durationMillis = 300), repeatMode = RepeatMode.Reverse ), label = "repeatable spec" )AnimationSnippets.kt

infiniteRepeatable is like repeatable, but it repeats for an infinite amount of iterations.

val value by animateFloatAsState( targetValue = 1f, animationSpec = infiniteRepeatable( animation = tween(durationMillis = 300), repeatMode = RepeatMode.Reverse ), label = "infinite repeatable" )AnimationSnippets.kt

In tests using ComposeTestRule, animations using infiniteRepeatable are not run. The component will be rendered using the initial value of each animated value.

snap is a special AnimationSpec that immediately switches the value to the end value. You can specify delayMillis in order to delay the start of the animation.

val value by animateFloatAsState( targetValue = 1f, animationSpec = snap(delayMillis = 50), label = "snap spec" )AnimationSnippets.kt

Duration-based AnimationSpec operations (such as tween or keyframes) use Easing to adjust an animation's fraction. This allows the animating value to speed up and slow down, rather than moving at a constant rate. Fraction is a value between 0 (start) and 1.0 (end) indicating the current point in the animation.

Easing is in fact a function that takes a fraction value between 0 and 1.0 and returns a float. The returned value can be outside the boundary to represent overshoot or undershoot. A custom Easing can be created like the code below.

val CustomEasing = Easing { fraction -> fraction * fraction } @Composable fun EasingUsage() { val value by animateFloatAsState( targetValue = 1f, animationSpec = tween( durationMillis = 300, easing = CustomEasing ), label = "custom easing" ) // …… }AnimationSnippets.kt

Compose provides several built-in Easing functions that cover most use cases. See Speed - Material Design for more information about what Easing to use depending on your scenario.

Most Compose animation APIs support Float, Color, Dp, and other basic data types as animation values by default, but you sometimes need to animate other data types including your custom ones. During animation, any animating value is represented as an AnimationVector. The value is converted into an AnimationVector and vice versa by a corresponding TwoWayConverter so that the core animation system can handle them uniformly. For example, an Int is represented as an AnimationVector1D that holds a single float value. TwoWayConverter for Int looks like this:

val IntToVector: TwoWayConverter<Int, AnimationVector1D> = TwoWayConverter({ AnimationVector1D(it.toFloat()) }, { it.value.toInt() })AnimationSnippets.kt

Color is essentially a set of 4 values, red, green, blue, and alpha, so Color is converted into an AnimationVector4D that holds 4 float values. In this manner, every data type used in animations is converted to either AnimationVector1D, AnimationVector2D, AnimationVector3D, or AnimationVector4D depending on its dimensionality. This allows different components of the object to be animated independently, each with their own velocity tracking. Built-in converters for basic data types can be accessed using converters such as Color.VectorConverter or Dp.VectorConverter.

When you want to add support for a new data type as an animating value, you can create your own TwoWayConverter and provide it to the API. For example, you can use animateValueAsState to animate your custom data type like this:

data class MySize(val width: Dp, val height: Dp) @Composable fun MyAnimation(targetSize: MySize) { val animSize: MySize by animateValueAsState( targetSize, TwoWayConverter( convertToVector = { size: MySize -> // Extract a float value from each of the `Dp` fields. AnimationVector2D(size.width.value, size.height.value) }, convertFromVector = { vector: AnimationVector2D -> MySize(vector.v1.dp, vector.v2.dp) } ), label = "size" ) }AnimationSnippets.kt

The following list includes some built-in VectorConverters:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## 

**URL:** https://developer.android.com/develop/ui/compose/animation/introduction

**Contents:**
  - Animations in Compose
- Animation modifiers and composables
  - Animate appearance and disappearance
  - Animate content size changes
  - Animate between different composables
- Value-based animations
  - Animate a single value
  - Animate multiple values together
  - Animate properties indefinitely
  - Customize your animations

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-07-01 UTC.

---

## Value-based animations Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/value-based

**Contents:**
- Value-based animations Stay organized with collections Save and categorize content based on your preferences.
- Animate a single value with animate*AsState
- Animate multiple properties simultaneously with a transition
  - Use transition with AnimatedVisibility and AnimatedContent
  - Encapsulate a transition and make it reusable
- Create an infinitely repeating animation with rememberInfiniteTransition
- Low-level animation APIs
  - Animatable: Coroutine-based single value animation
  - Animation: Manually controlled animation
    - TargetBasedAnimation

This page describes how to create value-based animations in Jetpack Compose, focusing on APIs that animate values based on their current and target states.

The animate*AsState functions are straightforward animation APIs in Compose for animating a single value. You provide only the target value (or end value), and the API starts animation from the current value to the specified value.

The following example animates alpha using this API. By wrapping the target value in animateFloatAsState, the alpha value is now an animation value between the provided values (1f or 0.5f in this case).

var enabled by remember { mutableStateOf(true) } val animatedAlpha: Float by animateFloatAsState(if (enabled) 1f else 0.5f, label = "alpha") Box( Modifier .fillMaxSize() .graphicsLayer { alpha = animatedAlpha } .background(Color.Red) )AnimationSnippets.kt

You don't need to create an instance of any animation class or handle interruption. Under the hood, an animation object (namely, an Animatable instance) will be created and remembered at the call site, with the first target value as its initial value. From there on, any time you supply this composable a different target value, an animation is automatically started towards that value. If there's already an animation in flight, the animation starts from its current value (and velocity) and animates toward the target value. During the animation, this composable gets recomposed and returns an updated animation value every frame.

By default, Compose provides animate*AsState functions for Float, Color, Dp, Size, Offset, Rect, Int, IntOffset, and IntSize. You can add support for other data types by providing a TwoWayConverter to animateValueAsState that takes a generic type.

You can customize the animation specifications by providing an AnimationSpec. See AnimationSpec for more information.

Transition manages one or more animations as its children and runs them simultaneously between multiple states.

The states can be any data type. In many cases, you can use a custom enum type to verify type safety, as in this example:

enum class BoxState { Collapsed, Expanded }AnimationSnippets.kt

updateTransition creates and remembers an instance of Transition and updates its state.

var currentState by remember { mutableStateOf(BoxState.Collapsed) } val transition = updateTransition(currentState, label = "box state")AnimationSnippets.kt

You can then use one of animate* extension functions to define a child animation in this transition. Specify the target values for each of the states. These animate* functions return an animation value that is updated every frame during the animation when the transition state is updated with updateTransition.

val rect by transition.animateRect(label = "rectangle") { state -> when (state) { BoxState.Collapsed -> Rect(0f, 0f, 100f, 100f) BoxState.Expanded -> Rect(100f, 100f, 300f, 300f) } } val borderWidth by transition.animateDp(label = "border width") { state -> when (state) { BoxState.Collapsed -> 1.dp BoxState.Expanded -> 0.dp } }AnimationSnippets.kt

Optionally, you can pass a transitionSpec parameter to specify a different AnimationSpec for each of the combinations of transition state changes. See AnimationSpec for more information.

val color by transition.animateColor( transitionSpec = { when { BoxState.Expanded isTransitioningTo BoxState.Collapsed -> spring(stiffness = 50f) else -> tween(durationMillis = 500) } }, label = "color" ) { state -> when (state) { BoxState.Collapsed -> MaterialTheme.colorScheme.primary BoxState.Expanded -> MaterialTheme.colorScheme.background } }AnimationSnippets.kt

Once a transition has arrived at the target state, Transition.currentState is the same as Transition.targetState. You can use this as a signal for whether the transition has finished.

Sometimes, you might want to have an initial state different from the first target state. You can use updateTransition with MutableTransitionState to achieve this. For example, it lets you start animation as soon as the code enters composition.

// Start in collapsed state and immediately animate to expanded var currentState = remember { MutableTransitionState(BoxState.Collapsed) } currentState.targetState = BoxState.Expanded val transition = rememberTransition(currentState, label = "box state") // ……AnimationSnippets.kt

For a more complex transition involving multiple composable functions, you can use createChildTransition to create a child transition. This technique is useful for separating concerns among multiple subcomponents in a complex composable. The parent transition is aware of all the animation values in the child transitions.

enum class DialerState { DialerMinimized, NumberPad } @Composable fun DialerButton(isVisibleTransition: Transition<Boolean>) { // `isVisibleTransition` spares the need for the content to know // about other DialerStates. Instead, the content can focus on // animating the state change between visible and not visible. } @Composable fun NumberPad(isVisibleTransition: Transition<Boolean>) { // `isVisibleTransition` spares the need for the content to know // about other DialerStates. Instead, the content can focus on // animating the state change between visible and not visible. } @Composable fun Dialer(dialerState: DialerState) { val transition = updateTransition(dialerState, label = "dialer state") Box { // Creates separate child transitions of Boolean type for NumberPad // and DialerButton for any content animation between visible and // not visible NumberPad( transition.createChildTransition { it == DialerState.NumberPad } ) DialerButton( transition.createChildTransition { it == DialerState.DialerMinimized } ) } }AnimationSnippets.kt

AnimatedVisibility and AnimatedContent are available as extension functions of Transition. The targetState for Transition.AnimatedVisibility and Transition.AnimatedContent is derived from the Transition, and trigger enter, exit, and sizeTransform animations as needed when the Transition's targetState changes. These extension functions let you hoist all enter, exit, and sizeTransform animations that would otherwise be internal to AnimatedVisibility/AnimatedContent into the Transition. With these extension functions, you can observe AnimatedVisibility/AnimatedContent's state change from outside. Instead of a boolean visible parameter, this version of AnimatedVisibility takes a lambda that converts the parent transition's target state into a boolean.

See AnimatedVisibility and AnimatedContent for the details.

var selected by remember { mutableStateOf(false) } // Animates changes when `selected` is changed. val transition = updateTransition(selected, label = "selected state") val borderColor by transition.animateColor(label = "border color") { isSelected -> if (isSelected) Color.Magenta else Color.White } val elevation by transition.animateDp(label = "elevation") { isSelected -> if (isSelected) 10.dp else 2.dp } Surface( onClick = { selected = !selected }, shape = RoundedCornerShape(8.dp), border = BorderStroke(2.dp, borderColor), shadowElevation = elevation ) { Column( modifier = Modifier .fillMaxWidth() .padding(16.dp) ) { Text(text = "Hello, world!") // AnimatedVisibility as a part of the transition. transition.AnimatedVisibility( visible = { targetSelected -> targetSelected }, enter = expandVertically(), exit = shrinkVertically() ) { Text(text = "It is fine today.") } // AnimatedContent as a part of the transition. transition.AnimatedContent { targetState -> if (targetState) { Text(text = "Selected") } else { Icon(imageVector = Icons.Default.Phone, contentDescription = "Phone") } } } }AnimationSnippets.kt

For straightforward use cases, defining transition animations in the same composable as your UI is a valid option. When working on a complex component with a number of animated values, however, you might want to separate the animation implementation from the composable UI.

You can do so by creating a class that holds all the animation values and an update function that returns an instance of that class. You can extract the transition implementation into the new separate function. This pattern is useful when you need to centralize the animation logic or make complex animations reusable.

enum class BoxState { Collapsed, Expanded } @Composable fun AnimatingBox(boxState: BoxState) { val transitionData = updateTransitionData(boxState) // UI tree Box( modifier = Modifier .background(transitionData.color) .size(transitionData.size) ) } // Holds the animation values. private class TransitionData( color: State<Color>, size: State<Dp> ) { val color by color val size by size } // Create a Transition and return its animation values. @Composable private fun updateTransitionData(boxState: BoxState): TransitionData { val transition = updateTransition(boxState, label = "box state") val color = transition.animateColor(label = "color") { state -> when (state) { BoxState.Collapsed -> Color.Gray BoxState.Expanded -> Color.Red } } val size = transition.animateDp(label = "size") { state -> when (state) { BoxState.Collapsed -> 64.dp BoxState.Expanded -> 128.dp } } return remember(transition) { TransitionData(color, size) } }AnimationSnippets.kt

InfiniteTransition holds one or more child animations like Transition, but the animations start running as soon as they enter the composition and don't stop unless they're removed. You can create an instance of InfiniteTransition with rememberInfiniteTransition, and add child animations with animateColor, animatedFloat, or animatedValue. You also need to specify an infiniteRepeatable to specify the animation specifications.

val infiniteTransition = rememberInfiniteTransition(label = "infinite") val color by infiniteTransition.animateColor( initialValue = Color.Red, targetValue = Color.Green, animationSpec = infiniteRepeatable( animation = tween(1000, easing = LinearEasing), repeatMode = RepeatMode.Reverse ), label = "color" ) Box( Modifier .fillMaxSize() .background(color) )AnimationSnippets.kt

All the high-level animation APIs mentioned in the preceding section build on the low-level animation APIs.

The animate*AsState functions are straightforward APIs that render an instant value change as an animation value. This functionality is backed by Animatable, a coroutine-based API for animating a single value.

updateTransition creates a transition object that can manage multiple animating values and run them when a state changes. rememberInfiniteTransition is similar, but it creates an infinite transition that can manage multiple animations that continue indefinitely. All of these APIs are composables except for Animatable, which means you can create these animations outside of composition.

All these APIs are based on the more fundamental Animation API. Though most apps won't interact directly with Animation, you can access some of its customization capabilities through higher-level APIs. See Customize animations for more information on AnimationVector and AnimationSpec.

Animatable is a value holder that can animate the value as it is changed using animateTo. This is the API backing up the implementation of animate*AsState. It ensures consistent continuation and mutual exclusiveness, which means the value change is always continuous and Compose cancels any ongoing animation.

Many features of Animatable, including animateTo, are suspend functions. This means you must wrap them in an appropriate coroutine scope. For example, you can use the LaunchedEffect composable to create a scope just for the duration of the specified key value.

// Start out gray and animate to green/red based on `ok` val color = remember { Animatable(Color.Gray) } LaunchedEffect(ok) { color.animateTo(if (ok) Color.Green else Color.Red) } Box( Modifier .fillMaxSize() .background(color.value) )AnimationSnippets.kt

In the preceding example, you create and remember an instance of Animatable with the initial value of Color.Gray. Depending on the value of the boolean flag ok, the color animates to either Color.Green or Color.Red. Any subsequent change to the boolean value starts an animation to the other color. If an animation is in progress when the value changes, Compose cancels the animation, and the new animation starts from the current snapshot value with the current velocity.

This Animatable API is the underlying implementation for animate*AsState mentioned in the previous section. Using Animatable directly offers finer-grained control in several ways:

See Gesture and animation for more information.

By default, Animatable supports Float and Color, but you can use any data type by providing a TwoWayConverter. See AnimationVector for more information.

You can customize the animation specifications by providing an AnimationSpec. See AnimationSpec for more information.

Animation is the lowest-level Animation API available. Many of the animations we've seen so far build on Animation. There are two Animation subtypes: TargetBasedAnimation and DecayAnimation.

Use Animation only to manually control the animation's time. Animation is stateless, and it doesn't have any concept of lifecycle. It serves as an animation calculation engine for higher-level APIs.

Other APIs cover most use cases, but using TargetBasedAnimation directly lets you control the animation's play time. In the following example, you manually control the TargetAnimation's play time based on the frame time provided by withFrameNanos.

val anim = remember { TargetBasedAnimation( animationSpec = tween(200), typeConverter = Float.VectorConverter, initialValue = 200f, targetValue = 1000f ) } var playTime by remember { mutableLongStateOf(0L) } LaunchedEffect(anim) { val startTime = withFrameNanos { it } do { playTime = withFrameNanos { it } - startTime val animationValue = anim.getValueFromNanos(playTime) } while (someCustomCondition()) }AnimationSnippets.kt

Unlike TargetBasedAnimation, DecayAnimation doesn't require a targetValue to be provided. Instead, it calculates its targetValue based on the starting conditions, set by initialVelocity and initialValue and the supplied DecayAnimationSpec.

Decay animations are often used after a fling gesture to slow elements to a stop. The animation velocity starts at the value that initialVelocityVector sets and slows down over time.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Quick guide to Animations in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/animation/quick-guide

**Contents:**
- Quick guide to Animations in Compose Stay organized with collections Save and categorize content based on your preferences.
- Animate common composable properties
  - Animate appearing / disappearing
  - Animate background color
  - Animate the size of a composable
  - Animate position of composable
  - Animate padding of a composable
  - Animate elevation of a composable
  - Animate text scale, translation or rotation
  - Animate text color

Compose has many built-in animation mechanisms and it can be overwhelming to know which one to choose. Below is a list of common animation use cases. For more detailed information about the full set of different API options available to you, read the full Compose Animation documentation.

Compose provides convenient APIs that allow you to solve for many common animation use cases. This section demonstrates how you can animate common properties of a composable.

Use AnimatedVisibility to hide or show a Composable. Children inside AnimatedVisibility can use Modifier.animateEnterExit() for their own enter or exit transition.

var visible by remember { mutableStateOf(true) } // Animated visibility will eventually remove the item from the composition once the animation has finished. AnimatedVisibility(visible) { // your composable here // ... }AnimationQuickGuide.kt

The enter and exit parameters of AnimatedVisibility allow you to configure how a composable behaves when it appears and disappears. Read the full documentation for more information.

Another option for animating the visibility of a composable is to animate the alpha over time using animateFloatAsState:

var visible by remember { mutableStateOf(true) } val animatedAlpha by animateFloatAsState( targetValue = if (visible) 1.0f else 0f, label = "alpha" ) Box( modifier = Modifier .size(200.dp) .graphicsLayer { alpha = animatedAlpha } .clip(RoundedCornerShape(8.dp)) .background(colorGreen) .align(Alignment.TopCenter) ) { }AnimationQuickGuide.kt

However, changing the alpha comes with the caveat that the composable remains in the composition and continues to occupy the space it's laid out in. This could cause screen readers and other accessibility mechanisms to still consider the item on screen. On the other hand, AnimatedVisibility eventually removes the item from the composition.

val animatedColor by animateColorAsState( if (animateBackgroundColor) colorGreen else colorBlue, label = "color" ) Column( modifier = Modifier.drawBehind { drawRect(animatedColor) } ) { // your composable here }AnimationQuickGuide.kt

This option is more performant than using Modifier.background(). Modifier.background() is acceptable for a one-shot color setting, but when animating a color over time, this could cause more recompositions than necessary.

For infinitely animating the background color, see repeating an animation section.

Compose lets you animate the size of composables in a few different ways. Use animateContentSize() for animations between composable size changes.

For example, if you have a box that contains text which can expand from one to multiple lines you can use Modifier.animateContentSize() to achieve a smoother transition:

var expanded by remember { mutableStateOf(false) } Box( modifier = Modifier .background(colorBlue) .animateContentSize() .height(if (expanded) 400.dp else 200.dp) .fillMaxWidth() .clickable( interactionSource = remember { MutableInteractionSource() }, indication = null ) { expanded = !expanded } ) { }AnimationQuickGuide.kt

You can also use AnimatedContent, with a SizeTransform to describe how size changes should take place.

To animate the position of a composable, use Modifier.offset{ } combined with animateIntOffsetAsState().

var moved by remember { mutableStateOf(false) } val pxToMove = with(LocalDensity.current) { 100.dp.toPx().roundToInt() } val offset by animateIntOffsetAsState( targetValue = if (moved) { IntOffset(pxToMove, pxToMove) } else { IntOffset.Zero }, label = "offset" ) Box( modifier = Modifier .offset { offset } .background(colorBlue) .size(100.dp) .clickable( interactionSource = remember { MutableInteractionSource() }, indication = null ) { moved = !moved } )AnimationQuickGuide.kt

If you want to ensure that composables are not drawn over or under other composables when animating position or size, use Modifier.layout{ }. This modifier propagates size and position changes to the parent, which then affects other children.

For example, if you are moving a Box within a Column and the other children need to move when the Box moves, include the offset information with Modifier.layout{ } as follows:

var toggled by remember { mutableStateOf(false) } val interactionSource = remember { MutableInteractionSource() } Column( modifier = Modifier .padding(16.dp) .fillMaxSize() .clickable(indication = null, interactionSource = interactionSource) { toggled = !toggled } ) { val offsetTarget = if (toggled) { IntOffset(150, 150) } else { IntOffset.Zero } val offset = animateIntOffsetAsState( targetValue = offsetTarget, label = "offset" ) Box( modifier = Modifier .size(100.dp) .background(colorBlue) ) Box( modifier = Modifier .layout { measurable, constraints -> val offsetValue = if (isLookingAhead) offsetTarget else offset.value val placeable = measurable.measure(constraints) layout(placeable.width + offsetValue.x, placeable.height + offsetValue.y) { placeable.placeRelative(offsetValue) } } .size(100.dp) .background(colorGreen) ) Box( modifier = Modifier .size(100.dp) .background(colorBlue) ) }AnimationQuickGuide.kt

To animate the padding of a composable, use animateDpAsState combined with Modifier.padding():

var toggled by remember { mutableStateOf(false) } val animatedPadding by animateDpAsState( if (toggled) { 0.dp } else { 20.dp }, label = "padding" ) Box( modifier = Modifier .aspectRatio(1f) .fillMaxSize() .padding(animatedPadding) .background(Color(0xff53D9A1)) .clickable( interactionSource = remember { MutableInteractionSource() }, indication = null ) { toggled = !toggled } )AnimationQuickGuide.kt

To animate the elevation of a composable, use animateDpAsState combined with Modifier.graphicsLayer{ }. For once-off elevation changes, use Modifier.shadow(). If you are animating the shadow, using Modifier.graphicsLayer{ } modifier is the more performant option.

val mutableInteractionSource = remember { MutableInteractionSource() } val pressed = mutableInteractionSource.collectIsPressedAsState() val elevation = animateDpAsState( targetValue = if (pressed.value) { 32.dp } else { 8.dp }, label = "elevation" ) Box( modifier = Modifier .size(100.dp) .align(Alignment.Center) .graphicsLayer { this.shadowElevation = elevation.value.toPx() } .clickable(interactionSource = mutableInteractionSource, indication = null) { } .background(colorGreen) ) { }AnimationQuickGuide.kt

Alternatively, use the Card composable, and set the elevation property to different values per state.

When animating scale, translation, or rotation of text, set the textMotion parameter on TextStyle to TextMotion.Animated. This ensures smoother transitions between text animations. Use Modifier.graphicsLayer{ } to translate, rotate or scale the text. val infiniteTransition = rememberInfiniteTransition(label = "infinite transition") val scale by infiniteTransition.animateFloat( initialValue = 1f, targetValue = 8f, animationSpec = infiniteRepeatable(tween(1000), RepeatMode.Reverse), label = "scale" ) Box(modifier = Modifier.fillMaxSize()) { Text( text = "Hello", modifier = Modifier .graphicsLayer { scaleX = scale scaleY = scale transformOrigin = TransformOrigin.Center } .align(Alignment.Center), // Text composable does not take TextMotion as a parameter. // Provide it via style argument but make sure that we are copying from current theme style = LocalTextStyle.current.copy(textMotion = TextMotion.Animated) ) } AnimationQuickGuide.kt

To animate text color, use the color lambda on the BasicText composable: val infiniteTransition = rememberInfiniteTransition(label = "infinite transition") val animatedColor by infiniteTransition.animateColor( initialValue = Color(0xFF60DDAD), targetValue = Color(0xFF4285F4), animationSpec = infiniteRepeatable(tween(1000), RepeatMode.Reverse), label = "color" ) BasicText( text = "Hello Compose", color = { animatedColor }, // ... )AnimationQuickGuide.kt

Use AnimatedContent to animate between different composables, if you just want a standard fade between composables, use Crossfade. var state by remember { mutableStateOf(UiState.Loading) } AnimatedContent( state, transitionSpec = { fadeIn( animationSpec = tween(3000) ) togetherWith fadeOut(animationSpec = tween(3000)) }, modifier = Modifier.clickable( interactionSource = remember { MutableInteractionSource() }, indication = null ) { state = when (state) { UiState.Loading -> UiState.Loaded UiState.Loaded -> UiState.Error UiState.Error -> UiState.Loading } }, label = "Animated Content" ) { targetState -> when (targetState) { UiState.Loading -> { LoadingScreen() } UiState.Loaded -> { LoadedScreen() } UiState.Error -> { ErrorScreen() } } }AnimationQuickGuide.kt

AnimatedContent can be customized to show many different kinds of enter and exit transitions. For more information, read the documentation on AnimatedContent or read this blog post on AnimatedContent.

To animate transitions between composables when using the navigation-compose artifact, specify the enterTransition and exitTransition on a composable. You can also set the default animation to be used for all destinations at the top level NavHost: val navController = rememberNavController() NavHost( navController = navController, startDestination = "landing", enterTransition = { EnterTransition.None }, exitTransition = { ExitTransition.None } ) { composable("landing") { ScreenLanding( // ... ) } composable( "detail/{photoUrl}", arguments = listOf(navArgument("photoUrl") { type = NavType.StringType }), enterTransition = { fadeIn( animationSpec = tween( 300, easing = LinearEasing ) ) + slideIntoContainer( animationSpec = tween(300, easing = EaseIn), towards = AnimatedContentTransitionScope.SlideDirection.Start ) }, exitTransition = { fadeOut( animationSpec = tween( 300, easing = LinearEasing ) ) + slideOutOfContainer( animationSpec = tween(300, easing = EaseOut), towards = AnimatedContentTransitionScope.SlideDirection.End ) } ) { backStackEntry -> ScreenDetails( // ... ) } }AnimationQuickGuide.kt

There are many different kinds of enter and exit transitions that apply different effects to the incoming and outgoing content, see the documentation for more.

Use rememberInfiniteTransition with an infiniteRepeatable animationSpec to continuously repeat your animation. Change RepeatModes to specify how it should go back and forth.

Use repeatable to repeat a set number of times. val infiniteTransition = rememberInfiniteTransition(label = "infinite") val color by infiniteTransition.animateColor( initialValue = Color.Green, targetValue = Color.Blue, animationSpec = infiniteRepeatable( animation = tween(1000, easing = LinearEasing), repeatMode = RepeatMode.Reverse ), label = "color" ) Column( modifier = Modifier.drawBehind { drawRect(color) } ) { // your composable here }AnimationQuickGuide.kt

LaunchedEffect runs when a composable enters the composition. It starts an animation on launch of a composable, you can use this to drive the animation state change. Using Animatable with the animateTo method to start the animation on launch:

val alphaAnimation = remember { Animatable(0f) } LaunchedEffect(Unit) { alphaAnimation.animateTo(1f) } Box( modifier = Modifier.graphicsLayer { alpha = alphaAnimation.value } )AnimationQuickGuide.kt

Use the Animatable coroutine APIs to perform sequential or concurrent animations. Calling animateTo on the Animatable one after the other causes each animation to wait for the previous animations to finish before proceeding . This is because it is a suspend function.

val alphaAnimation = remember { Animatable(0f) } val yAnimation = remember { Animatable(0f) } LaunchedEffect("animationKey") { alphaAnimation.animateTo(1f) yAnimation.animateTo(100f) yAnimation.animateTo(500f, animationSpec = tween(100)) }AnimationQuickGuide.kt

Use the coroutine APIs (Animatable#animateTo() or animate), or the Transition API to achieve concurrent animations. If you use multiple launch functions in a coroutine context, it launches the animations at the same time: val alphaAnimation = remember { Animatable(0f) } val yAnimation = remember { Animatable(0f) } LaunchedEffect("animationKey") { launch { alphaAnimation.animateTo(1f) } launch { yAnimation.animateTo(100f) } }AnimationQuickGuide.kt

You could use the updateTransition API to use the same state to drive many different property animations at the same time. The example below animates two properties controlled by a state change, rect and borderWidth:

var currentState by remember { mutableStateOf(BoxState.Collapsed) } val transition = updateTransition(currentState, label = "transition") val rect by transition.animateRect(label = "rect") { state -> when (state) { BoxState.Collapsed -> Rect(0f, 0f, 100f, 100f) BoxState.Expanded -> Rect(100f, 100f, 300f, 300f) } } val borderWidth by transition.animateDp(label = "borderWidth") { state -> when (state) { BoxState.Collapsed -> 1.dp BoxState.Expanded -> 0.dp } }AnimationQuickGuide.kt

Animations in Compose can cause performance problems. This is due to the nature of what an animation is: moving or changing pixels on screen quickly, frame-by-frame to create the illusion of movement.

Consider the different phases of Compose: composition, layout and draw. If your animation changes the layout phase, it requires all affected composables to relayout and redraw. If your animation occurs in the draw phase, it is by default be more performant than if you were to run the animation in the layout phase, as it would have less work to do overall.

To ensure your app does as little as possible while animating, choose the lambda version of a Modifier where possible. This skips recomposition and performs the animation outside of the composition phase, otherwise use Modifier.graphicsLayer{ }, as this modifier always runs in the draw phase. For more information on this, see the deferring reads section in the performance documentation.

Compose by default uses spring animations for most animations. Springs, or physics-based animations, feel more natural. They are also interruptible as they take into account the object's current velocity, instead of a fixed time. If you want to override the default, all the animation APIs demonstrated above have the ability to set an animationSpec to customize how an animation runs, whether you'd like it to execute over a certain duration or be more bouncy.

The following is a summary of the different animationSpec options:

Read the full documentation for more information about animationSpecs.

For more examples of fun animations in Compose, take a look at the following:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---
