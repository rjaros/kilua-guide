# Routing

Kilua has a routing module, which is a fork of the [routing-compose](https://github.com/hfhbd/routing-compose) library for Compose Web, HTML and Desktop. The `kilua-routing` module contains are two types of routers - `HashRouter` and `BrowserRouter`.&#x20;

## HashRouter&#x20;

`HashRouter` is used for hashed URLs (e.g. `https://example.com/#/path`). This kind of routing is 100% client side (the part of the URL after the `#` is not sent to the server). It doesn't require any additional setup on the backend side, neither in development mode nor in production.

|          Pros         |                    Cons                    |
| :-------------------: | :----------------------------------------: |
| Easy to setup and use |           Less SEO-friendly URLs           |
|                       | No support for SSR (Server Side Rendering) |

## BrowserRouter

`BrowserRouter` uses [History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API) and traditional URLs (e.g. `https://example.com/path`). This kind of routing requires support from the server, which must return some resource (most often the content of `index.html`) for every path.

|                   Pros                  |        Cons        |
| :-------------------------------------: | :----------------: |
|        SEO-friendly, natural URLs       | More complex setup |
| Support for SSR (Server Side Rendering) |                    |

All Kilua templates and example applications are already configured for use of `BrowserRouter` with webpack development. But you need to make sure your publication server is configured for History API routing. You can find some valuable information on [this page](https://router.vuejs.org/guide/essentials/history-mode#Example-Server-Configurations).

