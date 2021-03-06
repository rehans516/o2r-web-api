# Ship compendia and metadata

Shipments are used to deliver ERCs or their metadata to third party repositories or archives. This section covers shipment related requests, including repository file management.

## List shipments

This is a basic request to list all available shipments (no pagination yet).

`GET /api/v1/shipment`

```json
200

{
  "shipments": ["dc351fc6-314f-4947-a235-734ab5971eff", "..."]
}
```

### Get a single specific shipment

`GET /api/v1/shipment/dc351fc6-314f-4947-a235-734ab5971eff`

```json
200 

{
  "last_modified": "2016-12-12 10:34:32.001475",
  "recipient": "zenodo",
  "id": "dc351fc6-314f-4947-a235-734ab5971eff",
  "deposition_id": "63179",
  "user": "0000-0001-6021-1617",
  "status": "shipped",
  "compendium_id": "4XgD9",
  "deposition_url": "https://sandbox.zenodo.org/record/63179"
}
```

You can also get only the shipments belonging to a compendium id (e.g. _4XgD97_).

`GET /api/v1/shipment?compendium_id=4XgD97`

URL parameter:
- `compendium_id` - The identifier of a specific compendium.

```json
200 

{
  "last_modified": "2016-12-12 10:34:32.001475",
  "recipient": "zenodo",
  "id": "dc351fc6-314f-4947-a235-734ab5971eff",
  "deposition_id": "63179",
  "user": "0000-0001-6021-1617",
  "status": "shipped",
  "compendium_id": "4XgD9",
  "deposition_url": "https://sandbox.zenodo.org/record/63179"
}
```

_Note that returned deposition urls from Zenodo as well as Eudat b2share (records) will only be functional after publishing._




## Create a new shipment

You can start a initial creation of a shipment, leading to transmission to a repository at the same endpoint using a `POST` request.

`POST /api/v1/shipment`


This requires the following parameters and conditions:

- `compendium_id`: the id of the compendium
- `recipient` (name of the repository; currently only `zenodo` is possible)
- `cookie` user must be logged in with sufficient rights

Optionally, you can specifiy a custom `shipment_id`.


```json
201

{
  "id": "9ff3d75e-23dc-423e-a6c6-6987ac5ffc3e",
  "recipient": "zenodo",
  "status": "shipped",
  "deposition_id": "79102"
}
```

Currently the following recipients are possible: `zenodo` (Zenodo.org) and `eudat` (Eudat b2share).


### Shipment status

A shipment can have two possible status: Fristly its status can be `shipped`. That means a deposition has been created at a repository and completed the necessary metadata for publication. Secondly its status can be `published`. That means the contents of the shipment are published on the repository, in which case it the publishment can not be undone. 

To query a shipment for its current status you may use:

`GET api/v1/shipment/<shipment_id>/status`


```json
200

{
"id": "9ff3d75e-23dc-423e-a6c6-6987ac5ffc3e",
"status": "shipped"
}
```


### Publish a deposition on a supported repository

The publishment is supposed to have completed the status `shipped` where metadata requirements for publication have been checked.

_Note that once published, a deposition can no longer be deleted on the supported repositories._

`PUT api/v1/shipment/<shipment_id>/publishment`


```json
200

{
"id": "9ff3d75e-23dc-423e-a6c6-6987ac5ffc3e",
"status": "published"
}
```


### File management in a repository depot

## Get a list of all files and their properties that are in a depot

`GET api/v1/shipment/<shipment_id>/publishment`



```json
200

{
"files": [{
	"filesize": 393320,
	"id": "bae2a60c-bd59-47e1-a443-b34bb7d0a981",
	"filename": "4XgD9.zip",
	"checksum": "702f4db3e53b22176d1d5ddcda462a27",
	"links": {
		"self": "https://sandbox.zenodo.org/api/deposit/depositions/71552/files/bae2a60c-bd59-47e1-a443-b34bb7d0a981",
		"download": "https://sandbox.zenodo.org/api/files/31dc8f3d-df00-4d8a-bd99-64ef341372b3/4XgD9.zip"
	}
}]
}
```

You can find the `id` of the file you want to interact with in this json list object at `files[n].id`, where `n` is the position of that file in the array. Files can be identified in this response by either their id in the depot, their filename or their checksum.


## Delete a specific file from a depot

`DELETE api/v1/shipment/<shipment_id>/files/<file_id>`

```json
204

{
"deleted": "110d667c-7691-4fc9-93e7-5652a52df6f2"
}
```

In order to delete from a depot, you need state the `file_id` that can be retrieve from querying a shipments files object.



## Error responses 

```json
400

{"error":"bad request"}
```


```json
403

{"error": "insufficient permissions"}
```
