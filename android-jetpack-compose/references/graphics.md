# Jetpack-Compose - Graphics

**Pages:** 12

---

## Custom painter Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/custompainter

**Contents:**
- Custom painter Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

In Compose, a Painter object is used to represent something that can be drawn (a replacement to the Drawable APIs defined in Android) and influence measurement and layout of the corresponding composable that is using it . A BitmapPainter takes an ImageBitmap that can draw a Bitmap on screen.

For most use cases, using the painterResource() above returns the correct painter for the asset (i.e. BitmapPainter or VectorPainter). For more information on the differences between the two - read the ImageBitmap vs ImageVector section.

A Painter is different from a DrawModifier, which strictly draws within the bounds that are given to it and has no influence on the measurement or layout of the composable.

To create a custom painter, extend the Painter class, and implement the onDraw method, which allows access to the DrawScope to draw custom graphics. You can also override the intrinsicSize, which will be used to influence the Composable that it is contained in:

class OverlayImagePainter constructor( private val image: ImageBitmap, private val imageOverlay: ImageBitmap, private val srcOffset: IntOffset = IntOffset.Zero, private val srcSize: IntSize = IntSize(image.width, image.height), private val overlaySize: IntSize = IntSize(imageOverlay.width, imageOverlay.height) ) : Painter() { private val size: IntSize = validateSize(srcOffset, srcSize) override fun DrawScope.onDraw() { // draw the first image without any blend mode drawImage( image, srcOffset, srcSize, dstSize = IntSize( this@onDraw.size.width.roundToInt(), this@onDraw.size.height.roundToInt() ) ) // draw the second image with an Overlay blend mode to blend the two together drawImage( imageOverlay, srcOffset, overlaySize, dstSize = IntSize( this@onDraw.size.width.roundToInt(), this@onDraw.size.height.roundToInt() ), blendMode = BlendMode.Overlay ) } /** * Return the dimension of the underlying [ImageBitmap] as it's intrinsic width and height */ override val intrinsicSize: Size get() = size.toSize() private fun validateSize(srcOffset: IntOffset, srcSize: IntSize): IntSize { require( srcOffset.x >= 0 && srcOffset.y >= 0 && srcSize.width >= 0 && srcSize.height >= 0 && srcSize.width <= image.width && srcSize.height <= image.height ) return srcSize } }CustomPainterSnippets.kt

Now that we have our custom Painter, we can overlay any image on top of our source image as follows:

val rainbowImage = ImageBitmap.imageResource(id = R.drawable.rainbow) val dogImage = ImageBitmap.imageResource(id = R.drawable.dog) val customPainter = remember { OverlayImagePainter(dogImage, rainbowImage) } Image( painter = customPainter, contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier.wrapContentSize() )CustomPainterSnippets.kt

The output of combining the two images with a custom painter can be seen below:

A custom painter can also be used with the Modifier.paint(customPainter) to draw the content to a composable as follows:

val rainbowImage = ImageBitmap.imageResource(id = R.drawable.rainbow) val dogImage = ImageBitmap.imageResource(id = R.drawable.dog) val customPainter = remember { OverlayImagePainter(dogImage, rainbowImage) } Box( modifier = Modifier.background(color = Color.Gray) .padding(30.dp) .background(color = Color.Yellow) .paint(customPainter) ) { /** intentionally empty **/ }CustomPainterSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Images and graphics in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics

**Contents:**
- Images and graphics in Compose Stay organized with collections Save and categorize content based on your preferences.

Using bright, engaging visuals in your Android apps can help improve the look and feel of your application. In Android, there are a few different ways in which you can render something visually on screen using either a vector, bitmap, or directly drawing with a canvas on screen. In this section, learn how to work these different kinds of graphics.

Learn how to work with images using these details:

You can also learn more about working with Graphics here.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Working with images Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images

**Contents:**
- Working with images Stay organized with collections Save and categorize content based on your preferences.

Learn how to work with images using these details:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Loading images Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/loading

**Contents:**
- Loading images Stay organized with collections Save and categorize content based on your preferences.
- Load an image from the disk
  - Drawable support
- Load an image from the internet
  - Coil
  - Glide
- Additional resources
- Recommended for you

Use the Image composable to display a graphic on screen. To load an image (for example: PNG, JPEG, WEBP) or vector resource from the disk, use the painterResource API with your image reference. You don't need to know the type of the asset, just use painterResource in Image or paint modifiers.

DrawScope: Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description) )LoadingImagesSnippets.kt

To ensure that your app is accessible, supply a contentDescription for visual elements on screen. TalkBack reads out the content description, so you must ensure that the text is meaningful if read out loud and translated. In the above example, a stringResource() is used to load up the translated content description from the strings.xml file. If your visual element on screen is purely for visual decoration, set your contentDescription to null for the screen reader to ignore it.

If you need lower-level ImageBitmap specific functionality, you can use ImageBitmap.imageResource() to load up a Bitmap. For more information on ImageBitmaps, read the ImageBitmap versus ImageVector section.

painterResource currently supports the following drawable types:

