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

All Kilua templates and example applications are already configured for use of `BrowserRouter` with webpack development. But you need to make sure your publication server is configured for History API routing. You can find some valuable information on [this page](https://router.vuejs.org/guide/essentials/history-mode#Example-Server-Configurations).

## Routing configuration



## Layout based routing vs. state based routing

When using routing in your application you most often treat the URL of the browser as a kind of data source. Sometimes, with simpler apps, this data can be used directly to render the UI. But more complex applications also use different sources of data (often asynchronously like API calls) and keep their internal state in some type of store (e.g. Compose `MutableState` or coroutines `StateFlow`). In such cases it might be desirable to map the URL changes to the state changes and then render the UI based on the state and not the URL itself. With Kilua you can use both approaches.

### Layout based routing

Use Kilua's composable UI functions to directly build UI based on the routing data.

```kotlin
import app.softwork.routingcompose.HashRouter
import app.softwork.routingcompose.invoke

HashRouter("/") {
    route("/") {
        p {
            +"Home"
        }
    }
    route("/about") {
        p {
            +"About"
        }
    }
    route("/contact") {
        p {
            +"Contact"
        }
    }
    noMatch {
        p {
            +"Not found"
        }
    }
}
```

### State based routing

Use `routeAction()`, `stringAction()`, `intAction()`and `noMatchAction()` functions (which are convenient wrappers over more low-level `RouteEffect` function) to call some actions. The actions can be suspending, can access external resources and they should change the state of the application instead of directly rendering UI. The UI itself is rendered using standard Compose state binding.

```kotlin
import app.softwork.routingcompose.HashRouter
import app.softwork.routingcompose.invoke

HashRouter("/") {

    var state by remember { mutableStateOf("Home") }

    p {
        +state
    }

    routeAction("/") {
        state = "Home"
    }
    routeAction("/about") {
        state = "About"
    }
    routeAction("/contact") {
        state = "Contact"
    }
    noMatchAction {
        state = "Not found"
    }
}
```

{% hint style="info" %}
You can use `BrowserRouter` the same way.
{% endhint %}
