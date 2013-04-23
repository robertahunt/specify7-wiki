Using Django to Migrate Specify Database from MySQL to Postgres
===============================================================

This is the procedure I followed. It is reconstructed from memory;
your mileage may vary.

1. Once the webapp is working so that it is possible to use it to
   log in to an existing MySQL database, the Django `dumpdata` tool can
   be used to export the entire database as JSON.

       `./manage.sh dumpdata > specifydatabase.json`

   This will take awhile and the resulting file will be quite large.

2. Create an empty Postgres database and a Postgres user with
   `CREATE TABLE` privs. Consult the Postgres docs on how to do this,
   or email me if you run into problems. Postgres logins work
   differently than MySQL, and I always have to relearn the process.

3. Reconfigure Django to use the new Postgres database. Edit the
   `settings/__init__.py` file replacing
   `hibernateboolsbackend.backends.mysql` with
   `django.db.backends.postgresql_psycopg2` in the `DATABASES`
   section. Oh, you will probably need to install the Python Postgres
   bindings, too:

       `sudo apt-get install python-psycopg2`

4. In `setting/local_specify_settings.py` adjust the database name and login
   values to match those of the new Postgres database.

5. Comment out the `check_versions` line  in `specify/models.py` so it won't
   bail out when it doesn't find an `spversion` table in the new empty DB.

5. Tell Django to create the Specify schema in the database:
   `./manage.sh syncdb`.

6. At this point I tried populating the database from the JSON file,
   but it choked on some dates. For some reason it serialized all dates
   without times as `YYYY-MM-DDT00:00:00` but only wants to
   deserialize dates like `YYYY-MM-DD`. Since the import takes a long
   time and you probably don't want to have to restart it, I would go
   ahead and:

       `sed -i 's/T00:00:00//g' specifydatabase.json`

7. That done, I was able to import without errors although the import
   takes even longer than the export. It also seems to consume a lot
   of memory. This may be an issue for larger databases. The import
   command is:

       `./manage.sh loaddata --traceback specifydatabase.json`

8. Start the web server and enjoy your Postgres powered Specify
   webapp. `./manage.sh runserver`

