# Ajax/flask

## What did i learn

## Web programming

http-server can’t process other types of requests, like form inputs from the user.

A file might be in a folder, like folder/file.html, and that reference is known as a **path**. A path can also be called a **route**, which does not need to refer to an actual file.

To get an input from the user you'll need to use ?key=value like http://www.example.com/route?key=value

# Flask
Template is like a blueprint in the real world in other words plans to make something 
Just like bootstrap is used for CSS and javascript flask is used for python

Flask is also a framework, where the library of code also comes with a set of conventions for how it should be used. For example, like other libraries, Flask includes functions we can use to parse requests individually, but as a framework, also requires our program’s code to be organized in a certain way:
```
app.py
requirements.txt
static/
templates/
```
- <code>app.py</code> will have the Python code for our web server.
- <code>requirements.py</code> includes a list of required libraries for our application. 
- <code>static/</code>is a directory of static files, like images and CSS and JavaScript files.
- <code>templates/</code> is a directory for HTML files that will form our pages.

Other web server frameworks will have a different set of conventions and requirements.


Ex
```
from flask import Flask, render_template, request

app = Flask(__name__)

# hey Python, define a route for /, the default page on my website application

@app.route("/") 
def index():
    return render_template("index.html")

# hey Python, define a function called index, takes no arguments.
And the only thing you should ever do is return render template of quote unquote "index.html.
```

- The @ symbol in Python is called a decorator, which modifies a function.
- typing <code>flask run</code> will return that HTML file when we visit our server’s URL: 

## Getting input 

/?name=David at the end of the URL
```
from flask import Flask, render_template, request

app = Flask(__name__)


@app.route("/")
def index():
    name = request.args.get("name")
    return render_template("index.html", name=name)
```
We can use the request variable from the Flask library to get the arguments from the request. Then, we can pass in the name variable as an argument to the render_template function.

```
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta name="viewport" content="initial-scale=1, width=device-width">
        <title>hello</title>
    </head>
    <body>
        hello, {{ name }}
    </body>
</html>
```
To make sure our templates are reloaded, we can press control and C in the terminal window to exit Flask, and restart our server with <code>flask run</code> again.

## Forms

```
<!DOCTYPE html>

<html lang="en">
    <head>
        <meta name="viewport" content="initial-scale=1, width=device-width">
        <title>hello</title>
    </head>
    <body>
        <form action="/greet" method="get">
            <input autocomplete="off" autofocus name="name" placeholder="Name" type="text">
            <input type="submit">
        </form>
    </body>
</html>
```
We’ll send the form to the /greet route, and have an input for the name parameter as well as a submit button.

add a function for the /greet route with what we had in the index() function before:
```
  @app.route("/")
  def index():
      return render_template("index.html")


  @app.route("/greet")
  def greet():
      name = request.args.get("name")
      return render_template("greet.html", name=name)
```


# **you should never, ever, ever rely on client side safety checks like required.** 
```
## Layout

layout.html:

<!DOCTYPE html>

<html lang="en">
    <head>
        <title>hello</title>
    </head>
    <body>
        {% block body %}{% endblock %}
    </body>
</html>
```
 With the {% %} syntax, we can include placeholder blocks, or other chunks of code. Here we’ve named our block body since it contains the HTML that should go in the <body> element.

In index.html, we’ll use the layout.html blueprint and only define the body block with:

 
```
{% extends "layout.html" %}

{% block body %}

    <form action="/greet" method="post">
        <input autocomplete="off" autofocus name="name" placeholder="Name" type="text">
        <input type="submit">
    </form>

{% endblock %}
```
Or even
    
```
    {% extends "layout.html" %}

{% block body %}

    hello, {{ name }}

{% endblock %}
```
    
# POST

- Our form above used the GET method, which includes our form’s data in the URL.
- We’ll just need to change the method in our HTML form: <form action="/greet" method="post">.
- Now, when we visit our form and submit it, we see another error, “Method Not Allowed”.
- Our controller will also need to be changed to accept the POST method, and look for the input from the form:
    
```
    @app.route("/greet", methods=["POST"])
    def greet():
        return render_template("greet.html", name=request.form.get("name", "world"))
```
    
 - While request.args is for inputs from a GET request, we have to use request.form in Flask for inputs from a POST request.
 - Now, when we restart our application after making these changes, we can see that the form takes us to /greet, but the contents aren’t included in the URL anymore.
 - Note that when we reload the /greet page, the browser asks us to confirm the form submission, since it’s temporarily remembering the inputs.
 - GET requests are useful since they allow the browser to save the contents of the form in history, and allow links that include information as well, like https://www.google.com/search?q=what+time+is+it.
    
    request.args is for GET requests, request.form is for POST requests. 

# MVC

