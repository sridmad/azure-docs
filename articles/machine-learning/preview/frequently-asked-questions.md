---
title: Azure Machine Learning 2017 Preview FAQ | Microsoft Docs
description: This article contains commonly asked questions and answers for Azure Machine Learning preview features
services: machine-learning
author: serinakaye
ms.author: serinak
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 08/30/2017 
---
# Azure Machine Learning frequently asked questions

## General product questions

**What is Azure Machine Learning?**

Azure Machine Learning is a fully managed Azure service that allows you to create, test, manage, and deploy machine learning and AI models. Our new services and downloadable application offer a code-first approach that leverages the cloud, on-premises, and edge to provide the train, deploy, manage, and monitor models with power, speed, and flexibility. Alternatively, Azure Machine Learning Studio offers a browser-based, visual drag-and-drop authoring environment where no coding is required. 

**How do I get started with the preview?**

The easiest way to get started is to sign up for the Experimentation Service in Azure. From the portal, you can also download our free Workbench application or sign up for a Model Management account. In addition, you can visit our documentation site for How-To articles, videos, demo code and more. 

**I do not have an Azure subscription. Can I still try the new services?**

As part of the Microsoft Azure portfolio, our new services require an Azure subscription. Additionally,  you must have adequate permissions to create assets such as Resource Groups, Virtual Machines, etc. 

**In which regions are the new services be available?**

