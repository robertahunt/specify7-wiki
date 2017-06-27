How to use specifyweb API as a generic webservice.
==========

This is a simple introduction to accessing the specifyweb API using
the `curl` command line utility. For legibility, JSON output is piped
through a pretty-printer provided by the Python json library. It is
hoped that these examples illustrate how the API could be utilized by
any environment which supports HTTP and JSON.

These examples illustrate use with a demo server on
localhost. Substitute an apropriate hostname as necessary.

# Logging in #

To prevent unauthorized access to the API the browser session is
tracked with a session cookie that must be associated with a logged in
user.

A GET request to the login URL provides the available collections.

```
curl -i -c cookies.txt -b cookies.txt -X GET http://localhost:8000/context/login/
```

```
HTTP/1.0 200 OK
Date: Thu, 13 Apr 2017 21:54:55 GMT
Server: WSGIServer/0.1 Python/2.7.10
Cache-Control: no-cache, no-store, must-revalidate, max-age=0
Vary: Cookie
Expires: Thu, 13 Apr 2017 21:54:55 GMT
Content-Type: application/json
Last-Modified: Thu, 13 Apr 2017 21:54:55 GMT
Set-Cookie:  csrftoken=8BetyLjqobHb; expires=Thu, 12-Apr-2018 21:54:55 GMT; Max-Age=31449600; Path=/

{"username": null, "password": null, "collections": {"KUFishtissue": 32768, "KUFishvoucher": 4}, "collection": null}
```

To log in as a user of the API for one of the listed collections issue
a PUT request to the same URL. The *csrftoken* from the previous
response must be passed as a header. The *Referer* header must be included
when using HTTPS, but may be omitted when using HTTP.

```
curl -i -c cookies.txt -b cookies.txt -X PUT \
    --data '{"username":"sp7demofish","password":"sp7demofish","collection":4}' \
    -H "X-CSRFToken: 8BetyLjqobHb" \
    -H "Referer: http://localhost:8000/" \
    http://localhost:8000/context/login/
```

```
HTTP/1.0 204 No Content
Date: Thu, 13 Apr 2017 22:14:02 GMT
Server: WSGIServer/0.1 Python/2.7.10
Cache-Control: no-cache, no-store, must-revalidate, max-age=0
Vary: Cookie
Expires: Thu, 13 Apr 2017 22:14:02 GMT
Content-Type: text/html; charset=utf-8
Last-Modified: Thu, 13 Apr 2017 22:14:02 GMT
Set-Cookie:  csrftoken=b9SUdhWCbMGG; expires=Thu, 12-Apr-2018 22:14:02 GMT; Max-Age=31449600; Path=/
Set-Cookie:  sessionid=44kms3gdg6; httponly; Path=/
Set-Cookie:  collection=4; expires=Fri, 13-Apr-2018 22:14:02 GMT; Max-Age=31536000; Path=/

```

Note that a new CSRF token is generated when logging in and should be
used in subsequent data modifying requests.


The currently logged in user can be queried from the following resource:

```
curl -c cookies.txt -b cookies.txt -X GET http://localhost:8000/context/user.json \
    | python -m json.tool
```

