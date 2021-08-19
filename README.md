# sepRSE
Django project for SeptembRSE walkthrough

View the slides and videos here: TBC


## Step 1: Django Basics

To get up and running with Django, you'll first need to install Django into a virtual environment. I tend to use conda, as it means I can also set the Python version I want to use for this project.

```
conda create -n sepRSE python=3.9 django
```

Once installed and you've activated the environment, you're then ready to create your django project, all you need to provide is the name of the project which in this case was `sepRSE`.

```
django-admin startproject sepRSE
cd sepRSE/
```

This is will create the top-level management entry point `manage.py` as well as a directory with the project settings and running files. You'll want to then add your first app, which will be the core app for this project so we'll call it `core`.

```
python manage.py startapp core
```

This will create a `core` folder with all the model and view files you need to run your app. To make sure that django picks it up, you'll need to include it within the `INSTALLED_APPS` section of the settings file as below:

```python
# settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'core',
]
```

At the end of this step you should have the following project layout:

```
core/
  __init__.py
  admin.py
  apps.py
  models.py
  tests.py
  views.py
sepRSE/
  __init__.py
  asgi.py
  settings.py
  urls.py
  wsgi.py
manage.py
```

You can start running the development server with `python manage.py runserver` which will launch it to `http://localhost:8000`.
