# Darwin Core Archive Publishing in Specify 7

Publishing of DwCAs in Specify 7 is managed via several App Resource
records in XML format. These records can be accessed by admin users
through the **Resources** option in the *User Tools* dialog that is
opened by clicking on the user name, or by navigating to
`/specify/appresources/` on a Specify 7 site.

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
