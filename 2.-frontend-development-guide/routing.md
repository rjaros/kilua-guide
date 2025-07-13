# Routing

Kilua has a routing module, which is a fork of the [routing-compose](https://github.com/hfhbd/routing-compose) library for Compose Web, HTML and Desktop. It is possible to directly use the API of the original library, but Kilua provides a dedicated DSL to declare routing compatible with both frontend and server side rendering.&#x20;

The `kilua-routing` module contains two types of routers - `HashRouter` and `BrowserRouter`.&#x20;

## HashRouter&#x20;

`HashRouter` is used for hashed URLs (e.g. `https://example.com/#/path`). This kind of routing is 100% client side (the part of the URL after the `#` is not sent to the server). It doesn't require any additional setup on the backend side, neither in development mode nor in production.

|          Pros         |                    Cons                    |
| :-------------------: | :----------------------------------------: |
| Easy to setup and use |           Less SEO-friendly URLs           |
|                       | No support for SSR (Server Side Rendering) |

## BrowserRouter

`BrowserRouter` uses [History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) and traditional URLs (e.g. `https://example.com/path`). This kind of routing requires support from the server, which must return some resource (most often the content of the `index.html` file) for every path.

|                   Pros                  |        Cons        |
| :-------------------------------------: | :----------------: |
|        SEO-friendly, natural URLs       | More complex setup |
| Support for SSR (Server Side Rendering) |                    |

All Kilua templates and example applications are already configured for use of `BrowserRouter` with Webpack development. But you need to make sure your publication server is configured for History API routing. You can find some valuable information on [this page](https://router.vuejs.org/guide/essentials/history-mode#Example-Server-Configurations).

## Routing configuration

Both `HashRouter` and `BrowserRouter` are singleton objects. You can initialize and configure the chosen router by using `hashRouter` or `browserRouter` composable functions.&#x20;

A router is configured using a simple DSL with a tree-like structure. You can use `route` function to directly match a single part of the URL or you can use `string` and `int`  functions for more dynamic routing.

```kotlin
browserRouter {
    route("/") {
        view {
            p {
                +"Welcome"
            }
        }
    }
    route("/article") {
        int {
            view { articleId ->
                p {
                    +"Article: $articleId"
                }
            }
        }
        view {
            p {
                +"Article ID not specified"
            }
        }
    }
    route("/about") {
        view {
            p {
                +"About"
            }
        }
    }
    view {
        p {
            +"Not found"
        }
    }
}
```

## Layout based routing vs. state based routing

When using routing in your application you most often treat the URL as a kind of data source. Sometimes, with simpler apps, this data can be used directly to render the UI (like in the example above). But more complex applications also use different sources of data and probably keep their internal state in some type of store (e.g. Compose `MutableState` or coroutines `StateFlow`). In such cases it might be desirable to map the URL changes to the state changes (and then render the UI based on the state and not the URL itself).&#x20;

Kilua lets you work with this kind of routing configuration by using `action {}` blocks instead of `view {}` . The actions can be suspending, can access external resources and they should change the state of the application instead of directly rendering UI. The UI itself is rendered using standard Compose state binding.

```kotlin
var state by remember { mutableStateOf("Home") }

browserRouter {
    defaultContent {
        p {
            +state
        }
    }
    route("/") {
        action {
            state = "Welcome"
        }
    }
    route("/article") {
        int {
            action { articleId ->
                state = "Article: $articleId"
            }
        }
        action {
            state = "Article ID not specified"
        }
    }
    route("/about") {
        action {
            state = "About"
        }
    }
    action {
        state = "Not found"
    }
}
```

{% hint style="info" %}
You can use `hashRouter` the same way.
{% endhint %}

## Accessing path parameters

Kilua routing DSL is a mix of static and dynamic calls. The `route {}` , `string {}` and `int {}` blocks are executed statically, when the routing is defined. On the other hand `view {}` and `action {}` blocks are executed dynamically, when the current navigation path matches the given routing definition. That's why you can access path parameters only inside `view` and `action` blocks. The values are passed directly, when using `string` or `int` blocks, or can be accessed with a help of a routing context object providing `parameters` and `remainingPath` properties.

```kotlin
browserRouter { ctx ->
    route("/article") {
        int { ctxA ->
            action { articleId ->
                state = "Article: $articleId"
                console.log("Current parameters: ${ctxA.parameters}")
            }
        }
        action {
            state = "Article ID not specified"
        }
    }
    route("/about") { ctxB ->
        action {
            state = "About"
            console.log("Current parameters: ${ctxB.parameters}")
        }
    }
    action {
        state = "Not found"
        console.log("Remaining path: ${ctx.remainingPath}")
    }
}
```

## Providing metadata

For every declared route you can provide additional metadata. Even though it was designed primarily for server side rendering of the `<meta>` tags, you can also use this feature to enhance your routing tree data model. You can use `defaultMeta` block at the top level and `meta {}` or `meta { view {} }` blocks for every single route.

```kotlin
var state by remember { mutableStateOf("Home") }

browserRouter {
    defaultContent {
        p {
            +state
        }
    }
    defaultMeta {
        titleTemplate = "% - My page"
        description = "The description of my home page"
        keywords = listOf("home", "page")
    }
    route("/") {
        action {
            state = "Welcome"
        }
        meta {
            title = "Home"
        }
    }
    route("/article") {
        int { ctx ->
            action { articleId ->
                state = "Article: $articleId"
            }
            meta {
                view {
                    title = "${ctx.value}"
                }
            }
        }
        action {
            state = "Article ID not specified"
        }
        meta {
            title = "404"
            description = "Article not found"
        }
    }
    route("/about") {
        action {
            state = "About"
        }
        meta {
            title = "About"
        }
    }
    action {
        state = "Not found"
    }
    meta {
        title = "404"
    }
}
```

## Accessing router instance and navigating

When you declare a router in your application you will often need to navigate between different URLs in response to the occurrence of certain events (e.g. button clicks). You can do this by calling `navigate()` method of the router instance. You can use `HashRouter` or `BrowserRouter` objects directly, but if you want more universal code you can also use `Router.current` composable property or `Router.global` extension property.

```kotlin
button("Go to About page") {
    val router = Router.current // only works inside a @Composable function
    onClick {
        router.navigate("/about")
        // Router.global.navigate("/about")
        // HashRouter.navigate("/about")
        // BrowserRouter.navigate("/about")
    }
}
```

## Accessing routing tree model

