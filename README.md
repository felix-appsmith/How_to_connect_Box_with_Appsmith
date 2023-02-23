# Appsmith with box

This is a guide on how to make a successful connection from Appsmith to box.com using a developer token or using authentication via Oauth2.0. It also details all the problems encountered while making this connection and failing points.

## Table of Contents

- [Appsmith with box](#appsmith-with-box)
  - [How to connect box](#how-to-connect-box)
    - [Application configuration from box](#application-configuration-from-box)
      - [Create app](#create-app)
      - [Additional configurations in my app](#additional-configurations-in-my-app)
    - [Connect with Developer Token](#connect-with-developer-token)
    - [Oauth2 Configuration](#oauth2-configuration)
    - [Endpoint configuration](#endpoint-configuration)
      - [Get Files](#get-files)
      - [Download Files](#download-files)
      - [Delete Files](#delete-files)
      - [Upload Files](#upload-files)

## How to connect box

### Application configuration from box

#### Create app

1. Navigate to the developer dashboard in Box.
2. Click on My Apps
3. Press Create New App
![image](https://user-images.githubusercontent.com/114161539/220941067-0efd514a-404c-4d9b-b469-075671c37f9e.png)

4. Select Custom App
![image](https://user-images.githubusercontent.com/114161539/220941345-9ae7aa93-363a-405f-a87e-3fa122c88164.png)

5. Select the option called `User Authentication (OAuth 2.0)`
![image](https://user-images.githubusercontent.com/114161539/220942759-961be1d9-c97b-4bb8-b1ea-05fd87af1550.png)

6. Enter the name of your app
![image](https://user-images.githubusercontent.com/114161539/220943003-12b69aaa-7212-48b3-879f-4f77c301bd8e.png)

7. Press create
![image](https://user-images.githubusercontent.com/114161539/220943130-a0063b7e-f092-472e-8954-7209c6a1cff7.png)

#### Aditionals configurations in me app
1. In OAuth 2.0 Redirect URI add new uri and put appsmith uri
   - `https://app.appsmith.com/api/v1/datasources/authorize`
![image](https://user-images.githubusercontent.com/114161539/220943549-ddf58129-ae9e-47cc-865b-a67411204dfe.png)
![image](https://user-images.githubusercontent.com/114161539/220944555-d6a4b08e-bd1e-429a-a1ba-809067049d75.png)

2. In `Applications Scopes` select the option called `Write all files and folders stored in box`
![image](https://user-images.githubusercontent.com/114161539/220944688-99db0fef-0928-4a6d-bdd0-7282d6accfb2.png)

3. Press the Save changes button
![image](https://user-images.githubusercontent.com/114161539/220944801-a691d629-7ac4-4e08-a755-ca990a49e6d0.png)


   - Hapy hacking!

### Connect with Developer Token

`Import this CURL` in appsmith for automatic configuration

```bash
curl https://api.box.com/2.0/folders/0 -H "Authorization: Bearer <<Token>>" -H "Content-Type: application/json"
```

### Oauth2 Configuration

![image](https://user-images.githubusercontent.com/114161539/220946100-a72ecb64-881f-4895-8e3f-395b1c50460b.png)



1. Go to datasources in appsmith
2. Select AHutenticate API
3. Set the configuration parameters as shown below
```bash
URL: `https://api.box.com/2.0/`
Headers: `Content-Type`: `application/json`
Authentication Type: `OAuth 2.0`
Grant Type: `Authorization Code`
Add Access Token To: `Request Header`
Access Token URL: `https://api.box.com/oauth2/token`
Client ID: `<<your_client_id>>`
Client Secret: `<<your_client_secret>>`
Authorization URL: `https://account.box.com/api/oauth2/authorize`
```
Add you Redirect URL in the`OAuth 2.0 Redirect URI` in your `box.com` console

4. Press the authorize button
   `note: sometimes you will get a message that says something went wrong, just authorize the data source again`
![image](https://user-images.githubusercontent.com/114161539/220940326-305f0244-3971-42ce-85b8-2655cc6003dd.png)
   

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

URL: `https://api.box.com/2.0/files/{{Table1.selectedRow.id}}`

`Happy Hacking`


### Upload Files

Http method: `POST`
```bash
URL: `https://upload.box.com/api/2.0/files/content`
Headers: `content-type: multipart/form-data`
Body: In tab `MULTIPART_FORM_DATA` - `attributes:{"name":"{{File_data.files[0].name}}", "parent":{"id":"0"}}` - `file: {{File_data.files[0]}}`
```

