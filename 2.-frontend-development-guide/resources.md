# Resources

Most web application use some kind of external resources - images, CSS stylesheets, translations, JSON data files or any other types. In general you can put all these files into standard Kotlin `resources` directory (it can be `src/commonMain/resources` but also `src/jsMain/resources`, `src/wasmJsMain/resources` or `src/webMain/resources`, depending on the structure of your project). These files will be copied to the root of your distribution directory and can be accessed by the web browser with a simple URL.

{% hint style="info" %}
The main `index.html` file is also just a simple resource file.
{% endhint %}

