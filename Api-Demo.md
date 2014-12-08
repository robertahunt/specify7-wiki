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

A GET request to the login URL provides the available collections:

    $ curl -c cookies.txt -b cookies.txt -X GET http://localhost:8000/context/login/

    {"username": null, "password": null, "collections": {"KUFishtissue": 32768, "KUFishvoucher": 4}, "collection": null}

To log in as a user of the API for one of the listed collections issue
a PUT request to the same URL:

    $ curl -c cookies.txt -b cookies.txt -v -X PUT --data '{"username":"guest","password":"guest","collection":4}' http://localhost:8000/context/login/

The currently logged in user can be queried from the following resource:

    $ curl -c cookies.txt -b cookies.txt -X GET http://localhost:8000/context/user.json | python -m json.tool

    {
        "accumminloggedin": null,
        "agents": "/api/specify/agent/?specifyuser=2",
        "createdbyagent": "/api/specify/agent/3/",
        "email": "",
        "id": 2,
        "isadmin": true,
        "isauthenticated": true,
        "isloggedin": false,
        "isloggedinreport": false,
        "logincollectionname": null,
        "logindisciplinename": null,
        "loginouttime": "2014-11-19T17:42:27",
        "modifiedbyagent": null,
        "name": "guest",
        "resource_uri": "/api/specify/specifyuser/2/",
        "spappresourcedirs": "/api/specify/spappresourcedir/?specifyuser=2",
        "spappresources": "/api/specify/spappresource/?specifyuser=2",
        "spquerys": "/api/specify/spquery/?specifyuser=2",
        "tasksemaphores": "/api/specify/sptasksemaphore/?owner=2",
        "timestampcreated": "2012-10-04T14:45:39",
        "timestampmodified": "2014-10-27T15:43:54",
        "usertype": "Guest",
        "version": 2423,
        "workbenches": "/api/specify/workbench/?specifyuser=2",
        "workbenchtemplates": "/api/specify/workbenchtemplate/?specifyuser=2"
    }

Logging out of the session is accomplished using the login URL with the username and password set to null.

# Making API requests #

Once a logged-in session has been established and stored in the
`cookie.txt` file, making API requests is easy. Let's fetch the
collection object with id 12.

    $ curl -b cookies.txt http://localhost:8000/api/specify/collectionobject/12/ | python -m json.tool
    {
    "accession": "/api/specify/accession/610/",
    "altcatalognumber": null,
    "appraisal": null,
    "availability": null,
    "catalogeddate": "2006-06-13",
    "catalogeddateprecision": 1,
    "catalogeddateverbatim": null,
    "cataloger": "/api/specify/agent/66/",
    "catalognumber": "000038407",
    "collectingevent": "/api/specify/collectingevent/8846/",
    "collection": "/api/specify/collection/4/",
    "collectionmemberid": 4,
    "collectionobjectattachments": "/api/specify/collectionobjectattachment/?collectionobject=12",
    "collectionobjectattribute": null,
    "collectionobjectattrs": "/api/specify/collectionobjectattr/?collectionobject=12",
    "collectionobjectcitations": "/api/specify/collectionobjectcitation/?collectionobject=12",
    "conservdescriptions": "/api/specify/conservdescription/?collectionobject=12",
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
        "confidence": "Male",
        "createdbyagent": "/api/specify/agent/1/",
        "determinationcitations": "/api/specify/determinationcitation/?determination=41670",
        "determineddate": "2006-06-13",
        "determineddateprecision": 1,
        "determiner": "/api/specify/agent/66/",
        "featureorbasis": null,
        "id": 41670,
        "iscurrent": true,
        "method": null,
        "modifiedbyagent": "/api/specify/agent/3/",
        "nameusage": null,
        "number1": 212.0,
        "number2": null,
        "preferredtaxon": "/api/specify/taxon/12027/",
        "qualifier": null,
        "remarks": "\u0108u \u011di funkcias prave e\u0109 se mi enmetas unikodajn literojn? Ja ja kompis - nu f\u00f6rst\u00e5r jag exakt vad du menar.",
        "resource_uri": "/api/specify/determination/41670/",
        "subspqualifier": null,
        "taxon": "/api/specify/taxon/12027/",
        "text1": "45-51mm SL",
        "text2": null,
        "timestampcreated": "1999-10-25T12:32:15",
        "timestampmodified": "2012-10-15T15:45:48",
        "typestatusname": "",
        "varqualifier": null,
        "version": 38,
        "yesno1": false,
        "yesno2": null
    }
    ],
    "dnasequences": "/api/specify/dnasequence/?collectionobject=12",
    "exsiccataitems": "/api/specify/exsiccataitem/?collectionobject=12",
    "fieldnotebookpage": null,
    "fieldnumber": null,
    "guid": null,
    "id": 12,
    "inventorydate": null,
    "leftsiderels": "/api/specify/collectionrelationship/?leftside=12",
    "modifiedbyagent": "/api/specify/agent/3/",
    "modifier": " ",
    "name": null,
    "notifications": null,
    "number1": null,
    "number2": null,
    "objectcondition": null,
    "ocr": "",
    "otheridentifiers": "/api/specify/otheridentifier/?collectionobject=12",
    "paleocontext": null,
    "preparations": "/api/specify/preparation/?collectionobject=12",
    "projectnumber": null,
    "remarks": "",
    "resource_uri": "/api/specify/collectionobject/12/",
    "restrictions": null,
    "rightsiderels": "/api/specify/collectionrelationship/?rightside=12",
    "sgrstatus": null,
    "text1": "known",
    "text2": "",
    "timestampcreated": "1999-10-25T11:53:37",
    "timestampmodified": "2012-10-15T15:45:48",
    "totalvalue": null,
    "treatmentevents": "/api/specify/treatmentevent/?collectionobject=12",
    "version": 55,
    "visibility": 0,
    "visibilitysetby": null,
    "yesno1": null,
    "yesno2": null,
    "yesno3": null,
    "yesno4": null,
    "yesno5": null,
    "yesno6": null
    }

