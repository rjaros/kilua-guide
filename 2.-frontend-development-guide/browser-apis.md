# Browser APIs

Kilua applications are in general designed to run inside the web browser. But unlike with most other Kotlin web frameworks you write Kilua code inside a `common` sources set. What's more, you want your code to be compatible with both Js and Wasm/Js targets and still access various browser APIs and interfaces. Unfortunately the Kotlin compilers and standard libraries for Js and Wasm/Js are a bit different and not compatible with each other when it comes to JavaScript interoperability. As a result, you can't access any of standard browser APIs from the `common` code.&#x20;

Kilua solves this problem by providing it's own implementation of most browser APIs. Instead of using `kotlinx.browser.*` or `org.w3c.*` stdlib packages, you can use the `web.*` package, which includes objects like `web.window`, `web.document`, `web.localStorage` and other classes, objects and interfaces. Check the[ API documentation of kilua-dom](https://rjaros.github.io/kilua/api/modules/kilua-dom/index.html) module for full reference.

When creating a SSR application (an application designed to be rendered on the server side as well) you need to realize the code of your application will be also executed in a NodeJs environment. NodeJs is not a web browser and you can't access browser APIs. You can use the `isDom` global variable (or `renderConfig.isDom` component property) to check if your code is running in the browser or in NodeJs. In typical scenarios with SSR, you should check the DOM availability whenever you interact directly with browser API or external JavaScript components.

```kotlin
code(className = "code-blocks-selector") {
    attribute("data-highlight-only", "nocursor")
    attribute("theme", "darcula")
    attribute("auto-indent", "true")
    attribute("mode", "kotlin")
    attribute("lines", "false")
    +"""
                class App : Application() {
                    override fun start() {
                    }
                }
                
                fun main() {
                    startApplication(::App)
                }
                """.trimIndent()
    LaunchedEffect(Unit) {
        if (renderConfig.isDom) { // This will be executed only when running in the browser
            KotlinPlayground(".code-blocks-selector")
        }
    }
}
```
