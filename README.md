# Appsmith with box

This is a guide on how to make a successful connection from Appsmith to abox.com using a developer token or using authentication via Oauth2.0. It also details all the problems encountered while making this connection and failing points.

## Table of Contents

- [Appsmith with box](#appsmith-with-box)
  - [How to connect box](#how-to-connect-box)
    - [Application configuration from box](#application-configuration-from-box)
      - [Create app](#create-app)
      - [Additional configurations in my app](#additional-configurations-in-my-app)
    - [Connect with Developer Token](#connect-with-developer-token)
    - [Oauth2 Configuration](#oauth2-configuration)

## How to connect box

### Application configuration from box

#### Create app

1. Travel to the developer dashboard in box
2. Click on My Apps
3. Press Create New App
4. Select Custom App
5. Select the option called `User Authentication (OAuth 2.0)`
6. Enter the name of your app
7. Press create

#### Aditionals configurations in me app

1. In `Applications Scopes` select the option called `Write all files and folders stored in box`
2. In OAuth 2.0 Redirect URI add new uri and put appsmith uri
   - `https://app.appsmith.com/api/v1/datasources/authorize`
   - Hapy hacking!

### Connect with Developer Token

`Import this CURL` in appsmith for automatic configuration

```bash
curl https://api.box.com/2.0/folders/0 -H "Authorization: Bearer <<Token>>" -H "Content-Type: application/json"
```

### Oauth2 Configuration

1. Go to datasources in appsmith
2. Select AHutenticate API
3. Set the configuration parameters as shown below

URL: `https://api.box.com/2.0/`
Headers: `Content-Type`: `application/json`
Authentication Type: `OAuth 2.0`
Grant Type: `Authorization Code`
Add Access Token To: `Request Header`
Access Token URL: `https://api.box.com/oauth2/token`
Client ID: `<<your_client_id>>`
Client Secret: `<<your_client_secret>>`
Authorization URL: `https://account.box.com/api/oauth2/authorize`
Add you Redirect URL in the`OAuth 2.0 Redirect URI` in your `box.com` console

4. Press the authorize button
   `note: sometimes you will get a message that says something went wrong, just authorize the data source again`
   Happy hacking!

## Endpoint configuration

### Get Files

Htpp method: `GET`

URL: `https://api.box.com/2.0/folders/0`
Headers: `Content-Type: application/json`

1. You can link it to a table to show the files by writing this in the table data:
   `{{Get_Files.data.item_collection.entries}}`

`Happy Hacking`

### Download Files

1. Use a widget input
2. Set the default value of the input to this `{{Table1.selectedRow.id}}`

Http method: `GET`

URL: `https://api.box.com/2.0/files/{{Table1.selectedRow.id}}`
Headers: `content-type: application/json`
`Happy Hacking`

### Delete Files

1. Use a widget input
2. Set the default value of the input to this `{{Table1.selectedRow.id}}`
   `note:You can use the same input configured above`

Http method: `DELETE`

URL: `https://api.box.com/2.0/{{Table1.selectedRow.id}}`

#### Error notes

This method currently doesn't work in appsmith but with the same ID it does work in CURL from the terminal though.

Appsmith Error:

```bash
  "type": "error",
  "status": 404,
  "code": "not_found",
  "help_url": "http://developers.box.com/docs/#errors",
  "message": "Not Found",
  "request_id": "j26qivhb4ksv7jgs"
```

### Upload Files

Http method: `POST`

URL: `https://upload.box.com/api/2.0/files/content`
Headers: `content-type: multipart/form-data`
Body: In tab `MULTIPART_FORM_DATA` - `attributes: {name: {{File_data.files[0].name}}, parent: {id: '0'}}` - `file: {{File_data.files[0]}}`

#### Errors notes

This is the curl to upload files and it works fine in terminal:

```curl
curl -i -X POST "https://upload.box.com/api/2.0/files/content" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  -H "Content-Type: multipart/form-data" \
  -F attributes='{"name":"Contract.pdf", "parent":{"id":"11446498"}}' \
  -F file=@<FILE_NAME>
```

We get this error despite being passing the file correctly, I tried to load it in base64, binary and blob and none of it worked.

When trying to upload the file in blob format we get the following error:

```bash
{
  "code": "bad_request",
  "help_url": "http://developers.box.com/docs/#errors",
  "status": 400,
  "message": "non-file POST form-data field exceeds set limit of 1000000 bytes",
  "type": "error"
}
```

When you try to load the file in base64 and binaryion formats we get this error:

```bash
{
  "code": "bad_request",
  "help_url": "http://developers.box.com/docs/#errors",
  "status": 400,
  "message": "API upload did not contain a file part",
  "type": "error"
}
```