The Flask framework implements a particular paradigm, or way of thinking and programming. This paradigm, also implemented by other frameworks, is known as MVC, or Model–view–controller:
       
 ![image](https://user-images.githubusercontent.com/31789624/202298664-f9b96333-4957-4052-b637-adfc02ae5b21.png)

- The controller contains our “business logic”, code that manages our application overall, given user input. In Flask, this will be our Python code in app.py.
- The view includes templates and visuals for the user interface, like the HTML and CSS that the user will see and interact with.
- The model is our application’s data, such as a SQL database or CSV file, which we haven’t yet used.
    
 # Diagonisticing an error
    
 thought process to diagnose issues like this. Go back to the basics, go back to what HTTP and what HTML forms are all about, and just rule things in and out. 
    
 ## Session
  
Sessions are how web servers remembers information about each user, which enables features like allowing users to stay logged in, and saving items to a shopping cart. These features require our server to be stateful, or having access to additional state, or information. HTTP on its own is stateless, since after we make a request and get a response, the interaction is completed.
It turns out that servers can send another header in a response, called Set-Cookie:


 Cookies are small pieces of data from a web server that the browser saves for us. In many cases, they are large random numbers or strings used to uniquely identify and track a user between visits.
    
### login
    
we can use the flask_session library to help manage this for us, in a new app called login:
```
from flask import Flask, redirect, render_template, request, session
from flask_session import Session

# Configure app
app = Flask(__name__)

# Configure session
app.config["SESSION_PERMANENT"] = False
app.config["SESSION_TYPE"] = "filesystem"
Session(app)
```
default / route, we’ll redirect to /login if there’s no name set in session for the user yet, and otherwise show a default index.html template.
```
@app.route("/")
def index():
    if not session.get("name"):
        return redirect("/login")
    return render_template("index.html")

```

 In our index.html, we can check if session["name"] exists, and show different content if so:
```
{% extends "layout.html" %}
{% block body %}

    {% if session["name"] %}
        You are logged in as {{ session["name"] }}. <a href="/logout">Log out</a>.
    {% else %}
        You are not logged in. <a href="/login">Log in</a>.
    {% endif %}

{% endblock %}
```
For our /login route, we’ll store name in session to the form’s value sent via POST, and then redirect to the default route. If we visited the route via GET, we’ll render the login form at login.html:
```
@app.route("/login", methods=["GET", "POST"])
  def login():
      if request.method == "POST":
          session["name"] = request.form.get("name")
          return redirect("/")
      return render_template("login.html")

```
 Then, in our login.html, we can have a form that can submit to itself:
```
    {% extends "layout.html" %}

    {% block body %}

        <form action="/login" method="post">
            <input autocomplete="off" autofocus name="name" placeholder="Name" type="text">
            <input type="submit" value="Log In">
        </form>

    {% endblock %}
```
For the /logout route, we can clear the value for name in session by setting it to None, and redirect to / again:
```
@app.route("/logout")
def logout():
    session["name"] = None
    return redirect("/")

```

# Ajax
Ajax (formerly Asynchronous JavaScript and XML ) allows us to dynamically update a webpage even more dynamically.
 
Central to our ability to asynchronously update our pages is tomake use of a special JavaScript object called an XMLHttpRequest

    <code>var xhttp = new XMLHttpRequest</code>
    
- After obtaining your new object, you need to define its onreadystatechange behavior.
- This is a function (typically an anonymous function) that will be called when the asynchronous HTTP request has completed, and thus typically defines what is expected to change on your site.
 
- XMLHttpRequest s have two additional properties that are used to detect when the page finishes loading.

- The readyState property will change from from 0 (request not yet initialized) to 1, 2, 3, and finally 4 (request finished, response

- The status property will (hopefully!) be 200 

- Then just make your asynchronous request using the open() method to define the request and the send() method to actually send it.
- There is a slightly different way to do this syntactically with jQuery!
    
JavaScript function that is preparing, opening, and sending an Ajax request.
 ```
    function
ajax_request (
{
    var aj = new XMLHttpRequest
    aj.onreadystatechange   = function(){ 
    if (aj.readyState == 4 && aj.status == 200)
    // do something to the page
    };
    aj.open ("GET", /* url */*/,true);
    aj.send()
    }
 ```
generally done in Jquery
    http://api.jquery.com/jquery.ajax
## other  
- cross-site request forgery. A fancy way of saying you trick them into clicking a link that they shouldn't have, because the website was using GET alone.
    
- it's a very common convention on a server in the real world to store sensitive information in the computer's memory so that it can be accessed when your website is running, but not in your source code. It's way too easy if you put credentials, sensitive stuff in your source code, to post it to GitHub or to screenshot it accidentally, or for information to leak out. 
    
- OS.environ dictionary refers to what are called environment variables. And this is like an out-of-band, a special way of defining key value pairs in the computer's memory by running a certain command but that never show up in your actual code. Otherwise, there would be so many usernames and passwords accidentally visible on the internet. 
 
    A session is the technical term for what you and I know as a shopping cart. When you go to amazon.com and you start adding things to your shopping cart, they follow you from page to page to page. Heck if you close your browser, come back to the next day, they're typically still your shopping cart, which is great for Amazon because they want your business
 
**How to run the flask application**
```
    export FLASK_APP=application.py
export FLASK_DEBUG=1
flask run
```
