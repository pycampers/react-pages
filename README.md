# React Pages
#### React Pages lets you use React freely in non-node environments, like Django.

___sans_ React Pages__<br>
[![Image](https://cdn-images-1.medium.com/max/800/1*B-kFzr8f4OF9rr6Ueg-UFg.png)](https://hackernoon.com/reconciling-djangos-mvc-templates-with-react-components-3aa986cf510a)


## Features

- Zero Configuration required.
   Mostly thanks to
   [create-react-app](https://github.com/facebook/create-react-app).
- [Custom react scripts](https://github.com/kitze/custom-react-scripts)
   inbuilt.
- Allows cross-page imports
   (create-react-app [doesn't allow this](https://github.com/facebook/create-react-app/issues/834)).
- Ready-to-serve production builds with the proper paths.
    (using `--static-url` option)
- Natively use react in django.
- Go from development to production with ease.
- Donwloads npm packages only once, per virtualenv. <br>
    This means creating a new project is **really fast**
    (at the cost of installation time).
- Respect `NODE_ENV` var wherever possible and automatically update it
    (see [.env](react_pages/nodejs/.env#L28)).
- Supports sass-loader, essential for
    [material-components-web](https://github.com/material-components/material-components-web).
- Parallel-ized builds.

## Terminology

#### Project
The project contains some basic scaffolding for a project.<br>

Unlike traditional create-react-app projects,
 react-pages doesn't require any npm pacakges to be installed.

The inital package.json file is literally empty and is
 present as a scaffolding, nothing more.

```
└── my_project
    ├── package.json
    ├── .env
    ├── .gitignore
    ├── public
    │   ├── favicon.ico
    │   ├── index.html
    │   └── manifest.json
    <pages>
```

#### Page:

A page contains basic scaffolding for a web-page.

You are allowed to import files from other pages in your projects,
 leveraging true re-usability of components in a project.

```
└── my_page
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    └── registerServiceWorker.js
```

## Install

`pip install react-pages`

License: MIT License (MIT)<br>
Requires: Python >=3.6

---

If you don't have node, <br>

For bash/zsh, use [nvm](https://github.com/creationix/nvm#installation).

For fish shell, you can use `fisher fnm`
([get fisher](https://github.com/fisherman/fisherman)).

Once you have npm/node, react pages will work as expected.

*TODO: make react-pages automatically install node*

## Commands

```sh
$ react-pages project my_project # create a "project"

$ cd my_project # don't forget to do this!

$ react-pages page my_page # create a "page"

$ react-pages develop # development

$ react-pages deploy # production


# Open `./my_project/build/my_page/index.html` in browser


$ react-pages runserver # django runserver alternative

$ react-pages --build-cache # rebuild the cache

$ react-pages --rm # clear the cache

$ react-pages --cache # ouput the cache dir
```

## Django Integration

### Remember to use `react-pages runserver` instead of `manage.py runserver`!
(This was done to remove the manual build step).

### Setup

__settings.py__
```
INSTALLED_APPS = [
    ...
    'react_pages',
    ...
]

# specify the react-pages project directory
REACT_PAGES_PROJECT_DIR = os.path.join(BASE_DIR, 'my_project')

STATICFILES_DIRS = [
    ...
    os.path.join(REACT_PAGES_PROJECT_DIR, 'build')  # mark the build dir as a static file dir
    ...
]
```

### Usage

__template.html__
```
{% load react_pages %}
...
{% render_react_page 'my_page' %}
...
```


That's it!<br>
React Pages will pick-up the "my_page" page from "my_project"
 project and do the necessary work to transpile react JSX.

*TODO: For production, just put `DEBUG=False` in `settings.py` and relax*

### Django Context

You can pass django template context varialbes like so -

__views.py__
```python
context['py_var'] = [1, 2, 3]
```

__template.html__
```html
{% render_react_page 'my_page' js_var=py_var %}
```

__my_page/App.js__
```js
console.log(js_var);
```

**Note: These must be JSON serializable or JSON serialized.**

### Class Based View
__views.py__
```python
from react_pages.views import ReactPageView

class MyPageView(ReactPageView):
    page_name = 'my_page'
```

__urls.py__
```
urlpatterns = [
    ...
    path('my_page/', views.MyPageView.as_view(), name="my page"),
]
```

when you go over to 'my_page/' url,
you'll see the react page rendered in its full glory!

To pass the a context to JS, define a `get_js_context()` method

__views.py__
```python
class MyPageView(ReactPageView):
    page_name = 'my_page'

    def get_js_context(self):
        return {'js_var': 'Hello!'}
```

__my_page/App.js__
```js
console.log(js_var);
```

### Django Forms

__views.py__
```python
from .forms import MyAwesomeForm # Any ol' Django Form
from react_pages.views import ReactPagesFormView


class MyFormView(ReactPagesFormView):
    form_class = MyAwesomeForm
    page_name = "my_page"
```

__my_page/App.js__
```js
import React, { Component } from 'react';


// see the magic in console!
console.log(csrf_token);
console.log(form);

export default class App extends Component {
    render() {
        return (
            <form
                dangerouslySetInnerHTML={{
                    __html: csrf_token.as_html + form.as_html
                }}
            />
        );
    }
}

```


## Existing projects

React Pages will automatically patch itsef into any existing project,
that was created using `create-react-app`.

Just run `react-pages project .` from your project directory!

Projects not using `create-react-app` will probably work,
 but no guarantees can be made.

---

<a href="https://www.buymeacoffee.com/u75YezVri" target="_blank"><img src="https://www.buymeacoffee.com/assets/img/custom_images/black_img.png" alt="Buy Me A Coffee" style="height: auto !important;width: auto !important;" ></a>

[🐍🏕️](http://www.pycampers.com/)

