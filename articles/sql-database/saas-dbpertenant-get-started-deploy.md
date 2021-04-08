---
title: Multi-tenant SaaS tutorial - Azure SQL Database | Microsoft Docs 
description: "Deploy and explore the Wingtip Tickets SaaS multi-tenant application, that demonstrates the Database per Tenant and other SaaS patterns using Azure SQL Database."
keywords: sql database tutorial
services: sql-database
documentationcenter: ''
author: stevestein
manager: craigg
editor: ''

ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: "Inactive"
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: sstein

---
# Deploy and explore a multi-tenant SaaS application that uses the database per tenant pattern with Azure SQL Database

In this tutorial, you deploy and explore the Wingtip Tickets SaaS Database per Tenant application. The application uses a database-per-tenant pattern, to store the data of multiple tenants. The application is designed to showcase features of Azure SQL Database that simplify enabling SaaS scenarios.

Five minutes after clicking the *Deploy to Azure* button below, you have a multi-tenant SaaS application, using SQL Database, up and running in the cloud. The application is deployed with three sample tenants, each with its own database, all deployed into a SQL elastic pool. The app is deployed to your Azure subscription, giving you full access to explore and work with the individual application components. The application source code and management scripts are available in the WingtipTicketsSaaS-DbPerTenant GitHub repo.


In this tutorial you learn:

> [!div class="checklist"]

> * How to deploy the Wingtip SaaS application
> * Where to get the application source code, and management scripts
> * About the servers, pools, and databases that make up the app
> * How tenants are mapped to their data with the *catalog*
> * How to provision a new tenant
> * How to monitor tenant activity in the app

To explore various SaaS design and management patterns, a [series of related tutorials](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) is available that build upon this initial deployment. While going through the tutorials, dive into the provided scripts, and examine how the different SaaS patterns are implemented. Step through the scripts in each tutorial to gain a deeper understanding how to implement the many SQL Database features that simplify developing SaaS applications.

## Prerequisites

To complete this tutorial, make sure the following prerequisites are completed:

