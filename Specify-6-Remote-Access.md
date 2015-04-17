# Specify 6 Remote Access Procedure

This document details the process for accessing a Specify Cloud
database using Specify 6.


## The SSH tunnel

To protect the MySQL connection between the server and the Specify 6
client, the connection will be tunnelled through an SSH connection.

The first step is to request an SSH user from
[Specify support](specify@ku.edu). We will need an SSH public key from
each machine that will be connecting to the server.

### SSH public key

Follow these instructions to find or generate the SSH keys.

#### Linux and Mac

On Linux and Mac systems the SSH public key can be found in
`.ssh/id_rsa.pub` in the user's home directory. If this file does not
exist, it can be created with the `ssh-keygen` command.

#### Windows
On Windows, download `putty.exe` and `puttygen.exe` from the PuTTY [download site](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
Put these files into a place you can remember, such as `C:\Users\username\My Documents\SpecifySSHTunnel`. Run the `puttygen.exe` file and click *generate*. Follow the on-screen instructions until you have generated a public and private key. Once the keys are generated, click *Save public key*. This is the file that will be sent to Specify support. Then, click *Save private key*.

### Opening the tunnel

After we receive your SSH public key(s), we will add an SSH user for
your institution which will only be accessible from accounts for which
we received public keys.


#### Linux and Mac

On Linux and Mac, the tunnel is opened with the following command:

```
ssh -N -L3307:10.132.218.32:3306 [SSH user]@[your specify cloud domain]
```

For example,

```
ssh -N -L3307:10.132.218.32:3306 demo@demo.specifycloud.org
```

If the public key was setup correctly you will not be prompted for a
password, and an SSH connection will be started. The tunnel will
remain open until `Ctrl-C` is pressed. The tunnel may be left open
between Specify 6 sessions, although if network connectivity is
lost the connection may hang.

#### Windows

Right click on `putty.exe` and select *Create shortcut*. Right click on the shortcut file and choose *Properties*. Add this to the end of the **Target** field:

```
-ssh -i yourprivatekeyfile.ppk [SSH user]@[your specify cloud domain] -L 3307:10.132.218.32:3306 -N
```

For example, the **Target** field will look something like this:

```
"C:\Users\d123w969\My Documents\SpecifySSHTunnel\putty.exe" -ssh -i privatekey.ppk demo@demo.specifycloud.org -L 3307:10.132.218.32:3306 -N
```

Click *OK* to apply the changes to the shortcut file. If you'd like, you can now move this shortcut to a more convenient place such as your desktop or your start menu.

Double click on the shortcut to start the tunnel. If the public and private keys were setup correctly, you will not be prompted for a password, and an SSH connection will be started. The tunnel will
remain open until the PuTTY window is closed. The tunnel may be left open
between Specify 6 sessions, although if network connectivity is
lost the connection may hang.

## Starting Specify 6

### Obtaining a master key

To login with Specify 6 each user will need an encrypted master
key. In Specify 7 click on your user name and select *Generate Specify
6 Master Key* in the resulting dialog. You will be prompted for your
user password before the key is generated. This key will be valid
until the user or master password is changed.

### Loging in with Specify 6

In the Specify 6 login dialog select *More Information* and fill out
the fields as follows:

* **Username** - same as Specify 7
* **Password** - same as Specify 7
* **Database** - provided by Specify support in email
* **Server** - 127.0.0.1
* **Port** - 3307

Next click the *Configure Master Key* button and select *Encryption
key stored in local preferences* (the default). Copy the key generated
by Specify 7 into the field *Encrypted Username / Password* using the
clipboard button next to the field. Select *OK* and Specify should be
able to login.


