# Jetpack-Compose - Text And Input

**Pages:** 10

---

## Configure text layout Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/configure-layout

**Contents:**
- Configure text layout Stay organized with collections Save and categorize content based on your preferences.
- Limit visible lines
- Indicate text overflow
- Recommended for you

This page describes how to configure your text layout with parameters like maxLines and overflow.

To limit the number of visible lines in a Text composable, set the maxLines parameter:

@Composable fun LongText() { Text("hello ".repeat(50), maxLines = 2) }TextSnippets.kt

When limiting a long text, you may want to indicate a TextOverflow, which is only shown if the displayed text is truncated. To do so, set the textOverflow parameter:

@Composable fun OverflowedText() { Text("Hello Compose ".repeat(50), maxLines = 2, overflow = TextOverflow.Ellipsis) }TextSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Style text Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/style-text

**Contents:**
- Style text Stay organized with collections Save and categorize content based on your preferences.
- Common text stylings
  - Change text color
  - Change text size
  - Make text italic
  - Make text bold
  - Add shadow
- Add multiple styles in text
  - Display HTML with links in text
    - Example: HTML with styled link

The Text composable has multiple optional parameters to style its content. Below, we’ve listed parameters that cover the most common use cases with text. For all the parameters of Text, see the Compose Text source code.

Whenever you set one of these parameters, you’re applying the style to the whole text value. If you need to apply multiple styles within the same line or paragraphs, see the section on multiple inline styles.

The following sections describe common ways to style your text.

@Composable fun BlueText() { Text("Hello World", color = Color.Blue) }TextSnippets.kt

@Composable fun BigText() { Text("Hello World", fontSize = 30.sp) }TextSnippets.kt

Use the fontStyle parameter to italicize text (or set another FontStyle).

@Composable fun ItalicText() { Text("Hello World", fontStyle = FontStyle.Italic) }TextSnippets.kt

Use the fontWeight parameter to bold text (or set another FontWeight).

@Composable fun BoldText() { Text("Hello World", fontWeight = FontWeight.Bold) }TextSnippets.kt

The style parameter lets you set an object of type TextStyle and configure multiple parameters, for example shadow. Shadow receives a color for the shadow, the offset, or where it is located in respect of the Text and the blur radius which is how blurry it looks.

@Composable fun TextShadow() { val offset = Offset(5.0f, 10.0f) Text( text = "Hello world!", style = TextStyle( fontSize = 24.sp, shadow = Shadow( color = Color.Blue, offset = offset, blurRadius = 3f ) ) ) }TextSnippets.kt

To set different styles within the same Text composable, use an AnnotatedString, a string that can be annotated with styles of arbitrary annotations.

AnnotatedString is a data class containing:

TextStyle is for use in the Text composable, whereas SpanStyle and ParagraphStyle is for use in AnnotatedString. For more information about multiple styles in a paragraph, see Add multiple styles in a paragraph.

AnnotatedString has a type-safe builder to make it easier to create: buildAnnotatedString.

@Composable fun MultipleStylesInText() { Text( buildAnnotatedString { withStyle(style = SpanStyle(color = Color.Blue)) { append("H") } append("ello ") withStyle(style = SpanStyle(fontWeight = FontWeight.Bold, color = Color.Red)) { append("W") } append("orld") } ) }TextSnippets.kt

Use AnnotatedString.fromHtml() to display HTML-formatted text with clickable links in your Jetpack Compose application. This function converts a string with HTML tags into an AnnotatedString, allowing for styling and link handling.

This snippet renders HTML-formatted text with a link, applying specific styling to the link:

@Composable fun AnnotatedHtmlStringWithLink( modifier: Modifier = Modifier, htmlText: String = """ <h1>Jetpack Compose</h1> <p> Build <b>better apps</b> faster with <a href="https://www.android.com">Jetpack Compose</a> </p> """.trimIndent() ) { Text( AnnotatedString.fromHtml( htmlText, linkStyles = TextLinkStyles( style = SpanStyle( textDecoration = TextDecoration.Underline, fontStyle = FontStyle.Italic, color = Color.Blue ) ) ), modifier ) }HtmlStyling.kt

AnnotatedString.fromHtml() converts the htmlText string into an AnnotatedString. The linkStyles parameter customizes link appearance.

TextLinkStyles defines the style for links within the HTML. SpanStyle sets text decoration, font style, and color for the links.

The Text composable displays the resulting AnnotatedString.

This snippet enables "Jetpack Compose" as a clickable link, styled with blue color, underlined, and italicized:

To enable more advanced text styling, you can use the Brush API with TextStyle and SpanStyle. In any place where you would typically use TextStyle or SpanStyle, you can now also use Brush.

Configure your text using a built-in brush within TextStyle. For example, you can configure a linearGradient brush to your text as follows:

val gradientColors = listOf(Cyan, LightBlue, Purple /*...*/) Text( text = text, style = TextStyle( brush = Brush.linearGradient( colors = gradientColors ) ) )TextSnippets.kt

You are not limited to this particular color scheme or style of coloring. While we have provided a simple example to highlight, use any of the built-in brushes or even just a SolidColor to enhance your text.

Since you can use Brush alongside both TextStyle and SpanStyle, integration with TextField and buildAnnotatedString is seamless.

For more information about using the brush API within a TextField, see Style input with Brush API.

If you only want to apply a brush to parts of your text, use buildAnnotatedString and the SpanStyle API, along with your brush and gradient of choice.

Text( text = buildAnnotatedString { append("Do not allow people to dim your shine\n") withStyle( SpanStyle( brush = Brush.linearGradient( colors = rainbowColors ) ) ) { append("because they are blinded.") } append("\nTell them to put some sunglasses on.") } )TextSnippets.kt

To adjust the opacity of a particular span of text, use SpanStyle's optional alpha parameter. Use the same brush for both parts of a text, and change the alpha parameter in the corresponding span. In the code sample, the first span of text displays at half opacity (alpha =.5f) while the second displays at full opacity (alpha = 1f).

val brush = Brush.linearGradient(colors = rainbowColors) buildAnnotatedString { withStyle( SpanStyle( brush = brush, alpha = .5f ) ) { append("Text in ") } withStyle( SpanStyle( brush = brush, alpha = 1f ) ) { append("Compose ❤️") } }TextSnippets.kt

