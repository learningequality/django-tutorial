## Django Filter installation

In your `django-tutorial/requirements.txt` file you should add the following text:

{% filename %}django-tutorial/requirements.txt{% endfilename %}
```
django-filter=={{ book.django_filter_version }}
```

Now, run `pip install -r requirements.txt` to install Django Filter.

{% filename %}command-line{% endfilename %}
```
(myvenv) ~$ pip install -r requirements.txt
Collecting django-filter=={{ book.django_filter_version }} (from -r requirements.txt (line 1))
  Downloading django-filter-{{ book.django_filter_version }}-py3-none-any.whl (7.9MB)
Installing collected packages: django-filter
Successfully installed django-filter-{{ book.django_filter_version }}
```

> **Note** In this tutorial we are using Django Filter version {{ book.django_filter_version }} - however, the last Django Filter version to support Python 2.7 was 1.1.0, as a result there are some slight differences in how Django Filter is used in Kolibri.

We have seen how to extend Django using our own `blog` app - but we can also extend Django using installable libraries like Django Filter. To tell Django that we are using Django Filter in our project, we need to add the `django_filters` app to our `INSTALLED_APPS` setting.

We do that in the file `mysite/settings.py` -- open it in your code editor. We need to find `INSTALLED_APPS` and add a line containing `'django_filters',` just above `]`. So the final product should look like this:

{% filename %}mysite/settings.py{% endfilename %}
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'blog',
    'rest_framework',
    'django_filters',
]
```

If we wanted to add Django Filter into all of our `ViewSet`s by default, we could also add this to our settings file:

{% filename %}mysite/settings.py{% endfilename %}
```python
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ['django_filters.rest_framework.DjangoFilterBackend']
}
```

For now, we will leave it as is, and add Django Filter support on a `ViewSet` by `ViewSet` basis.