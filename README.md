# Secure Login with Django

A small web app with sign up, log in, a user dashboard, log out and delete account. Built with Django, plain HTML, CSS and JavaScript. Users are stored in a Django-managed SQLite database.

## Features

- Sign up and log in on one page (two tabs)
- New users are saved to the database and sent straight to the dashboard
- Existing users log in without creating a new account
- Dashboard with some Lorem ipsum text, a **Log out** button and a **Delete account** button (with a confirmation prompt)
- Passwords are hashed, forms are CSRF-protected, and the dashboard is only reachable when logged in
- Admin panel for managing users

## Project structure

```
secureauth_project/
├── manage.py                  # Django's command-line tool
├── requirements.txt           # Python packages needed (Django)
├── db.sqlite3                 # Database (created by "migrate")
├── secureauth/                # Project-wide configuration
│   ├── settings.py            # Database, security and password rules
│   └── urls.py                # Top-level URL map
└── accounts/                  # The login/dashboard app
    ├── forms.py               # Sign-up form (username, email, passwords)
    ├── views.py               # Logic for login, signup, dashboard, logout, delete
    ├── urls.py                # URL map for this app
    ├── templates/accounts/
    │   ├── auth.html          # Login / sign-up page
    │   └── dashboard.html     # User dashboard
    └── static/accounts/
        ├── style.css          # Styling
        └── auth.js            # Tab switching and delete confirmation
```

## Setup

### 1. Install Python

### 2. Open a terminal in the project folder

```
cd path/to/secureauth_project
```

You should see `manage.py` in this folder.

### 3. Create and activate a virtual environment

| Shell | Create | Activate |
|---|---|---|
| Windows (cmd) | `python -m venv venv` | `venv\Scripts\activate` |
| Mac / Linux (bash, zsh) | `python3 -m venv venv` | `source venv/bin/activate` |
| Linux (fish) | `python3 -m venv venv` | `source venv/bin/activate.fish` |

Your prompt should now start with `(venv)`. Run the activate command again each time you open a new terminal.

### 4. Install Django

```
pip install -r requirements.txt
```

### 5. Create the database

```
python manage.py migrate
```

### 6. Create your admin account

```
python manage.py createsuperuser
```

Typing a password shows nothing on screen. That is normal.

### 7. Start the server

```
python manage.py runserver
```

### 8. Open it in your browser

- Website: http://127.0.0.1:8000/
- Admin panel: http://127.0.0.1:8000/admin/

Press **Ctrl+C** in the terminal to stop the server.

### 9. Additional info (Important):

- You can `source venv/bin/activate.fish` directly to start the server if you already have a venv.
- But when you install this folder the first time do make sure to activate a virtual environment. (Step-3)
- You have to use a **private window** for each extra user. Private windows keep separate cookies, so you can have the admin in one window and a normal user in another without them conflicting. This resolves issues like **403 Forbidden, CSRF verification failed**.

## Pages

| URL | What it does |
|---|---|
| `/` | Log in |
| `/signup/` | Sign up |
| `/dashboard/` | Dashboard (login required) |
| `/logout/` | Log out (POST only) |
| `/delete-account/` | Delete the current user (POST only) |
| `/admin/` | Admin panel (superusers only) |

## Managing users as an admin

Log in at `/admin/` and open **Users**. From there you can search users, edit details, deactivate an account (untick **Active** to block login without deleting it), reset passwords and delete users.

Useful terminal commands:

```
python manage.py createsuperuser        # make another admin
python manage.py changepassword NAME    # reset a user's password
```

To look inside the database directly, open `db.sqlite3` with a tool such as DB Browser for SQLite. Back it up before making big changes.

## Customising

- Change the look in `accounts/static/accounts/style.css`.
- Change the page content in `accounts/templates/accounts/auth.html` and `dashboard.html`. Leave the `{% ... %}` and `{{ ... }}` parts as they are, since Django fills them in.
- Change password rules in `AUTH_PASSWORD_VALIDATORS` in `secureauth/settings.py`.
- After editing CSS, do a hard refresh (Ctrl+F5, or Cmd+Shift+R on Mac).

## Security notes

- Passwords are stored as hashes, never as plain text.
- Every form includes a CSRF token, and log out and delete account only accept POST requests.
- Session cookies are `HttpOnly`.
- The default `SECRET_KEY` in `settings.py` is for development only. Before putting the site online, set real values as environment variables:

```
DJANGO_SECRET_KEY=<a long random string>
DJANGO_DEBUG=0
DJANGO_ALLOWED_HOSTS=yourdomain.com
```

With `DJANGO_DEBUG=0`, HTTPS-only cookies, HTTPS redirects and HSTS turn on automatically, so the site must be served over HTTPS.
- SQLite is fine for learning and small sites. For a larger production site, switch to PostgreSQL in the `DATABASES` setting.

## Troubleshooting

| Problem | Fix |
|---|---|
| `python` or `pip` not recognized | Re-run the Python installer with the PATH box ticked, or try `python3` / `python3 -m pip` |
| `No module named django` | The virtual environment is not active. Run the activate command again |
| fish shell: `'case' builtin not inside of switch block` | Use `source venv/bin/activate.fish` |
| Port already in use | Run `python manage.py runserver 8080` and open `http://127.0.0.1:8080/` |
| **403 Forbidden, CSRF verification failed** | The page is stale. Logging in as another user in the same browser invalidates the old token. Reload the page and try again. Use a private window for each extra test user, and stick to one address (`127.0.0.1` or `localhost`, not both) |
| CSS changes not showing | Hard refresh with Ctrl+F5 (Cmd+Shift+R on Mac) |