You can apply the basicMarquee modifier to any composable to produce an animated scrolling effect. The marquee effect occurs if the content is too wide to fit in the available constraints. By default, basicMarquee has certain configurations (such as velocity and initial delay) set, but you can modify these parameters to customize the effect.

The following snippet implements a basic marquee effect on a Text composable:

@Composable fun BasicMarqueeSample() { // Marquee only animates when the content doesn't fit in the max width. Column(Modifier.width(400.dp)) { Text( "Learn about why it's great to use Jetpack Compose", modifier = Modifier.basicMarquee(), fontSize = 50.sp ) } }TextSnippets.kt

Figure 6. The basicMarquee modifier applied to text.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Configure text fields Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/user-input

**Contents:**
- Configure text fields Stay organized with collections Save and categorize content based on your preferences.
- Choose TextField implementation
- Style TextField
- Style input with Brush API
  - Implement colored gradients using TextStyle
- Manage text field state
  - Modify text with TextFieldBuffer
  - Edit text in TextFieldState
- Modify user input
  - Filter user input with input transformations

TextField allows users to enter and modify text. There are two types of text fields you can use: state-based text fields and value-based text fields. Select the type you want to display content for:

We recommend using state-based text fields, as they provide a more complete and reliable approach to managing the state of a TextField. The following table outlines differences between these types of text fields, and includes the key advantages state-based text fields offer:

Value-based text fields

State-based text fields

Updates text field state with the onValueChange callback. You are responsible for updating the value in your own state based on the changes reported by onValueChange.

Explicitly uses a TextFieldState object to manage the text input state (value, selection, and composition). This state can be remembered and shared.

Visual transformation

Uses VisualTransformation for modifying how the displayed text appears. This typically handles both input and output formatting in a single step.

Uses InputTransformation for modifying the user's input before it's committed to the state, and OutputTransformation for formatting text field content without changing the underlying state data.

Accepts singleLine: Boolean, maxLines: Int, and minLines: Int to control the number of lines.

Uses lineLimits: TextFieldLineLimits to configure the minimum and maximum number of lines the text field can occupy.

SecureTextField is a composable built on top of state-based text fields for writing a password field.

This page describes how you can implement TextField, style TextField input, and configure other TextField options, like keyboard options and visually transforming user input.

There are two levels of TextField implementation:

TextField( state = rememberTextFieldState(initialText = "Hello"), label = { Text("Label") } )StateBasedText.kt

OutlinedTextField( state = rememberTextFieldState(), label = { Text("Label") } )StateBasedText.kt

TextField and BasicTextField share many common parameters for customization. The complete list for TextField is available in the TextField source code. This is a non-exhaustive list of some of the useful parameters:

TextField( state = rememberTextFieldState("Hello\nWorld\nInvisible"), lineLimits = TextFieldLineLimits.MultiLine(maxHeightInLines = 2), placeholder = { Text("") }, textStyle = TextStyle(color = Color.Blue, fontWeight = FontWeight.Bold), label = { Text("Enter text") }, modifier = Modifier.padding(20.dp) )StateBasedText.kt

We recommend TextField over BasicTextField when your design calls for a Material TextField or OutlinedTextField. However, BasicTextField should be used when building designs that don't need the decorations from the Material spec.

You can use the Brush API for more advanced styling in your TextField. The following section describes how you can use a Brush to add a colored gradient to TextField input.

For more information about using the Brush API to style text, see Enable advanced styling with Brush API.

To implement a colored gradient as you type within a TextField, set your brush of choice as a TextStyle for your TextField. In this example, we use a built-in brush with a linearGradient to view the rainbow gradient effect as text is typed into the TextField.

val brush = remember { Brush.linearGradient( colors = listOf(Color.Red, Color.Yellow, Color.Green, Color.Blue, Color.Magenta) ) } TextField( state = rememberTextFieldState(), textStyle = TextStyle(brush = brush) )StateBasedText.kt

TextField uses a dedicated state holder class called TextFieldState for its content and selection. TextFieldState is designed to be hoisted wherever it fits in your architecture. There are 2 main properties that are provided by TextFieldState:

What differentiates TextFieldState from other approaches, like the onValueChange callback, is that TextFieldState fully encapsulates the entire input flow. This includes using the correct backing data structures, inlining filters and formatters, and also synchronizing all edits coming from different sources.

You can use TextFieldState() to hoist state in TextField. For this, we recommend using the rememberTextFieldState() function. rememberTextFieldState() creates the TextFieldState instance in your composable, makes sure the state object is remembered, and provides built-in save and restore functionality:

val usernameState = rememberTextFieldState() TextField( state = usernameState, lineLimits = TextFieldLineLimits.SingleLine, placeholder = { Text("Enter Username") } )StateBasedText.kt

rememberTextFieldState can have a blank parameter or have an initial value passed in to represent the text's value on initialization. If a different value is passed in a subsequent recomposition, the value of the state is not updated. To update the state after it's initialized, call edit methods on TextFieldState.

TextField( state = rememberTextFieldState(initialText = "Username"), lineLimits = TextFieldLineLimits.SingleLine, )StateBasedText.kt

A TextFieldBuffer serves as an editable text container, similar in function to a StringBuilder. It holds both the text content and information about the selection.

You often encounter TextFieldBuffer as a receiver scope on functions like TextFieldState.edit, InputTransformation.transformInput, or OutputTransformation.transformOutput. In these functions, you can read or update the TextFieldBuffer as needed. Afterwards, these changes are either committed to TextFieldState, or passed down to the rendering pipeline in the case of OutputTransformation.

You can use standard editing functions like append, insert, replace, or delete to modify the buffer's contents. To change the selection state, either directly set its selection: TextRange variable, or use utility functions such as placeCursorAtEnd or selectAll. The selection itself is represented by a TextRange, where the start index is inclusive and the end index is exclusive. A TextRange with identical start and end values, like (3, 3), signifies a cursor position with no characters selected.

val phoneNumberState = rememberTextFieldState("1234567890") TextField( state = phoneNumberState, keyboardOptions = KeyboardOptions( keyboardType = KeyboardType.Phone ), inputTransformation = InputTransformation.maxLength(10).then { if (!asCharSequence().isDigitsOnly()) { revertAllChanges() } }, outputTransformation = OutputTransformation { if (length > 0) insert(0, "(") if (length > 4) insert(4, ")") if (length > 8) insert(8, "-") } )StateBasedText.kt

There are several methods that allow you to edit the state directly through your state variable:

edit: Lets you edit the state contents and gives you TextFieldBuffer functions so you can use methods like insert, replace, append, and more.

// Initial textFieldState text passed in is "I love Android" // textFieldState.text : I love Android // textFieldState.selection: TextRange(14, 14) textFieldState.edit { insert(14, "!") } // textFieldState.text : I love Android! // textFieldState.selection: TextRange(15, 15) textFieldState.edit { replace(7, 14, "Compose") } // textFieldState.text : I love Compose! // textFieldState.selection: TextRange(15, 15) textFieldState.edit { append("!!!") } // textFieldState.text : I love Compose!!!! // textFieldState.selection: TextRange(18, 18) textFieldState.edit { selectAll() } // textFieldState.text : I love Compose!!!! // textFieldState.selection: TextRange(0, 18)StateBasedText.kt

setTextAndPlaceCursorAtEnd: Clears the current text, replaces it with the given text, and sets the cursor at the end.

textFieldState.setTextAndPlaceCursorAtEnd("I really love Android") // textFieldState.text : I really love Android // textFieldState.selection : TextRange(21, 21)StateBasedText.kt

clearText: Clears all text.

textFieldState.clearText() // textFieldState.text : // textFieldState.selection : TextRange(0, 0)StateBasedText.kt

For other TextFieldState functions, see the TextFieldState reference.

The following sections describe how to modify user input. Input transformation lets you filter TextField input while the user is typing, while output transformation formats user input before it's displayed on-screen.

An input transformation lets you filter input from the user. For example, if your TextField takes in an American phone number, you only want to accept 10 digits. The results of the InputTransformation are saved in the TextFieldState.

There are built-in filters for common InputTransformation use cases. To limit length, call InputTransformation.maxLength():

TextField( state = rememberTextFieldState(), lineLimits = TextFieldLineLimits.SingleLine, inputTransformation = InputTransformation.maxLength(10) )StateBasedText.kt

InputTransformation is a single function interface. When implementing your custom InputTransformation, you need to override TextFieldBuffer.transformInput:

class CustomInputTransformation : InputTransformation { override fun TextFieldBuffer.transformInput() { } }StateBasedText.kt

For a phone number, add a custom input transformation that only allows digits to be typed into the TextField:

class DigitOnlyInputTransformation : InputTransformation { override fun TextFieldBuffer.transformInput() { if (!asCharSequence().isDigitsOnly()) { revertAllChanges() } } }StateBasedText.kt

To add multiple filters on your text input, chain InputTransformations using the then extension function. Filters are executed sequentially. As a best practice, apply the most selective filters first to avoid unnecessary transformations on data that would ultimately be filtered out.

TextField( state = rememberTextFieldState(), inputTransformation = InputTransformation.maxLength(6) .then(CustomInputTransformation()), )StateBasedText.kt

After adding input transformations, the TextField input accepts 10 digits maximum.

OutputTransformations let you format user input before it is rendered on the screen. Unlike InputTransformation, the formatting done through the OutputTransformation is not saved in the TextFieldState. Building on the previous phone number example, you need to add parentheses and dashes in the appropriate places:

This is the updated way of handling VisualTransformations in value-based TextFields, with a key difference being that you don't have to calculate their offset mappings.

OutputTransformation is a single abstract method interface. In order to implement a custom OutputTransformation, you need to override the transformOutput method:

class CustomOutputTransformation : OutputTransformation { override fun TextFieldBuffer.transformOutput() { } }StateBasedText.kt

To format a phone number, add an opening parentheses at index 0, a closing parentheses at index 4, and a dash at index 8 to your OutputTransformation:

class PhoneNumberOutputTransformation : OutputTransformation { override fun TextFieldBuffer.transformOutput() { if (length > 0) insert(0, "(") if (length > 4) insert(4, ")") if (length > 8) insert(8, "-") } }StateBasedText.kt

Next, add your OutputTransformation to TextField:

TextField( state = rememberTextFieldState(), outputTransformation = PhoneNumberOutputTransformation() )StateBasedText.kt

The following diagram shows the flow from text input to transformation to output:

TextField lets you set keyboard configurations options, such as the keyboard layout, or enable the autocorrect if it's supported by the keyboard. Some options may not be guaranteed if the software keyboard doesn't comply with the options provided here. Here is the list of the supported keyboard options:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Text in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text

**Contents:**
- Text in Compose Stay organized with collections Save and categorize content based on your preferences.
- Samples
- Recommended for you

Text is a central piece of any UI, and Jetpack Compose makes it easier to display or write text. Compose leverages composition of its building blocks, meaning you don’t need to overwrite properties and methods or extend big classes to have a specific composable design and logic working the way you want.

As its base, Compose provides a BasicText and BasicTextField, which are the barebones to display text and handle user input. At a higher level, Compose provides Text and TextField, which are composables following Material Design guidelines. It’s recommended to use them as they have the right look and feel for users on Android, and includes other options to simplify their customization without having to write a lot of code.

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2024-03-25 UTC.

---

## Display text Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/display-text

**Contents:**
- Display text Stay organized with collections Save and categorize content based on your preferences.
- Display text from resource
- Recommended for you

The most basic way to display text is to use the Text composable with a String as an argument:

@Composable fun SimpleText() { Text("Hello World") }TextSnippets.kt

We recommend you use string resources instead of hardcoding Text values, as you can share the same strings with your Android Views as well as preparing your app for internationalization:

@Composable fun StringResourceText() { Text(stringResource(R.string.hello_world)) }TextSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Display emoji Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/emoji

**Contents:**
- Display emoji Stay organized with collections Save and categorize content based on your preferences.
- Interoperatibility
  - Extending from ComponentActivity
  - Extending from AppCompatActivity
- Troubleshooting
- Recommended for you

The standard set of emoji is refreshed annually by Unicode, as emoji usage is increasing rapidly for all types of apps.

If your app displays internet content or provides text input, we strongly recommend supporting the latest emoji fonts. Otherwise, later emoji might be displayed as a small square box called tofu (☐) or other incorrectly rendered emoji sequences.

Android versions 11 (API level 30) and lower can't update the emoji font, so apps that display them on those versions must be updated manually.

The following are examples of modern emoji.

BOM March 2023 (Compose UI 1.4) brings support for the latest emoji version, including backwards compatibility with older Android versions down to API 21.

This support requires no changes to your app— if you use Text and TextField (Material 2 or Material 3) or BasicText and BasicTextField, you get modern emoji support out of the box.

The best way to test the latest emojis in your app is by using a real device on API 30 or below.

If you're using a custom emoji solution, or need to disable the default emoji resolution in Compose for any other reason, you can use PlatformTextStyle(emojiSupportMatch):

Text( text = "Hello $EMOJI_TEXT", style = TextStyle( platformStyle = PlatformTextStyle( emojiSupportMatch = EmojiSupportMatch.None )/* ... */ ) )EmojiSnippets.kt

If your app uses both Views and Compose in the same Activity, make sure you are using the appropriate APIs to configure emojis correctly. The following sections describe when to use each API.

If your Activity extends from Compose ComponentActivity instead of AppCompatActivity, follow the Support emoji without AppCompat instructions.

Because you are not extending AppCompatActivity, add the Emoji2 library to your dependencies and use EmojiTextView in your views instead of the TextView widget, as shown in the following snippet:

class MainActivity : ComponentActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContentView(R.layout.activity_main) val emojiTextView: EmojiTextView = findViewById(R.id.emoji_text_view) emojiTextView.text = getString(R.string.emoji_text_view, EMOJI_TEXT) val composeView: ComposeView = findViewById(R.id.compose_view) composeView.apply { setContent { // compose code } } } }EmojiSnippets.kt

Then, in your XML file:

If your Activity extends from AppCompatActivity, you can use ComposeView to call composable functions. Emojis render correctly across Android versions when you use Text composables.

If you are extending from AppCompatActivity, inflate TextView from XML to have emojis rendered correctly.

This applies if you are inflating the XML:

class MyActivity : AppCompatActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContentView(R.layout.activity_main) val emojiTextView: TextView = findViewById(R.id.emoji_text_view) emojiTextView.text = getString(R.string.emoji_text_view, EMOJI_TEXT) val composeView: ComposeView = findViewById(R.id.compose_view) composeView.apply { setContent { // compose code } } } }EmojiSnippets.kt

class MyActivity : AppCompatActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContentView( ComposeView(this).apply { setContent { Column { Text(EMOJI_TEXT) AndroidViewBinding(ExampleViewBinding::inflate) { emojiTextView.text = EMOJI_TEXT } } } } ) } }EmojiSnippets.kt

To inflate a text with AndroidView inside ComposeView, use AppCompatTextView to render emojis properly:

class MyActivity : AppCompatActivity() { override fun onCreate(savedInstanceState: Bundle?) { super.onCreate(savedInstanceState) setContentView( ComposeView(this).apply { setContent { Column { Text(EMOJI_TEXT) AndroidView( factory = { context -> AppCompatTextView(context) }, update = { it.text = EMOJI_TEXT } ) } } } ) } }EmojiSnippets.kt

See the Interoperability APIs documentation for details.

If you're seeing tofu (☐) instead of the emoji, first check if the problem is your specific test device. There are few main things you can check:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Style paragraph Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/style-paragraph

**Contents:**
- Style paragraph Stay organized with collections Save and categorize content based on your preferences.
- Set text alignment
- Add multiple styles in a paragraph
- Adjust line height and padding
- Insert line breaks
  - Customize line breaks
  - CJK considerations
- Hyphenate text split across lines
- Recommended for you

This page describes how you can style text for your paragraph. To set paragraph-level styling, you can configure parameters like textAlign and lineHeight or define your own ParagraphStyle.

The textAlign parameter lets you set the horizontal alignment of the text within a Text composable surface area.

By default, Text will select the natural text alignment depending on its content value:

@Composable fun CenterText() { Text( "Hello World", textAlign = TextAlign.Center, modifier = Modifier.width(150.dp) ) }TextSnippets.kt

If you want to manually set the text alignment of a Text composable, prefer using TextAlign.Start and TextAlign.End instead of TextAlign.Left and TextAlign.Right respectively, as they resolve to the right edge of the Text composable depending on the preferred language text orientation. For example, TextAlign.End aligns to the right side for French text and to the left side for Arabic text, but TextAlign.Right aligns to the right side no matter what alphabet is used.

To add multiple styles in a paragraph, you can use ParagraphStyle in an AnnotatedString, which can be annotated with styles of arbitrary annotations. Once a portion of your text is marked with a ParagraphStyle, that portion is separated from the remaining text as if it had line feeds at the beginning and end.

For more information about adding multiple styles in a text, see Add multiple styles in text.

AnnotatedString has a type-safe builder to make it easier to create: buildAnnotatedString. The following snippet uses buildAnnotatedString to set ParagraphStyle:

@Composable fun ParagraphStyle() { Text( buildAnnotatedString { withStyle(style = ParagraphStyle(lineHeight = 30.sp)) { withStyle(style = SpanStyle(color = Color.Blue)) { append("Hello\n") } withStyle( style = SpanStyle( fontWeight = FontWeight.Bold, color = Color.Red ) ) { append("World\n") } append("Compose") } } ) }TextSnippets.kt

includeFontPadding is a legacy property that adds extra padding based on font metrics at the top of the first line and bottom of the last line of a text. Starting from Compose BOM version 2024.01.01, includeFontPadding is set to false by default, which brings the default text layout more in line with common design tools.

The ability to configure lineHeight is not new– it has been available since Android Q. You can configure lineHeight for Text using the lineHeight parameter, which distributes the line height in each line of text. You can then use the new LineHeightStyle API to further configure how this text is aligned within the space, and remove whitespace.

You may want to adjust lineHeight using the text unit "em" (relative font size) instead of "sp" (scaled pixels) for improved precision. For more information about selecting an appropriate text unit, see TextUnit.

Text( text = text, style = LocalTextStyle.current.merge( TextStyle( lineHeight = 2.5.em, platformStyle = PlatformTextStyle( includeFontPadding = false ), lineHeightStyle = LineHeightStyle( alignment = LineHeightStyle.Alignment.Center, trim = LineHeightStyle.Trim.None ) ) ) )TextSnippets.kt

In addition to adjusting lineHeight, you can now further center and style text using configurations with the LineHeightStyle API: LineHeightStyle.Alignment and LineHeightStyle.Trim (includeFontPadding must be set to false for Trim to work). Alignment and Trim use the measured space in between lines of text to more appropriately distribute it to all lines– including a single line of text and the top line of a block of text.

LineHeightStyle.Alignment defines how to align the line in the space provided by the line height. Within each line, you can align the text to the top, bottom, center, or proportionally. LineHeightStyle.Trim then allows you to leave or remove the extra space to the top of the first line and bottom of the last line of your text, generated from any lineHeight and Alignment adjustments. The following samples show how multi-line text looks with various LineHeightStyle.Trim configurations when alignment is centered (LineHeightStyle.Alignment.Center).

See the Fixing Font Padding in Compose Text blog post to learn more about the context of this change, how includeFontPadding worked in the View system, and the changes made for Compose and the new LineHeightStyle APIs.

The LineBreak API defines the criteria by which text is split across multiple lines. You can specify the type of line breaking you want in the TextStyle block of your Text composable. The preset line breaking types include the following:

The following snippet uses both Simple and Paragraph to specify line-breaking behavior on a long block of text:

TextSample( samples = mapOf( "Simple" to { Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(130.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = LineBreak.Simple ) ) }, "Paragraph" to { Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(130.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = LineBreak.Paragraph ) ) } ) )TextSnippets.kt

In the above output, notice that the Paragraph line breaking behavior produces a more visually balanced result than the Simple line breaking.

You can also construct your own LineBreak configuration with the Strategy parameter. The Strategy can be any of the following:

The following snippet shows the difference between a paragraph with default settings and a paragraph optimized for small screens with the Balanced line-breaking strategy:

TextSample( samples = mapOf( "Balanced" to { val smallScreenAdaptedParagraph = LineBreak.Paragraph.copy(strategy = LineBreak.Strategy.Balanced) Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(200.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = smallScreenAdaptedParagraph ) ) }, "Default" to { Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(200.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default ) } ) )TextSnippets.kt

