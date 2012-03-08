# Welcome to the djangospecify wiki!

The purpose of this page is to provide an overview of the code for those who would like to understand its workings.

## Architecture in broad strokes

The project breaks down into two main components: a _back end_ that basically surfaces the Specify datamodel as a set of resources according to REST principles and a _front end_ that provides a UI for interacting with those resources.

### The back end

The role of the back end is three-fold. Most important it provides RESTful access to the underlying database and implements business rules necessary to maintain data integrity. It is also responsible for authentication and for serving the static resources needed by the front end.

The back end is implemented in Python and makes use of the Django framework for ORM and authentication subsystems. The TastyPie library for Django is used to facilitate the implementation of the API resources.

* **The ORM layer:**
    The ORM is built on the foundation provided by Django. Django represents a database schema through a set of classes where basically each database table is modeled by a Python class, called appropriately enough, a _Model_. Here is an example from the [Django Documentation](https://docs.djangoproject.com/en/1.3/topics/db/models/):

        class Musician(models.Model):
            first_name = models.CharField(max_length=50)
            last_name = models.CharField(max_length=50)
            instrument = models.CharField(max_length=100)

        class Album(models.Model):
            artist = models.ForeignKey(Musician)
            name = models.CharField(max_length=100)
            release_date = models.DateField()
            num_stars = models.IntegerField()

    In the case of Specify there are about 170 tables in the schema. Writing a class for each one is possible, but there is a better way. The existing thick client makes use of an automatically generated `specify_datamodel.xml' file which completely describes the schema, including named implicit (i.e. many-to-many) and reverse relationships that cannot be discovered through pure database introspection. This file can be leveraged to build the Django models automatically.

    The basic idea is to use the dynamic nature of Python to produce Model classes programmatically based on the XML description. For example, the above classes in Python could be rendered as follows:

        Musician = type('Musician', (models.Model,), {
            'first_name' : models.CharField(max_length=50),
            'last_name' : models.CharField(max_length=50),
            'instrument' : models.CharField(max_length=100)})

        Album = type('Album', ....

    If the literals are replaced with variables and the field objects instantiated by function dispatch, it is possible to straight forwardly turn the XML description of the schema into a set of objects that Django can understand.

    The code that does so is in [djangospecify/specify/models.py](https://github.com/benanhalt/djangospecify/blob/master/specify/models.py).

* **The API layer:**
    The API generation is based around the [TastyPie](https://github.com/toastdriven/django-tastypie) library. The idea is much the same as before. Traditionally each resource would be described to TastyPie by a Python class. E.g. from [the docs](http://django-tastypie.readthedocs.org/en/latest/fields.html):

        class PersonResource(Resource):
            name = fields.CharField(attribute='name')
            age = fields.IntegerField(attribute='years_old', null=True)
            created = fields.DateTimeField(readonly=True, default=utils.now)
            is_active = fields.BooleanField(default=True)
            profile = fields.ToOneField(ProfileResource, 'profile')
            notes = fields.ToManyField(NoteResource, 'notes', full=True)

    So again the strategy is dynamic class generation. This code lives at [djangospecify/specify/api.py](https://github.com/benanhalt/djangospecify/blob/master/specify/api.py). The code is complicated somewhat by subclassing the TastyPie resource implementations to provide customized behavior, but the module remains quite concise and manageable.

* **Authentication:**
     It is desirable that the authentication system utilize the same user credentials as the current thick client. That has been achieved by implementing the same decryption protocol that is used to challenge user passwords against the encrypted versions stored in the `specifyusers` table. The relevant code is in [djangospecify/specify/encryption.py](https://github.com/benanhalt/djangospecify/blob/master/specify/encryption.py). And the code which interfaces it to the Django authentication system is found in [djangospecify/specify/authbackend.py](https://github.com/benanhalt/djangospecify/blob/master/specify/authbackend.py).

### The front end

The front end will be the more complicated of the two major components since it must interact with the most complicated part that exists in any application, the user. Additionally, the front end must leverage existing UI customization definitions that are integral to the Specify application. These definitions consist of perhaps several dozen XML files per installation for which there exists no specification (to my knowledge) beyond the current thick client implementation.

Clearly since the application is targeting the web, this UI rendering will take some form whereby XML UI definitions are the input to a process that ultimately produces HTML as output. Thus, my strategy has been to utilize the powerful DOM traversal and manipulation capabilities of the jQuery JavaScript library both for parsing the XML definitions and for building the corresponding HTML documents.

The overall front end naturally breaks down into several subsystems among which separation-of-concerns should be maintained to as high a degree as possible. The subsystems I have considered so far are as follows:

* **Form generation:**
    Form generation should be possible as a purely static process. That is, the existing static XML resources describing the UI ought to be sufficient to produce completely isomorphic HTML documents (or document fragments that can be assembled without access to the original resources) without any dynamic information. This allows the HTML UI to be generated ahead of time and cached if required for performance reasons. It also makes it possible to build tests for the form generation system that can verify predictable behavior over the course of development. Finally, it permits reuse of this component outside of the broader app, perhaps facilitating a JSF or other implementation.

    At present the front end code is changing rapidly, but most of the form generation implementation can be found in [djangospecify/specify/static/specifyform.js](https://github.com/benanhalt/djangospecify/blob/master/specify/static/specifyform.js).

* **Form population:**
    Form population consists of filling in the controls on a page with the data from a API resource object. This includes setting up control widgets (e.g. pick lists and combo boxes) and applying formatting to the raw field data. Again, separation-of-concerns should be respected here. As a dynamic portion of the application, the only interaction with the static form descriptions must occur through the HTML produced by the form generation subsystem. The generated forms must contain all necessary information to allow the various fragments to be assembled, and all control widgets to be configured.
