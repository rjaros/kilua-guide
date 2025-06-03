# Forms

Forms are one of the most important parts of GUI of many applications, especially the ones which collect and manage data from the users. Traditionally handling HTML forms is a complex process, composed of many small steps like placing HTML form controls on the page, setting and displaying initial data, reading user data from individual HTML elements and finally transforming and validating the data. It gets even worse if you are using custom visual components for complex data types - date, time, rich text, multiple select, uploaded files etc.

Kilua lets you work with forms in a very simple, consistent and efficient way. It hides all complex data transformations inside the framework logic and offers you a fully type-safe binding between your data and your forms. It has support for many different, both simple and complex, form controls. And it gives you ready to use data validation.

## Form data model

Form data is modelled with a standard Kotlin data class enhanced with `@Serializable` annotation from [kotlinx.serialization](https://github.com/Kotlin/kotlinx.serialization) library. Every field of this model class holds the value of one input item rendered within a form. The model support basic data types: `String`, `Number`(including `Int`and `Double`), `Boolean`, `LocalDate` , `LocalTime` , `LocalDateTime` and also a special type `List<KFile>` (a list of uploaded files).

```kotlin
@Serializable
data class FormModel(
    val text: String? = null,
    val password: String? = null,
    val password2: String? = null,
    val textarea: String? = null,
    val richtext: String? = null,
    val date: LocalDate? = null,
    val time: LocalTime? = null,
    val checkbox: Boolean = false,
    val radio: Boolean = false,
    val select: String? = null,
    val tomselect: String? = null,
    val spinner: Int? = null,
    val radiogroup: String? = null,
    val upload: List<KFile>? = null
)
```

{% hint style="info" %}
Note: When defining form data model you need to make sure all the fields have default values.
{% endhint %}

## Dynamic forms

Sometimes it's not possible to know the data model of the form because the fields need to be added and/or removed dynamically. For such use cases Kilua allows you to model your data with a `Map<String, Any?>` type. The values are still strictly typed, but you need to use casting to access the correct type of data. &#x20;

## From controls

Kilua comes with a bunch of built-in or modular form composables for many different types of form data:

<table><thead><tr><th width="195">Composable</th><th width="158">Data type</th><th width="114">Module</th><th>Description</th></tr></thead><tbody><tr><td><code>text()</code></td><td><code>String?</code></td><td>built-in</td><td>A text field</td></tr><tr><td><code>textArea()</code></td><td><code>String?</code></td><td>built-in</td><td>A text area</td></tr><tr><td><code>password()</code></td><td><code>String?</code></td><td>built-in</td><td>A password field</td></tr><tr><td><code>checkBox()</code></td><td><code>Boolean</code></td><td>built-in</td><td>A checkbox</td></tr><tr><td><code>triStateCheckBox()</code></td><td><code>Boolean?</code></td><td>built-in</td><td>A tri-state checkbox (with <code>null</code> state)</td></tr><tr><td><code>radio()</code></td><td><code>Boolean</code></td><td>built-in</td><td>A radio button</td></tr><tr><td><code>radioGroup()</code></td><td><code>String?</code></td><td>built-in</td><td>A group of radio buttons</td></tr><tr><td><code>colorPicker()</code></td><td><code>String?</code></td><td>built-in</td><td>An HTML color picker</td></tr><tr><td><code>numeric()</code></td><td><code>Number?</code></td><td>built-in</td><td>A numeric field</td></tr><tr><td><code>range()</code></td><td><code>Number?</code></td><td>built-in</td><td>A range field</td></tr><tr><td><code>spinner()</code></td><td><code>Int?</code></td><td>built-in</td><td>A spinner numeric field</td></tr><tr><td><code>select()</code></td><td><code>String?</code></td><td>built-in</td><td>A simple select field</td></tr><tr><td><code>date()</code></td><td><code>LocalDate?</code></td><td>built-in</td><td>A simple HTML date picker</td></tr><tr><td><code>dateTime()</code></td><td><code>LocalDateTime?</code></td><td>built-in</td><td>A simple HTML date and time picker</td></tr><tr><td><code>time()</code></td><td><code>LocalTime?</code></td><td>built-in</td><td>A simple HTML time picker</td></tr><tr><td><code>upload()</code></td><td><code>List&#x3C;KFile>?</code></td><td>built-in</td><td>A File picker</td></tr><tr><td><code>imaskNumeric()</code></td><td><code>Number?</code></td><td>kilua-imask</td><td>A numeric field with masking</td></tr><tr><td><code>richText()</code></td><td><code>String?</code></td><td>kilua-trix</td><td>A rich text editor</td></tr><tr><td><code>richDate()</code></td><td><code>LocalDate?</code></td><td>kilua-tempus-dominus</td><td>A rich date picker</td></tr><tr><td><code>richDateTime()</code></td><td><code>LocalDateTime?</code></td><td>kilua-tempus-dominus</td><td>A rich date and time picker</td></tr><tr><td><code>richTime()</code></td><td><code>LocalTime?</code></td><td>kilua-tempus-dominus</td><td>A rich time picker</td></tr><tr><td><code>tomSelect()</code></td><td><code>String?</code></td><td>kilua-tom-select</td><td>A rich select field</td></tr><tr><td><code>tomTypeahead()</code></td><td><code>String?</code></td><td>kilua-tom-select</td><td>A text field with autocomplete </td></tr></tbody></table>

## Creating a form

To create a form just use a `form` composable function. It can be used with a type parameter for static data model (a data class) or without it to use a dynamic model (`Map<String, Any?>`).

Let's build an example login form, with two standard fields and additional "remember me" checkbox. We will use strict data model:

```kotlin
@Serializable
data class LoginForm(
    val username: String? = null, 
    val password: String? = null,
    val rememberMe: Boolean = false
)
```

We start with a `form` composable:

```kotlin
form<LoginForm> {
}
```

Next, we need to add form controls:

```kotlin
form<LoginForm> {
    text()
    password()
    checkBox()
}
```

We connect form controls with our data model using `bind` method calls:

```kotlin
form<LoginForm> {
    text {
        bind(LoginForm::username)
    }
    password {
        bind(LoginForm::password)
    }
    checkBox {
        bind(LoginForm::rememberMe)
    }
}
```

Now our form (strictly speaking a component created by the composable, which can be accessed with `this@form` or returned with `formRef {}` variant of the composable) can be treated as a kind of "black box". We can both get and set the data using our model class with `getData()` and `setData()` methods. In our case we can initialize the form with some non-default values (`rememberMe = true`) and print login data after clicking the "Login" button:

```kotlin
form<LoginForm> {
    text {
        bind(LoginForm::username)
    }
    password {
        bind(LoginForm::password)
    }
    checkBox {
        bind(LoginForm::rememberMe)
    }
    button("Login") {
        onClick {
            println("Login data: ${this@form.getData()}")
        }
    }
    LaunchedEffect(Unit) {
        this@form.setData(LoginForm(rememberMe = true))
    }
}
```

Our form is working, but it's not very accessible, because there are no labels for our fields. We can add them manually:

```kotlin
form<LoginForm> {
    label(htmlFor = "username") {
        +"Username:"
    }
    text(id = "username") {
        bind(LoginForm::username)
    }
    label(htmlFor = "password") {
        +"Password:"
    }
    password(id = "password") {
        bind(LoginForm::password)
    }
    label(htmlFor = "remember") {
        +"Remember me:"
    }
    checkBox(id = "remember", value = true) {
        bind(LoginForm::rememberMe)
    }
    button("Login") {
        onClick {
            println("Login data: ${this@form.getData()}")
        }
    }
    LaunchedEffect(Unit) {
        this@form.setData(LoginForm(rememberMe = true))
    }
}
```

We can also use a `fieldWithLabel` helper function, which will automatically generate ID attributes for our controls:

```kotlin
form<LoginForm> {
    fieldWithLabel("Username") {
        text(id = it) {
            bind(LoginForm::username)
        }
    }
    fieldWithLabel("Password") {
        password(id = it) {
            bind(LoginForm::password)
        }
    }
    fieldWithLabel("Remember me") {
        checkBox(id = it) {
            bind(LoginForm::rememberMe)
        }
    }
    button("Login") {
        onClick {
            println("Login data: ${this@form.getData()}")
        }
    }
    LaunchedEffect(Unit) {
        this@form.setData(LoginForm(rememberMe = true))
    }
}
```

And finally we can add some flexbox styling to make the form more visually appealing:

```kotlin
form<LoginForm> {
    vPanel(gap = 5.px) {
        maxWidth(400.px)
        hPanel(justifyContent = JustifyContent.SpaceBetween, gap = 15.px) {
            fieldWithLabel("Username") {
                text(id = it) {
                    bind(LoginForm::username)
                }
            }
        }
        hPanel(justifyContent = JustifyContent.SpaceBetween, gap = 15.px) {
            fieldWithLabel("Password") {
                password(id = it) {
                    bind(LoginForm::password)
                }
            }
        }
        hPanel(justifyContent = JustifyContent.Start, gap = 5.px) {
            fieldWithLabel("Remember me", labelAfter = true) {
                checkBox(id = it) {
                    bind(LoginForm::rememberMe)
                }
            }
        }
        hPanel(justifyContent = JustifyContent.Center) {
            button("Login") {
                onClick {
                    println("Login data: ${this@form.getData()}")
                }
            }
        }
    }
    LaunchedEffect(Unit) {
        this@form.setData(LoginForm(rememberMe = true))
    }
}
```