You can also customize LineBreak with the Strictness and WordBreak APIs, which were designed specifically for CJK languages. You may not always see the effects of these APIs in non-CJK languages. Overall, line-breaking rules are defined based on locale.

Strictness describes the strictness of the line breaking with the following properties:

WordBreak defines how line breaks should be inserted within words with the following properties:

The following snippet uses a Strict strictness and a Phrase word breaking setting for a Japanese text:

val customTitleLineBreak = LineBreak( strategy = LineBreak.Strategy.HighQuality, strictness = LineBreak.Strictness.Strict, wordBreak = LineBreak.WordBreak.Phrase ) Text( text = "あなたに寄り添う最先端のテクノロジー。", modifier = Modifier.width(250.dp), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = customTitleLineBreak ) )TextSnippets.kt

The Hyphens API lets you add hyphenation support to your app. Hyphenation refers to inserting a dash-like punctuation mark to indicate that a word is divided across lines of text. When enabled, hyphenation is added between the syllables of a word at appropriate hyphenation points.

By default, hyphenation is not enabled. To enable hyphenation, add Hyphens.Auto as a parameter in a TextStyle block:

TextSample( samples = mapOf( "Hyphens - None" to { Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(130.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = LineBreak.Paragraph, hyphens = Hyphens.None ) ) }, "Hyphens - Auto" to { Text( text = SAMPLE_LONG_TEXT, modifier = Modifier .width(130.dp) .border(BorderStroke(1.dp, Color.Gray)), fontSize = 14.sp, style = TextStyle.Default.copy( lineBreak = LineBreak.Paragraph, hyphens = Hyphens.Auto ) ) } ) )TextSnippets.kt

