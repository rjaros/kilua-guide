# Using Bootstrap

The `kilua-bootstrap` module provides convenient wrappers around many of the features provided by the popular [Bootstrap](https://getbootstrap.com/) library.

## Text and colors

Bootstrap defines a lot of CSS classes for colors, backgrounds and borders. You can use these classes directly but there are also a few enum classes defined in the Bootstrap module: `BsColor`, `BsBgColor`,  `BsBorder` and `BsRounded`.

```kotlin
div("${BsBorder.Border} ${BsBorder.BorderDanger} ${BsRounded.RoundedPill} ${BsColor.TextBgInfo}") {
    width(300.px)
    padding(10.px)
    +"Hello Bootstrap!"
}
```
