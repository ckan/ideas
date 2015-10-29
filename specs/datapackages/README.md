# CKAN / Data Package integration

## Overview
[Data Packages](http://data.okfn.org/doc/data-package) are a really simple specification for packaging up data files to allow easy reuse and sharing. They are essentially a descriptor file named `datapackage.json` which contains metadata about the dataset and points towards the data files.   It has a focus on simplicity and extensibility.

For instance, there is a special flavour of Data Package aimed at [Tabular data](http://data.okfn.org/doc/tabular-data-package) which enforces the use of the [JSON Table schema](http://dataprotocols.org/json-table-schema/), another spec part of the same family of standards which allows to describe the structure of CSV files.

The full Data Package spec can be found here:

http://dataprotocols.org/data-packages/

There is a growing number of [tools](http://data.okfn.org/tools) being developed around Data Packages. Improving its support within CKAN would allow out of the box integration with this ecosystem of tools.

Also besides direct support for handling Data Packages, there is a number of areas where adopting and implementing standards around them could help deal with long standing issues in CKAN. Perhaps the most clear example is the JSON Table Schema, which would allow publishers to properly describe data shown on previews or being imported to the DataStore. Implementing this would be a massive win in order to build better import and ETL tools into CKAN (or even just to get the previews right).

## Prior work

Open Knowledge developed a while ago http://datapackager.okfn.org, a tool for creating Data Packages from data files.

It was implemented in CKAN (as a CKAN [extension](https://github.com/ckan/ckanext-datapackager) that heavily customized some parts of the CKAN UI).

It contains functions to translate Data Packages metadata into CKAN datasets and viceversa, but perhaps more interestingly includes logic functions and an interface for modifying the schema for tabular resources:

![Schemas](http://i.imgur.com/i5L5oNz.png)



## Main goals

1. Allow importing Data Packages into CKAN, creating the necessary datasets and resources, both via the UI and the API

2. Allow exporting CKAN datasets as Data Packages, regardless of whether they were imported from Data Packages in a first instance or not.

3. Support schema definition for CKAN resources, allowing publishers to describe the contents of published files


For achieving this it seems sensible to reuse as much stuff as we can from ckanext-datapackager, extending it if necessary

The first two points could be implemented either in core or separate extension, but I'd like to propose that the third regarding resource schemas is implemented in core (in a generic form). It seems central enough to all future work around data import, cleaning, etc (eg closer integration with DataStore) to justify it.

## Field mapping    

Both for transforming Data Packages into CKAN datasets and to generate Data Packages from CKAN datasets we need to have a good mapping between the two.

CKAN **dataset** core fields have essentially the same equivalents on Data Packages (`title`, `description` , `keywords`, ...). Potential issues:

* `name` - collision with existing CKAN one (we can just add an incremental number)
* `license` - mapping to the ones available on the CKAN instance. **Q:** What happens if we can not map it?
* `author` / `contributors` - these refer to the datapackage creators not the data ones (`sources` is used for this) **Q:** Should be bother trying to map to CKAN's `author` / `author_email` or `maintainer` /  `maintainer_email`?

Data Packages can have extra fields not present in the CKAN schema:
* `sources` (name, web, email)
* `image`
* `base`
* `dataDependencies` (ordering of the hash keys is important)
* `schemas` 
* Arbitrary fields added to the Data Package

If we don't store the original Data Package somewhere we'll need to store these as extras. 

As per **resources**,  Data Packages `resources` can point the actual data files using `url` or `path` + `base`, or have the data embedded with a `data` field. (The current spec allows all of them to be present at the same time, which hopefully we can restrict https://github.com/dataprotocols/dataprotocols/issues/223). If Data Packages have `data` embedded we can push it to the DataStore to make it available.

Most fields map to CKAN core ones. The ones that are not defined are:

* `encoding`
* `hash`
* `sources`
* `license`

This is not as problematic as dataset fields, as we can add arbitrary top level fields to resources.

## Importing Data Packages

### Implementation

At a higher level this seems quite straight-forward: a  `datapackage_import` or similar action which inherits the auth from `package_create` and `package_update` (eg you need to belong to an org by default etc). The action will validate the JSON object, transform it to a CKAN dict and create or update the relevant package. A controller will handle requests from the UI (at `/datapackage/import`).
In terms of UI there would be a dedicated page for importing Data Packages. This will essentially contain just an upload field to upload the `datapackage.json` or a text file to enter the URL to an accessible one. This page would show validation errors and redirect to the dataset page once it's created.

Perhaps to make it more accessible we can link to this page from the dataset creation form (from the top bar or the left hand column).

### Potential issues

Identifiers: Data Packages are solely identified by the `name` field, which according to the Data Package spec:

> [is a] short url-usable (and preferably human-readable) name of the package. This MUST be lower-case and contain only alphanumeric characters along with ., _ or - characters. It will function as a unique identifier and therefore SHOULD be unique in relation to any registry in which this package will be deposited (and preferably globally unique).

We can not assume that these identifiers will be unique and that they will not clash with existing CKAN datasets. We can append stuff to the name but then we need to consider what happens when we reupload the same Data Package (ie to update the dataset).

**Q:** Do we upload the files described on the Data Package to the CKAN server?
I would say not by default. As long as the files are publicly accessible the DataPusher can get them into the DataStore.

**Q:** Do we keep a copy of the original `datapackage.json` ?
I think yes. Trying to store all fields as extras and replicate them back when generating the datapackage back would be too much hassle. Also we would need to keep adding fields as the spec changes and handle arbitrary fields added by publishers. An easier approach might be outputting back what was uploaded, but with the fields that are actually mapped to CKAN (title, description, keywords etc) updated, as these might have been modified in CKAN.

## Exporting Data Packages

### Implementation

Accessing `/dataset/{id}/datapackage.json` will output the generated Data Package for this dataset. Similarly as above, we'll have a `datapackage_export` action (with the same auth as `package_show`) and a controller to handle requests.

### Potential issues

See point above regarding outputting back custom fields.

I think that the JSON files could be generated on the fly, but if performance is an issue the Data Package JSON blobs for each dataset could be generated before hand (using `after_create` and `after_update`) and stored in the DB or even as a field in the Solr index.

**Q:** So far we've only considered giving back the `datapackage.json` file, but would it make sense to have an extra endpoint that returns a zip of the `datapackage.json` plus the data files?

## Resource schemas


### Implementation

Add new core fields to the resource model:

 * `schema`: a JSON object describing the structure of the resource file, or a URL linking to the schema.
 * `schemaType`: a string or URI that specifies what is the specification for the schema. 
 * `schemaVersion`: the version of the spec being used

For all these, and if we are talking about tabular data, I'd default to JSON Table Schema. But these fields are flexible enough that they can be used on other data formats as well.

Once the changes on the model are in place we will incorporate an enhanced version (or a new one if necessary) of the schema editor mentioned above. This will be able to be accessed by publishers when creating a dataset (if they are adding a tabular data file) or afterwards editing the resource.


TODO:

### Potential issues

Future:
Change DataStore fields according to schema