To load an image from the internet, there are several third-party libraries available to help you handle the process. Image loading libraries do a lot of the heavy lifting for you; they handle both caching (so you don't download the image multiple times) and networking logic to download the image and display it on screen.

For example, to load an image with Coil from Instacart, add the library to your gradle file, and use an AsyncImage to load an image from a URL:

AsyncImage( model = "https://example.com/image.jpg", contentDescription = "Translated description of what the image contains" )LoadingImagesSnippets.kt

An image loading library backed by Kotlin Coroutines (Instacart).

A fast and efficient image loading library for Android focused on smooth scrolling (Google).

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Brush: gradients and shaders Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/draw/brush

**Contents:**
- Brush: gradients and shaders Stay organized with collections Save and categorize content based on your preferences.
- Gradient brushes
  - Change distribution of colors with colorStops
  - Repeat a pattern with TileMode
  - Change brush Size
- Use an image as a brush
- Advanced example: Custom brush
  - AGSL RuntimeShader brush
- Additional resources
- Recommended for you

A Brush in Compose describes how something is drawn on screen: it determines the color(s) that are drawn in the drawing area (i.e. a circle, square, path). There are a few built-in Brushes that are useful for drawing, such as LinearGradient, RadialGradient or a plain SolidColor brush.

Brushes can be used with Modifier.background(), TextStyle, or DrawScope draw calls to apply the painting style to the content being drawn.

For example, a horizontal gradient brush can be applied to drawing a circle in DrawScope: val brush = Brush.horizontalGradient(listOf(Color.Red, Color.Blue)) Canvas( modifier = Modifier.size(200.dp), onDraw = { drawCircle(brush) } )BrushExampleSnippets.kt Figure 1: Circle drawn with Horizontal Gradient

There are many built-in gradient brushes that can be used to achieve different gradient effects. These brushes allow you to specify the list of colors that you would like to create a gradient from.

A list of available gradient brushes and their corresponding output:

To customize how the colors appear in the gradient, you can tweak the colorStops value for each one. colorStops should be specified as a fraction, between 0 and 1. Values greater than 1 will result in those colors not rendering as part of the gradient.

You can configure the color stops to have different amounts, such as less or more of one color:

val colorStops = arrayOf( 0.0f to Color.Yellow, 0.2f to Color.Red, 1f to Color.Blue ) Box( modifier = Modifier .requiredSize(200.dp) .background(Brush.horizontalGradient(colorStops = colorStops)) )BrushExampleSnippets.kt

The colors are dispersed at the provided offset as defined in the colorStop pair, less yellow than red and blue.

Each gradient brush has the option to set a TileMode on it. You may not notice the TileMode if you haven’t set a start and end for the gradient, as it’ll default to fill the whole area. A TileMode will only tile the gradient if the size of the area is bigger than the Brush size.

The following code will repeat the gradient pattern 4 times, since the endX is set to 50.dp and the size is set to 200.dp:

val listColors = listOf(Color.Yellow, Color.Red, Color.Blue) val tileSize = with(LocalDensity.current) { 50.dp.toPx() } Box( modifier = Modifier .requiredSize(200.dp) .background( Brush.horizontalGradient( listColors, endX = tileSize, tileMode = TileMode.Repeated ) ) )BrushExampleSnippets.kt

Here is a table detailing what the different Tile Modes do for the HorizontalGradient example above:

TileMode works in a similar way for the other directional gradients, the difference being the direction that the repetition occurs.

If you know the size of the area in which your brush will be drawn, you can set the tile endX as we’ve seen above in the TileMode section. If you are in a DrawScope, you can use its size property to get the size of the area.

If you don't know the size of your drawing area (for example if the Brush is assigned to Text), you can extend Shader and utilize the size of the drawing area in the createShader function.

In this example, divide the size by 4 to repeat the pattern 4 times:

val listColors = listOf(Color.Yellow, Color.Red, Color.Blue) val customBrush = remember { object : ShaderBrush() { override fun createShader(size: Size): Shader { return LinearGradientShader( colors = listColors, from = Offset.Zero, to = Offset(size.width / 4f, 0f), tileMode = TileMode.Mirror ) } } } Box( modifier = Modifier .requiredSize(200.dp) .background(customBrush) )BrushExampleSnippets.kt

You can also change the brush size of any other gradient, such as radial gradients. If you don't specify a size and center, the gradient will occupy the full bounds of the DrawScope, and the center of the radial gradient defaults to the center of the DrawScope bounds. This results in the radial gradient's center appearing as the center of the smaller dimension (either width or height):

Box( modifier = Modifier .fillMaxSize() .background( Brush.radialGradient( listOf(Color(0xFF2be4dc), Color(0xFF243484)) ) ) )BrushExampleSnippets.kt

When the radial gradient is changed to set the radius size to the max dimension, you can see that it produces a better radial gradient effect:

val largeRadialGradient = object : ShaderBrush() { override fun createShader(size: Size): Shader { val biggerDimension = maxOf(size.height, size.width) return RadialGradientShader( colors = listOf(Color(0xFF2be4dc), Color(0xFF243484)), center = size.center, radius = biggerDimension / 2f, colorStops = listOf(0f, 0.95f) ) } } Box( modifier = Modifier .fillMaxSize() .background(largeRadialGradient) )BrushExampleSnippets.kt

It is worth noting that the actual size that is passed into the creation of the shader is determined from where it is invoked. By default, Brush will reallocate its Shader internally if the size is different from the last creation of the Brush, or if a state object used in creation of the shader has changed.

The following code creates the shader three different times with different sizes, as the size of the drawing area changes:

val colorStops = arrayOf( 0.0f to Color.Yellow, 0.2f to Color.Red, 1f to Color.Blue ) val brush = Brush.horizontalGradient(colorStops = colorStops) Box( modifier = Modifier .requiredSize(200.dp) .drawBehind { drawRect(brush = brush) // will allocate a shader to occupy the 200 x 200 dp drawing area inset(10f) { /* Will allocate a shader to occupy the 180 x 180 dp drawing area as the inset scope reduces the drawing area by 10 pixels on the left, top, right, bottom sides */ drawRect(brush = brush) inset(5f) { /* will allocate a shader to occupy the 170 x 170 dp drawing area as the inset scope reduces the drawing area by 5 pixels on the left, top, right, bottom sides */ drawRect(brush = brush) } } } )BrushExampleSnippets.kt

To use an ImageBitmap as a Brush, load up the image as an ImageBitmap, and create an ImageShader brush:

val imageBrush = ShaderBrush(ImageShader(ImageBitmap.imageResource(id = R.drawable.dog))) // Use ImageShader Brush with background Box( modifier = Modifier .requiredSize(200.dp) .background(imageBrush) ) // Use ImageShader Brush with TextStyle Text( text = "Hello Android!", style = TextStyle( brush = imageBrush, fontWeight = FontWeight.ExtraBold, fontSize = 36.sp ) ) // Use ImageShader Brush with DrawScope#drawCircle() Canvas(onDraw = { drawCircle(imageBrush) }, modifier = Modifier.size(200.dp))BrushExampleSnippets.kt

The Brush is applied to a few different types of drawing: a background, the Text and Canvas. This outputs the following:

Notice that the text is now also rendered using the ImageBitmap to paint the pixels for the text.

AGSL offers a subset of GLSL Shader capabilities. Shaders can be written in AGSL and used with a Brush in Compose.

To create a Shader brush, first define the Shader as AGSL shader string:

@Language("AGSL") val CUSTOM_SHADER = """ uniform float2 resolution; layout(color) uniform half4 color; layout(color) uniform half4 color2; half4 main(in float2 fragCoord) { float2 uv = fragCoord/resolution.xy; float mixValue = distance(uv, vec2(0, 1)); return mix(color, color2, mixValue); } """.trimIndent()BrushExampleSnippets.kt

The shader above takes two input colors, calculates the distance from the bottom left (vec2(0, 1)) of the drawing area and does a mix between the two colors based on the distance. This produces a gradient effect.

Then, create the Shader Brush, and set the uniforms for resolution - the size of the drawing area, and the color and color2 you want to use as input to your custom gradient:

val Coral = Color(0xFFF3A397) val LightYellow = Color(0xFFF8EE94) @RequiresApi(Build.VERSION_CODES.TIRAMISU) @Composable @Preview fun ShaderBrushExample() { Box( modifier = Modifier .drawWithCache { val shader = RuntimeShader(CUSTOM_SHADER) val shaderBrush = ShaderBrush(shader) shader.setFloatUniform("resolution", size.width, size.height) onDrawBehind { shader.setColorUniform( "color", android.graphics.Color.valueOf( LightYellow.red, LightYellow.green, LightYellow .blue, LightYellow.alpha ) ) shader.setColorUniform( "color2", android.graphics.Color.valueOf( Coral.red, Coral.green, Coral.blue, Coral.alpha ) ) drawRect(shaderBrush) } } .fillMaxWidth() .height(200.dp) ) }BrushExampleSnippets.kt

Running this, you can see the following rendered on screen:

It's worth noting that you can do a lot more with shaders than just gradients, as it's all math-based calculations. For more information on AGSL, check out the AGSL documentation.

For more examples of using Brush in Compose, check out the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Icons Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/material

**Contents:**
- Icons Stay organized with collections Save and categorize content based on your preferences.
- Recommended for you

The Icon composable is a convenient way to draw a single color icon on screen that follows Material Design guidelines. To use Icon, include the Compose Material library (or the Compose Material 3 library).

For example, if you had a vector drawable that you wanted to load up with Material defaults, you can use the Icon composable as follows:

Icon( painter = painterResource(R.drawable.baseline_directions_bus_24), contentDescription = stringResource(id = R.string.bus_content_description) )MaterialIconsSnippets.kt

By default, the Icon composable is tinted with LocalContentColor.current and is 24.dp in size. It also exposes a tint color parameter (which leverages the same mechanism for tinting as described in the Image tint section). The Icon composable is intended for use for small icon elements. You should use the Image composable for more customization options.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Shapes in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/draw/shapes

**Contents:**
- Shapes in Compose Stay organized with collections Save and categorize content based on your preferences.
- Create polygons
  - Round the corners of a polygon
    - Radius
    - Smoothing
  - Size and position
- Morph shapes
- Use polygon as clip
- Morph button on click
- Animate shape morphing infinitely

With Compose, you can create shapes that are made from polygons. For example, you can make the following kinds of shapes:

To create a custom rounded polygon in Compose, add the graphics-shapes dependency to your app/build.gradle:

This library lets you create shapes that are made from polygons. While polygonal shapes have only straight edges and sharp corners, these shapes allow for optional rounded corners. It makes it simple to morph between two different shapes. Morphing is difficult between arbitrary shapes, and tends to be a design-time problem. But this library makes it simple by morphing between these shapes with similar polygonal structures.

The following snippet creates a basic polygon shape with 6 points in the center of the drawing area:

Box( modifier = Modifier .drawWithCache { val roundedPolygon = RoundedPolygon( numVertices = 6, radius = size.minDimension / 2, centerX = size.width / 2, centerY = size.height / 2 ) val roundedPolygonPath = roundedPolygon.toPath().asComposePath() onDrawBehind { drawPath(roundedPolygonPath, color = Color.Blue) } } .fillMaxSize() )ShapesSnippets.kt

In this example, the library creates a RoundedPolygon which holds the geometry representing the requested shape. In order to draw that shape in a Compose app, you must get a Path object from it to get the shape into a form which Compose knows how to draw.

To round the corners of a polygon, use the CornerRounding parameter. This takes two parameters, radius and smoothing. Each rounded corner is made up of 1-3 cubic curves, the center of which has a circular arc shape while the two side ("flanking") curves transition from the shape's edge to the center curve.

The radius is the radius of the circle used to round a vertex.

For example, the following rounded corner triangle is made as follows:

Smoothing is a factor which determines how long it takes to get from the circular rounding portion of the corner to the edge. A smoothing factor of 0 (unsmoothed, the default value for CornerRounding) results in purely circular corner rounding. A nonzero smoothing factor (up to the max of 1.0) results in the corner being rounded by three separate curves.

For example, the snippet below illustrates the subtle difference in setting smoothing to 0 versus 1: Box( modifier = Modifier .drawWithCache { val roundedPolygon = RoundedPolygon( numVertices = 3, radius = size.minDimension / 2, centerX = size.width / 2, centerY = size.height / 2, rounding = CornerRounding( size.minDimension / 10f, smoothing = 0.1f ) ) val roundedPolygonPath = roundedPolygon.toPath().asComposePath() onDrawBehind { drawPath(roundedPolygonPath, color = Color.Black) } } .size(100.dp) ) ShapesSnippets.kt

By default, a shape is created with a radius of 1 around the center (0, 0). This radius represents the distance between the center and the exterior vertices of the polygon on which the shape is based. Note that rounding the corners results in a smaller shape since the rounded corners will be closer to the center than the vertices being rounded. To size a polygon, adjust the radius value. To adjust the position, change the centerX or centerY of the polygon. Alternatively, transform the object to change its size, position, and rotation using standard DrawScope transformation functions such as DrawScope#translate().

A Morph object is a new shape representing an animation between two polygonal shapes. To morph between two shapes, create two RoundedPolygons and a Morph object that takes these two shapes. To calculate a shape between the start and end shapes, provide a progress value between zero and one to determine its form between the starting (0) and ending (1) shapes:

Box( modifier = Modifier .drawWithCache { val triangle = RoundedPolygon( numVertices = 3, radius = size.minDimension / 2f, centerX = size.width / 2f, centerY = size.height / 2f, rounding = CornerRounding( size.minDimension / 10f, smoothing = 0.1f ) ) val square = RoundedPolygon( numVertices = 4, radius = size.minDimension / 2f, centerX = size.width / 2f, centerY = size.height / 2f ) val morph = Morph(start = triangle, end = square) val morphPath = morph .toPath(progress = 0.5f).asComposePath() onDrawBehind { drawPath(morphPath, color = Color.Black) } } .fillMaxSize() )ShapesSnippets.kt

In the above example, the progress is exactly halfway between the two shapes (rounded triangle and a square), producing the following result:

In most scenarios, morphing is done as part of an animation, and not just a static rendering. To animate between these two, you can use the standard Animation APIs in Compose to change the progress value over time. For example, you can infinitely animate the morph between these two shapes as follows:

val infiniteAnimation = rememberInfiniteTransition(label = "infinite animation") val morphProgress = infiniteAnimation.animateFloat( initialValue = 0f, targetValue = 1f, animationSpec = infiniteRepeatable( tween(500), repeatMode = RepeatMode.Reverse ), label = "morph" ) Box( modifier = Modifier .drawWithCache { val triangle = RoundedPolygon( numVertices = 3, radius = size.minDimension / 2f, centerX = size.width / 2f, centerY = size.height / 2f, rounding = CornerRounding( size.minDimension / 10f, smoothing = 0.1f ) ) val square = RoundedPolygon( numVertices = 4, radius = size.minDimension / 2f, centerX = size.width / 2f, centerY = size.height / 2f ) val morph = Morph(start = triangle, end = square) val morphPath = morph .toPath(progress = morphProgress.value) .asComposePath() onDrawBehind { drawPath(morphPath, color = Color.Black) } } .fillMaxSize() )ShapesSnippets.kt

It's common to use the clip modifier in Compose to change how a composable is rendered, and to take advantage of shadows that draw around the clipping area:

fun RoundedPolygon.getBounds() = calculateBounds().let { Rect(it[0], it[1], it[2], it[3]) } class RoundedPolygonShape( private val polygon: RoundedPolygon, private var matrix: Matrix = Matrix() ) : Shape { private var path = Path() override fun createOutline( size: Size, layoutDirection: LayoutDirection, density: Density ): Outline { path.rewind() path = polygon.toPath().asComposePath() matrix.reset() val bounds = polygon.getBounds() val maxDimension = max(bounds.width, bounds.height) matrix.scale(size.width / maxDimension, size.height / maxDimension) matrix.translate(-bounds.left, -bounds.top) path.transform(matrix) return Outline.Generic(path) } }ShapesSnippets.kt

You can then use the polygon as a clip, as shown in the following snippet:

val hexagon = remember { RoundedPolygon( 6, rounding = CornerRounding(0.2f) ) } val clip = remember(hexagon) { RoundedPolygonShape(polygon = hexagon) } Box( modifier = Modifier .clip(clip) .background(MaterialTheme.colorScheme.secondary) .size(200.dp) ) { Text( "Hello Compose", color = MaterialTheme.colorScheme.onSecondary, modifier = Modifier.align(Alignment.Center) ) }ShapesSnippets.kt

This results in the following:

This may not look that different from what was rendering before, but it allows for leveraging other features in Compose. For example, this technique can be used to clip an image and apply a shadow around the clipped region:

val hexagon = remember { RoundedPolygon( 6, rounding = CornerRounding(0.2f) ) } val clip = remember(hexagon) { RoundedPolygonShape(polygon = hexagon) } Box( modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center ) { Image( painter = painterResource(id = R.drawable.dog), contentDescription = "Dog", contentScale = ContentScale.Crop, modifier = Modifier .graphicsLayer { this.shadowElevation = 6.dp.toPx() this.shape = clip this.clip = true this.ambientShadowColor = Color.Black this.spotShadowColor = Color.Black } .size(200.dp) ) }ShapesSnippets.kt

You can use the graphics-shape library to create a button that morphs between two shapes on press. First, create a MorphPolygonShape that extends Shape, scaling and translating it to fit appropriately. Note the passing in of the progress so that the shape can be animated:

class MorphPolygonShape( private val morph: Morph, private val percentage: Float ) : Shape { private val matrix = Matrix() override fun createOutline( size: Size, layoutDirection: LayoutDirection, density: Density ): Outline { // Below assumes that you haven't changed the default radius of 1f, nor the centerX and centerY of 0f // By default this stretches the path to the size of the container, if you don't want stretching, use the same size.width for both x and y. matrix.scale(size.width / 2f, size.height / 2f) matrix.translate(1f, 1f) val path = morph.toPath(progress = percentage).asComposePath() path.transform(matrix) return Outline.Generic(path) } } ShapesSnippets.kt

To use this morph shape, create two polygons, shapeA and shapeB. Create and remember the Morph. Then, apply the morph to the button as a clip outline, using the interactionSource on press as the driving force behind the animation:

val shapeA = remember { RoundedPolygon( 6, rounding = CornerRounding(0.2f) ) } val shapeB = remember { RoundedPolygon.star( 6, rounding = CornerRounding(0.1f) ) } val morph = remember { Morph(shapeA, shapeB) } val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() val animatedProgress = animateFloatAsState( targetValue = if (isPressed) 1f else 0f, label = "progress", animationSpec = spring(dampingRatio = 0.4f, stiffness = Spring.StiffnessMedium) ) Box( modifier = Modifier .size(200.dp) .padding(8.dp) .clip(MorphPolygonShape(morph, animatedProgress.value)) .background(Color(0xFF80DEEA)) .size(200.dp) .clickable(interactionSource = interactionSource, indication = null) { } ) { Text("Hello", modifier = Modifier.align(Alignment.Center)) }ShapesSnippets.kt

This results in the following animation when the box is tapped:

To endlessly animate a morph shape, use rememberInfiniteTransition. Below is an example of a profile picture that changes shape (and rotates) infinitely over time. This approach uses a small adjustment to the MorphPolygonShape shown above:

class CustomRotatingMorphShape( private val morph: Morph, private val percentage: Float, private val rotation: Float ) : Shape { private val matrix = Matrix() override fun createOutline( size: Size, layoutDirection: LayoutDirection, density: Density ): Outline { // Below assumes that you haven't changed the default radius of 1f, nor the centerX and centerY of 0f // By default this stretches the path to the size of the container, if you don't want stretching, use the same size.width for both x and y. matrix.scale(size.width / 2f, size.height / 2f) matrix.translate(1f, 1f) matrix.rotateZ(rotation) val path = morph.toPath(progress = percentage).asComposePath() path.transform(matrix) return Outline.Generic(path) } } @Preview @Composable private fun RotatingScallopedProfilePic() { val shapeA = remember { RoundedPolygon( 12, rounding = CornerRounding(0.2f) ) } val shapeB = remember { RoundedPolygon.star( 12, rounding = CornerRounding(0.2f) ) } val morph = remember { Morph(shapeA, shapeB) } val infiniteTransition = rememberInfiniteTransition("infinite outline movement") val animatedProgress = infiniteTransition.animateFloat( initialValue = 0f, targetValue = 1f, animationSpec = infiniteRepeatable( tween(2000, easing = LinearEasing), repeatMode = RepeatMode.Reverse ), label = "animatedMorphProgress" ) val animatedRotation = infiniteTransition.animateFloat( initialValue = 0f, targetValue = 360f, animationSpec = infiniteRepeatable( tween(6000, easing = LinearEasing), repeatMode = RepeatMode.Reverse ), label = "animatedMorphProgress" ) Box( modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center ) { Image( painter = painterResource(id = R.drawable.dog), contentDescription = "Dog", contentScale = ContentScale.Crop, modifier = Modifier .clip( CustomRotatingMorphShape( morph, animatedProgress.value, animatedRotation.value ) ) .size(200.dp) ) } }ShapesSnippets.kt

This code gives the following fun result:

If shapes created from regular polygons don't cover your use case, you can create a more custom shape with a list of vertices. For example, you may want to create a heart shape like this:

You can specify the individual vertices of this shape using the RoundedPolygon overload that takes a float array of x, y coordinates.

To break down the heart polygon, notice that the polar coordinate system for specifying points makes this easier than using the cartesian (x,y) coordinate system, where 0° starts on the right hand side, and proceeds clockwise, with 270° at the 12 o'clock position:

The shape can now be defined in an easier way by specifying the angle (𝜭) and radius from the center at each point:

The vertices can now be created and passed to the RoundedPolygon function:

val vertices = remember { val radius = 1f val radiusSides = 0.8f val innerRadius = .1f floatArrayOf( radialToCartesian(radiusSides, 0f.toRadians()).x, radialToCartesian(radiusSides, 0f.toRadians()).y, radialToCartesian(radius, 90f.toRadians()).x, radialToCartesian(radius, 90f.toRadians()).y, radialToCartesian(radiusSides, 180f.toRadians()).x, radialToCartesian(radiusSides, 180f.toRadians()).y, radialToCartesian(radius, 250f.toRadians()).x, radialToCartesian(radius, 250f.toRadians()).y, radialToCartesian(innerRadius, 270f.toRadians()).x, radialToCartesian(innerRadius, 270f.toRadians()).y, radialToCartesian(radius, 290f.toRadians()).x, radialToCartesian(radius, 290f.toRadians()).y, ) }ShapesSnippets.kt

The vertices need to be translated into cartesian coordinates using this radialToCartesian function:

internal fun Float.toRadians() = this * PI.toFloat() / 180f internal val PointZero = PointF(0f, 0f) internal fun radialToCartesian( radius: Float, angleRadians: Float, center: PointF = PointZero ) = directionVectorPointF(angleRadians) * radius + center internal fun directionVectorPointF(angleRadians: Float) = PointF(cos(angleRadians), sin(angleRadians))ShapesSnippets.kt

The preceding code gives you the raw vertices for the heart, but you need to round specific corners to get the chosen heart shape. The corners at 90° and 270° have no rounding, but the other corners do. To achieve custom rounding for individual corners, use the perVertexRounding parameter:

val rounding = remember { val roundingNormal = 0.6f val roundingNone = 0f listOf( CornerRounding(roundingNormal), CornerRounding(roundingNone), CornerRounding(roundingNormal), CornerRounding(roundingNormal), CornerRounding(roundingNone), CornerRounding(roundingNormal), ) } val polygon = remember(vertices, rounding) { RoundedPolygon( vertices = vertices, perVertexRounding = rounding ) } Box( modifier = Modifier .drawWithCache { val roundedPolygonPath = polygon.toPath().asComposePath() onDrawBehind { scale(size.width * 0.5f, size.width * 0.5f) { translate(size.width * 0.5f, size.height * 0.5f) { drawPath(roundedPolygonPath, color = Color(0xFFF15087)) } } } } .size(400.dp) )ShapesSnippets.kt

This results in the pink heart:

If the preceding shapes don't cover your use case, consider using the Path class to draw a custom shape, or loading up an ImageVector file from disk. The graphics-shapes library is not intended for use for arbitrary shapes, but is specifically meant to simplify creation of rounded polygons and morph animations between them.

For more information and examples, see the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (json):
```json
implementation "androidx.graphics:graphics-shapes:1.0.1"
```

---

## Customize an image Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/customize

**Contents:**
- Customize an image Stay organized with collections Save and categorize content based on your preferences.
- Content scale
- Clip an Image composable to a shape
- Add a border to an Image composable
- Set a custom aspect ratio
- Color filter: transform pixel colors of image
  - Tint images
  - Apply an Image filter with color matrix
    - Adjust contrast or brightness of an Image composable
    - Invert colors of an Image composable

You can customize images using properties on an Image composable (contentScale, colorFilter). You can also apply existing modifiers to apply different effects to your Image. Modifiers can be used on any composable, not just the Image composable, whereas contentScale and colorFilter are explicit parameters on the Image composable.

Specify a contentScale option to crop or change how an image is scaled inside its bounds. By default, if you don't specify a contentScale option, ContentScale.Fit is used.

In the following example, the Image composable is restricted to a 150dp size with a border, and the background is set to yellow on the Image composable to showcase the different ContentScale options in the table below.

val imageModifier = Modifier .size(150.dp) .border(BorderStroke(1.dp, Color.Black)) .background(Color.Yellow) Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Fit, modifier = imageModifier )CustomizeImageSnippets.kt

Setting different ContentScale options results in different outputs. The following table helps you choose the correct ContentScale mode:

To make an image fit into a shape, use the built-in clip modifier. To crop an image into a circle shape, use Modifier.clip(CircleShape):

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(200.dp) .clip(CircleShape) )CustomizeImageSnippets.kt

For a rounded corner shape, use Modifier.clip(RoundedCornerShape(16.dp)), with the size of the corners you want to be rounded:

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(200.dp) .clip(RoundedCornerShape(16.dp)) )CustomizeImageSnippets.kt

You can also create your own clipping shape by extending Shape, and providing a Path for the shape to clip around:

class SquashedOval : Shape { override fun createOutline( size: Size, layoutDirection: LayoutDirection, density: Density ): Outline { val path = Path().apply { // We create an Oval that starts at ¼ of the width, and ends at ¾ of the width of the container. addOval( Rect( left = size.width / 4f, top = 0f, right = size.width * 3 / 4f, bottom = size.height ) ) } return Outline.Generic(path = path) } } Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(200.dp) .clip(SquashedOval()) )CustomizeImageSnippets.kt

A common operation is to combine the Modifier.border() with Modifier.clip() to create a border around an image:

val borderWidth = 4.dp Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(150.dp) .border( BorderStroke(borderWidth, Color.Yellow), CircleShape ) .padding(borderWidth) .clip(CircleShape) )CustomizeImageSnippets.kt

To create a gradient border, you can use the Brush API to draw a rainbow gradient border around the image:

val rainbowColorsBrush = remember { Brush.sweepGradient( listOf( Color(0xFF9575CD), Color(0xFFBA68C8), Color(0xFFE57373), Color(0xFFFFB74D), Color(0xFFFFF176), Color(0xFFAED581), Color(0xFF4DD0E1), Color(0xFF9575CD) ) ) } val borderWidth = 4.dp Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(150.dp) .border( BorderStroke(borderWidth, rainbowColorsBrush), CircleShape ) .padding(borderWidth) .clip(CircleShape) )CustomizeImageSnippets.kt

To transform an image into a custom aspect ratio, use Modifier.aspectRatio(16f/9f) to provide a custom ratio for an image (or any composable).

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), modifier = Modifier.aspectRatio(16f / 9f) )CustomizeImageSnippets.kt

The Image composable has a colorFilter parameter that can change the output of individual pixels of your image.

Using ColorFilter.tint(color, blendMode) applies a blend mode with the given color onto your Image composable. ColorFilter.tint(color, blendMode) uses BlendMode.SrcIn to tint content, meaning that the color supplied is shown where the image is displayed on screen. This is useful for icons and vectors that need to be themed differently.

Image( painter = painterResource(id = R.drawable.baseline_directions_bus_24), contentDescription = stringResource(id = R.string.bus_content_description), colorFilter = ColorFilter.tint(Color.Yellow) )CustomizeImageSnippets.kt

Other BlendModes result in different effects. For example, setting BlendMode.Darken with a Color.Green on an image produces the following result:

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), colorFilter = ColorFilter.tint(Color.Green, blendMode = BlendMode.Darken) )CustomizeImageSnippets.kt

See the BlendMode reference documentation for more information on the different blend modes available.

Transform your image using the color matrix ColorFilter option. For example, to apply a black and white filter onto your images you could use the ColorMatrix and set the saturation to 0f.

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), colorFilter = ColorFilter.colorMatrix(ColorMatrix().apply { setToSaturation(0f) }) )CustomizeImageSnippets.kt

