URL Routing
===========

The Specify Webapp is for the most part a [single-page application][]
where the UI all exists as a single web page. Nevertheless it is
useful to allow linking (e.g. bookmarks, emailable links) and back-
and forward-button functionality by having different views represented
by different URLs. This means that multiple URLs must all map to the
same web page, the one that is the application. This is at odds with
the traditional idea that there is a one-to-one correspondence between
URLs and web pages. When coupled with Django on the backend which is
usually used for multi-page applications, things become doubly
confused.

Normally Django maps URLs to Django views which are dynamically
rendered to HTML on the backend when a user's browser requests a
particular URL. Other frameworks are similar. In Django, these
mappings are ordinarily spelled out in `url.py` files. For our
single-page application, we need to map an entire family of URLs
(those that the frontend is to handle) to a single Django view that
_is_ the frontend (or will become it, when the HTML and JS
is executed by the browser). For the Specify Webapp, this mapping
occurs in the top level `urls.py` and `frontend/urls.py` files.

The top level `urls.py` contains the line:

    url(r'^specify/', include('frontend.urls')),

This has the effect of telling Django that all URL paths beginning
with 'specify/' should be mapped by `frontend/urls.py` which says:

    (r'', login_required(TemplateView.as_view(template_name="form.html"))),

The empty regexp at the beginning matches anything, meaning Django
will serve `form.html` to any client asking for a URL of the form
'http://HOSTNAME/specify/*'. Since `form.html` bootstraps the web
application, this achieves the desired end as far the backend is
concerned.

In short, the `url.py` files conspire to make Django give up all
control of URLs of the form 'http://HOST/specify/*' to the web
application frontend to do with as it likes. The frontend's
interpretation of the URLs is spelled out in `specifyapp.js` as a
mapping provided to the Backbone.js router library. For example:

    'view/:model/new/*splat'      : 'newResource',

means that URLs of the form '.../specify/view/MODELNAME/new/*' are to
be handled by a function called `newResource`.

Once frontend has begun its work, it must in general communicate with
the backend to obtain or modify business data or retrieve various
application resources. These communications are via URLs outside the
'/specify/...' family. For example, '/express_search/...' for the
express search API. The backend obviously must understand the meanings
of these URLs, and does so by means of other definitions in the
various `urls.py` files.

A final wrinkle is the log-in and log-out pages which, at present, are
simple server generated forms separate from the main frontend. They
have their own URLs defined in the Django `urls.py` file.

[single-page application]: http://en.wikipedia.org/wiki/Single-page_application