```json
{
    "accumminloggedin": null,
    "agent": {
        "abbreviation": "tcu",
        "addresses": [],
        "agentattachments": [],
        "agentgeographies": [],
        "agentspecialties": [],
        "agenttype": 1,
        "collcontentcontact": null,
        "collectors": "/api/specify/agent/?agent=3",
        "colltechcontact": null,
        "createdbyagent": null,
        "dateofbirth": null,
        "dateofbirthprecision": 1,
        "dateofdeath": null,
        "dateofdeathprecision": 1,
        "datetype": null,
        "division": "/api/specify/division/2/",
        "email": "testuser@ku.edu",
        "firstname": "Test",
        "groups": [],
        "guid": "f2fec95a-7699-45f2-a794-337040b5bbc8",
        "id": 3,
        "initials": null,
        "instcontentcontact": null,
        "insttechcontact": null,
        "interests": null,
        "jobtitle": null,
        "lastname": "User",
        "members": "/api/specify/agent/?member=3",
        "middleinitial": "C",
        "modifiedbyagent": null,
        "organization": null,
        "orgmembers": "/api/specify/agent/?organization=3",
        "remarks": null,
        "resource_uri": "/api/specify/agent/3/",
        "specifyuser": "/api/specify/specifyuser/1/",
        "suffix": null,
        "timestampcreated": "2012-08-09T12:29:16",
        "timestampmodified": "2012-08-09T12:29:16",
        "title": "mr",
        "url": null,
        "variants": [],
        "version": 3
    },
    "agents": "/api/specify/specifyuser/?specifyuser=1",
    "available_collections": [
        [
            4,
            "KUFishvoucher"
        ],
        [
            32768,
            "KUFishtissue"
        ]
    ],
    "createdbyagent": null,
    "email": "testuser@ku.edu",
    "id": 1,
    "isadmin": true,
    "isauthenticated": true,
    "isloggedin": true,
    "isloggedinreport": false,
    "logincollectionname": "KUFishtissue",
    "logindisciplinename": "Ichthyology",
    "loginouttime": "2017-04-11T16:03:08",
    "message_set": "/api/specify/specifyuser/?user=1",
    "modifiedbyagent": null,
    "name": "Sp7demofish",
    "resource_uri": "/api/specify/specifyuser/1/",
    "spappresourcedirs": "/api/specify/specifyuser/?specifyuser=1",
    "spappresources": "/api/specify/specifyuser/?specifyuser=1",
    "spquerys": "/api/specify/specifyuser/?specifyuser=1",
    "tasksemaphores": "/api/specify/specifyuser/?owner=1",
    "timestampcreated": "2012-08-09T12:29:16",
    "timestampmodified": "2012-08-09T12:29:16",
    "usertype": "Manager",
    "version": 349,
    "workbenches": "/api/specify/specifyuser/?specifyuser=1",
    "workbenchtemplates": "/api/specify/specifyuser/?specifyuser=1"
}
```

Logging out of the session is accomplished using the login URL with
the username and password set to null.

```
curl -i -c cookies.txt -b cookies.txt -X PUT \
    --data '{"username":null,"password":null,"collection":4}' \
    -H "X-CSRFToken: 44kms3gdg6" \
    -H "Referer: http://localhost:8000/" \
    http://localhost:8000/context/login/
```

# Making API requests #

Once a logged-in session has been established and stored in the
`cookie.txt` file, making API requests is easy. Let's fetch the
collection object with id *12*.

```
curl -b cookies.txt http://localhost:8000/api/specify/collectionobject/12/ \
    | python -m json.tool
```