The result is a JSON representation of the requested object. The first
thing to notice is that related object can either be referenced by
links to their resources or have their data included 'in line' in the
parent resource. Whether this happens is controlled by the
`INLINED_FIELDS` variable in `specify/api.py`.

The URIs for resources take the form `/api/specify/TABLE/ID/`. To
retrieve a collection of resources use URIs of the form
`/api/specify/TABLE/`.


    $ curl -b cookies.txt http://localhost:8000/api/specify/collectionobject/ | python -m json.tool
    **LOTS OF DATA**

By default a maximum of twenty rows will be returned when requesting a
collection. The `limit` query parameter can be used to adjust the
amount. Using `limit=0` returns ALL rows.

    $ curl -b cookies.txt http://localhost:8000/api/specify/taxon/?limit=2 | python -m json.tool
    **TWO TAXON RECORDS**

The `offset` query parameter can be used along with `limit` to
implement paging.

### Filtering ###

Often it will be necessary to retrieve a collection of resources that
meet certain criteria. The API implements basic filtering for this
purpose. E.g. to retrieve all preparation records for collection
object with id 12:

    $ curl -b cookies.txt http://localhost:8000/api/specify/preparation/?collectionobject=12 | python -m json.tool

Collection requests can also be filtered by **domain**, which means
that only records which are in the same collection context as the user
will be returned.

    $ curl -b cookies.txt http://localhost:8000/api/specify/taxon/?domainfilter=true | python -m json.tool

### Updates ###

Resources can be updated by making PUT requests to the corresponding
URI. Because the API implements optimistic locking, it is necessary to
obtain the current version of the resource before attempting an
update.

    $  curl -b cookies.txt http://localhost:8000/api/specify/agent/3/ | grep -o '\"version\": [0-9]*'
    "version": 144

To modify the resource we must supply the same version:

    $  curl -b cookies.txt -X PUT --data '{"version": 144, "remarks":"test"}' http://localhost:8000/api/specify/agent/3/
    **AGENT DATA WITH UPDATED REMARKS FIELD AND INCREMENTED VERSION NUMBER**

If the version if is omitted, the server will return a `400 Bad
Request`. If the version does not match the current version in the
database, `409 Conflict` will be returned.

### Resource creation ###

Resources can be created by issuing a POST request to the collection
URI representing the target table. To create a new collection object:


    $  curl -b cookies.txt -v -X POST --data '{"collection":"/api/specify/collection/4/"}' http://localhost:8000/api/specify/collectionobject/
    **DATA OF NEWLY CREATED COLLECTION OBJECT**

### Resource deletion ###

Delete resources by issuing DELETE to the resource's URI. Because no
data is included in a DELETE request, the version information is
placed in the `If-Match` HTTP header:

    $  curl -b cookies.txt -v -X DELETE -H 'If-Match: 0' http://localhost:8000/api/specify/collectionobject/123456789/

where `0` and `123456789` are replaced with the appropriate
values. Again, if the version info is missing or out of date, an error
will be returned.