To change the contrast and brightness of an image, you can use the ColorMatrix to change the values:

val contrast = 2f // 0f..10f (1 should be default) val brightness = -180f // -255f..255f (0 should be default) val colorMatrix = floatArrayOf( contrast, 0f, 0f, 0f, brightness, 0f, contrast, 0f, 0f, brightness, 0f, 0f, contrast, 0f, brightness, 0f, 0f, 0f, 1f, 0f ) Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), colorFilter = ColorFilter.colorMatrix(ColorMatrix(colorMatrix)) )CustomizeImageSnippets.kt

To invert the colors of an image, set the ColorMatrix to invert the colors:

val colorMatrix = floatArrayOf( -1f, 0f, 0f, 0f, 255f, 0f, -1f, 0f, 0f, 255f, 0f, 0f, -1f, 0f, 255f, 0f, 0f, 0f, 1f, 0f ) Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), colorFilter = ColorFilter.colorMatrix(ColorMatrix(colorMatrix)) )CustomizeImageSnippets.kt

To blur an image, use Modifier.blur(), supplying the radiusX and radiusY, which specifies the blur radius in the horizontal and vertical direction respectively.

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(150.dp) .blur( radiusX = 10.dp, radiusY = 10.dp, edgeTreatment = BlurredEdgeTreatment(RoundedCornerShape(8.dp)) ) )CustomizeImageSnippets.kt