```json
{
    "accession": "/api/specify/accession/614/",
    "altcatalognumber": null,
    "appraisal": null,
    "availability": null,
    "catalogeddate": "2006-06-13",
    "catalogeddateprecision": 1,
    "catalogeddateverbatim": null,
    "cataloger": "/api/specify/agent/66/",
    "catalognumber": "000038407",
    "collectingevent": "/api/specify/collectingevent/8868/",
    "collection": "/api/specify/collection/4/",
    "collectionmemberid": 4,
    "collectionobjectattachments": [],
    "collectionobjectattribute": {
        "bottomdistance": null,
        "collectionmemberid": 4,
        "collectionobjects": "/api/specify/collectionobjectattribute/?collectionobjectattribute=42336",
        "createdbyagent": "/api/specify/agent/1/",
        "direction": null,
        "distanceunits": null,
        "id": 42336,
        "modifiedbyagent": null,
        "number1": null,
        "number10": null,
        "number11": null,
        "number12": null,
        "number13": null,
        "number14": null,
        "number15": null,
        "number16": null,
        "number17": null,
        "number18": null,
        "number19": null,
        "number2": null,
        "number20": null,
        "number21": null,
        "number22": null,
        "number23": null,
        "number24": null,
        "number25": null,
        "number26": null,
        "number27": null,
        "number28": null,
        "number29": null,
        "number3": null,
        "number30": null,
        "number31": null,
        "number32": null,
        "number33": null,
        "number34": null,
        "number35": null,
        "number36": null,
        "number37": null,
        "number38": null,
        "number39": null,
        "number4": null,
        "number40": null,
        "number41": null,
        "number42": null,
        "number5": null,
        "number6": null,
        "number7": null,
        "number8": null,
        "number9": null,
        "positionstate": null,
        "remarks": null,
        "resource_uri": "/api/specify/collectionobjectattribute/42336/",
        "text1": null,
        "text10": null,
        "text11": "45-51mm SL",
        "text12": null,
        "text13": null,
        "text14": null,
        "text15": null,
        "text16": null,
        "text17": null,
        "text18": null,
        "text2": null,
        "text3": null,
        "text4": null,
        "text5": null,
        "text6": null,
        "text7": null,
        "text8": null,
        "text9": null,
        "timestampcreated": "1999-10-25T12:32:15",
        "timestampmodified": null,
        "topdistance": null,
        "version": 0,
        "yesno1": null,
        "yesno2": null,
        "yesno3": null,
        "yesno4": null,
        "yesno5": null,
        "yesno6": null,
        "yesno7": null
    },
    "collectionobjectattrs": [],
    "collectionobjectcitations": [],
    "conservdescriptions": [],
    "container": null,
    "containerowner": null,
    "countamt": 1,
    "createdbyagent": "/api/specify/agent/1/",
    "deaccessioned": false,
    "description": null,
    "determinations": [
        {
            "addendum": null,
            "alternatename": null,
            "collectionmemberid": 4,
            "collectionobject": "/api/specify/collectionobject/12/",
            "confidence": null,
            "createdbyagent": "/api/specify/agent/1/",
            "determinationcitations": [],
            "determineddate": "2006-06-13",
            "determineddateprecision": 1,
            "determiner": "/api/specify/agent/66/",
            "featureorbasis": null,
            "guid": "df1929e3-1ed3-11e3-bfac-90b11c41863e",
            "id": 41841,
            "iscurrent": true,
            "method": null,
            "modifiedbyagent": "/api/specify/agent/1514/",
            "nameusage": null,
            "number1": 212.0,
            "number2": null,
            "preferredtaxon": "/api/specify/taxon/11486/",
            "qualifier": null,
            "remarks": null,
            "resource_uri": "/api/specify/determination/41841/",
            "subspqualifier": null,
            "taxon": "/api/specify/taxon/11486/",
            "text1": null,
            "text2": null,
            "timestampcreated": "1999-10-25T12:32:15",
            "timestampmodified": "2006-09-19T10:37:01",
            "typestatusname": null,
            "varqualifier": null,
            "version": 1,
            "yesno1": false,
            "yesno2": null
        }
    ],
    "dnasequences": [],
    "exsiccataitems": [],
    "fieldnotebookpage": null,
    "fieldnumber": null,
    "guid": "db192e8c-1ed3-11e3-bfac-90b11c41863e",
    "id": 12,
    "integer1": null,
    "integer2": null,
    "inventorydate": null,
    "leftsiderels": "/api/specify/collectionobject/?leftside=12",
    "modifiedbyagent": "/api/specify/agent/1514/",
    "modifier": " ",
    "name": null,
    "notifications": null,
    "number1": null,
    "number2": null,
    "objectcondition": null,
    "ocr": null,
    "otheridentifiers": [],
    "paleocontext": null,
    "preparations": [
        {
            "collectionmemberid": 4,
            "collectionobject": "/api/specify/collectionobject/12/",
            "countamt": 4,
            "createdbyagent": "/api/specify/agent/1/",
            "deaccessionpreparations": "/api/specify/preparation/?preparation=35011",
            "description": null,
            "exchangeinpreps": "/api/specify/preparation/?preparation=35011",
            "exchangeoutpreps": "/api/specify/preparation/?preparation=35011",
            "giftpreparations": "/api/specify/preparation/?preparation=35011",
            "id": 35011,
            "integer1": null,
            "integer2": null,
            "isonloan": false,
            "loanpreparations": "/api/specify/preparation/?preparation=35011",
            "modifiedbyagent": "/api/specify/agent/66/",
            "number1": null,
            "number2": null,
            "preparationattachments": [],
            "preparationattribute": null,
            "preparationattrs": [],
            "preparedbyagent": null,
            "prepareddate": null,
            "prepareddateprecision": 1,
            "preptype": "/api/specify/preptype/2/",
            "remarks": null,
            "reservedinteger3": null,
            "reservedinteger4": null,
            "resource_uri": "/api/specify/preparation/35011/",
            "samplenumber": null,
            "status": null,
            "storage": null,
            "storagelocation": null,
            "text1": null,
            "text2": "Jar",
            "timestampcreated": "2002-07-29T13:38:25",
            "timestampmodified": "2006-06-13T10:13:09",
            "version": 0,
            "yesno1": null,
            "yesno2": null,
            "yesno3": null
        }
    ],
    "projectnumber": null,
    "remarks": null,
    "reservedinteger3": null,
    "reservedinteger4": null,
    "reservedtext": null,
    "reservedtext2": null,
    "reservedtext3": null,
    "resource_uri": "/api/specify/collectionobject/12/",
    "restrictions": null,
    "rightsiderels": "/api/specify/collectionobject/?rightside=12",
    "sgrstatus": null,
    "text1": "known",
    "text2": null,
    "text3": null,
    "timestampcreated": "1999-10-25T11:53:37",
    "timestampmodified": "2006-09-19T10:37:01",
    "totalvalue": null,
    "treatmentevents": [],
    "version": 1,
    "visibility": 0,
    "visibilitysetby": null,
    "yesno1": null,
    "yesno2": null,
    "yesno3": null,
    "yesno4": null,
    "yesno5": null,
    "yesno6": null
}
```

