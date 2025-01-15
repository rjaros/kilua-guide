# Resources

Most web application use some kind of external resources - images, CSS stylesheets, translations, JSON data files or any other types. In general you can put all these files into standard Kotlin `resources` directory (it can be `src/commonMain/resources` but also `src/jsMain/resources`, `src/wasmJsMain/resources` or `src/webMain/resources`, depending on the structure of your project). These files will be copied to the root of your distribution directory and can be accessed by the web browser with a simple URL.

{% hint style="info" %}
The main `index.html` file is also just a simple resource file.
{% endhint %}

## Processing resources with Webpack

The method described above is good enough, when your application doesn't need to process these files. But there are cases, when you want some additional processing by Webpack. You can use `@JsModule` annotation to inject resources processed by the bundler.&#x20;

### Using CSS stylesheet:

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

This will include CSS styles in the JS bundle without the need to include it manually with `<style>` tag.&#x20;

{% hint style="info" %}
Using `@JsModule` annotation is not enough for Webpack bundler to actually import and include resources in your application, because such object is treated as unused and removed to optimize the bundle size. You can use the `useModule` function to inform Webpack you need this resource.
{% endhint %}

### Using images

Images processed by Webpack will be copied to the destination folder with hashed names. Use `LocalResource` helper class to access the URL of the image.&#x20;

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
