---
title: Azure Government Cognitive Services quickStart | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: yujhongmicrosoft
manager: zakramer

ms.assetid: cba97199-851d-43ae-a75a-c601f3f81601
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 10/09/2017
ms.author: yujhong

---

# Cognitive Services – Computer Vision, Face, Translator Text APIs

To see an overview of Cognitive Services on Azure Government, [click here](documentation-government-services-aiandcognitiveservices.md).

## Part 1: Provision Cognitive Services Accounts
In order to access any of the Cognitive Services APIs, you must first provision a Cognitive Services account for each of the APIs you want to access. **Cognitive Services is not yet supported in the Azure Government Portal**, but you can use Azure PowerShell to access the APIs and services. 

> [!NOTE]
> You must go through the process of creating an account and retrieving a key(explained below) **for each** of the APIs you want to access.
> 
> 

### Prerequisites
* Install and Configure [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0)
* Connect [PowerShell with Azure Government](documentation-government-get-started-connect-with-ps.md)

1. Make sure that you have the **Cognitive Services resource provider registered on your account**. You can do this by **running the following Powershell command:**
   ```PowerShell
   Get-AzureRmResourceProvider
   ```
   If you do **not see `Microsoft.CognitiveServices`**, you have to register the resource provider by **running the following command**:
   ```PowerShell
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.CognitiveServices
   ```
2. In the Powershell command below, replace "rg-name", "name-of-your-api", and "location-of-resourcegroup" with your relevant account information. 

   Replace the "type of API" tag with any of the three following APIs you want to access:
       * ComputerVision
       * Face
       * TextTranslation

   ```PowerShell
   New-AzureRmCognitiveServicesAccount -ResourceGroupName 'rg-name' -name 'name-of-your-api' -Type <type of API> -SkuName S0 -Location 'location-of-resourcegroup'
   ```
   Example: 

   ```PowerShell
   New-AzureRmCognitiveServicesAccount -ResourceGroupName 'resourcegrouptest' -name 'myFaceAPI' -Type Face -SkuName S0 -Location 'usgovvirginia'
   ```

After you run the command, you should see something like this: 

![cog1](./media/documentation-government-cognitiveservices-img1.png)

3. Copy and save the "Endpoint" attribute somewhere as you will need it when making calls to the API. 

### Retrieve Account Key

You must retrieve an account key to access the specific API. 

In the Powershell command below, replace the "youraccountname" tag with the name that you gave the Account that you created above. Replace the 'rg-name' tag with the name of your resource group.

```PowerShell
Get-AzureRmCognitiveServicesAccountKey -Name <youraccountname> -ResourceGroupName 'rg-name'
```

Example:
```PowerShell
Get-AzureRmCognitiveServicesAccountKey -Name myFaceAPI -ResourceGroupName 'resourcegrouptest'
```
Copy and save the first key somewhere as you will need it to make calls to the API.

![cog2](./media/documentation-government-cognitiveservices-img2.png)

Now you are ready to make calls to the APIs. 

## Part 2: API Quickstarts 
The Quickstarts below will help you to get started with the APIs available through Cognitive Services in Azure Government.

## Computer Vision API
### Prerequisites