The result is a JSON representation of the requested object. The first
thing to notice is that related objects can either be referenced by
links to their resources or have their data included *in line* in the
parent resource.

The URIs for resources take the form `/api/specify/TABLE/ID/`. To
retrieve a collection of resources use URIs of the form
`/api/specify/TABLE/`.

```
curl -b cookies.txt http://localhost:8000/api/specify/collectionobject/ \
    | python -m json.tool
```

By default a maximum of twenty rows will be returned when requesting a
collection. The `limit` query parameter can be used to adjust the
amount. Using `limit=0` returns *all* rows.

```
curl -b cookies.txt http://localhost:8000/api/specify/taxon/?limit=2 \
    | python -m json.tool
```

The `offset` query parameter can be used along with `limit` to
implement paging.

### Filtering ###

Often it will be necessary to retrieve a collection of resources that
meet certain criteria. The API implements basic filtering for this
purpose. E.g. to retrieve all preparation records for collection
object with id 12:

```
curl -b cookies.txt http://localhost:8000/api/specify/preparation/?collectionobject=12 \
    | python -m json.tool
```

Collection requests can also be filtered by **domain**, which means
that only records which are in the same collection context as the user
will be returned.

```
curl -b cookies.txt http://localhost:8000/api/specify/taxon/?domainfilter=true \
    | python -m json.tool
```

### Updates ###

Resources can be updated by making *PUT* requests to the corresponding
URI. Because the API implements optimistic locking, it is necessary to
obtain the current version of the resource before attempting an
update.

```
curl -b cookies.txt http://localhost:8000/api/specify/agent/3/ | grep -o '\"version\": [0-9]*'
```

If the output is, for example, `"version": 3`, then the resource
could be modified as follows:

```
curl -b cookies.txt -X PUT \
    -H "X-CSRFToken: 44kms3gdg6" \
    -H "Referer: http://localhost:8000/" \
    --data '{"version": 3, "remarks":"test"}' \
    http://localhost:8000/api/specify/agent/3/ \
        | python -m json.tool
```

The response will be the data of the updated resource.

If the version if is omitted, the server will return a `400 Bad
Request`. If the version does not match the current version in the
database, `409 Conflict` will be returned.

### Resource creation ###

Resources can be created by issuing a *POST* request to the collection
URI representing the target table. To create a new collection object:

```
curl -b cookies.txt -X POST \
    -H "X-CSRFToken: 44kms3gdg6" \
    -H "Referer: http://localhost:8000/" \
    --data '{"collection":"/api/specify/collection/4/"}' \
    http://localhost:8000/api/specify/collectionobject/ \
        | python -m json.tool
```

The response will be the complete representation of the newly created
resource.

### Resource deletion ###

Delete resources by issuing *DELETE* to the resource's URI. Because no
data is included in a DELETE request, the version information is
placed in the `If-Match` HTTP header:

```
curl -b cookies.txt -X DELETE \
    -H 'If-Match: 0' \
    -H "X-CSRFToken: 44kms3gdg6" \
    -H "Referer: http://localhost:8000/" \
    http://localhost:8000/api/specify/collectionobject/123456789/
```

where `0` and `123456789` are replaced with the appropriate
values. Again, if the version info is missing or out of date, an error
will be returned.
