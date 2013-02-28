How to use specifyweb API as a generic webservice.
==========

This is a simple introduction to accessing the specifyweb API using
the `curl` command line utility. For legibility, JSON output is piped
through a pretty-printer provided by the Python json library. It is
hoped that these examples illustrate how the API could be utilized by
any environment which supports HTTP and JSON.

These examples use the demo server at `http://dhwd99p1.nhm.ku.edu`.

# Logging in #

To prevent unauthorized access to the API the browser session is
tracked with a session cookie that must be associated with a logged in
user.

Presently, the login mechanism is designed to be accessed by a
browser. In the future it would be straightforward to add a mechanism
for simpler automated logins. Nevertheless, here is how to login with
curl:

    $ curl -c cookies.txt http://dhwd99p1.nhm.ku.edu/accounts/login/ | grep csrfmiddlewaretoken | grep -o value=\'.*\'
    value='LMuk6ZD3ejheZR6Q51gidio3le6w4jWa'

The login form utilizes a token to prevent cross site scripting
attacks. The token will be needed to submit the login request. The
token is easily extracted using grep and is visible as the last line
above, `LMuk6ZD3ejheZR6Q51gidio3le6w4jWa`.

Now to submit the login request:

    $ curl -c cookies.txt -b cookies.txt -d"username=testuser&password=testuser&csrfmiddlewaretoken=LMuk6ZD3ejheZR6Q51gidio3le6w4jWa&this_is_the_login_form=1&next=&collection_id=4" -X POST http://dhwd99p1.nhm.ku.edu/accounts/login/

Notice that csrf token has been included in the request data. You will
have to use the value obtained in the first step.

# Making API requests #

Once a logged-in session has been established and stored in the
`cookie.txt` file, making API requests is easy. Let's fetch the
collection object with id 12.

    $ curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/collectionobject/12/ | python -m json.tool
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


    $ curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/collectionobject/ | python -m json.tool
    **LOTS OF DATA**

By default a maximum of twenty rows will be returned when requesting a
collection. The `limit` query parameter can be used to adjust the
amount. Using `limit=0` returns ALL rows.

    $ curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/taxon/?limit=2 | python -m json.tool
    **TWO TAXON RECORDS**

The `offset` query parameter can be used along with `limit` to
implement paging.

### Filtering ###

Often it will be necessary to retrieve a collection of resources that
meet certain criteria. The API implements basic filtering for this
purpose. E.g. to retrieve all preparation records for collection
object with id 12:

    $ curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/preparation/?collectionobject=12 | python -m json.tool

Collection requests can also be filtered by **domain**, which means
that only records which are in the same collection context as the user
will be returned.

    $ curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/taxon/?domainfilter=true | python -m json.tool

### Updates ###

Resources can be updated by making PUT requests to the corresponding
URI. Because the API implements optimistic locking, it is necessary to
obtain the current version of the resource before attempting an
update.

    $  curl -b cookies.txt http://dhwd99p1.nhm.ku.edu/api/specify/agent/3/ | grep -o '\"version\": [0-9]*'
    "version": 144

To modify the resource we must supply the same version:

    $  curl -b cookies.txt -X PUT --data '{"version": 144, "remarks":"test"}' http://dhwd99p1.nhm.ku.edu/api/specify/agent/3/
    **AGENT DATA WITH UPDATED REMARKS FIELD AND INCREMENTED VERSION NUMBER**

If the version if is omitted, the server will return a `400 Bad
Request`. If the version does not match the current version in the
database, `409 Conflict` will be returned.

### Resource creation ###

Resources can be created by issuing a POST request to the collection
URI representing the target table. To create a new collection object:


    $  curl -b cookies.txt -v -X POST --data '{"collection":"/api/specify/collection/4/"}' http://dhwd99p1.nhm.ku.edu/api/specify/collectionobject/
    **DATA OF NEWLY CREATED COLLECTION OBJECT**

### Resource deletion ###

Delete resources by issuing DELETE to the resource's URI. Because no
data is included in a DELETE request, the version information is
placed in the `If-Match` HTTP header:

    $  curl -b cookies.txt -v -X DELETE -H 'If-Match: 0' http://dhwd99p1.nhm.ku.edu/api/specify/collectionobject/123456789/

where `0` and `123456789` are replaced with the appropriate
values. Again, if the version info is missing or out of date, an error
will be returned.


