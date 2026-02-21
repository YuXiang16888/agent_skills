# Jetpack-Compose - Design Systems

**Pages:** 4

---

## Material Design 3 in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/material3

**Contents:**
- Material Design 3 in Compose Stay organized with collections Save and categorize content based on your preferences.
- Dependency
  - Experimental APIs
- Material theming
  - Color scheme
    - Generate color schemes
    - Dynamic color schemes
    - Color usage
  - Typography
    - Define typography

Jetpack Compose offers an implementation of Material You and Material 3 Expressive, the next evolution of Material Design. M3 Expressive is an expansion of Material Design 3, including research-backed updates to theming, components, motion, typography, and more — all designed to help you make engaging and desirable products that users love. It also supports Material You personalization features like dynamic color. M3 Expressive complements the Android 16 visual style and system UI.

Below, we demonstrate the Material Design 3 implementation using the Reply sample app as an example. The Reply sample is based entirely on Material Design 3.

To start using Material 3 in your Compose app, add the Compose Material 3 dependency to your build.gradle files:

Once the dependency is added, you can start adding Material Design systems, including color, typography, and shape, to your apps.

Some M3 APIs are considered experimental. In such cases you need to opt in at the function or file level using the ExperimentalMaterial3Api annotation:

// import androidx.compose.material3.ExperimentalMaterial3Api @Composable fun AppComposable() { // M3 composables } Material3Snippets.kt

An M3 theme contains the following subsystems: color scheme, typography and shapes. When you customize these values, your changes are automatically reflected in the M3 components you use to build your app.

Jetpack Compose implements these concepts with the M3 MaterialTheme composable:

MaterialTheme( colorScheme = /* ... typography = /* ... shapes = /* ... ) { // M3 app content }Material3Snippets.kt

To theme your application content, define the color scheme, typography, and shapes specific to your app.

The foundation of a color scheme is the set of five key colors. Each of these colors relate to a tonal palette of 13 tones, which are used by Material 3 components. For example, this is the color scheme for light theme for Reply:

Read more about the Color scheme and color roles.

While you can create a custom ColorScheme manually, it’s often easier to generate one using source colors from your brand. The Material Theme Builder tool allows you to do this, and optionally export Compose theming code. The following files are generated:

val md_theme_light_primary = Color(0xFF476810) val md_theme_light_onPrimary = Color(0xFFFFFFFF) val md_theme_light_primaryContainer = Color(0xFFC7F089) // .. // .. val md_theme_dark_primary = Color(0xFFACD370) val md_theme_dark_onPrimary = Color(0xFF213600) val md_theme_dark_primaryContainer = Color(0xFF324F00) // .. // ..Material3Snippets.kt

private val LightColorScheme = lightColorScheme( primary = md_theme_light_primary, onPrimary = md_theme_light_onPrimary, primaryContainer = md_theme_light_primaryContainer, // .. ) private val DarkColorScheme = darkColorScheme( primary = md_theme_dark_primary, onPrimary = md_theme_dark_onPrimary, primaryContainer = md_theme_dark_primaryContainer, // .. ) @Composable fun ReplyTheme( darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit ) { val colorScheme = if (!darkTheme) { LightColorScheme } else { DarkColorScheme } MaterialTheme( colorScheme = colorScheme, content = content ) }Material3Snippets.kt

To support light and dark themes, use isSystemInDarkTheme(). Based on the system setting, define which color scheme to use: light or dark.

Dynamic color is the key part of Material You, in which an algorithm derives custom colors from a user’s wallpaper to be applied to their apps and system UI. This color palette is used as the starting point to generate light and dark color schemes.

Dynamic color is available on Android 12 and above. If dynamic color is available, you can set up a dynamic ColorScheme. If not, you should fall back to using a custom light or dark ColorScheme.

ColorScheme provides builder functions to create a dynamic light or dark color scheme:

// Dynamic color is available on Android 12+ val dynamicColor = Build.VERSION.SDK_INT >= Build.VERSION_CODES.S val colors = when { dynamicColor && darkTheme -> dynamicDarkColorScheme(LocalContext.current) dynamicColor && !darkTheme -> dynamicLightColorScheme(LocalContext.current) darkTheme -> DarkColorScheme else -> LightColorScheme }Material3Snippets.kt

You can access Material theme colors in your app via MaterialTheme.colorScheme:

Text( text = "Hello theming", color = MaterialTheme.colorScheme.primary )Material3Snippets.kt

Each color role can be used in a variety of places depending on the component’s state, prominence, and emphasis.

The Reply sample app design uses on-primary-container color on top of primary-container to put emphasis on the selected item.

Card( colors = CardDefaults.cardColors( containerColor = if (isSelected) MaterialTheme.colorScheme.primaryContainer else MaterialTheme.colorScheme.surfaceVariant ) ) { Text( text = "Dinner club", style = MaterialTheme.typography.bodyLarge, color = if (isSelected) MaterialTheme.colorScheme.onPrimaryContainer else MaterialTheme.colorScheme.onSurface, ) } Material3Snippets.kt

Here you can see in the Reply Navigation drawer how secondary and tertiary container colors are used in contrast to create emphasis and accent.

Material Design 3 defines a type scale, including text styles that have been adapted from Material Design 2. The naming and grouping have been simplified to: display, headline, title, body, and label, with large, medium, and small sizes for each.

Compose provides the M3 Typography class — along with the existing TextStyle and font-related classes — to model the Material 3 type scale. The Typography constructor offers defaults for each style so you can omit any parameters you do not want to customize:

val replyTypography = Typography( titleLarge = TextStyle( fontWeight = FontWeight.SemiBold, fontSize = 22.sp, lineHeight = 28.sp, letterSpacing = 0.sp ), titleMedium = TextStyle( fontWeight = FontWeight.SemiBold, fontSize = 16.sp, lineHeight = 24.sp, letterSpacing = 0.15.sp ), // .. ) // ..Material3Snippets.kt

Your product will likely not need all 15 default styles from the Material Design type scale. In this example, five sizes are chosen for a reduced set while the rest are omitted.

You can customize your typography by changing default values of TextStyle and font-related properties like fontFamily and letterSpacing.

bodyLarge = TextStyle( fontWeight = FontWeight.Normal, fontFamily = FontFamily.SansSerif, fontStyle = FontStyle.Italic, fontSize = 16.sp, lineHeight = 24.sp, letterSpacing = 0.15.sp, baselineShift = BaselineShift.Subscript ),Material3Snippets.kt

Once you have defined your Typography, pass it to the M3 MaterialTheme:

MaterialTheme( typography = replyTypography, ) { // M3 app Content }Material3Snippets.kt

You can retrieve the typography provided to the M3 MaterialTheme composable by using MaterialTheme.typography:

Text( text = "Hello M3 theming", style = MaterialTheme.typography.titleLarge ) Text( text = "you are learning typography", style = MaterialTheme.typography.bodyMedium )Material3Snippets.kt

You can read more about the Material guidelines on applying typography.

Material surfaces can be displayed in different shapes. Shapes direct attention, identify components, communicate state, and express brand.

The shape scale defines the style of container corners, offering a range of roundedness from square to fully circular.

Compose provides the M3 Shapes class with expanded parameters to support new M3 shapes. The M3 shape scale is more like the type scale, enabling an expressive range of shapes across the UI.

There are different sizes of shapes:

By default, each shape has a default value, but you can override those:

val replyShapes = Shapes( extraSmall = RoundedCornerShape(4.dp), small = RoundedCornerShape(8.dp), medium = RoundedCornerShape(12.dp), large = RoundedCornerShape(16.dp), extraLarge = RoundedCornerShape(24.dp) )Material3Snippets.kt

Once you have defined your Shapes, you can pass it to the M3 MaterialTheme:

MaterialTheme( shapes = replyShapes, ) { // M3 app Content }Material3Snippets.kt

You can customize the shape scale for all components in the MaterialTheme or you can do it on a per component basis.

Apply medium and large shape with default values:

Card(shape = MaterialTheme.shapes.medium) { /* card content */ } FloatingActionButton( shape = MaterialTheme.shapes.large, onClick = { } ) { /* fab content */ }Material3Snippets.kt