When blurring Images, it is recommended to use BlurredEdgeTreatment(Shape), instead of BlurredEdgeTreatment.Unbounded, as the latter is used for blurring of arbitrary renderings that are expected to render outside the bounds of the original content. For images, it is likely that they won't render outside the bounds of the content, whereas blurring a rounded rectangle may require this distinction.

For example, if we set the BlurredEdgeTreatment to Unbounded on the preceding image, the edges of the image appear blurred instead of sharp:

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description), contentScale = ContentScale.Crop, modifier = Modifier .size(150.dp) .blur( radiusX = 10.dp, radiusY = 10.dp, edgeTreatment = BlurredEdgeTreatment.Unbounded ) .clip(RoundedCornerShape(8.dp)) )CustomizeImageSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Image bitmap versus image vector Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/compare

**Contents:**
- Image bitmap versus image vector Stay organized with collections Save and categorize content based on your preferences.
- ImageBitmap
- ImageVector
- Recommended for you

The two most common kinds of image formats are raster and vector images.

A raster graphic format contains pixels: tiny individual squares that contain a color (made up of red, green, blue, and alpha values). When placing a lot of pixels together, a very detailed image can be formed, such as a photograph. A raster graphic has a fixed resolution (fixed number of pixels). This means that when you increase the size of the image, it loses detail, and pixelation can occur. Examples of raster graphic formats are JPEG, PNG, and WEBP.

