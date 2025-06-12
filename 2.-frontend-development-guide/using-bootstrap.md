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

### TabPanel

The `tabPanel()` composable function allows you to create a popular tabbed layout, with tabs on the top, left or right side of the content.&#x20;

```kotlin
tabPanel {
    tab("Apple", "fab fa-apple") {
        div {
            +"Apple description"
        }
    }
    tab("Google", "fab fa-google") {
        div {
            +"Google description"
        }
    }
    tab("Microsoft", "fab fa-windows") {
        div {
            +"Microsoft description"
        }
    }
}
```

For tabs displayed on the left or right side of the content, you need to declare the ratio between the size of the tabs and the content. The `sideTabSize` parameter takes one of six enumerated values (from `Size1` to `Size6`) which mean 1/11, 2/10, 3/9, 4/8, 5/7, and 6/6 ratios. The default ratio is 3/9.

```kotlin
tabPanel(tabPosition = TabPosition.Left, sideTabSize = SideTabSize.Size2) {
    tab("Apple", "fab fa-apple") {
        div {
            +"Apple description"
        }
    }
    tab("Google", "fab fa-google") {
        div {
            +"Google description"
        }
    }
    tab("Microsoft", "fab fa-windows") {
        div {
            +"Microsoft description"
        }
    }
}
```

Tabs can be made closable. A closable tab displays a cross icon on the tab bar. When a user clicks this icon, the tab is not automatically removed from the panel, but a special `closeTab` event is triggered. The event need to be handled and the tab should be hidden with some custom condition, preferably backed by a compose state variable.

```kotlin
tabPanel {
    var msTabVisible by remember { mutableStateOf(true) }

    tab("Apple", "fab fa-apple") {
        div {
            +"Apple description"
        }
    }
    tab("Google", "fab fa-google") {
        div {
            +"Google description"
        }
    }
    if (msTabVisible) {
        tab("Microsoft", "fab fa-microsoft", closable = true) {
            div {
                +"Microsoft description"
            }
        }
    }
    onEvent<CustomEvent<*>>("closeTab") {
        msTabVisible = false
    }
}
```

You can also allow to reorder tabs with drag & drop using `draggableTabs = true` parameter. Similar to closing, the result of the D\&D operation is a `moveTab` event, which should be processed by your code and result in a change of the state used to define tabs order.

```kotlin
external class MoveInfo : JsAny {
    val from: Int
    val to: Int
}

val tabs = mutableStateListOf<Triple<String, String, @Composable IComponent.() -> Unit>>(
    Triple("Apple", "fab fa-apple", {
        div {
            +"Apple description"
        }
    }), Triple("Google", "fab fa-google", {
        div {
            +"Google description"
        }
    }), Triple("Microsoft", "fab fa-microsoft", {
        div {
            +"Microsoft description"
        }
    })
)

tabPanel(draggableTabs = true) {
    tabs.forEach { (title, icon, content) ->
        tab(title, icon) {
            content()
        }
    }
    onEvent<CustomEvent<*>>("moveTab") {
        val info = parse<MoveInfo>(it.detail.toString())
        if (info.from < info.to) {
            tabs.add(info.to + 1, tabs[info.from])
            tabs.removeAt(info.from)
        } else {
            tabs.add(info.to, tabs[info.from])
            tabs.removeAt(info.from + 1)
        }
    }
}
```

### Offcanvas

Bootstrap offcanvas is a sidebar component that can be toggled to appear from the left, right, top, or bottom edge of the viewport.

```kts
val offcanvas = offcanvasRef("Some caption") {
    p {
        +"This is an offcanvas example."
    }
}
button("Open offcanvas") {
    onClick {
        offcanvas.show()
    }
}
```

You can use additional parameters of the composable function to customize offcanvas placement, responsiveness, closing, scrolling and backdrop usage.

```kotlin
val offcanvas = offcanvasRef(
    "Some caption",
    placement = OffPlacement.OffcanvasEnd,
    responsiveType = OffResponsiveType.OffcanvasLg,
    closeButton = false,
    bodyScrolling = true,
    backdrop = false,
    escape = false,
) {
    p {
        +"This is an offcanvas example."
    }
}
button("Toggle offcanvas", className = "d-lg-none") {
    onClick {
        offcanvas.toggle()
    }
}
```

### Accordion

Bootstrap accordion component allows you to create vertically collapsing panels with various content. &#x20;

```kotlin
accordion {
    item("Google", "fab fa-google") {
        +"Google is a technology company that specializes in Internet-related services and products."
    }
    item("Apple", "fab fa-apple") {
        +"Apple Inc. is a technology company that designs, manufactures, and markets consumer electronics, computer software, and online services."
    }
    item("Microsoft", "fab fa-microsoft") {
        +"Microsoft Corporation is a technology company that produces computer software, consumer electronics, personal computers, and related services."
    }
}
```

You can use additional parameters of the composable function to customize accordion behaviour.

```kotlin
accordion(flush = true, alwaysOpen = true, openedIndex = 1) {
    item("Google", "fab fa-google") {
        +"Google is a technology company that specializes in Internet-related services and products."
    }
    item("Apple", "fab fa-apple") {
        +"Apple Inc. is a technology company that designs, manufactures, and markets consumer electronics, computer software, and online services."
    }
    item("Microsoft", "fab fa-microsoft") {
        +"Microsoft Corporation is a technology company that produces computer software, consumer electronics, personal computers, and related services."
    }
}
```

### Carousel

Bootstrap carousel component allows you to cycle through its children elements.

```kotlin
carousel {
    item("First slide", "First slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Red)
        }
    }
    item("Second slide", "Second slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Green)
        }
    }
    item("Third slide", "Third slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Blue)
        }
    }
}
```

You can use additional parameters of the composable function to customize carousel behaviour. You can also use component reference to control carousel playback.

```kotlin
val carousel = carouselRef(fade = true, autoPlay = true, interval = 1000) {
    item("First slide", "First slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Red)
        }
    }
    item("Second slide", "Second slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Green)
        }
    }
    item("Third slide", "Third slide label") {
        div("d-block w-100") {
            height(200.px)
            background(Color.Blue)
        }
    }
}
button("Play") {
    onClick {
        carousel.cycle()
    }
}
button("Pause") {
    onClick {
        carousel.pause()
    }
}
```