When enabled, hyphenation only occurs under the following conditions:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Enable user interactions Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/user-interactions

**Contents:**
- Enable user interactions Stay organized with collections Save and categorize content based on your preferences.
- Select text
- Create clickable sections of text with LinkAnnotation
- Recommended for you

Jetpack Compose enables fine-grained interactivity in Text. Text selection is now more flexible and can be done across composable layouts. User interactions in text are different from other composable layouts, as you can’t add a modifier to a portion of a Text composable. This page highlights the APIs that enable user interactions.

By default, composables aren’t selectable, which means that users can't select and copy text from your app. To enable text selection, wrap your text elements with a SelectionContainer composable:

@Composable fun SelectableText() { SelectionContainer { Text("This text is selectable") } }TextSnippets.kt

You may want to disable selection on specific parts of a selectable area. To do so, you need to wrap the unselectable part with a DisableSelection composable:

@Composable fun PartiallySelectableText() { SelectionContainer { Column { Text("This text is selectable") Text("This one too") Text("This one as well") DisableSelection { Text("But not this one") Text("Neither this one") } Text("But again, you can select this one") Text("And this one too") } } }TextSnippets.kt

To listen for clicks on Text, you can add the clickable modifier. However, you may want to attach extra information to a certain part of the Text value, like a URL attached to a certain word to be opened in a browser. In cases like this, you need to use a LinkAnnotation, which is an annotation that represents a clickable part of the text.

