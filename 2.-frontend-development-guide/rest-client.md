# REST client

The `dev.kilua.rest.RestClient` class, located in the `kilua-rest` module, can be used to connect to any HTTP service. You can use remote services with both dynamic and type-safe calls (using `@Serializable` classes). The `RestClient` class has only a single `receive()` method, which uses the builder pattern for configuration and returns a `RestResponse<T>` object. A number of extension functions is defined for `RestClient` class, which allow you to make typical calls easier.

## Dynamic parameters, dynamic result

```kotlin
import dev.kilua.rest.callDynamic

val restClient = RestClient()
val result: JsAny? = restClient.callDynamic("https://api.github.com/search/repositories") {
    data = jsObjectOf("q" to "kilua")
}
```

## Dynamic parameters, type-safe result

```kts
import dev.kilua.rest.call

@Serializable
data class Repository(val id: Int, val full_name: String?, val description: String?, val fork: Boolean)

val restClient = RestClient()
val items: List<Repository> = restClient.call("https://api.github.com/search/repositories") {
    data = jsObjectOf("q" to "kilua")
    resultTransform = { it?.jsGet("items") }
}
```

## Type-safe parameters, dynamic result

```kotlin
import dev.kilua.rest.callDynamic

@Serializable
data class Query(val q: String?)

val restClient = RestClient()
val result: JsAny? = restClient.callDynamic("https://api.github.com/search/repositories", Query("kilua"))
```
