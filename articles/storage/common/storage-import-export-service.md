---
title: Using Azure Import/Export to transfer data to and from Azure Storage | Microsoft Docs
description: Learn how to create import and export jobs in the Azure portal for transferring data to and from Azure Storage.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''

ms.assetid: 668f53f2-f5a4-48b5-9369-88ec5ea05eb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2017
ms.author: muralikk

---
# Use the Microsoft Azure Import/Export service to transfer data to Azure Storage
In this article, we provide step-by-step instructions on using Azure Import/Export service to securely transfer large amounts of data to Azure Blob storage and Azure Files by shipping disk drives to an Azure data center. This service can also be used to transfer data from Azure storage to hard disk drives and ship to your on-premise sites. Data from a single internal SATA disk drive can be imported either to Azure Blob storage or Azure Files. 

> [!IMPORTANT] 
> This service only accepts internal SATA HDDs or SSDs only. No other device is supported. Do not send external HDDs, NAS devices, etc., as they will be returned if possible, or otherwise discarded.
>
>

Follow the below steps if the data on the disk is to be imported into Azure Storage.
### Step 1: Prepare the drive/s using WAImportExport tool and generate journal file/s.

1.  Identify the data to be imported into Azure Storage. This could be directories and standalone files on a local server or a network share.
2.  Depending on total size of the data, procure the required number of 2.5 inch SSD or 2.5" or 3.5" SATA II or III hard disk drives.
3.	Attach the hard drives directly using SATA or with external USB adaptors to a windows machine.
4.  Create a single NTFS volume on each hard drive and assign a drive letter to the volume. No mountpoints.
5.  Enable bit locker encryption on the NTFS volume. Use the instructions on https://technet.microsoft.com/en-us/library/cc731549(v=ws.10).aspx to enable encryption on the windows machine.
6.  Completely copy data to these encrypted single NTFS volumes on disks using copy & paste or  drag & drop or Robocopy or any such tool.
7.	Download WAImportExport V1 from https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.	Unzip to the default folder waimportexportv1. For example, C:\WaImportExportV1  
9.	Run as Administrator and open a PowerShell or Command line and change directory to the unzipped folder. For example, cd C:\WaImportExportV1
10.	Copy the below command line to a notepad and edit it to create a command line.
  ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite
    
    /j:  The name of a file called journal file with .jrn extension. A journal file is generated per drive and so it is recommended to use the disk serial number as the journal file name.
    /sk: Azure Storage Account key. 
    /t:  Drive letter of the disk to be shipped. For example, D
    /bk: is the bit locker key of the drive
    /srcdir: Drive letter of the disk to be shipped followed by :\. For example, D:\
    /dstdir: The name of Azure Storage Container to which the data is to be imported.
    /skipwrite 
    
11. Repeat step 10 for each of disk that needs to be shipped.
12. A journal file with name provided with /j: parameter is created for every run of the command line.

### Step 2: Create an Import Job on Azure Portal.

1. Log on to https://portal.azure.com/ and under More services -> STORAGE -> "Import/export jobs" Click **Create Import/export Job**.

2. In Basics section, select "Import into Azure", enter a string for job name, select a subscription, enter or select a resource group. Enter a descriptive name for the import job. Note that the name you enter may contain only lowercase letters, numbers, hyphens, and underscores, must start with a letter, and may not contain spaces. You will use the name you choose to track your jobs while they are in progress and once they are completed.

3. In Job details section, upload the drive journal files that you obtained during the drive preparation step. If waimportexport.exe version1 was used, you will need to upload one file for each drive that you have prepared. Select the storage account that the data will be imported into in the "Import destination" Storage account section. The Drop-Off location  will be automatically populated based on the region of the storage account selected.
   
   ![Create import job - Step 3](./media/storage-import-export-service/import-job-03.png)
4. In Return shipping info section, select the carrier from the drop down list and enter a valid carrier account number that you have created with that carrier. Microsoft will use this account to ship the drives back to you once your import job is complete. Provide a complete and valid contact name, phone, email, street address, city, zip, state/proviince and country/region.
   
