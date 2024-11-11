# Development Workflow

To run the application with Gradle continuous build, enter one of these commands:

```
./gradlew -t jsRun                                    (run Js target on Linux)
./gradlew -t wasmJsRun                                (run WasmJs target on Linux)
gradlew.bat -t jsRun                                  (run Js target on Windows)
gradlew.bat -t wasmJsRun                              (run WasmJs target on Windows)
```

{% hint style="info" %}
You can choose whether you want to develop Js or WasmJs version of your application without any code changes. In general it's recommended to develop with Js target, because the Kotlin/JS compiler is a lot faster and also HMR is fully supported.
{% endhint %}

After Gradle finishes downloading dependencies and building the application, open [http://localhost:3000/](http://localhost:3000/) in your favorite browser.

You can import the project in **IntelliJ IDEA** and open `src/commonMain/kotlin/main.kt` file. You can of course use your favourite text editor.

Make some code changes inside the `start` function:

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

{% hint style="info" %}
It's recommended to use Gradle from a command line (in a terminal window).
{% endhint %}

