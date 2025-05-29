# Flask Auth0 Authentication Example

This document explains how I implemented user login functionality in a Python web application using Flask and Auth0 with the Authlib OAuth library..

---

## I want to integrate with my app

### System requirements

- Python 3
- Authlib 1.0+
- Flask 2.0+

---

## Configured Auth0

### Get the Application Keys

When you sign up for Auth0, a new application is created for you (or you can create a new one).  
You will need the following details from your Auth0 Application Settings:

- **Domain**
- **Client ID**
- **Client Secret**

### Configured Callback URLs

Add the following to your Auth0 Application Settings:

- **Allowed Callback URLs:**  
  `http://localhost:3000/callback`

### Configured Logout URLs

- **Allowed Logout URLs:**  
  `http://localhost:3000`

---

## Install dependencies

Created a `requirements.txt` file:

```txt
flask>=2.0.3
python-dotenv>=0.19.2
authlib>=1.0
requests>=2.27.1
```

Installed dependencies:

```bash
pip install -r requirements.txt
```

---

## Configured .env file

Created a `.env` file in your project directory:

```properties
AUTH0_CLIENT_ID=your_auth0_client_id
AUTH0_CLIENT_SECRET=your_auth0_client_secret
AUTH0_DOMAIN=your_auth0_domain
APP_SECRET_KEY=your_flask_secret_key
```

Generate a suitable string for `APP_SECRET_KEY` using:

```bash
openssl rand -hex 32
```

---

## Setup application
Once after adding the quick-start guide code this is my final `server.py`

```python
import json
from os import environ as env
from urllib.parse import quote_plus, urlencode

from authlib.integrations.flask_client import OAuth
from dotenv import find_dotenv, load_dotenv
from flask import Flask, redirect, render_template, session, url_for
from functools import wraps

ENV_FILE = find_dotenv()
if ENV_FILE:
    load_dotenv(ENV_FILE)

app = Flask(__name__)
app.secret_key = env.get("APP_SECRET_KEY")

oauth = OAuth(app)
oauth.register(
    "auth0",
    client_id=env.get("AUTH0_CLIENT_ID"),
    client_secret=env.get("AUTH0_CLIENT_SECRET"),
    client_kwargs={"scope": "openid profile email"},
    server_metadata_url=f'https://{env.get("AUTH0_DOMAIN")}/.well-known/openid-configuration'
)

def requires_auth(f):
    @wraps(f)
    def decorated(*args, **kwargs):
        if 'user' not in session:
            return redirect('/login')
        return f(*args, **kwargs)
    return decorated

@app.route("/login")
def login():
    return oauth.auth0.authorize_redirect(
        redirect_uri=url_for("callback", _external=True)
    )

@app.route("/callback", methods=["GET", "POST"])
def callback():
    token = oauth.auth0.authorize_access_token()
    userinfo = token.get("userinfo")
    session["user"] = userinfo
    return redirect("/")

@app.route("/logout")
def logout():
    session.clear()
    return redirect(
        "https://" + env.get("AUTH0_DOMAIN")
        + "/v2/logout?"
        + urlencode(
            {
                "returnTo": url_for("home", _external=True),
                "client_id": env.get("AUTH0_CLIENT_ID"),
            },
            quote_via=quote_plus,
        )
    )

@app.route("/protected")
@requires_auth
def protected():
    user = session.get("user")
    return render_template('protected.html', user=user)

@app.route("/")
def home():
    return render_template("home.html")

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=int(env.get("PORT", 3000)))
```

---

## Add templates

Created a `templates` directory and add these files:

### `templates/home.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
</head>
<body>
    {% if session.user %}
        <h1>Welcome {{ session.user.name }}!</h1>
        <p><a href="/protected">Go to Protected Page</a></p>
        <p><a href="/logout">Logout</a></p>
    {% else %}
        <h1>Welcome!</h1>
        <p><a href="/login">Login</a></p>
        <p><a href="/protected">Go to Protected Page</a></p>
    {% endif %}
</body>
</html>
```

### `templates/protected.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Protected</title>
</head>
<body>
    <h1>Protected Page</h1>
    <p>Hi {{ user['name'] }}!</p>
    <p>This page is protected and requires login.</p>
    <p><a href="/logout">Logout</a></p>
</body>
</html>
```

---

## Run application

```bash
python3 server.py
```

Visit [http://localhost:3000](http://localhost:3000) in your browser.

---

## Demo Video

[Watch the 5-minute demo on YouTube](https://www.youtube.com/watch?v=u6ZSNnYqSdc)

---

## What I Learned

- How to integrate Auth0 authentication in a Flask app.
- How to protect routes using session-based authentication.
- How to use decorators for route protection in Flask.
- How to manage environment variables securely.

---

**New to Auth0?**  
Learn [How Auth0 works](https://auth0.com/docs/get-started), how it integrates with Regular Web Applications, and which protocol it uses.
