# Installing Specify7 on Fedora 22

## Install system dependencies.

It seems MariaDB has replaced MySQL on Fedora 22?

The development packages are needed to build the Python crypto and
mysql drivers.

```
sudo yum install python-pip python-devel
sudo yum install mysql mysql-devel mysql-libs
sudo yum install make automake gcc
sudo yum install mariadb-server
```

As root:

`curl -sL https://rpm.nodesource.com/setup_4.x | bash -`

## Fetch the Specify 7 release.

```
git clone https://github.com/specify/specify7.git
cd specify7
git checkout release
```

## Install python dependencies.

You can use a Python virtual environment or run the following with
sudo to install system wide.

```
pip install -r requirements.txt
```

## Copy a Specify 6 installation to the server.

E.g.

```
scp -r Specify6.6.02 root@45.55.47.246:/opt
```

Generate the front end.
-----------------------
The Javascript dependencies and sources for the browser need to be
packaged.

    make -C specify7

When the Specify7 repository is updated, this step should be repeated.

### MySQL (MariaDB)

If you are using MySQL on the same server, you will need to restore a
Specify database into it and setup a "master" user.

## Adjust settings files.
In the directory `specify7/specifyweb/settings` you will find the
`specify_settings.py` file. Make a copy of this file as
`local_specify_settings.py` and edit it. The file contains comments
explaining the various settings.
    
## Turn on debugging.
For development purposes, Django debugging should be turned on. It
will enable stacktraces in responses that encounter exceptions, and
allow operation with the unoptimized Javascript files. 

Debugging can be enabled by creating the file
`specify7/specifyweb/settings/debug.py` with the contents, `DEBUG =
True`.

## The development server.
Specify7 can be run using the Django development server. If you are
using Python virtual environment, you will of course need to activate
it first.

    python specify7/specifyweb/manage.py runserver

This will start a development server for testing purposes on
`localhost:8000`.


# Deployment to production.
Start by following the development instructions above, but don't
enable debugging (or **disable** it if you enabled it previously).


## Production requirements.
For production environments, Specify7 can be hosted by Apache. The
following packages are needed:

* Apache
* mod-wsgi to connect Python to Apache

```
sudo yum install httpd mod_wsgi
```

## Setup Apache.
Create a `specifyweb_apache.conf` file in
`/etc/httpd/conf.d` with the following content, adjusting the paths as
necessary.

```aconf
<VirtualHost *:80>
        <Directory />
           Require all granted
        </Directory>

        # Alias the following to the Specify6 installation + /config
        Alias /static/config    /opt/Specify6.6.02/config

        # Alias the following to the Specify7 installation + /specifyweb/frontend/static
        Alias /static           /home/specify/specify7/specifyweb/frontend/static

        # Alias the following to the Specify7 installation + /specifyweb.wsgi
        WSGIScriptAlias / /home/specify/specify7/specifyweb.wsgi

        ErrorLog /var/log/httpd/error.log

        # Possible values include: debug, info, notice, warn, error, crit,
        # alert, emerg.
        LogLevel warn

        CustomLog /var/log/httpd/access.log combined
</VirtualHost>
```

Apache then needs to be restarted, `sudo systemctl restart httpd`.

It may be necesary to change the permissions on the home directory
where Specify 7 is installed.

```
chmod a+rx ~
```

And if SELinux is enabled, there may be other permissions issues that
are beyond the scope of this document.
