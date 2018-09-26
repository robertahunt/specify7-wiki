# Darwin Core Archive Publishing in Specify 7

Specify 7 is capable of exporting collections data in the [Darwin Core
Archive](https://en.wikipedia.org/wiki/Darwin_Core) format. This
capability expands on the existing Specify 6 functionality by
supporting extensions to the core format.

Publishing of DwCAs in Specify 7 is managed via
several [App Resource](#app-resource) records in XML format. There are
three relevant types of
resource: the
[Basic DwCA definition resource](#basic-dwca-definition-resource), 
the [Dataset metadata resource](#dataset-metadata-resource), and
the [Export feed resource](#export-feed-resource).

## Table of Contents

1. [Darwin Core Archive Publishing in Specify 7](#darwin-core-archive-publishing-in-specify-7)
2. [App resources](#app-resources)
3. [Basic DwCA definition resource](#basic-dwca-definition-resource)
   1. [Example Specify 7 DwCA definition](#example-specify-7-dwca-definition)
   2. [Query stanzas](#query-stanzas)
      1. [Example query export](#example-query-export)
   3. [Testing a DwCA definition resource](#testing-a-dwca-definition-resource)
4. [Dataset metadata resource](#dataset-metadata-resource)
5. [Export feed resource](#export-feed-resource)
   1. [Example Specify 7 export feed resource](#example-specify-7-export-feed-resource)
   2. [Export feed endpoint](#export-feed-endpoint)
6. [Updating the data publishing feed](#updating-the-data-publishing-feed)
   
   
## App resources

App resources can be accessed by admin users through the **Resources**
option in the *User Tools* dialog that is opened by clicking on the
user name, or by navigating to `http://SITE/specify/appresources/` on a Specify 7
site.

The App Resources are organized hierarchically starting with *Global*
and *Discipline* level resources and descending through *Collection*,
*User Types*, and *User* levels. The various levels may be expanded or
collapsed by clicking on the headings. At each level, individual
resources can be opened by clicking on the name of the resource, and
new resources can be added by clicking on **New Resource** and entering
a resource name.

## Basic DwCA definition resource

The fundamental resource for producing a DwCA consists of an XML
definition that is modeled on the DwCA *Metafile* (`meta.xml`) as
described in
the
[Darwin Core Text Guide](http://rs.tdwg.org/dwc/terms/guides/text/index.htm),
hereafter DCTG.

New resources for DwCA definitions should be created under a particular
*collection* level within the App Resource hierarchy corresponding to
the collection whose data they will be used to export.

The basic structure is a single `<archive>` element containing exactly
one `<core>` stanza and optional `<extension>` stanzas, each of which must
indicate the `rowType` that specifies the class of data represented by
that stanza. This corresponds exactly to the `rowType` that will
appear in the generated `meta.xml` as described in the DCTG.

Each `<core>` and `<extension>` element may contain multiple free standing
`<field>` elements with `term` and `value` attributes. The `term` is the
URI for the term represented by the field, and `value` is a value for
that term which is the same for all rows in the data set represented
by the stanza. Aside from these constant values, the data for each row
in the data set will be produced from one or more queries defined in
the `<queries>` stanza within each `<core>` and `<extension>` stanza.

### Example Specify 7 DwCA definition

```xml
<?xml version="1.0" encoding="utf-8"?>
<archive>

  <!-- The core of this DwCA will consist of Occurrence records. -->
  <core rowType="http://rs.tdwg.org/dwc/terms/Occurrence"> 
  
    <!-- Free standing constant fields -->
    
    <field term="http://rs.tdwg.org/dwc/terms/institutionCode"
        value="KU"/>
    
    <field term="http://rs.tdwg.org/dwc/terms/institutionID"
        value="http://grbio.org/cool/iakn-125z"/>
        
    <field term="http://purl.org/dc/terms/rights"
        value="http://creativecommons.org/licenses/by/4.0/deed.en_US"/>
        
    <field term="http://purl.org/dc/terms/accessRights"
        value="http://biodiversity.ku.edu/research/university-kansas-biodiversity-institute-data-publication-and-use-norms"/>
        
    <field term="http://rs.tdwg.org/dwc/terms/collectionCode"
        value="KUI"/>
        
    <field term="http://rs.tdwg.org/dwc/terms/basisOfRecord"
        value="PreservedSpecimen"/>
        
    <field term="http://rs.tdwg.org/dwc/terms/datasetName"
        value="University of Kansas Biodiversity Institute Fish Voucher Collection"/>

    <!-- The remaining field are produced by the following query: -->
    <queries>
      <query contextTableId="1" name="occurrence.txt">
        <id    term="http://rs.tdwg.org/dwc/terms/occurrenceID" isNot="false" isRelFld="false" oper="11" stringId="1.collectionobject.guid" value=""/>
        <field term="http://purl.org/dc/terms/accessRights" isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.termsOfUse" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/basisOfRecord" isNot="false" isRelFld="false" oper="11" stringId="1,23.collection.collectionType" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/catalogNumber" isNot="false" isRelFld="false" oper="1" stringId="1.collectionobject.catalogNumber" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/class" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Class" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/collectionCode" isNot="false" isRelFld="false" oper="11" stringId="1,23.collection.code" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/continent" isNot="false" isRelFld="false" oper="11" stringId="1,10,2,3.geography.Continent" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/country" isNot="false" isRelFld="false" oper="11" stringId="1,10,2,3.geography.Country" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/county" isNot="false" isRelFld="false" oper="11" stringId="1,10,2,3.geography.County" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/datasetName" isNot="false" isRelFld="false" oper="11" stringId="1,23.collection.description" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/decimalLatitude" isNot="false" isRelFld="false" oper="1" stringId="1,10,2.locality.latitude1" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/decimalLongitude" isNot="false" isRelFld="false" oper="1" stringId="1,10,2.locality.longitude1" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/eventDate" isNot="false" isRelFld="false" oper="1" stringId="1,10.collectingevent.startDate" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/family" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Family" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/fieldNumber" isNot="false" isRelFld="false" oper="11" stringId="1.collectionobject.fieldNumber" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/genus" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Genus" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/geodeticDatum" isNot="false" isRelFld="false" oper="11" stringId="1,10,2.locality.datum" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/georeferencedDate" isNot="false" isRelFld="false" oper="1" stringId="1,10,2,123-geoCoordDetails.geocoorddetail.geoRefDetDate" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/higherGeography" isNot="false" isRelFld="false" oper="11" stringId="1,10,2,3.geography.fullName" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/infraspecificEpithet" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Subspecies" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/institutionCode" isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.code" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/institutionID" isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.altName" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/kingdom" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Kingdom" value=""/>
        <field term="http://purl.org/dc/terms/license" isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.copyright" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/locality" isNot="false" isRelFld="false" oper="11" stringId="1,10,2.locality.localityName" value=""/>
        <field term="http://purl.org/dc/terms/modified" isNot="false" isRelFld="false" oper="1" stringId="1.collectionobject.timestampModified" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/order" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Order" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/phylum" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Phylum" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/preparations" isNot="false" isRelFld="true" oper="11" stringId="1,63-preparations.preparation.preparations" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/recordedBy" isNot="false" isRelFld="true" oper="11" stringId="1,10,30-collectors.collector.collectors" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/scientificName" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.fullName" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/specificEpithet" isNot="false" isRelFld="false" oper="11" stringId="1,9-determinations,4-preferredTaxon.taxon.Species" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/stateProvince" isNot="false" isRelFld="false" oper="11" stringId="1,10,2,3.geography.State" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/typeStatus" isNot="false" isRelFld="false" oper="1" stringId="1,9-determinations.determination.typeStatusName" value=""/>
        <field isNot="false" isRelFld="false" oper="13" stringId="1,9-determinations.determination.isCurrent" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/sex" isNot="false" isRelFld="false" oper="1" stringId="1.collectionobject.text2" value=""/>
        <field term="http://rs.tdwg.org/dwc/terms/lifeStage" isNot="false" isRelFld="false" oper="1" stringId="1.collectionobject.text3" value=""/>
      </query>
    </queries>
  </core>
  
  <!-- The DwCA will include a Multimedia extension. -->
  
  <extension rowType="http://rs.tdwg.org/ac/terms/Multimedia">
  
    <!-- Constant fields. -->
    <field term="http://purl.org/dc/terms/rights" value="http://creativecommons.org/licenses/by/4.0/deed.en_US"/>
    <field term="http://purl.org/dc/terms/accessRights" value="http://biodiversity.ku.edu/research/university-kansas-biodiversity-institute-data-publication-and-use-norms"/>

    <!-- In this case there are two queries to produces the rows. -->
    <queries>
    
      <!-- Rows from the collection object attachments records. -->
      <query contextTableId="111" name="collectionobjectattachment.csv">
        <id isNot="false" isRelFld="false" oper="1" stringId="111,1.collectionobject.guid" value=""/>
        <field term="http://rs.tdwg.org/ac/terms/accessURI" isNot="false" isRelFld="true" oper="1" stringId="111,41.attachment.attachment" value="" formatName="AttachmentTest"/>
        <field term="http://purl.org/dc/terms/identifier" isNot="false" isRelFld="false" oper="1" stringId="111,41.attachment.guid" value=""/>
        <field term="http://purl.org/dc/terms/format" isNot="false" isRelFld="false" oper="1" stringId="111,41.attachment.mimeType" value=""/>
      </query>
      
      <!-- Rows from the research work attachment records. -->
      <query contextTableId="1" name="researchworkattachment.csv">
        <id isNot="false" isRelFld="false" oper="1" stringId="1.collectionobject.guid" value=""/>
        <field term="http://rs.tdwg.org/ac/terms/accessURI" isNot="false" isRelFld="true" oper="1" stringId="1,29-collectionobjectcitations,69,143-referenceworkattachments,41.attachment.attachment" value="" formatName="AttachmentTest"/>
        <field term="http://purl.org/dc/terms/identifier" isNot="true" isRelFld="false" oper="12" stringId="1,29-collectionobjectcitations,69,143-referenceworkattachments,41.attachment.guid" value=""/>
        <field term="http://purl.org/dc/terms/format" isNot="false" isRelFld="false" oper="1" stringId="1,29-collectionobjectcitations,69,143-referenceworkattachments,41.attachment.mimeType" value=""/>
      </query>
    </queries>
  </extension>
</archive>
```


### Query stanzas

The query stanzas within the DwCA definition are meant to be generated
with the assistance of the Specify 7 query builder. After a Specify
query has been defined, tested, and saved using the query builder, it
can be used as the basis for a query stanza. There is an **export**
button in the edit dialog that appears when the pencil icon next to
query name is clicked. The **export** button will open a dialog
containing XML that can be copy-pasted into an editor for inclusion in
a DwCA definition.

After the XML export of the query has been copied, the Specify query
can be deleted, if so desired.


#### Example query export

```xml
<?xml version="1.0" ?>
<query contextTableId="1" name="DwCKUI">
	<field isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.code" value=""/>
	<field isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.altName" value=""/>
	<field isNot="false" isRelFld="false" oper="11" stringId="1,23,26,96,94.institution.copyright" value=""/>
    <!-- . -->
    <!-- . -->
    <!-- . -->
    <!-- . -->
</query>

```

The exported XML contains enough information to generate the rows for
the DwCA file, but needs to be edited to include the `term` attribute
for each field which should be a URI. Fields which do not specify a
term will not be included in the output but maybe required for
filtering within the query.

It is also necessary for one of the fields to be designated the *core
id* field by changing the element tag from `field` to `id`. This field
will become the `<id>` field if included in the `<core>` stanza or the
`<coreId>` field if included in an `<extension>` stanza. 

See the DCTG for more information about the `<id>` and `<coreId>` fields
and `term` attributes.

Finally, the `name` attribute should be adjusted to an appropriate
filename for the corresponding CSV file that will be included in the
DwCA file. That is, it should be unique within a given DwCA definition
and not contain special characters.

After these modifications, a query element and its contents can be
copied into the `<queries>` element of a DwCA definition resource.

##### Overriding the formatter or aggregator

When a query field requires formatting or aggregating rows from a
table Specify uses definitions from the **DataObjFormatters**
appresource at the corresponding discipline level. By default the
data export will use the same formatters and aggregators as the form
system as configured through the schema config tool. For increased
flexibility this mechanism can be overridden by adding a `formatName`
attribute to `<field>` elements in the query stanza. The value of the
attribute should be the name of one of the formatters or aggregators
defined in **DataObjFormatters** for the relavent table.

For instance, the following would apply the formatter named
`CreateAttachmentURL` to the rows from the attachment table:

```
<query contextTableId="111" name="collectionobjectattachment.csv">
    <!-- . -->
    <field term="http://rs.tdwg.org/ac/terms/accessURI" isNot="false"
           isRelFld="true" oper="1" 
           stringId="111,41.attachment.attachment" value=""
           formatName="CreateAttachmentURL"/>
    <!-- . -->
</query>
```

### Testing a DwCA definition resource

After a DwCA definition resource has been created, it can be tested by
instructing Specify to produce a DwCA file based on it.

The user should be logged into the collection corresponding to the
DwCA definition resource. Clicking on the user name followed by **Make
DwCA** in *User Tools* opens a dialog that accepts a **DwCA definition**
and a **Metadata resource**. Enter the name of the DwCA definition
resource in the **DwCA definition** field, and click start. The
**Metadata resource** can be left blank. Specify will begin running the
queries and generating the DwCA file. A notification with a link to
download the archive will be produced when the process completes.

## Dataset metadata resource

Specify 7 can include a metadata file in generated DwCA files
describing the whole dataset
in
[Ecological Metadata Language](https://en.wikipedia.org/wiki/Ecological_Metadata_Language).
Like the DwCA definition resource, the metadata resource should be
created at the *collection* level of the resource hierachy.

When generating a DwCA
as [described above](#testing-a-dwca-definition-resource), the name of
a metadata resource can be provided. The contents of the resource will
be included in the generated DwCA unchanged except for `pubDate` which
will be updated to reflect the date the archive was generated. Inside
the archive the resource will be given the filename `eml.xml`.

## Export feed resource

One final resource, the export feed resource, allows DwCA generation to
be automated and advertised through an RSS feed. While a single
Specify 7 instance (or database) may utilize multiple DwCA definition
and metadata resources, it may only use one export feed resource that
will define all of the published exports. The export feed resource
must be created at the *Global Resources* level of the hierarchy and
must be named `ExportFeed`.

The content of the export feed resource should be XML with a root
element `<channel>` comprising one or more `<item>` elements. The
channel element corresponds to the eponymous RSS element and may also
include `<title>`, `<description>`, and `<language>` elements to be
passed through to the generated RSS unchanged. Each `<item>` resource
corresponds to an individual DwCA that may be published by the system.

### Export feed item

Like the `<channel>` element, the `<item>` element accepts several
child elements that will be included in the RSS unchanged. These are
`<title>`, `<description>`, `<id>`, and `<guid>`.

Additionally, the `<item>` element requires the following attributes
that control the generation of the DwCA the item is to represent:

1. `collectionId` - This is the database id of the collection whose
   data the DwCA will contain. The id of a collection can be found by
   logging into the collection in a browser then visiting the URL
   `http://SITE/context/collection/` and inspecting the `current` value in the
   resulting JSON.
   
2. `userId` - This is the database id of the Specify user under whose
   account the DwCA query should be executed. This can, in principle,
   affect things like which formatters are used and should generally
   be the same user as created the query. User ids can be found in the
   URL shown in the browser when editing a user record.
   
3. `notifyUserId` - This attribute is optional and indicates that
   notifications generated during export should go to the indicated
   user rather than the user specified by `userId`.
   
4. `definition` - The name of
   the [DwCA definition resource](#basic-dwca-definition-resource) to
   be used to generate the DwCA. This resource must be associated with
   the collection indicated by `collectionId`.
   
5. `metadata` - The name of
   the [dataset metadata resource](#dataset-metadata-resource) to be
   included in the DwCA. This resource must be associated with
   the collection indicated by `collectionId`.
   
6. `days` - When the RSS feed is updated via
   the [command line tool](#updating-the-data-publishing-feed), the DwCA represented
   by this item will only be updated if it is more than the stated
   number of days old.
   
7. `filename` - The filename to be given to the generated DwCA. This
   filename will be used in URLs, so it is better if it doesn't
   contain any awkward characters.
   
8. `publish` - If this attribute is not included and set to `true`,
   the corresponding DwCA will not be included in the RSS
   feed. Nevertheless, it will be generated or updated when
   the [feed is updated](#updating-the-data-publishing-feed), and the
   file will continue to be available for download to those knowing
   the URL.
   

### Example Specify 7 export feed resource

```xml
<channel>
  <title>KUBI ichthyology RSS Feed</title>
  <description>RSS feed for KUBI Ichthyology Voucher and Tissue collections</description>

  <item collectionId="4" userId="2" notifyUserId="2" definition="DwCA_voucher" metadata="DwCA_voucher_metadata" days="7" filename="kui-dwca.zip" publish="true">
    <title>KU Fish</title>
    <id>8f79c802-a58c-447f-99aa-1d6a0790825a</id>
  </item>

  <item collectionId="32768" userId="2" notifyUserId="2" definition="DwCA_tissue" metadata="DwCA_tissue_metadata" days="7" filename="kuit-dwca.zip" publish="true">
    <title>KU Fish Tissue</title>
    <id>56caf05f-1364-4f24-85f6-0c82520c2792</id>
  </item>
</channel>
```

### Export feed endpoint

When a valid `ExportFeed` resource is present at the *Global
Resources* app resource level, the `http://SITE/export/rss/` URL will become
active and return the generated RSS feed.


## Updating the data publishing feed

When an RSS publishing feed has been [defined](#export-feed-resource)
its contents can be updated in two separate ways.

1. An admin user may select *Update Feed Now* from the *User Tools*
   dialog. This will immediately update all items in the feed
   irrespective of any `days` attribute. Notifications will also go to
   the activating user rather than the user specified by the
   `notifyUserId` attribute.
   
2. The following command may be invoked on the Specify 7 server from
   the Specify 7 installation directory:
   ```sh
   python manage.py update_feed [--force]
   
   ```
   The `--force` option will cause all items in the feed to be updated
   regardless of any `days` attributes. Otherwise, only DwCA files
   older than the given number of days will be updated by this
   command.
   
   This command is intended to facilate a work-flow utilizing *cron*
   or other task scheduler to maintain an up-to-date data publishing
   feed.
   
   Note: If the installation is utilizing a python virtualenv, it
   must be activated prior to issuing the command.