There are two other shapes — RectangleShape and CircleShape — which are part of Compose. Rectangle shape is with no border radius and circle shape shows full circled edges:

Card(shape = RectangleShape) { /* card content */ } Card(shape = CircleShape) { /* card content */ }Material3Snippets.kt

The examples below demonstrate some of the components with default shape values applied to them:

You can read more about the Material guidelines on applying shape.

Emphasis in M3 is provided using variations of color and its on-color combinations. In M3, there are two ways to add emphasis to your UI:

bodyLarge = TextStyle( fontWeight = FontWeight.Bold ), bodyMedium = TextStyle( fontWeight = FontWeight.Normal )Material3Snippets.kt

Material 3 represents elevation mainly using tonal color overlays. This is a new way to differentiate containers and surfaces from each other — increasing tonal elevation uses a more prominent tone — in addition to shadows.

Elevation overlays in dark themes have also changed to tonal color overlays in Material 3. The overlay color comes from the primary color slot.

The M3 Surface — the backing composable behind most M3 components — includes support for both tonal and shadow elevation:

Surface( modifier = Modifier, tonalElevation = /*... shadowElevation = /*... ) { Column(content = content) }Material3Snippets.kt

Material Design comes with a rich set of Material components (such as buttons, chips, cards, navigation bar) which already follow Material Theming and help you make beautiful Material Design apps. You can start using components with default properties right out of the box.

Button(onClick = { /*..*/ }) { Text(text = "My Button") }Material3Snippets.kt

M3 provides many versions of the same components to be used in different roles according to emphasis and attention.

ExtendedFloatingActionButton( onClick = { /*..*/ }, modifier = Modifier ) { Icon( imageVector = Icons.Default.Edit, contentDescription = stringResource(id = R.string.edit), ) Text( text = stringResource(id = R.string.add_entry), ) }Material3Snippets.kt

Button(onClick = { /*..*/ }) { Text(text = stringResource(id = R.string.view_entry)) }Material3Snippets.kt

TextButton(onClick = { /*..*/ }) { Text(text = stringResource(id = R.string.replated_articles)) }Material3Snippets.kt

You can read more about Material buttons and other components. Material 3 provides a wide variety of component suites such as Buttons, App bars, Navigation components that are specifically designed for different use cases and screen sizes.

Material also provides several navigation components that help you implement navigation, depending on different screen sizes and states.

NavigationBar is used for compact devices when you want to target 5 or less destinations:

NavigationBar(modifier = Modifier.fillMaxWidth()) { Destinations.entries.forEach { replyDestination -> NavigationBarItem( selected = selectedDestination == replyDestination, onClick = { }, icon = { } ) } }Material3Snippets.kt

NavigationRail is used for small-to-medium size tablets or phones in landscape mode. It provides ergonomics to users and improves the user experience for those devices.

NavigationRail( modifier = Modifier.fillMaxHeight(), ) { Destinations.entries.forEach { replyDestination -> NavigationRailItem( selected = selectedDestination == replyDestination, onClick = { }, icon = { } ) } }Material3Snippets.kt

