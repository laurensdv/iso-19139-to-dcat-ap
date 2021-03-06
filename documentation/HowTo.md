# How to use the XSLT

The XSLT can be run directly on the ouput of a CSW or on single metadata records, by using any of the programming languages supporting XSLT conversion.

The actual output of the XSLT depends on configuration parameters that specify:

* whether to use the _core_ or _extended_ GeoDCAT-AP profile;
* which spatial reference system to use for the geometry encoding of the bounding box (when available).
* The HTTP URIs to be used for the metadata record and/or the described resource.

The following sections describe first the XSLT configuration parameters, and then give some examples on how to run the XSLT.

## Configuration parameters

### Parameter `$profile`

This parameter specifies the GeoDCAT-AP profile to be used:

* value `core`: the GeoDCAT-AP _core_ profile, which includes only the INSPIRE / "core" ISO 19115 metadata elements supported in DCAT-AP.
* value `extended`: the GeoDCAT-AP _extended_ profile, which defines mappings for all the INSPIRE / "core" ISO 19115 metadata elements.

The default value is: `extended`.
  
The current specifications for the core and extended GeoDCAT-AP profiles are available on the Joinup collaboration platform of the EU ISA Programme:

    https://joinup.ec.europa.eu/node/139283/

### Parameters for the spatial reference system of the bounding box

These parameters denote the URI and URN, respectively, of the spatial reference system (SRS) used in the geometry encodings of the bounding box generated by the XSLT (WKT, GML, and GeoJSON). More precisely:

* The SRS URI (parameter `$SrsUri`) is used in the WKT and GML encodings of the bounding box.
* The SRS URN (parameter `$SrsUrn`) is used in the GeoJSON encoding of the bounding box.

The SRS URI's and URN's to be used are those operated by the OGC's registry:

* URIs: `http://www.opengis.net/def/crs/<authority>/(<version>|0)/<code>`. Examples: 
    * [`http://www.opengis.net/def/crs/OGC/1.3/CRS84`](http://www.opengis.net/def/crs/OGC/1.3/CRS84) (CRS84: WGS84 lon/lat)
    * [`http://www.opengis.net/def/crs/EPSG/0/4326`](http://www.opengis.net/def/crs/EPSG/0/4326) (EPSG:4326: WGS84 lat/lon)
* URNs: `urn:ogc:def:crs:<authority>:[<version>]:<code>`. Examples: 
    * `urn:ogc:def:crs:OGC:1.3:CRS84` (CRS84: WGS84 lon/lat)
    * `urn:ogc:def:crs:EPSG::4326` (EPSG:4326: WGS84 lat/lon)

The default SRS is CRS84 (WGS84 lon/lat). If a different SRS is used, the axis order (lat/lon or lon/lat) must be explicitly specified in parameter `$SrsAxisOrder`.

The possible values for parameter `$SrsAxisOrder` are the following:

* `LatLon`: latitude / longitude.
* `LonLat`: longitude / latitude.

As already mentioned, the axis order must be specified only if the reference SRS is different from CRS84. If the reference SRS is CRS84, the value specified in parameter `$SrsAxisOrder` is ignored.

### Parameters `$ResourceUri` and `$MetadataUri` 

These parameters must be customised depending on the strategy used to assign HTTP URIs.
  
The default rule implies that HTTP URIs are specified for the metadata file identifier (metadata URI) and the resource identifier (resource URI). For more details and examples, see [the relevant XSLT documentation](./HTTP-URIs.md).

## Running the XSLT

This section provides examples of code from popular programming languages that can be used to run the XSLT.

### PHP

````php
<?php

// The URL of the XML document to be transformed. Here it corresponds to a "GetRecords" output of a fictitious CSW, with the "maxRecords" parameter set to 10.
  $xmlURL = "http://some.site/csw?request=GetRecords&service=CSW&version=2.0.2&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.isotc211.org/2005/gmd&outputFormat=application/xml&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&maxRecords=10";

// The URL pointing to the latest version of the XSLT.
  $xslURL = "https://webgate.ec.europa.eu/CITnet/stash/projects/ODCKAN/repos/iso-19139-to-dcat-ap/browse/iso-19139-to-dcat-ap.xsl?raw";
  
  $xml = new DOMDocument;
  $xml->load($xmlURL) or die();

  $xsl = new DOMDocument;
  $xsl->load($xslURL) or die();
  
  $proc = new XSLTProcessor();
  $proc->importStyleSheet($xsl);
  
  echo $proc->transformToXML($xml);

?>
````

### Python

````python
import lxml.etree as ET

# The URL of the XML document to be transformed. Here it corresponds to a "GetRecords" output of a fictitious CSW, with the "maxRecords" parameter set to 10.
xmlURL = "http://some.site/csw?request=GetRecords&service=CSW&version=2.0.2&namespace=xmlns%28csw=http://www.opengis.net/cat/csw%29&resultType=results&outputSchema=http://www.isotc211.org/2005/gmd&outputFormat=application/xml&typeNames=csw:Record&elementSetName=full&constraintLanguage=CQL_TEXT&constraint_language_version=1.1.0&maxRecords=10"

# The URL pointing to the latest version of the XSLT.
xslURL = "https://webgate.ec.europa.eu/CITnet/stash/projects/ODCKAN/repos/iso-19139-to-dcat-ap/browse/iso-19139-to-dcat-ap.xsl?raw"
  
xml = ET.parse(xmlURL)
xsl = ET.parse(xslURL)

transform = ET.XSLT(xsl)

print(ET.tostring(transform(xml), pretty_print=True))
````

### Java

TBD
