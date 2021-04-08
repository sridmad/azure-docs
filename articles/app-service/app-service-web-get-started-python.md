---
title: Create a Python web app in Azure | Microsoft Docs
description: Deploy your first Python Hello World in Azure App Service Web Apps in minutes.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''

ms.assetid: 928ee2e5-6143-4c0c-8546-366f5a3d80ce
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/26/2017
ms.author: cephalin;cfowler
ms.custom: mvc, devcenter
---
# Create a Python web app in Azure

[Azure Web Apps](app-service-web-overview.md) provides a highly scalable, self-patching web hosting service.  This quickstart walks through how to develop and deploy a Python app to Azure Web Apps. You create the web app using the [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), and you use Git to deploy sample Python code to the web app.

![Sample app running in Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

You can follow the steps below using a Mac, Windows, or Linux machine. Once the prerequisites are installed, it takes about five minutes to complete the steps.

## Prerequisites

To complete this tutorial:

* <a href="https://git-scm.com/" target="_blank">Install Git</a>
* <a href="https://www.python.org/downloads/" target="_blank">Install Python</a>

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Download the sample

In a terminal window, run the following command to clone the sample app repository to your local machine.

```bash
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Change to the directory that contains the sample code.

```bash
cd python-docs-hello-world
```

## Run the app locally

Install the required packages using `pip`.

```bash
pip install -r requirements.txt
```

Run the application locally by opening a terminal window and using the `Python` command to launch the built-in Python web server.

```bash
python main.py
```

Open a web browser, and navigate to the sample app at `http://localhost:5000`.

You can see the **Hello World** message from the sample app displayed in the page.

![Sample app running locally](media/app-service-web-get-started-python/localhost-hello-world-in-browser.png)

In your terminal window, press **Ctrl+C** to exit the web server.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## Create a web app

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-python-no-h.md)]

Browse to your newly created web app. Replace _&lt;app name>_ with a unique app name.

```bash
http://<app name>.azurewebsites.net
```

![Empty web app page](media/app-service-web-get-started-python/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)]

```bash
Counting objects: 18, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (16/16), done.
Writing objects: 100% (18/18), 4.31 KiB | 0 bytes/s, done.
Total 18 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '44e74fe7dd'.
remote: Generating deployment script.
remote: Generating deployment script for python Web Site
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling python deployment.
remote: KuduSync.NET from: 'D:\home\site\repository' to: 'D:\home\site\wwwroot'
remote: Deleting file: 'hostingstart.html'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'main.py'
remote: Copying file: 'README.md'
remote: Copying file: 'requirements.txt'
remote: Copying file: 'virtualenv_proxy.py'
remote: Copying file: 'web.2.7.config'
remote: Copying file: 'web.3.4.config'
remote: Detected requirements.txt.  You can skip Python specific steps with a .skipPythonDeployment file.
remote: Detecting Python runtime from site configuration
remote: Detected python-3.4
remote: Creating python-3.4 virtual environment.
remote: .................................
remote: Pip install requirements.
remote: Successfully installed Flask click itsdangerous Jinja2 Werkzeug MarkupSafe
remote: Cleaning up...
remote: .
remote: Overwriting web.config with web.3.4.config
remote:         1 file(s) copied.
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
```

## Browse to the app

Browse to the deployed application using your web browser.

```bash
http://<app_name>.azurewebsites.net
```

The Python sample code is running in an Azure App Service web app.

![Sample app running in Azure](media/app-service-web-get-started-python/hello-world-in-browser.png)

**Congratulations!** You've deployed your first Python app to App Service.

## Update and redeploy the code

Using a local text editor, open the `main.py` file in the Python app, and make a small change to the text next to the `return` statement:

```python
return 'Hello, Azure!'
```

In the local terminal window, commit your changes in Git, and then push the code changes to Azure.

```bash
git commit -am "updated output"
git push azure master
```

Once deployment has completed, switch back to the browser window that opened in the [Browse to the app](#browse-to-the-app) step, and refresh the page.

![Updated sample app running in Azure](media/app-service-web-get-started-python/hello-azure-in-browser.png)

## Manage your new Azure web app

Go to the <a href="https://portal.azure.com" target="_blank">Azure portal</a> to manage the web app you created.

From the left menu, click **App Services**, and then click the name of your Azure web app.

![Portal navigation to Azure web app](./media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-list.png)

You see your web app's Overview page. Here, you can perform basic management tasks like browse, stop, start, restart, and delete.

![App Service blade in Azure portal](media/app-service-web-get-started-nodejs-poc/nodejs-docs-hello-world-app-service-detail.png)

The left menu provides different pages for configuring your app.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## Next steps

> [!div class="nextstepaction"]
> [Map an existing custom DNS name to Azure Web Apps](app-service-web-tutorial-custom-domain.md)
