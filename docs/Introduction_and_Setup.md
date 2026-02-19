# Part 1: Introduction & Setup

## Django Version

This documentation is written for **Django 4.2 LTS** (Long Term Support).

- **Django Version**: 4.2.7 or higher
- **Python Version**: 3.8 or higher
- **Support**: Django 4.2 LTS is supported until April 2026

**Why Django 4.2 LTS?**
- Long-term support with security updates
- Stable and production-ready
- Wide community adoption
- Compatible with modern Python versions

---

## What is Django?

Django is a high-level Python web framework that enables rapid development of secure and maintainable websites. Built by experienced developers, it takes care of much of the hassle of web development, so you can focus on writing your app without needing to reinvent the wheel.

### Key Features

- **Fast Development**: Django was designed to help developers take applications from concept to completion quickly
- **Security**: Django helps developers avoid many common security mistakes (SQL injection, XSS, CSRF, etc.)
- **Scalable**: Used by Instagram, Pinterest, Mozilla, and many high-traffic sites
- **Batteries Included**: Comes with authentication, admin panel, ORM, forms, and more out of the box
- **Versatile**: Can build any type of website - from content management systems to social networks to scientific computing platforms

### Who Uses Django?

- **Instagram** - Handles billions of requests daily
- **Pinterest** - Manages millions of pins and users
- **Mozilla** - Powers their support and add-ons sites
- **National Geographic** - Content management
- **NASA** - Various internal applications

---

## Django Architecture (MVT Pattern)

Django follows the **MVT (Model-View-Template)** pattern, which is similar to MVC (Model-View-Controller):

```
┌─────────────────────────────────────────────────────────┐
│                    User Request                         │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                  URLs (urls.py)                         │
│            Maps URL patterns to Views                   │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                  VIEW (views.py)                        │
│          Contains business logic & processing           │
│                                                          │
│    ┌──────────────────┐      ┌──────────────────┐     │
│    │  Queries MODEL   │◄────►│  Renders TEMPLATE│     │
│    └──────────────────┘      └──────────────────┘     │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│               MODEL (models.py)                         │
│          Database structure & ORM queries               │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│              TEMPLATE (HTML files)                      │
│          Presentation layer with Django syntax          │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│                  HTTP Response                          │
└─────────────────────────────────────────────────────────┘
```

### Components Explained

**Model (M):**
- Defines data structure (database tables)
- Handles data logic and database operations
- Written in Python classes
- Uses Django ORM (Object-Relational Mapping)

**View (V):**
- Contains business logic
- Processes user requests
- Interacts with models
- Passes data to templates
- Returns HTTP responses

**Template (T):**
- Presentation layer (HTML)
- Uses Django Template Language (DTL)
- Displays data from views
- Handles user interface

**URLs:**
- Routes requests to appropriate views
- Maps URL patterns to view functions/classes
- Handles URL parameters

---

## Environment Setup

### Prerequisites

Before starting, ensure you have:
- Python 3.8 or higher
- pip (Python package manager)
- Virtual environment tool (venv)
- Text editor or IDE (VS Code, PyCharm, etc.)

### Step 1: Verify Python Installation

```bash
# Check Python version
python --version
# or
python3 --version

# Expected output: Python 3.8.x or higher
```

