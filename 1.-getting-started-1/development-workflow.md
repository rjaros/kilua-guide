# Development Workflow

To run the application with Gradle continuous build, enter one of these commands:

```
./gradlew -t jsBrowserDevelopmentRun                 (run Js target on Linux)
./gradlew -t wasmJsBrowserDevelopmentRun             (run WasmJs target on Linux)
gradlew.bat -t jsBrowserDevelopmentRun               (run Js target on Windows)
gradlew.bat -t wasmJsBrowserDevelopmentRun           (run WasmJs target on Windows)
```

{% hint style="info" %}
You can choose whether you want to develop Js or WasmJs version of your application without any code changes. In general it's recommended to develop with Js target, because the Kotlin/JS compiler is a lot faster and also HMR is fully supported.
{% endhint %}

After Gradle finishes downloading dependencies and building the application, open [http://localhost:3000/](http://localhost:3000/) in your favourite browser.

You can import the project in **IntelliJ IDEA** and open `src/commonMain/kotlin/main.kt` file. You can of course use your favourite text editor.

Make some code changes in the `start` function:

{% code title="main.kt" %}
```kotlin
override fun start() {
    root("root") {
        div {
            +"Hello Kilua!"
        }
    }
}
```
{% endcode %}

You should see your changes immediately in the browser.