* Get the Microsoft Computer Vision API Windows SDK [here](https://github.com/Microsoft/Cognitive-vision-windows).

* Make sure Visual Studio has been installed:
    -   [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), including the **Azure development** workload.
    
    >[!NOTE] 
    > After you install or upgrade to Visual Studio 2017 version 15.3, you might also need to manually update the Visual Studio 		2017 tools for Azure Functions. You can update the tools from the **Tools** menu under **Extensions and Updates...** > 			**Updates** > **Visual Studio Marketplace** > **Azure Functions and Web Jobs Tools** > **Update**. 
    >
    >
    
### Variations
* The URI for accessing the Face API in Azure Government is :
   - `https://(resource-group-location).api.cognitive.microsoft.us/face/v1.0`
   - The main difference between this URI and the URI used in Commercial Azure is the ending of **.us** and the location at the beginning of the uri


### Analyze an Image With Computer Vision API using C# <a name="AnalyzeImage"> </a>

With the [Analyze Image method](https://westcentralus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fa), you can extract visual features based on image content. You can upload an image or specify an image URL and choose which features to return, including:
* A detailed list of tags related to the image content.
* A description of image content in a complete sentence.
* The coordinates, gender, and age of any faces contained in the image.
* The ImageType (clip art or a line drawing).
* The dominant color, the accent color, or whether an image is black & white.
* The category defined in this [taxonomy](../cognitive-services/computer-vision/category-taxonomy.md).
* Does the image contain adult or sexually suggestive content?

### Analyze an image C# example request

1. Create a new Console solution in Visual Studio.
2. Replace Program.cs with the following code.
3. Change the `uriBase` to the "Endpoint" attribute that you saved from Part 1, and keep the "/analyze" after the endpoint.
4. Replace the `subscriptionKey` value with your valid subscription key.
5. Run the program.

```csharp
using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

namespace VisionApp1
{
        static class Program
        {
            // **********************************************
            // *** Update or verify the following values. ***
            // **********************************************

            // Replace the subscriptionKey string value with your valid subscription key.
            const string subscriptionKey = "<subscription key>";

            //Copy and paste the "Endpoint" attribute that you saved before into the uriBase string "/analyze" at the end. 
            //Example: https://virginia.api.cognitive.microsoft.us/vision/v1.0/analyze
  
            const string uriBase = "<endpoint>/analyze";
            
            static void Main()
            {
                // Get the path and filename to process from the user.
                Console.WriteLine("Analyze an image:");
                Console.Write("Enter the path to an image you wish to analyze: ");
                string imageFilePath = Console.ReadLine();

                // Execute the REST API call.
                MakeAnalysisRequest(imageFilePath);

                Console.WriteLine("\nPlease wait a moment for the results to appear. Then, press Enter to exit...\n");
                Console.ReadLine();
            }


            /// <summary>
            /// Gets the analysis of the specified image file by using the Computer Vision REST API.
            /// </summary>
            /// <param name="imageFilePath">The image file.</param>
            static async void MakeAnalysisRequest(string imageFilePath)
            {
                HttpClient client = new HttpClient();

                // Request headers.
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                // Request parameters. A third optional parameter is "details".
                string requestParameters = "visualFeatures=Categories,Description,Color&language=en";

                // Assemble the URI for the REST API Call.
                string uri = uriBase + "?" + requestParameters;

                HttpResponseMessage response;

                // Request body. Posts a locally stored JPEG image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (ByteArrayContent content = new ByteArrayContent(byteData))
                {
                    // This example uses content type "application/octet-stream".
                    // The other content types you can use are "application/json" and "multipart/form-data".
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                    // Execute the REST API call.
                    response = await client.PostAsync(uri, content);

                    // Get the JSON response.
                    string contentString = await response.Content.ReadAsStringAsync();

                    // Display the JSON response.
                    Console.WriteLine("\nResponse:\n");
                    Console.WriteLine(JsonPrettyPrint(contentString));
                }
            }


            /// <summary>
            /// Returns the contents of the specified file as a byte array.
            /// </summary>
            /// <param name="imageFilePath">The image file to read.</param>
            /// <returns>The byte array of the image data.</returns>
            static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }


            /// <summary>
            /// Formats the given JSON string by adding line breaks and indents.
            /// </summary>
            /// <param name="json">The raw JSON string to format.</param>
            /// <returns>The formatted JSON string.</returns>
            static string JsonPrettyPrint(string json)
            {
                if (string.IsNullOrEmpty(json))
                    return string.Empty;

                json = json.Replace(Environment.NewLine, "").Replace("\t", "");

                StringBuilder sb = new StringBuilder();
                bool quote = false;
                bool ignore = false;
                int offset = 0;
                int indentLength = 3;

                foreach (char ch in json)
                {
                    switch (ch)
                    {
                        case '"':
                            if (!ignore) quote = !quote;
                            break;
                        case '\'':
                            if (quote) ignore = !ignore;
                            break;
                    }

                    if (quote)
                        sb.Append(ch);
                    else
                    {
                        switch (ch)
                        {
                            case '{':
                            case '[':
                                sb.Append(ch);
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', ++offset * indentLength));
                                break;
                            case '}':
                            case ']':
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', --offset * indentLength));
                                sb.Append(ch);
                                break;
                            case ',':
                                sb.Append(ch);
                                sb.Append(Environment.NewLine);
                                sb.Append(new string(' ', offset * indentLength));
                                break;
                            case ':':
                                sb.Append(ch);
                                sb.Append(' ');
                                break;
                            default:
                                if (ch != ' ') sb.Append(ch);
                                break;
                        }
                    }
                }

                return sb.ToString().Trim();
            }
        }
    }
```
### Analyze an Image response

A successful response is returned in JSON. Following is an example of a successful response:

```json

{
   "categories": [
      {
         "name": "people_baby",
         "score": 0.52734375
      },
      {
         "name": "people_young",
         "score": 0.4375
      }
   ],
   "description": {
      "tags": [
         "person",
         "indoor",
         "clothing",
         "woman",
         "white",
         "table",
         "food",
         "girl",
         "smiling",
         "posing",
         "holding",
         "black",
         "sitting",
         "young",
         "plate",
         "hair",
         "wearing",
         "cake",
         "large",
         "shirt",
         "dress",
         "eating",
         "standing",
         "blue"
      ],
      "captions": [
         {
            "text": "a woman posing for a picture",
            "confidence": 0.460196158842535
         }
      ]
   },
   "requestId": "7c20cc50-f5eb-453b-abb5-98378917431c",
   "metadata": {
      "width": 721,
      "height": 960,
      "format": "Jpeg"
   },
   "color": {
      "dominantColorForeground": "Black",
      "dominantColorBackground": "White",
      "dominantColors": [
         "White"
      ],
      "accentColor": "7C4F57",
      "isBWImg": false
   }
}
```
For more information, please see [public documentation](../cognitive-services/computer-vision/index.md) and [public API documentation](https://westus.dev.cognitive.microsoft.com/docs/services/56f91f2d778daf23d8ec6739/operations/56f91f2e778daf14a499e1fa) for Computer Vision API.

## Face API
### Prerequisites
* Get the Microsoft Face API Windows SDK [here](https://www.nuget.org/packages/Microsoft.ProjectOxford.Face/)

* Make sure Visual Studio has been installed:
    -   [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), including the **Azure development** workload.
    
    >[!NOTE] 
    > After you install or upgrade to Visual Studio 2017 version 15.3, you might also need to manually update the Visual Studio 		2017 tools for Azure Functions. You can update the tools from the **Tools** menu under **Extensions and Updates...** > 			**Updates** > **Visual Studio Marketplace** > **Azure Functions and Web Jobs Tools** > **Update**. 
    >
    >
    
### Variations 
* The URI for accessing the Face API in Azure Government is :
   - `https://(resource-group-location).api.cognitive.microsoft.us/face/v1.0`
   - The main difference between this URI and the URI used in Commercial Azure is the ending of **.us** and the location at the beginning of the uri

### Detect Faces in images with Face API using C# <a name="Detect"> </a>
Use the [Face - Detect method](https://westcentralus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 
to detect faces in an image and return face attributes including:
* Face ID: Unique ID used in several Face API scenarios. 
* Face Rectangle: The left, top, width, and height indicating the location of the face in the image.
* Landmarks: An array of 27-point face landmarks pointing to the important positions of face components.
* Facial attributes including age, gender, smile intensity, head pose, and facial hair. 

### Face detect C# example request

The sample is written in C# using the Face API client library. 

1. Create a new Console solution in Visual Studio.
2. Replace Program.cs with the following code.
3. Replace the `subscriptionKey` value with the key value that you retrieved above.
4. Change the `uriBase` value to the "Endpoint" attribute you retrieved above.
5. Run the program.
6. Enter the path to an image on your hard drive.

```csharp

using System;
using System.IO;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;

namespace FaceApp1
{

    static class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************
         
        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "<subscription key>";

        //Copy and paste the "Endpoint" attribute that you saved before into the uriBase string "/detect" at the end. 
        //Example: https://virginia.api.cognitive.microsoft.us/face/v1.0/detect
        const string uriBase ="<endpoint>/detect";

        static void Main()
        {
            // Get the path and filename to process from the user.
            Console.WriteLine("Detect faces:");
            Console.Write("Enter the path to an image with faces that you wish to analzye: ");
            string imageFilePath = Console.ReadLine();

            // Execute the REST API call.
            MakeAnalysisRequest(imageFilePath);

            Console.WriteLine("\nPlease wait a moment for the results to appear. Then, press Enter to exit...\n");
            Console.ReadLine();
        }


        /// <summary>
        /// Gets the analysis of the specified image file by using the Computer Vision REST API.
        /// </summary>
        /// <param name="imageFilePath">The image file.</param>
        static async void MakeAnalysisRequest(string imageFilePath)
        {
            HttpClient client = new HttpClient();

            // Request headers.
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request parameters. A third optional parameter is "details".
            string requestParameters = "returnfaceId=true&returnfaceLandmarks=false&returnfaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion";

            // Assemble the URI for the REST API Call.
            string uri = uriBase + "?" + requestParameters;

            HttpResponseMessage response;

            // Request body. Posts a locally stored JPEG image.
            byte[] byteData = GetImageAsByteArray(imageFilePath);

            using (ByteArrayContent content = new ByteArrayContent(byteData))
            {
                // This example uses content type "application/octet-stream".
                // The other content types you can use are "application/json" and "multipart/form-data".
                content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");

                // Execute the REST API call.
                response = await client.PostAsync(uri, content);

                // Get the JSON response.
                string contentString = await response.Content.ReadAsStringAsync();

                // Display the JSON response.
                Console.WriteLine("\nResponse:\n");
                Console.WriteLine(JsonPrettyPrint(contentString));
            }
        }


        /// <summary>
        /// Returns the contents of the specified file as a byte array.
        /// </summary>
        /// <param name="imageFilePath">The image file to read.</param>
        /// <returns>The byte array of the image data.</returns>
        static byte[] GetImageAsByteArray(string imageFilePath)
        {
            FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
            BinaryReader binaryReader = new BinaryReader(fileStream);
            return binaryReader.ReadBytes((int)fileStream.Length);
        }


        /// <summary>
        /// Formats the given JSON string by adding line breaks and indents.
        /// </summary>
        /// <param name="json">The raw JSON string to format.</param>
        /// <returns>The formatted JSON string.</returns>
        static string JsonPrettyPrint(string json)
        {
            if (string.IsNullOrEmpty(json))
                return string.Empty;

            json = json.Replace(Environment.NewLine, "").Replace("\t", "");

            StringBuilder sb = new StringBuilder();
            bool quote = false;
            bool ignore = false;
            int offset = 0;
            int indentLength = 3;

            foreach (char ch in json)
            {
                switch (ch)
                {
                    case '"':
                        if (!ignore) quote = !quote;
                        break;
                    case '\'':
                        if (quote) ignore = !ignore;
                        break;
                }

                if (quote)
                    sb.Append(ch);
                else
                {
                    switch (ch)
                    {
                        case '{':
                        case '[':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', ++offset * indentLength));
                            break;
                        case '}':
                        case ']':
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', --offset * indentLength));
                            sb.Append(ch);
                            break;
                        case ',':
                            sb.Append(ch);
                            sb.Append(Environment.NewLine);
                            sb.Append(new string(' ', offset * indentLength));
                            break;
                        case ':':
                            sb.Append(ch);
                            sb.Append(' ');
                            break;
                        default:
                            if (ch != ' ') sb.Append(ch);
                            break;
                    }
                }
            }

            return sb.ToString().Trim();
        }
    }
}
```
### Face detect response

A successful response is returned in JSON. Following is an example of a successful response: 

```json
Response:
[
   {
      "faceId": "0ed7f4db-1207-40d4-be2e-84694e42d682",
      "faceRectangle": {
         "top": 60,
         "left": 83,
         "width": 361,
         "height": 361
      },
      "faceAttributes": {
         "smile": 0.284,
         "headPose": {
            "pitch": 0.0,
            "roll": -12.2,
            "yaw": -16.7
         },
         "gender": "female",
         "age": 16.5,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.003,
            "contempt": 0.001,
            "disgust": 0.001,
            "fear": 0.002,
            "happiness": 0.284,
            "neutral": 0.694,
            "sadness": 0.012,
            "surprise": 0.004
         }
      }
   }
]
```
For more information, please see [public documentation](../cognitive-services/Face/index.md), and [public API documentation](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) for Face API.

## Text Translation API 
### Prerequisites

* Make sure Visual Studio has been installed:
    -   [Visual Studio 2017 version 15.3](https://www.visualstudio.com/vs/preview/), including the **Azure development** workload.
    
    >[!NOTE] 
    > After you install or upgrade to Visual Studio 2017 version 15.3, you might also need to manually update the Visual Studio 		2017 tools for Azure Functions. You can update the tools from the **Tools** menu under **Extensions and Updates...** > 			**Updates** > **Visual Studio Marketplace** > **Azure Functions and Web Jobs Tools** > **Update**. 
    >
    >
    
### Variations
* The URI for accessing the Text Translation API in Azure Government is: 
   - `https://api.microsofttranslator.us/v2/http.svc`
   - The only difference from the Commercial URI is the endpoint of **.us**
* The URI for retrieving the access token to the Text Translation API is the `Endpoint` attribute that was saved in Part 1 of this quickstart, with `/IssueToken` at the end

### Text Translation Method
This sample will use the [Text Translation - Translate method](http://docs.microsofttranslator.com/text-translate.html#!/default/get_Translate) to translate a string of text from a language into another specified language. There are multiple [language codes](https://msdn.microsoft.com/library/hh456380.aspx) that can be used with the Text Translation API. 

### Text Translation C# example request

The sample is written in C#. 

1. Create a new Console solution in Visual Studio.
2. Replace Program.cs with the corresponding code below.
3. Replace the `subscriptionKey` value with the key value that you retrieved above.
4. Create a Token.cs class and copy and paste the corresponding code below. 
5. Run the program.

You can also test out different languages and texts by replacing the "text", "from", and "to" variables in Program.cs. 

```csharp
using System;
using System.IO;
using System.Net;
using System.Runtime.Serialization;
using System.Threading.Tasks;

namespace TextTranslationApp1
{
        static class Program
        {
            // **********************************************
            // *** Update or verify the following value. ***
            // **********************************************

            // Replace the subscriptionKey string value with your valid subscription key.
            const string subscriptionKey = "<subscription key>";

            // This is the base uri for accessing the Text Translation API. We will be testing the /translate method.
            const string uriBase = "https://api.microsofttranslator.us/v2/http.svc/translate?";

            static void Main()
            {
                MainAsync().Wait();
            }

            private static async Task MainAsync()
            {
                // Replace the "text" variable with whatever text you want to translate
                // Replace the "from" variable with the language that the "text" string is written in
                // Replace the "to" variable with the language that you want the text to be translated into

                string text = "elephants are cute";
                string from = "en";
                string to = "es";

                //Formulating the uri to make a GET call to the API 
                string uri = uriBase + "text=" + text + "&from=" + from + "&to=" + to;

                //Declare an instance of the Token class
                Token token = new Token();
                //Retrieve the token
                string accessToken = await token.GetToken(subscriptionKey);
                // Make the GET call
                HttpWebRequest httpWebRequest = (HttpWebRequest)WebRequest.Create(uri);
                httpWebRequest.Headers.Add("Authorization", accessToken);
                //Getting the JSON response
                using (WebResponse response = httpWebRequest.GetResponse())
                using (Stream stream = response.GetResponseStream())
                {
                    DataContractSerializer dcs = new DataContractSerializer(Type.GetType("System.String"));
                    string translation = (string)dcs.ReadObject(stream);
                    System.Console.WriteLine("Translation for source text '{0}' from {1} to {2} is", text, from, to);
                    Console.WriteLine(translation);
                    Console.ReadLine();
                }

            }

        }
    }
```
We are now going to create a Token.cs class that will contain our method for retrieving the access token to the Text Translation API.
Right click on your project from Solution Explorer and add "New class". Copy and paste the code below into the Token.cs class.

```csharp
using System;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;

namespace TextTranslationApp1
{
    public class Token
    {
        /// URL of the token service
        /// Replace this url with your endpoint (the location should be the location of your resource group) and add the "IssueToken" at the end
        private static readonly Uri ServiceUrl = new Uri("https://virginia.api.cognitive.microsoft.us/sts/v1.0/IssueToken");

        /// Name of header used to pass the subscription key to the token service
        private const string OcpApimSubscriptionKeyHeader = "Ocp-Apim-Subscription-Key";

        /// After obtaining a valid token, this class will cache it for this duration.
        /// Use a duration of 5 minutes, which is less than the actual token lifetime of 10 minutes.
        private static readonly TimeSpan TokenCacheDuration = new TimeSpan(0, 5, 0);

        /// Cache the value of the last valid token obtained from the token service.
        public string _storedTokenValue = string.Empty;

        /// When the last valid token was obtained.
        private DateTime _storedTokenTime = DateTime.MinValue;


        /// Gets the HTTP status code for the most recent request to the token service.
        public HttpStatusCode RequestStatusCode
        {
            get; private set;
        }

        // This method retrieves and caches an access token and generates a new one after 10 minutes, when the token expires. 
        public async Task<string> GetToken(string SubscriptionKey)
        {
            if (string.IsNullOrWhiteSpace(SubscriptionKey))
            {
                return string.Empty;
            }

            // Re-use the cached token if there is one.
            if ((DateTime.Now - _storedTokenTime) < TokenCacheDuration)
            {
                return _storedTokenValue;
            }

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = ServiceUrl;
                request.Content = new StringContent(string.Empty);
                request.Headers.TryAddWithoutValidation(OcpApimSubscriptionKeyHeader, SubscriptionKey);
                client.Timeout = TimeSpan.FromMinutes(10);
                var response = await client.SendAsync(request);
                this.RequestStatusCode = response.StatusCode;
                response.EnsureSuccessStatusCode();
                var token = await response.Content.ReadAsStringAsync();
                _storedTokenTime = DateTime.Now;
                _storedTokenValue = "Bearer " + token;
                return _storedTokenValue;
            }
        }       
    }
}

```
### Text Translation response

An example of a successful response output from the console is shown below. 

![success](./media/documentation-government-cognitiveservices-img6.png)

For more information, please see [public documentation](../cognitive-services/translator/translator-info-overview.md) and [public API documentation](http://docs.microsofttranslator.com/text-translate.html) for Translator Text API.

### Next Steps
* Subscribe to the [Azure Government blog](https://blogs.msdn.microsoft.com/azuregov/)
* Get help on Stack Overflow by using the "[azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)" tag
* Give us feedback or request new features via the [Azure Government feedback forum](https://feedback.azure.com/forums/558487-azure-government)
