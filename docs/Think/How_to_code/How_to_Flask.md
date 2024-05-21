## Installation
### Python Version

We recommend using the latest version of Python. Flask supports Python 3.8 and newer.

### Dependencies

These distributions will be installed automatically when installing Flask.

- [Werkzeug](https://palletsprojects.com/p/werkzeug/) implements WSGI, the standard Python interface between applications and servers.
- [Jinja](https://palletsprojects.com/p/jinja/) is a template language that renders the pages your application serves.
- [MarkupSafe](https://palletsprojects.com/p/markupsafe/) comes with Jinja. It escapes untrusted input when rendering templates to avoid injection attacks.
- [ItsDangerous](https://palletsprojects.com/p/itsdangerous/) securely signs data to ensure its integrity. This is used to protect Flask’s session cookie.
- [Click](https://palletsprojects.com/p/click/) is a framework for writing command line applications. It provides the `flask` command and allows adding custom management commands.
- [Blinker](https://blinker.readthedocs.io/) provides support for [Signals](https://flask.palletsprojects.com/en/3.0.x/signals/).

### Optional dependencies

These distributions will not be installed automatically. Flask will detect and use them if you install them.

- [python-dotenv](https://github.com/theskumar/python-dotenv#readme) enables support for [Environment Variables From dotenv](https://flask.palletsprojects.com/en/3.0.x/cli/#dotenv) when running `flask` commands. 
- [Watchdog](https://pythonhosted.org/watchdog/) provides a faster, more efficient reloader for the development server.

### Greenlet

You may choose to use gevent or eventlet with your application. In this case, greenlet>=1.0 is required. When using PyPy, PyPy>=7.3.7 is required.

These are not minimum supported versions, they only indicate the first versions that added necessary features. You should use the latest versions of each.

### Virtual environments

Use a virtual environment to manage the dependencies for your project, both in development and in production.

What problem does a virtual environment solve? The more Python projects you have, the more likely it is that you need to work with different versions of Python libraries, or even Python itself. Newer versions of libraries for one project can break compatibility in another project.

Virtual environments are independent groups of Python libraries, one for each project. Packages installed for one project will not affect other projects or the operating system’s packages.

Python comes bundled with the [`venv`](https://docs.python.org/3/library/venv.html#module-venv "(in Python v3.12)") module to create virtual environments.

### Create an environment

Create a project folder and a `.venv` folder within:

```sh
mkdir myproject
cd myproject
py -3 -m venv .venv
```
### Activate the environment

Before you work on your project, activate the corresponding environment:

```sh
.venv\Scripts\activate
```

Your shell prompt will change to show the name of the activated environment.

### Install Flask

Within the activated environment, use the following command to install Flask:

```sh
pip install Flask
```

Flask is now installed. Check out the [[#Quickstart]] or go to the [Documentation Overview](https://flask.palletsprojects.com/en/3.0.x/).

## Quickstart

Eager to get started? This page gives a good introduction to Flask. Follow [[#Installation]] to set up a project and install Flask first.

### A Minimal Application

A minimal Flask application looks something like this:

```python
from flask import Flask

app = Flask(__name__)

@app.route("/")
def hello_world():
    return "<p>Hello, World!</p>"
```

So what did that code do?

1. First we imported the [`Flask`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask "flask.Flask") class. An instance of this class will be our WSGI application.
2. Next we create an instance of this class. The first argument is the name of the application’s module or package. `__name__` is a convenient shortcut for this that is appropriate for most cases. This is needed so that Flask knows where to look for resources such as templates and static files.
3. We then use the [`route()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.route "flask.Flask.route") decorator to tell Flask what URL should trigger our function.
4. The function returns the message we want to display in the user’s browser. The default content type is HTML, so HTML in the string will be rendered by the browser.

Save it as `hello.py` or something similar. Make sure to not call your application `flask.py` because this would conflict with Flask itself.

To run the application, use the `flask` command or `python -m flask`. You need to tell the Flask where your application is with the `--app` option.

```sh
flask --app hello run
 * Serving Flask app 'hello'
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
```

> Application Discovery Behavior
>
>As a shortcut, if the file is named `app.py` or `wsgi.py`, you don’t have to use `--app`. See [Command Line Interface](https://flask.palletsprojects.com/en/3.0.x/cli/) for more details.

This launches a very simple builtin server, which is good enough for testing but probably not what you want to use in production. For deployment options see [Deploying to Production](https://flask.palletsprojects.com/en/3.0.x/deploying/).

Now head over to [http://127.0.0.1:5000/](http://127.0.0.1:5000/), and you should see your hello world greeting.

If another program is already using port 5000, you’ll see `OSError: [Errno 98]` or `OSError: [WinError 10013]` when the server tries to start. See [Address already in use](https://flask.palletsprojects.com/en/3.0.x/server/#address-already-in-use) for how to handle that.

> Externally Visible Server
>
> If you run the server you will notice that the server is only accessible from your own computer, not from any other in the network. This is the default because in debugging mode a user of the application can execute arbitrary Python code on your computer.
>
> If you have the debugger disabled or trust the users on your network, you can make the server publicly available simply by adding `--host=0.0.0.0` to the command line:
>
> $ flask run --host=0.0.0.0
>
> This tells your operating system to listen on all public IPs.

### Debug Mode

The `flask run` command can do more than just start the development server. By enabling debug mode, the server will automatically reload if code changes, and will show an interactive debugger in the browser if an error occurs during a request.

![The interactive debugger in action.](https://flask.palletsprojects.com/en/3.0.x/_images/debugger.png)

> Warning
> 
> The debugger allows executing arbitrary Python code from the browser. It is protected by a pin, but still represents a major security risk. Do not run the development server or debugger in a production environment.

To enable debug mode, use the `--debug` option.

```sh
flask --app hello run --debug
 * Serving Flask app 'hello'
 * Debug mode: on
 * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: nnn-nnn-nnn
```


### HTML Escaping

When returning HTML (the default response type in Flask), any user-provided values rendered in the output must be escaped to protect from injection attacks. HTML templates rendered with Jinja, introduced later, will do this automatically.

`escape()`, shown here, can be used manually. It is omitted in most examples for brevity, but you should always be aware of how you’re using untrusted data.

```python
from markupsafe import escape

@app.route("/<name>")
def hello(name):
    return f"Hello, {escape(name)}!"
```

If a user managed to submit the name `<script>alert("bad")</script>`, escaping causes it to be rendered as text, rather than running the script in the user’s browser.

`<name>` in the route captures a value from the URL and passes it to the view function. These variable rules are explained below.

### Routing

Modern web applications use meaningful URLs to help users. Users are more likely to like a page and come back if the page uses a meaningful URL they can remember and use to directly visit a page.

Use the [`route()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.route "flask.Flask.route") decorator to bind a function to a URL.

```python
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

You can do more! You can make parts of the URL dynamic and attach multiple rules to a function.

#### Variable Rules

You can add variable sections to a URL by marking sections with `<variable_name>`. Your function then receives the `<variable_name>` as a keyword argument. Optionally, you can use a converter to specify the type of the argument like `<converter:variable_name>`.

```python
from markupsafe import escape

@app.route('/user/<username>')
def show_user_profile(username):
    # show the user profile for that user
    return f'User {escape(username)}'

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # show the post with the given id, the id is an integer
    return f'Post {post_id}'

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # show the subpath after /path/
    return f'Subpath {escape(subpath)}'
```

Converter types:

| `string` | (default) accepts any text without a slash |
| -------- | ------------------------------------------ |
| `int`    | accepts positive integers                  |
| `float`  | accepts positive floating point values     |
| `path`   | like `string` but also accepts slashes     |
| `uuid`   | accepts UUID strings                       |

#### Unique URLs / Redirection Behavior

The following two rules differ in their use of a trailing slash.

```python
@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```

The canonical URL for the `projects` endpoint has a trailing slash. It’s similar to a folder in a file system. If you access the URL without a trailing slash (`/projects`), Flask redirects you to the canonical URL with the trailing slash (`/projects/`).

The canonical URL for the `about` endpoint does not have a trailing slash. It’s similar to the pathname of a file. Accessing the URL with a trailing slash (`/about/`) produces a 404 “Not Found” error. This helps keep URLs unique for these resources, which helps search engines avoid indexing the same page twice.

#### URL Building

To build a URL to a specific function, use the [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") function. It accepts the name of the function as its first argument and any number of keyword arguments, each corresponding to a variable part of the URL rule. Unknown variable parts are appended to the URL as query parameters.

Why would you want to build URLs using the URL reversing function [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") instead of hard-coding them into your templates?

1. Reversing is often more descriptive than hard-coding the URLs.
2. You can change your URLs in one go instead of needing to remember to manually change hard-coded URLs.
3. URL building handles escaping of special characters transparently.
4. The generated paths are always absolute, avoiding unexpected behavior of relative paths in browsers.
5. If your application is placed outside the URL root, for example, in `/myapplication` instead of `/`, [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") properly handles that for you.

For example, here we use the [`test_request_context()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.test_request_context "flask.Flask.test_request_context") method to try out [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for"). [`test_request_context()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.test_request_context "flask.Flask.test_request_context") tells Flask to behave as though it’s handling a request even while we use a Python shell. See [Context Locals](https://flask.palletsprojects.com/en/3.0.x/quickstart/#context-locals).

```python
from flask import url_for

@app.route('/')
def index():
    return 'index'

@app.route('/login')
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return f'{username}\'s profile'

with app.test_request_context():
    print(url_for('index'))
    print(url_for('login'))
    print(url_for('login', next='/'))
    print(url_for('profile', username='John Doe'))
```

```
/
/login
/login?next=/
/user/John%20Doe
```

#### HTTP Methods

Web applications use different HTTP methods when accessing URLs. You should familiarize yourself with the HTTP methods as you work with Flask. By default, a route only answers to `GET` requests. You can use the `methods` argument of the [`route()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.route "flask.Flask.route") decorator to handle different HTTP methods.

```python
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()
```

The example above keeps all methods for the route within one function, which can be useful if each part uses some common data.

You can also separate views for different methods into different functions. Flask provides a shortcut for decorating such routes with [`get()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.get "flask.Flask.get"), [`post()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.post "flask.Flask.post"), etc. for each common HTTP method.

```python
@app.get('/login')
def login_get():
    return show_the_login_form()

@app.post('/login')
def login_post():
    return do_the_login()
```

If `GET` is present, Flask automatically adds support for the `HEAD` method and handles `HEAD` requests according to the [HTTP RFC](https://www.ietf.org/rfc/rfc2068.txt). Likewise, `OPTIONS` is automatically implemented for you.

### Static Files

Dynamic web applications also need static files. That’s usually where the CSS and JavaScript files are coming from. Ideally your web server is configured to serve them for you, but during development Flask can do that as well. Just create a folder called `static` in your package or next to your module and it will be available at `/static` on the application.

To generate URLs for static files, use the special `'static'` endpoint name:

```python
url_for('static', filename='style.css')
```

The file has to be stored on the filesystem as `static/style.css`.

### Rendering Templates

Generating HTML from within Python is not fun, and actually pretty cumbersome because you have to do the HTML escaping on your own to keep the application secure. Because of that Flask configures the [Jinja2](https://palletsprojects.com/p/jinja/) template engine for you automatically.

Templates can be used to generate any type of text file. For web applications, you’ll primarily be generating HTML pages, but you can also generate markdown, plain text for emails, and anything else.

For a reference to HTML, CSS, and other web APIs, use the [MDN Web Docs](https://developer.mozilla.org/).

To render a template you can use the [`render_template()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.render_template "flask.render_template") method. All you have to do is provide the name of the template and the variables you want to pass to the template engine as keyword arguments. Here’s a simple example of how to render a template:

```python
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

Flask will look for templates in the `templates` folder. So if your application is a module, this folder is next to that module, if it’s a package it’s actually inside your package:

**Case 1**: a module:

```
/application.py
/templates
    /hello.html
```

**Case 2**: a package:

```
/application
    /__init__.py
    /templates
        /hello.html
```

For templates you can use the full power of Jinja2 templates. Head over to the official [Jinja2 Template Documentation](https://jinja.palletsprojects.com/templates/) for more information.

Here is an example template:

```jinja2
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```

Inside templates you also have access to the [`config`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.config "flask.Flask.config"), [`request`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.request "flask.request"), [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") and [`g`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.g "flask.g") [[1]](https://flask.palletsprojects.com/en/3.0.x/quickstart/#id3) objects as well as the [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") and [`get_flashed_messages()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.get_flashed_messages "flask.get_flashed_messages") functions.

Templates are especially useful if inheritance is used. If you want to know how that works, see [Template Inheritance](https://flask.palletsprojects.com/en/3.0.x/patterns/templateinheritance/). Basically template inheritance makes it possible to keep certain elements on each page (like header, navigation and footer).

Automatic escaping is enabled, so if `name` contains HTML it will be escaped automatically. If you can trust a variable and you know that it will be safe HTML (for example because it came from a module that converts wiki markup to HTML) you can mark it as safe by using the `Markup` class or by using the `|safe` filter in the template. Head over to the Jinja 2 documentation for more examples.

Here is a basic introduction to how the `Markup` class works:

```python
>>> from markupsafe import Markup
>>> Markup('<strong>Hello %s!</strong>') % '<blink>hacker</blink>'
Markup('<strong>Hello &lt;blink&gt;hacker&lt;/blink&gt;!</strong>')
>>> Markup.escape('<blink>hacker</blink>')
Markup('&lt;blink&gt;hacker&lt;/blink&gt;')
>>> Markup('<em>Marked up</em> &raquo; HTML').striptags()
'Marked up » HTML'
```

### Accessing Request Data

For web applications it’s crucial to react to the data a client sends to the server. In Flask this information is provided by the global [`request`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.request "flask.request") object. If you have some experience with Python you might be wondering how that object can be global and how Flask manages to still be threadsafe. The answer is context locals:

#### Context Locals

Certain objects in Flask are global objects, but not of the usual kind. These objects are actually proxies to objects that are local to a specific context. What a mouthful. But that is actually quite easy to understand.

Imagine the context being the handling thread. A request comes in and the web server decides to spawn a new thread (or something else, the underlying object is capable of dealing with concurrency systems other than threads). When Flask starts its internal request handling it figures out that the current thread is the active context and binds the current application and the WSGI environments to that context (thread). It does that in an intelligent way so that one application can invoke another application without breaking.

So what does this mean to you? Basically you can completely ignore that this is the case unless you are doing something like unit testing. You will notice that code which depends on a request object will suddenly break because there is no request object. The solution is creating a request object yourself and binding it to the context. The easiest solution for unit testing is to use the [`test_request_context()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.test_request_context "flask.Flask.test_request_context") context manager. In combination with the `with` statement it will bind a test request so that you can interact with it. Here is an example:

```python
from flask import request

with app.test_request_context('/hello', method='POST'):
    # now you can do something with the request until the
    # end of the with block, such as basic assertions:
    assert request.path == '/hello'
    assert request.method == 'POST'
```

The other possibility is passing a whole WSGI environment to the [`request_context()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.request_context "flask.Flask.request_context") method:

```python
with app.request_context(environ):
    assert request.method == 'POST'
```

#### The Request Object

The request object is documented in the API section and we will not cover it here in detail (see [`Request`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request "flask.Request")). Here is a broad overview of some of the most common operations. First of all you have to import it from the `flask` module:

```python
from flask import request
```

The current request method is available by using the [`method`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.method "flask.Request.method") attribute. To access form data (data transmitted in a `POST` or `PUT` request) you can use the [`form`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.form "flask.Request.form") attribute. Here is a full example of the two attributes mentioned above:

```python
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # the code below is executed if the request method
    # was GET or the credentials were invalid
    return render_template('login.html', error=error)
```

What happens if the key does not exist in the `form` attribute? In that case a special [`KeyError`](https://docs.python.org/3/library/exceptions.html#KeyError "(in Python v3.12)") is raised. You can catch it like a standard [`KeyError`](https://docs.python.org/3/library/exceptions.html#KeyError "(in Python v3.12)") but if you don’t do that, a HTTP 400 Bad Request error page is shown instead. So for many situations you don’t have to deal with that problem.

To access parameters submitted in the URL (`?key=value`) you can use the [`args`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.args "flask.Request.args") attribute:

```python
searchword = request.args.get('key', '')
```

We recommend accessing URL parameters with `get` or by catching the [`KeyError`](https://docs.python.org/3/library/exceptions.html#KeyError "(in Python v3.12)") because users might change the URL and presenting them a 400 bad request page in that case is not user friendly.

For a full list of methods and attributes of the request object, head over to the [`Request`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request "flask.Request") documentation.

#### File Uploads

You can handle uploaded files with Flask easily. Just make sure not to forget to set the `enctype="multipart/form-data"` attribute on your HTML form, otherwise the browser will not transmit your files at all.

Uploaded files are stored in memory or at a temporary location on the filesystem. You can access those files by looking at the `files` attribute on the request object. Each uploaded file is stored in that dictionary. It behaves just like a standard Python `file` object, but it also has a [`save()`](https://werkzeug.palletsprojects.com/en/3.0.x/datastructures/#werkzeug.datastructures.FileStorage.save "(in Werkzeug v3.0.x)") method that allows you to store that file on the filesystem of the server. Here is a simple example showing how that works:

```python
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...
```

If you want to know how the file was named on the client before it was uploaded to your application, you can access the [`filename`](https://werkzeug.palletsprojects.com/en/3.0.x/datastructures/#werkzeug.datastructures.FileStorage.filename "(in Werkzeug v3.0.x)") attribute. However please keep in mind that this value can be forged so never ever trust that value. If you want to use the filename of the client to store the file on the server, pass it through the [`secure_filename()`](https://werkzeug.palletsprojects.com/en/3.0.x/utils/#werkzeug.utils.secure_filename "(in Werkzeug v3.0.x)") function that Werkzeug provides for you:

```python
from werkzeug.utils import secure_filename

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        file = request.files['the_file']
        file.save(f"/var/www/uploads/{secure_filename(file.filename)}")
    ...
```

For some better examples, see [Uploading Files](https://flask.palletsprojects.com/en/3.0.x/patterns/fileuploads/).

#### Cookies

To access cookies you can use the [`cookies`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.cookies "flask.Request.cookies") attribute. To set cookies you can use the [`set_cookie`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Response.set_cookie "flask.Response.set_cookie") method of response objects. The [`cookies`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.cookies "flask.Request.cookies") attribute of request objects is a dictionary with all the cookies the client transmits. If you want to use sessions, do not use the cookies directly but instead use the [Sessions](https://flask.palletsprojects.com/en/3.0.x/quickstart/#sessions) in Flask that add some security on top of cookies for you.

Reading cookies:

```python
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # use cookies.get(key) instead of cookies[key] to not get a
    # KeyError if the cookie is missing.
```

Storing cookies:

```python
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

Note that cookies are set on response objects. Since you normally just return strings from the view functions Flask will convert them into response objects for you. If you explicitly want to do that you can use the [`make_response()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.make_response "flask.make_response") function and then modify it.

Sometimes you might want to set a cookie at a point where the response object does not exist yet. This is possible by utilizing the [Deferred Request Callbacks](https://flask.palletsprojects.com/en/3.0.x/patterns/deferredcallbacks/) pattern.

For this also see [About Responses](https://flask.palletsprojects.com/en/3.0.x/quickstart/#about-responses).

### Redirects and Errors
To redirect a user to another endpoint, use the [`redirect()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.redirect "flask.redirect") function; to abort a request early with an error code, use the [`abort()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.abort "flask.abort") function:

```python
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

This is a rather pointless example because a user will be redirected from the index to a page they cannot access (401 means access denied) but it shows how that works.

By default a black and white error page is shown for each error code. If you want to customize the error page, you can use the [`errorhandler()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.errorhandler "flask.Flask.errorhandler") decorator:

```python
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

Note the `404` after the [`render_template()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.render_template "flask.render_template") call. This tells Flask that the status code of that page should be 404 which means not found. By default 200 is assumed which translates to: all went well.

See [Handling Application Errors](https://flask.palletsprojects.com/en/3.0.x/errorhandling/) for more details.

### About Responses

The return value from a view function is automatically converted into a response object for you. If the return value is a string it’s converted into a response object with the string as response body, a `200 OK` status code and a _text/html_ mimetype. If the return value is a dict or list, `jsonify()` is called to produce a response. The logic that Flask applies to converting return values into response objects is as follows:

1. If a response object of the correct type is returned it’s directly returned from the view.
2. If it’s a string, a response object is created with that data and the default parameters.
3. If it’s an iterator or generator returning strings or bytes, it is treated as a streaming response.
4. If it’s a dict or list, a response object is created using [`jsonify()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.json.jsonify "flask.json.jsonify").
5. If a tuple is returned the items in the tuple can provide extra information. Such tuples have to be in the form `(response, status)`, `(response, headers)`, or `(response, status, headers)`. The `status` value will override the status code and `headers` can be a list or dictionary of additional header values.
6. If none of that works, Flask will assume the return value is a valid WSGI application and convert that into a response object.

If you want to get hold of the resulting response object inside the view you can use the [`make_response()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.make_response "flask.make_response") function.

Imagine you have a view like this:

```python
from flask import render_template

@app.errorhandler(404)
def not_found(error):
    return render_template('error.html'), 404
```

You just need to wrap the return expression with [`make_response()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.make_response "flask.make_response") and get the response object to modify it, then return it:

```python
from flask import make_response

@app.errorhandler(404)
def not_found(error):
    resp = make_response(render_template('error.html'), 404)
    resp.headers['X-Something'] = 'A value'
    return resp
```

#### APIs with JSON

A common response format when writing an API is JSON. It’s easy to get started writing such an API with Flask. If you return a `dict` or `list` from a view, it will be converted to a JSON response.

```python
@app.route("/me")
def me_api():
    user = get_current_user()
    return {
        "username": user.username,
        "theme": user.theme,
        "image": url_for("user_image", filename=user.image),
    }

@app.route("/users")
def users_api():
    users = get_all_users()
    return [user.to_json() for user in users]
```

This is a shortcut to passing the data to the [`jsonify()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.json.jsonify "flask.json.jsonify") function, which will serialize any supported JSON data type. That means that all the data in the dict or list must be JSON serializable.

For complex types such as database models, you’ll want to use a serialization library to convert the data to valid JSON types first. There are many serialization libraries and Flask API extensions maintained by the community that support more complex applications.

### Sessions

In addition to the request object there is also a second object called [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") which allows you to store information specific to a user from one request to the next. This is implemented on top of cookies for you and signs the cookies cryptographically. What this means is that the user could look at the contents of your cookie but not modify it, unless they know the secret key used for signing.

In order to use sessions you have to set a secret key. Here is how sessions work:

```python
from flask import session
app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

@app.route('/')
def index():
    if 'username' in session:
        return f'Logged in as {session["username"]}'
    return 'You are not logged in'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # remove the username from the session if it's there
    session.pop('username', None)
    return redirect(url_for('index'))
```

A note on cookie-based sessions: Flask will take the values you put into the session object and serialize them into a cookie. If you are finding some values do not persist across requests, cookies are indeed enabled, and you are not getting a clear error message, check the size of the cookie in your page responses compared to the size supported by web browsers.

Besides the default client-side based sessions, if you want to handle sessions on the server-side instead, there are several Flask extensions that support this.

### Message Flashing

Good applications and user interfaces are all about feedback. If the user does not get enough feedback they will probably end up hating the application. Flask provides a really simple way to give feedback to a user with the flashing system. The flashing system basically makes it possible to record a message at the end of a request and access it on the next (and only the next) request. This is usually combined with a layout template to expose the message.

To flash a message use the [`flash()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.flash "flask.flash") method, to get hold of the messages you can use [`get_flashed_messages()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.get_flashed_messages "flask.get_flashed_messages") which is also available in the templates. See [Message Flashing](https://flask.palletsprojects.com/en/3.0.x/patterns/flashing/) for a full example.

### Logging

Sometimes you might be in a situation where you deal with data that should be correct, but actually is not. For example you may have some client-side code that sends an HTTP request to the server but it’s obviously malformed. This might be caused by a user tampering with the data, or the client code failing. Most of the time it’s okay to reply with `400 Bad Request` in that situation, but sometimes that won’t do and the code has to continue working.

You may still want to log that something fishy happened. This is where loggers come in handy. As of Flask 0.3 a logger is preconfigured for you to use.

Here are some example log calls:

```python
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

The attached [`logger`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.logger "flask.Flask.logger") is a standard logging [`Logger`](https://docs.python.org/3/library/logging.html#logging.Logger "(in Python v3.12)"), so head over to the official [`logging`](https://docs.python.org/3/library/logging.html#module-logging "(in Python v3.12)") docs for more information.

See [Handling Application Errors](https://flask.palletsprojects.com/en/3.0.x/errorhandling/).

### Hooking in WSGI Middleware

To add WSGI middleware to your Flask application, wrap the application’s `wsgi_app` attribute. For example, to apply Werkzeug’s [`ProxyFix`](https://werkzeug.palletsprojects.com/en/3.0.x/middleware/proxy_fix/#werkzeug.middleware.proxy_fix.ProxyFix "(in Werkzeug v3.0.x)") middleware for running behind Nginx:

```python
from werkzeug.middleware.proxy_fix import ProxyFix
app.wsgi_app = ProxyFix(app.wsgi_app)
```
Wrapping `app.wsgi_app` instead of `app` means that `app` still points at your Flask application, not at the middleware, so you can continue to use and configure `app` directly.

### Using Flask Extensions

Extensions are packages that help you accomplish common tasks. For example, Flask-SQLAlchemy provides SQLAlchemy support that makes it simple and easy to use with Flask.

For more on Flask extensions, see [Extensions](https://flask.palletsprojects.com/en/3.0.x/extensions/).

### # Deploying to a Web Server

Ready to deploy your new Flask app? See [Deploying to Production](https://flask.palletsprojects.com/en/3.0.x/deploying/).
This tutorial will walk you through creating a basic blog application called Flaskr. Users will be able to register, log in, create posts, and edit or delete their own posts. You will be able to package and install the application on other computers.

![screenshot of index page](https://flask.palletsprojects.com/en/3.0.x/_images/flaskr_index.png)

It’s assumed that you’re already familiar with Python. The [official tutorial](https://docs.python.org/3/tutorial/) in the Python docs is a great way to learn or review first.

While it’s designed to give a good starting point, the tutorial doesn’t cover all of Flask’s features. Check out the [Quickstart](https://flask.palletsprojects.com/en/3.0.x/quickstart/) for an overview of what Flask can do, then dive into the docs to find out more. The tutorial only uses what’s provided by Flask and Python. In another project, you might decide to use [Extensions](https://flask.palletsprojects.com/en/3.0.x/extensions/) or other libraries to make some tasks simpler.

![screenshot of login page](https://flask.palletsprojects.com/en/3.0.x/_images/flaskr_login.png)

Flask is flexible. It doesn’t require you to use any particular project or code layout. However, when first starting, it’s helpful to use a more structured approach. This means that the tutorial will require a bit of boilerplate up front, but it’s done to avoid many common pitfalls that new developers encounter, and it creates a project that’s easy to expand on. Once you become more comfortable with Flask, you can step out of this structure and take full advantage of Flask’s flexibility.

![screenshot of edit page](https://flask.palletsprojects.com/en/3.0.x/_images/flaskr_edit.png)

[The tutorial project is available as an example in the Flask repository](https://github.com/pallets/flask/tree/3.0.3/examples/tutorial), if you want to compare your project with the final product as you follow the tutorial.

## Tutorial

### Project Layout

Create a project directory and enter it:

```sh
$ mkdir flask-tutorial
$ cd flask-tutorial
```

Then follow the [installation instructions](https://flask.palletsprojects.com/en/3.0.x/installation/) to set up a Python virtual environment and install Flask for your project.

The tutorial will assume you’re working from the `flask-tutorial` directory from now on. The file names at the top of each code block are relative to this directory.

A Flask application can be as simple as a single file.

`hello.py`

```python
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello, World!'
```

However, as a project gets bigger, it becomes overwhelming to keep all the code in one file. Python projects use _packages_ to organize code into multiple modules that can be imported where needed, and the tutorial will do this as well.

The project directory will contain:

- `flaskr/`, a Python package containing your application code and files.
- `tests/`, a directory containing test modules.
- `.venv/`, a Python virtual environment where Flask and other dependencies are installed.
- Installation files telling Python how to install your project.
- Version control config, such as [git](https://git-scm.com/). You should make a habit of using some type of version control for all your projects, no matter the size.
- Any other project files you might add in the future.

By the end, your project layout will look like this:

```
/home/user/Projects/flask-tutorial
├── flaskr/
│   ├── __init__.py
│   ├── db.py
│   ├── schema.sql
│   ├── auth.py
│   ├── blog.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── auth/
│   │   │   ├── login.html
│   │   │   └── register.html
│   │   └── blog/
│   │       ├── create.html
│   │       ├── index.html
│   │       └── update.html
│   └── static/
│       └── style.css
├── tests/
│   ├── conftest.py
│   ├── data.sql
│   ├── test_factory.py
│   ├── test_db.py
│   ├── test_auth.py
│   └── test_blog.py
├── .venv/
├── pyproject.toml
└── MANIFEST.in
```

If you’re using version control, the following files that are generated while running your project should be ignored. There may be other files based on the editor you use. In general, ignore files that you didn’t write. For example, with git:

`.gitignore`

```
.venv/

*.pyc
__pycache__/

instance/

.pytest_cache/
.coverage
htmlcov/

dist/
build/
*.egg-info/
```

### Application Setup

A Flask application is an instance of the [`Flask`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask "flask.Flask") class. Everything about the application, such as configuration and URLs, will be registered with this class.

The most straightforward way to create a Flask application is to create a global [`Flask`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask "flask.Flask") instance directly at the top of your code, like how the “Hello, World!” example did on the previous page. While this is simple and useful in some cases, it can cause some tricky issues as the project grows.

Instead of creating a [`Flask`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask "flask.Flask") instance globally, you will create it inside a function. This function is known as the _application factory_. Any configuration, registration, and other setup the application needs will happen inside the function, then the application will be returned.

#### The Application Factory

It’s time to start coding! Create the `flaskr` directory and add the `__init__.py` file. The `__init__.py` serves double duty: it will contain the application factory, and it tells Python that the `flaskr` directory should be treated as a package.

```sh
$ mkdir flaskr
```

`flaskr/__init__.py`

```python
import os

from flask import Flask

def create_app(test_config=None):
    # create and configure the app
    app = Flask(__name__, instance_relative_config=True)
    app.config.from_mapping(
        SECRET_KEY='dev',
        DATABASE=os.path.join(app.instance_path, 'flaskr.sqlite'),
    )

    if test_config is None:
        # load the instance config, if it exists, when not testing
        app.config.from_pyfile('config.py', silent=True)
    else:
        # load the test config if passed in
        app.config.from_mapping(test_config)

    # ensure the instance folder exists
    try:
        os.makedirs(app.instance_path)
    except OSError:
        pass

    # a simple page that says hello
    @app.route('/hello')
    def hello():
        return 'Hello, World!'

    return app
```

`create_app` is the application factory function. You’ll add to it later in the tutorial, but it already does a lot.

1. `app = Flask(__name__, instance_relative_config=True)` creates the [`Flask`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask "flask.Flask") instance.
    - `__name__` is the name of the current Python module. The app needs to know where it’s located to set up some paths, and `__name__` is a convenient way to tell it that.
    - `instance_relative_config=True` tells the app that configuration files are relative to the [instance folder](https://flask.palletsprojects.com/en/3.0.x/config/#instance-folders). The instance folder is located outside the `flaskr` package and can hold local data that shouldn’t be committed to version control, such as configuration secrets and the database file.
        
2. [`app.config.from_mapping()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Config.from_mapping "flask.Config.from_mapping") sets some default configuration that the app will use:
    - [`SECRET_KEY`](https://flask.palletsprojects.com/en/3.0.x/config/#SECRET_KEY "SECRET_KEY") is used by Flask and extensions to keep data safe. It’s set to `'dev'` to provide a convenient value during development, but it should be overridden with a random value when deploying.
    - `DATABASE` is the path where the SQLite database file will be saved. It’s under [`app.instance_path`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.instance_path "flask.Flask.instance_path"), which is the path that Flask has chosen for the instance folder. You’ll learn more about the database in the next section.
        
3. [`app.config.from_pyfile()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Config.from_pyfile "flask.Config.from_pyfile") overrides the default configuration with values taken from the `config.py` file in the instance folder if it exists. For example, when deploying, this can be used to set a real `SECRET_KEY`.
    - `test_config` can also be passed to the factory, and will be used instead of the instance configuration. This is so the tests you’ll write later in the tutorial can be configured independently of any development values you have configured.
        
4. [`os.makedirs()`](https://docs.python.org/3/library/os.html#os.makedirs "(in Python v3.12)") ensures that [`app.instance_path`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.instance_path "flask.Flask.instance_path") exists. Flask doesn’t create the instance folder automatically, but it needs to be created because your project will create the SQLite database file there.
    
5. [`@app.route()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.route "flask.Flask.route") creates a simple route so you can see the application working before getting into the rest of the tutorial. It creates a connection between the URL `/hello` and a function that returns a response, the string `'Hello, World!'` in this case.

#### Run The Application

Now you can run your application using the `flask` command. From the terminal, tell Flask where to find your application, then run it in debug mode. Remember, you should still be in the top-level `flask-tutorial` directory, not the `flaskr` package.

Debug mode shows an interactive debugger whenever a page raises an exception, and restarts the server whenever you make changes to the code. You can leave it running and just reload the browser page as you follow the tutorial.

$ flask --app flaskr run --debug

You’ll see output similar to this:

* Serving Flask app "flaskr"
* Debug mode: on
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
* Restarting with stat
* Debugger is active!
* Debugger PIN: nnn-nnn-nnn

Visit [http://127.0.0.1:5000/hello](http://127.0.0.1:5000/hello) in a browser and you should see the “Hello, World!” message. Congratulations, you’re now running your Flask web application!

If another program is already using port 5000, you’ll see `OSError: [Errno 98]` or `OSError: [WinError 10013]` when the server tries to start. See [Address already in use](https://flask.palletsprojects.com/en/3.0.x/server/#address-already-in-use) for how to handle that.

### Define and Access the Database

The application will use a [SQLite](https://sqlite.org/about.html) database to store users and posts. Python comes with built-in support for SQLite in the [`sqlite3`](https://docs.python.org/3/library/sqlite3.html#module-sqlite3 "(in Python v3.12)") module.

SQLite is convenient because it doesn’t require setting up a separate database server and is built-in to Python. However, if concurrent requests try to write to the database at the same time, they will slow down as each write happens sequentially. Small applications won’t notice this. Once you become big, you may want to switch to a different database.

The tutorial doesn’t go into detail about SQL. If you are not familiar with it, the SQLite docs describe the [language](https://sqlite.org/lang.html).

#### Connect to the Database

The first thing to do when working with a SQLite database (and most other Python database libraries) is to create a connection to it. Any queries and operations are performed using the connection, which is closed after the work is finished.

In web applications this connection is typically tied to the request. It is created at some point when handling a request, and closed before the response is sent.

`flaskr/db.py`

```python
import sqlite3

import click
from flask import current_app, g

def get_db():
    if 'db' not in g:
        g.db = sqlite3.connect(
            current_app.config['DATABASE'],
            detect_types=sqlite3.PARSE_DECLTYPES
        )
        g.db.row_factory = sqlite3.Row

    return g.db

def close_db(e=None):
    db = g.pop('db', None)

    if db is not None:
        db.close()
```

[`g`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.g "flask.g") is a special object that is unique for each request. It is used to store data that might be accessed by multiple functions during the request. The connection is stored and reused instead of creating a new connection if `get_db` is called a second time in the same request.

[`current_app`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.current_app "flask.current_app") is another special object that points to the Flask application handling the request. Since you used an application factory, there is no application object when writing the rest of your code. `get_db` will be called when the application has been created and is handling a request, so [`current_app`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.current_app "flask.current_app") can be used.

[`sqlite3.connect()`](https://docs.python.org/3/library/sqlite3.html#sqlite3.connect "(in Python v3.12)") establishes a connection to the file pointed at by the `DATABASE` configuration key. This file doesn’t have to exist yet, and won’t until you initialize the database later.

[`sqlite3.Row`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Row "(in Python v3.12)") tells the connection to return rows that behave like dicts. This allows accessing the columns by name.

`close_db` checks if a connection was created by checking if `g.db` was set. If the connection exists, it is closed. Further down you will tell your application about the `close_db` function in the application factory so that it is called after each request.

#### Create the Tables

In SQLite, data is stored in _tables_ and _columns_. These need to be created before you can store and retrieve data. Flaskr will store users in the `user` table, and posts in the `post` table. Create a file with the SQL commands needed to create empty tables:

`flaskr/schema.sql`

```sql
DROP TABLE IF EXISTS user;
DROP TABLE IF EXISTS post;

CREATE TABLE user (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  username TEXT UNIQUE NOT NULL,
  password TEXT NOT NULL
);

CREATE TABLE post (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  author_id INTEGER NOT NULL,
  created TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  title TEXT NOT NULL,
  body TEXT NOT NULL,
  FOREIGN KEY (author_id) REFERENCES user (id)
);
```

Add the Python functions that will run these SQL commands to the `db.py` file:

`flaskr/db.py`

```python
def init_db():
    db = get_db()

    with current_app.open_resource('schema.sql') as f:
        db.executescript(f.read().decode('utf8'))

@click.command('init-db')
def init_db_command():
    """Clear the existing data and create new tables."""
    init_db()
    click.echo('Initialized the database.')
```
[`open_resource()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.open_resource "flask.Flask.open_resource") opens a file relative to the `flaskr` package, which is useful since you won’t necessarily know where that location is when deploying the application later. `get_db` returns a database connection, which is used to execute the commands read from the file.

[`click.command()`](https://click.palletsprojects.com/en/8.1.x/api/#click.command "(in Click v8.1.x)") defines a command line command called `init-db` that calls the `init_db` function and shows a success message to the user. You can read [Command Line Interface](https://flask.palletsprojects.com/en/3.0.x/cli/) to learn more about writing commands.

#### Register with the Application

The `close_db` and `init_db_command` functions need to be registered with the application instance; otherwise, they won’t be used by the application. However, since you’re using a factory function, that instance isn’t available when writing the functions. Instead, write a function that takes an application and does the registration.

`flaskr/db.py`

```python
def init_app(app):
    app.teardown_appcontext(close_db)
    app.cli.add_command(init_db_command)
```

[`app.teardown_appcontext()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.teardown_appcontext "flask.Flask.teardown_appcontext") tells Flask to call that function when cleaning up after returning the response.

[`app.cli.add_command()`](https://click.palletsprojects.com/en/8.1.x/api/#click.Group.add_command "(in Click v8.1.x)") adds a new command that can be called with the `flask` command.

Import and call this function from the factory. Place the new code at the end of the factory function before returning the app.

`flaskr/__init__.py`

```python
def create_app():
    app = ...
    # existing code omitted

    from . import db
    db.init_app(app)

    return app
```

#### Initialize the Database File

Now that `init-db` has been registered with the app, it can be called using the `flask` command, similar to the `run` command from the previous page.

>Note
>
>If you’re still running the server from the previous page, you can either stop the server, or run this command in a new terminal. If you use a new terminal, remember to change to your project directory and activate the env as described in [Installation](https://flask.palletsprojects.com/en/3.0.x/installation/).

Run the `init-db` command:

```sh
$ flask --app flaskr init-db
Initialized the database.
```

There will now be a `flaskr.sqlite` file in the `instance` folder in your project.

### Blueprints and Views

A view function is the code you write to respond to requests to your application. Flask uses patterns to match the incoming request URL to the view that should handle it. The view returns data that Flask turns into an outgoing response. Flask can also go the other direction and generate a URL to a view based on its name and arguments.

#### Create a Blueprint

A [`Blueprint`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Blueprint "flask.Blueprint") is a way to organize a group of related views and other code. Rather than registering views and other code directly with an application, they are registered with a blueprint. Then the blueprint is registered with the application when it is available in the factory function.

Flaskr will have two blueprints, one for authentication functions and one for the blog posts functions. The code for each blueprint will go in a separate module. Since the blog needs to know about authentication, you’ll write the authentication one first.

`flaskr/auth.py`

```python
import functools

from flask import (
    Blueprint, flash, g, redirect, render_template, request, session, url_for
)
from werkzeug.security import check_password_hash, generate_password_hash

from flaskr.db import get_db

bp = Blueprint('auth', __name__, url_prefix='/auth')
```

This creates a [`Blueprint`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Blueprint "flask.Blueprint") named `'auth'`. Like the application object, the blueprint needs to know where it’s defined, so `__name__` is passed as the second argument. The `url_prefix` will be prepended to all the URLs associated with the blueprint.

Import and register the blueprint from the factory using [`app.register_blueprint()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.register_blueprint "flask.Flask.register_blueprint"). Place the new code at the end of the factory function before returning the app.

`flaskr/__init__.py`

```python
def create_app():
    app = ...
    # existing code omitted

    from . import auth
    app.register_blueprint(auth.bp)

    return app
```

The authentication blueprint will have views to register new users and to log in and log out.

#### The First View: Register

When the user visits the `/auth/register` URL, the `register` view will return [HTML](https://developer.mozilla.org/docs/Web/HTML) with a form for them to fill out. When they submit the form, it will validate their input and either show the form again with an error message or create the new user and go to the login page.

For now you will just write the view code. On the next page, you’ll write templates to generate the HTML form.

`flaskr/auth.py`[](https://flask.palletsprojects.com/en/3.0.x/tutorial/views/#id3 "Link to this code")

```python
@bp.route('/register', methods=('GET', 'POST'))
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        db = get_db()
        error = None

        if not username:
            error = 'Username is required.'
        elif not password:
            error = 'Password is required.'

        if error is None:
            try:
                db.execute(
                    "INSERT INTO user (username, password) VALUES (?, ?)",
                    (username, generate_password_hash(password)),
                )
                db.commit()
            except db.IntegrityError:
                error = f"User {username} is already registered."
            else:
                return redirect(url_for("auth.login"))

        flash(error)

    return render_template('auth/register.html')
```

Here’s what the `register` view function is doing:

1. [`@bp.route`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Blueprint.route "flask.Blueprint.route") associates the URL `/register` with the `register` view function. When Flask receives a request to `/auth/register`, it will call the `register` view and use the return value as the response.
2. If the user submitted the form, [`request.method`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.method "flask.Request.method") will be `'POST'`. In this case, start validating the input.
3. [`request.form`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Request.form "flask.Request.form") is a special type of [`dict`](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.12)") mapping submitted form keys and values. The user will input their `username` and `password`.
4. Validate that `username` and `password` are not empty.
5. If validation succeeds, insert the new user data into the database.
    - [`db.execute`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection.execute "(in Python v3.12)") takes a SQL query with `?` placeholders for any user input, and a tuple of values to replace the placeholders with. The database library will take care of escaping the values so you are not vulnerable to a _SQL injection attack_.
    - For security, passwords should never be stored in the database directly. Instead, [`generate_password_hash()`](https://werkzeug.palletsprojects.com/en/3.0.x/utils/#werkzeug.security.generate_password_hash "(in Werkzeug v3.0.x)") is used to securely hash the password, and that hash is stored. Since this query modifies data, [`db.commit()`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection.commit "(in Python v3.12)") needs to be called afterwards to save the changes.
    - An [`sqlite3.IntegrityError`](https://docs.python.org/3/library/sqlite3.html#sqlite3.IntegrityError "(in Python v3.12)") will occur if the username already exists, which should be shown to the user as another validation error.
6. After storing the user, they are redirected to the login page. [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") generates the URL for the login view based on its name. This is preferable to writing the URL directly as it allows you to change the URL later without changing all code that links to it. [`redirect()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.redirect "flask.redirect") generates a redirect response to the generated URL.
7. If validation fails, the error is shown to the user. [`flash()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.flash "flask.flash") stores messages that can be retrieved when rendering the template.
8. When the user initially navigates to `auth/register`, or there was a validation error, an HTML page with the registration form should be shown. [`render_template()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.render_template "flask.render_template") will render a template containing the HTML, which you’ll write in the next step of the tutorial.

#### Login

This view follows the same pattern as the `register` view above.

`flaskr/auth.py`

```python
@bp.route('/login', methods=('GET', 'POST'))
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        db = get_db()
        error = None
        user = db.execute(
            'SELECT * FROM user WHERE username = ?', (username,)
        ).fetchone()

        if user is None:
            error = 'Incorrect username.'
        elif not check_password_hash(user['password'], password):
            error = 'Incorrect password.'

        if error is None:
            session.clear()
            session['user_id'] = user['id']
            return redirect(url_for('index'))

        flash(error)

    return render_template('auth/login.html')
```

There are a few differences from the `register` view:

1. The user is queried first and stored in a variable for later use.
    [`fetchone()`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchone "(in Python v3.12)") returns one row from the query. If the query returned no results, it returns `None`. Later, [`fetchall()`](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchall "(in Python v3.12)") will be used, which returns a list of all results.
2. [`check_password_hash()`](https://werkzeug.palletsprojects.com/en/3.0.x/utils/#werkzeug.security.check_password_hash "(in Werkzeug v3.0.x)") hashes the submitted password in the same way as the stored hash and securely compares them. If they match, the password is valid.
3. [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") is a [`dict`](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.12)") that stores data across requests. When validation succeeds, the user’s `id` is stored in a new session. The data is stored in a _cookie_ that is sent to the browser, and the browser then sends it back with subsequent requests. Flask securely _signs_ the data so that it can’t be tampered with.

Now that the user’s `id` is stored in the [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session"), it will be available on subsequent requests. At the beginning of each request, if a user is logged in their information should be loaded and made available to other views.

`flaskr/auth.py`

```python
@bp.before_app_request
def load_logged_in_user():
    user_id = session.get('user_id')

    if user_id is None:
        g.user = None
    else:
        g.user = get_db().execute(
            'SELECT * FROM user WHERE id = ?', (user_id,)
        ).fetchone()
```

[`bp.before_app_request()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Blueprint.before_app_request "flask.Blueprint.before_app_request") registers a function that runs before the view function, no matter what URL is requested. `load_logged_in_user` checks if a user id is stored in the [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") and gets that user’s data from the database, storing it on [`g.user`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.g "flask.g"), which lasts for the length of the request. If there is no user id, or if the id doesn’t exist, `g.user` will be `None`.

#### Logout

To log out, you need to remove the user id from the [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session"). Then `load_logged_in_user` won’t load a user on subsequent requests.

`flaskr/auth.py`

```python
@bp.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('index'))
```

#### Require Authentication in Other Views

Creating, editing, and deleting blog posts will require a user to be logged in. A _decorator_ can be used to check this for each view it’s applied to.

`flaskr/auth.py`

```python
def login_required(view):
    @functools.wraps(view)
    def wrapped_view(**kwargs):
        if g.user is None:
            return redirect(url_for('auth.login'))

        return view(**kwargs)

    return wrapped_view
```

This decorator returns a new view function that wraps the original view it’s applied to. The new function checks if a user is loaded and redirects to the login page otherwise. If a user is loaded the original view is called and continues normally. You’ll use this decorator when writing the blog views.

#### Endpoints and URLs

The [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") function generates the URL to a view based on a name and arguments. The name associated with a view is also called the _endpoint_, and by default it’s the same as the name of the view function.

For example, the `hello()` view that was added to the app factory earlier in the tutorial has the name `'hello'` and can be linked to with `url_for('hello')`. If it took an argument, which you’ll see later, it would be linked to using `url_for('hello', who='World')`.

When using a blueprint, the name of the blueprint is prepended to the name of the function, so the endpoint for the `login` function you wrote above is `'auth.login'` because you added it to the `'auth'` blueprint.

### Template

You’ve written the authentication views for your application, but if you’re running the server and try to go to any of the URLs, you’ll see a `TemplateNotFound` error. That’s because the views are calling [`render_template()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.render_template "flask.render_template"), but you haven’t written the templates yet. The template files will be stored in the `templates` directory inside the `flaskr` package.

Templates are files that contain static data as well as placeholders for dynamic data. A template is rendered with specific data to produce a final document. Flask uses the [Jinja](https://jinja.palletsprojects.com/templates/) template library to render templates.

In your application, you will use templates to render [HTML](https://developer.mozilla.org/docs/Web/HTML) which will display in the user’s browser. In Flask, Jinja is configured to _autoescape_ any data that is rendered in HTML templates. This means that it’s safe to render user input; any characters they’ve entered that could mess with the HTML, such as `<` and `>` will be _escaped_ with _safe_ values that look the same in the browser but don’t cause unwanted effects.

Jinja looks and behaves mostly like Python. Special delimiters are used to distinguish Jinja syntax from the static data in the template. Anything between `{{` and `}}` is an expression that will be output to the final document. `{%` and `%}` denotes a control flow statement like `if` and `for`. Unlike Python, blocks are denoted by start and end tags rather than indentation since static text within a block could change indentation.

#### The Base Layout

Each page in the application will have the same basic layout around a different body. Instead of writing the entire HTML structure in each template, each template will _extend_ a base template and override specific sections.

`flaskr/templates/base.html`

```jinja2
<!doctype html>
<title>{% block title %}{% endblock %} - Flaskr</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
<nav>
  <h1>Flaskr</h1>
  <ul>
    {% if g.user %}
      <li><span>{{ g.user['username'] }}</span>
      <li><a href="{{ url_for('auth.logout') }}">Log Out</a>
    {% else %}
      <li><a href="{{ url_for('auth.register') }}">Register</a>
      <li><a href="{{ url_for('auth.login') }}">Log In</a>
    {% endif %}
  </ul>
</nav>
<section class="content">
  <header>
    {% block header %}{% endblock %}
  </header>
  {% for message in get_flashed_messages() %}
    <div class="flash">{{ message }}</div>
  {% endfor %}
  {% block content %}{% endblock %}
</section>
```

[`g`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.g "flask.g") is automatically available in templates. Based on if `g.user` is set (from `load_logged_in_user`), either the username and a log out link are displayed, or links to register and log in are displayed. [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") is also automatically available, and is used to generate URLs to views instead of writing them out manually.

After the page title, and before the content, the template loops over each message returned by [`get_flashed_messages()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.get_flashed_messages "flask.get_flashed_messages"). You used [`flash()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.flash "flask.flash") in the views to show error messages, and this is the code that will display them.

There are three blocks defined here that will be overridden in the other templates:

1. `{% block title %}` will change the title displayed in the browser’s tab and window title.
2. `{% block header %}` is similar to `title` but will change the title displayed on the page.
3. `{% block content %}` is where the content of each page goes, such as the login form or a blog post.

The base template is directly in the `templates` directory. To keep the others organized, the templates for a blueprint will be placed in a directory with the same name as the blueprint.

#### Register

`flaskr/templates/auth/register.html`

```jinja2
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Register{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="username">Username</label>
    <input name="username" id="username" required>
    <label for="password">Password</label>
    <input type="password" name="password" id="password" required>
    <input type="submit" value="Register">
  </form>
{% endblock %}
```

`{% extends 'base.html' %}` tells Jinja that this template should replace the blocks from the base template. All the rendered content must appear inside `{% block %}` tags that override blocks from the base template.

A useful pattern used here is to place `{% block title %}` inside `{% block header %}`. This will set the title block and then output the value of it into the header block, so that both the window and page share the same title without writing it twice.

The `input` tags are using the `required` attribute here. This tells the browser not to submit the form until those fields are filled in. If the user is using an older browser that doesn’t support that attribute, or if they are using something besides a browser to make requests, you still want to validate the data in the Flask view. It’s important to always fully validate the data on the server, even if the client does some validation as well.

#### Log In

This is identical to the register template except for the title and submit button.

`flaskr/templates/auth/login.html`

```jinja2
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Log In{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="username">Username</label>
    <input name="username" id="username" required>
    <label for="password">Password</label>
    <input type="password" name="password" id="password" required>
    <input type="submit" value="Log In">
  </form>
{% endblock %}
```

#### Register A User

Now that the authentication templates are written, you can register a user. Make sure the server is still running (`flask run` if it’s not), then go to [http://127.0.0.1:5000/auth/register](http://127.0.0.1:5000/auth/register).

Try clicking the “Register” button without filling out the form and see that the browser shows an error message. Try removing the `required` attributes from the `register.html` template and click “Register” again. Instead of the browser showing an error, the page will reload and the error from [`flash()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.flash "flask.flash") in the view will be shown.

Fill out a username and password and you’ll be redirected to the login page. Try entering an incorrect username, or the correct username and incorrect password. If you log in you’ll get an error because there’s no `index` view to redirect to yet.

### Static Files

The authentication views and templates work, but they look very plain right now. Some [CSS](https://developer.mozilla.org/docs/Web/CSS) can be added to add style to the HTML layout you constructed. The style won’t change, so it’s a _static_ file rather than a template.

Flask automatically adds a `static` view that takes a path relative to the `flaskr/static` directory and serves it. The `base.html` template already has a link to the `style.css` file:

```jinja2
{{ url_for('static', filename='style.css') }}
```

Besides CSS, other types of static files might be files with JavaScript functions, or a logo image. They are all placed under the `flaskr/static` directory and referenced with `url_for('static', filename='...')`.

This tutorial isn’t focused on how to write CSS, so you can just copy the following into the `flaskr/static/style.css` file:

`flaskr/static/style.css`

```css
html { font-family: sans-serif; background: #eee; padding: 1rem; }
body { max-width: 960px; margin: 0 auto; background: white; }
h1 { font-family: serif; color: #377ba8; margin: 1rem 0; }
a { color: #377ba8; }
hr { border: none; border-top: 1px solid lightgray; }
nav { background: lightgray; display: flex; align-items: center; padding: 0 0.5rem; }
nav h1 { flex: auto; margin: 0; }
nav h1 a { text-decoration: none; padding: 0.25rem 0.5rem; }
nav ul  { display: flex; list-style: none; margin: 0; padding: 0; }
nav ul li a, nav ul li span, header .action { display: block; padding: 0.5rem; }
.content { padding: 0 1rem 1rem; }
.content > header { border-bottom: 1px solid lightgray; display: flex; align-items: flex-end; }
.content > header h1 { flex: auto; margin: 1rem 0 0.25rem 0; }
.flash { margin: 1em 0; padding: 1em; background: #cae6f6; border: 1px solid #377ba8; }
.post > header { display: flex; align-items: flex-end; font-size: 0.85em; }
.post > header > div:first-of-type { flex: auto; }
.post > header h1 { font-size: 1.5em; margin-bottom: 0; }
.post .about { color: slategray; font-style: italic; }
.post .body { white-space: pre-line; }
.content:last-child { margin-bottom: 0; }
.content form { margin: 1em 0; display: flex; flex-direction: column; }
.content label { font-weight: bold; margin-bottom: 0.5em; }
.content input, .content textarea { margin-bottom: 1em; }
.content textarea { min-height: 12em; resize: vertical; }
input.danger { color: #cc2f2e; }
input[type=submit] { align-self: start; min-width: 10em; }
```

You can find a less compact version of `style.css` in the [example code](https://github.com/pallets/flask/tree/3.0.3/examples/tutorial/flaskr/static/style.css).

Go to [http://127.0.0.1:5000/auth/login](http://127.0.0.1:5000/auth/login) and the page should look like the screenshot below.

![screenshot of login page](https://flask.palletsprojects.com/en/3.0.x/_images/flaskr_login.png)

You can read more about CSS from [Mozilla’s documentation](https://developer.mozilla.org/docs/Web/CSS). If you change a static file, refresh the browser page. If the change doesn’t show up, try clearing your browser’s cache.

### Blog Blueprint

You’ll use the same techniques you learned about when writing the authentication blueprint to write the blog blueprint. The blog should list all posts, allow logged in users to create posts, and allow the author of a post to edit or delete it.

As you implement each view, keep the development server running. As you save your changes, try going to the URL in your browser and testing them out.

#### The Blueprint

Define the blueprint and register it in the application factory.

`flaskr/blog.py`

```python
from flask import (
    Blueprint, flash, g, redirect, render_template, request, url_for
)
from werkzeug.exceptions import abort

from flaskr.auth import login_required
from flaskr.db import get_db

bp = Blueprint('blog', __name__)
```

Import and register the blueprint from the factory using [`app.register_blueprint()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.register_blueprint "flask.Flask.register_blueprint"). Place the new code at the end of the factory function before returning the app.

`flaskr/__init__.py`

```python
def create_app():
    app = ...
    # existing code omitted

    from . import blog
    app.register_blueprint(blog.bp)
    app.add_url_rule('/', endpoint='index')

    return app
```
Unlike the auth blueprint, the blog blueprint does not have a `url_prefix`. So the `index` view will be at `/`, the `create` view at `/create`, and so on. The blog is the main feature of Flaskr, so it makes sense that the blog index will be the main index.

However, the endpoint for the `index` view defined below will be `blog.index`. Some of the authentication views referred to a plain `index` endpoint. [`app.add_url_rule()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.add_url_rule "flask.Flask.add_url_rule") associates the endpoint name `'index'` with the `/` url so that `url_for('index')` or `url_for('blog.index')` will both work, generating the same `/` URL either way.

In another application you might give the blog blueprint a `url_prefix` and define a separate `index` view in the application factory, similar to the `hello` view. Then the `index` and `blog.index` endpoints and URLs would be different.

#### Index

The index will show all of the posts, most recent first. A `JOIN` is used so that the author information from the `user` table is available in the result.

`flaskr/blog.py`

```python
@bp.route('/')
def index():
    db = get_db()
    posts = db.execute(
        'SELECT p.id, title, body, created, author_id, username'
        ' FROM post p JOIN user u ON p.author_id = u.id'
        ' ORDER BY created DESC'
    ).fetchall()
    return render_template('blog/index.html', posts=posts)
```

`flaskr/templates/blog/index.html`

```jinja2
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Posts{% endblock %}</h1>
  {% if g.user %}
    <a class="action" href="{{ url_for('blog.create') }}">New</a>
  {% endif %}
{% endblock %}

{% block content %}
  {% for post in posts %}
    <article class="post">
      <header>
        <div>
          <h1>{{ post['title'] }}</h1>
          <div class="about">by {{ post['username'] }} on {{ post['created'].strftime('%Y-%m-%d') }}</div>
        </div>
        {% if g.user['id'] == post['author_id'] %}
          <a class="action" href="{{ url_for('blog.update', id=post['id']) }}">Edit</a>
        {% endif %}
      </header>
      <p class="body">{{ post['body'] }}</p>
    </article>
    {% if not loop.last %}
      <hr>
    {% endif %}
  {% endfor %}
{% endblock %}
```

When a user is logged in, the `header` block adds a link to the `create` view. When the user is the author of a post, they’ll see an “Edit” link to the `update` view for that post. `loop.last` is a special variable available inside [Jinja for loops](https://jinja.palletsprojects.com/templates/#for). It’s used to display a line after each post except the last one, to visually separate them.

#### Create

The `create` view works the same as the auth `register` view. Either the form is displayed, or the posted data is validated and the post is added to the database or an error is shown.

The `login_required` decorator you wrote earlier is used on the blog views. A user must be logged in to visit these views, otherwise they will be redirected to the login page.

`flaskr/blog.py`

```python
@bp.route('/create', methods=('GET', 'POST'))
@login_required
def create():
    if request.method == 'POST':
        title = request.form['title']
        body = request.form['body']
        error = None

        if not title:
            error = 'Title is required.'

        if error is not None:
            flash(error)
        else:
            db = get_db()
            db.execute(
                'INSERT INTO post (title, body, author_id)'
                ' VALUES (?, ?, ?)',
                (title, body, g.user['id'])
            )
            db.commit()
            return redirect(url_for('blog.index'))

    return render_template('blog/create.html')
```

`flaskr/templates/blog/create.html`

```jinja2
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}New Post{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="title">Title</label>
    <input name="title" id="title" value="{{ request.form['title'] }}" required>
    <label for="body">Body</label>
    <textarea name="body" id="body">{{ request.form['body'] }}</textarea>
    <input type="submit" value="Save">
  </form>
{% endblock %}
```

#### Update

Both the `update` and `delete` views will need to fetch a `post` by `id` and check if the author matches the logged in user. To avoid duplicating code, you can write a function to get the `post` and call it from each view.

`flaskr/blog.py`

```python
def get_post(id, check_author=True):
    post = get_db().execute(
        'SELECT p.id, title, body, created, author_id, username'
        ' FROM post p JOIN user u ON p.author_id = u.id'
        ' WHERE p.id = ?',
        (id,)
    ).fetchone()

    if post is None:
        abort(404, f"Post id {id} doesn't exist.")

    if check_author and post['author_id'] != g.user['id']:
        abort(403)

    return post
```

[`abort()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.abort "flask.abort") will raise a special exception that returns an HTTP status code. It takes an optional message to show with the error, otherwise a default message is used. `404` means “Not Found”, and `403` means “Forbidden”. (`401` means “Unauthorized”, but you redirect to the login page instead of returning that status.)

The `check_author` argument is defined so that the function can be used to get a `post` without checking the author. This would be useful if you wrote a view to show an individual post on a page, where the user doesn’t matter because they’re not modifying the post.

`flaskr/blog.py`

```python
@bp.route('/<int:id>/update', methods=('GET', 'POST'))
@login_required
def update(id):
    post = get_post(id)

    if request.method == 'POST':
        title = request.form['title']
        body = request.form['body']
        error = None

        if not title:
            error = 'Title is required.'

        if error is not None:
            flash(error)
        else:
            db = get_db()
            db.execute(
                'UPDATE post SET title = ?, body = ?'
                ' WHERE id = ?',
                (title, body, id)
            )
            db.commit()
            return redirect(url_for('blog.index'))

    return render_template('blog/update.html', post=post)
```

Unlike the views you’ve written so far, the `update` function takes an argument, `id`. That corresponds to the `<int:id>` in the route. A real URL will look like `/1/update`. Flask will capture the `1`, ensure it’s an [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.12)"), and pass it as the `id` argument. If you don’t specify `int:` and instead do `<id>`, it will be a string. To generate a URL to the update page, [`url_for()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.url_for "flask.url_for") needs to be passed the `id` so it knows what to fill in: `url_for('blog.update', id=post['id'])`. This is also in the `index.html` file above.

The `create` and `update` views look very similar. The main difference is that the `update` view uses a `post` object and an `UPDATE` query instead of an `INSERT`. With some clever refactoring, you could use one view and template for both actions, but for the tutorial it’s clearer to keep them separate.

`flaskr/templates/blog/update.html`

```python
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Edit "{{ post['title'] }}"{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="title">Title</label>
    <input name="title" id="title"
      value="{{ request.form['title'] or post['title'] }}" required>
    <label for="body">Body</label>
    <textarea name="body" id="body">{{ request.form['body'] or post['body'] }}</textarea>
    <input type="submit" value="Save">
  </form>
  <hr>
  <form action="{{ url_for('blog.delete', id=post['id']) }}" method="post">
    <input class="danger" type="submit" value="Delete" onclick="return confirm('Are you sure?');">
  </form>
{% endblock %}
```
This template has two forms. The first posts the edited data to the current page (`/<id>/update`). The other form contains only a button and specifies an `action` attribute that posts to the delete view instead. The button uses some JavaScript to show a confirmation dialog before submitting.

The pattern `{{ request.form['title'] or post['title'] }}` is used to choose what data appears in the form. When the form hasn’t been submitted, the original `post` data appears, but if invalid form data was posted you want to display that so the user can fix the error, so `request.form` is used instead. [`request`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.request "flask.request") is another variable that’s automatically available in templates.

#### Delete

The delete view doesn’t have its own template, the delete button is part of `update.html` and posts to the `/<id>/delete` URL. Since there is no template, it will only handle the `POST` method and then redirect to the `index` view.

`flaskr/blog.py`

```python
@bp.route('/<int:id>/delete', methods=('POST',))
@login_required
def delete(id):
    get_post(id)
    db = get_db()
    db.execute('DELETE FROM post WHERE id = ?', (id,))
    db.commit()
    return redirect(url_for('blog.index'))
```

Congratulations, you’ve now finished writing your application! Take some time to try out everything in the browser. However, there’s still more to do before the project is complete.

### Make the Project Installable

Making your project installable means that you can build a _wheel_ file and install that in another environment, just like you installed Flask in your project’s environment. This makes deploying your project the same as installing any other library, so you’re using all the standard Python tools to manage everything.

Installing also comes with other benefits that might not be obvious from the tutorial or as a new Python user, including:

- Currently, Python and Flask understand how to use the `flaskr` package only because you’re running from your project’s directory. Installing means you can import it no matter where you run from.
- You can manage your project’s dependencies just like other packages do, so `pip install yourproject.whl` installs them.
- Test tools can isolate your test environment from your development environment.

>Note
>
>This is being introduced late in the tutorial, but in your future projects you should always start with this.

#### Describe the Project

The `pyproject.toml` file describes your project and how to build it.

`pyproject.toml`

```yaml
[project]
name = "flaskr"
version = "1.0.0"
description = "The basic blog app built in the Flask tutorial."
dependencies = [
    "flask",
]

[build-system]
requires = ["flit_core<4"]
build-backend = "flit_core.buildapi"
```

See the official [Packaging tutorial](https://packaging.python.org/tutorials/packaging-projects/) for more explanation of the files and options used.

#### Install the Project

Use `pip` to install your project in the virtual environment.

```sh
$ pip install -e .
```

This tells pip to find `pyproject.toml` in the current directory and install the project in _editable_ or _development_ mode. Editable mode means that as you make changes to your local code, you’ll only need to re-install if you change the metadata about the project, such as its dependencies.

You can observe that the project is now installed with `pip list`.

```sh
$ pip list

Package        Version   Location
-------------- --------- ----------------------------------
click          6.7
Flask          1.0
flaskr         1.0.0     /home/user/Projects/flask-tutorial
itsdangerous   0.24
Jinja2         2.10
MarkupSafe     1.0
pip            9.0.3
Werkzeug       0.14.1
```

Nothing changes from how you’ve been running your project so far. `--app` is still set to `flaskr` and `flask run` still runs the application, but you can call it from anywhere, not just the `flask-tutorial` directory.

### Test Coverage

Writing unit tests for your application lets you check that the code you wrote works the way you expect. Flask provides a test client that simulates requests to the application and returns the response data.

You should test as much of your code as possible. Code in functions only runs when the function is called, and code in branches, such as `if` blocks, only runs when the condition is met. You want to make sure that each function is tested with data that covers each branch.

The closer you get to 100% coverage, the more comfortable you can be that making a change won’t unexpectedly change other behavior. However, 100% coverage doesn’t guarantee that your application doesn’t have bugs. In particular, it doesn’t test how the user interacts with the application in the browser. Despite this, test coverage is an important tool to use during development.

>Note
>
>This is being introduced late in the tutorial, but in your future projects you should test as you develop.

You’ll use [pytest](https://pytest.readthedocs.io/) and [coverage](https://coverage.readthedocs.io/) to test and measure your code. Install them both:

```sh
$ pip install pytest coverage
```

#### Setup and Fixtures

The test code is located in the `tests` directory. This directory is _next to_ the `flaskr` package, not inside it. The `tests/conftest.py` file contains setup functions called _fixtures_ that each test will use. Tests are in Python modules that start with `test_`, and each test function in those modules also starts with `test_`.

Each test will create a new temporary database file and populate some data that will be used in the tests. Write a SQL file to insert that data.

`tests/data.sql`

```sql
INSERT INTO user (username, password)
VALUES
  ('test', 'pbkdf2:sha256:50000$TCI4GzcX$0de171a4f4dac32e3364c7ddc7c14f3e2fa61f2d17574483f7ffbb431b4acb2f'),
  ('other', 'pbkdf2:sha256:50000$kJPKsz6N$d2d4784f1b030a9761f5ccaeeaca413f27f2ecb76d6168407af962ddce849f79');

INSERT INTO post (title, body, author_id, created)
VALUES
  ('test title', 'test' || x'0a' || 'body', 1, '2018-01-01 00:00:00');
```

The `app` fixture will call the factory and pass `test_config` to configure the application and database for testing instead of using your local development configuration.

`tests/conftest.py`

```python
import os
import tempfile

import pytest
from flaskr import create_app
from flaskr.db import get_db, init_db

with open(os.path.join(os.path.dirname(__file__), 'data.sql'), 'rb') as f:
    _data_sql = f.read().decode('utf8')

@pytest.fixture
def app():
    db_fd, db_path = tempfile.mkstemp()

    app = create_app({
        'TESTING': True,
        'DATABASE': db_path,
    })

    with app.app_context():
        init_db()
        get_db().executescript(_data_sql)

    yield app

    os.close(db_fd)
    os.unlink(db_path)

@pytest.fixture
def client(app):
    return app.test_client()

@pytest.fixture
def runner(app):
    return app.test_cli_runner()
```

[`tempfile.mkstemp()`](https://docs.python.org/3/library/tempfile.html#tempfile.mkstemp "(in Python v3.12)") creates and opens a temporary file, returning the file descriptor and the path to it. The `DATABASE` path is overridden so it points to this temporary path instead of the instance folder. After setting the path, the database tables are created and the test data is inserted. After the test is over, the temporary file is closed and removed.

[`TESTING`](https://flask.palletsprojects.com/en/3.0.x/config/#TESTING "TESTING") tells Flask that the app is in test mode. Flask changes some internal behavior so it’s easier to test, and other extensions can also use the flag to make testing them easier.

The `client` fixture calls [`app.test_client()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.test_client "flask.Flask.test_client") with the application object created by the `app` fixture. Tests will use the client to make requests to the application without running the server.

The `runner` fixture is similar to `client`. [`app.test_cli_runner()`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Flask.test_cli_runner "flask.Flask.test_cli_runner") creates a runner that can call the Click commands registered with the application.

Pytest uses fixtures by matching their function names with the names of arguments in the test functions. For example, the `test_hello` function you’ll write next takes a `client` argument. Pytest matches that with the `client` fixture function, calls it, and passes the returned value to the test function.

#### Factory

There’s not much to test about the factory itself. Most of the code will be executed for each test already, so if something fails the other tests will notice.

The only behavior that can change is passing test config. If config is not passed, there should be some default configuration, otherwise the configuration should be overridden.

`tests/test_factory.py`

```python
from flaskr import create_app

def test_config():
    assert not create_app().testing
    assert create_app({'TESTING': True}).testing

def test_hello(client):
    response = client.get('/hello')
    assert response.data == b'Hello, World!'
```

You added the `hello` route as an example when writing the factory at the beginning of the tutorial. It returns “Hello, World!”, so the test checks that the response data matches.

#### Database

Within an application context, `get_db` should return the same connection each time it’s called. After the context, the connection should be closed.

`tests/test_db.py`

```python
import sqlite3

import pytest
from flaskr.db import get_db

def test_get_close_db(app):
    with app.app_context():
        db = get_db()
        assert db is get_db()

    with pytest.raises(sqlite3.ProgrammingError) as e:
        db.execute('SELECT 1')

    assert 'closed' in str(e.value)
```

The `init-db` command should call the `init_db` function and output a message.

`tests/test_db.py`

```python
def test_init_db_command(runner, monkeypatch):
    class Recorder(object):
        called = False

    def fake_init_db():
        Recorder.called = True

    monkeypatch.setattr('flaskr.db.init_db', fake_init_db)
    result = runner.invoke(args=['init-db'])
    assert 'Initialized' in result.output
    assert Recorder.called
```

This test uses Pytest’s `monkeypatch` fixture to replace the `init_db` function with one that records that it’s been called. The `runner` fixture you wrote above is used to call the `init-db` command by name.

#### Authentication

For most of the views, a user needs to be logged in. The easiest way to do this in tests is to make a `POST` request to the `login` view with the client. Rather than writing that out every time, you can write a class with methods to do that, and use a fixture to pass it the client for each test.

`tests/conftest.py`

```python
class AuthActions(object):
    def __init__(self, client):
        self._client = client

    def login(self, username='test', password='test'):
        return self._client.post(
            '/auth/login',
            data={'username': username, 'password': password}
        )

    def logout(self):
        return self._client.get('/auth/logout')

@pytest.fixture
def auth(client):
    return AuthActions(client)
```

With the `auth` fixture, you can call `auth.login()` in a test to log in as the `test` user, which was inserted as part of the test data in the `app` fixture.

The `register` view should render successfully on `GET`. On `POST` with valid form data, it should redirect to the login URL and the user’s data should be in the database. Invalid data should display error messages.

`tests/test_auth.py`

```python
import pytest
from flask import g, session
from flaskr.db import get_db

def test_register(client, app):
    assert client.get('/auth/register').status_code == 200
    response = client.post(
        '/auth/register', data={'username': 'a', 'password': 'a'}
    )
    assert response.headers["Location"] == "/auth/login"

    with app.app_context():
        assert get_db().execute(
            "SELECT * FROM user WHERE username = 'a'",
        ).fetchone() is not None

@pytest.mark.parametrize(('username', 'password', 'message'), (
    ('', '', b'Username is required.'),
    ('a', '', b'Password is required.'),
    ('test', 'test', b'already registered'),
))
def test_register_validate_input(client, username, password, message):
    response = client.post(
        '/auth/register',
        data={'username': username, 'password': password}
    )
    assert message in response.data
```
[`client.get()`](https://werkzeug.palletsprojects.com/en/3.0.x/test/#werkzeug.test.Client.get "(in Werkzeug v3.0.x)") makes a `GET` request and returns the [`Response`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Response "flask.Response") object returned by Flask. Similarly, [`client.post()`](https://werkzeug.palletsprojects.com/en/3.0.x/test/#werkzeug.test.Client.post "(in Werkzeug v3.0.x)") makes a `POST` request, converting the `data` dict into form data.

To test that the page renders successfully, a simple request is made and checked for a `200 OK` [`status_code`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Response.status_code "flask.Response.status_code"). If rendering failed, Flask would return a `500 Internal Server Error` code.

`headers` will have a `Location` header with the login URL when the register view redirects to the login view.

[`data`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.Response.data "flask.Response.data") contains the body of the response as bytes. If you expect a certain value to render on the page, check that it’s in `data`. Bytes must be compared to bytes. If you want to compare text, use [`get_data(as_text=True)`](https://werkzeug.palletsprojects.com/en/3.0.x/wrappers/#werkzeug.wrappers.Response.get_data "(in Werkzeug v3.0.x)") instead.

`pytest.mark.parametrize` tells Pytest to run the same test function with different arguments. You use it here to test different invalid input and error messages without writing the same code three times.

The tests for the `login` view are very similar to those for `register`. Rather than testing the data in the database, [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") should have `user_id` set after logging in.

`tests/test_auth.py`

```python
def test_login(client, auth):
    assert client.get('/auth/login').status_code == 200
    response = auth.login()
    assert response.headers["Location"] == "/"

    with client:
        client.get('/')
        assert session['user_id'] == 1
        assert g.user['username'] == 'test'

@pytest.mark.parametrize(('username', 'password', 'message'), (
    ('a', 'test', b'Incorrect username.'),
    ('test', 'a', b'Incorrect password.'),
))
def test_login_validate_input(auth, username, password, message):
    response = auth.login(username, password)
    assert message in response.data
```

Using `client` in a `with` block allows accessing context variables such as [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") after the response is returned. Normally, accessing `session` outside of a request would raise an error.

Testing `logout` is the opposite of `login`. [`session`](https://flask.palletsprojects.com/en/3.0.x/api/#flask.session "flask.session") should not contain `user_id` after logging out.

`tests/test_auth.py`

```python
def test_logout(client, auth):
    auth.login()

    with client:
        auth.logout()
        assert 'user_id' not in session
```
#### Blog

All the blog views use the `auth` fixture you wrote earlier. Call `auth.login()` and subsequent requests from the client will be logged in as the `test` user.

The `index` view should display information about the post that was added with the test data. When logged in as the author, there should be a link to edit the post.

You can also test some more authentication behavior while testing the `index` view. When not logged in, each page shows links to log in or register. When logged in, there’s a link to log out.

`tests/test_blog.py`

```python
import pytest
from flaskr.db import get_db

def test_index(client, auth):
    response = client.get('/')
    assert b"Log In" in response.data
    assert b"Register" in response.data

    auth.login()
    response = client.get('/')
    assert b'Log Out' in response.data
    assert b'test title' in response.data
    assert b'by test on 2018-01-01' in response.data
    assert b'test\nbody' in response.data
    assert b'href="/1/update"' in response.data
```

A user must be logged in to access the `create`, `update`, and `delete` views. The logged in user must be the author of the post to access `update` and `delete`, otherwise a `403 Forbidden` status is returned. If a `post` with the given `id` doesn’t exist, `update` and `delete` should return `404 Not Found`.

`tests/test_blog.py`

```python
@pytest.mark.parametrize('path', (
    '/create',
    '/1/update',
    '/1/delete',
))
def test_login_required(client, path):
    response = client.post(path)
    assert response.headers["Location"] == "/auth/login"

def test_author_required(app, client, auth):
    # change the post author to another user
    with app.app_context():
        db = get_db()
        db.execute('UPDATE post SET author_id = 2 WHERE id = 1')
        db.commit()

    auth.login()
    # current user can't modify other user's post
    assert client.post('/1/update').status_code == 403
    assert client.post('/1/delete').status_code == 403
    # current user doesn't see edit link
    assert b'href="/1/update"' not in client.get('/').data

@pytest.mark.parametrize('path', (
    '/2/update',
    '/2/delete',
))
def test_exists_required(client, auth, path):
    auth.login()
    assert client.post(path).status_code == 404
```

The `create` and `update` views should render and return a `200 OK` status for a `GET` request. When valid data is sent in a `POST` request, `create` should insert the new post data into the database, and `update` should modify the existing data. Both pages should show an error message on invalid data.

`tests/test_blog.py`

```python
def test_create(client, auth, app):
    auth.login()
    assert client.get('/create').status_code == 200
    client.post('/create', data={'title': 'created', 'body': ''})

    with app.app_context():
        db = get_db()
        count = db.execute('SELECT COUNT(id) FROM post').fetchone()[0]
        assert count == 2

def test_update(client, auth, app):
    auth.login()
    assert client.get('/1/update').status_code == 200
    client.post('/1/update', data={'title': 'updated', 'body': ''})

    with app.app_context():
        db = get_db()
        post = db.execute('SELECT * FROM post WHERE id = 1').fetchone()
        assert post['title'] == 'updated'

@pytest.mark.parametrize('path', (
    '/create',
    '/1/update',
))
def test_create_update_validate(client, auth, path):
    auth.login()
    response = client.post(path, data={'title': '', 'body': ''})
    assert b'Title is required.' in response.data
```

The `delete` view should redirect to the index URL and the post should no longer exist in the database.

`tests/test_blog.py`

```python
def test_delete(client, auth, app):
    auth.login()
    response = client.post('/1/delete')
    assert response.headers["Location"] == "/"

    with app.app_context():
        db = get_db()
        post = db.execute('SELECT * FROM post WHERE id = 1').fetchone()
        assert post is None
```

#### Running the Tests

Some extra configuration, which is not required but makes running tests with coverage less verbose, can be added to the project’s `pyproject.toml` file.

`pyproject.toml`

```yaml
[tool.pytest.ini_options]
testpaths = ["tests"]

[tool.coverage.run]
branch = true
source = ["flaskr"]
```

To run the tests, use the `pytest` command. It will find and run all the test functions you’ve written.

```sh
$ pytest

========================= test session starts ==========================
platform linux -- Python 3.6.4, pytest-3.5.0, py-1.5.3, pluggy-0.6.0
rootdir: /home/user/Projects/flask-tutorial
collected 23 items

tests/test_auth.py ........                                      [ 34%]
tests/test_blog.py ............                                  [ 86%]
tests/test_db.py ..                                              [ 95%]
tests/test_factory.py ..                                         [100%]

====================== 24 passed in 0.64 seconds =======================
```

If any tests fail, pytest will show the error that was raised. You can run `pytest -v` to get a list of each test function rather than dots.

To measure the code coverage of your tests, use the `coverage` command to run pytest instead of running it directly.

```sh
$ coverage run -m pytest
```

You can either view a simple coverage report in the terminal:

```sh
$ coverage report
```

```sh
Name                 Stmts   Miss Branch BrPart  Cover
------------------------------------------------------
flaskr/__init__.py      21      0      2      0   100%
flaskr/auth.py          54      0     22      0   100%
flaskr/blog.py          54      0     16      0   100%
flaskr/db.py            24      0      4      0   100%
------------------------------------------------------
TOTAL                  153      0     44      0   100%

An HTML report allows you to see which lines were covered in each file:

$ coverage html
```

This generates files in the `htmlcov` directory. Open `htmlcov/index.html` in your browser to see the report.

### Deploy to Production

This part of the tutorial assumes you have a server that you want to deploy your application to. It gives an overview of how to create the distribution file and install it, but won’t go into specifics about what server or software to use. You can set up a new environment on your development computer to try out the instructions below, but probably shouldn’t use it for hosting a real public application. See [Deploying to Production](https://flask.palletsprojects.com/en/3.0.x/deploying/) for a list of many different ways to host your application.

#### Build and Install

When you want to deploy your application elsewhere, you build a _wheel_ (`.whl`) file. Install and use the `build` tool to do this.

```sh
$ pip install build
$ python -m build --wheel
```
You can find the file in `dist/flaskr-1.0.0-py3-none-any.whl`. The file name is in the format of {project name}-{version}-{python tag} -{abi tag}-{platform tag}.

Copy this file to another machine, [set up a new virtualenv](https://flask.palletsprojects.com/en/3.0.x/installation/#install-create-env), then install the file with `pip`.

```sh
$ pip install flaskr-1.0.0-py3-none-any.whl
```

Pip will install your project along with its dependencies.

Since this is a different machine, you need to run `init-db` again to create the database in the instance folder.

```sh
$ flask --app flaskr init-db
```

When Flask detects that it’s installed (not in editable mode), it uses a different directory for the instance folder. You can find it at `.venv/var/flaskr-instance` instead.

#### Configure the Secret Key

In the beginning of the tutorial that you gave a default value for [`SECRET_KEY`](https://flask.palletsprojects.com/en/3.0.x/config/#SECRET_KEY "SECRET_KEY"). This should be changed to some random bytes in production. Otherwise, attackers could use the public `'dev'` key to modify the session cookie, or anything else that uses the secret key.

You can use the following command to output a random secret key:

```sh
$ python -c 'import secrets; print(secrets.token_hex())'

'192b9bdd22ab9ed4d12e236c78afcb9a393ec15f71bbf5dc987d54727823bcbf'
```

Create the `config.py` file in the instance folder, which the factory will read from if it exists. Copy the generated value into it.

`.venv/var/flaskr-instance/config.py`

```python
SECRET_KEY = '192b9bdd22ab9ed4d12e236c78afcb9a393ec15f71bbf5dc987d54727823bcbf'
```

You can also set any other necessary configuration here, although `SECRET_KEY` is the only one needed for Flaskr.

#### Run with a Production Server

When running publicly rather than in development, you should not use the built-in development server (`flask run`). The development server is provided by Werkzeug for convenience, but is not designed to be particularly efficient, stable, or secure.

Instead, use a production WSGI server. For example, to use [Waitress](https://docs.pylonsproject.org/projects/waitress/en/stable/), first install it in the virtual environment:

```sh
$ pip install waitress
```

You need to tell Waitress about your application, but it doesn’t use `--app` like `flask run` does. You need to tell it to import and call the application factory to get an application object.

```sh
$ waitress-serve --call 'flaskr:create_app'
```

Serving on http://0.0.0.0:8080

See [Deploying to Production](https://flask.palletsprojects.com/en/3.0.x/deploying/) for a list of many different ways to host your application. Waitress is just an example, chosen for the tutorial because it supports both Windows and Linux. There are many more WSGI servers and deployment options that you may choose for your project.

### Keep Developing!

You’ve learned about quite a few Flask and Python concepts throughout the tutorial. Go back and review the tutorial and compare your code with the steps you took to get there. Compare your project to the [example project](https://github.com/pallets/flask/tree/3.0.3/examples/tutorial), which might look a bit different due to the step-by-step nature of the tutorial.

There’s a lot more to Flask than what you’ve seen so far. Even so, you’re now equipped to start developing your own web applications. Check out the [Quickstart](https://flask.palletsprojects.com/en/3.0.x/quickstart/) for an overview of what Flask can do, then dive into the docs to keep learning. Flask uses [Jinja](https://palletsprojects.com/p/jinja/), [Click](https://palletsprojects.com/p/click/), [Werkzeug](https://palletsprojects.com/p/werkzeug/), and [ItsDangerous](https://palletsprojects.com/p/itsdangerous/) behind the scenes, and they all have their own documentation too. You’ll also be interested in [Extensions](https://flask.palletsprojects.com/en/3.0.x/extensions/) which make tasks like working with the database or validating form data easier and more powerful.

If you want to keep developing your Flaskr project, here are some ideas for what to try next:

- A detail view to show a single post. Click a post’s title to go to its page.
- Like / unlike a post.
- Comments.
- Tags. Clicking a tag shows all the posts with that tag.
- A search box that filters the index page by name.
- Paged display. Only show 5 posts per page.
- Upload an image to go along with a post.
- Format posts using Markdown.
- An RSS feed of new posts.

Have fun and make awesome applications!