With LinkAnnotation, you can attach a URL to a part of a Text composable that automatically opens once clicked, as shown in the following snippet:

@Composable fun AnnotatedStringWithLinkSample() { // Display multiple links in the text Text( buildAnnotatedString { append("Go to the ") withLink( LinkAnnotation.Url( "https://developer.android.com/", TextLinkStyles(style = SpanStyle(color = Color.Blue)) ) ) { append("Android Developers ") } append("website, and check out the") withLink( LinkAnnotation.Url( "https://developer.android.com/jetpack/compose", TextLinkStyles(style = SpanStyle(color = Color.Green)) ) ) { append("Compose guidance") } append(".") } ) }TextSnippets.kt

You can also configure a custom action in response to a user click on a part of the Text composable. In the following snippet, when the user clicks on “Jetpack Compose,” a link is displayed, and metrics are logged if the user clicks the link:

@Composable fun AnnotatedStringWithListenerSample() { // Display a link in the text and log metrics whenever user clicks on it. In that case we handle // the link using openUri method of the LocalUriHandler val uriHandler = LocalUriHandler.current Text( buildAnnotatedString { append("Build better apps faster with ") val link = LinkAnnotation.Url( "https://developer.android.com/jetpack/compose", TextLinkStyles(SpanStyle(color = Color.Blue)) ) { val url = (it as LinkAnnotation.Url).url // log some metrics uriHandler.openUri(url) } withLink(link) { append("Jetpack Compose") } } ) }TextSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Work with fonts Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/fonts

**Contents:**
- Work with fonts Stay organized with collections Save and categorize content based on your preferences.
- Set font
- Downloadable fonts
  - Use downloadable fonts programmatically
  - Groovy
  - Kotlin
  - Add fallback fonts
  - Debug your implementation
  - Caveats
- Use variable fonts

This page describes how to set fonts in your Compose app.

Text has a fontFamily parameter to allow setting the font used in the composable. By default, serif, sans-serif, monospace and cursive font families are included:

@Composable fun DifferentFonts() { Column { Text("Hello World", fontFamily = FontFamily.Serif) Text("Hello World", fontFamily = FontFamily.SansSerif) } }TextSnippets.kt

You can use the fontFamily attribute to work with custom fonts and typefaces defined in the res/font folder:

This example shows how you would define a fontFamily based on those font files and using the Font function:

val firaSansFamily = FontFamily( Font(R.font.firasans_light, FontWeight.Light), Font(R.font.firasans_regular, FontWeight.Normal), Font(R.font.firasans_italic, FontWeight.Normal, FontStyle.Italic), Font(R.font.firasans_medium, FontWeight.Medium), Font(R.font.firasans_bold, FontWeight.Bold) )TextSnippets.kt

You can pass this fontFamily to your Text composable. Because a fontFamily can include different weights, you can manually set fontWeight to select the right weight for your text:

Column { Text(text = "text", fontFamily = firaSansFamily, fontWeight = FontWeight.Light) Text(text = "text", fontFamily = firaSansFamily, fontWeight = FontWeight.Normal) Text( text = "text", fontFamily = firaSansFamily, fontWeight = FontWeight.Normal, fontStyle = FontStyle.Italic ) Text(text = "text", fontFamily = firaSansFamily, fontWeight = FontWeight.Medium) Text(text = "text", fontFamily = firaSansFamily, fontWeight = FontWeight.Bold) }TextSnippets.kt

To learn how to set the typography in your entire app, see Custom design systems in Compose.

Starting in Compose 1.2.0, you can use the downloadable fonts API in your Compose app to download Google fonts asynchronously and use them in your app.

Support for downloadable fonts provided by custom providers is not currently available.

To download a font programmatically from within your app, follow these steps:

Text( fontFamily = fontFamily, text = "Hello World!" )TextDownloadableFontsSnippets.kt

You can also define Typography to use your FontFamily:

val MyTypography = Typography( bodyMedium = TextStyle( fontFamily = fontFamily, fontWeight = FontWeight.Normal, fontSize = 12.sp/*...*/ ), bodyLarge = TextStyle( fontFamily = fontFamily, fontWeight = FontWeight.Bold, letterSpacing = 2.sp, /*...*/ ), headlineMedium = TextStyle( fontFamily = fontFamily, fontWeight = FontWeight.SemiBold/*...*/ ), /*...*/ )TextDownloadableFontsSnippets.kt

Next, set the Typography to your app’s theme:

MyAppTheme( typography = MyTypography )/*...*/TextDownloadableFontsSnippets.kt

For an example of an app that’s implementing downloadable fonts in Compose together with Material3, see the Jetchat sample app.

You can determine a chain of fallbacks for your font in case the font fails to download properly. For instance, if you have your downloadable font defined like this:

// ... import androidx.compose.ui.text.googlefonts.Font // ... val fontName = GoogleFont("Lobster Two") val fontFamily = FontFamily( Font(googleFont = fontName, fontProvider = provider), Font(googleFont = fontName, fontProvider = provider, weight = FontWeight.Bold) )TextDownloadableFontsSnippets.kt

You can define the defaults for your font for both weights like this:

// ... import androidx.compose.ui.text.font.Font import androidx.compose.ui.text.googlefonts.Font // ... val fontName = GoogleFont("Lobster Two") val fontFamily = FontFamily( Font(googleFont = fontName, fontProvider = provider), Font(resId = R.font.my_font_regular), Font(googleFont = fontName, fontProvider = provider, weight = FontWeight.Bold), Font(resId = R.font.my_font_regular_bold, weight = FontWeight.Bold) )TextDownloadableFontsSnippets.kt

Make sure you’re adding the correct imports.

Defining the FontFamily like this creates a FontFamily containing two chains, one per weight. The loading mechanism will try to resolve the online font first, and then the font located in your local R.font resource folder.

To help you verify if the font is being downloaded correctly, you can define a debug coroutine handler. Your handle provides the behavior of what to do in case the font fails to load asynchronously.

Start by creating a CoroutineExceptionHandler:

val handler = CoroutineExceptionHandler { _, throwable -> // process the Throwable Log.e(TAG, "There has been an issue: ", throwable) }TextDownloadableFontsSnippets.kt

Pass it to the createFontFamilyResolver method to have the resolver use the new handler:

CompositionLocalProvider( LocalFontFamilyResolver provides createFontFamilyResolver(LocalContext.current, handler) ) { Column { Text( text = "Hello World!", style = MaterialTheme.typography.bodyMedium ) } }TextDownloadableFontsSnippets.kt

You can also use the isAvailableOnDevice API from the provider to test if the provider is available and certificates are configured correctly. To do this, you can call the isAvailableOnDevice method that returns false if the provider is configured incorrectly.

val context = LocalContext.current LaunchedEffect(Unit) { if (provider.isAvailableOnDevice(context)) { Log.d(TAG, "Success!") } }TextDownloadableFontsSnippets.kt

Google Fonts takes several months to make new fonts available on Android. There's a gap in time between when a font is added in fonts.google.com and when it's available through the downloadable fonts API (either in the View system or in Compose). Newly added fonts might fail to load in your app with an IllegalStateException. To help developers identify this error over other types of font loading errors, we added descriptive messaging for the exception in Compose with the changes here. If you find any issues, report them using the issue tracker.

A variable font is a font format that allows one font file to contain different styles. With variable fonts, you can modify axes (or parameters) to generate your preferred style. These axes can be standard, such as weight, width, slant, and italic, or custom, which differ across variable fonts.

Using variable fonts instead of regular font files allows you to only have one font file instead of multiple.

For more background about variable fonts, see Google Fonts Knowledge, the entire catalog of available variable fonts, and a table of the supported axes for each font.

This doc shows you how to implement a variable font in your Compose app.

Download the variable font you want to use (for example Roboto Flex) and place it in the app/res/font folder in your app. Make sure that the .ttf file you add is the variable font version of the font, and that the name of your font file is all lowercase and doesn't contain any special characters.

To load a variable font, define a FontFamily using the font placed in the res/font/ directory:

// In Typography.kt @OptIn(ExperimentalTextApi::class) val displayLargeFontFamily = FontFamily( Font( R.font.robotoflex_variable, variationSettings = FontVariation.Settings( FontVariation.weight(950), FontVariation.width(30f), FontVariation.slant(-6f), ) ) )VariableFontsSnippets.kt

The FontVariation API allows you to configure standard font axes such as weight, width, and slant. These are standard axes that are available with any variable font. You can create different configurations of the font based on where the font will be used.

Variable fonts are only available for Android versions O and above, so add a guardrail and configure an appropriate fallback:

// In Typography.kt val default = FontFamily( /* * This can be any font that makes sense */ Font( R.font.robotoflex_static_regular ) ) @OptIn(ExperimentalTextApi::class) val displayLargeFontFamily = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { FontFamily( Font( R.font.robotoflex_variable, variationSettings = FontVariation.Settings( FontVariation.weight(950), FontVariation.width(30f), FontVariation.slant(-6f), ) ) ) } else { default }VariableFontsSnippets.kt

Extract the settings into a set of constants for easier reuse and replace the font settings with these constants:

// VariableFontDimension.kt object DisplayLargeVFConfig { const val WEIGHT = 950 const val WIDTH = 30f const val SLANT = -6f const val ASCENDER_HEIGHT = 800f const val COUNTER_WIDTH = 500 } @OptIn(ExperimentalTextApi::class) val displayLargeFontFamily = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { FontFamily( Font( R.font.robotoflex_variable, variationSettings = FontVariation.Settings( FontVariation.weight(DisplayLargeVFConfig.WEIGHT), FontVariation.width(DisplayLargeVFConfig.WIDTH), FontVariation.slant(DisplayLargeVFConfig.SLANT), ) ) ) } else { default }VariableFontsSnippets.kt

Configure the Material Design 3 typography to use the FontFamily:

// Type.kt val Typography = Typography( displayLarge = TextStyle( fontFamily = displayLargeFontFamily, fontSize = 50.sp, lineHeight = 64.sp, letterSpacing = 0.sp, /***/ ) )VariableFontsSnippets.kt

This sample uses displayLarge Material 3 typography, which has different default font settings and recommended uses. For example, you should use displayLarge for short, critical text, as it's the largest text on the screen.

With Material 3, you can change the default values of TextStyle and fontFamily to customize your typography. In the snippet above, you configure instances of TextStyle to customize font settings for each font family.

Now that you've defined your typography, pass it to the M3 MaterialTheme:

MaterialTheme( colorScheme = MaterialTheme.colorScheme, typography = Typography, content = content )VariableFontsSnippets.kt

Finally, use a Text composable and specify the style to one of the defined typography styles, MaterialTheme.typography.displayLarge:

@Composable @Preview fun CardDetails() { MyCustomTheme { Card( shape = RoundedCornerShape(8.dp), elevation = CardDefaults.cardElevation(defaultElevation = 4.dp), modifier = Modifier .fillMaxWidth() .padding(16.dp) ) { Column( modifier = Modifier.padding(16.dp) ) { Text( text = "Compose", style = MaterialTheme.typography.displayLarge, modifier = Modifier.padding(bottom = 8.dp), maxLines = 1 ) Text( text = "Beautiful UIs on Android", style = MaterialTheme.typography.headlineMedium, modifier = Modifier.padding(bottom = 8.dp), maxLines = 2 ) Text( text = "Jetpack Compose is Android’s recommended modern toolkit for building native UI. It simplifies and accelerates UI development on Android. Quickly bring your app to life with less code, powerful tools, and intuitive Kotlin APIs.", style = MaterialTheme.typography.bodyLarge, modifier = Modifier.padding(bottom = 8.dp), maxLines = 3 ) } } } }VariableFontsSnippets.kt

Each Text composable is configured through its Material theme's style and contains a different variable font configuration. You can use MaterialTheme.typography to retrieve the typography provided to the M3 MaterialTheme composable.

Fonts can also have custom axes. These are defined within the font file itself. For example, the Roboto Flex font has the ascender height ("YTAS") axis, which adjusts the height of the lowercase ascenders, while counter width ("XTRA") adjusts the width of each letter.

You can change the value of these axes with the FontVariation settings.

For more information about the custom axes you can configure for a font, see the table of the supported axes for each font.

To use custom axes, define functions for the custom ascenderHeight and counterWidth axes:

fun ascenderHeight(ascenderHeight: Float): FontVariation.Setting { require(ascenderHeight in 649f..854f) { "'Ascender Height' must be in 649f..854f" } return FontVariation.Setting("YTAS", ascenderHeight) } fun counterWidth(counterWidth: Int): FontVariation.Setting { require(counterWidth in 323..603) { "'Counter width' must be in 323..603" } return FontVariation.Setting("XTRA", counterWidth.toFloat()) }VariableFontsSnippets.kt

These functions do the following:

Using the axes with the font configuration, pass additional parameters to each Font that is loaded:

@OptIn(ExperimentalTextApi::class) val displayLargeFontFamily = if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { FontFamily( Font( R.font.robotoflex_variable, variationSettings = FontVariation.Settings( FontVariation.weight(DisplayLargeVFConfig.WEIGHT), FontVariation.width(DisplayLargeVFConfig.WIDTH), FontVariation.slant(DisplayLargeVFConfig.SLANT), ascenderHeight(DisplayLargeVFConfig.ASCENDER_HEIGHT), counterWidth(DisplayLargeVFConfig.COUNTER_WIDTH) ) ) ) } else { default }VariableFontsSnippets.kt

Notice that the height of the lowercase ascenders is now increased, and the other text is wider:

For more information, see the following blog post about variable fonts:

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---

## Autofill in Compose Stay organized with collections Save and categorize content based on your preferences.

**URL:** https://developer.android.com/develop/ui/compose/text/autofill

**Contents:**
- Autofill in Compose Stay organized with collections Save and categorize content based on your preferences.
- Fill credentials
- Save credentials
- Set up Autofill
- Add Autofill to your text field using content type
  - Value-based text field
  - State-based text field
- Add Autofill fields with multiple types
  - Value-based text field
  - State-based text field

Some apps, such as password managers, can fill out the components in other apps with data provided by the user. Apps that fill out other apps' components are called autofill services. The autofill framework manages the communication between an app and an autofill service.

Filling out credentials and forms is a time-consuming and error-prone task. Autofill allows users to save time spent filling in fields and minimizes user input errors.

With only a few lines of code, you can implement Autofill in Compose. This feature provides the following benefits to users:

Autofill allows users to populate their credentials in the following ways:

Users can save credentials through Autofill in the following ways:

You can use Autofill in your app to streamline the retrieval of saved data for users. Autofill supports text components through BasicTextField and all Material text fields that build on that component.

Before using the Autofill APIs on your device or emulator, you must activate Autofill in Settings. There, you can specify a credential provider for Autofill to store your credentials.

To indicate that a TextField is Autofill-enabled, set the ContentType semantics with the types that the field can accept. This indicates to Autofill services what kind of user data might be relevant to this specific field. Use ContentType.Username to set a TextField that users can fill in with their username.

By setting the ContentType semantics, your users can access Autofill information already saved in their device's credential provider. For example, if a user has already signed into your app through the Chrome browser on their laptop and saved their password through a credential provider, then their credentials are served to them through Autofill.

TextField( value = textFieldValue.value, onValueChange = {textFieldValue.value = it}, modifier = Modifier.semantics { contentType = ContentType.Username } )AutofillSnippets.kt

TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.Username } )AutofillSnippets.kt

In some cases, you may want your TextField to take on more than one ContentType. For example, a login field may accept either an email address or a username. You can add multiple content types to your TextField with the + operator.

For all the types of data available to save with Autofill, see the ContentType reference.

TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.Username + ContentType.EmailAddress } )AutofillSnippets.kt

TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.Username + ContentType.EmailAddress } )AutofillSnippets.kt

When you add a ContentType in a TextField, you won't need to do anything else for users to be able to fill credentials.

When a user clicks into an Autofill-enabled field, if there is relevant data stored, they see a chip in the toolbar above the keyboard that prompts them to fill in credentials.

Compose automatically tries to determine when a user navigates from a page and commits the inputted credentials. Once a field is Autofill-enabled, it will automatically save credential information when a user navigates away from the page without requiring any additional code.

To explicitly save new credentials through text fields with Autofill, the Autofill context should be committed (or canceled) by the Autofill manager. The local Autofill manager then communicates with the Autofill framework whenever necessary. If you want to remove credentials that the user has inputted, call AutofillManager.cancel to delete any pending data without saving it.

The following snippets show how to save data with Autofill explicitly using a button:

Create a local variable to hold the Autofill manager, which can be retrieved in the following way: val autofillManager = LocalAutofillManager.currentAutofillSnippets.kt

In your TextField(s), add your chosen content type through Modifier.semantics:

With value-based text fields: val autofillManager = LocalAutofillManager.current Column { TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.NewUsername } ) Spacer(modifier = Modifier.height(16.dp)) TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.NewPassword } ) }AutofillSnippets.kt

With state-based text fields: val autofillManager = LocalAutofillManager.current Column { TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.NewUsername } ) Spacer(modifier = Modifier.height(16.dp)) TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.NewPassword } ) }AutofillSnippets.kt

