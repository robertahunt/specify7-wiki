Directory
==========

In which are described points of interest in the source code tree.

## specifyweb/ ##
* **businessrules/** - place for business rules to live
* **context/** - the AppContext subsystem. Views, etc.
* **expresss_search/** - search subsystem.
* **hibernateboolsbackend/** - make Django understand how Hibernate stores bools
* **specify/** - the data API
* **frontend/** - front-end static files
* **manage.py** - the Django management script
* **r.js** - the `requirejs` library's optimizer
* **settings.py** - the main Django settings for the project
* **specify_settings.py** - settings that are more specific to this project
* **todo.org** - where I capture notes about stuff that needs doing
* **todo_overview.org** - higher level stuff that needs doing
* **urls.py** - tells Django how to handle different URLs

## specifyweb/context ##
* **data/** - basically mocked-up data for testing purposes
* **app_resource.py** - logic for finding App Resources
* **middleware.py** - adds information about the specify user to incoming requests
* **schema_localization.py** - provide the proper SL to the frontend
* **test.py** - tests should go here
* **testsviews.py** - serves mock views for testing purposes
* **testurls.py** - provides urls to access the mocked views
* **urls.py** - defines the urls for the app context subsystem
* **views.py** - defines the resources that are provided by this subsystem

## specifyweb/express_search ##
* **related.py** - base class for related search
* **related_search.py** - defines specific related searches
* **views.py** - implements the express search mechanism

## specifyweb/specify ##
* **fixtures/** - data for tests
* **api.py** - implements the RESTful business data API
* **api_tests.py** - tests for the above
* **authbackend.py** - tells Django how to authenticate using the exist Specify users
* **autonumbering.py** - what it says on the tin
* **encryption.py** - handles Specify user password crypto
* **filter_by_col.py** - modules for filtering resources by the collection logged in
* **models.py** - sets up Django ORM with the Specify datamodel
* **selenium_tests.py** - browser automation testing
* **tests.py** - test suite entry point
* **uiformatters.py** - supports autonumbering mechanism
* **views.py** - a few non-business data resource end points

## specifyweb/frontend ##
* **static/** - statically served files, i.e. the complete front end
* **templates/** - Django templates, minimally used.
* **urls.py** - just provide a URL that serves the HTML container for the front end

### specifyweb/frontend/static ###
* **css/** - stylesheets
* **html/** - mostly templates used by the front end to build the UI
* **img/** - UI images
* **js/** - the frontend code

#### specifyweb/frontend/static/js ####
* **tests/** - frontend tests
* **vendor/** - javascript libs