5. In the Summary section, Azure DataCenter shipping address is provided to be used for shipping disks to Azure DC. Ensure that the job name and the full address are mentioned on the shipping label. 

6. Click OK on the Summary Page to complete Import job creation.

### Step 3: Ship the drive/s to the Azure Datacenter shipping address provided in Step 2.
FedEx, UPS or DHL can be used to ship the package to Azure DC.

### Step 4: Update the job created in Step2 with tracking number of the shipment.
After shipping the disks, return to the **Import/Export** page on the Azure portal to update the tracking number using the steps below, 
     a) Navigate and click on the import job
     b) Click on **Update job status and tracking info once drives are shipped**. 
     c) Select the check box "Mark as shipped"
     d) Provide the Carrier and Tracking number.
If the tracking number is not updated within 2 weeks of creating the job, the job will expire. The job progress can be tracked on the portal dashboard. See what each job state in the previous section means by [Viewing your job status](#viewing-your-job-status).

## When should I use the Azure Import/Export service?
Consider using Azure Import/Export service when uploading or downloading data over the network is too slow, or getting additional network bandwidth is cost-prohibitive.

You can use this service in scenarios such as:

* Migrating data to the cloud: Move large amounts of data to Azure quickly and cost effectively.
* Content distribution: Quickly send data to your customer sites.
* Backup: Take backups of your on-premises data to store in Azure Storage.
* Data recovery: Recover large amount of data stored in storage and have it delivered to your on-premises location.

## Prerequisites
In this section we list the prerequisites required to use this service. Please review them carefully before shipping your drives.

### Storage account
You must have an existing Azure subscription and one or more storage accounts to use the Import/Export service. Each job may be used to transfer data to or from only one storage account. In other words, a single import/export job cannot span across multiple storage accounts. For information on creating a new storage account, see [How to Create a Storage Account](storage-create-storage-account.md#create-a-storage-account).

### Data types
You can use Azure Import/Export service to copy data to **Block** blobs, **Page** blobs, or **Files**. Conversely, you can only export **Block** blobs, **Page** blobs or **Append** blobs from Azure storage using this service. The service supports only import of Azure Files into Azure storage. Exporting Azure Files is not currently supported.

### Job
To begin the process of importing to or exporting from storage, you first create a job. A job can be an import job or an export job:

* Create an import job when you want to transfer data you have on-premises to your Azure storage account.
* Create an export job when you want to transfer data currently stored in your storage account to hard drives that are shipped to us. When you create a job, you notify the Import/Export service that you will be shipping one or more hard drives to an Azure data center.

* For an import job, you will be shipping hard drives containing your data.
* For an export job, you will be shipping empty hard drives.
* You can ship up to 10 hard disk drives per job.

You can create an import or export job using the Azure portal or the [Azure Storage Import/Export REST API](/rest/api/storageimportexport).

### WAImportExport tool
The first step in creating an **import** job is to prepare your drives that will be shipped for import. To prepare your drives, you must connect it to a local server and run the WAImportExport Tool on the local server. This WAImportExport tool facilitates copying your data to the drive, encrypting the data on the drive with BitLocker, and generating the drive journal files.

The journal files store basic information about your job and drive such as drive serial number and storage account name. This journal file is not stored on the drive. It is used during import job creation. Step-by-step details about job creation are provided later in this article.

The WAImportExport tool is only compatible with 64-bit Windows operating system. See the [Operating System](#operating-system) section for specific OS versions supported.

Download the latest version of the [WAImportExport tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). For more details about using the WAImportExport Tool, see the [Using the WAImportExport Tool](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Previous Version:** You can [download WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) version of the tool and refer to [WAImportExpot V1 usage guide](storage-import-export-tool-how-to-v1.md). WAImportExpot V1 version of the tool does provide support for **preparing disks when data is already pre-written to the disk**. Also you will need to use WAImportExpot V1 tool if the only key available is SAS-Key.

>

### Hard disk drives
Only 2.5 inch SSD or 2.5" or 3.5" SATA II or III internal HDD are supported for use with the Import/Export service. A single import/export job can have a maximum of 10 HDD/SSDs and each individual HDD/SSD can be of any size. Large number of drives can be spread across multiple jobs and there is no limits on the number of jobs that can be created. 

For import jobs, only the first data volume on the drive will be processed. The data volume must be formatted with NTFS.

> [!IMPORTANT]
> External hard disk drives that come with a built-in USB adaptor are not supported by this service. Also, the disk inside the casing of an external HDD cannot be used; please do not send external HDDs.
> 
> 

Below is a list of external USB adaptors used to copy data to internal HDDs. 
Anker 68UPSATAA-02BU
Anker 68UPSHHDS-BU
Startech SATADOCK22UE
Orico 6628SUS3-C-BK (6628 Series)
Thermaltake BlacX Hot-Swap SATA External Hard Drive Docking Station (USB 2.0 & eSATA)

### Encryption
The data on the drive must be encrypted using BitLocker Drive Encryption. This protects your data while it is in transit.

For import jobs, there are two ways to perform the encryption. The first way is to specify the option when using dataset CSV file while running the WAImportExport tool during drive preparation. The second way is to enable BitLocker encryption manually on the drive and specify the encryption key in the driveset CSV when running WAImportExport tool command line during drive preparation.

For export jobs, after your data is copied to the drives, the service will encrypt the drive using BitLocker before shipping it back to you. The encryption key will be provided to you via the Azure portal.  

### Operating System
You can use one of the following 64-bit Operating Systems to prepare the hard drive using the WAImportExport Tool before shipping the drive to Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10<sup>1</sup>, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. All of these operating systems support BitLocker Drive Encryption.

### Locations
The Azure Import/Export service supports copying data to and from all Public Azure storage accounts. You can ship hard disk drives to one of the following locations. If your storage account is in a public Azure location which is not specified here, an alternate shipping location will be provided when you are creating the job using the Azure portal or the Import/Export REST API.

Supported shipping locations:

* East US
* West US
* East US 2
* West US 2
* Central US
* North Central US
* South Central US
* West Central US
* North Europe
* West Europe
* East Asia
* Southeast Asia
* Australia East
* Australia Southeast
* Japan West
* Japan East
* Central India
* South India
* West India
* Canada Central
* Canada East
* Brazil South
* Korea Central
* US Gov Virginia
* US Gov Iowa
* US DoD East
* US DoD Central
* China East
* China North
* UK South

### Shipping
**Shipping drives to the data center:**

When creating an import or export job, you will be provided a shipping address of one of the supported locations to ship your drives. The shipping address provided will depend on the location of your storage account, but it may not be the same as your storage account location.

FedEx, UPS or DHL can be used to ship your drives to the shipping address.

**Shipping drives from the data center:**

When creating an import or export job, you must provide a return address for Microsoft to use when shipping the drives back after your job is complete. Please make sure you provide a valid return address to avoid delays in processing.

The carrier should have appropriate tracking in order to maintain chain of custody. You must provide a valid FedEx, UPS or DHL carrier account number to be used by Microsoft for shipping the drives back. A FedEx, UPS or DHL account number is required for shipping drives back from the US and Europe locations. A DHL account number is required for shipping drives back from Asia and Australia locations. You can create a [FedEx](http://www.fedex.com/us/oadr/) (for US and Europe) or [DHL](http://www.dhl.com/) (Asia and Australia) carrier account if you do not have one. If you already have a carrier account number, please verify that it is valid.

In shipping your packages, you must follow the terms at [Microsoft Azure Service Terms](https://azure.microsoft.com/support/legal/services-terms/).

> [!IMPORTANT]
> Please note that the physical media that you are shipping may need to cross international borders. You are responsible for ensuring that your physical media and data are imported and/or exported in accordance with the applicable laws. Before shipping the physical media, check with your advisers to verify that your media and data can legally be shipped to the identified data center. This will help to ensure that it reaches Microsoft in a timely manner. For instance, any package that will cross international borders needs a commercial invoice to be accompanied with the package (except if crossing borders within European Union). You could print out a filled copy of the commercial invoice from carrier website. Example of commercial invoices are [DHL Commercial Invoice](http://invoice-template.com/wp-content/uploads/dhl-commercial-invoice-template.pdf) and [FedEx Commercial Invoice](http://images.fedex.com/downloads/shared/shipdocuments/blankforms/commercialinvoice.pdf). Make sure that Microsoft has not been indicated as the exporter.
> 
> 

## How does the Azure Import/Export service work?
You can transfer data between your on-premises site and Azure storage using the Azure Import/Export service by creating jobs and shipping hard disk drives to an Azure data center. Each hard disk drive you ship is associated with a single job. Each job is associated with a single storage account. Review the [pre-requisites section](#pre-requisites) carefully to learn about the specifics of this service such as supported data types, disk types, locations, and shipping.

In this section, we describe at a high level the steps involved in import and export jobs. Later in the [Quick Start section](#quick-start), we provide step-by-step instructions to create an import and export job.

### Inside an import job
At a high level, an import job involves the following steps:

* Determine the data to be imported, and the number of drives you will need.
* Identify the destination blob or file location for your data in Azure storage.
* Use the WAImportExport Tool to copy your data to one or more hard disk drives and encrypt them with BitLocker.
* Create an import job in your target storage account using the Azure portal or the Import/Export REST API. If using the Azure portal, upload the drive journal files.
* Provide the return address and carrier account number to be used for shipping the drives back to you.
* Ship the hard disk drives to the shipping address provided during job creation.
* Update the delivery tracking number in the import job details and submit the import job.
* Drives are received and processed at the Azure data center.
* Drives are shipped using your carrier account to the return address provided in the import job.
  
    ![Figure 1:Import job flow](./media/storage-import-export-service/importjob.png)

### Inside an export job
> [!IMPORTANT]
> The service only support export of Azure Blobs and does not support export of Azure Files..
> 
>

At a high level, an export job involves the following steps:

* Determine the data to be exported and the number of drives you will need.
* Identify the source blobs or container paths of your data in Blob storage.
* Create an export job in your source storage account using the Azure portal or the Import/Export REST API.
* Specify the source blobs or container paths of your data in the export job.
* Provide the return address and carrier account number for to be used for shipping the drives back to you.
* Ship the hard disk drives to the shipping address provided during job creation.
* Update the delivery tracking number in the export job details and submit the export job.
* The drives are received and processed at the Azure data center.
* The drives are encrypted with BitLocker; the keys are available via the Azure portal.  
* The drives are shipped using your carrier account to the return address provided in the import job.
  
    ![Figure 2:Export job flow](./media/storage-import-export-service/exportjob.png)

### Viewing your job and drive status
You can track the status of your import or export jobs from the Azure portal. Click the **Import/Export** tab. A list of your jobs will appear on the page.

![View Job State](./media/storage-import-export-service/jobstate.png)

You will see one of the following job statuses depending on where your drive is in the process.

| Job Status | Description |
|:--- |:--- |
| Creating | After a job is created, its state is set to Creating. While the job is in the Creating state, the Import/Export service assumes the drives have not been shipped to the data center. A job may remain in the Creating state for up to two weeks, after which it is automatically deleted by the service. |
| Shipping | After you ship your package, you should update the tracking information in the Azure portal.  This will turn the job into "Shipping". The job will remain in the Shipping state for up to two weeks. 
| Received | After all drives have been received at the data center, the job state will be set to the Received. |
| Transferring | Once at least one drive has begun processing, the job state will be set to the Transferring. See the Drive States section below for detailed information. |
| Packaging | After all drives have completed processing, the job will be placed in the Packaging state until the drives are shipped back to you. |
| Completed | After all drives have been shipped back to the customer, if the job has completed without errors, then the job will be set to the Completed state. The job will be automatically deleted after 90 days in the Completed state. |
| Closed | After all drives have been shipped back to the customer, if there have been any errors during the processing of the job, then the job will be set to the Closed state. The job will be automatically deleted after 90 days in the Closed state. |

The table below describes the life cycle of an individual drive as it transitions through an import or export job. The current state of each drive in a job is now visible from the Azure portal.
The following table describes each state that each drive in a job may pass through.

| Drive State | Description |
|:--- |:--- |
| Specified | For an import job, when the job is created from the Azure portal, the initial state for a drive is the Specified state. For an export job, since no drive is specified when the job is created, the initial drive state is the Received state. |
| Received | The drive transitions to the Received state when the Import/Export service operator has processed the drives that were received from the shipping company for an import job. For an export job, the initial drive state is the Received state. |
| NeverReceived | The drive will move to the NeverReceived state when the package for a job arrives but the package doesn't contain the drive. A drive can also move into this state if it has been two weeks since the service received the shipping information, but the package has not yet arrived at the data center. |
| Transferring | A drive will move to the Transferring state when the service begins to transfer data from the drive to Windows Azure Storage. |
| Completed | A drive will move to the Completed state when the service has successfully transferred all the data with no errors.
| CompletedMoreInfo | A drive will move to the CompletedMoreInfo state when the service has encountered some issues while copying data either from or to the drive. The information can include errors, warnings, or informational messages about overwriting blobs.
| ShippedBack | The drive will move to the ShippedBack state when it has been shipped from the data center back to the return address. |

This image from the Azure portal displays the drive state of an example job:

![View Drive State](./media/storage-import-export-service/drivestate.png)

The following table describes the drive failure states and the actions taken for each state.

| Drive State | Event | Resolution / Next step |
|:--- |:--- |:--- |
| NeverReceived | A drive that is marked as NeverReceived (because it was not received as part of the job's shipment) arrives in another shipment. | The operations team will move the drive to the Received state. |
| N/A | A drive that is not part of any job arrives at the data center as part of another job. | The drive will be marked as an extra drive and will be returned to the customer when the job associated with the original package is completed. |

### Time to process job
The amount of time it takes to process an import/export job varies depending on different factors such as shipping time, job type, type and size of the data being copied, and the size of the disks provided. The Import/Export service does not have an SLA but after the disks are received the service strives to complete the copy in 7 to 10 days. You can use the REST API to track the job progress more closely. There is a percent complete parameter in the List Jobs operation which gives an indication of copy progress. Reach out to us if you need an estimate to complete a time critical import/export job.

### Pricing
**Drive handling fee**

There is a drive handling fee for each drive processed as part of your import or export job. See the details on the [Azure Import/Export Pricing](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Shipping costs**

When you ship drives to Azure, you pay the shipping cost to the shipping carrier. When Microsoft returns the drives to you, the shipping cost is charged to the carrier account which you provided at the time of job creation.

**Transaction costs**

There are no transaction costs when importing data into Azure Storage. The standard egress charges are applicable when data is exported from Blob storage. For more details on transaction costs, see [Data transfer pricing.](https://azure.microsoft.com/pricing/details/data-transfers/)



## How to import data into Azure File Storage using internal SATA HDDs and SSDs?
Follow the below steps if the data on the disk is to be imported into Azure File Storage.
The first step when importing data using the Azure Import/Export service is to prepare your drives using the WAImportExport tool. Follow the steps below to prepare your drives.

1. Identify the data to be imported into Azure File Storage. This could be directories and standalone files on the local server or a network share.  
2. Determine the number of drives you will need depending on total size of the data. Procure the required number of 2.5 inch SSD or 2.5" or 3.5" SATA II or III hard disk drives.
4. Determine the directories and/or standalone files that will be copied to each hard disk drive.
5. Create the CSV files for dataset and driveset.
    
  Below is a sample dataset CSV file example for importing data as Azure Files:
  
    ```
    BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
    "F:\50M_original\100M_1.csv.txt","fileshare/100M_1.csv.txt",file,rename,"None",None
    "F:\50M_original\","fileshare/",file,rename,"None",None 
    ```
   In the above example, 100M_1.csv.txt  will be copied to the root of the "fileshare". If the "Fileshare" does not exist, one will be created. All files and folders under 50M_original will be recursively copied to fileshare. Folder structure will be maintained.

    Learn more about [preparing the dataset CSV file](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    


    **Driveset CSV File**

    The value of the driveset flag is a CSV file which contains the list of disks to which the drive letters are mapped in order for the tool to correctly pick the list of disks to be prepared. 

    Below is the example of driveset CSV file:
    
    ```
    DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
    G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
    H,Format,SilentMode,Encrypt,
    ```

    In the above example, it is assumed that two disks are attached and basic NTFS volumes with volume-letter G:\ and H:\ have been created. The tool will format and encrypt the disk which hosts H:\ and will not format or encrypt the disk hosting volume G:\.

    Learn more about [preparing the driveset CSV file](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file).

6.	Use the [WAImportExport Tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) to copy your data to one or more hard drives.
7.	You can specify "Encrypt" on Encryption field in drivset CSV to enable BitLocker encryption on the hard disk drive. Alternatively, you could also enable BitLocker encryption manually on the hard disk drive and specify "AlreadyEncrypted" and supply the key in the driveset CSV while running the tool.

8. Do not modify the data on the hard disk drives or the journal file after completing disk preparation.

> [!IMPORTANT]
> Each hard disk drive you prepare will result in a journal file. When you are creating the import job using the Azure portal, you must upload all the journal files of the drives which are part of that import job. Drives without journal files will not be processed.
> 
>

Below are the commands and examples for preparing the hard disk drive using WAImportExport tool.

WAImportExport tool PrepImport command for the first copy session to copy directories and/or files with a new copy session:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Import example 1**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

In order to **add more drives**, one can create a new driveset file and run the command as below. For subsequent copy sessions to the different disk drives than specified in InitialDriveset .csv file, specify a new driveset CSV file and provide it as a value to the parameter "AdditionalDriveSet". Use the **same journal file** name and provide a **new session ID**. The format of AdditionalDriveset CSV file is same as InitialDriveSet format.

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

**Import example 2**
```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```

In order to add additional data to the same driveset, WAImportExport tool PrepImport command can be called for subsequent copy sessions to copy additional files/directory:
For subsequent copy sessions to the same hard disk drives specified in InitialDriveset .csv file, specify the **same journal file** name and provide a **new session ID**; there is no need to provide the storage account key.

```
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

**Import example 3**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

See more details about using the WAImportExport tool in [Preparing hard drives for import](storage-import-export-tool-preparing-hard-drives-import.md).

Also, refer to the [Sample workflow to prepare hard drives for an import job](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md) for more detailed step-by-step instructions.  



## Create an export job
Create an export job to notify the Import/Export service that you'll be shipping one or more empty drives to the data center so that data can be exported from your storage account to the drives and the drives then shipped to you.

### Prepare your drives
Following pre-checks are recommended for preparing your drives for an export job:

1. Check the number of disks required using the WAImportExport tool's PreviewExport command. For more information, see [Previewing Drive Usage for an Export Job](https://msdn.microsoft.com/library/azure/dn722414.aspx). It helps you preview drive usage for the blobs you selected, based on the size of the drives you are going to use.
2. Check that you can read/write to the hard drive that will be shipped for the export job.

### Create the export job
1. To create an export job, navigate to More services -> STORAGE -> "Import/export jobs" on the Azure portal. Click **Create Import/export Job**.
2. In Step 1 Basics, select "Export from Azure", enter a string for job name, select a subscription, enter or select a resource group. Enter a descriptive name for the import job. Note that the name you enter may contain only lowercase letters, numbers, hyphens, and underscores, must start with a letter, and may not contain spaces. You will use the name you choose to track your jobs while they are in progress and once they are completed. provide contact information for the person responsible for this export job. 

3. In Step 2 Job details, select the storage account that the data will be exported from in the Storage account section. The Drop-Off location  will be automatically be populated based on the region of the storage account selected. Specify which blob data you wish to export from your storage account to your blank drive or drives. You can choose to export all blob data in the storage account, or you can specify which blobs or sets of blobs to export.
   
   To specify a blob to export, use the **Equal To** selector, and specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
   
   To specify all blobs starting with a prefix, use the **Starts With** selector, and specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name.
   
   The following table shows examples of valid blob paths:
   
   | Selector | Blob Path | Description |
   | --- | --- | --- |
   | Starts With |/ |Exports all blobs in the storage account |
   | Starts With |/$root/ |Exports all blobs in the root container |
   | Starts With |/book |Exports all blobs in any container that begins with prefix **book** |
   | Starts With |/music/ |Exports all blobs in container **music** |
   | Starts With |/music/love |Exports all blobs in container **music** that begin with prefix **love** |
   | Equal To |$root/logo.bmp |Exports blob **logo.bmp** in the root container |
   | Equal To |videos/story.mp4 |Exports blob **story.mp4** in container **videos** |
   
   You must provide the blob paths in valid formats to avoid errors during processing, as shown in this screenshot.
   
   ![Create export job - Step 3](./media/storage-import-export-service/export-job-03.png)

4. In Step 3 Return shipping info, select the carrier from the drop down list and enter a valid carrier account number that you have created with that carrier. Microsoft will use this account to ship the drives back to you once your import job is complete. Provide a complete and valid contact name, phone, email, street address, city, zip, state/proviince and country/region..
   
 5. In the Summary Page, Azure DataCenter shipping address is provided to be used for shipping disks to Azure DC. Ensure that the job name and the full address are mentioned on the shipping label. 

6. Click OK on the Summary Page to complete Import job creation

7. After shipping the disks, return to the **Import/Export** page on the Azure portal, 
     a) Navigate and click on the import job
     b) Click on **Update job status and tracking info once drives are shipped**. 
     c) Select the check box "Mark as shipped"
     d) Provide the Carrier and Tracking number.
    
   If the tracking number is not updated within 2 weeks of creating the job, the job will expire.
   
8. You can track your job progress on the portal dashboard. See what each job state in the previous section means by [Viewing your job status](#viewing-your-job-status).

   > [!NOTE]
   > If the blob to be exported is in use at the time of copying to hard drive, Azure Import/Export service will take a snapshot of the blob and copy the snapshot.
   > 
   > 
 
9. After you receive the drives with your exported data, you can view and copy the BitLocker keys generated by the service for your drive. Navigate to export job in the Azure portal and click the Import/Export tab. Select your export job from the list, and click the BitLocker Keys option. The BitLocker keys appear as shown below:
   
   ![View BitLocker keys for export job](./media/storage-import-export-service/export-job-bitlocker-keys.png)

Please go through the FAQ section below as it covers the most common questions customers encounter when using this service.

## Frequently asked questions

**Can I copy Azure File storage using the Azure Import/Export service?**

Yes, the Azure Import/Export service supports import to Azure File Storge. It does not support export of Azure Files at this time.

**Is the Azure Import/Export service available for CSP subscriptions?**

Azure Import/Export service does support CSP subscriptions.

**Can I skip the drive preparation step for an import job or can I prepare a drive without copying?**

Any drive that you want to ship for importing data must be prepared using the Azure WAImportExport tool. You must use the WAImportExport tool to copy data to the drive.

**Do I need to perform any disk preparation when creating an export job?**

No, but some pre-checks are recommended. Check the number of disks required using the WAImportExport tool's PreviewExport command. For more information, see [Previewing Drive Usage for an Export Job](https://msdn.microsoft.com/library/azure/dn722414.aspx). It helps you preview drive usage for the blobs you selected, based on the size of the drives you are going to use. Also check that you can read from and write to the hard drive that will be shipped for the export job.

**What happens if I accidentally send an HDD which does not conform to the supported requirements?**

The Azure data center will return the drive that does not conform to the supported requirements to you. If only some of the drives in the package meet the support requirements, those drives will be processed, and the drives that do not meet the requirements will be returned to you.

**Can I cancel my job?**

You can cancel a job when its status is Creating or Shipping.

**How long can I view the status of completed jobs in the Azure portal?**

You can view the status for completed jobs for up to 90 days. Completed jobs will be deleted after 90 days.

**If I want to import or export more than 10 drives, what should I do?**

One import or export job can reference only 10 drives in a single job for the Import/Export service. If you want to ship more than 10 drives, you can create multiple jobs. Drives that are associated with the same job must be shipped together in the same package.
Microsoft offers guidance and assistance when data capacity spans multiple disk import jobs. Please contact bulkimport@microsoft.com for more information

**Does the service format the drives before returning them?**

No. All drives are encrypted with BitLocker.

**Can I purchase drives for import/export jobs from Microsoft?**

No. You will need to ship your own drives for both import and export jobs.

** How can I access data that is imported by this service**

The data under your Azure storage account can be accessed via the Azure Portal or using a standalone tool called Storage Explorer. https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer 

**After the import job completes, what will my data look like in the storage account? Will my directory hierarchy be preserved?**

When preparing a hard drive for an import job, the destination is specified by the DstBlobPathOrPrefix field in the dataset CSV. This is the destination container in the storage account to which data from the hard drive is copied. Within this destination container, virtual directories are created for folders from the hard drive and blobs are created for files. 

**If the drive has files that already exist in my storage account, will the service overwrite existing blobs or files in my storage account?**

When preparing the drive, you can specify whether the destination files should be overwritten or ignored using the field in dataset CSV file called Disposition:<rename|no-overwrite|overwrite>. By default, the service will rename the new files rather than overwrite existing blobs or files.

**Is the WAImportExport tool compatible with 32-bit operating systems?**
No. The WAImportExport tool is only compatible with 64-bit Windows operating systems. Please refer to the Operating Systems section in the [pre-requisites](#pre-requisites) for a complete list of supported OS versions.

**Should I include anything other than the hard disk drive in my package?**

Please ship only your hard drives. Do not include items like power supply cables or USB cables.

**Do I have to ship my drives using FedEx or DHL?**

You can ship drives to the data center using any known carrier like FedEx, DHL, UPS, or US Postal Service. However, for shipping the drives back to you from the data center, you must provide a FedEx account number in the US and EU, or a DHL account number in the Asia and Australia regions.

**Are there any restrictions with shipping my drive internationally?**

Please note that the physical media that you are shipping may need to cross international borders. You are responsible for ensuring that your physical media and data are imported and/or exported in accordance with the applicable laws. Before shipping the physical media, check with your advisors to verify that your media and data can legally be shipped to the identified data center. This will help to ensure that it reaches Microsoft in a timely manner.

**When creating a job, the shipping address is a location that is different from my storage account location. What should I do?**

Some storage account locations are mapped to alternate shipping locations. Previously available shipping locations can also be temporarily mapped to alternate locations. Always check the shipping address provided during job creation before shipping your drives.

**When shipping my drive, the carrier asks for the data center contact address and phone number. What should I provide?**

The phone number and DC address is provided as part of job creation.

**Can I use the Azure Import/Export service to copy PST mailboxes and SharePoint data to O365?**

Please refer to [Import PST files or SharePoint data to Office 365](https://technet.microsoft.com/library/ms.o365.cc.ingestionhelp.aspx).

**Can I use the Azure Import/Export service to copy my backups offline to the Azure Backup Service?**

Please refer to [Offline Backup workflow in Azure Backup](../../backup/backup-azure-backup-import-export.md).

**What is the maximum numbers of HDD for in one shipment?**

Any number of HDDs can be in one shipment and if the disks belong to multiple jobs it is recommended to 
a) Have the disks labeled with the corresponding job names. 
b) Update the jobs with a tracking number suffixed with -1, -2 etc.
  
**What is the Maximum Block Blob and Page Blob Size supported by Disk Import/Export?**

Max Block Blob size is approximately 4.768TB  or 5,000,000 MB.
Max Page Blob size is 1TB.

**Does Disk Import/Export support AES 256 encryption?**

Azure Import/Export service by default encrypts with AES 128 bitlocker encryption but this can be increased to AES 256 by manually encrypting with bitlocker before data is copied. 

If using [WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip), below is a sample command
```
WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>] 
```
If using [WAImportExport Tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExport.zip) specify "AlreadyEncrypted" and supply the key in the driveset CSV.
```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631 |
```
## Next steps

* [Setting up the WAImportExport tool](storage-import-export-tool-how-to.md)
* [Transfer data with the AzCopy command-line utility](storage-use-azcopy.md)
* [Azure Import Export REST API sample](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