If Python is not installed, download it from [python.org](https://www.python.org/downloads/)

### Step 2: Create Project Directory

```bash
# Create a directory for your Django projects
mkdir django_projects
cd django_projects
```

### Step 3: Create Virtual Environment

A virtual environment isolates your project dependencies from other Python projects.

```bash
# Create a virtual environment named 'django_env'
python -m venv django_env
```

#### Activate Virtual Environment (Choose Your OS)

**On Linux/macOS:**
```bash
source django_env/bin/activate
```

**On Windows Command Prompt:**
```cmd
django_env\Scripts\activate
```

**On Windows PowerShell:**
```powershell
django_env\Scripts\Activate.ps1
```

**On Windows Git Bash:**
```bash
source django_env/Scripts/activate
```

**You should see (django_env) in your terminal prompt after activation.**

#### Common Virtual Environment Issues

**Issue 1: Windows PowerShell Execution Policy Error**
```
Error: "cannot be loaded because running scripts is disabled on this system"
```
**Solution:**
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

**Issue 2: Permission Denied on Linux/macOS**
```
Error: "Permission denied"
```
**Solution:**
```bash
chmod +x django_env/bin/activate
source django_env/bin/activate
```

**Issue 3: Command Not Found**
```
Error: "python: command not found"
```
**Solution:**
- Try `python3` instead of `python`
- Verify Python is installed: `python3 --version`

**Why use virtual environments?**
- Isolates project dependencies
- Prevents version conflicts
- Makes project portable
- Easy to replicate environment

### Step 4: Install Django

```bash
# Install latest Django version
pip install django

# Verify installation
django-admin --version

# Expected output: 5.x.x or similar
```

### Step 5: Install Additional Packages

```bash
# Install useful packages for our project
pip install pillow              # For image handling
pip install python-decouple     # For environment variables
pip install django-crispy-forms # For better forms (optional)

# Create requirements.txt to track dependencies
pip freeze > requirements.txt
```

**What is requirements.txt?**
- Lists all project dependencies
- Makes it easy to install packages on another machine
- Essential for deployment

---

## Creating Your First Django Project

### Step 1: Create Django Project

```bash
# Create a new Django project named 'taskmanager'
django-admin startproject taskmanager

# Navigate to project directory
cd taskmanager

# View project structure
ls -la  # Linux/Mac
dir     # Windows
```

### Project Structure Explained

```
taskmanager/
├── manage.py              # Command-line utility for Django
└── taskmanager/           # Python package for your project
    ├── __init__.py        # Makes this directory a Python package
    ├── settings.py        # Project settings and configuration
    ├── urls.py            # URL declarations (routing)
    ├── asgi.py            # ASGI config for async servers
    └── wsgi.py            # WSGI config for deployment
```

**File Descriptions:**

**`manage.py`**
- Command-line utility to interact with Django project
- Used to run server, create apps, migrations, etc.
- Don't modify this file

**`settings.py`**
- All project settings
- Database configuration
- Installed apps
- Middleware
- Templates configuration
- Static files settings

**`urls.py`**
- URL routing for the project
- Maps URLs to views
- Like a "table of contents" for your site

**`wsgi.py` and `asgi.py`**
- Entry points for web servers
- Used in production deployment
- WSGI for synchronous, ASGI for asynchronous

### Step 2: Run Development Server

```bash
# Run the development server
python manage.py runserver

# Expected output:
# Watching for file changes with StatReloader
# Performing system checks...
# System check identified no issues (0 silenced).
# You have 18 unapplied migration(s)...
# Starting development server at http://127.0.0.1:8000/
# Quit the server with CONTROL-C.
```

Open your browser and visit: `http://127.0.0.1:8000/`

You should see the Django welcome page! 🎉

**Note:** Ignore the migration warning for now. We'll handle it soon.

### Step 3: Create Django App

Django projects are organized into **apps**. An app is a web application that does something specific.

```bash
# Stop the server (Ctrl+C)
# Create an app named 'tasks'
python manage.py startapp tasks
```

### App Structure Explained

```
tasks/
├── __init__.py           # Python package marker
├── admin.py              # Admin interface configuration
├── apps.py               # App configuration
├── migrations/           # Database migrations folder
│   └── __init__.py
├── models.py             # Data models (database tables)
├── tests.py              # Unit tests
└── views.py              # View functions/classes
```

**File Descriptions:**

**`models.py`**
- Define database structure
- Create Python classes that represent tables
- Django ORM converts these to SQL

**`views.py`**
- Business logic
- Handle requests and return responses
- Query models and render templates

**`admin.py`**
- Register models for admin interface
- Customize admin panel

**`tests.py`**
- Write unit tests
- Test your code automatically

**`migrations/`**
- Tracks database schema changes
- Version control for database

### Step 4: Register the App

Open `taskmanager/settings.py` and add your app to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tasks',  # Add your app here
]
```

**Why register apps?**
- Django needs to know which apps are part of the project
- Enables migrations, templates, static files for the app
- Required for app functionality

### Step 5: Apply Initial Migrations

Django comes with built-in apps (auth, admin, etc.) that need database tables.

```bash
# Create database tables for built-in apps
python manage.py migrate

# Expected output:
# Operations to perform:
#   Apply all migrations: admin, auth, contenttypes, sessions
# Running migrations:
#   Applying contenttypes.0001_initial... OK
#   Applying auth.0001_initial... OK
#   ... (more migrations)
```

This creates a `db.sqlite3` file - your database!

### Step 6: Create Superuser

Create an admin account to access Django's admin interface.

```bash
# Create superuser
python manage.py createsuperuser

# You'll be prompted for:
# Username: admin
# Email: admin@example.com
# Password: ******** (choose a strong password)
# Password (again): ********
```

### Step 7: Access Admin Interface

```bash
# Start the server
python manage.py runserver
```

Visit: `http://127.0.0.1:8000/admin/`

Login with your superuser credentials. You'll see the Django admin panel! 🎉

---

## Understanding Django Settings

Let's explore important settings in `settings.py`:

### Debug Mode

```python
# Development: True, Production: False
DEBUG = True
```

**Never set DEBUG=True in production!** It exposes sensitive information.

### Allowed Hosts

```python
# Hosts allowed to serve the application
ALLOWED_HOSTS = []  # Empty for development
# Production: ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']
```

