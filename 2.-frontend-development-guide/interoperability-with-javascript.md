# Interoperability with JavaScript

Kilua gives you a unified way to work with JavaScript modules from the `common` code using external declarations. It gives you access to `web.JsAny` external interface, which is introduced to match `kotlin.js.JsAny` interface from Kotlin/WasmJs standard library. When using Kilua you will notice a lot of use of `web.JsAny` interface, because it's the only way to build Kotlin/WasmJs compatible code. The same applies to types such as `JsArray`, `JsString`, `JsNumber` of even `JsBoolean`, which are just aliases for standard types in Js target but are totally different in WasmJs.

When declaring your own external wrapper types, always extend `web.JsAny` to make sure your externals will be compatible with WasmJs target.

## Dynamic typing

Sometimes, especially when migrating code written for Kotlin/Js, you might want to use some `dynamic` types. Unfortunately `dynamic` is not supported in Kotlin/Wasm and also not available in Kilua. Instead you should use `JsAny`, and use extension operators defined in Kilua for map-like string indexing. For instance instead of writing this Kotlin/Js code:

```kotlin
val someObject: dynamic = someFunction()
console.log(someObject.property1.property2)
someObject.property3 = "Some value"
```

&#x20; you can write this in Kilua:

```kotlin
val someObject: JsAny = someFunction()
console.log(someObject["property1"]!!["property2"])
someObject["property3"] = "Some value".toJsString()
```

## Native JavaScript objects

Kilua also offers some helper functions to automatically generate a native JS objects from external declarations or string-based mappings of different types.

```kotlin
val emptyObject: JsAny = obj()

external interface Test: JsAny {
    var field1: String
    var field2: Int
}

val someObject: Test = obj<Test> {
    field1 = "test"
    field2 = 1
}

val map = mapOf("field1" to "test", "field2" to 1)

val jsObject1: JsAny = map.toJsAny()

val jsObject2: JsAny = jsObjectOf(
    "a" to mapOf(
        "b" to mapOf(
            "c" to "c",
            "d" to "d"
        ),
        "array" to listOf(1, 2)
    ),
    "isCorrect" to true,
    "pi" to 3.14
)
```

The `toJsAny()` extension and `jsObjectOf()` function support all basic Kotlin types - `String`, `Int`, `Double`, `Boolean`, `Array<*>`, `List<*>` and `Map<*,*>` and also support complex types.

## Importing modules and resources

Kilua applications are developed with `useEsModules()` configuration option to support ECMAScript modules. If you have worked with some other Kotlin/JS frameworks before, you might be familiar with the `require()` function, but Kilua configuration doesn't allow you to use it for importing modules. Instead you should use `@JsModule` annotation, which is also available as `dev.kilua.JsModule` for use in the common sources set. This annotation is used both for external NPM modules and local resources (like CSS files, images or translation files).

Using NPM module:

```kotlin
import dev.kilua.JsModule

external interface JQueryStatic: JsAny {
    fun ajax(settings: JsAny)
}

external class JQuery : JsAny {
    fun html(html: String)
}

@JsModule("jquery")
external val jQuery: JQueryStatic

@JsModule("jquery")
external fun jQuery(selector: String): JQuery
```

Using local CSS stylesheet:

```kotlin
import dev.kilua.JsModule
import dev.kilua.useModule

@JsModule("./modules/css/style.css")
external object styleCss : JsAny

class App : Application() {

    init {
        useModule(styleCss)
    }

    override fun start() {
        // ...
    }
}
```

Using `@JsModule` annotation is not enough for Webpack bundler to actually import and include resources in your application, because such object is treated as unused and removed to optimize the bundle size. You can use the `useModule` function to inform Webpack you need this resource.

Using local image file with a helper `LocalResource` class:

```kotlin
import dev.kilua.JsModule
import dev.kilua.LocalResource

@JsModule("./modules/img/cat.jpg")
external object catJpg : LocalResource

class App : Application() {

    override fun start() {

        root("root") {
            div {
                img(catJpg.url, alt = "A cat")
            }
        }
    }
}
```

{% hint style="info" %}
It's recommended to use `resources/modules` directory for keeping your local resources imported with `@JsModule` annotation, because this particular directory is not copied to the final distribution destination folder (the files in this directory are usually processed by Webpack and included in the bundle or saved with a mangled names in root directory).
{% endhint %}
