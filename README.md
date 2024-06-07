# vercel_blob

A Python wrapper for the Vercel Blob Storage API

## Installation


```bash
pip3 install vercel_blob
```

## Getting Started

### Python Version

This package (vercel_blob) was written using Python 3 in mind, and Python 2 will not work properly.

### Set Environment Variable for Authorization

```bash
export BLOB_READ_WRITE_TOKEN="superssecretkey"
```

### Add vercel_blob to your code

```python
import vercel_blob
#or
import vercel_blob.blob_store
# or if you want to use an alias
import vercel_blob.blob_store as vb_store
```

## Using vercel_blob

Currently only supports the basic list, put, delete, copy and head(get file metadata) operations. Here's a quick overview:

### List all files in the Blob Storage

The list method will return a list of all files in the blob storage. You can pass 'limit' to limit the returned number of blobs.

```python
def list_all_blobs():
    blobs = vercel_blob.blob_store.list({
        'limit': '5',
    })
```

list() returns a JSON object in the following format:

```javascript
blobs: {
  size: `number`;
  uploadedAt: `Date`;
  pathname: `string`;
  url: `string`;
  downloadUrl: `string`
}[]
cursor?: `string`;
hasMore: `boolean`;
folders?: `string[]`
```

For a long list of blob objects (the default list limit is 1000), you can use the cursor and hasMore parameters to paginate through the results as shown in the example below:

```python
blobs = vercel_blob.blob_store.list({
        'limit': '4',
        'cursor': cursor,
    })
```

### Upload File / Blob to the Storage

The put method can be used to upload a blob to the blob store. If the blob is already present in the store, it will be overwritten.

```python
def upload_a_blob():
    with open('file.txt', 'rb') as f:
        resp = vercel_blob.blob_store.put('test.txt', f.read())
        print(resp)
```

The method takes in the filename as the first argument, and the bytes of the file as the second argument. The third parameters can be the options dictionary.

The response object would look something like this:
```javascript
pathname: `string`,
contentType: `string`,
contentDisposition: `string`,
url: `string`
downloadUrl: `string`
```

You would notice that the blob uploaded automatically has a random suffix attached to it. To remove that, just add the 'addRandomSuffix' parameter to the options dictionary. Here's an example:

```python
def upload_a_blob():
    with open('file.txt', 'rb') as f:
        resp = vercel_blob.blob_store.put('test.txt', f.read(), {
                "addRandomSuffix": "false",
            })
        print(resp)
```

### Delete a blob or a list of blobs from the Blob Storage

The delete method will delete a file from the Blob Storage. It takes in the URL of the blob, or a list of blobs. Here's an example:

```python
def delete_a_list_of_blobs():
    resp = vercel_blob.blob_store.delete([
            'blob_url_1',
            'blob_url_2'
        ])
    print(resp)
```

Printing the response will result in "None", since the delete method does not return anything. If a blob is present, it will be deleted. If a blob is not present, it will not result in any error.

### Get blob metadata

The head method will return the blob object's metadata.

```python
def get_blob_metadata():
    resp = vercel_blob.blob_store.head('blob_url')
    print(resp)
```

The JSON object returned will contain the following properties:
```javascript
  size: `number`;
  uploadedAt: `Date`;
  pathname: `string`;
  contentType: `string`;
  contentDisposition: `string`;
  url: `string`;
  downloadUrl: `string`
  cacheControl: `string`;
```

If the blob url provided is not valid, an Exception will be thrown.

### Copy blob from one folder to another

The copy method can be used to copy an existing blob to another location inside the same blob store. Note that the addRandomSuffix option is False by default for copy operations, hence it overwrites by default. To prevent this behavior, you can set the 'addRandomSuffix' option to "true".

```python
def copy_a_blob():
    resp = vercel_blob.blob_store.copy("https://blobstore.public.blob.vercel-storage.com/test.txt", "new-folder/test.txt")
    print(resp)
```

The JSON representation of the response should look something like this:
```javascript
  pathname: `string`,
  contentType: `string`,
  contentDisposition: `string`,
  url: `string`
  downloadUrl: `string`
```

