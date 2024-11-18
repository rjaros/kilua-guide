# Rendering HTML

## HTML tags

Kilua provides many functions to render all standard HTML tags. Almost all of them have names directly corresponding to tag names. The exceptions are these HTML tags:

* `<input>`, which can be rendered using many different functions, dedicated for different types of inputs (e.g. text, checkbox, radiobutton, upload, color etc.)
* &#x20;`<a>`, rendered with a `link()` function (in my opinion `a()` is not very convenient name for one of the most important functions)
* `<link>`, rendered with a `linkTag()` function (to avoid clash with the above)
* `<map>`,  rendered with a `mapTag()` function (to avoid clash with Kotlin maps)
* `<object>`,  rendered with an  `objectTag()` function (to avoid clash with Kotlin `object` keyword)
* `<var>`, renderd with `varTag()` function (to avoid clash with Kotlin `var` keyword)

{% hint style="info" %}
There is a dedicated `kilua-svg` module to render `<svg>` tags containing vector graphics.
{% endhint %}

You can also render custom HTML tags, by using `tag()` function.

```kotlin
tag("my-custom-tag") {
    id("my-id")
}
```

## HTML attributes



## Text content

There are a few different composable functions to render textual content of HTML tags. The most basic is a `textNode()`, which also has a convenient extension operator `+`. It can be used like this:

```kotlin
div {
    p {
        +"Paragraph content"
    }
}
```

For some often used cases Kilua defines dedicated functions (adding `t` to the name), which take the text content as the first parameter - these are: `bt()`, `divt()`, `emt()`, `h1t()`, `h2t()`, `h3t()`, `h4t()`, `h5t()`, `h6t()`, `it()`, `lit()`, `pt()`, `pret()`, `st()`, `smallt()`, `spant()`, `strongt()`, `subt()`, `supt()`, `ut()`.

```kotlin
div {
    pt("Paragraph content")
}
```

The text content rendered with `textNode()` function (or `+` operator) is always rendered as plain text (any HTML code is escaped). But you can also render raw HTML code with the help of `rawHtml()` and `rawHtmlBlock()` functions. The first one renders rich content inside an additional `<span style="display: contents;"></span>` tag and the second one inside a `<div style="display: contents;"></div>` tag. You can choose whether you need an inline or a block element based on your HTML structure.

```kotlin
div {
    rawHtml("Some rich text <b>with bold</b> and <i>italic</i>.")
}
```

{% hint style="info" %}
When using raw HTML make sure your code is safe from any XSS vulnerabilities. If you need to display some external data, always sanitize it before rendering with some reliable library. You can use `kilua-sanitize-html` module, which is based on proven [sanitize-html](https://github.com/apostrophecms/sanitize-html) project.
{% endhint %}

