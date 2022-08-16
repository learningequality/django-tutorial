> Part of this section is based on tutorials by Geek Girls Carrots (https://github.com/ggcarrots/django-carrots).

> Part of this section is based on the [django-marcador
tutorial](http://django-marcador.keimlink.de/) licensed under the Creative Commons
Attribution-ShareAlike 4.0 International License. The django-marcador tutorial
is copyrighted by Markus Zapke-Gründemann et al.


## Virtual Environments

Virtual environments allow a developer to have an encapsulated Python environment, using a specific version of Python, and with dependencies installed in a way that only affect the virtual environment. This is important as different projects or even different versions of the same project may have different dependencies, and virtual environments allow you to switch between them seamlessly and explicitly.

### Using `pyenv virtualenv` with pyenv

To create a virtualenv for the Python version used with pyenv, run `pyenv virtualenv`, specifying the Python version you want and the name of the virtualenv directory. For example, because we can make a virtual environment for Kolibri using Python {{ book.py_release }}:

```sh
$ pyenv virtualenv {{ book.py_release }} djangotutorial-py{{ book.py_version }}
```

*If you get 'command not found' or a similar error, and pyenv virtualenv is not installed, [please follow the installation instructions](https://github.com/pyenv/pyenv-virtualenv#installation).*

will create a virtualenv based on Python {{ book.py_release }} under `$(pyenv root)/versions` in a
folder called `kolibri-py{{ book.py_version }}`.

### List existing virtualenvs

`pyenv virtualenvs` shows you the list of existing virtualenvs and `conda` environments.

```sh
$ pyenv virtualenvs
  {{ book.py_release }}/envs/djangotutorial-py{{ book.py_version }} (created from /home/youuuu/.pyenv/versions/{{ book.py_release }})
  djangotutorial-py{{ book.py_version }} (created from /home/youuuu/.pyenv/versions/{{ book.py_release }})
```

There are two entries for each virtualenv, and the shorter one is just a symlink.


### Activate virtualenv

If you want a virtual environment to always activate when you enter a certain directory, you can use the [`pyenv local`](https://github.com/pyenv/pyenv/blob/master/COMMANDS.md#pyenv-local) command.

```
mkdir django-tutorial
cd django-tutorial
pyenv local djangotutorial-py{{ book.py_version }}
```

Now whenever you enter the directory, the virtual environment will be activated.

You can also activate and deactivate a pyenv virtualenv manually:

```sh
pyenv activate djangotutorial-py{{ book.py_version }}
pyenv deactivate
```

### Delete existing virtualenv

Removing the directories in `$(pyenv root)/versions` and `$(pyenv root)/versions/{version}/envs` will delete the virtualenv, or you can run:

```sh
pyenv uninstall djangotutorial-py{{ book.py_version }}
```

You can also delete existing virtualenvs by using `virtualenv-delete` command, e.g. you can run:
```sh
pyenv virtualenv-delete djangotutorial-py{{ book.py_version }}
```
This will delete virtualenv called `djangotutorial-py{{ book.py_version }}`.

For more information on use of virtual environments see the [pyenv-virtualenv documentation](https://github.com/pyenv/pyenv-virtualenv/blob/master/README.md#usage).

## Installing Django {#django}

Now that you have your `virtualenv` started, you can install Django.

Before we do that, we should make sure we have the latest version of `pip`, the software that we use to install Django:

{% filename %}command-line{% endfilename %}
```
(myvenv) ~$ python -m pip install --upgrade pip
```

### Installing packages with requirements

A requirements file keeps a list of dependencies to be installed using
`pip install`:

First create a `requirements.txt` file inside of the `django-tutorial/` folder, using the code editor that you installed earlier. You do this by opening a new file in the code editor and then saving it as `requirements.txt` in the `django-tutorial/` folder. Your directory will look like this:

```
django-tutorial
└───requirements.txt
```

In your `django-tutorial/requirements.txt` file you should add the following text:

{% filename %}django-tutorial/requirements.txt{% endfilename %}
```
Django~={{ book.django_version }}
```

Now, run `pip install -r requirements.txt` to install Django.

{% filename %}command-line{% endfilename %}
```
(myvenv) ~$ pip install -r requirements.txt
Collecting Django~={{ book.django_version }} (from -r requirements.txt (line 1))
  Downloading Django-{{ book.django_version }}-py3-none-any.whl (7.9MB)
Installing collected packages: Django
Successfully installed Django-{{ book.django_version }}
```

That's it! You're now (finally) ready to create a Django application!
