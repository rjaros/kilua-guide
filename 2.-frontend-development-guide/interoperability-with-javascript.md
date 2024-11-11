# Interoperability with JavaScript

Kilua gives you a unified way to work with JavaScript modules from the `common` code using external declarations. It gives you access to `web.JsAny` external interface, which is introduced to match `kotlin.js.JsAny` interface from Kotlin/WasmJs standard library. When using Kilua you will notice a lot of use of `web.JsAny` interface, because it's the only way to build Kotlin/WasmJs compatible code. The same applies to types such as `JsArray`, `JsString`, `JsNumber` of even `JsBoolean`, which are just aliases for standard types in Js target but are totally different in WasmJs.

When declaring your own external wrapper types, always extend `web.JsAny` to make sure your externals will be compatible with WasmJs target.

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

val jsObject: JsAny = jsObjectOf("field1" to "test", "field2" to 1)
```