Vector images, on the other hand, are scalable mathematical representations of a visual element on screen. A vector is a set of commands describing how to draw the image on screen—for example, a line, point, or fill. When scaling a vector on screen, it won't lose quality, as the mathematical formula maintains the relationship between the different commands. Good examples of ImageVectors are the Material Symbols, as they can all be defined with mathematical formulas.

In Compose, a raster image (often referred to as a Bitmap) can be loaded up into an ImageBitmap instance, and a BitmapPainter is what is responsible for drawing the bitmap to screen.

For basic use cases, painterResource() can be used to create an ImageBitmap and returns a Painter object (in this case - a BitmapPainter):

Image( painter = painterResource(id = R.drawable.dog), contentDescription = stringResource(id = R.string.dog_content_description) )VectorVsBitmapSnippets.kt

If you require further customization (for example, a custom painter implementation) and need access to the ImageBitmap itself, you can load it in the following way:

val imageBitmap = ImageBitmap.imageResource(R.drawable.dog)VectorVsBitmapSnippets.kt

A VectorPainter is responsible for drawing an ImageVector to screen. ImageVector supports a subset of Scalable Vector Graphics (SVG) commands. Not all images can be represented as vectors (for example, the photos you take with your camera cannot be transformed into a vector).

You can create a custom ImageVector either by importing an existing vector drawable XML file (imported into Android Studio using the import tool) or implementing the class and issuing path commands manually.

For basic use cases, painterResource() works for ImageVectors in the same way it does for the ImageBitmap class, returning a VectorPainter as the result. painterResource() handles the loading of VectorDrawables and BitmapDrawables into VectorPainter and BitmapPainter respectively. To load a VectorDrawable into an image, use:

Image( painter = painterResource(id = R.drawable.baseline_shopping_cart_24), contentDescription = stringResource(id = R.string.shopping_cart_content_desc) )VectorVsBitmapSnippets.kt

