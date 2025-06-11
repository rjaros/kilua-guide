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

## Bootstrap buttons

You can use dedicated `bsButton` composable function to use Bootstrap buttons with predefined styles and sizes.

```kotlin
bsButton("Click me", style = ButtonStyle.BtnSuccess, size = ButtonSize.BtnLg) {
    onClick {
        console.log("Hello Bootstrap!")
    }
}
```

## Color mode

To support color mode switching you need to initialize `ThemeManger` object using its `init()` method.

```kotlin
class App : Application() {
    override fun start() {
        ThemeManager.init()
    }
}
```

By default the last selected color mode will be used and it will be automatically saved in the browser local storage. You can customize this behaviour with optional parameters.

```kotlin
ThemeManager.init(initialTheme = Theme.Auto, remember = false)
```

You can use `ThemeManager.theme` property to get and set the current color mode at any time.

```kotlin
ThemeManager.theme = Theme.Dark
```

You can also use built-in theme switcher component.

```kotlin
themeSwitcher(style = ButtonStyle.BtnSuccess, round = true)
```

The theme switcher uses Font Awesome icons by default, so you should also add `kilua-fontawesome` module dependency to your project. If your applications uses a different icons set (e.g. Bootstrap Icons), you can use optional switcher parameters to customize icons.

```kotlin
themeSwitcher(autoIcon = "bi bi-circle-half", darkIcon = "bi bi-moon", lightIcon = "bi bi-sun")
```

## Layout containers
