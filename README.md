# sepRSE
Django project for SeptembRSE walkthrough

View the slides and videos here: TBC


## 1: Django Basics

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

## 2: Settings and Layout

In this next part we'll re-configure the layout for our settings files and set up where the templates should sit.

### Settings

Django will automatically create a `settings.py` file for you within the project directory, but we want to be able to configure this for different environments so first we'll create a new package called `settings` and move the file into this new folder. We'll also rename it to `base.py` as it contains the base settings that are applicable to all running environments. This should leave your project directory looking like this:

```
sepRSE/
  settings/
    __init__.py
    base.py  # previously settings.py
  __init__.py
  asgi.py
  urls.py
  wsgi.py
```

Because we've moved that settings file down a directory you'll need to edit the location of the `BASE_DIR` setting and add one extra `.parent`:

```python
BASE_DIR = Path(__file__).resolve().parent.parent.parent
```

We're then going to create three new settings files that'll expand on those base settings with items that are specific for the environment. We'll create `dev.py`, `test.py` and `prod.py` settings files. For example our production settings file might look like the below.

```python
# prod.py

from .base import *

DEBUG = False
```

You could also create a `local.py` settings file that would import the settings from either dev, test or prod and add any secrets, e.g. overwriting the Django secret key setting. You would ensure this file isn't included in any version control.

After creating the three new settings files your project directory should now look like this:

```
sepRSE/
  settings/
    __init__.py
    base.py  # previously settings.py
    dev.py
    prod.py
    test.py
  __init__.py
  asgi.py
  urls.py
  wsgi.py
```

You then just need to make sure that the entry files are using the correct settings, so in the `manage.py` top-level file you'll need to set it to use the dev settings by updating the following line:

```python
# manage.py

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'sepRSE.settings.dev')
```

For the `asgi.py` and `wsgi.py` files that are used in production you'll want to change the equivalent line to use the prod settings (`sepRSE.settings.prod`). 

This now sets your project up ready for running in multiple environments with easy to distinguish settings.

### Templates

We can now set up the template directories and add some base files. There are two different template directories that we'll create at this stage, the first is for project-wide templates and templates that will overwrite some other imported apps. The second is specific to our app `core`. 

#### Project Templates

This directory will live at the top level of the project, and will initially contain the base templates that the whole project will expand from. We'll also include any templates that overwrite other apps that we're using, which in our case will be the django registration login template. We can also seperate out template snippets which might get re-used in multiple places, for example our navigation topbar. Feel free to copy and use the templates included within this repo, to get the following directory structure:

```
core/
sepRSE/
templates/
  registration/
    login.html
  snippets/
    base_menu.html
  base.html
  base_clear.html
manage.py
```

You then just need to ensure Django can pick up and find this template folder, so you need to add it to the `base.py` settings file:

```python
# settings/base.py


TEMPLATES = [
    {
        # ...
        'DIRS': [BASE_DIR / 'templates'],  # This is the bit you need to add
        # ...
    }
```

Because Django reads the template dirs in order, and it will use whichever correctly named file it finds first, having this top-level directory ensures that these files will be used before any app-specfic templates.

#### App Templates

We'll still want to create some app templates though, which in our case will be specific to views within our `core` app. We'll therefore create a templates folder within the core app, which will in turn contain a `core` directory. This last directory may feel redundant but will ensure we have the correct namespacing within all our templates. As we said, Django will use the first template file it comes across with the right name so if you have two different apps installed, each of which has a `main.html` template, only one of those templates will be used without namespacing. 

We can then create our first template into the app-specific template directory, which we'll call `main.html`. The root directory should now look like this:

```
core/
  templates/
    core/
      main.html
  __init__.py
  admin.py
  apps.py
  models.py
  tests.py
  views.py
sepRSE/
templates/
manage.py
```
