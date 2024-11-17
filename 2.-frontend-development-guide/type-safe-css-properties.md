# Type-safe CSS properties

Every Kilua component has a full range of properties based on CSS specification. Most of them are 100% type-safe - based on enumeration values, dedicated classes and functions. You can of course use custom CSS stylesheets and assign predefined classes to your components, but Kilua gives you a choice. With CSS properties you can style any component's size, position, margins, paddings, borders, colors, backgrounds, text and fonts with pure Kotlin code.

```kotlin
div {
    margin(10.px)
    padding(20.px)
    fontFamily("Times New Roman")
    fontSize(32.px)
    fontStyle(FontStyle.Oblique)
    fontWeight(FontWeight.Bolder)
    fontVariant(FontVariant.SmallCaps)
    textDecoration(TextDecoration(TextDecorationLine.Underline, TextDecorationStyle.Dotted, Color.Red))
    +"A label with custom CSS styling"
}
```

## CSS units

Kilua supports all CSS units as an extension properties on `Number` type. So you can specify dimensions, sizes, position and thickness with such example notations: `50.px`, `12.pt`, `2.em`, `90.perc`, `100.vh`, `1.49.em` etc.

## Colors

Kilua offers a few convenient ways to specify colors. You can use methods defined in the `Color` class to create values with  hexadecimal `Int` literals or direct RGB(A) values. You can also use predefined constants for all CSS color names.

```kotlin
div {
    color(Color.hex(0x0000ff))
    color(Color.rgb(0, 0, 255))
    color(Color.rgba(0, 0, 255, 128))
    color(Color.Blue)
    color(Color("blue"))
}
```
