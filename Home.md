# Welcome to the djangospecify wiki!

The purpose of this page is to provide an overview of the code for those who would like to understand its workings.

## Architecture in broad strokes

The project breaks down into two main components: a _back end_ that basically surfaces the Specify datamodel as a set of resources according to REST principles and a _front end_ that provides a UI for interacting with those resources.

### The back end

The role of the back end is three-fold. Most important it provides RESTful access to the underlying database and implements business rules necessary to maintain data integrity. It is also responsible for authentication and for serving the static resources needed by the front end.

The back end is implemented in Python and makes use of the Django framework for ORM and authentication subsystems. The TastyPie library for Django is used to facilitate the implementation of the API resources.

* The ORM layer:
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

* The API layer:
    The API generation is based around the [TastyPie](https://github.com/toastdriven/django-tastypie) library. The idea is much the same as before. Traditionally each resource would be described to TastyPie by a Python class. E.g. from [the docs](http://django-tastypie.readthedocs.org/en/latest/fields.html):

        class PersonResource(Resource):
            name = fields.CharField(attribute='name')
            age = fields.IntegerField(attribute='years_old', null=True)
            created = fields.DateTimeField(readonly=True, default=utils.now)
            is_active = fields.BooleanField(default=True)
            profile = fields.ToOneField(ProfileResource, 'profile')
            notes = fields.ToManyField(NoteResource, 'notes', full=True)

    So again the strategy is dynamic class generation. This code lives at [djangospecify/specify/api.py](https://github.com/benanhalt/djangospecify/blob/master/specify/api.py). The code is complicated somewhat by subclassing the TastyPie resource implementations to provide customized behavior, but the module remains quite concise and manageable.

* Authentication:
     It is desirable that the authentication system utilize the same user credentials as the current thick client. That has been achieved by implementing the same decryption protocol that is used to challenge user passwords against the encrypted versions stored in the `specifyusers` table. The relevant code is in [djangospecify/specify/encryption.py](https://github.com/benanhalt/djangospecify/blob/master/specify/encryption.py). And the code which interfaces it to the Django authentication system is found in [djangospecify/specify/authbackend.py](https://github.com/benanhalt/djangospecify/blob/master/specify/authbackend.py).