Reply using both in default theming to provide immersive user experience for all device sizes.

NavigationDrawer is used for medium-to-large size tablets where you have enough space to show detail. You can use both PermanentNavigationDrawer or ModalNavigationDrawer along with NavigationRail.

PermanentNavigationDrawer(modifier = Modifier.fillMaxHeight(), drawerContent = { Destinations.entries.forEach { replyDestination -> NavigationRailItem( selected = selectedDestination == replyDestination, onClick = { }, icon = { }, label = { } ) } }) { }Material3Snippets.kt

Navigation options enhance the user experience, ergonomics and reachability. You can learn more about Material navigation components in the Compose adaptive codelab.

M3 encourages personalization and flexibility. All components have default colors applied to them but expose flexible APIs to customize their colors if required.

Most components, like cards and buttons, provide a default object exposing color and elevation interfaces that can be modified to customize your component:

val customCardColors = CardDefaults.cardColors( contentColor = MaterialTheme.colorScheme.primary, containerColor = MaterialTheme.colorScheme.primaryContainer, disabledContentColor = MaterialTheme.colorScheme.surface, disabledContainerColor = MaterialTheme.colorScheme.onSurface, ) val customCardElevation = CardDefaults.cardElevation( defaultElevation = 8.dp, pressedElevation = 2.dp, focusedElevation = 4.dp ) Card( colors = customCardColors, elevation = customCardElevation ) { // m3 card content }Material3Snippets.kt

You can read more about customizing Material 3.

Some aspects of Material You come from the new visual style and system UI on Android 12 and above. Two key areas where there are changes are ripple and overscroll. No additional work is required to implement these changes.

Ripple now uses a subtle sparkle to illuminate surfaces when pressed. Compose Material Ripple uses a platform RippleDrawable under the hood on Android, so sparkle ripple is available on Android 12 and above for all Material components.

Overscroll now uses a stretch effect at the edge of scrolling containers. Stretch overscroll is on by default in scrolling container composables — for example, LazyColumn, LazyRow, and LazyVerticalGrid — in Compose Foundation 1.1.0 and above, regardless of API level.

Accessibility standards built into Material components are designed to provide a foundation for inclusive product design. Understanding your product’s accessibility can enhance usability for all users, including those with low vision, blindness, hearing impairments, cognitive impairments, motor impairments, or situational disabilities (such as a broken arm).

Dynamic color is designed to meet accessibility standards for color contrast. The system of tonal palettes is critical to making any color scheme accessible by default.

Material's color system provides standard tone values and measurements that can be used to meet accessible contrast ratios.

All Material components and dynamic theming already use the above color roles from a set of tonal palettes, selected to meet accessibility requirements. However, if you are customizing components, make sure to use appropriate color roles and avoid mismatch.

Use on-primary on top of primary, and on-primary-container on top of primary-container, and the same for other accent and neutral colors to provide accessible contrast to the user.

The use of a tertiary container on top of primary gives the user a poor contrast button:

// ✅ Button with sufficient contrast ratio Button( onClick = { }, colors = ButtonDefaults.buttonColors( containerColor = MaterialTheme.colorScheme.primary, contentColor = MaterialTheme.colorScheme.onPrimary ) ) { } // ❌ Button with poor contrast ratio Button( onClick = { }, colors = ButtonDefaults.buttonColors( containerColor = MaterialTheme.colorScheme.tertiaryContainer, contentColor = MaterialTheme.colorScheme.primaryContainer ) ) { }Material3Snippets.kt

The M3 type scale updates the static type ramp and values to offer a simplified but dynamic framework of size categories that scale across devices.

For example, in M3, Display Small can be assigned different values depending upon the device context, such as a phone or a tablet.

Material provides guidance on adaptive layouts and foldables to make your apps accessible and improve the ergonomics of users holding large devices.

Material provides different kinds of navigation to help you provide better user experience for large devices.

You can learn more about Android large screen app quality guidelines and see our Reply sample for adaptive and accessible design.

To learn more about Material Theming in Compose, check out the following resources:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (bash):
```bash
implementation "androidx.compose.material3:material3:$material3_version"
```

---

## Custom design systems in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/custom

**Contents:**
- Custom design systems in Compose Stay organized with collections Save and categorize content based on your preferences.
- Extend Material Theming
  - Use Material components
- Replace Material subsystems
  - Use Material components
- Implement a fully custom design system
  - Use Material components
- Recommended for you

While Material is our recommended design system and Jetpack Compose ships an implementation of Material, you are not forced to use it. Material is built entirely on public APIs, so it's possible to create your own design system in the same manner.

There are several approaches you might take:

You may also want to continue using Material components with a custom design system. It's possible to do this but there are things to keep in mind to suit the approach you've taken.

To learn more about the lower-level constructs and APIs used by MaterialTheme and custom design systems, check out the Anatomy of a theme in Compose guide.

Compose Material closely models Material Theming to make it straightforward and type-safe to follow the Material guidelines. However, it's possible to extend the color, typography, and shape sets with additional values. The simplest approach is to add extension properties:

// Use with MaterialTheme.colorScheme.snackbarAction val ColorScheme.snackbarAction: Color @Composable get() = if (isSystemInDarkTheme()) Red300 else Red700 // Use with MaterialTheme.typography.textFieldInput val Typography.textFieldInput: TextStyle get() = TextStyle(/* ... */) // Use with MaterialTheme.shapes.card val Shapes.card: Shape get() = RoundedCornerShape(size = 20.dp)CustomDesignSystem.kt

This provides consistency with MaterialTheme usage APIs. An example of this defined by Compose itself is surfaceColorAtElevation, which determines the surface color that should be used depending on the elevation.

Another approach is to define an extended theme that "wraps" MaterialTheme and its values.

Suppose you want to add two additional colors — caution and onCaution, a yellow color used for actions that are semi-dangerous — whilst keeping the existing Material colors:

@Immutable data class ExtendedColors( val caution: Color, val onCaution: Color ) val LocalExtendedColors = staticCompositionLocalOf { ExtendedColors( caution = Color.Unspecified, onCaution = Color.Unspecified ) } @Composable fun ExtendedTheme( /* ... */ content: @Composable () -> Unit ) { val extendedColors = ExtendedColors( caution = Color(0xFFFFCC02), onCaution = Color(0xFF2C2D30) ) CompositionLocalProvider(LocalExtendedColors provides extendedColors) { MaterialTheme( /* colors = ..., typography = ..., shapes = ... */ content = content ) } } // Use with eg. ExtendedTheme.colors.caution object ExtendedTheme { val colors: ExtendedColors @Composable get() = LocalExtendedColors.current }CustomDesignSystem.kt

This is similar to MaterialTheme usage APIs. It also supports multiple themes as you can nest ExtendedThemes in the same way as MaterialTheme.

When extending Material Theming, existing MaterialTheme values are maintained and Material components still have reasonable defaults.

If you want to use extended values in components, wrap them in your own composable functions, directly setting the values you want to alter, and exposing others as parameters to the containing composable:

@Composable fun ExtendedButton( onClick: () -> Unit, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Button( colors = ButtonDefaults.buttonColors( containerColor = ExtendedTheme.colors.caution, contentColor = ExtendedTheme.colors.onCaution /* Other colors use values from MaterialTheme */ ), onClick = onClick, modifier = modifier, content = content ) }CustomDesignSystem.kt

You would then replace usages of Button with ExtendedButton where appropriate.

@Composable fun ExtendedApp() { ExtendedTheme { /*...*/ ExtendedButton(onClick = { /* ... */ }) { /* ... */ } } }CustomDesignSystem.kt

Instead of extending Material Theming, you may want to replace one or more systems — Colors, Typography, or Shapes — with a custom implementation, while maintaining the others.

Suppose you want to replace the type and shape systems while keeping the color system:

@Immutable data class ReplacementTypography( val body: TextStyle, val title: TextStyle ) @Immutable data class ReplacementShapes( val component: Shape, val surface: Shape ) val LocalReplacementTypography = staticCompositionLocalOf { ReplacementTypography( body = TextStyle.Default, title = TextStyle.Default ) } val LocalReplacementShapes = staticCompositionLocalOf { ReplacementShapes( component = RoundedCornerShape(ZeroCornerSize), surface = RoundedCornerShape(ZeroCornerSize) ) } @Composable fun ReplacementTheme( /* ... */ content: @Composable () -> Unit ) { val replacementTypography = ReplacementTypography( body = TextStyle(fontSize = 16.sp), title = TextStyle(fontSize = 32.sp) ) val replacementShapes = ReplacementShapes( component = RoundedCornerShape(percent = 50), surface = RoundedCornerShape(size = 40.dp) ) CompositionLocalProvider( LocalReplacementTypography provides replacementTypography, LocalReplacementShapes provides replacementShapes ) { MaterialTheme( /* colors = ... */ content = content ) } } // Use with eg. ReplacementTheme.typography.body object ReplacementTheme { val typography: ReplacementTypography @Composable get() = LocalReplacementTypography.current val shapes: ReplacementShapes @Composable get() = LocalReplacementShapes.current }CustomDesignSystem.kt

When one or more systems of MaterialTheme have been replaced, using Material components as-is may result in unwanted Material color, type, or shape values.

If you want to use replacement values in components, wrap them in your own composable functions, directly setting the values for the relevant system, and exposing others as parameters to the containing composable.

@Composable fun ReplacementButton( onClick: () -> Unit, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Button( shape = ReplacementTheme.shapes.component, onClick = onClick, modifier = modifier, content = { ProvideTextStyle( value = ReplacementTheme.typography.body ) { content() } } ) }CustomDesignSystem.kt

You would then replace usages of Button with ReplacementButton where appropriate.

@Composable fun ReplacementApp() { ReplacementTheme { /*...*/ ReplacementButton(onClick = { /* ... */ }) { /* ... */ } } }CustomDesignSystem.kt

You may want to replace Material Theming with a fully custom design system. Consider that MaterialTheme provides the following systems:

If you want to continue using Material components, you must replace some of these systems in your custom themes or handle the systems in your components to avoid unwanted behavior.

However, design systems are not limited to the concepts Material relies on. You can modify existing systems and introduce entirely new ones — with new classes and types — to make other concepts compatible with themes.

In the following code, we model a custom color system that includes gradients (List<Color>), include a type system, introduce a new elevation system, and exclude other systems provided by MaterialTheme:

@Immutable data class CustomColors( val content: Color, val component: Color, val background: List<Color> ) @Immutable data class CustomTypography( val body: TextStyle, val title: TextStyle ) @Immutable data class CustomElevation( val default: Dp, val pressed: Dp ) val LocalCustomColors = staticCompositionLocalOf { CustomColors( content = Color.Unspecified, component = Color.Unspecified, background = emptyList() ) } val LocalCustomTypography = staticCompositionLocalOf { CustomTypography( body = TextStyle.Default, title = TextStyle.Default ) } val LocalCustomElevation = staticCompositionLocalOf { CustomElevation( default = Dp.Unspecified, pressed = Dp.Unspecified ) } @Composable fun CustomTheme( /* ... */ content: @Composable () -> Unit ) { val customColors = CustomColors( content = Color(0xFFDD0D3C), component = Color(0xFFC20029), background = listOf(Color.White, Color(0xFFF8BBD0)) ) val customTypography = CustomTypography( body = TextStyle(fontSize = 16.sp), title = TextStyle(fontSize = 32.sp) ) val customElevation = CustomElevation( default = 4.dp, pressed = 8.dp ) CompositionLocalProvider( LocalCustomColors provides customColors, LocalCustomTypography provides customTypography, LocalCustomElevation provides customElevation, content = content ) } // Use with eg. CustomTheme.elevation.small object CustomTheme { val colors: CustomColors @Composable get() = LocalCustomColors.current val typography: CustomTypography @Composable get() = LocalCustomTypography.current val elevation: CustomElevation @Composable get() = LocalCustomElevation.current }CustomDesignSystem.kt

When no MaterialTheme is present, using Material components as-is will result in unwanted Material color, type, and shape values and indication behavior.

If you want to use custom values in components, wrap them in your own composable functions, directly setting the values for the relevant system, and exposing others as parameters to the containing composable.

We recommend that you access values you set from your custom theme. Alternatively, if your theme doesn't provide Color, TextStyle, Shape, or other systems, you can hardcode them.

@Composable fun CustomButton( onClick: () -> Unit, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Button( colors = ButtonDefaults.buttonColors( containerColor = CustomTheme.colors.component, contentColor = CustomTheme.colors.content, disabledContainerColor = CustomTheme.colors.content .copy(alpha = 0.12f) .compositeOver(CustomTheme.colors.component), disabledContentColor = CustomTheme.colors.content .copy(alpha = 0.38f) ), shape = ButtonShape, elevation = ButtonDefaults.elevatedButtonElevation( defaultElevation = CustomTheme.elevation.default, pressedElevation = CustomTheme.elevation.pressed /* disabledElevation = 0.dp */ ), onClick = onClick, modifier = modifier, content = { ProvideTextStyle( value = CustomTheme.typography.body ) { content() } } ) } val ButtonShape = RoundedCornerShape(percent = 50)CustomDesignSystem.kt

If you've introduced new class types — such as List<Color> to represent gradients — then it may be better to implement components from scratch instead of wrapping them. For an example, take a look at JetsnackButton from the Jetsnack sample.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Material Design 2 in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/material

**Contents:**
- Material Design 2 in Compose Stay organized with collections Save and categorize content based on your preferences.
- Color
  - Use theme colors
  - Surface and content color
  - Content alpha
  - Dark theme
    - Elevation overlays
    - Limited color accents
- Typography
  - Use text styles

Jetpack Compose offers an implementation of Material Design, a comprehensive design system for creating digital interfaces. The Material Design components (buttons, cards, switches, and so on) are built on top of Material Theming, which is a systematic way to customize Material Design to better reflect your product’s brand. A Material Theme contains color, typography, and shape attributes. When you customize these attributes, your changes are automatically reflected in the components you use to build your app.

Jetpack Compose implements these concepts with the MaterialTheme composable:

MaterialTheme( colors = // ... typography = // ... shapes = // ... ) { // app content }Material2Snippets.kt

Configure the parameters you pass to MaterialTheme to theme your application.

Colors are modeled in Compose with the Color class, a data-holding class.

val Red = Color(0xffff0000) val Blue = Color(red = 0f, green = 0f, blue = 1f)Material2Snippets.kt

While you can organize these however you like (as top-level constants, within a singleton, or defined inline), we strongly recommend specifying colors in your theme and retrieving the colors from there. This approach makes it possible to support dark theme and nested themes.

Compose provides the Colors class to model the Material color system. Colors provides builder functions to create sets of light or dark colors:

private val Yellow200 = Color(0xffffeb46) private val Blue200 = Color(0xff91a4fc) // ... private val DarkColors = darkColors( primary = Yellow200, secondary = Blue200, // ... ) private val LightColors = lightColors( primary = Yellow500, primaryVariant = Yellow400, secondary = Blue700, // ... )Material2Snippets.kt

After you have defined your Colors, you can pass them to a MaterialTheme:

MaterialTheme( colors = if (darkTheme) DarkColors else LightColors ) { // app content }Material2Snippets.kt

You can retrieve the Colors provided to the MaterialTheme composable by using MaterialTheme.colors.

Text( text = "Hello theming", color = MaterialTheme.colors.primary )Material2Snippets.kt

Many components accept a pair of color and content color:

Surface( color = MaterialTheme.colors.surface, contentColor = contentColorFor(color), // ... ) { /* ... */ } TopAppBar( backgroundColor = MaterialTheme.colors.primarySurface, contentColor = contentColorFor(backgroundColor), // ... ) { /* ... */ }Material2Snippets.kt

This lets you not only set the color of a composable, but also provide a default color for the content, the composables contained within it. Many composables use this content color by default. For example, Text bases its color on its parent's content color, and Icon uses that color to set its tint.

The contentColorFor() method retrieves the appropriate "on" color for any theme colors. For example, if you set a primary background color on Surface, it uses this function to set onPrimary as the content color. If you set a non-theme background color, you should also specify an appropriate content color. Use LocalContentColor to retrieve the preferred content color for the current background, at a given position in the hierarchy.

Often you want to vary how much you emphasize content to communicate importance and provide visual hierarchy. The Material Design text legibility recommendations advise employing different levels of opacity to convey different importance levels.

Jetpack Compose implements this using LocalContentAlpha. You can specify a content alpha for a hierarchy by providing a value for this CompositionLocal. Nested composables can use this value to apply the alpha treatment to their content. For example, Text and Icon by default use the combination of LocalContentColor adjusted to use LocalContentAlpha. Material specifies some standard alpha values (high, medium, disabled) which are modeled by the ContentAlpha object.

// By default, both Icon & Text use the combination of LocalContentColor & // LocalContentAlpha. De-emphasize content by setting content alpha CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.medium) { Text( // ... ) } CompositionLocalProvider(LocalContentAlpha provides ContentAlpha.disabled) { Icon( // ... ) Text( // ... ) }Material2Snippets.kt

To learn more about CompositionLocal, see Locally scoped data with CompositionLocal.

In Compose, you implement light and dark themes by providing different sets of Colors to the MaterialTheme composable:

@Composable fun MyTheme( darkTheme: Boolean = isSystemInDarkTheme(), content: @Composable () -> Unit ) { MaterialTheme( colors = if (darkTheme) DarkColors else LightColors, /*...*/ content = content ) }Material2Snippets.kt

In this example, MaterialTheme is wrapped in its own composable function, which accepts a parameter that specifies whether to use a dark theme or not. In this case, the function gets the default value for darkTheme by querying the device theme setting.

You can use code like this to check if the current Colors are light or dark:

val isLightTheme = MaterialTheme.colors.isLight Icon( painterResource( id = if (isLightTheme) { R.drawable.ic_sun_24 } else { R.drawable.ic_moon_24 } ), contentDescription = "Theme" )Material2Snippets.kt

In Material, surfaces in dark themes with higher elevations receive elevation overlays, which lighten their background. The higher a surface's elevation (raising it closer to an implied light source), the lighter that surface becomes.

The Surface composable applies these overlays automatically when using dark colors, and so does any other Material composable which uses a surface:

Surface( elevation = 2.dp, color = MaterialTheme.colors.surface, // color will be adjusted for elevation /*...*/ ) { /*...*/ }Material2Snippets.kt

For custom scenarios that don’t involve a Surface, use LocalElevationOverlay, a CompositionLocal containing the ElevationOverlay used by Surface components:

// Elevation overlays // Implemented in Surface (and any components that use it) val color = MaterialTheme.colors.surface val elevation = 4.dp val overlaidColor = LocalElevationOverlay.current?.apply( color, elevation )Material2Snippets.kt

To disable elevation overlays, provide null at the chosen point in a composable hierarchy:

MyTheme { CompositionLocalProvider(LocalElevationOverlay provides null) { // Content without elevation overlays } }Material2Snippets.kt

Material recommends applying limited color accents for dark themes by preferring the use of the surface color over the primary color in most cases. Material composables like TopAppBar and BottomNavigation implement this behavior by default.

For custom scenarios, use the primarySurface extension property:

Surface( // Switches between primary in light theme and surface in dark theme color = MaterialTheme.colors.primarySurface, /*...*/ ) { /*...*/ }Material2Snippets.kt

Material defines a type system, encouraging you to use a small number of semantically-named styles.

Compose implements the type system with the Typography, TextStyle, and font-related classes. The Typography constructor offers defaults for each style so you can omit any you don't want to customize:

val raleway = FontFamily( Font(R.font.raleway_regular), Font(R.font.raleway_medium, FontWeight.W500), Font(R.font.raleway_semibold, FontWeight.SemiBold) ) val myTypography = Typography( h1 = TextStyle( fontFamily = raleway, fontWeight = FontWeight.W300, fontSize = 96.sp ), body1 = TextStyle( fontFamily = raleway, fontWeight = FontWeight.W600, fontSize = 16.sp ) /*...*/ ) MaterialTheme(typography = myTypography, /*...*/) { /*...*/ }Material2Snippets.kt

If you want to use the same typeface throughout, specify the defaultFontFamily parameter and omit the fontFamily of any TextStyle elements:

val typography = Typography(defaultFontFamily = raleway) MaterialTheme(typography = typography, /*...*/) { /*...*/ }Material2Snippets.kt

TextStyle elements are accessed using MaterialTheme.typography. Retrieve the TextStyle elements as follows:

Text( text = "Subtitle2 styled", style = MaterialTheme.typography.subtitle2 )Material2Snippets.kt

Material defines a shape system, letting you define shapes for large, medium, and small components.

Compose implements the shape system with the Shapes class, which lets you specify a CornerBasedShape for each size category:

val shapes = Shapes( small = RoundedCornerShape(percent = 50), medium = RoundedCornerShape(0f), large = CutCornerShape( topStart = 16.dp, topEnd = 0.dp, bottomEnd = 0.dp, bottomStart = 16.dp ) ) MaterialTheme(shapes = shapes, /*...*/) { /*...*/ }Material2Snippets.kt

Many components use these shapes by default. For example, Button, TextField, and FloatingActionButton default to small, AlertDialog defaults to medium, and ModalDrawer defaults to large — see the shape scheme reference for the complete mapping.

Shape elements are accessed using MaterialTheme.shapes. Retrieve the Shape elements with code like this:

Surface( shape = MaterialTheme.shapes.medium, /*...*/ ) { /*...*/ }Material2Snippets.kt

There is no equivalent concept in Compose of default styles from Android Views. You can provide similar functionality by creating your own overload composable functions that wrap Material components. For example, to create a style of button, wrap a button in your own composable function, directly setting the parameters you want or need to alter and exposing others as parameters to the containing composable.

@Composable fun MyButton( onClick: () -> Unit, modifier: Modifier = Modifier, content: @Composable RowScope.() -> Unit ) { Button( colors = ButtonDefaults.buttonColors( backgroundColor = MaterialTheme.colors.secondary ), onClick = onClick, modifier = modifier, content = content ) }Material2Snippets.kt

You can achieve the equivalent of theme overlays from Android Views in Compose by nesting MaterialTheme composables. Because MaterialTheme defaults the colors, typography, and shapes to the current theme value, all other parameters keep their default values when a theme only sets one of those parameters.

Furthermore, when migrating View-based screens to Compose, watch out for usages of the android:theme attribute. It's likely you need a new MaterialTheme in that part of the Compose UI tree.

In this example, the details screen uses a PinkTheme for most of the screen, and then a BlueTheme for the related section. The following screenshot and code illustrate this concept:

@Composable fun DetailsScreen(/* ... */) { PinkTheme { // other content RelatedSection() } } @Composable fun RelatedSection(/* ... */) { BlueTheme { // content } }Material2Snippets.kt

Material components that can be interacted with (clicked, toggled, etc.) can be in different visual states. States include enabled, disabled, pressed, etc.

Composables often have an enabled parameter. Setting it to false prevents interaction, and changes properties like color and elevation to visually convey the component state.

In most cases you can rely on defaults for values like color and elevation. If you need to configure values used in different states, there are classes and convenience functions available. Consider the following button example:

Button( onClick = { /* ... */ }, enabled = true, // Custom colors for different states colors = ButtonDefaults.buttonColors( backgroundColor = MaterialTheme.colors.secondary, disabledBackgroundColor = MaterialTheme.colors.onBackground .copy(alpha = 0.2f) .compositeOver(MaterialTheme.colors.background) // Also contentColor and disabledContentColor ), // Custom elevation for different states elevation = ButtonDefaults.elevation( defaultElevation = 8.dp, disabledElevation = 2.dp, // Also pressedElevation ) ) { /* ... */ }Material2Snippets.kt

Material components use ripples to indicate they're being interacted with. If you're using MaterialTheme in your hierarchy, a Ripple is used as the default Indication inside modifiers such as clickable and indication.

In most cases you can rely on the default Ripple. If you need to configure their appearance, you can use RippleTheme to change properties like color and alpha.

You can extend RippleTheme and make use of the defaultRippleColor and defaultRippleAlpha utility functions. You can then provide your custom ripple theme in your hierarchy using LocalRippleTheme:

@Composable fun MyApp() { MaterialTheme { CompositionLocalProvider( LocalRippleTheme provides SecondaryRippleTheme ) { // App content } } } @Immutable private object SecondaryRippleTheme : RippleTheme { @Composable override fun defaultColor() = RippleTheme.defaultRippleColor( contentColor = MaterialTheme.colors.secondary, lightTheme = MaterialTheme.colors.isLight ) @Composable override fun rippleAlpha() = RippleTheme.defaultRippleAlpha( contentColor = MaterialTheme.colors.secondary, lightTheme = MaterialTheme.colors.isLight ) }Material2Snippets.kt

To learn more about Material Theming in Compose, consult the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2025-12-29 UTC.

---

## Migrate from Material 2 to Material 3 in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/designsystems/material2-material3

**Contents:**
- Migrate from Material 2 to Material 3 in Compose Stay organized with collections Save and categorize content based on your preferences.
- Approaches
  - When to migrate
  - Phased approach
- Dependencies
  - M2
  - M3
- Experimental APIs
- Theming
  - M2

Material Design 3 is the next evolution of Material Design. It includes updated theming, components, and Material You personalization features like dynamic color. It's an update to Material Design 2 and is cohesive with the new visual style and system UI on Android 12 and higher.

This guide focuses on migrating from the Compose Material (androidx.compose.material) Jetpack library to the Compose Material 3 (androidx.compose.material3) Jetpack library.

In general, you should not use both M2 and M3 in a single app long-term. This is because the two design systems and respective libraries differ significantly in terms of their UX/UI designs and Compose implementations.

Your app may use a design system, such as one created using Figma. In such cases, we also highly recommend that you or your design team migrate it from M2 to M3 before starting the Compose migration. It doesn't make sense to migrate an app to M3 if its UX/UI design is based on M2.

Furthermore, your approach to migration should take your app's size, complexity, and UX/UI design into account. Doing so helps you to minimize the impact on your codebase. Take a phased approach to migration.

You should ideally start the migration as soon as possible. However, it's important to consider whether your app is in a realistic position to fully migrate from M2 to M3. There are some blocker scenarios to consider investigating before you start:

Even if you're affected by the preceding scenarios, you should take a phased approach to migration before committing and releasing an app update. In these cases, you would use M2 and M3 side-by-side, and gradually phase out M2 while migrating to M3.

The general steps to a phased migration are as follows:

M3 has a separate package and version from M2:

See the latest M3 versions on the Compose Material 3 releases page.

Other Material dependencies outside of the main M2 and M3 libraries have not changed. They use a mix of the M2 and M3 packages and versions, but this has no impact on migration. They can be used as-is with M3:

Some M3 APIs are considered experimental. In such cases, you need to opt in at the function or file level using the ExperimentalMaterial3Api annotation:

In both M2 and M3, the theme composable is named MaterialTheme but the import packages and parameters differ:

The color system in M3 is significantly different to M2. The number of color parameters has increased, they have different names, and they map differently to M3 components. In Compose, this applies to the M2 Colors class, the M3 ColorScheme class, and related functions:

Given the significant differences between the M2 and M3 color systems, there's no reasonable mapping for Color parameters. Instead, use the Material Theme Builder tool to generate an M3 color scheme. Use the M2 colors as core source colors in the tool, which the tool expands into tonal palettes used by the M3 color scheme. The following mappings are recommended as a starting point:

You can copy the color hex code values for light and dark themes from the tool and use them to implement an M3 ColorScheme instance. Alternatively, Material Theme Builder can export Compose code.

Unlike the M2 Colors class, the M3 ColorScheme class doesn't include an isLight parameter. In general, you should try and model whatever needs this information at the theme level. For example:

See the Custom design systems in Compose guide for more information.

A new feature in M3 is dynamic color. Instead of using custom colors, an M3 ColorScheme can make use of device wallpaper colors on Android 12 and higher, using the following functions:

The typography system in M3 is different to M2. The number of typography parameters is roughly the same, but they have different names and they map differently to M3 components. In Compose, this applies to the M2 Typography class and the M3 Typography class:

The following TextStyle parameter mappings are recommended as a starting point:

The shape system in M3 is different to M2. The number of shape parameters has increased, they're named differently and they map differently to M3 components. In Compose, this applies to the M2 Shapes class and the M3 Shapes class:

The following Shape parameter mappings are recommended as a starting point:

Most components and layouts from M2 are available in M3. There are, however, some missing as well as new ones that didn't exist in M2. Furthermore, some M3 components have more variations than their equivalents in M2. In general, the M3 API surfaces are designed to be as similar as possible to their closest equivalents in M2.

Given the updated color, typography and shape systems, M3 components tend to map differently to the new theming values. It's a good idea to check out the tokens directory in the Compose Material 3 source code as a source of truth for these mappings.

While some components require special considerations, the following function mappings are recommended as a starting point:

See the latest M3 components and layouts on the Compose Material 3 API reference overview, and keep an eye out on the releases page for new and updated APIs.

Scaffold in M3 is different to M2. In both M2 and M3, the main layout composable is named Scaffold but the import packages and parameters differ:

The M2 Scaffold contains a backgroundColor parameter is now named to containerColor in the M3 Scaffold:

The M2 ScaffoldState class no longer exists in M3 as it contains a drawerState parameter which is no longer needed. To show snackbars with the M3 Scaffold, use SnackbarHostState instead:

All of the drawer* parameters from the M2 Scaffold have been removed from the M3 Scaffold. These include parameters such as drawerShape and drawerContent. To show a drawer with the M3 Scaffold, use a navigation drawer composable, such as ModalNavigationDrawer, instead:

Top app bars in M3 are different to those in M2. In both M2 and M3, the main top app bar composable is named TopAppBar but the import packages and parameters differ:

Consider using the M3 CenterAlignedTopAppBar if you were previously centering content within the M2 TopAppBar. It's good to be aware of the MediumTopAppBar and LargeTopAppBar as well.

M3 top app bars contain a new scrollBehavior parameter to provide different functionality on scroll through the TopAppBarScrollBehavior class, such as changing elevation. This works in conjunction with scrolling content using Modifier.nestedScroll. This was possible in the M2 TopAppBar by manually changing the elevation parameter:

Bottom navigation in M2 has been renamed to navigation bar in M3. In M2 there are the BottomNavigation and BottomNavigationItem composables, while in M3 there are the NavigationBar and NavigationBarItem composables:

Buttons, icon buttons and floating action buttons (FABs) in M3 are different to those in M2. M3 includes all of the M2 button composables:

M3 also includes new button variations. Check them out on the Compose Material 3 API reference overview.

Switch in M3 is different to M2. In both M2 and M3, the switch composable is named Switch but the import packages differ:

The surface and elevation systems in M3 are different to M2. There are two types of elevation in M3:

In Compose, this applies to the M2 Surface function and the M3 Surface function:

You can use the elevation Dp values in M2 for both shadowElevation and/or tonalElevation in M3, depending on the UX/UI design preference. Surface is the backing composable behind most components, so component composables might also expose elevation parameters you must migrate in the same way.

Tonal elevation in M3 replaces the concept of elevation overlays in M2 dark themes. As a result, ElevationOverlay and LocalElevationOverlay don't exist in M3, and LocalAbsoluteElevation in M2 has changed to LocalAbsoluteTonalElevation in M3.

Emphasis in M3 is significantly different to M2. In M2, emphasis involved using on colors with certain alpha values to differentiate content like text and icons. In M3, there are now a couple different approaches:

As a result, ContentAlpha and LocalContentAlpha don't exist in M3 and need to be replaced.

The following mappings are recommended as a starting point:

Here's an example of icon emphasis in M2 versus M3:

Here are examples of text emphasis in M2 and M3:

Backgrounds in M2 are named containers in M3. In general, you can replace background* parameters in M2 with container* in M3, using the same values. For example:

To learn more about migrating from M2 to M3 in Compose, consult the following additional resources.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

**Examples:**

Example 1 (bash):
```bash
implementation "androidx.compose.material:material:$m2-version"
```

Example 2 (bash):
```bash
implementation "androidx.compose.material3:material3:$m3-version"
```

Example 3 (python):
```python
import androidx.compose.material3.ExperimentalMaterial3Api

@OptIn(ExperimentalMaterial3Api::class)
@Composable
fun AppComposable() {
    // M3 composables
}
```

Example 4 (python):
```python
import androidx.compose.material.MaterialTheme

MaterialTheme(
    colors = AppColors,
    typography = AppTypography,
    shapes = AppShapes
) {
    // M2 content
}
```

---
