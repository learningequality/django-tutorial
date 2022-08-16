# Django REST Framework basics

[Django REST Framework](https://www.django-rest-framework.org/) is a framework for creating human and computer readable Django views for data - very often representations of Django models - as Web APIs. Broadly, it aims to help implement RESTful API endpoints, but it can be used to implement a wide range of different kinds of API endpoints.

To get started, install Django REST Framework into your project.

{% include "/drf_basics/instructions.md" %}

## Building an API for blog posts

We've been displaying our posts in web pages, but if we want to make the data available to a Javascript driven frontend that dynamically loads the data, or as a public web API for other programs to access and pull posts from, we need to build an API that serves up the data from the post models.

To do this, we can create a web API to return either lists of blog posts, or a single blog post at a time. In order to manage the two different views to provide this kind of response, Django REST Framework introduces a concept called a `ViewSet` - a Python class that then exposes multiple Django views, in the case we are concerned about, one to handle GET requests for a list of blog posts, and one to handle GET requests for a single blog post.

Let's re-open up this file in our code editor to add a `ViewSet`:

{% filename %}blog/views.py{% endfilename %}
```python
from django.shortcuts import render
from django.utils import timezone

from rest_framework import permissions
from rest_framework import viewsets

from .models import Post

def post_list(request):
    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
    return render(request, 'blog/post_list.html', {'posts': posts})


class PostViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows posts to be viewed or edited.
    """
    queryset = Post.objects.all().order_by('published_date')
    permission_classes = [permissions.IsAuthenticated]

```

Let's look at the properties of the `PostViewSet` class. Firstly, it inherits from the `ModelViewSet` class which is setup to handle Django Models specifically. We tell it which model to use by setting the `queryset` property, which we have set to all of the `Post` objects, ordered by the published date.

The `permission_classes` property, tells Django REST Framework who is allowed to access this web API. `permissions.isAuthenticated` is a standard Django REST Framework permission class that requires that the user be logged in - there are others that require the user to be an administrator, or you can create custom permission classes that do object level permission checks to make sure that you have permission to access the specific data.

The only thing we haven't defined is what data from the `Post` objects we should be returning in the endpoint. To do this, Django REST Framework uses `Serializer` classes to define which data from objects are returned from the API. We can add a definition for a serializer into the file:

{% filename %}blog/views.py{% endfilename %}
```python
from django.shortcuts import render
from django.utils import timezone

from rest_framework import permissions
from rest_framework import serializers
from rest_framework import viewsets

from .models import Post

def post_list(request):
    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
    return render(request, 'blog/post_list.html', {'posts': posts})


class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ['id', 'author', 'title', 'text', 'created_date', 'published_date']


class PostViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows posts to be viewed or edited.
    """
    queryset = Post.objects.all().order_by('published_date')
    permission_classes = [permissions.IsAuthenticated]
    serializer_class = PostSerializer

```

Here we are using the Django REST Framework `ModelSerializer` which takes advantage of the fact that it is serializing a Django model to make some inferences about what data is being serialized. Because of this, we are able to use configuration with the `Meta` class, to define which Django model we are serializing, and which fields we should serialize. Note that we have included the `id` field, which we did not explicitly specify on the Django model, but which Django adds for us automatically. Django REST Framework will inspect the `Post` model and use a sensible default for serializing the data for each field.

Now we have a complete `ViewSet` with a `Serializer` - but we are still not able to access this. To do that we have to map URLs to the views in the `ViewSet` much like we did for the Django views we defined earlier. Reopen the `blogs/urls.py` file and create a Django REST Framework `Router` to register the `ViewSet` against:

{% filename %}{{ warning_icon }} blog/urls.py{% endfilename %}
```python
from django.urls import include
from django.urls import path
from rest_framework import routers

from . import views

router = routers.DefaultRouter()
router.register(r'post', views.PostViewSet, basename="post-api")


urlpatterns = [
    path('', views.post_list, name='post_list'),
    path('post/<int:pk>/', views.post_detail, name='post_detail'),
    path('api/', include(router.urls)),
]
```

This will mount URLs for the `PostViewSet` under `api/post` for retrieving a list of posts and `api/post/<pk>` for retrieving individual posts. Run the server and go to http://127.0.0.1:8000/api/post to view the browsable Django REST Framework API showing the posts.