* Azure PowerShell is installed. For details, see [Getting started with Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## Deploy the Wingtip Tickets SaaS application

Deploy the app:

1. Clicking the **Deploy to Azure** button opens the Wingtip Tickets SaaS Database per Tenant deployment template in the Azure portal. The template requires two parameter values; a name for a new resource group, and a user name that distinguishes this deployment from other deployments of the Wingtip Tickets SaaS Database per Tenant app. The next step provides details for setting these values.

   Make sure to note the exact values that you use, because you will need to enter them into a configuration file later.

   <a href="https://aka.ms/deploywingtipdpt" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Enter required parameter values for the deployment:

    > [!IMPORTANT]
    > Some authentication, and server firewalls are intentionally unsecured for demonstration purposes. **Create a new resource group**. Do not use existing resource groups, servers, or pools. Do not use this application, scripts, or any deployed resources for production. Delete this resource group when you are finished with the application to stop related billing.

    * **Resource group** - Select **Create new** and provide a **Name** for the resource group. 
    * **Location** - Select a **Location** from the drop-down list.
    * **User** - Some resources require names that are globally unique. To ensure uniqueness, each time you deploy the application provide a value to differentiate resources you create, from resources created by any other deployment of the Wingtip application. It’s recommended to use a short **User** name, such as your initials plus a number (for example, *af1*), and then use that in the resource group name (for example, *wingtip-af1*). The **User** parameter can only contain letters, numbers, and hyphens (no spaces). The first and last character must be a letter or a number (all lowercase is recommended).


1. **Deploy the application**.

    * Click to agree to the terms and conditions.
    * Click **Purchase**.

1. Monitor deployment status by clicking **Notifications** (the bell icon right of the search box). Deploying the Wingtip Tickets SaaS app takes approximately five minutes.

   ![deployment succeeded](media/saas-dbpertenant-get-started-deploy/succeeded.png)

## Download and unblock the Wingtip Tickets management scripts

While the application is deploying, download the source code and management scripts.

> [!IMPORTANT]
> Executable contents (scripts, dlls) may be blocked by Windows when zip files are downloaded from an external source and extracted. When extracting the scripts from a zip file, follow the steps below to unblock the .zip file before extracting. This ensures the scripts are allowed to run.

1. Browse to the [WingtipTicketsSaaS-DbPerTenant GitHub repo](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
1. Click **Clone or download**.
1. Click **Download ZIP** and save the file.
1. Right-click the **WingtipTicketsSaaS-DbPerTenant-master.zip** file, and select **Properties**.
1. On the **General** tab, select **Unblock**, and click **Apply**.
1. Click **OK**.
1. Extract the files.

Scripts are located in the *..\\WingtipTicketsSaaS-DbPerTenant-master\\Learning Modules* folder.

## Update the user configuration file for this deployment

Before running any scripts, update the *resource group* and *user* values in **UserConfig.psm1**. Set these variables to the values you used during deployment.

1. In the *PowerShell ISE*, open ...\\Learning Modules\\*UserConfig.psm1* 
1. Update *ResourceGroupName* and *Name* with the specific values for your deployment (on lines 10 and 11 only).
1. Save the changes!

These values are referenced in nearly every script.

## Run the application

The app targets venues, such as concert halls, jazz clubs, and sports clubs, that host events. Venues register as customers (or tenants) of Wingtip Tickets, for an easy way to list events and sell tickets. Each venue gets a personalized web site to manage and list their events and sell tickets, independent and isolated from other tenants. Under the covers, each tenant gets a SQL database deployed into a SQL elastic pool.

A central **Events Hub** page provides a list of links to the tenants in your deployment.

1. Open the _Events Hub_ in your web browser: http://events.wingtip-dpt.&lt;USER&gt;.trafficmanager.net (substitute &lt;USER&gt; with your deployment's user value):

    ![events hub](media/saas-dbpertenant-get-started-deploy/events-hub.png)

1. Click **Fabrikam Jazz Club** in the *Events Hub*.

   ![Events](./media/saas-dbpertenant-get-started-deploy/fabrikam.png)


To control the distribution of incoming requests, the app uses [*Azure Traffic Manager*](../traffic-manager/traffic-manager-overview.md). The events pages, which are tenant-specific, require that tenant names are included in the URLs. All the tenant URLs include your specific *User* value and follow this format: http://events.wingtipp-dpt.&lt;USER&gt;.trafficmanager.net/*fabrikamjazzclub*. The events app parses the tenant name from the URL and uses it to create a key to access a catalog implemented using [*shard map management*](sql-database-elastic-scale-shard-map-management.md). The catalog maps the key to the tenant's database location. The **Events Hub** uses extended metadata in the catalog to retrieve the tenant’s name associated with each database to provide the list of URLs.

In a production environment, you would typically create a CNAME DNS record to [*point a company internet domain*](../traffic-manager/traffic-manager-point-internet-domain.md) to the traffic manager profile.

## Start generating load on the tenant databases

Now that the app is deployed, let’s put it to work! The *Demo-LoadGenerator* PowerShell script starts a workload running against all tenant databases. The real-world load on many SaaS apps is typically sporadic and unpredictable. To simulate this type of load, the generator produces a load distributed across all tenants, with randomized bursts on each tenant occurring at randomized intervals. Because of this it takes several minutes for the load pattern to emerge, so it’s best to let the generator run for at least three or four minutes before monitoring the load.

1. In the *PowerShell ISE*, open the ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.ps1* script.
1. Press **F5** to run the script and start the load generator (leave the default parameter values for now).

> [!IMPORTANT]
> To run other scripts, open a new PowerShell ISE window. The load generator is running as a series of jobs in your local PowerShell session. The *Demo-LoadGenerator.ps1* script kicks off the actual load generator script, which runs as a foreground task plus a series of background load-generation jobs. A load-generator job is invoked for each database registered in the catalog. The jobs are running in your local PowerShell session, so closing the PowerShell session stops all jobs. If you suspend your machine, load generation is paused and will resume when you wake up your machine.

Once the load generator invokes load-generation jobs for each tenant, the foreground task remains in a job-invoking state, where it starts additional background jobs for any new tenants that are subsequently provisioned. You can use *Ctrl-C* or press the *Stop* button to stop the foreground task, but existing background jobs will continue generating load on each database. If you need to monitor and control the background jobs, use *Get-Job*, *Receive-Job* and *Stop-Job*. While the foreground task is running you cannot use the same PowerShell session to execute other scripts. To run other scripts, open a new PowerShell ISE window.

If you want to restart the load generator session, for example with different parameters, you can stop the foreground task and then re-run the *Demo-LoadGenerator.ps1* script. Re-running *Demo-LoadGenerator.ps1* first stops any currently running jobs, and then restarts the generator, which kicks off a new set of jobs using the current parameters.

For now, leave the load generator running in the job-invoking state.


## Provision a new tenant

The initial deployment creates three sample tenants, but let’s create another tenant to see how this impacts the deployed application. The Wingtip Tickets SaaS provisioning-tenants workflow is detailed in the [Provision and catalog tutorial](saas-dbpertenant-provision-and-catalog.md). In this step, you quickly create a new tenant.

1. In the *PowerShell ISE*, open ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* .
1. Press **F5** to run the script (leave the default values for now).

   > [!NOTE]
   > Many Wingtip SaaS scripts use *$PSScriptRoot* to navigate folders to call functions in other scripts. This variable is only evaluated when the full script is executed by pressing **F5**.  Highlighting and running a selection (**F8**) can result in errors, so press **F5** when running scripts.

The new tenant database is created in a SQL elastic pool, initialized and registered in the catalog. After successful provisioning, the new tenant's *Events* site appears in your browser:

![New tenant](./media/saas-dbpertenant-get-started-deploy/red-maple-racing.png)

Refresh the *Events Hub* and the new tenant now appears in the list.


## Explore the servers, pools, and tenant databases

Now that you've started running a load against the collection of tenants, let’s look at some of the resources that were deployed:

1. In the [Azure portal](http://portal.azure.com), browse to your list of SQL servers and open the **catalog-dpt-&lt;USER&gt;** server. The catalog server contains two databases, the **tenantcatalog** and the **basetenantdb** (a template database that is copied to create new tenants).

   ![databases](./media/saas-dbpertenant-get-started-deploy/databases.png)

1. Go back to your list of SQL servers and open the **tenants1-dpt-&lt;USER&gt;** server that holds the tenant databases. Each tenant database is an _Elastic Standard_ database in a 50 eDTU standard pool. Also notice there is a _Red Maple Racing_ database, the tenant database you provisioned previously.

   ![server](./media/saas-dbpertenant-get-started-deploy/server.png)

## Monitor the pool

If the load generator has been running for several minutes, enough data should be available to start looking at some of the monitoring capabilities built into pools and databases.

1. Browse to the server **tenants1-dpt-&lt;USER&gt;**, and click **Pool1** to view resource utilization for the pool (the load generator ran for an hour in the following charts):

   ![monitor pool](./media/saas-dbpertenant-get-started-deploy/monitor-pool.png)

The upper chart shows pool eDTU utilization, while the bottom chart shows eDTU utiization of the top 5 databases in the pool.  What these two charts nicely illustrate, is how well suited elastic pools and SQL Database are for SaaS application workloads. Four databases that are each bursting to as much as 40 eDTUs are easily being supported in a 50 eDTU pool. If they were provisioned as standalone databases, they would each need to be an S2 (50 DTU) to support the bursts. The cost of 4 standalone S2 databases would be nearly 3 times the price of the pool, and the pool still has plenty of headroom for many more databases. In real-world situations, SQL Database customers are currently running up to 500 databases in 200 eDTU pools. For more information, see the [performance monitoring tutorial](saas-dbpertenant-performance-monitoring.md).


## Next steps

In this tutorial you learned:

> [!div class="checklist"]

> * How to deploy the Wingtip Tickets SaaS application
> * About the servers, pools, and databases that make up the app
> * Tenants are mapped to their data with the *catalog*
> * How to provision new tenants
> * How to view pool utilization to monitor tenant activity
> * How to delete sample resources to stop related billing

Now try the [Provision and catalog tutorial](saas-dbpertenant-provision-and-catalog.md).



## Additional resources

* Additional [tutorials that build on the Wingtip Tickets SaaS Database per Tenant application](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* To learn about elastic pools, see [*What is an Azure SQL elastic pool*](sql-database-elastic-pool.md)
* To learn about elastic jobs, see [*Managing scaled-out cloud databases*](sql-database-elastic-jobs-overview.md)
* To learn about multi-tenant SaaS applications, see [*Design patterns for multi-tenant SaaS applications*](saas-tenancy-app-design-patterns.md)
