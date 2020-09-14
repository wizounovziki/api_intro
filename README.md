# ASPN API Introduction

## Route: /emotion
### Parameters

|  Variables  |Required(Y/N) | Description  |
|  ----       | ----|        ----  |
| image_type {file/base64} | Y| Specify the image format send from client. |
| image  |Y |image file in bytes or base64 string |

### Usage
**
Post method.
/emotion
**
### Examples
**
image_type = file:
form data = {
    image_type = "file",
    image = file
}

image_type = base64:
form data = {
    image_type = "base64",
    image = your_base64_string
}

**
### Sample Response
**
{
    "anger": false,
    "disgust": false,
    "fear": false,
    "happy": false,
    "neutral": true,
    "sad": false,
    "surprise": false
}
**
