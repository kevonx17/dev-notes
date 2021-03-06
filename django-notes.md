# Django Notes

## Data and Models

### Connecting to Postgres

Here is the [database settings documentation](https://docs.djangoproject.com/en/1.10/ref/settings/#std:setting-DATABASES).

You will need to have [psychopg](http://initd.org/psycopg/) installed:

```bash
$ python -m pip install psychopg2
```

In your `mysite/settings.py` file setup postgres with:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydatabase',
        'USER': 'mydatabaseuser',
        'PASSWORD': 'mypassword',
        'HOST': '127.0.0.1',
        'PORT': '5432',
    }
}
```

### Generating models

Here is the [model documentation](https://docs.djangoproject.com/en/1.10/topics/db/models/).

In your app models file you can setup a model with:

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```

This is very DataMapper like from the Ruby world, where you describe your model's fields in the model itself.

[Model fields documentation](https://docs.djangoproject.com/en/1.10/ref/models/fields/)

Generate the migrations after creating the model:

```bash
$ python manage.py makemigrations bestappever
```

You can view the sql a migration will execute:

```bash
$ python manage.py sqlmigrate polls 0001
```

Run the migrations:

```bash
$ python manage.py migrate
```

### Field type examples

```python
# Standard types
title = models.CharField(max_length=200)
release_date = models.DateField()
num_stars = models.IntegerField()
active = models.BooleanField()

# Auto timestamp
created_at = models.DateTimeField(auto_now_add=True)

# Verbose label
first_name = models.CharField("Person's first name", max_length=30)

# Setting default
foo = models.CharField(default="bar")

# Unique index
email = models.CharField(unique=True)

# Options
SHIRT_SIZES = (
    ('S', 'Small'),
    ('M', 'Medium'),
    ('L', 'Large'),
)
shirt_size = models.CharField(max_length=1, choices=SHIRT_SIZES)

# Primary key: this is done for you by default, so you don't have to add
# this, but if Django sees that the name is different than "id" then it 
# won't auto add the id field and will use your custom field instead.
id = models.AutoField(primary_key=True)

# Belongs to with cascade delete
post = models.ForeignKey(Post, on_delete=models.CASCADE)
```

### Seeding data

[Here's documentation on seeding](https://docs.djangoproject.com/en/1.10/howto/initial-data/).

You can create yaml or json files in a `fixtures` directory in your app.

Then you can load the data with:

```bash
$ python manage.py loaddata <fixturename>
```

If you want to use yaml, you will need to install the yaml package:

```bash
$ python -m pip install pyyaml
```

## App creation workflow

Setup your virtual env:

```bash
$ pyenv virtualenv 3.6.0 my-app-3.6.0
```

Activate the virtual env:

```bash
$ pyenv activate my-app-3.6.0
```

Rehash the shell:

```bash
$ exec $SHELL
```

Create the Django project:

```bash
$ django-admin startproject my_app
```

Set the virtual env for the project:

```bash
$ cd my_app
$ pyenv local my-app-3.6.0
```

Create the initial requirements file:

```bash
$ python -m pip freeze > requirements.txt
```

Add some Git. [Here's a good `.gitignore`](https://raw.githubusercontent.com/github/gitignore/master/Python.gitignore)

```bash
$ git init
$ touch .gitignore
```

Then run the server and login:

```bash
$ python manage.py runserver
```


Generate the app:

```bash
$ python manage.py startapp bestappever
```

## Admin site

To log into the admin site, you need to create a superuser:

```bash
$ python manage.py createsuperuser
```

Then in your app, you need to register your models to be editable by the admin section:

`blog/admin.py`

```python
from django.contrib import admin
from .models import Comment, Post


admin.site.register(Comment)
admin.site.register(Post)
```

### Customizing templates

Add `DIRS` section to your `settings.py`:

```python
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [os.path.join(BASE_DIR, 'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

Find the location of the default Django templates:

```bash
$ python -c "import django; print(django.__path__)"
```

Copy and paste the template into `<projectroot>/templates/admin`.  `projectroot` being the same directory as the `manage.py` file.