Look for supported Azure regions for experimentation and model management service under Azure Machine Learning on the [Azure Products by region](https://azure.microsoft.com/regions/services/) page.

More regions will be added as we develop the product.  You can help us prioritize which regions to deploy to on our [Azure Machine Learning Feedback](https://feedback.azure.com/forums/257792-machine-learning) site. 

**What other Azure services are required?**

To fully experience all capabilities of Azure Machine Learning, Azure Machine Learnign will use Visual Studio VSTS account, Azure Blob Storage, Azure Container Registry, Data Science VM or HDInsight compute, and Azure Container Service.

**How does Azure Machine Learning relate to Microsoft Machine Learning Services in SQL Server 2017?**	

Machine Learning Services in SQL Server 2017 is an extensible, scalable platform for integrating machine learning tasks into database workflows. It is a perfect fit for scenarios where an on-premise solution is required, for example where data movement is expensive or untenable. In contrast, cloud or hybrid workloads are a great fit for our new Azure services. 

**Do you support both Python and R? What about other programming languages like C++**

We currently support Python only. We are working on R integration and expect to have it available soon. 

**How does Azure Machine Learning relate to Microsoft Machine Learning for Spark?**

A: MMLSpark provides deep learning and data science tools for Apache Spark, with emphasis on productivity, ease of experimentation and state-of-the-art algorithms. MMLSpark offers integration of Spark Machine Learning pipelines with the Microsoft Cognitive Toolkit and OpenCV. You can create powerful, highly scalable predictive, and analytical models for image and text data. MMLSpark is available under an open-source license and is included in AML Workbench as a set of consumable models and algorithms. For more information on MMLSpark, visit our product documentation. 

**Which versions of Spark are supported by the new tools and services? Top section**

Workbench currently includes and supports MMLSpark version 0.8, which is compatible with Apache Spark 2.1. You also have an option to use GPU-enabled Docker image of MMLSpark 0.8 on Linux virtual machines.

## Experimentation Service

**What is the Azure Machine Learning Experimentation Service?**

The Experimentation Service is a managed Azure service that takes machine learning experimentation to the next level. Experiments can be built locally or in the cloud. Rapidly prototype on a desktop, then scale to virtual machines or Spark clusters. Azure VMs with the latest GPU technology allow you to engage in deep learning quickly and effectively. We’ve also included deep integration with Git so you can plug easily into existing workflows for code tracking, configuration, and collaboration. 

**How will I be charged for the Experimentation Service?**

The first two users associated with your Azure Machine Learning Experimentation Service are free. Additional users will be charged at the Public Preview rate of $50 / month. For more information on pricing and billing, visit our Pricing page.

**Will I be charged based on how many experiments I run?**

No, the Experimentation Service allows as many experiments as you need and charges only based on the number of users. Experimentation compute resources are charged separately. We encourage you to perform multiple experiments so you can find the best fitting model for your solution.   

**What specific kinds of compute and storage resources can I use?**

The Experimentation service can execute your experiments on local machines (direct or Docker-based), [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), and [HDInsight](https://azure.microsoft.com/services/hdinsight/). The service also accesses an [Azure Storage](https://azure.microsoft.com/services/storage/) account for storing execution outputs and can leverage a [Visual Studio Team Service](https://azure.microsoft.com/services/visual-studio-team-services/) account for version-control and Git storage. Note that you will be billed independently for any consumed compute and storage resources, based upon their individual pricing.  


## Model Management

**What is Azure Machine Learning Model Management?**

Azure Machine Learning Model Management is a managed Azure service that allows data scientists and dev-ops teams to deploy predictive models reliably into a wide variety of environments. Git repositories and Docker containers provide traceability and repeatability. Models can be deployed reliably in the cloud, on-premises, or edge. Once in production, you can manage model performance, then proactively retrain if performance degrades. You can deploy models on local machines, to [Azure VMs](https://azure.microsoft.com/services/virtual-machines/), Spark on [HDInsight](https://azure.microsoft.com/services/hdinsight/) or Kubernetes-orchestrated [Azure Container Service](https://azure.microsoft.com/services/container-service/) clusters.  

**What is a "model"?**

A model is the output of an experimentation run that has been promoted for model management. A model that is registered in the hosting account is counted against your plan, including models updated through retraining or version iteration.

**What is a "managed model?"**

A model is the output of a training process and is the application of a machine learning algorithm to training data. Model Management enables you to deploy models as web services, manage various versions of your models, and monitor their performance and metrics. "Managed" models have been registered with an Azure Machine Learning Model Management account. As an example, consider a scenario where you are trying to forecast sales. During the experimentation phase, you generate many models by using different data sets or algorithms. You have generated four models with varying accuracies but choose to register only the model with the highest accuracy. The model that is registered becomes your first managed model.
 
**What is a "deployment?"**

Model Management allows you to deploy models as packaged web service containers in Azure. These web services can be invoked using REST APIs. Each web service is counted as a single deployment, and the total number of active deployments are counted towards your plan. Using the sales forecasting example, when you deploy your best performing model, your plan is incremented by one deployment. If you then retrain and deploy another version, you have two deployments. If you determine that the newer model is better, and delete the original, your deployment count is decremented by one.  

**What specific compute resources are available for my deployments?** 

Model Management can run your deployments as Docker containers registered to [Azure Container Service](https://azure.microsoft.com/services/container-service/), as [Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), or on local machines. Additional deployment targets will be added shortly. Note that you will be billed independently for any consumed compute resources, based upon their individual pricing.     

**Can I use the Azure Machine Learning Model Management to deploy models built using tools other than the Experimentation Service?**

You get the best experience when you deploy models created using the Experimentation Service. However, you can deploy models built using other frameworks and tools. We support a variety of models including  MMLSpark, TensorFlow, Microsoft Cognitive Toolkit, scikit-learn, Keras, etc. 

**Can I use my own Azure resources?**

Yes, the Experimentation Service and Model Management work in conjunction with multiple Azure data stores, compute workloads, and other services. Refer to our technical documentation for further details on required Azure services.

**Do you support both on-premises and cloud deployment scenarios?**

Yes. We support on-premises and cloud deployment scenarios via Docker containers. Local execution targets include: single-node Docker deployments, [Microsoft SQL Server with ML Services](https://docs.microsoft.com/sql/advanced-analytics/r/r-services), Hadoop, or Spark. We also support cloud deployments via Docker, including: clustered deployments via Azure Container Service and Kubernetes, HDInsight, or Spark clusters. Edge scenarios are supported via Docker containers and Azure IOT Edge. 

**Can I run a Docker image that was created using the Azure Machine Learning CLI on another host?**

Yes. You can use the image as a web service on any docker host as long as host has sufficient compute resources for hosting the docker image.

**Do you support retraining of deployed models?**

Yes, you can deploy multiple versions of the same model. Model Management will support  service updates for all updated models and images.

## Workbench

**What is the Azure Machine Learning Workbench?**

The Azure Machine Learning Workbench is a companion application built for professional data scientists. Available for Windows and Mac, the Machine Learning Workbench provides overview, management, and control for machine learning solutions. The Machine Learning Workbench includes access to cutting edge AI frameworks from both Microsoft and the open source community. We've included the most popular data science toolkits, including TensorFlow, Microsoft Cognitive Toolkit, Spark ML, scikit-learn and more. We’ve also enabled integration with popular data science IDEs such as Jupyter notebooks, PyCharm, and Visual Studio Code. The Machine Learning Workbench has built-in data preparation capabilities to rapidly sample, understand, and prepare data, whether structured or unstructured. Our new data preparation tool, called [PROSE](https://microsoft.github.io/prose/), is built on cutting-edge technology from Microsoft Research.  

**Is Workbench an IDE?**

No. The Machine Learning Workbench has been designed as a companion to popular IDEs such as Jupyter Notebooks, Visual Studio Code, and PyCharm but it is not a fully functional IDE. The Machine Learning Workbench offers some basic text editing capabilities, but debugging, intellisense and other commonly used IDE capabilities are not supported. We recommend that you use your favorite IDE for code development, editing and debugging. 

**Is there a charge for using the Azure Machine Learning Workbench?**

No. Azure Machine Learning Workbench is a free application. You can download it on as many machines, and for as many users, as you need. In order to use the Azure Machine Learning Workbench, you must have an Experimentation account. .  

**Do you support command-line capabilities?**

A: Yes, Azure Machine Learning offers a full CLI interface. The Machine Learning CLI is installed by default with the Azure Machine Learning Workbench. It is also provided as part of the Linux Data Science virtual machine on Azure and will be integrated into the [Azure CLI](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)


**Can I use Jupyter Notebooks with Workbench?**

Yes! You can run Jupyter notebooks in Workbench, with Workbench as the client hosting application, just as you would use a browser as a client. 

**Which Jupyter Notebook kernels are supported?**

A: The current version of Jupyter included with Workbench launches a Python 3 kernel, and an additional kernel for each "runconfig" file in your aml_config folder. Supported configurations include:
- Local Python
- Python in local or remote Docker

## Data formats and capabilities

**Which file formats are currently supported for data ingestion in Workbench?**

The data preparation tools in Workbench currently support ingestion from the following formats: 
- Delimited files such as CSV, TSV, etc.  
- Fixed width files
- Plain text files
- Excel (.xls/xlsx)
- JSON files
- Parquet files 
- Custom files (scripts)
If your solution requires data ingestion from additional sources, Python code can be used to... 

**Which data storage locations are currently supported?**

For public preview, Workbench supports data ingestion from: 
- Local hard drive or mapped network storage location
- Azure BLOB or Azure Storage (requires an Azure subscription)
- Azure SQL Server
- Microsoft SQL Server


**What kinds of data wrangling, preparation, and transformations are available?**

For public preview, Workbench supports “Derive Column by Example”, “Split Column by Example”, “Text Clustering”, “Handle Missing Values” and many others.  Workbench also supports data type conversion, data aggregation (COUNT, MEAN, VARIANCE, etc.), and complex data joins. For a full list of supported capabilities, visit our product documentation. 

**Are there any data size limits enforced by Azure Machine Learning Workbench, Experimentation, or Model Management?**

A: No, the new services do not impose any data limitations. However, there are limitations introduced by the environment in which you are performing your data preparation, model training, experimentation, or deployment. For example, if you are targeting a local environment for training, you are limited by the available space in your hard drive. Alternatively, if you are targeting HDInsight, you are limited by any associated size or compute restraints. 

## Algorithms and libraries

**What algorithms are supported in Azure Machine Learning Workbench?**

Our preview products and services include the best of the open source community. We support a wide range of algorithms and libraries including TensorFlow, scikit-learn, Apache Spark, and the Microsoft Cognitive Toolkit. The Azure Machine Learning Workbench also packages the [Microsoft revoscalepy](https://docs.microsoft.com/sql/advanced-analytics/python/what-is-revoscalepy) package.

**How does Azure Machine Learning relate to the Microsoft Cognitive Toolkit?**

The [Microsoft Cognitive Toolkit](https://www.microsoft.com/cognitive-toolkit/) is one of many frameworks supported by our new tools and services. The Cognitive Toolkit is a unified deep-learning toolkit that allows you to consume and combine popular machine learning models including Feed-Forward Deep Neural Networks, Convolutional Nets, Sequence-to-Sequence, and Recurrent Networks. For more information on Microsoft Cognitive Toolkit, visit our [product documentation](https://docs.microsoft.com/cognitive-toolkit/). 

## Pricing and billing

**Are you charging for Azure Machine Learning during preview?**	

A: The Azure Machine Learning Workbench applicaiton is available free of charge to Azure subscribers. The Experimentation Service and Model Management offer free tiers, in addition to paid tiers, avialable at a discount during Public Preview.

**Will I be charged based on how many experiments I run?**

No, the Experimentation Service allows as many experiments as you need, and charges only based on the number of users. Experimentation compute resources are charged separately.  We encourage you to perform multiple experiments to arrive at the best model for your solution. 

**Will I be charged based on how many times my web services is called?**

No. Web services can be called as often as required, without any Model Management billing implications. You have full control to scale your deployments to meet the needs of your applications.

**How can I scale the number of units I have purchased in the Azure Machine Learning Model Management?**

You can change the number of units, up or down, using either the Azure portal or the CLI. 

**What will my bill look like?**

Bills are produced daily. For billing purposes, a day commences at midnight UTC. Bills are generated monthly. Separate charges are incurred for any Azure services consumed in conjunction with Azure Machine Learning. Charges can include, but are not limited to: 
- Compute charges
- HDInsight
- Azure Container Service
- Azure Container Registry 
- Azure Blob Storage
- Application Insights
- Azure Key Vault
- Visual Studio Team Services
- Azure Event Hub
- Azure Stream Analytics 
For further details, or to view a sample bill, visit our pricing page. 

## Support and training

**Where can I get training for Azure Machine Learning?**

The [Azure Machine Learning Documentation Center](./overview-what-is-azure-ml.md) hosts video tutorials and how-to guides. These step-by-step guides introduce the services and explain the data science life cycle. We add new material to the Machine Learning Center on an ongoing basis. You can submit requests for additional learning material at the user feedback forum.

**How do I get support for Azure Machine Learning?**

For technical support, go to Azure Support, and select Machine Learning. Azure Machine Learning also has an active community on [StackOverflow](https://stackoverflow.com/questions/tagged/azure-machine-learning) tagged with "Azure-Machine-Learning", monitored by the team.  Suggestions and feature requests can be submitted at the [Azure Machine Learning Feedback](https://feedback.azure.com/forums/257792-machine-learning) site. 
