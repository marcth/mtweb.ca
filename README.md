# mtweb
Learning Django


## Initial Install

```
mkdir -p ~/Python && cd ~/Python

#Empty Repo with README.md and .gitignore template: Python
git clone git@github.com:marcth/mtweb.ca.git

cd mtweb.ca
```
---
**Note**: Make sure the terminal is not an activated Python virtual environment. If so issue the command `deactivate`.

---

```
python -m venv venv

source venv/bin/activate

mkdir -p ~/Python/mtweb.ca/src && cd src


echo "Django<4.2" > requirements.txt

../venv/bin/python -m pip install -r requirements.txt

django-admin startproject mtweb
```


### Configure Django Static Files
```
mkdir -p ~/Python/mtweb.ca/src/static
mkdir -p ~/Python/mtweb.ca/local-cdn/static
```


Edit `./.gitignore` and prepend the the following line to the top of the file:
```
local-cdn/
```


Edit `./src/settings.py` and change:
```
STATIC_URL = 'static/'
```
to:
```
STATIC_URL = 'static/'

STATICFILES_DIRS = [
    BASE_DIR / "static"
]

STATIC_ROOT =  BASE_DIR .parent / "local-cdn" / "static"
```


Edit `./src/mtweb/mtweb/urls.py` and add the following lines:
```
from django.conf import settings

...

if settings.DEBUG:
    #Do not run the following code in production!
    from django.conf.urls.static import static

    urlpatterns += static(
        settings.STATIC_URL, 
        document_root=settings.STATIC_ROOT
    )
```


You can test the static file configurations by issuing the command:
```
python ~/Python/mtweb.ca/src/manage.py collectstatic
```

The output should be similar to: `130 static files copied to '~/Python/mtweb.ca/local-cdn/static'.`



## Configure Django Template and Home View
Edit `./src/mtweb/settings.py` and change:
```
TEMPLATES = [
    {
...

        'DIRS': [],
        
...
    },
]
```
to:
```
TEMPLATES = [
    {
...

        'DIRS': [BASE_DIR / 'templates'],

...
    },
]
```


Create the templates directory: `mkdir -p ~/Python/mtweb.ca/src/templates/pages`


In the `templates` directory, create the file `./src/templates/layout.html` and add:
```
<!DOCTYPE html>
<html lang=""en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">

    <title>{% block head_title %}mtweb Home{% endblock %}</title>
</head>

<body>
    <div>
        {% block content %}{%endblock %}
    </div>
</body>
</html>
```


In the `templates/pages` directory, create the file `./src/templates/pages/home.html` and add:
```
{% extends "layout.html" %}

{% block content %}
    <h1>Hello World!</h1>
{% endblock %}
```


Create a file called `views.py` in `./src/mtweb`  (`touch ~/Python/mtweb.ca/src/mtweb/views.py`) and add:
```
from django.shortcuts import render


def home_view(request):
    return render(request, "pages/home.html", {})
```


Edit `./src/mtweb/urls.py` and add the following lines:
```
...

from . import views

urlpatterns = [
    path('', views.home_view, name='home'),
...
]

```


Issue the following command to run the development server:
```
cd ~/Python/mtweb.ca/src && python manage.py runserver
```


Test in locally in browser at: [http://127.0.0.1:8000/](http://127.0.0.1:8000/).




## Initialize Tailwind CSS
---
**NOTES**

***Note**: prolly smarter to install them via the Node Version Manager--next time!* 

If do not have NodeJS or NPM installed and are using Ubuntu, avoid installing them from the default Repositories. Instead install them via a NodeSource PPA as documented by DigitalOcean:

[How To Install Node.js on Ubuntu 22.04 > Option 2 — Installing Node.js with Apt Using a NodeSource PPA](https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-22-04#option-2-installing-node-js-with-apt-using-a-nodesource-ppa)

```
~$ node --version
v18.16.0

~$ npm --version
9.5.1
```

---


Create a `head` directory under the `./src/templates/` and create a file called `css.html`:
```
mkdir -p ~/Python/mtweb.ca/src/templates/head`
touch ~/Python/mtweb.ca/src/templates/head/css.html
```


Set the following contents to `./src/templates/head/css.html`:
```
{% load static %}
<link rel="stylesheet" href="{% static 'css/styles.css' %}">

```


Create the static folders `src` and `css` under `./src/static`:
```
mkdir ~/Python/mtweb.ca/src/static/src/
mkdir ~/Python/mtweb.ca/src/static/css/

touch ~/Python/mtweb.ca/src/static/css/styles.css
```


Create `tailwind.css` under `~/Python/mtweb.ca/src/static/src/` and add the following base configurations:
```
@tailwind base;
@tailwind component;
@tailwind utilities;
```


Excute the command:

```
cd ~/Python/metweb.ca/

npm install -D tailwindcss
```


Edit `./.gitignore` and ensure the following line exists in the file:
```
node_modules/
```


Edit `./package.json` and change:
```
{
  "devDependencies": {
    "tailwindcss": "^3.3.2"
  }
}

```
with (ensure to keep the version of `tailwindcss`, in this case `^3.3.2`):
```
{
  "private": true,
  "type": "module",
  "scripts": {
      "dev": "tailwindcss -i src/static/src/tailwind.css -o src/static/css/styles.css --watch"
  },
  "devDependencies": {
    "tailwindcss": "^3.3.2"
  }
}
```
---
**@TODO**: `./package.json` isn't configured correctly.

---


Issue following command to initialize a basic `tailwind.config.js` configuration file:
```
cd ~/Python/metweb.ca/
npx tailwindcss init
```


Edit `~/Python/metweb.ca/tailwind.config.js` and add to the `content` section as follows:
```
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: ["./src/**/*.{html,js}"],
  theme: {
    extend: {},
  },
  plugins: [],
}
```


In `./src/templates/layout.html` add the following line within the `<head>` tag:
```
...
    {% include 'head/css.html' %}
</head>
...
```
---
**EXTRAS**

```
npm install -D @tailwindcss/forms
npm install -D @tailwindcss/typography

npm install --save-dev tailwindcss-font-inter
```
Add the following to `tailwind.config.js`

```
module.exports = {
...
  plugins: [
    require('@tailwindcss/typography'),
    require('@tailwindcss/forms'),
...
  ],
}
```

---
