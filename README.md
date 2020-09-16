# ASPN API Introduction

## Route: /emotion
### Description
Input an image with in bytes or base64 format, return the inference of the emotion on the face, now supports 6 emotions:
anger, disgust, fear, happy, neutral, sad, surprise.
### Parameters

|  Variables  |Required(Y/N) | Description  |
|  ----       | ----|        ----  |
| image_type {file/base64} | Y| Specify the image format send from client. |
| image  |Y |image file(jpg/png) in bytes or base64 string |

### Usage
```
Method: POST
Route:  /emotion
Content-disposition: form-data
```
### Examples
#### image_type = file:
form data = {
    image_type = "file",
    image = file
}
```
//C# sample codes
var client = new RestClient("http://intellik.southeastasia.cloudapp.azure.com:443/emotion");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddHeader("Cookie", "XSRF-TOKEN=6d02c51c-4d37-4f43-84fe-18188a690907");
request.AddParameter("image_type", "file");
request.AddFile("image", "Your/file/path");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

```
```
//node.js(axios) sample codes
var axios = require('axios');
var FormData = require('form-data');
var fs = require('fs');
var data = new FormData();
data.append('image_type', 'file');
data.append('image', fs.createReadStream('Your/file/path'));

var config = {
  method: 'post',
  url: 'http://intellik.southeastasia.cloudapp.azure.com:443/emotion',
  headers: { 
    'Cookie': 'XSRF-TOKEN=6d02c51c-4d37-4f43-84fe-18188a690907', 
    ...data.getHeaders()
  },
  data : data
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

#### image_type = base64:
form data = {
    image_type = "base64",
    image = your_base64_string
}
```
// C# sample codes
var client = new RestClient("http://intellik.southeastasia.cloudapp.azure.com:443/emotion");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddHeader("Cookie", "XSRF-TOKEN=6d02c51c-4d37-4f43-84fe-18188a690907");
request.AlwaysMultipartFormData = true;
request.AddParameter("image_type", "base64");
request.AddParameter("image", "YourBase64String");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

```
```
// node.js(axios) sample codes
var axios = require('axios');
var FormData = require('form-data');
var data = new FormData();
data.append('image_type', 'base64');
data.append('image', 'yourBase64String');
var config = {
  method: 'post',
  url: 'http://intellik.southeastasia.cloudapp.azure.com:443/emotion',
  headers: { 
    'Cookie': 'XSRF-TOKEN=6d02c51c-4d37-4f43-84fe-18188a690907', 
    ...data.getHeaders()
  },
  data : data
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```
### Sample Response
```
Status code:200(face detected)
{
    "anger": false,
    "disgust": false,
    "fear": false,
    "happy": false,
    "neutral": true,
    "sad": false,
    "surprise": false
}
Status code:200(face not detected)
{
    "error": "face not detected."
}
```
