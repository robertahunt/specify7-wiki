# Installing Specify7 on Fedora 22

## Install system dependencies.

It seem MariaDB has replaced MySQL on Fedora 22?

The development packages are needed to build the Python crypto and
mysql drivers.

```
sudo yum install python-pip python-devel
sudo yum install mysql mysql-devel mysql-libs
sudo yum install make automake gcc
sudo yum install mariadb-server 
```

## Fetch the Specify 7 release.

```
git clone https://github.com/specify/specify7.git
cd specify7
git checkout release
```

## Install python dependencies.

You can use a Python virtual environment or run the following with
sudo to install systemwide.

```
pip install -r requirements.txt
```

