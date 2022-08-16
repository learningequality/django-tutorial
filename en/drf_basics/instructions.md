## Django REST Framework installation

In your `django-tutorial/requirements.txt` file you should add the following text:

{% filename %}django-tutorial/requirements.txt{% endfilename %}
```
djangorestframework=={{ book.drf_version }}
```

Now, run `pip install -r requirements.txt` to install Django REST Framework.

{% filename %}command-line{% endfilename %}
```
(myvenv) ~$ pip install -r requirements.txt
Collecting djangorestframework=={{ book.drf_version }} (from -r requirements.txt (line 1))
  Downloading djangorestframework-{{ book.drf_version }}-py3-none-any.whl (7.9MB)
Installing collected packages: djangorestframework
Successfully installed djangorestframework-{{ book.drf_version }}
```

> **Note** In this tutorial we are using Django REST Framework version {{ book.drf_version }} - however, the last Django REST Framework version to support Python 2.7 was 3.9.1, this means that in Kolibri there may be some subtle differences. The tutorial will point out when these might arise.

We have seen how to extend Django using our own `blog` app - but we can also extend Django using installable libraries like Django REST Framework. To tell Django that we are using Django REST Framework in our project, we need to add the `rest_framework` app to our `INSTALLED_APPS` setting.

We do that in the file `mysite/settings.py` -- open it in your code editor. We need to find `INSTALLED_APPS` and add a line containing `'rest_framework',` just above `]`. So the final product should look like this:

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
]
```
