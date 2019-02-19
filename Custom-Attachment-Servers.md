# Adding Custom Attachment Servers to Specify 7

Related to pull request: https://github.com/specify/specify7/pull/470

Specify 7 allows the capability to send attachments to different
attachment servers, and/or use multiple attachment servers at once. 
This might be desired, if, for example, you want some of the images 
to be publically available via a IIIF compliant server,and want other 
images to be stored privately on the normal attachment server.

Doing this requires of course, some development knowledge and expertise. This should not be attempted with no programming experience.

## Steps

Four major steps are required to allow specify 7 to connect to another attachment server after you have already set up the server itself. 
1. [Create new js file](#create-new-js-file)
2. [Modify attachmentsplugin to point to new js](#modify-attachmentsplugin-to-point-to-new-js)
3. [Modify settings to add server specific settings](#modify-settings-to-add-server-specific-settins)
4. [OPTIONAL Modify attachmentgw views](#modify-attachmentgw-views)

## Create New JS File
Javascript files for attachment servers are stored in:
specifyweb/frontend/js_src/lib/attachments

Copy 'attachments.js' in this folder, and rename the copy something else,
for example 'attachmentserverpublic.js'.

Open the new javascript file, and look at the javascript. You can see it creates a var 'attachments' which implements several important functions, getThumbnail, uploadFile, openOriginal, systemAvailable, originalURL. You will need to modify these to match your new server, using the specify attachment server as an example. An example of this is below.

```javascript
"use strict";

var $ = require('jquery');
var _ = require('underscore');

var schema         = require('./../schema.js');
var initialContext = require('./../initialcontext.js');
var attachmentserverbase = require('./attachments.js');

var settings;
initialContext.load('attachment_settings.json', data => settings = data);

function placeholderforlorisauthentication(notused) {
    return $.get('/attachment_gw/get_token/', { filename: 'asfdas' });
}

var attachmentserverpublic = {
  getThumbnail: function(attachment, scale) {
      scale || (scale = 256);
      var style = "max-width:" + scale + "px; " + "max-height:" + scale + "px;";
      var base_url = settings.attachment_servers_url['LORIS'];
      var attachmentlocation = attachment.get('attachmentlocation');
      return placeholderforlorisauthentication(attachmentlocation).pipe(function(token) {
          return $('<img>', {src: `${base_url}/${attachmentlocation}/full/${scale},/0/default.jpg`, style: style});
      });
  },
  uploadFile: function(file, progressCB) {
      var formData = new FormData();
      var attachment;

      formData.append('media', file);
      var attachmentlocation = file.name;
      
      return $.ajax({
              url: settings.attachment_servers_upload_url['LORIS'],
              type: 'POST',
              data: formData,
              processData: false,
              contentType: false,
          }).pipe(function() {
          var attachment = new schema.models.Attachment.Resource({
              attachmentlocation: attachmentlocation,
              mimetype: file.type,
              origfilename: file.name,
              ispublic: 1,
              servername: 'LORIS',
          });
          return attachment;
      });
  },
  openOriginal: function(attachment) {
      var attachmentlocation = attachment.get('origfilename');
      var base_url = settings.attachment_servers_url['LORIS'];
      var src = `${base_url}/${attachmentlocation}/full/full/0/default.jpg`;
      window.open(src);
  }
};

module.exports = Object.assign(Object.create(attachmentserverbase), attachmentserverpublic);

```

## Modify attachmentsplugin to point to new js
The attachmentsplugin.js file can be found in:
specifyweb/frontend/js_src/lib

This file points specify towards the different attachment servers. 
In order to allow it to use the functions you created, you need to require it, by adding the following line:
```javascript
var attachmentserverpublic = require('./attachments/attachmentserverpublic.js');
```

If you have trouble finding where it should be added, add it after the line:
```javascript
var attachmentserverprivate = require('./attachments/attachments.js');
```

## Modify settings to add server specific settings
Settings for the attachments servers are stored in the local_attachment_settings python files under specifyweb/settings. Here you add settings such as the base_url, the name of the related javascript file, and perhaps some tokens for authentication purposes which you would like to keep secret. An example from a local development environment is shown below:
```python
ATTACHMENT_SERVERS = {
'PRIVATE':    {
        'URL': 'http://127.0.0.1:8080/web_asset_store.xml',
        'FILEUPLOAD_URL': '',
        'KEY': 'SECRET-KEY-HERE',
        'COLLECTION': None,
        'REQUIRES_KEY_FOR_GET': False,
        'JS_SRC': 'attachments.js'
    },
    
'LORIS':    {
        'URL': 'http://127.0.0.1:5050/loris',
        'FILEUPLOAD_URL': 'http://127.0.0.1:5050/loris/upload_image',
        'KEY': 'SECRET-KEY-HERE',
        'JS_SRC':'attachmentserverpublic.js'
    },
}
```

## OPTIONAL Modify attachmentgw views
In case you need to add some functionality based on this local settings file which is not included inherently, you may have to modify the views.py file in the specifyweb/attachment_gw folder. Of particular importance here is the 'get_settings' function which is what passes the settings from django on to the javascript files. 


