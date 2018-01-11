# RESTful API with Django Tastypie.

### Install Django and the required dependencies
> pip install Django==1.11.9

> pip install django-tastypie

> pip install defusedxml

> pip install lxml

###  Django project
`INSTALLED_APPS` section in settings.py:

```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    'whatever',
]
```
SQLite (or your RDBMS of choice) in settings.py:
```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'test.db'),
    }
}
```

models.py file:
```
from django.db import models


class Whatever(models.Model):
    title = models.CharField(max_length=200)
    body = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```
### Django Shell and populate the database
```
$ python manage.py shell
>>> from whatever.models import Whatever
>>> w = Whatever(title="What Am I Good At?", body="What am I good at? What is my talent? What makes me stand out? These are the questions we ask ourselves over and over again and somehow can not seem to come up with the perfect answer. This is because we are blinded, we are blinded by our own bias on who we are and what we should be. But discovering the answers to these questions is crucial in branding yourself.")
>>> w.save()

>>> w = Whatever(title="Charting Best Practices: Proper Data Visualization", body="Charting data and determining business progress is an important part of measuring success. From recording financial statistics to webpage visitor tracking, finding the best practices for charting your data is vastly important for your company’s success. Here is a look at five charting best practices for optimal data visualization and analysis.")
>>> w.save()

>>> w = Whatever(title="Understand Your Support System Better With Sentiment Analysis", body="There’s more to evaluating success than monitoring your bottom line. While analyzing your support system on a macro level helps to ensure your costs are going down and earnings are rising, taking a micro approach to your business gives you a thorough appreciation of your business’ performance. Sentiment analysis helps you to clearly see whether your business practices are leading to higher customer satisfaction, or if you’re on the verge of running clients away.")
>>> w.save()
```

### Tastypie Setup
new file in App called api.py:
```
from tastypie.resources import ModelResource
from tastypie.constants import ALL

from whatever.models import Whatever


class WhateverResource(ModelResource):
    class Meta:
        queryset = Whatever.objects.all()
        resource_name = 'whatever'
        filtering = {'title': ALL}
```
urls.py:
```
from django.conf.urls import url, include
from django.contrib import admin

from django19.api import WhateverResource

whatever_resource = WhateverResource()

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^api/', include(whatever_resource.urls)),
]
```
> Navigate to http://localhost:8000/api/whatever/?format=json to get the data in JSON format

> Navigate to http://localhost:8000/api/whatever/?format=xml to get the data in XML format

filter we put on the WhateverResource class:
```
filtering = {'title': ALL}
```

 filter the objects by title. Try out various keywords: 
> http://localhost:8000/api/whatever/?format=json&title__contains=what

> http://localhost:8000/api/whatever/?format=json&title__contains=test


Official Dooc: http://tastypieapi.org/ 