If you require further customization and need to access to the ImageVector itself, you can load it in the following way:

val imageVector = ImageVector.vectorResource(id = R.drawable.baseline_shopping_cart_24)VectorVsBitmapSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Add shadows in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/draw/shadows

**Contents:**
- Add shadows in Compose Stay organized with collections Save and categorize content based on your preferences.
- Create basic shadows
- Implement drop shadows
  - Key points about the code
  - Result
- Implement inner shadows
- Animate shadows on user interaction
  - Key points about the code
  - Result
- Create gradient shadows

Shadows visually elevate your UI, indicate interactivity to users, and provide immediate feedback on user actions. Compose provides several ways to incorporate shadows into your app:

Modifier.shadow() is suitable for creating basic shadows, while the dropShadow() and innerShadow() modifiers offer more fine-grained control and precision over shadow rendering.

This page describes how to implement each of these modifiers, including how to animate shadows upon user interaction and how to chain the innerShadow() and dropShadow() modifiers to create gradient shadows, neumorphic shadows, and more.

Modifier.shadow() creates a basic shadow following Material Design guidelines that simulates a light source from above. The shadow depth is based on an elevation value, and the cast shadow is clipped to the shape of the composable.

@Composable fun ElevationBasedShadow() { Box( modifier = Modifier.aspectRatio(1f).fillMaxSize(), contentAlignment = Alignment.Center ) { Box( Modifier .size(100.dp, 100.dp) .shadow(10.dp, RectangleShape) .background(Color.White) ) } }Shadows.kt

Use the dropShadow() modifier to draw an accurate shadow behind your content, which makes the element appear elevated.

You can control the following key aspects through its Shadow parameter:

Additionally, the shape parameter defines the shadow's overall shape. It can use any geometry from the androidx.compose.foundation.shape package, as well as the Material Expressive shapes.

To implement a basic drop shadow, add the dropShadow() modifier onto your composable chain, providing the radius, color, and spread. Note that the purpleColor background that appears on top of the shadow is drawn after the dropShadow() modifier:

@Composable fun SimpleDropShadowUsage() { Box(Modifier.fillMaxSize()) { Box( Modifier .width(300.dp) .height(300.dp) .dropShadow( shape = RoundedCornerShape(20.dp), shadow = Shadow( radius = 10.dp, spread = 6.dp, color = Color(0x40000000), offset = DpOffset(x = 4.dp, 4.dp) ) ) .align(Alignment.Center) .background( color = Color.White, shape = RoundedCornerShape(20.dp) ) ) { Text( "Drop Shadow", modifier = Modifier.align(Alignment.Center), fontSize = 32.sp ) } } }Shadows.kt

To create an inverse effect to dropShadow(), use Modifier.innerShadow(), which creates the illusion that an element is recessed or pressed into the underlying surface.

Order is significant when creating inner shadows. The innerShadow() modifier draws on top of the content. To make sure the shadow is visible, you typically perform the following steps:

If the innerShadow() is placed before the background, the background is drawn over the shadow, hiding it completely.

The following example shows an application of innerShadow() on a RoundedCornerShape:

