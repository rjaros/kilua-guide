# Creating a New Application

## Creating a new application

The recommended way to create a new application is to use [Kilua Project Wizard](https://plugins.jetbrains.com/plugin/27530-kilua-project-wizard) plugin for IntelliJ IDEA. Just install the plugin from JetBrains Marketplace in your IDE and run `File -> New -> Project...` from the main menu. Choose `Kilua` group, select desired project type, choose options and optional modules from the list on the first page. On the second page choose your project name and location. Your project will be generated and opened in IDE when you click `Finish` button. After a few moments required to download all required dependencies you are ready to go.

You can also just copy the [template project](https://github.com/rjaros/kilua/tree/main/templates/template) and modify it by adding modules.

### gradle/libs.versions.toml

The template uses Gradle version catalog. All plugins and dependencies are listed in the `gradle/libs.versions.toml` file.

```toml
[versions]
kilua = "0.0.25"
kotlin = "2.2.0-RC"
compose = "1.8.1"

[libraries]
kilua = { module = "dev.kilua:kilua", version.ref = "kilua" }

[plugins]
compose = { id = "org.jetbrains.compose", version.ref = "compose" }
compose-compiler = { id = "org.jetbrains.kotlin.plugin.compose", version.ref = "kotlin" }
kotlin-multiplatform = { id = "org.jetbrains.kotlin.multiplatform", version.ref = "kotlin" }
kotlinx-serialization = { id = "org.jetbrains.kotlin.plugin.serialization", version.ref = "kotlin" }
kilua = { id = "dev.kilua", version.ref = "kilua" }
```

### build.gradle.kts

The `build.gradle.kts` file is responsible for the definition of the build process. It declares all required dependencies (in particular Kilua optional modules). Kilua Gradle plugin is used to simplify the configuration.&#x20;

{% code title="build.gradle.kts" %}
```kotlin
import org.jetbrains.kotlin.gradle.ExperimentalWasmDsl

plugins {
    alias(libs.plugins.kotlin.multiplatform)
    alias(libs.plugins.kotlinx.serialization)
    alias(libs.plugins.compose)
    alias(libs.plugins.compose.compiler)
    alias(libs.plugins.kilua)
}

@OptIn(ExperimentalWasmDsl::class)
kotlin {
    js(IR) {
        useEsModules()
        browser {
            commonWebpackConfig {
                outputFileName = "main.bundle.js"
                sourceMaps = false
            }
            testTask {
                useKarma {
                    useChromeHeadless()
                }
            }
        }
        binaries.executable()
        compilerOptions {
            target.set("es2015")
        }
    }
    wasmJs {
        useEsModules()
        browser {
            commonWebpackConfig {
                outputFileName = "main.bundle.js"
                sourceMaps = false
            }
            testTask {
                useKarma {
                    useChromeHeadless()
                }
            }
        }
        binaries.executable()
        compilerOptions {
            target.set("es2015")
        }
    }
    sourceSets {
        val commonMain by getting {
            dependencies {
                implementation(libs.kilua)
            }
        }
        val jsMain by getting {
            dependencies {
            }
        }
        val wasmJsMain by getting {
            dependencies {
            }
        }
    }
}
```
{% endcode %}

### Source code

As Kilua apps can be compiled to both Kotlin/Js and Kotlin/WasmJs targets, the source code for the application is contained in the `src/commonMain` directory. It consists of Kotlin sources in the `kotlin` directory and the `resources` directory with main `index.html` file and optional assets (e.g. images, CSS files, translation files).

Test sources are contained in the `src/commonTest` directory.

{% hint style="info" %}
When creating fullstack or SSR apps with additional JVM target, the `common` source set can't be used for the fronted code. It is recommended to create a custom, shared `webMain` source set.
{% endhint %}

### The Application class

The main Kilua application class must extend the `dev.kilua.Application` class and override the`start` method. Use `startApplication`function to initialize and start your app.

{% code title="App.kt" %}
```kotlin
class App : Application() {

    override fun start() {
        root("root") {
            div {
                +"Hello World!"
            }
        }
    }
}

fun main() {
    startApplication(::App)
}
```
{% endcode %}

##
