# Hosting Multiple Databases using Apache Virtual Hosting
It is possible to use name based virtual hosting in Apache to host
multiple Specify databases from a single server. The technique
involves using Apache *mod_macro* to abstract over the server name in
the Apache conf file, and mapping the server name to the database name
in the *wsgi* config file so that it can be substituted into the
`DATABASE_NAME` setting in the Django `settings.py` file.

## mod_macro
For this technique Apache *mod_macro* will need to be installed and
enabled. In Ubuntu it looks like this:

```
apt-get install libapache2-mod-macro
a2enmod macro
```

## The Apache conf file
The Specify 7 repository includes a
[sample](https://github.com/specify/specify7/blob/master/specifyweb_apache.conf)
Apache conf file that can be modifed and linked or copied into the
`/etc/apache2/site-enabled` directory. This configuration can be
modified to use Apache macros for multiple name based hosts. Here is
an example:

```aconf
<Macro SpecifyVH $servername>
<VirtualHost *:80>
        ServerName $servername.specify.ku.edu

        <Directory />
                Order deny,allow
                Allow from all
                Options -Indexes
        </Directory>

        Alias /static/config    /home/anhalt/Specify/config
        Alias /static           /home/anhalt/specify7/specifyweb/frontend/static

        WSGIDaemonProcess $servername user=anhalt group=anhalt
        WSGIProcessGroup $servername

        WSGIScriptAlias / /home/anhalt/specify7/specify.ku.edu_virtual_host.wsgi

        ErrorLog ${APACHE_LOG_DIR}/$servername_error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog ${APACHE_LOG_DIR}/$servername_access.log combined
</VirtualHost>
</Macro>

#Use SpecifyVH test
Use SpecifyVH plants
Use SpecifyVH lichens
Use SpecifyVH trichomycetes
Use SpecifyVH accessions
Use SpecifyVH creac
Use SpecifyVH vertebratepaleontology
Use SpecifyVH mammalogy
Use SpecifyVH invertebratepaleontology
Use SpecifyVH ichthyology
Use SpecifyVH herpetology
Use SpecifyVH entomology
Use SpecifyVH ornithology
# Use SpecifyVH botany
```

This configuration creates a macro that is abstracted over the
variable name $servername. The `Use` directives at the end instantiate
the macro with twelve values for the server name producing twelve
separate name based virtual hosts. Each virtual host creates a
separate WSGI process and separate log files.

## The wsgi file.
Also included in the Specify 7 repository is a
[sample](https://github.com/specify/specify7/blob/master/specifyweb.wsgi)
wsgi file that can be modified and referenced from the Apache conf
file. When an WSGI application is invoked it is passed an `environ`
value which will contain the `ServerName` value from Apache. The wsgi
file can be updated to map the server name to the database name to
pass to the Specify 7 application.

```python
# -*- python -*-
import os, sys, json

# this is needed to prevent threading issues with mod_wsgi, e.g.
# ImportError: Failed to import _strptime because the import lockis held by another thread.
# might be fixed in python 3
# see: https://code.google.com/p/modwsgi/issues/detail?id=177

from datetime import datetime
datetime.strptime('01/14/2014', '%m/%d/%Y')

# Map server name to database name
db_map = {
    'test': 'Specify6_TEST',
    'ichthyology': 'KU_Fish_Tissue',
    'plants': 'KANUVascularPlantDB',
    'lichens': 'KANULichenDB',
    'trichomycetes': 'trichomycetes_dbo_6',
    'accessions': 'kuaccessions',
    'creac': 'CReAC',
    'vertebratepaleontology': 'KUVP_dbo_6',
    'mammalogy': 'kumam_dbo_6',
    'invertebratepaleontology': 'kuinvp4_dbo_6',
    'herpetology': 'KUHerps',
    'entomology': 'entosp_dbo_6',
    'ornithology': 'KUBirds',
#    'botany': '',
}

this_dir = os.path.dirname(__file__)

sys.path.append(this_dir)
os.environ['DJANGO_SETTINGS_MODULE'] = 'specifyweb.settings'

import django.core.handlers.wsgi
django_app = django.core.handlers.wsgi.WSGIHandler()

def application(environ, start_response):
    server_name = environ['SERVER_NAME'].split('.')[0]
    # Set environment variable with database name.
    os.environ['SPECIFY_DATABASE_NAME'] = db_map[server_name]
    return django_app(environ, start_response)
```

## The Django settings file.
Finally, the Django settings file in `specify7/specifyweb/settings/`
can obtain the database name from the OS environment. The following
example uses the database name not just to access the correct
database but also changes various other settings based on its value.

```python
import os

# The webapp server piggy backs on the thick client.
# Set the path to a thick client installation.
THICK_CLIENT_LOCATION = '/home/anhalt/Specify'

# Set the database name to the mysql database you
# want to access. This is modified to get its value from the environment.
DATABASE_NAME = os.environ['SPECIFY_DATABASE_NAME']

DATABASE_HOST = 'dbserver.somewhere.net'
DATABASE_PORT = ''

# The master user login. Use the same values as
# you did setting up the thick client.
MASTER_NAME = 'specifymaster'
MASTER_PASSWORD = 'cantbeguessed'

# The Specify web attachement server URL.
WEB_ATTACHMENT_URL = ("http://testattachments.nhm.ku.edu:3088/web_asset_store.xml"
                      if DATABASE_NAME == 'Specify6_TEST' else
                      "http://biimages.biodiversity.ku.edu/web_asset_store.xml")

# The Specify web attachment server key.
WEB_ATTACHMENT_KEY = ("testtesttest"
                      if DATABASE_NAME == 'Specify6_TEST' else
                      "supersecret")

# The collection name to use with the web attachment server.
WEB_ATTACHMENT_COLLECTION = None  # Use collection name.

# Set to true if asset server requires auth token to get files.
WEB_ATTACHMENT_REQUIRES_KEY_FOR_GET = False

# Report runner service
REPORT_RUNNER_HOST = ''
REPORT_RUNNER_PORT = ''

# To allow anonymous use, set ANONYMOUS_USER to a Specify username
# to use for anonymous access.
if DATABASE_NAME == 'Specify6_TEST':
    ANONYMOUS_USER = 'specifyguest'
elif DATABASE_NAME in ('KU_Fish_Tissue', 'KUHerps'):
    ANONYMOUS_USER = 'guest'
else:
    ANONYMOUS_USER = None

```
