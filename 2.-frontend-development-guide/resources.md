# Resources

Most web application use some kind of external resources - images, CSS stylesheets, translations, JSON data files or any other types. In general you can put all these files into standard Kotlin `resources` directory (it can be `src/commonMain/resources` but also `src/jsMain/resources`, `src/wasmJsMain/resources` or `src/webMain/resources`, depending on the structure of your project). These files will be copied to the root of your distribution directory and can be accessed by the web browser with a simple URL.

{% hint style="info" %}
The main `index.html` file is also just a simple resource file.
{% endhint %}

## Processing resources with Webpack

You may also need some additional processing of the resources files. You can use `@JsModule` annotation to inject resources processed by the Webpack bundler.&#x20;

### Using CSS stylesheet:

You can include CSS styles in the JS bundle without the need to manually use the `<style>` tag.&#x20;

```kotlin
import dev.kilua.useModule
import js.import.JsModule

@JsModule("/kotlin/modules/css/style.css")
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

{% hint style="info" %}
Using `@JsModule` annotation is not enough for Webpack bundler to actually import and include resources in your application, because such object is treated as unused and removed to optimize the bundle size. You can use the `useModule` function to inform Webpack you need this resource.
{% endhint %}

### Using images

Images processed by Webpack will be copied to the destination folder with hashed names. Use `LocalResource` helper class to access the proper URL of the image.&#x20;

```kotlin
import dev.kilua.LocalResource
import js.import.JsModule

@JsModule("/kotlin/modules/img/cat.jpg")
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

### Reading JSON files

You can use JSON data files directly in your application. Use `LocalResource` helper class to access the content of the file.&#x20;

```kotlin
import dev.kilua.LocalResource
import dev.kilua.utils.jsGet
import js.import.JsModule
import web.console.console

/* The content of the test.json file:
{
  "property1": "Lorem Ipsum"
}
*/
@JsModule("/kotlin/modules/json/test.json")
external object testJson : LocalResource

class App : Application() {

    override fun start() {
        console.log(testJson.content.jsGet("property1"))

        root("root") {
        }
    }
}
```

{% hint style="info" %}
It's recommended to use `resources/modules` directory for keeping your local resources imported with `@JsModule` annotation, because this particular directory is not copied to the final distribution destination folder.
{% endhint %}
