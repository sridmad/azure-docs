---
title: Media Services release notes | Microsoft Docs
description: Media Services Release Notes
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''

ms.assetid: 3ca2d7af-1cf0-45fa-9585-3b73f3ee057d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 10/18/2017
ms.author: juliako

---
# Azure Media Services release notes
These release notes summarize changes from previous releases and known issues.

> [!NOTE]
> We want to hear from our customers and focus on fixing problems that affect you. To report a problem or ask questions, please post in the [Azure Media Services MSDN Forum].
> 
> 

## <a id="issues"></a>Currently Known Issues
### <a id="general_issues"></a>Media Services General Issues
| Issue | Description |
| --- | --- |
| Several common HTTP headers are not provided in the REST API. |If you develop Media Services applications using the REST API, you find that some common HTTP header fields (including CLIENT-REQUEST-ID, REQUEST-ID, and RETURN-CLIENT-REQUEST-ID) are not supported. The headers will be added in a future update. |
| Percent-encoding is not allowed. |Media Services uses the value of the IAssetFile.Name property when building URLs for the streaming content (for example, http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters.) For this reason, percent-encoding is not allowed. The value of the **Name** property cannot have any of the following [percent-encoding-reserved characters](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters): !*'();:@&=+$,/?%#[]". Also, there can only be one ‘.’ for the file name extension. |
| The ListBlobs method that is part of the Azure Storage SDK version 3.x fails. |Media Services generates SAS URLs based on the [2012-02-12](https://docs.microsoft.com/rest/api/storageservices/Version-2012-02-12) version. If you want to use Azure Storage SDK to list blobs in a blob container, use the [CloudBlobContainer.ListBlobs](http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx) method that is part of Azure Storage SDK version 2.x. The ListBlobs method that is part of the Azure Storage SDK version 3.x will fail. |
| Media Services throttling mechanism restricts the resource usage for applications that make excessive request to the service. The service may return the Service Unavailable (503) HTTP status code. |For more information, see the description of the 503 HTTP status code in the [Azure Media Services Error Codes](media-services-encoding-error-codes.md) article. |
| When querying entities, there is a limit of 1000 entities returned at one time because public REST v2 limits query results to 1000 results. |You need to use **Skip** and **Take** (.NET)/ **top** (REST) as described in [this .NET example](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) and [this REST API example](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Some clients can come across a repeat tag issue in the Smooth Streaming manifest. |For more information, see [this](media-services-deliver-content-overview.md#known-issues) section. |
| Azure Media Services .NET SDK objects cannot be serialized and as a result do not work with Azure Caching. |If you try to serialize the SDK AssetCollection object to add it to Azure Caching, an exception is thrown. |


## <a id="rest_version_history"></a>REST API Version History
For information about the Media Services REST API version history, see [Azure Media Services REST API Reference].

## October 2017 Release
> [!IMPORTANT] 
> Reminder: Azure Media Services is going to be deprecating support for ACS authentication keys.  On June 1, 2018, you will no longer be able to authenticate with the AMS backend via code using ACS keys. You must update your code to use Azure Active Directory (AAD) per the article [Azure Active Directory (Azure AD)-based authentication](media-services-use-aad-auth-to-access-ams-api.md). You will also receive warnings in the Azure portal of this change.

### Updates for October 2017 include:
#### SDKs
* .NET SDK Updated to support AAD authentication.  We have removed support for ACS authentication from the latest .NET SDK on Nuget.org to encourage faster migration to AAD. 
* JAVA SDK Updated to support AAD authentication.  We have added support for AAD authentication to our Java SDK. You can read details on how to use the Java SDK with AMS in the article [Get started with the Java client SDK for Azure Media Services](media-services-java-how-to-use.md)

#### File Based Encoding
1.	You can now use the Premium Encoder to encode your content to H.265(HEVC) video codec. There is no pricing impact for choosing H.265 over other codecs like H.264. Please refer to the [Online Services Terms](https://azure.microsoft.com/support/legal/) for an important note regarding HEVC patent license(s).
2.	If you have source video that is encoded with H.265(HEVC) video codec, such as video captured using iOS11 or GoPro Hero 6, you can now use either the Premium Encoder or the Standard Encoder to encode those videos. Please refer to the [Online Services Terms](https://azure.microsoft.com/support/legal/) for an important note about patent license(s).
3.	If you have content that contains multiple language audio tracks, then as long as the language values are correctly labeled according to the corresponding file format specification (e.g. ISO MP4), then you can use the Standard Encoder to encode that content for streaming. The resultant streaming locator will list the available audio languages.
4.	The Standard Encoder now supports two new audio-only system presets, “AAC Audio” and “AAC Good Quality Audio”. Both produce stereo AAC output, at bit rates of 128 kbps and 192 kbps, respectively.
5.	The Premium Encoder now supports QuickTime/MOV file formats as input as long as the video codec is either one of the [Apple ProRes flavors listed here](https://docs.microsoft.com/en-us/azure/media-services/media-services-media-encoder-standard-formats), and the audio is either AAC or PCM.

> [!NOTE]
> The Premium Encoder does not support, for example, DVC/DVCPro video wrapped in QuickTime/MOV files, as input.  However, the Standard Encoder does support these video codecs.
>
>

6.	Bug fixes in Encoders:
    * You can now submit Jobs using an Input Asset, and after these complete, modify the Asset (for example by adding/deleting/renaming files within the Asset), and submit additional Jobs. 
    * Improved quality of JPEG thumbnails produced by the Standard Encoder
    * Improvements to Standard Encoder for short-duration videos. Better handling of input metadata and thumbnail generation in very short duration videos.
    * Improvements to the H.264 decoder used in the Standard Encoder, eliminates certain rare artifacts. 

#### Media Analytics
* GA of Azure Media Redactor - This Media Processor (MP) will perform anonymization by blurring the faces of selected individuals, and is ideal for use in public safety and news media scenarios. For an overview on this new processor see the blog post [here](https://azure.microsoft.com/blog/azure-media-redactor/). For detailed documentation and settings, see [Redact faces with Azure Media Analytics](media-services-face-redaction.md).



## June 2017 Release

Media Services now supports [Azure Active Directory (Azure AD)-based authentication](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Currently, Media Services supports the Azure Access Control service authentication model. However, Access Control authorization will be deprecated on June 1, 2018. We recommend that you migrate to the Azure AD authentication model as soon as possible.

## March 2017 Release

You can now use Azure Media Standard to [auto-generate a bitrate ladder](media-services-autogen-bitrate-ladder-with-mes.md) by specifying the "Adaptive Streaming" preset string when creating an encoding task. "Adaptive Streaming" is the recommended preset if you want to encode a video for streaming with Media Services. If you need to customize an encoding preset for your specific scenario, you can begin with [these](media-services-mes-presets-overview.md) presets.

You can now use Azure Media Standard or Media Encoder Premium Workflow to [create an encoding task that generates fMP4 chunks](media-services-generate-fmp4-chunks.md). 


## February 2017 Release

Starting April 1, 2017, any Job record in your account older than 90 days will be automatically deleted, along with its associated Task records, even if the total number of records is below the maximum quota. If you need to archive the job/task information, you can use the code described [here](media-services-dotnet-manage-entities.md).

## January 2017 Release

In Microsoft Azure Media Services (AMS), a **Streaming Endpoint** represents a streaming service that can deliver content directly to a client player application, or to a Content Delivery Network (CDN) for further distribution. Media Services also provides seamless Azure CDN integration. The outbound stream from a StreamingEndpoint service can be a live stream, a video on demand, or progressive download of your asset in your Media Services account. Each Azure Media Services account includes a default StreamingEndpoint. Additional StreamingEndpoints can be created under the account. There are two versions of StreamingEndpoints, 1.0 and 2.0. Starting with January 10th 2017, any newly created AMS accounts will include version 2.0 **default** StreamingEndpoint. Additional streaming endpoints that you add to this account will also be version 2.0. This change will not impact the existing accounts; existing StreamingEndpoints will be version 1.0 and can be upgraded to version 2.0. With this change there will be behavior, billing and feature changes (for more information, see [this](media-services-streaming-endpoints-overview.md) article).

In addition, starting with the 2.15 version, Azure Media Services added the following properties to the Streaming Endpoint entity: **CdnProvider**, **CdnProfile**, **FreeTrialEndTime**, **StreamingEndpointVersion**. For detailed overview of these properties, see [this](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint). 

## December 2016 Release

Azure Media Services now enables you to access telemetry/metrics data for its services. The current version of AMS lets you collect telemetry data for live Channel, StreamingEndpoint, and live Archive entities. For more information, see [this](media-services-telemetry-overview.md) article.

## <a id="july_changes16"></a>July 2016 Release
### Updates to manifest file (*.ISM) generated by encoding tasks
When an encoding task is submitted to Media Encoder Standard or Azure Media Encoder, the encoding task generates a [streaming manifest file](media-services-deliver-content-overview.md) (*.ism) file in the output Asset. With the latest service release, the syntax of this streaming manifest file has been updated.

> [!NOTE]
> The syntax of the streaming manifest (.ism) file is reserved for internal use, and is subject to change in future releases. Please do not modify or manipulate the contents of this file.
> 
> 

### A new client manifest (*.ISMC) file is generated in the output Asset when an encoding task outputs one or more MP4 files
Starting with the latest service release, after the completion of an encoding task that generates one more MP4 files, the output Asset will also contain a streaming client manifest (*.ismc) file. The .ismc file helps improve the performance of dynamic streaming. 

> [!NOTE]
> The syntax of the client manifest (.ismc) file is reserved for internal use, and is subject to change in future releases. Please do not modify or manipulate the contents of this file.
> 
> 

For more information, see [this](https://blogs.msdn.microsoft.com/randomnumber/2016/07/08/encoder-changes-within-azure-media-services-now-create-ismc-file/) blog.

### Known issues
Some clients can come across a repeat tag issue in the Smooth Streaming manifest. For more information, see [this](media-services-deliver-content-overview.md#known-issues) section.

## <a id="apr_changes16"></a>April 2016 Release
### Azure Media Analytics
Azure Media Services introduced Azure Media Analytics for powerful video intelligence. For detailed information, see [Azure Media Services Analytics Overview](media-services-analytics-overview.md).

### Apple FairPlay (Preview)
Azure Media Services now enables you to dynamically encrypt your HTTP Live Streaming (HLS) content with  Apple FairPlay. You can also use AMS license delivery service to deliver FairPlay licenses to clients. For more detailed information, see [Use Azure Media Services to Stream your HLS content Protected with Apple FairPlay ](media-services-protect-hls-with-fairplay.md).

## <a id="feb_changes16"></a>February 2016 Release
The latest version of Azure Media Services SDK for .NET (3.5.3) contains a Widevine related bug fix. The problem was:  AssetDeliveryPolicy couldn't be reused for multiple assets encrypted with Widevine. As part of this bug fix the following property was added to the SDK: **WidevineBaseLicenseAcquisitionUrl**.

    Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
        new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
    {
        {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

    };

## <a id="jan_changes_16"></a>January 2016 Release
Encoding Reserved Units renamed to reduce confusion with Encoder names.

The Basic, Standard, and Premium encoding reserved units are renamed to S1, S2, and S3  reserved units, respectively.  Customers using Basic Encoding RUs today will see S1 as the label in Azure portal (and in the bill), while Standard and Premium will see the labels S2 and S3 respectively. 

## <a id="dec_changes_15"></a>December 2015 Release

### Azure Media Encoder deprecation announcement

Azure Media Encoder will be deprecated starting in approximately 12 months from the release of Media Encoder Standard.

### Azure SDK for PHP
The Azure SDK team published a new release of the [Azure SDK for PHP](http://github.com/Azure/azure-sdk-for-php) package that contains updates and new features for Microsoft Azure Media Services. In particular, the Azure Media Services SDK for PHP now supports the latest [content protection](media-services-content-protection-overview.md) features: dynamic encryption with AES and DRM (PlayReady and Widevine) with and without Token restriction. It also supports scaling [Encoding Units](media-services-dotnet-encoding-units.md).

For more information, see:

* The [Microsoft Azure Media Services SDK for PHP](http://southworks.com/blog/2015/12/09/new-microsoft-azure-media-services-sdk-for-php-release-available-with-new-features-and-samples/) blog.
* The following [code samples](http://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) to help get you started quickly:
  * **vodworkflow_aes.php**: This is a PHP file that shows how to use AES-128 Dynamic Encryption and Key Delivery Service. It is based on the .NET sample explained in [this](media-services-protect-with-aes128.md) article.
  * **vodworkflow_aes.php**: This is a PHP file that shows how to use PlayReady Dynamic Encryption and License Delivery Service. It is based on the .NET sample explained in [this](media-services-protect-with-drm.md) article.
  * **scale_encoding_units.php**: This is a PHP file that shows how to scale encoding reserved unit.

## <a id="nov_changes_15"></a>November 2015 Release
Azure Media Services now offers Google Widevine license delivery service in the cloud. For more details, refer to [this announcement blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Also, see [this tutorial](media-services-protect-with-drm.md) and [GitHub repository](http://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Note that Widevine license delivery services provided by Azure Media Services is in preview. For more information see [this blog](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a id="oct_changes_15"></a>October 2015 Release
Azure Media Services (AMS) is now live in the following data centers: Brazil South,  India West, India South and India Central. You can now use the Azure portal to [create Media Service accounts](media-services-portal-create-account.md) and perform various tasks described [here](https://azure.microsoft.com/documentation/services/media-services/). However, Live Encoding is not enabled in these data centers. Further, not all types of Encoding Reserved Units are available in these data centers.

* Brazil South:                                          Only Standard and Basic Encoding Reserved Units are available
* India West, India South and India Central:             Only Basic Encoding Reserved Units are available

## <a id="september_changes_15"></a>September 2015 Release
* AMS now offers the ability to protect both Video-On-Demand (VOD) and Live Streams with Widevine Modular DRM technology. You can use the following delivery services partners to help you deliver Widevine licenses: [Axinom](http://www.axinom.com/press/ibc-axinom-drm-6/), [EZDRM](http://ezdrm.com/), [castLabs](http://castlabs.com/company/partners/azure/). For more information, see [this blog](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
    You can use [AMS .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (starting with the version 3.5.1) or REST API to configure your AssetDeliveryConfiguration to use Widevine.  
* AMS added support for Apple ProRes videos. You can now upload your QuickTime source videos files that use Apple ProRes or other codecs. For more information, see [this blog](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* You can now use Media Encoder Standard to do sub-clipping and live archive extraction. For more information, see [this blog](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* The following filtering updates were made: 
  
  * You can now use Apple HTTP Live Streaming (HLS) format with audio-only filter. This update enables you to remove audio-only track by specifying (audio-only=false) in the URL.
  * When defining filters for your assets, you now have ability to combine multiple (up to 3) filters in a single URL.
    
    For more information see [this](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.
* AMS now supports I-Frames in HLS v4. I-Frame support optimizes fast-forward and rewind operations. By default, all HLS v4 outputs include I-Frame playlist (EXT-X-I-FRAME-STREAM-INF).
  
    For more information see [this](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) blog.

## <a id="august_changes_15"></a>August 2015 Release
* Azure Media Services SDK for Java V0.8.0 release and new samples are now available. For more information, see:
  
  * [Blog post](http://southworks.com/blog/2015/08/25/microsoft-azure-media-services-sdk-for-java-v0-8-0-released-and-new-samples-available/)
  * [Java samples repository](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
* Azure Media Player update with multi-audio stream support. For more information, see:
  * [Blog post](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/)

## <a id="july_changes_15"></a>July 2015 Release
* Announcing the general availability of Media Encoder Standard. For more information, see [this blog post](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard uses presets described in [this](http://go.microsoft.com/fwlink/?LinkId=618336) section. Note that when using a preset for 4k encodes, you should get the **Premium** reserved unit type. For more information, see [How to Scale Encoding](media-services-scale-media-processing-overview.md).
* Live Real-time captions with Azure Media Services and Player. For more information, see  [this blog post](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/)

### Media Services .NET SDK Updates
Azure Media Services .NET SDK is now version 3.4.0.0. The following functionality was added in this release:  

* Implemented support for live archive. Note that you cannot download an asset that contains a live archive.
* Implemented support for dynamic filters.
* Implemented functionality that allows users to keep storage container while deleting asset.
* Bug fixes related to retry policies in channels.
* Enabled  **Media Encoder Premium Workflow**.

## <a id="june_changes_15"></a>June 2015 Release
### Media Services .NET SDK Updates
Azure Media Services .NET SDK is now version 3.3.0.0. The following functionality was added in this release:  

* support for OpenId Connect Discovery spec,
* support for handling keys rollover on identity provider side. 

If you are using an identity provider which exposes OpenID Connect discovery document (as the following providers do: Azure Active Directory, Google, Salesforce), you can instruct Azure Media Services to obtain signing keys for validation of JWT token from OpenID connect discovery spec. 

For more information, see [Using Json Web Keys from OpenID Connect discovery spec to work with JWT token authentication in Azure Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a id="may_changes_15"></a>May 2015 Release
Announcing the following new features:

* [A preview of Live Encoding with Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamic manifest](media-services-dynamic-manifest-overview.md)
* [A preview of Azure Media Hyperlapse media processor](https://azure.microsoft.com/blog/?p=286281&preview=1&_ppp=61e1a0b3db)

## <a id="april_changes_15"></a>April 2015 Release
### General Media Services Updates
* [Announcing Azure Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/).
* Starting with Media Services REST 2.10, channels that are configured to ingest an RTMP protocol, are created with primary and secondary ingest URLs. For more information, see [Channel ingest configurations](media-services-live-streaming-with-onprem-encoders.md#channel_input)
* Azure Media Indexer updates
* Support for Spanish Language
* New configuration xml format

For more information see [this blog](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### Media Services .NET SDK Updates
Azure Media Services .NET SDK is now version 3.2.0.0.

The following are some of the customer facing updates:

* **Breaking change**: Changed **TokenRestrictionTemplate.Issuer** and **TokenRestrictionTemplate.Audience** to be of a string type.
* Updates related to creating custom retry policies.
* Bug fixes related to uploading/downloading files.
* The **MediaServicesCredentials** class now accepts primary and secondary access control endpoint to authenticate against.

## <a id="march_changes_15"></a>March 2015 Release
### General Media Services Updates
* Media Services now provides Azure CDN integration. To support the integration, the **CdnEnabled** property was added to **StreamingEndpoint**.  **CdnEnabled** can be used with REST APIs starting with version 2.9 (for more information, see [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint)).  **CdnEnabled** can be used with .NET SDK starting with version 3.1.0.2 (for more information, see [StreamingEndpoint](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint\(v=azure.10\).aspx)).
* Announcement of **Media Encoder Premium Workflow**. For more information, see [Introducing Premium Encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a id="february_changes_15"></a>February 2015 Release
### General Media Services Updates
Media Services REST API is now version 2.9. Starting with this version, you can enable the Azure CDN integration with streaming endpoints. For more information, see [StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx).

## <a id="january_changes_15"></a>January 2015 Release
### General Media Services Updates
Announcement of General Availability (GA) of content protection with dynamic encryption. For more information, see [Azure Media Services enhances streaming security with General Availability of DRM technology](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### Media Services .NET SDK Updates
Azure Media Services .NET SDK is now version 3.1.0.1.

This release marked the default Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate constructor as obsolete. The new constructor takes TokenType as an argument.

    TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>December 2014 Release
### General Media Services Updates
* Some updates and new features were added to the Azure Indexer Media processor. For more information, see [Azure Media Indexer Version 1.1.6.7 Release Notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Added a new REST API that enables you to update encoding reserved units: [EncodingReservedUnitType with REST](https://docs.microsoft.com/rest/api/media/operations/encodingreservedunittype).
* Added CORS support for key delivery service.
* Performance improvements of querying authorization policy options were done.
* In China data center, the [Key Delivery URL](https://docs.microsoft.com/rest/api/media/operations/contentkey#get_delivery_service_url) is now per customer (just like in other data centers).
* Added HLS auto target duration. When doing live streaming, HLS is always packaged dynamically. By default, Media Services automatically calculates HLS segment packaging ratio (FragmentsPerSegment) based on the keyframe interval (KeyFrameInterval ), also referred to as Group of Pictures – GOP, that is received from the Live encoder. For more information, see [Working with Azure Media Services Live Streaming].

### Media Services .NET SDK Updates
* [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) is now version 3.1.0.0.
* Upgraded the .Net SDK dependency to .NET 4.5 Framework.
* Added a new API that enables you to update encoding reserved units. For more information, see [Updating Reserved Unit Type and Increasing Encoding RUs using .NET](media-services-dotnet-encoding-units.md).
* Added JWT (JSON Web Token) support for token authentication. For more information, see [JWT token Authentication in Azure Media Services and Dynamic Encryption](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Added relative offsets for BeginDate and ExpirationDate in the PlayReady license template.

## <a id="november_changes_14"></a>November 2014 Release
* Media Services now enables you to ingest a live Smooth Streaming (FMP4) content over an SSL connection. To ingest over SSL, make sure to update the ingest URL to HTTPS.  Note that, currently, AMS doesn’t support SSL with custom domains.  For more information about live streaming, see [Working with Azure Media Services Live Streaming].
* Currently, you cannot ingest an RTMP live stream over an SSL connection.
* You can only stream over SSL if the streaming endpoint from which you deliver your content was created after September 10th, 2014. If your streaming URLs are based on the streaming endpoints created after September 10th, the URL contains “streaming.mediaservices.windows.net” (the new format). Streaming URLs that contain “origin.mediaservices.windows.net” (the old format) do not support SSL. If your URL is in the old format and you want to be able to stream over SSL, [create a new streaming endpoint](media-services-portal-manage-streaming-endpoints.md). Use URLs created based on the new streaming endpoint to stream your content over SSL.

## <a id="october_changes_14"></a>October 2014 Release
### <a id="new_encoder_release"></a>Media Services Encoder Release
Announcing the new release of Media Services Azure Media Encoder. With the latest Azure Media Encoder you are only charged for output GBs, but otherwise the new encoder is feature compatible with the previous encoder. For more information [Media Services Pricing Details]).

### <a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK for .NET Extensions is now version 2.0.0.3.

Media Services SDK for .NET is now version 3.0.0.8.

The following changes were made:

* Refactoring in retry policy classes.
* Adding user agent string to http request headers.
* Adding nuget restore build step.
* Fixing scenario tests to use x509 cert from repository.
* Validating settings when updating channel and streaming end.

### New GitHub repository to host Media Services samples
Samples are located in [Azure Media Services samples GitHub repository](https://github.com/Azure/Azure-Media-Services-Samples).

## <a id="september_changes_14"></a>September 2014 Release
Media Services REST metadata is now version 2.7. For more information about the latest REST updates, see [Azure Media Services REST API Reference].

Media Services SDK for .NET is now version 3.0.0.7

### <a id="sept_14_breaking_changes"></a>Breaking Changes
* **Origin** was renamed to [StreamingEndpoint].
* A change in the default behavior when using the **Azure portal** to encode and then publish MP4 files.

Previously, when using the Azure Classic portal to publish a single-file MP4 video asset a SAS URL would be created (SAS URLs allow you to download the video from a blob storage). Currently, when you use the Azure Classic portal to encode and then publish a single-file MP4 video asset, the generated URL points to an Azure Media Services streaming endpoint.  This change does not affect MP4 videos that are directly uploaded to Media Services and published without being encoded by Azure Media Services.

Currently, you have the following two options to solve the problem.

* Enable streaming units and use dynamic packaging to stream the .mp4 asset as a smooth streaming presentation.
* Create a SAS url to download (or progressively play) the .mp4. For more information about how to create a SAS locator, see [Delivering Content].

### <a id="sept_14_GA_changes"></a>New features/scenarios that are part of GA release
* **Indexer Media Processor**. For more information see [Indexing Media Files with Azure Media Indexer].
* The [StreamingEndpoint] entity now enables you to add custom domain (host) names.
  
    For a custom domain name to be used as the Media Services streaming endpoint name, you need to add custom host names to your streaming endpoint. Use the Media Services REST APIs or .NET SDK to add custom host names.
  
    The following considerations apply:
  
  * You must have the ownership of the custom domain name.
  * The ownership of the domain name must be validated by Azure Media Services. To validate the domain, create a CName that maps <MediaServicesAccountId>.<parent domain> to verify dns.<mediaservices-dns-zone>. 
  * You must create another CName  that maps the custom host name (for example,  sports.contoso.com) to your Media Services StreamingEndpont’s host name (for example,  amstest.streaming.mediaservices.windows.net).

    For more information, see the **CustomHostNames** property in the [StreamingEndpoint] article.

### <a id="sept_14_preview_changes"></a>New features/scenarios that are part of the public preview release
* Live Streaming Preview. For more information, see [Working with Azure Media Services Live Streaming].
* Key Delivery Service. For more information, see [Using AES-128 Dynamic Encryption and Key Delivery Service].
* AES Dynamic Encryption. For more information, see [Using AES-128 Dynamic Encryption and Key Delivery Service].
* PlayReady License Delivery Service. For more information, see [Using PlayReady Dynamic Encryption and License Delivery Service].
* PlayReady Dynamic Encryption. For more information, see [Using PlayReady Dynamic Encryption and License Delivery Service].
* Media Services PlayReady License Template. For more information, see [Media Services PlayReady License Template Overview].
* Streaming storage encrypted assets. For more information, see [Streaming Storage Encrypted Content].

## <a id="august_changes_14"></a>August 2014 Release
When you encode an asset, an output asset is produced upon completion of the encoding job. Until this release, Azure Media Services Encoder produced metadata about output assets. Starting with this release the encoder also produces metadata about input assets. For more information, see the [Input Metadata] and [Output Metadata] articles.

## <a id="july_changes_14"></a>July 2014 Release
The following bug fixes were made for the Azure Media Services Packager and Encryptor:

* Only audio plays back when transmuxing a live archive asset to HTTP Live Streaming – this has been fixed and now both audio and video are played.
* When packaging an asset to HTTP Live Streaming and AES 128-bit envelope encryption, the packaged streams do not play back on Android devices – this bug has been fixed and the packaged stream plays back on Android devices that support HTTP Live Streaming.

## <a id="may_changes_14"></a>May 2014 Release
### <a id="may_14_changes"></a>General Media Services Updates
You can now use [Dynamic Packaging] to stream HTTP Live Streaming (HLS) v3. To stream HLS v3, add the following format to the origin locator path: * .ism/manifest(format=m3u8-aapl-v3). For more information, see [Nick Drouin's Blog].

Dynamic Packaging now also supports delivering HLS (v3 and v4) encrypted with PlayReady based on Smooth Streaming statically encrypted with PlayReady. For information on how to encrypt Smooth Streaming with PlayReady, see [Protecting Smooth Stream with PlayReady].

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK Updates
The following improvements are included in the Media Services .NET SDK 3.0.0.5 release:

* Better speed and resilience for uploading/downloading media assets.
* Improvements in retry logic and transient exception handling: 
  
  * Transient error detection and retry logic were improved for exceptions that are caused by querying, saving changes, uploading or downloading files. 
  * When getting Web Exceptions (for example, during an ACS token request), you will notice that fatal errors are failing faster now.

For more information, see [Retry Logic in the Media Services SDK for .NET].

## <a id="april_changes_14"></a>April 2014 Encoder Release
### <a name="april_14_enocer_changes"></a>Media Services Encoder Updates
* Added support for ingesting AVI files authored using the Grass Valley EDIUS nonlinear editor, where the video is lightly compressed using Grass Valley HQ/HQX codec. For more information, see [Grass Valley Announces EDIUS 7 Streaming Through the Cloud].
* Added support for specifying the naming convention for the files produced by the Media Encoder. For more information, see [Controlling Media Service Encoder Output Filenames].
* Added support for video and/or audio overlays. For more information, see [Creating Overlays].
* Added support for stitching together multiple video segments. For more information, see [Stitching Video Segments].
* Fixed a bug related to transcoding of MP4s where the audio has been encoded with MPEG-1 Audio layer 3 (aka MP3).

## <a id="jan_feb_changes_14"></a>January/February 2014 Releases
### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1, 3.0.0.2 and 3.0.0.3
The changes in 3.0.0.1 and 3.0.0.2 include:

* Fixed issues related to usage of LINQ queries with OrderBy statements.
* Split test solutions in [GitHub] into Unit-based tests and Scenario-based tests.

For more details about the changes, see: [Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases].

The following changes were made in 3.0.0.3:

* Upgraded Azure storage dependencies to use version 3.0.3.0. 
* Fixed backward compatibility issue for 3.0.*.* releases. 

## <a id="december_changes_13"></a>December 2013 Release
### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0
> [!NOTE]
> 3.0.x.x releases are not backward compatible with 2.4.x.x releases.
> 
> 

The latest version of the Media Services SDK is now 3.0.0.0. You can download the latest package from Nuget or get the bits from [GitHub].

Starting with the Media Services SDK version 3.0.0.0, you can reuse the [Azure Active Directory Access Control Service (ACS)] tokens. For more information, see the “Reusing Access Control Service Tokens” section in the [Connecting to Media Services with the Media Services SDK for .NET] article.

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0
The Azure Media Services .NET SDK Extensions is a set of extension methods and helper functions that will simplify your code and make it easier to develop with Azure Media Services. You can get the latest bits from [Azure Media Services .NET SDK Extensions].

## <a id="november_changes_13"></a>November 2013 Release
### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK Changes
Starting with this version, the Media Services SDK for .NET handles transient fault errors that may occur when making calls to the Media Services REST API layer.

## <a id="august_changes_13"></a>August 2013 Release
### <a name="aug_13_powershell_changes"></a>Media Services PowerShell Cmdlets included in Azure SDK Tools
The following Media Services PowerShell cmdlets are now included in [azure-sdk-tools].

* Get-AzureMediaServices 
  
    For example, `Get-AzureMediaServicesAccount`.
* New-AzureMediaServicesAccount 
  
    For example, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.
* New-AzureMediaServicesKey 
  
    For example, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.
* Remove-AzureMediaServicesAccount 
  
    For example, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <a id="june_changes_13"></a>June 2013 Release
### <a name="june_13_general_changes"></a>Azure Media Services changes
The changes mentioned in this section are updates included in the June 2013 Media Services releases.

* Ability to link multiple storage accounts to a Media Service account. 
  
    StorageAccount
  
    Asset.StorageAccountName and Asset.StorageAccount
* Ability to update Job.Priority. 
* Notification related entities and properties: 
  
    JobNotificationSubscription
  
    NotificationEndPoint
  
    Job
* Asset.Uri 
* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK changes
The following changes are included in June 2013 Media Services SDK releases. The latest Media Services SDK is available on GitHub.

* Starting with the version 2.3.0.0, the Media Services SDK supports linking multiple storage accounts to a Media Services account. The following APIs support this feature:
  
    The IStorageAccount type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts property.
  
    The StorageAccount property.
  
    The StorageAccountName property.
  
    For more information, see [Managing Media Services Assets across Multiple Storage Accounts].
* Notification related APIs. Starting with the version 2.2.0.0 you have the ability to listen to Azure Queue storage notifications. For more information see, [Handling Media Services Job Notifications].
  
    The Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions property.
  
    The Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType type.
  
    The Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState type.
* Dependency on the Azure Storage Client SDK 2.0 (Microsoft.WindowsAzure.StorageClient.dll).
* Dependency on OData 5.5 (Microsoft.Data.OData.dll).

## <a id="december_changes_12"></a>December 2012 Release
### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK changes
* Intellisense : Added missing Intellisense documentation for many types.
* Microsoft.Practices.TransientFaultHandling.Core : Fixed an issue where the SDK still had a dependency to an old version of this assembly. The SDK now references version 5.1.1209.1 of this assembly.

Fixes for issues found in the November 2012 SDK:

* IAsset.Locators.Count : This count is now correctly reported on new IAsset interfaces after all locators have been deleted.
* IAssetFile.ContentFileSize : This value is now properly set after an upload by IAssetFile.Upload(filepath).
* IAssetFile.ContentFileSize : This property can now be set when creating an asset file. It was previously read-only.
* IAssetFile.Upload(filepath) : Fixed an issue where this synchronous upload method was throwing the following error when uploading multiple files to the asset. The error was "Server failed to authenticate the request. Make sure the value of Authorization header is formed correctly including the signature."
* IAssetFile.UploadAsync : Fixed an issue where no more than 5 files could be uploaded simultaneously.
* IAssetFile.UploadProgressChanged : This event is now provided by the SDK.
* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): This method overload is now provided.
* IAssetFile.DownloadAsync : Fixed an issue where no more than 5 files could be downloaded simultaneously.
* IAssetFile.Delete() : Fixed an issue where calling delete may throw an exception if no file was uploaded for the IAssetFile.
* Jobs : Fixed an issue where chaining a "MP4 to Smooth Streams task" with a "PlayReady Protection Task" using a job template would not create any tasks at all.
* EncryptionUtils.GetCertificateFromStore() : This method no longer throws a null reference exception due to a failures finding the certificate based on certificate configuration issues.

## <a id="november_changes_12"></a>November 2012 Release
The changes mentioned in this section were updates included in the November 2012 (version 2.0.0.0) SDK. These changes may require any code written for the June 2012 preview SDK release to be modified or rewritten.

* Assets
  
    IAsset.Create(assetName) is the ONLY asset creation function. IAsset.Create no longer uploads files as part of the method call. Use IAssetFile for uploading.
  
    The IAsset.Publish method and the AssetState.Publish enumeration value have been removed from the Services SDK. Any code that relies on this value must be re-written.
* FileInfo
  
    This class has been removed and replaced by IAssetFile.
  
    IAssetFiles
  
    IAssetFile replaces FileInfo and has a different behavior. To use it, instantiate the IAssetFiles object, followed by a file upload either using the Media Services SDK or the Azure Storage SDK. The following IAssetFile.Upload overloads can be used:
  
  * IAssetFile.Upload(filePath): A synchronous method that blocks the thread and is recommended only when uploading a single file.
  * IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): An asynchronous method. This is the preferred upload mechanism. 
    
    Known bug: using the cancellationToken will indeed cancel the upload; however, the cancellation state of the tasks can be any of a number of states. You must properly catch and handle exceptions.
* Locators
  
    The Origin-specific versions have been removed. The SAS-specific context.Locators.CreateSasLocator(asset, accessPolicy) will be marked deprecated, or removed by GA. See the Locators section under New Functionality for updated behavior.

## <a id="june_changes_12"></a>June 2012 Preview Release
The following functionality was new in the November release of the SDK.

* Deleting Entities
  
    IAsset, IAssetFile, ILocator, IAccessPolicy, IContentKey objects are now deleted at the object level, i.e. IObject.Delete(), instead of requiring a delete in the Collection, that is cloudMediaContext.ObjCollection.Delete(objInstance).
* Locators
  
    Locators must now be created using the CreateLocator method and use the LocatorType.SAS or LocatorType.OnDemandOrigin enum values as an argument for the specific type of locator you want to create.
  
    New properties were added to Locators to make it easier to obtain usable URIs for your content. This redesign of Locators was meant to provide more flexibility for future third-party extensibility and increase ease-of-use for media client applications.
* Asynchronous Method Support
  
    Asynchronous support has been added to all methods.

## Media Services learning paths
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## Provide feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Azure Media Services MSDN Forum]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API Reference]: https://docs.microsoft.com/rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services Pricing Details]: http://azure.microsoft.com/pricing/details/media-services/
[Input Metadata]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Output Metadata]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Delivering Content]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indexing Media Files with Azure Media Indexer]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Working with Azure Media Services Live Streaming]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Using AES-128 Dynamic Encryption and Key Delivery Service]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Using PlayReady Dynamic Encryption and License Delivery Service]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Preview features]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady License Template Overview]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Streaming Storage Encrypted Content]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure portal]: https://manage.windowsazure.com
[Dynamic Packaging]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's Blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protecting Smooth Stream with PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Retry Logic in the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Controlling Media Service Encoder Output Filenames]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creating Overlays]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Stitching Video Segments]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Connecting to Media Services with the Media Services SDK for .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Managing Media Services Assets across Multiple Storage Accounts]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Handling Media Services Job Notifications]: http://msdn.microsoft.com/library/azure/dn261241.aspx

