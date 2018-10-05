# Client Routes

Unlike [public routes](#public-routes), client routes are all prefixed with
`/v1/client` and only perform actions on data owned by your application. All
client routes require an `accessToken` in the Authorization header, as described
in the <a href="#access-tokens">Access Tokens</a> section.

<aside class="success">
  All requests to client routes require an <a href="#access-tokens">Access Token</a>.
</aside>


## Get Client

This endpoint can be used to retrieve the details of your application. This is
useful if you want to check your [gas allowance](#gas-allowance) before sending
a request that would consume gas.

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // your Client record
})
```

> The above API call returns a single [Client](#client).

### HTTP Request

`GET /v1/client`


## Get All Codex Records

This endpoint can be used to retrieve all Codex Records _that your application
currently owns._

<aside class="notice">
  There is no public route to list all Codex Records; you may only list Codex
  Records that your application currently owns.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/records',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  // sort by date created in reverse and get 5 records, skipping the first ten
  body: {
    limit: 5,
    offset: 10,
    order: '-createdAt',
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // an array of your application's Codex Records
})
```

> The above API call returns an array of [Codex Records](#codex-record).

### HTTP Request

`GET /v1/client/records`

### Query Parameters

Parameter    | Type   | Default   | Description
------------ | ------ | --------- | --------------------------------------------
limit        | Number | 100       | How many records to retrieve. Use with `offset` to paginate through Codex Records.
offset       | Number | 0         | How many records to skip before applying the `limit`. Use with `limit` to paginate through Codex Records.
order        | String | createdAt | To sort in reverse order, specify this value as `-createdAt`.


## Create a Codex Record

This endpoint can be used to begin the creation of a Codex Record. With this
request, you're actually creating the Codex Record's [metadata](#metadata) since
the Codex Record itself can't be created until the transaction has been mined on
the blockchain.

<aside class="success">
  This is an asynchronous action. When the Codex Record has been created and
  logged on the blockchain, your application's webhook will be called with the
  new Codex Record in the request body. See <a href="#webhooks">webhooks</a>
  for details.
</aside>

```javascript
import fs from 'fs'
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/record',
  method: 'post',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  // by using "formData" here instead of "body", request will send this request
  //  as multipart/form-data, which is necessary for sending file data
  formData: {
    name: 'Really Cool Codex Record',
    description: 'This is a really cool Codex Record!',
    mainImage: fs.createReadStream('/tmp/uploads/cool-main-image.jpg'),
    images: [
      fs.createReadStream('/tmp/uploads/cool-supplemental-image-1.jpg'),
      fs.createReadStream('/tmp/uploads/cool-supplemental-image-2.jpg'),
    ],
    files: [
      fs.createReadStream('/tmp/uploads/author-notes.txt'),
      fs.createReadStream('/tmp/uploads/certificate-of-authenticity.pdf'),
    ],
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the metadata for the soon-to-be-created Codex Record
})
```

> The above API call returns Codex Record [metadata](#metadata), since the
Codex Record itself can't be created until the transaction has been mined.

### HTTP Request

`POST /v1/client/record`

### Query Parameters

Parameter    | Type             | Description
------------ | ---------------- | ----------------------------------------------
name         | String           | The plain text name of this Codex Record.
description  | String           | _(Optional)_ The plain text description of this Codex Record. The field can be set to `null` or simply omitted to leave the description empty.
mainImage    | File Data        | The main image of this Codex Record.
images       | Array[File Data] | _(Optional)_ An array of supplemental images that belong to this metadata.
files        | Array[File Data] | _(Optional)_ An array of supplemental files that belong to this metadata. This is considered the "[historical provenance](#historical-provenance)" of the Codex Record.


## Modify a Codex Record

This endpoint can be used to modify the "[off-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)"
data for a Codex Record. Since no "on-chain" data can be modified with this
request, the response will be the immediately-updated Codex Record.

<aside class="notice">
  To update "<a href="#quot-off-chain-quot-vs-quot-on-chain-quot-data">on-chain</a>"
  data such as a Codex Record's name, description, and/or images, you must use
  the <a href="#modify-a-codex-record-39-s-metadata">modify metadata route</a>.
</aside>

```javascript
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/record/0',
  method: 'put',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  body: {
    isPrivate: false,
    isHistoricalProvenancePrivate: false,
    whitelistedAddresses: [
      '0xf17f52151ebef6c7334fad080c5704d77216b732',
      '0xc5fdf4076b8f3a5357c5e395ab970b5b54098fef',
      '0x821aea9a577a9b44299b9c15c88cf3087f3b5544',
      '0x0d1d4e623d10f9fba5db95830f7d3839406c6af2',
    ],
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // the immediately-updated Codex Record
})
```

> The above API call returns the immediately-updated [Codex Record](#codex-record).

### HTTP Request

`PUT /v1/client/record/:tokenId`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record to update.

### Query Parameters

At least one of the following query parameters is required for this route:

Parameter                     | Type          | Description
----------------------------- | ------------- | --------------------------------
isPrivate                     | Boolean       | This flag indicates that the metadata for the Codex Record is private and can only be retrieved by the owner, the `approvedAddress`, and the addresses listed in `whitelistedAddresses`.
whitelistedAddresses          | Array[String] | An array of Ethereum addresses allowed to view private metadata for this Codex Record. This allows users to give people read-only access to their Codex Records.
isHistoricalProvenancePrivate | Boolean       | This flag indicates whether or not [historical provenance](#historical-provenance) (i.e. `metadata.files`) should be hidden, regardless of the value of `isPrivate`.

<aside class="warning">
  <code>whitelistedAddresses</code> is provided in this route as a convenience
  for replacing the list along with other "off-chain" data. Whatever you pass in
  will replacing the existing list, so be careful not to accidentally override
  existing data.

  <br><br>

  There are also dedicated routes to manage the <code>whitelistedAddresses</code>
  array, which are more suitable for adding and removing addresses one-by-one.
  See <a href="#get-a-codex-record-39-s-whitelisted-addresses">Get a Codex
  Record's Whitelisted Addresses</a> (and subsequent sections) for details.
</aside>


## Modify a Codex Record's Metadata

This endpoint can be used to modify the "[on-chain](#quot-off-chain-quot-vs-quot-on-chain-quot-data)"
data for a Codex Record. With this request, you're actually creating a
[Pending Update](#pending-update) since the metadata itself can't be modified
until the transaction has been mined on the blockchain.

<aside class="notice">
  To update "<a href="#quot-off-chain-quot-vs-quot-on-chain-quot-data">off-chain</a>"
  data such as a Codex Record's privacy settings, you must use the
  <a href="#modify-a-codex-record">modify Codex Record route</a>.
</aside>

<aside class="success">
  This is an asynchronous action. When the Codex Record has been updated and
  logged on the blockchain, your application's webhook will be called with the
  updated Codex Record in the request body. See <a href="#webhooks">webhooks</a>
  for details.
</aside>

```javascript
import fs from 'fs'
import request from 'request'

const options = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/record/0/metadata',
  method: 'put',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },

  // by using "formData" here instead of "body", request will send this request
  //  as multipart/form-data, which is necessary for sending file data
  formData: {
    name: 'Super Duper New Title',
    description: 'This will replace the current description!',

    // replace the current mainImage
    newMainImage: fs.createReadStream('/tmp/uploads/super-duper-new-main-image.jpg'),

    // add these files to the files & images arrays
    newFiles: [
      fs.createReadStream('/tmp/uploads/2018-appraisal-documents.pdf'),
    ],
    newImages: [
      fs.createReadStream('/tmp/uploads/super-duper-new-photo-1.jpg'),
      fs.createReadStream('/tmp/uploads/super-duper-new-photo-2.jpg'),
    ],

    // before adding new files & images, replace the existing arrays with these
    // see "Removing Files and Images" for details
    files: [
      { id: '5bb640d492d258fdf2efa290', },
      { id: '5bb640d492d258fdf2efa291', },
    ],
    images: [
      { id: '5bb640d492d258fdf2efa292', },
      { id: '5bb640d492d258fdf2efa293', },
    ],
  },
}

request(options, (error, response) => {
  console.log(response.body.result) // a new Pending Update
})
```

> The above API call returns a new [Pending Update](#pending-update).

### HTTP Request

`PUT /v1/client/record/:tokenId/metadata`

### URL Parameters

Parameter    | Type   | Description
------------ | ------ | --------------------------------------------------------
tokenId      | Number | The `tokenId` of the Codex Record for which to modify the metadata of.

### Query Parameters

At least one of the following query parameters is required for this route:

Parameter    | Type                 | Description
------------ | -------------------- | ------------------------------------------
name         | String               | The new name of this Codex Record.
files        | Array[[File](#file)] | See [Removing Files and Images](#removing-files-and-images) for details.
images       | Array[[File](#file)] | See [Removing Files and Images](#removing-files-and-images) for details.
newFiles     | Array[File Data]     | An array of supplemental files to append to the existing `files` array for this Codex Record.
newImages    | Array[File Data]     | An array of supplemental images to append to the existing `images` array for this Codex Record.
description  | String               | The new description of this Codex Record. The field can be set to `null` to erase an existing description.
newMainImage | File Data            | The new main image of this Codex Record.

<!--
  the previous code example is hella long, and the spacing looks weird with the
  upcoming code example right next to it, so this is kind of a hacky way to push
  down this subheader so that it's code example isn't nestled up next to the
  previous one
  -->
<br><br><br><br><br><br><br><br>

### Removing Files and Images

> Removing the [File](#file) with id `5bb640d492d258fdf2efa294` from a Codex
Record's `images` array:

```javascript
import request from 'request'

// first, get the existing images
const getCodexRecordOptions = {
  url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/records/0/images',
  method: 'get',
  json: true,

  headers: {
    Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
  },
}

request(getCodexRecordOptions, (getCodexRecordError, getCodexRecordResponse) => {

  const codexRecordImages = getCodexRecordResponse.body.result

  // the remove the image with id '5bb640d492d258fdf2efa294'
  const newCodexRecordImages = codexRecordImages
    .filter((image) => {
      return image.id !== '5bb640d492d258fdf2efa294'
    })

    // this map is optional since the the only field you're required to send in
    //  is the File's id
    //
    // it's perfectly fine to send the entire File object, but it's a good idea
    //  to reduce the amount of unnecessary data sent with requests
    .map((image) => {
      return { id: image.id }
    })

  // now send in the "new state" of the images array
  const updateCodexRecordOptions = {
    url: 'https://rinkeby-codex-registry-api.codexprotocol.com/v1/client/record/0/metadata',
    method: 'put',
    json: true,

    headers: {
      Authorization: 'Bearer d49694e5a3459759cc7ac1741de246e184e51d6e',
    },

    body: {
      images: newCodexRecordImages,
    },
  }

  request(updateCodexRecordOptions, (updateCodexRecordError, updateCodexRecordResponse) => {
    console.log(updateCodexRecordResponse.body.result) // a new Pending Update
  })

})
```

The `files` and `images` parameters are each arrays of [File](#file) objects
(not to be confused with binary "File Data" in a `multipart/form-data` request...)
These parameters provide the mechanism for removing files and/or images.

To remove files and/or images from a Codex Record, follow these steps:

1. If you do not already have the Codex Record, you will need to retrieve the
   existing files and/or images first. You can get this data by [getting the
   whole Codex Record](#get-a-codex-record), [getting the Codex Record's
   files only](#get-a-codex-record-39-s-files-only), and/or [getting the
   Codex Record's images only](#get-a-codex-record-39-s-images-only).

1. Remove the images from the array(s).

1. Send the "new state" of the array(s) with this request as `files` and/or
   `images`.

<aside class="success">
  You're only <em>required</em> to send in the <code>id</code> of each
  <a href="#files">File</a>, since that's the only piece of data necessary to
  know which File to remove. However, it's perfectly fine to just send the
  entire File object.
</aside>
