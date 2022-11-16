# Ajax/flask

## What did i learn

## Web programming

http-server can’t process other types of requests, like form inputs from the user.

A file might be in a folder, like folder/file.html, and that reference is known as a **path**. A path can also be called a **route**, which does not need to refer to an actual file.

To get an input from the user you'll need to use ?key=value like http://www.example.com/route?key=value

## flask

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




