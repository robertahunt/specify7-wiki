# Welcome to the djangospecify wiki!

The purpose of this page is to provide an overview of the code for those who would like to understand it's workings.

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

