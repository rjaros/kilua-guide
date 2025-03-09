# Routing

Kilua has a routing module, which is a fork of the [routing-compose](https://github.com/hfhbd/routing-compose) library for Compose Web, HTML and Desktop. The `kilua-routing` module contains two types of routers - `HashRouter` and `BrowserRouter`.&#x20;

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

Both `HashRouter` and `BrowserRouter` are singleton objects. You can initialize and configure the chosen router by using `SimpleHashRouter` or `SimpleBrowserRouter` composable functions.&#x20;

A router is configured using a simple DSL with a tree-like structure. You can use `route` function to directly match a single part of the URL or you can use `string` and `int`  functions for more dynamic routing. There is also a `noMatch` function to capture all routes not declared earlier.

```kotlin
SimpleBrowserRouter("/") {
    route("/") {
        p {
            +"Home"
        }
    }
    route("/article") {
        int { articleId ->
            p {
                +"Article: $articleId"
            }
        }
        noMatch {
            p {
                +"Article ID not specified"
            }
        }
    }
    route("/about") {
        p {
            +"About"
        }
    }
    noMatch {
        p {
            +"Not found"
        }
    }
}
```

## Layout based routing vs. state based routing

When using routing in your application you most often treat the URL as a kind of data source. Sometimes, with simpler apps, this data can be used directly to render the UI (like in the example above). But more complex applications also use different sources of data and probably keep their internal state in some type of store (e.g. Compose `MutableState` or coroutines `StateFlow`). In such cases it might be desirable to map the URL changes to the state changes (and then render the UI based on the state and not the URL itself).&#x20;

Kilua lets you work with this kind of routing configuration by using `routeAction()`, `stringAction()`, `intAction()`and `noMatchAction()` functions on the lowest levels of the routing tree to call some actions. The actions can be suspending, can access external resources and they should change the state of the application instead of directly rendering UI. The UI itself is rendered using standard Compose state binding.

{% hint style="info" %}
&#x20;These function are wrappers over more low-level `RouteEffect` function.
{% endhint %}

```kotlin
SimpleBrowserRouter("/") {

    var state by remember { mutableStateOf("Home") }

    p {
        +state
    }

    routeAction("/") {
        state = "Home"
    }
    route("/article") {
        intAction { articleId ->
            state = "Article: $articleId"
        }
        noMatchAction {
            state = "Article ID not specified"
        }
    }
    routeAction("/about") {
        state = "About"
    }
    noMatchAction {
        state = "Not found"
    }
}
```

{% hint style="info" %}
You can use `SimpleHashRouter` the same way.
{% endhint %}
