# Django REST Framework filtering

The quickest and most flexible way to add filtering to [Django REST Framework](https://www.django-rest-framework.org/) endpoints is by using the `django-filter` library.

To get started, install `django-filter` into your project.

{% include "/drf_filtering/instructions.md" %}

## Filtering our blog post API

So far in our API, we have had to either list all blog posts, or look at blog posts individually. With a filter, we are able to specify specific fields to filter the values for.

To do this, we have two options - we can either create an explicit `FilterSet` class, which explicitly defines which fields are filtered and how, or we can use a shortcut property on the `ViewSet` to define which fields we want to filter by. When we choose the latter option, a `FilterSet` class is still created, but it is handled automatically by Django Filter, and we don't have to define the class ourselves.

Let's re-open up this file in our code editor to add the properties to our `ViewSet`:

{% filename %}blog/views.py{% endfilename %}
```python
from django.shortcuts import render
from django.utils import timezone

from rest_framework import permissions
from rest_framework import serializers
from rest_framework import viewsets

from django_filters.rest_framework import DjangoFilterBackend

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
    filter_backends = [DjangoFilterBackend]
    filterset_fields = ['author']

```

Let's look at the new properties of the `PostViewSet` class. The `filter_backends` property, tells Django REST Framework what kind of filtering this `ViewSet` can do - in this case, we are using the Django Filter Backend, so Django REST Framework will defer to Django Filter to filter the objects we return. The `filterset_fields` property defines a list of fields that we want to be able to filter by, in this instance we have only specified `author` which will allow us to filter the returned blog posts by the primary key of the user that we are interested in.

> **Note** In Django Filter 1.1.0, this property is called `filter_fields` not `filterset_fields`, so if you are working on Kolibri, the former is required.

To test that this filtering is working, run the server and go to http://127.0.0.1:8000/api/post?author=1 to view the browsable Django REST Framework API showing the posts, filtered to the user with `id` of `1`.

## More complex filters

Having to know the primary key of an author can be sufficient for finding the right results - however, sometimes it might be more useful to be able to do something a bit more complex - like looking up blog posts by matching against the author's name. To do this, we will need to create our own `FilterSet` to create a more complex lookup expression - the `filterset_fields` do the equivalent of `author=<value>` in a Django Queryset filter, so we need to construct a more complex query.

If we were to do this filter in a Django queryset we would do something like `Post.objects.filter(author__username__contains=<value>)` - here we are doing two things in the filter lookup expression - first we are traversing the foreign key to `author` using the `__username` so that we can do a match against the author's username and not just their `id` value. With the `__contains` we are adding a lookup expression to say we are not looking for an exact match, but rather just looking for a username that contains the value. So if we looked up with a value of `tim` then we would match against usernames `tinytim` and `astima`.

Let's define a `FilterSet` to handle this filtering (while also retaining our previous foreign key filtering on `author`):

{% filename %}blog/views.py{% endfilename %}
```python
from django.shortcuts import render
from django.utils import timezone

from rest_framework import permissions
from rest_framework import serializers
from rest_framework import viewsets

from django_filters import FilterSet
from django_filters.rest_framework import DjangoFilterBackend
from django_filters.rest_framework import CharFilter

from .models import Post

def post_list(request):
    posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
    return render(request, 'blog/post_list.html', {'posts': posts})


class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ['id', 'author', 'title', 'text', 'created_date', 'published_date']


class PostFilter(FilterSet):
    username = CharFilter(field_name="author__username", lookup_expr="contains")

    class Meta:
        model = Post
        fields = ['author', 'username']


class PostViewSet(viewsets.ModelViewSet):
    """
    API endpoint that allows posts to be viewed or edited.
    """
    queryset = Post.objects.all().order_by('published_date')
    permission_classes = [permissions.IsAuthenticated]
    serializer_class = PostSerializer
    filter_backends = [DjangoFilterBackend]
    filterset_class = PostFilter

```

Instead of `filterset_fields` we have now defined `filterset_class` and are point to our new `PostFilter` class - these two options are mutually exclusive, you can have one or the other, but not both.

> **Note** In Django Filter 1.1.0, the property to set the filter set is called `filter_class` not `filterset_class`, so if you are working on Kolibri, the former is required.

Similarly to the `PostSerializer`, the `PostFilter` defines a `class Meta` that shows which model is being referred to, and which fields it should filter by. Because `author` is a field directly on the `Post` model, we do not have to specify anything further - but `username` is not a field of the `Post` model, so we have to explicitly define the filtering.

If we look at that in more detail we see: `username = CharFilter(field_name="author__username", lookup_expr="contains")` as the definition. `CharFilter` is a filter used for filtering by string values. We specify the `field_name`, which in this case is the expression for traversing the `author` foreign key and filtering by the `username` - `author__username`.

> **Note** In Django Filter 1.1.0, the property to set the field name is called `name` not `field_name`, so if you are working on Kolibri, the former is required.

Lastly, the `lookup_expr` argument sets the type of lookup that we will be using when we use this filter - in this case we set it as `contains` so that we can lookup for any post by an author whose username contains some specific characters.

To test that this filtering is working, run the server and go to http://127.0.0.1:8000/api/post?username=<your username> to view the browsable Django REST Framework API showing the posts, filtered to your user.
