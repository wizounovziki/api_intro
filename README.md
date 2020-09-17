# IntelliK AI API Documentation
Welcome to the new IntelliK AI API documentation page. Our team is here to support you in the integration of your own AI in IntelliK.

This documentation provides the guidelines for integration with external AI API in IntelliK. The external AI should be hosted in public domain and should be accessible via RESTFUL API. Currently, the IntelliK is supporting HTTP API call and will be migrated to HTTPS soon.

Watch this space as we continue to improve our API integration services!

## Image Analytic
This feature allow you to access the images acquired in IntelliK app and return actionable items to IntelliK app to take actions. Please refer to IntelliK user guide for detail configuration on IntelliK editor. The following provides the guidelines on inputs and outputs of your AI model to interface with IntelliK.

- Inputs for AI model

|  Variables  |Required(Y/N) | Description  |
|  ----       | ----|        ----  |
| image_type {file/base64} | Y| Specify the image format send from client. |
| image  |Y |image file(jpg/png) in bytes or base64 string |

- Outputs from AI model

IntelliK currently accepts the classifcation outputs and all the classes should be listed as follow:
```
{
    "ClassA": false,
    "ClassB": false,
    "ClassC": false,
    "ClassD": true
}
```
Note: Only one of the classes must be true.

### Emotion AI model
#### Route: /emotion
#### Description
Input an image with in bytes or base64 format, return the inference of the emotion on the face, now supports 6 emotions:
anger, disgust, fear, happy, neutral, sad, surprise.
#### Parameters

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
//C# codes for testing your AI model
var client = new RestClient("http://yourAImodel/emotion");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AddParameter("image_type", "file");
request.AddFile("image", "Your/file/path");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

```

#### image_type = base64:
form data = {
    image_type = "base64",
    image = your_base64_string
}
```
//C# codes for testing your AI model
var client = new RestClient("http://yourAImodel/emotion");
client.Timeout = -1;
var request = new RestRequest(Method.POST);
request.AlwaysMultipartFormData = true;
request.AddParameter("image_type", "base64");
request.AddParameter("image", "YourBase64String");
IRestResponse response = client.Execute(request);
Console.WriteLine(response.Content);

```

#### Sample Response
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