### Database Configuration

```python
DATABASES = 
    'default': 
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
```

Django supports multiple databases:
- SQLite (default, good for development)
- PostgreSQL (recommended for production)
- MySQL
- Oracle

### Static Files

```python
STATIC_URL = '/static/'
# For production, also set:
# STATIC_ROOT = BASE_DIR / 'staticfiles'
```

### Templates

```python
TEMPLATES = [
    
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],  # Add custom template directories here
        'APP_DIRS': True,  # Look for templates in app directories
        ...
    },
]
```

---

## Django Management Commands

Essential commands you'll use frequently:

### Project & App Commands

```bash
# Create new project
django-admin startproject projectname

# Create new app
python manage.py startapp appname
```

### Server Commands

```bash
# Run development server
python manage.py runserver

# Run on different port
python manage.py runserver 8080

# Run on different host
python manage.py runserver 0.0.0.0:8000
```

### Database Commands

```bash
# Create migration files
python manage.py makemigrations

# Apply migrations
python manage.py migrate

# Show migrations
python manage.py showmigrations

# SQL for a migration
python manage.py sqlmigrate appname 0001
```

### User Management

```bash
# Create superuser
python manage.py createsuperuser

# Change user password
python manage.py changepassword username
```

### Shell & Testing

```bash
# Django shell (interactive Python)
python manage.py shell

# Run tests
python manage.py test

# Run specific test
python manage.py test appname.tests.TestClassName
```

### Utility Commands

```bash
# Check for problems
python manage.py check

# Collect static files (for production)
python manage.py collectstatic

# Clear database
python manage.py flush
```

---

## Project Structure Best Practices

### Recommended Structure

```
taskmanager/
├── manage.py
├── requirements.txt
├── .gitignore
├── README.md
├── taskmanager/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│   └── asgi.py
├── tasks/
│   ├── migrations/
│   ├── templates/
│   │   └── tasks/
│   ├── static/
│   │   └── tasks/
│   ├── __init__.py
│   ├── admin.py
│   ├── apps.py
│   ├── models.py
│   ├── views.py
│   ├── urls.py
│   └── forms.py
└── media/
```

### Create .gitignore

```bash
# Create .gitignore file
touch .gitignore
```

Add to `.gitignore`:

```
# Python
*.py[cod]
__pycache__/
*.so
*.egg
*.egg-info/
dist/
build/

# Django
*.log
db.sqlite3
media/
staticfiles/

# Virtual Environment
venv/
django_env/
env/

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db
```

---

## Summary

You've learned:

✅ What Django is and why it's popular
✅ Django's MVT architecture
✅ How to set up a Python virtual environment
✅ How to install Django
✅ How to create a Django project and app
✅ Django project structure
✅ Essential Django management commands

### Common Mistakes to Avoid

### Mistake 1: Forgetting to Activate Virtual Environment
**Error**: Packages installed globally instead of in virtual environment
**Symptom**: `ModuleNotFoundError` even after installing Django
**Solution**: Always activate virtual environment before installing packages
```bash
source django_env/bin/activate  # Linux/Mac
django_env\Scripts\activate     # Windows
```

### Mistake 2: Using Wrong Python Command
**Error**: `python: command not found` or wrong Python version
**Solution**: 
- Check which command works: `python --version` or `python3 --version`
- Use the correct one consistently throughout the project

### Mistake 3: Not Adding App to INSTALLED_APPS
**Error**: `django.core.exceptions.ImproperlyConfigured: No module named 'tasks'`
**Solution**: Add your app to `INSTALLED_APPS` in `settings.py`
```python
INSTALLED_APPS = [
    # ...
    'tasks',  # Don't forget this!
]
```

### Mistake 4: Running Server on Wrong Port
**Error**: Port already in use
**Solution**: Specify a different port
```bash
python manage.py runserver 8001
```

### Mistake 5: Not Running Migrations After Model Changes
**Error**: `django.db.utils.OperationalError: no such table`
**Solution**: Always run migrations after creating/modifying models
```bash
python manage.py makemigrations
python manage.py migrate
```

---

**You're now ready to start building Django applications! 🚀**

**Next:** Continue to [FBV_Models_and_Database.md](FBV_Models_and_Database.md) to learn about Django models and databases.

✅ What Django is and why it's popular
✅ Django's MVT architecture
✅ How to set up a Python virtual environment
✅ How to install Django
✅ How to create a Django project and app
✅ Django project structure
✅ Essential Django management commands
✅ How to access the admin interface

### Next Steps

👉 **Continue to:** [FBV_Models_and_Database.md](FBV_Models_and_Database.md)

In the next section, we'll:
- Create database models
- Understand Django ORM
- Work with migrations
- Query the database
- Build the Task model for our project

---

**Ready to dive deeper? Let's build something! 🚀**
