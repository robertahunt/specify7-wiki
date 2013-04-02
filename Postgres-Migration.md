Using Django to Migrate Specify Database from MySQL to Postgres
===============================================================

This is the procedure I followed. This is reconstructed from memory,
your mileage may vary.

1. I used the Django1.5 branch of the Specify Webapp code which can be
   found
   [here](https://github.com/specify/specifyweb/tree/django1.5). Be
   aware that the setup instructions (README.md) have changed
   (hopefully for the simpler) and that it is probably easier to clone
   another working copy rather than checking out the Django1.5 branch.
   I've been reorganizing the directory layout and the settings files
   get a bit wonky going between the master and django1.5 branch.

2. Once the webapp is working so that it is possible to use it to
   log in to an existing MySQL database, the Django `dumpdata` tool can
   be used to export the entire database as JSON.

       `./manage.sh dumpdata > specifydatabase.json`

   This will take awhile and the resulting file will be quite large.

3. Next I created an empty Postgres database and a Postgres user with
   `CREATE TABLE` privs. Consult the Postgres docs on how to do this,
   or email me if you run into problems. Postgres logins work
   differently than MySQL, and I always have to relearn the process.

4. Reconfigure Django to use the new Postgres database. Edit the
   `settings/__init__.py` file replacing
   `hibernateboolsbackend.backends.mysql` with
   `django.db.backends.postgresql_psycopg2` in the `DATABASES`
   section. Oh, you will probably need to install the Python Postgres
   bindings, too:

       `sudo apt-get install python-psycopg2`

5. In `setting/local_specify_settings.py` adjusted the database name and login
   values to match those of the new Postgres database.

6. Tell Django to create the Specify schema in the database:
   `./manage.sh syncdb`.

7. At this point I tried populating the database from the JSON file,
   but it choked on some dates. For some reason it serialized all date
   without times as `YYYY-MM-DDT00:00:00` but only wants to
   deserialize dates like `YYYY-MM-DD`. Since the import takes a long
   time and you probably don't want to have to restart it, I would go
   ahead and:

       `sed -i 's/T00:00:00//g' specifydatabase.json`

8. That done, I was able to import without errors although the import
   takes even longer than the export. It also seems to consume a lot
   of memory. This may be an issue for larger databases. The import
   command is:

       `./manage.sh loaddata --traceback specifydatabase.json`

9. Start the web server and enjoy your Postgres powered Specify
   webapp. `./manage.sh runserver`

