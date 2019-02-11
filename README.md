# Crispy_forms

# FIRST CREATE PROJECT

>>mkdir test_project

>>cd test_project

>>virtualenv -p python3 .

>>source bin/activate

>>pip install django

>>pip install --upgrade django-crispy-forms

>>mkdir src

>>cd src

>>django-admin startproject myproject .

>>python manage.py startapp people

>>python manage.py runserver


# 2.EDIT myproject/settings.py

```
INSTALLED_APPS = [
    'people.apps.PeopleConfig',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'crispy_forms',
    'people',
]
```

```

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
       'DIRS': [os.path.join(BASE_DIR,'templates')],
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
# 3.ADD FIELD TO THE MODEL: people/models.py

```
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=130)
    email = models.EmailField(blank=True)
    job_title = models.CharField(max_length=30, blank=True)
    bio = models.TextField(blank=True)
```

# 4.ADD DATA TO forms FILE TO THE PEOPLE APPS: people/forms.py

```
from django import forms
from crispy_forms.helper import FormHelper
from crispy_forms.layout import Submit
from people.models import Person

class PersonForm(forms.ModelForm):
    class Meta:
        model = Person
        fields = ('name', 'email', 'job_title', 'bio')

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.helper = FormHelper()
        self.helper.form_method = 'post'
        self.helper.add_input(Submit('submit', 'Save person'))
```

# 5.ADD VIEW: people/views.py

```
from django.views.generic import ListView, CreateView, UpdateView
from django.urls import reverse_lazy

from people.models import Person
from people.forms import PersonForm


class PersonListView(ListView):
    model = Person
    context_object_name = 'people'


class PersonCreateView(CreateView):
    model = Person
    fields = ('name', 'email', 'job_title', 'bio')
    success_url = reverse_lazy('person_list')


class PersonUpdateView(UpdateView):
    model = Person
    form_class = PersonForm
    template_name = 'people/person_update_form.html'
    success_url = reverse_lazy('person_list')
```
# 6. ADD TEMPLATES FOLDER: templates/people/

>>tourch templates/base.html

>>tourch templates/people/person_forms.html

>>tourch templates/people/person_list.html

>>tourch templates/people/person_update_form.html

# 7. ADD DATA OF THOSE TEMPLATES:

>> templates/base.html:
```
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.3/css/bootstrap.min.css" integrity="sha384-MCw98/SFnGE8fJT3GXwEOngsV7Zt27NXFoaoApmYm81iuXoPkFOJwJ8ERdknLPMO" crossorigin="anonymous">
    <title>Django People</title>
  </head>
  <body>
    <div class="container">
      <div class="row justify-content-center">
        <div class="col-8">
          <h1 class="mt-2">Django People</h1>
          <hr class="mt-0 mb-4">
          {% block content %}
          {% endblock %}
        </div>
      </div>
    </div>
  </body>
</html>
```

>> templates/people/person_list.htm

```
{% extends 'base.html' %}

{% block content %}
  <p>
    <a href="{% url 'person_add' %}" class="btn btn-primary">Add person</a>
  </p>
  <table class="table table-bordered">
    <thead>
      <tr>
        <th>Name</th>
        <th>Email</th>
        <th>Job Title</th>
      </tr>
    </thead>
    <tbody>
      {% for person in people %}
        <tr>
          <td><a href="{% url 'person_edit' person.pk %}">{{ person.name }}</a></td>
          <td>{{ person.email }}</td>
          <td>{{ person.job_title }}</td>
        </tr>
      {% empty %}
        <tr class="table-active">
          <td colspan="3">No data</td>
        </tr>
      {% endfor %}
    </tbody>
  </table>
{% endblock %}
```

>>templates/people/people_update_form.html

```
{% extends 'base.html' %}

{% load crispy_forms_tags %}

{% block content %}
  {% crispy form %}
{% endblock %}
```
# 8.EDIT URLS: mysite/urls.py

````
from django.contrib import admin
from django.urls import path
from people.views import PersonListView, PersonCreateView, PersonUpdateView


urlpatterns = [
	path('admin/', admin.site.urls),
    path('', PersonListView.as_view(), name='person_list'),
    path('add/', PersonCreateView.as_view(), name='person_add'),
    path('<int:pk>/edit/', PersonUpdateView.as_view(), name='person_edit'),
]
````
