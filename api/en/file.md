# `/files` - Files API Endpoint

### Summary

Every account on 10Centuries comes with storage that can be used for a number of types of file. At the moment file types are limited, though the list of supported types continues to grow as the platform evolves. Files are never deleted from a person's account, even if the account type drops to a level with reduced storage.

> **Note:** All API requests to the Files API must have a valid authorization token in the HTTP header.

### Sections

1. Supported MIME Types
2. Uploading a File
3. Getting File Details
4. Deleting a File

### Supported MIME Types

At the moment, the following MIME Types are supported by the API. More are added as the platform evolves, and you can request specific ones at any time.

* `audio/mp3`
* `audio/mp4`
* `audio/m4a`
* `audio/x-mp3`
* `audio/x-mp4`
* `audio/mpeg`
* `audio/x-m4a`
* `image/gif`
* `image/x-gif`
* `image/jpg`
* `image/jpeg`
* `image/png`
* `image/tiff`
* `image/bmp`
* `image/x-windows-bmp`
* `video/quicktime`
* `video/m4a`
* `video/m4v`
* `video/mp4`
* `application/x-mpegurl`
* `video/mp2t`

When some images are uploaded, they will be compressed where possible. Images that are wider than 960 pixels will also be resized and compressed to reduce the amount of data being sent to people on mobile data plans. The full-sized image is stored alongside the resized image with a `_original` suffix added to the hashed filename. Another nice feature is that the API will attempt to reorient images based on the information stored in the metadata to ensure correct orientation when returned for others to view.

Please note that while limited support for video files exist, the API has not yet been fully developed to handle the different types of video or convert the files to a web-friendly format. This is planned in a future update.

### Uploading a File

Uploading a file to 10Centuries is a one-step process, intentionally ignoring the more common process of starting with a `PUT` and uploading with a `POST`. The API will support files up to 256MB in size so long as the MIME type is in the list of supported types above.

From your application, send a request to the following endpoint:

```
POST https://api.10centuries.org/files/upload
```

Files are sent via a standard `multipart/form-data` object, and multiple files can be sent in the same package. File information such as the type and original name are extracted from the file package. Files with invalid metadata may return an error.

Upon successful completion, an object containing an array of files, bucket information, and errors will be returned in the following format:

```
{
    "files": [...],
    "bucket": [...],
    "errors": [...]
}
```

The structure of these objects can be found below.

#### File Object

The File object contains the basic elements of data referring to a file stored on the CDN. File objects do not contain the bytes that constitute a file on the CDN. Instead, those need to be requested individually. The File object will look something like this:

```
{
    "file": [ "id": 123,
               "name": "Nozomi on the Grass.jpg",
               "size": 12345678,
               "mime": "image/jpeg",
               "is_deleted": false],
    "in_post": true,
    "in_meta": true,
    "is_avatar": false,
    "has_metadata": true,
    "cdn_url": "https://cdn.10centuries.org/...",
    "uploaded_at": "2017-01-01T00:00:00Z"
    "uploaded_unix": 1483196400,
    "updated_at": "2017-01-01T00:00:00Z",
    "updated_unix": 1483196400
}
```

If a File has been deleted, then most of the data returned in the object will read `false`. Only the `file.id`, `file.is_deleted`, `updated_at`, and `updated_unix` values will contain information.

#### Bucket Object

The Bucket object contains four pieces of data and is returned with every upload object, successful for otherwise. The object will look something like this:

```
{
    "type": "Premium",
    "files": 1701,
    "limit": 5368709120,
    "used": 1234567890
}
```

#### Error Objects

Should an error occur, an array of objects will be returned detailing what the problem was. The object will look something like this:

```
{
    "name": "filename.abc",
    "size": 74656,
    "type": "application/abc",
    "reason": "Unsupported File Type"
}
```

Different reasons for a file being rejected can include:

* Bad File Name
* Could Not Record File Data
* Insufficient Storage Remaining
* Unsupported File Type

Should there be insufficient storage remaining, the details for the account can be found in the `bucket` return object.

### Getting File Details

To retrieve a list of files and details for an account, or just details on a specific file, send a request to the following API endpoint:

```
GET https://api.10centuries.org/files/{file_id}
```

Note that if no `file_id` is provided, a list of the files owned by the logged in account will be returned.

**Example**

```
curl -X GET -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
"https://api.10centuries.org/files/123"
```

If the request was successful, the API will respond with a JSON package:

```
{
    "meta": {
        "code": 200
    },
    "data": {
              [{
                 "file": [ "id": 123,
                           "name": "Nozomi on the Grass.jpg",
                           "size": 12345678,
                           "mime": "image/jpeg",
                           "is_deleted": false],
                 "in_post": true,
                 "in_meta": true,
                 "is_avatar": false,
                 "has_metadata": true,
                 "cdn_url": "https://cdn.10centuries.org/...",
                 "uploaded_at": "2017-01-01T00:00:00Z"
                 "uploaded_unix": 1483196400,
                 "updated_at": "2017-01-01T00:00:00Z",
                 "updated_unix": 1483196400
              }]
    }
}
```

If there was some problem, the API will respond with an error:

```
{
    "meta": {
        "code": 404,
        "text": "No Files Found"
    },
    "data": false
}
```

### Deleting a File

Deleting a file is pretty much exactly the same as requesting individual file details, but with a `DELETE` request. Of course, only the owner of a file may delete it.

To delete a file, send a request to the following endpoint:

```
DELETE https://api.10centuries.org/files/{file_id}
```

Note that if no `file_id` is provided or the file is not owned by the logged in account, an error will be returned.

**Example**

```
curl -X DELETE -H "Authorization: {Authorization Token}" \
-H "Content-Type: application/x-www-form-urlencoded" \
"https://api.10centuries.org/files/123"
```

If the request was successful, the API will respond with a JSON package:

```
{
    "meta": {
        "code": 200
    },
    "data": { "file": [ "id": 123,
                        "name": false,
                        "size": false,
                        "mime": false,
                        "is_deleted": true],
              "in_post": false,
              "in_meta": false,
              "is_avatar": false,
              "has_metadata": false,
              "cdn_url": false,
              "uploaded_at": "2017-01-01T00:00:00Z"
              "uploaded_unix": 1483196400,
              "updated_at": "2017-01-01T00:00:00Z",
              "updated_unix": 1483196400
    }
}
```

If there was some problem, the API will respond with an error:

```
{
    "meta": {
        "code": 401,
        "text": "You Do Not Own This File"
    },
    "data": false
}
```