Commit the Autofill context as needed through a button click:

With value-based text fields: val autofillManager = LocalAutofillManager.current Column { TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.NewUsername }, ) Spacer(modifier = Modifier.height(16.dp)) TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.NewPassword }, ) // Submit button Button(onClick = { autofillManager?.commit() }) { Text("Reset credentials") } }AutofillSnippets.kt

With state-based text fields: val autofillManager = LocalAutofillManager.current Column { TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.NewUsername }, ) Spacer(modifier = Modifier.height(16.dp)) TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.NewPassword }, ) // Submit button Button(onClick = { autofillManager?.commit() }) { Text("Reset credentials") } }AutofillSnippets.kt

Commit is called whenever a user navigates away from the screen. If a Submit button is linked to navigation, then Commit does not need to be called. If you still want clicking a Submit to trigger the save dialog, add Commit here.

When the user clicks the button, they'll see this bottom sheet prompting them to save the credentials to the selected credential provider:

Depending on your credential provider, when you're using the NewUsername and NewPassword content types, users may see a button in the keyboard to Suggest strong password. When they click this, a bottom sheet appears, which allows them to save their credentials. You don't need to implement anything else for users to have this experience.

When invoking the "saving" user journey, if you click "Not now" more than once, your credential provider may no longer display the bottom sheet. To re-enable it and have it appear once more, you need to remove specific apps that have blocked the "Remember this password?".

In a typical Autofill user journey, when an Autofill-enabled component has been populated with credentials, it changes color and becomes highlighted to signal to the user that Autofill has successfully completed.

To customize this highlight color, use CompositionLocal and provide whichever color you'd like. The default Autofill highlight color is defined as Color(0x4dffeb3b).

val customHighlightColor = Color.Red CompositionLocalProvider(LocalAutofillHighlightColor provides customHighlightColor) { TextField( value = textFieldValue.value, onValueChange = { textFieldValue.value = it }, modifier = Modifier.semantics { contentType = ContentType.Username } ) }AutofillSnippets.kt

val customHighlightColor = Color.Red CompositionLocalProvider(LocalAutofillHighlightColor provides customHighlightColor) { TextField( state = rememberTextFieldState(), modifier = Modifier.semantics { contentType = ContentType.Username } ) }AutofillSnippets.kt

Content and code samples on this page are subject to the licenses described in the Content License. Java and OpenJDK are trademarks or registered trademarks of Oracle and/or its affiliates.

Last updated 2026-01-15 UTC.

---
