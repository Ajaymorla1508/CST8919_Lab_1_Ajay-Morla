# CST8919_Lab_1_Ajay-Morla
# Flask Auth0 Protected Page Demo

## Overview

This is a simple Flask web application demonstrating authentication with Auth0. It includes:
- A public home page
- A login/logout flow using Auth0
- A `/protected` route that only authenticated users can access

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <your-repo-url>
cd lab1-8919
```

### 2. Configure Auth0

1. Sign up or log in to [Auth0](https://auth0.com/).
2. Create a new "Regular Web Application".
3. In the application settings, set:
   - **Allowed Callback URLs:** `http://localhost:3000/callback`
   - **Allowed Logout URLs:** `http://localhost:3000/`
   - **Allowed Web Origins:** `http://localhost:3000`
4. Copy your **Domain**, **Client ID**, and **Client Secret**.

### 3. Create a `.env` File

Create a `.env` file in the project root with the following content (replace with your Auth0 values):

```properties
AUTH0_CLIENT_ID=your_auth0_client_id
AUTH0_CLIENT_SECRET=your_auth0_client_secret
AUTH0_DOMAIN=your_auth0_domain
APP_SECRET_KEY=your_flask_secret_key
```

Example:
```properties
AUTH0_CLIENT_ID=igLCWfzpmpTPLa699Kqjyd2kSROr3Hol
AUTH0_CLIENT_SECRET=Sr0JZ30P3rXZ7mSCaY_FdEEQy0n0ltVcpDWUhwSIG39pGdrHxRG8tSNkHZEyfpaj
AUTH0_DOMAIN=dev-y8z4qjl7ocq73kel.us.auth0.com
APP_SECRET_KEY=309476c9dbffc262e4abd131e9b5b770076335f752ec458ae039ac43843c541e
```

### 4. Install Dependencies

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

If you don’t have a `requirements.txt`, use:
```bash
pip install Flask python-dotenv Authlib
```

### 5. Run the App

```bash
python3 server.py
```

Visit [http://localhost:3000](http://localhost:3000) in your browser.

---

## Demo Video

[Watch the 5-minute demo on YouTube](https://www.youtube.com/watch?v=YOUR_VIDEO_ID)

---

## What’s in the Code

- **server.py**: Main Flask app with Auth0 integration and protected route.
- **templates/**: HTML templates for home and protected pages.
- **.env**: Environment variables for Auth0 and Flask secret key.

---

## What I Learned

- How to integrate Auth0 authentication in a Flask app.
- How to protect routes using session-based authentication.
- How to use decorators for route protection in Flask.
- How to manage environment variables securely.

---

Feel free to reach out if you have any questions!