@Composable fun SimpleInnerShadowUsage() { Box(Modifier.fillMaxSize()) { Box( Modifier .width(300.dp) .height(200.dp) .align(Alignment.Center) // note that the background needs to be defined before defining the inner shadow .background( color = Color.White, shape = RoundedCornerShape(20.dp) ) .innerShadow( shape = RoundedCornerShape(20.dp), shadow = Shadow( radius = 10.dp, spread = 2.dp, color = Color(0x40000000), offset = DpOffset(x = 6.dp, 7.dp) ) ) ) { Text( "Inner Shadow", modifier = Modifier.align(Alignment.Center), fontSize = 32.sp ) } } }Shadows.kt

To make your shadows respond to user interactions, you can integrate shadow properties with Compose's animation APIs. When a user presses a button, for example, the shadow can change to provide instantaneous visual feedback.

The following code creates a "pressed" effect with a shadow (the illusion that the surface is being pushed down into the screen):

@Composable fun AnimatedColoredShadows() { SnippetsTheme { Box(Modifier.fillMaxSize()) { val interactionSource = remember { MutableInteractionSource() } val isPressed by interactionSource.collectIsPressedAsState() // Create transition with pressed state val transition = updateTransition( targetState = isPressed, label = "button_press_transition" ) fun <T> buttonPressAnimation() = tween<T>( durationMillis = 400, easing = EaseInOut ) // Animate all properties using the transition val shadowAlpha by transition.animateFloat( label = "shadow_alpha", transitionSpec = { buttonPressAnimation() } ) { pressed -> if (pressed) 0f else 1f } // ... val blueDropShadow by transition.animateColor( label = "shadow_color", transitionSpec = { buttonPressAnimation() } ) { pressed -> if (pressed) Color.Transparent else blueDropShadowColor } // ... Box( Modifier .clickable( interactionSource, indication = null ) { // ** ...... **// } .width(300.dp) .height(200.dp) .align(Alignment.Center) .dropShadow( shape = RoundedCornerShape(70.dp), shadow = Shadow( radius = 10.dp, spread = 0.dp, color = blueDropShadow, offset = DpOffset(x = 0.dp, -(2).dp), alpha = shadowAlpha ) ) .dropShadow( shape = RoundedCornerShape(70.dp), shadow = Shadow( radius = 10.dp, spread = 0.dp, color = darkBlueDropShadow, offset = DpOffset(x = 2.dp, 6.dp), alpha = shadowAlpha ) ) // note that the background needs to be defined before defining the inner shadow .background( color = Color(0xFFFFFFFF), shape = RoundedCornerShape(70.dp) ) .innerShadow( shape = RoundedCornerShape(70.dp), shadow = Shadow( radius = 8.dp, spread = 4.dp, color = innerShadowColor2, offset = DpOffset(x = 4.dp, 0.dp) ) ) .innerShadow( shape = RoundedCornerShape(70.dp), shadow = Shadow( radius = 20.dp, spread = 4.dp, color = innerShadowColor1, offset = DpOffset(x = 4.dp, 0.dp), alpha = innerShadowAlpha ) ) ) { Text( "Animated Shadows", // ... ) } } } }Shadows.kt

Shadows are not limited to solid colors. The shadow API accepts a Brush, which lets you create gradient shadows.

Box( modifier = Modifier .width(240.dp) .height(200.dp) .dropShadow( shape = RoundedCornerShape(70.dp), shadow = Shadow( radius = 10.dp, spread = animatedSpread.dp, brush = Brush.sweepGradient( colors ), offset = DpOffset(x = 0.dp, y = 0.dp), alpha = animatedAlpha ) ) .clip(RoundedCornerShape(70.dp)) .background(Color(0xEDFFFFFF)), contentAlignment = Alignment.Center ) { Text( text = breathingText, color = Color.Black, style = MaterialTheme.typography.bodyLarge ) }Shadows.kt

You can use a brush as a shadow to create a gradient dropShadow() with a "breathing" animation:

You can combine and layer the dropShadow() and innerShadow() modifiers to create a variety of effects. The following sections show you how to produce neumorphic, neobrutalist, and realistic shadows with this technique.

Neumorphic shadows are characterized by a soft appearance that emerges organically from the background. To create neumorphic shadows, do the following:

The following snippet layers two dropShadow() modifiers to create the neumorphic effect:

@Composable fun NeumorphicRaisedButton( shape: RoundedCornerShape = RoundedCornerShape(30.dp) ) { val bgColor = Color(0xFFe0e0e0) val lightShadow = Color(0xFFFFFFFF) val darkShadow = Color(0xFFb1b1b1) val upperOffset = -10.dp val lowerOffset = 10.dp val radius = 15.dp val spread = 0.dp Box( modifier = Modifier .fillMaxSize() .background(bgColor) .wrapContentSize(Alignment.Center) .size(240.dp) .dropShadow( shape, shadow = Shadow( radius = radius, color = lightShadow, spread = spread, offset = DpOffset(upperOffset, upperOffset) ), ) .dropShadow( shape, shadow = Shadow( radius = radius, color = darkShadow, spread = spread, offset = DpOffset(lowerOffset, lowerOffset) ), ) .background(bgColor, shape) ) }Shadows.kt

The neobrutalist style showcases high-contrast, blocky layouts, vivid colors, and thick borders. To create this effect, use a dropShadow() with zero blur and a distinct offset, as shown in the following snippet:

@Composable fun NeoBrutalShadows() { SnippetsTheme { val dropShadowColor = Color(0xFF007AFF) val borderColor = Color(0xFFFF2D55) Box(Modifier.fillMaxSize()) { Box( Modifier .width(300.dp) .height(200.dp) .align(Alignment.Center) .dropShadow( shape = RoundedCornerShape(0.dp), shadow = Shadow( radius = 0.dp, spread = 0.dp, color = dropShadowColor, offset = DpOffset(x = 8.dp, 8.dp) ) ) .border( 8.dp, borderColor ) .background( color = Color.White, shape = RoundedCornerShape(0.dp) ) ) { Text( "Neobrutal Shadows", modifier = Modifier.align(Alignment.Center), style = MaterialTheme.typography.bodyMedium ) } } } }Shadows.kt

Realistic shadows mimic shadows in the physical world— they appear lit by a primary light source, resulting in both a direct shadow and a more diffuse shadow. You can stack multiple dropShadow() and innerShadow() instances with different properties to recreate realistic shadow effects, as shown in the following snippet:

@Composable fun RealisticShadows() { Box(Modifier.fillMaxSize()) { val dropShadowColor1 = Color(0xB3000000) val dropShadowColor2 = Color(0x66000000) val innerShadowColor1 = Color(0xCC000000) val innerShadowColor2 = Color(0xFF050505) val innerShadowColor3 = Color(0x40FFFFFF) val innerShadowColor4 = Color(0x1A050505) Box( Modifier .width(300.dp) .height(200.dp) .align(Alignment.Center) .dropShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 40.dp, spread = 0.dp, color = dropShadowColor1, offset = DpOffset(x = 2.dp, 8.dp) ) ) .dropShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 4.dp, spread = 0.dp, color = dropShadowColor2, offset = DpOffset(x = 0.dp, 4.dp) ) ) // note that the background needs to be defined before defining the inner shadow .background( color = Color.Black, shape = RoundedCornerShape(100.dp) ) // // .innerShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 12.dp, spread = 3.dp, color = innerShadowColor1, offset = DpOffset(x = 6.dp, 6.dp) ) ) .innerShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 4.dp, spread = 1.dp, color = Color.White, offset = DpOffset(x = 5.dp, 5.dp) ) ) .innerShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 12.dp, spread = 5.dp, color = innerShadowColor2, offset = DpOffset(x = (-3).dp, (-12).dp) ) ) .innerShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 3.dp, spread = 10.dp, color = innerShadowColor3, offset = DpOffset(x = 0.dp, 0.dp) ) ) .innerShadow( shape = RoundedCornerShape(100.dp), shadow = Shadow( radius = 3.dp, spread = 9.dp, color = innerShadowColor4, offset = DpOffset(x = 1.dp, 1.dp) ) ) ) { Text( "Realistic Shadows", modifier = Modifier.align(Alignment.Center), fontSize = 24.sp, color = Color.White ) } } }Shadows.kt

The previous code snippet produces the following:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Optimizing performance for images Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/images/optimization

**Contents:**
- Optimizing performance for images Stay organized with collections Save and categorize content based on your preferences.
- Only load the size of the bitmap you need
    - Use vectors over bitmaps where possible
    - Supply alternative resources for different screen sizes
    - When using ImageBitmap, call prepareToDraw before drawing
    - Prefer passing a Int DrawableRes or URL as parameters into your composable instead of Painter
- Don’t store a bitmap in memory longer than you need it
- Don’t package large images with your AAB/APK file
- Recommended for you

Working with images can quickly introduce performance issues if you aren't careful. You can quite easily run into an OutOfMemoryError when working with large bitmaps. Follow these best practices to ensure your app performs at its best.

Most smartphones have high resolution cameras that produce large image files. If you're showing an image on screen, you must either reduce the resolution of the image or only load the image up to the size of your image container. The constant loading of larger than needed images can exhaust GPU caches, leading to less performant UI rendering.

To manage image sizes:

When representing something visually on screen, you need to decide if it can be represented as a vector or not. Prefer vector images over bitmaps, as they don’t pixelate when you scale them to different sizes. However, not everything can be represented as a vector - images taken with a camera can’t be converted into a vector.

If you are shipping images with your app, consider supplying different sized assets for different device resolutions. This can help reduce the download size of your app on devices, and improve performance as it’ll load up a lower resolution image on a lower resolution device. For more information on providing alternative bitmaps for different device sizes, check out the alternative bitmap documentation.

When using ImageBitmap, to start the process of uploading the texture to the GPU, call ImageBitmap#prepareToDraw() before actually drawing it. This helps the GPU prepare the texture and improve the performance of showing a visual on screen. Most image loading libraries already do this optimization, but if you are working with the ImageBitmap class yourself, it is something to keep in mind.

Due to the complexities of dealing with images (for example, writing an equals function for Bitmaps would be computationally expensive), the Painter API is explicitly not marked as a Stable class. Unstable classes can lead to unnecessary recompositions because the compiler cannot easily infer if the data has changed.

Therefore, it is preferable to pass a URL or drawable resource ID as parameters to your composable, instead of passing a Painter as a parameter.

The more bitmaps you load into memory, the more likely it is that you could run out of memory on the device. For instance, if loading a large list of Image composables on screen, use LazyColumn or LazyRow to ensure that memory is freed up when scrolling a large list.

One of the top causes for large app download size is due to graphics that are packaged inside the AAB or APK file. Use the APK analyzer tool to ensure that you aren't packaging larger than required image files. Reduce the sizes or consider placing the images on a server and only downloading them when required.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (python):
```python
// Prefer this:
@Composable
fun MyImage(url: String) {

}
// Over this:
@Composable
fun MyImage(painter: Painter) {

}
```

---

## Graphics in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/graphics/draw/overview

**Contents:**
- Graphics in Compose Stay organized with collections Save and categorize content based on your preferences.
- Basic drawing with modifiers and DrawScope
- Coordinate system
- Basic transformations
  - Scale
  - Translate
  - Rotate
  - Inset
  - Multiple transformations
- Common drawing operations

Many apps need to be able to precisely control exactly what's drawn on the screen. This might be as small as putting a box or a circle on the screen in just the right place, or it might be an elaborate arrangement of graphic elements in many different styles.

The core way to draw something custom in Compose is with modifiers, such as Modifier.drawWithContent, Modifier.drawBehind, and Modifier.drawWithCache.

For example, to draw something behind your composable, you can use the drawBehind modifier to start executing drawing commands:

Spacer( modifier = Modifier .fillMaxSize() .drawBehind { // this = DrawScope } )CanvasSnippets.kt

If all you need is a composable that draws, you can use the Canvas composable. The Canvas composable is a convenient wrapper around Modifier.drawBehind. You place the Canvas in your layout the same way you would with any other Compose UI element. Within the Canvas, you can draw elements with precise control over their style and location.

All drawing modifiers expose a DrawScope, a scoped drawing environment that maintains its own state. This lets you set the parameters for a group of graphical elements. The DrawScope provides several useful fields, like size, a Size object specifying the current dimensions of the DrawScope.

To draw something, you can use one of the many draw functions on DrawScope. For example, the following code draws a rectangle in the top left corner of the screen:

Canvas(modifier = Modifier.fillMaxSize()) { val canvasQuadrantSize = size / 2F drawRect( color = Color.Magenta, size = canvasQuadrantSize ) }CanvasSnippets.kt

To learn more about different drawing modifiers, see the Graphics Modifiers documentation.

To draw something on screen, you need to know the offset (x and y) and size of your item. With many of the draw methods on DrawScope, the position and size are provided by default parameter values. The default parameters generally position the item at the [0, 0] point on the canvas, and provide a default size that fills the entire drawing area, as in the example above - you can see the rectangle is positioned in the top left. To adjust the size and position of your item, you need to understand the coordinate system in Compose.

The origin of the coordinate system ([0,0]) is at the top leftmost pixel in the drawing area. x increases as it moves right and y increases as it moves downwards.

For example, if you want to draw a diagonal line from the top-right corner of the canvas area to the bottom-left corner, you can use the DrawScope.drawLine() function, and specify a start and end offset with the corresponding x and y positions:

Canvas(modifier = Modifier.fillMaxSize()) { val canvasWidth = size.width val canvasHeight = size.height drawLine( start = Offset(x = canvasWidth, y = 0f), end = Offset(x = 0f, y = canvasHeight), color = Color.Blue ) }CanvasSnippets.kt

DrawScope offers transformations to change where or how the drawing commands are executed.

Use DrawScope.scale() to increase the size of your drawing operations by a factor. Operations like scale() apply to all drawing operations within the corresponding lambda. For example, the following code increases the scaleX 10 times and scaleY 15 times:

Canvas(modifier = Modifier.fillMaxSize()) { scale(scaleX = 10f, scaleY = 15f) { drawCircle(Color.Blue, radius = 20.dp.toPx()) } }CanvasSnippets.kt

Use DrawScope.translate() to move your drawing operations up, down, left, or right. For example, the following code moves the drawing 100 px to the right and 300 px up:

Canvas(modifier = Modifier.fillMaxSize()) { translate(left = 100f, top = -300f) { drawCircle(Color.Blue, radius = 200.dp.toPx()) } }CanvasSnippets.kt

Use DrawScope.rotate() to rotate your drawing operations around a pivot point. For example, the following code rotates a rectangle 45 degrees:

Canvas(modifier = Modifier.fillMaxSize()) { rotate(degrees = 45F) { drawRect( color = Color.Gray, topLeft = Offset(x = size.width / 3F, y = size.height / 3F), size = size / 3F ) } }CanvasSnippets.kt

Use DrawScope.inset() to adjust the default parameters of the current DrawScope, changing the drawing boundaries and translating the drawings accordingly:

Canvas(modifier = Modifier.fillMaxSize()) { val canvasQuadrantSize = size / 2F inset(horizontal = 50f, vertical = 30f) { drawRect(color = Color.Green, size = canvasQuadrantSize) } }CanvasSnippets.kt

This code effectively adds padding to the drawing commands:

To apply multiple transformations to your drawings, use the DrawScope.withTransform() function, which creates and applies a single transformation that combines all your desired changes. Using withTransform() is more efficient than making nested calls to individual transformations, because all the transformations are performed together in a single operation, instead of Compose needing to calculate and save each of the nested transformations.

For example, the following code applies both a translation and a rotation to the rectangle:

Canvas(modifier = Modifier.fillMaxSize()) { withTransform({ translate(left = size.width / 5F) rotate(degrees = 45F) }) { drawRect( color = Color.Gray, topLeft = Offset(x = size.width / 3F, y = size.height / 3F), size = size / 3F ) } }CanvasSnippets.kt

To draw text in Compose, you can typically use the Text composable. However, if you are in a DrawScope or you want to draw your text manually with customization, you can use the DrawScope.drawText() method.

To draw text, create a TextMeasurer using rememberTextMeasurer and call drawText with the measurer:

val textMeasurer = rememberTextMeasurer() Canvas(modifier = Modifier.fillMaxSize()) { drawText(textMeasurer, "Hello") }CanvasSnippets.kt

Drawing text works a bit differently from other drawing commands. Normally, you give the drawing command the size (width and height) to draw the shape/image as. With text, there are a few parameters that control the size of the rendered text, such as font size, font, ligatures, and letter spacing.

With Compose, you can use a TextMeasurer to get access to the measured size of text, depending on the above factors. If you want to draw a background behind the text, you can use the measured information to get the size of the area that the text takes up:

val textMeasurer = rememberTextMeasurer() Spacer( modifier = Modifier .drawWithCache { val measuredText = textMeasurer.measure( AnnotatedString(longTextSample), constraints = Constraints.fixedWidth((size.width * 2f / 3f).toInt()), style = TextStyle(fontSize = 18.sp) ) onDrawBehind { drawRect(pinkColor, size = measuredText.size.toSize()) drawText(measuredText) } } .fillMaxSize() )CanvasSnippets.kt

This code snippet produces a pink background on the text:

Adjusting the constraints, font size, or any property that affects measured size results in a new size reported. You can set a fixed size for both the width and height, and the text then follows the set TextOverflow. For example, the following code renders text in ⅓ of the height and ⅓ of the width of the composable area, and sets the TextOverflow to TextOverflow.Ellipsis:

val textMeasurer = rememberTextMeasurer() Spacer( modifier = Modifier .drawWithCache { val measuredText = textMeasurer.measure( AnnotatedString(longTextSample), constraints = Constraints.fixed( width = (size.width / 3f).toInt(), height = (size.height / 3f).toInt() ), overflow = TextOverflow.Ellipsis, style = TextStyle(fontSize = 18.sp) ) onDrawBehind { drawRect(pinkColor, size = measuredText.size.toSize()) drawText(measuredText) } } .fillMaxSize() )CanvasSnippets.kt

The text is now drawn in the constraints with an ellipsis at the end:

To draw an ImageBitmap with DrawScope, load up the image using ImageBitmap.imageResource() and then call drawImage:

val dogImage = ImageBitmap.imageResource(id = R.drawable.dog) Canvas(modifier = Modifier.fillMaxSize(), onDraw = { drawImage(dogImage) })CanvasSnippets.kt

There are many shape drawing functions on DrawScope. To draw a shape, use one of the predefined draw functions, such as drawCircle:

val purpleColor = Color(0xFFBA68C8) Canvas( modifier = Modifier .fillMaxSize() .padding(16.dp), onDraw = { drawCircle(purpleColor) } )CanvasSnippets.kt

A path is a series of mathematical instructions that result in a drawing once executed. DrawScope can draw a path using the DrawScope.drawPath() method.

For example, say you wanted to draw a triangle. You can generate a path with functions such as lineTo() and moveTo() using the size of the drawing area. Then, call drawPath() with this newly created path to get a triangle.

Spacer( modifier = Modifier .drawWithCache { val path = Path() path.moveTo(0f, 0f) path.lineTo(size.width / 2f, size.height / 2f) path.lineTo(size.width, 0f) path.close() onDrawBehind { drawPath(path, Color.Magenta, style = Stroke(width = 10f)) } } .fillMaxSize() )CanvasSnippets.kt

With DrawScope, you don't have direct access to a Canvas object. You can use DrawScope.drawIntoCanvas() to get access to the Canvas object itself that you can call functions on.

For example, if you have a custom Drawable that you'd like to draw onto the canvas, you can access the canvas and call Drawable#draw(), passing in the Canvas object:

val drawable = ShapeDrawable(OvalShape()) Spacer( modifier = Modifier .drawWithContent { drawIntoCanvas { canvas -> drawable.setBounds(0, 0, size.width.toInt(), size.height.toInt()) drawable.draw(canvas.nativeCanvas) } } .fillMaxSize() )CanvasSnippets.kt

For more information on Drawing in Compose, take a look at the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---
