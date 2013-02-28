This page is intended to present an overview of the front-end code for the Specify web application.

The Specify web app front-end is essentially a Javascript application which presents a user
interface for the Specify collection management system. The Javascript and various static resources
are served from a back-end server which also provides a REST api to the underlying database. This api
is directly utilized by the front-end to provide the user interface.

Third-Party Technology
----------------------

A few Javascript libraries are utilized in the front-end. A basic understanding of these is necessary
to understand the Specify web app code.

### JQuery

[JQuery](http://docs.jquery.com/Main_Page) is heavily relied upon in
this application. It is used for DOM manipulation of the user visible
web pages that constitute the UI. Additionally, various resources
(view definitions, the specify datamodel, schema localization, etc.)
are represented by XML exposed by the server. JQuery is used in
querying these data structures.  Finally, heavy use is made of the
[JQuery deferred
object](http://api.jquery.com/category/deferred-object/) in order to
allow concurrent fetching of data and rendering of UI. An
understanding of this
[paradigm](http://en.wikipedia.org/wiki/Promise_(programming)) is
essential for working with this code base.

The ajax requests to the back-end server are also brokered by JQuery.


### RequireJS

The web app is of sufficient complexity that it is necessary to organize the code into separate
modules encapsulating logically separate concerns. Currently lacking a built-in module system,
Javascript benefits from the use of libraries to provide this functionality. I have
chosen [RequireJS](http://requirejs.org/) for use in this project.

For a cursory understanding of the code base it is probably sufficient to understand how a module
definition is structured when using RequireJS. Most of the javascript files in this project take the
following form:

```javascript
define(['dependency1', 'dependency2', ...], function(localNameForDep1, localNameForDep2, ...) {
   ....
   module setup and local definitions
   ....
   return exported functionality
});
```

For example,

```javascript
define(['jquery', 'mainview', 'specifyapi'], function($, MainView, api) {

});
```

might, in a language with builtin modules, be the equivalent of:

```python
import jquery as $
import mainview as MainView
import specifyapi as api
```

Given this structure, RequireJS ensures that all the necessary `.js`  files are loaded
by the browser before the code is interpreted.

### UnderscoreJS

[UnderscoreJS](http://documentcloud.github.com/underscore/) is a utility library that
abstracts away many of the rough edges of Javascript in much the same way that JQuery abstracts away
complexities in the browser DOM interface. It also makes use of the familiar JQuery style whereby an
object is *wrapped* to provide extended functionality. Thus, if ` foo ` is an object,
then ` _(foo) ` is a wrapped object with special sauce.

### Backbone.js

Resources that are fetched from the back-end must be represented in data structures in
Javascript. These structures then become associated with various UI elements in the
DOM. [Backbone](http://documentcloud.github.com/backbone/) provides a standard set of
abstractions based around a MVC pattern that can be used to structure these data and UI
interactions. The main abstractions provided and their use in this application are as follows:

* Models
      Models represent groups of related data that are generally synchronized with objects on the
      back end. In this project they are mostly used to represent instances of Specify datamodel
      objects. E.g. CollectionObjects, Accessions, Determinations, etc.

      The advantage of using Backbone here is that it provides out-of-the-box underpinnings for tasks like
      synchronizing with the server, parsing objects, observer pattern, etc.

      Sadly, the term *Model* is highly overloaded in this project and is used more-or-less
      interchangeably along with *Resource* to mean a variety of things. The situation for *View*
      and *Form* is similarly fraught.

* Collections
      Essentially these represent collections of Models. Predominately these are used
      to represent one-to-many collections, record sets, querycbx search results and the like.

* Views
      Views abstract the association of data with user interface. One thing to be aware of here is that I
      use the ` View.render() ` method somewhat differently than the Backbone documentation
      describes. Where the more standard semantics for that method imply that it may be called repeatedly
      to redraw the UI if the underlying model changes, I have adopted a more single-use style in which
      the method sets up the DOM for the particular UI and events are attached which modify it 'in-place'
      when changes occur

Architecture and Flow
---------------------

The web app is essentially a system which takes a URI pointing to a resource and renders a form
based UI representing the data in that resource. Behaviors are attached to the rendered form that
permit the data to be modified and relationships to be navigated.

![](images/specify_webapp_frontend.png?raw=true)

The figure presents the architecture of the front-end in simplified schematic form. Rendering
proceeds mainly from top to bottom.

*   Visiting a particular resource corresponds to accessing a particular
    URL. The `main.js` module examines the URL to determine the preliminary information
    needed to begin rendering the view. In this case, we will be producing a form for a single
    CollectionObject with primary key 12.

*   Given the type of the object, `schema.js` returns a data structure representing the
    schema information for that type (i.e. fields, relationships, default forms, etc.). This data
    structure is sometimes given the name ` specifyModel ` in the code to differentiate
    it from what Backbone calls models, although the class itself is `schema.Model`.

*   With the specifyModel and the object's id, there are a family of modules, `*api.js`,
    which conspire to retrieve the associated data from the back-end webservice. The retrieved data
    will eventually become available in the form of linked Backbone models and collections. The code
    base uses *resource* and *model* somewhat interchangeably to refer to these structures.

#### Aside regarding deferreds and `rget`

Because the resources are ultimately being retrieved asynchronously from the server, the api
code in general returns deferred promises so that the UI can continue rendering and other
resources can be fetched concurrently. The resources that are returned are specialized Backbone
models which implement a `rget(fieldName)` method. This method extends the semantics
of the regular models' ` get ` methods to return deferreds. This is necessary because
a requested field may exist as a related object or even be a few steps down in a related object
tree, e.g. `collector.agent.lastName`. If the related object(s) has not been previously
fetched, we must request it from the server. This means a promise is the best that we can
return.

*   Once the schema info is available and the resource fetch is underway, control flows into
    the `populateform.js` module. First the schema info is passed to the `
    specifyform.js ` module which determines the appropriate view for the object and parses
    the XML view definitions to produce a skeleton DOM which contains all the structure and
    information needed to produce the finished view. This process is independent of the specific
    data in the given resource and can happen concurrently with the fetch. This also means that the
    constructed DOMs could be serialized and replace the existing form definitions in future
    versions.

*   The DOM is then supplemented with information from the schema localization tables. This step is
    also independent of the resource data fetch.

*   Having obtained the DOM structure for the view, the populateform module will visit the various
    UI elements within. These elements carry attributes which determine what sort of UI they
    require, what fields they represent, whether they are read-only or required, etc. Populateform
    essentially dispatches on the UI type for each element to various other modules which are
    responsible for generating the corresponding UI. The figure sketches out the Subview, querycbx,
    picklist and uifield modules indicating how the first three may utilize the backend api to
    retrieve extra resources (pick list definitions, etc.) as necessary. The dashed red arrow
    illustrates how the Subview module makes recursive use of populateform. Not indicated in the
    figure are the various plugin modules that populateform may dispatch to via
    the `specifyplugins.js` module. All of the UI producing modules and plugins should be
    implemented as Backbone views in order to maintain a consistent interface and structure. A few
    of the existing modules need to be refactored to meet this goal.
