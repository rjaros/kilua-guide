# Layout containers

Layout containers allow you to position your components in a different ways to create both simple and complex views. Different container types can be stacked together to achieve even the most sophisticated layouts. Built-in containers are available in the `dev.kilua.panel` package.

## flexPanel

The `flexPanel` composable function allows you to display children components with all the power of[ CSS Flexible Box Layout Module](https://www.w3.org/TR/css-flexbox/) W3C recommendation. In the flex layout model, the children components can be laid out horizontally or vertically, left to right, right to left, top to bottom or bottom to top. Children can change their sizes, either growing or shrinking. The specification is quite complex and most of the available CSS properties are supported with Kotlin enum values.

```kotlin
flexPanel(FlexDirection.Row, FlexWrap.Wrap, JustifyContent.FlexStart, AlignItems.Center, columnGap = 10.px) {
    div {
        order(3)
        +"Component 1"
    }
    div {
        order(1)
        +"Component 2"
    }
    div {
        order(2)
        +"Component 3"
    }
}
```

## hPanel, vPanel

Both`hPanel` and `vPanel` functions are convenient shortcuts for frequently used horizontal (left to right) and vertical (top to bottom) flexbox layouts.

```kotlin
hPanel(gap = 5.px) {
    div {
        +"Component 1"
    }
    div {
        +"Component 2"
    }
    div {
        +"Component 3"
    }
}
vPanel(gap = 5.px) {
    div {
        +"Component 1"
    }
    div {
        +"Component 2"
    }
    div {
        +"Component 3"
    }
}
```

## gridPanel

The `gridPanel` composable function allows you to display children components with the use of [CSS Grid Layout Module](https://www.w3.org/TR/css-grid/) W3C recommendation - a two-dimensional layout system, which allows the children to be positioned into arbitrary slots in a predefined flexible or fixed-size grid. This specification is even more complex than Flexbox, but is still supported mostly with Kotlin enum values and type-safe parameters.

```kotlin
gridPanel(columnGap = 5.px, rowGap = 5.px, justifyItems = JustifyItems.Center) {
    div {
        gridRow("1")
        gridColumn("1")
        +"Component 1, 1"
    }
    div {
        gridArea("1 / 2")
        +"Component 1, 2"
    }
    div {
        gridArea("2 / 1")
        +"Component 2, 1"
    }
    div {
        gridArea("auto")
        +"Component 2, 2"
    }
}
```
