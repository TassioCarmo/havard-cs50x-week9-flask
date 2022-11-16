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
    
    
 ## Frost templating with jinja
    
    single list of sports instead of hard coding:
```
from flask import Flask, render_template, request

app = Flask(__name__)

SPORTS = [
    "Basketball"
    "Soccer",
    "Ultimate Frisbee"
]


@app.route("/")
def index():
    return render_template("index.html", sports=SPORTS)
```
-
```   
   {% extends "layout.html" %}

{% block body %}
    <h1>Register</h1>
    <form action="/register" method="post">
        <input autocomplete="off" autofocus name="name" placeholder="Name" type="text">
            <select name="sport">
                <option disabled selected value="">Sport</option>
                {% for sport in sports %}
                    <option value="{{ sport }}">{{ sport }}</option>
                {% endfor %}
            </select>
        <input type="submit" value="Register">
    </form>
{% endblock %}
 ```
   - 
```
    @app.route("/register", methods=["POST"])
def register():

    if not request.form.get("name") or request.form.get("sport") not in SPORTS:
        return render_template("failure.html")

    return render_template("success.html")
    ```

## Storing data
    
 registered students in a dictionary in the memory of our web server with froshims3:
```
# Implements a registration form, storing registrants in a dictionary, with error messages

from flask import Flask, redirect, render_template, request

app = Flask(__name__)

REGISTRANTS = {}

SPORTS = [
    "Basketball",
    "Soccer",
    "Ultimate Frisbee"
]


@app.route("/")
def index():
    return render_template("index.html", sports=SPORTS)


@app.route("/register", methods=["POST"])
def register():

    # Validate name
    name = request.form.get("name")
    if not name:
        return render_template("error.html", message="Missing name")

    # Validate sport
    sport = request.form.get("sport")
    if not sport:
        return render_template("error.html", message="Missing sport")
    if sport not in SPORTS:
        return render_template("error.html", message="Invalid sport")

    # Remember registrant
    REGISTRANTS[name] = sport

    # Confirm registration
    return redirect("/registrants")


@app.route("/registrants")
def registrants():
    return render_template("registrants.html", registrants=REGISTRANTS)
```
    
 We’ll create a dictionary called REGISTRANTS, and in register we’ll first check the name and sport, returning a different error message in each case with error.html. Then, we can store the name and sport in our REGISTRANTS dictionary, and redirect to another route that will display registered students.

The error message template, meanwhile, will display the error message along with a fun image of a grumpy cat:
    
```
{% extends "layout.html" %}

{% block body %}
    <h1>Error</h1>
    <p>{{ message }}</p>
    <img alt="Grumpy Cat" src="/static/cat.jpg">
{% endblock %}

Our registrants.html template will print a table with the dictionary passed in as input:

  {% extends "layout.html" %}

  {% block body %}
      <h1>Registrants</h1>
      <table>
          <thead>
              <tr>
                  <th>Name</th>
                  <th>Sport</th>
              </tr>
          </thead>
          <tbody>
              {% for name in registrants %}
                  <tr>
                      <td>{{ name }}</td>
                      <td>{{ registrants[name] }}</td>
                  </tr>
              {% endfor %}
          </tbody>
      </table>
  {% endblock %}
```
    
Our table has a header row, and then a row for each key and value stored in registrants.

If our web server stops running, we’ll lose the data stored in memory, so we’ll use a SQLite database with the SQL library from cs50 in froshims4:

```
# Implements a registration form, storing registrants in a SQLite database, with support for deregistration

from cs50 import SQL
from flask import Flask, redirect, render_template, request

app = Flask(__name__)

db = SQL("sqlite:///froshims.db")

SPORTS = [
    "Basketball",
    "Soccer",
    "Ultimate Frisbee"
]


@app.route("/")
def index():
    return render_template("index.html", sports=SPORTS)

```


```
@app.route("/register", methods=["POST"])
def register():

    # Validate submission
    name = request.form.get("name")
    sport = request.form.get("sport")
    if not name or sport not in SPORTS:
        return render_template("failure.html")

    # Remember registrant
    db.execute("INSERT INTO registrants (name, sport) VALUES(?, ?)", name, sport)

    # Confirm registration
    return redirect("/registrants")

```
    
    
  delete that row from our databas
```    
 @app.route("/deregister", methods=["POST"])
def deregister():

    # Forget registrant
    id = request.form.get("id")
    if id:
        db.execute("DELETE FROM registrants WHERE id = ?", id)
    return redirect("/registrants")

```
