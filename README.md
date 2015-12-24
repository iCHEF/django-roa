Django-ROA (Resource Oriented Architecture)
===========================================

**Use Django’s ORM to model remote API resources.**

Fork of original [David Larlet Django ROA lib]. Now ROA works directly
with an API like [Django Rest Framework]

**How does it works:** Each time a request is passed to the database,
the request is intercepted and transformed to an HTTP request to the
remote server with the right method (GET, POST, PUT or DELETE) given the
get\_resource\_url\_\* methods specified in the model’s definition.

Documentation
-------------

Initial documentation:

-   [Wiki home]
-   [Getting started with Django-ROA]
-   [Developing with Django-ROA]

Supported versions
------------------

[![Build Status]]

-   Django 1.4, 1.5, 1.6, 1.7
-   Python 2.6, 2.7

Installation
------------

``` {.sourceCode .bash}
$ pip install -e git+https://github.com/charles-vdulac/django-roa.git@master#egg=django_roa
```

Fork getting started
--------------------

If you have an API output like this (typical DRF output):

``` {.sourceCode .python}
# GET http://api.example.com/articles/
# HTTP 200 OK
# Content-Type: application/json
# Vary: Accept
# Allow: GET, POST, HEAD, OPTIONS

{
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 1,
            "headline": "John's first story",
            "pub_date": "2013-01-04",
            "reporter": {
                "id": 1,
                "account": {
                    "id": 1,
                    "email": "john@example.com"
                },
                "first_name": "John",
                "last_name": "Smith"
            }
        },
        ...
    ]
}
```

Your code will look like this:

``` {.sourceCode .python}
from django.db import models
from django_roa import Model as ROAModel

class Article(ROAModel):
    id = models.IntegerField(primary_key=True)  # don't forget it !
    headline = models.CharField(max_length=100)
    pub_date = models.DateField()
    reporter = models.ForeignKey(Reporter, related_name='articles')

    api_base_name = 'articles'

    @classmethod
    def serializer(cls):
        from .serializers import ArticleSerializer
        return ArticleSerializer

    @classmethod
    def get_resource_url_list(cls):
        return u'http://api.example.com/{base_name}/'.format(
            base_name=cls.api_base_name,
```

Setting Args
--------------------
If you want to set query args, put ROA_ARGS_NAMES_MAPPING as dict in your django setting file 
```
ROA_ARGS_NAMES_MAPPING = {
    'FILTER_':'filter_',  # filtre prefix
    'EXCLUDE_':'exclude_',  # exclude prefix
    'SEARCH_':'search',  # search parameter in url, EX: ?search=search_text
    'ORDER_BY':'order_by',  # order_by parameter in url, EX: ?order_by=order_by_key
    'LIMIT_START':'limit_start',  # slice start index
    'LIMIT_STOP':'limit_stop',  # slice stop index
    'FORMAT':'format',  # format parameter in url, EX: ?format=json
    'ROA_CUSTOM_ARGS':{},  # you can update all above parameter by this dict
}
```




  [David Larlet Django ROA lib]: http://code.larlet.fr/django-roa/src
  [Django Rest Framework]: http://www.django-rest-framework.org/
  [Wiki home]: http://code.larlet.fr/django-roa/wiki/Home
  [Getting started with Django-ROA]: http://code.larlet.fr/django-roa/wiki/GettingStarted#!getting-started-with-django-roa
  [Developing with Django-ROA]: http://code.larlet.fr/django-roa/wiki/Development#!developing-with-django-roa
  [Build Status]: https://travis-ci.org/charles-vdulac/django-roa.png?branch=master
  [![Build Status]]: https://travis-ci.org/charles-vdulac/django-roa