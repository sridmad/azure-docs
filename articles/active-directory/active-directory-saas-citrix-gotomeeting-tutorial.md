---
title: 'Tutorial: Azure Active Directory integration with GoToMeeting | Microsoft Docs'
description: Learn how to configure single sign-on between Azure Active Directory and GoToMeeting.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore

ms.assetid: bcaf19f2-5809-4e1c-acbc-21a8d3498ccf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: jeedes

---
# Tutorial: Azure Active Directory integration with GoToMeeting

In this tutorial, you learn how to integrate GoToMeeting with Azure Active Directory (Azure AD).

Integrating GoToMeeting with Azure AD provides you with the following benefits:

- You can control in Azure AD who has access to GoToMeeting.
- You can enable your users to automatically get signed-on to GoToMeeting (Single Sign-On) with their Azure AD accounts.
- You can manage your accounts in one central location - the Azure portal.

If you want to know more details about SaaS app integration with Azure AD, see [what is application access and single sign-on with Azure Active Directory](active-directory-appssoaccess-whatis.md).

## Prerequisites

To configure Azure AD integration with GoToMeeting, you need the following items:

- An Azure AD subscription
- A GoToMeeting single sign-on enabled subscription

> [!NOTE]
> To test the steps in this tutorial, we do not recommend using a production environment.

To test the steps in this tutorial, you should follow these recommendations:

- Do not use your production environment, unless it is necessary.
- If you don't have an Azure AD trial environment, you can [get a one-month trial](https://azure.microsoft.com/pricing/free-trial/).

## Scenario description
In this tutorial, you test Azure AD single sign-on in a test environment. 
The scenario outlined in this tutorial consists of two main building blocks:

1. Adding GoToMeeting from the gallery
2. Configuring and testing Azure AD single sign-on

## Adding GoToMeeting from the gallery
To configure the integration of GoToMeeting into Azure AD, you need to add GoToMeeting from the gallery to your list of managed SaaS apps.

**To add GoToMeeting from the gallery, perform the following steps:**

1. In the **[Azure portal](https://portal.azure.com)**, on the left navigation panel, click **Azure Active Directory** icon. 

	![The Azure Active Directory button][1]

2. Navigate to **Enterprise applications**. Then go to **All applications**.

	![The Enterprise applications blade][2]
	
3. To add new application, click **New application** button on the top of dialog.

	![The New application button][3]

4. In the search box, type **GoToMeeting**, select **GoToMeeting** from result panel then click **Add** button to add the application.

	![GoToMeeting in the results list](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_addfromgallery.png)

## Configure and test Azure AD single sign-on

In this section, you configure and test Azure AD single sign-on with GoToMeeting based on a test user called "Britta Simon".

For single sign-on to work, Azure AD needs to know what the counterpart user in GoToMeeting is to a user in Azure AD. In other words, a link relationship between an Azure AD user and the related user in GoToMeeting needs to be established.

In GoToMeeting, assign the value of the **user name** in Azure AD as the value of the **Username** to establish the link relationship.

To configure and test Azure AD single sign-on with GoToMeeting, you need to complete the following building blocks:

1. **[Configure Azure AD Single Sign-On](#configure-azure-ad-single-sign-on)** - to enable your users to use this feature.
2. **[Create an Azure AD test user](#create-an-azure-ad-test-user)** - to test Azure AD single sign-on with Britta Simon.
3. **[Create a GoToMeeting test user](#create-a-gotomeeting-test-user)** - to have a counterpart of Britta Simon in GoToMeeting that is linked to the Azure AD representation of user.
4. **[Assign the Azure AD test user](#assign-the-azure-ad-test-user)** - to enable Britta Simon to use Azure AD single sign-on.
5. **[Test single sign-on](#test-single-sign-on)** - to verify whether the configuration works.

### Configure Azure AD single sign-on

In this section, you enable Azure AD single sign-on in the Azure portal and configure single sign-on in your GoToMeeting application.

**To configure Azure AD single sign-on with GoToMeeting, perform the following steps:**

1. In the Azure portal, on the **GoToMeeting** application integration page, click **Single sign-on**.

	![Configure single sign-on link][4]

2. On the **Single sign-on** dialog, select **Mode** as	**SAML-based Sign-on** to enable single sign-on.
 
	![Single sign-on dialog box](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_samlbase.png)

3. On the **GoToMeeting Domain and URLs** section, click **Show advanced URL settings** and perform following actions -

	![GoToMeeting Domain and URLs single sign-on information](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_ssourl.png)

    a. In the **Identifier** textbox, type the URL:
	`https://authentication.logmeininc.com/saml/sp`

	b. In the **Reply URL** textbox, type the URL:	`https://authentication.logmeininc.com/saml/acs`

	c. In the **Relay State** textbox, type one of the following URLs:

	**For GoToMeeting**: `https://global.gotomeeting.com`
	
	**For GoToTraining**: `https://global.gototraining.com`

	**For GoToWebinar**: `https://global.gotowebinar.com`

	**For GoToAssist**: `https://app.gotoassist.com`


4. On the **SAML Signing Certificate** section, click **Metadata XML** and then save the metadata file on your computer.

	![The Certificate download link](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_certificate.png) 

5. Click **Save** button.

	![Configure Single Sign-On Save button](./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_400.png)

6. To generate the **Metadata** url, perform the following steps:

    a. Click **App registrations**.
    
    ![Configure Single Sign-On](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appregistrations.png)
   
    b. Click **Endpoints** to open **Endpoints** dialog box.  
    
    ![Configure Single Sign-On](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpointicon.png)

    c. Click the copy button to copy **FEDERATION METADATA DOCUMENT** url and paste it into notepad.
    
    ![Configure Single Sign-On](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_endpoint.png)
     
    d. Now go to the property page of **GoToMeeting** and copy the **Application Id** using **Copy** button and paste it into notepad.
 
    ![Configure Single Sign-On](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_appid.png)

    e. Generate the **Metadata URL** using the following pattern: `<FEDERATION METADATA DOCUMENT url>?appid=<application id>`	

7. On the **GoToMeeting Configuration** section, click **Configure GoToMeeting** to open **Configure sign-on** window. Copy the **Sign-Out URL, SAML Entity ID, and SAML Single Sign-On Service URL** from the **Quick Reference section.**

	![GoToMeeting Configuration](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_configure.png) 

8. In a different browser window, log in to your [GoToMeeting Organization Center](https://account.citrixonline.com/organization/administration/)

9. Under **identity provider** tab, you can configure the Azure settings either by providing the generated **Metadata URL** or the downloaded **Metadata file** or **Manual**.

10. For **Metadata URL** perform the following steps:

	![GoToMeeting Configuration](./media/active-directory-saas-gotomeeting-tutorial/config1.png)

	a. In the **How would you like to configure your SAML IDP?**, Select **Automatic** from the dropdown.

	b. Paste the **Metadata URL**, which you have generated in the previous steps into the  **Metadata URL** textbox.

	c. Click **Save**.

11. For **Metadata file** perform the following steps:

	![GoToMeeting Configuration](./media/active-directory-saas-gotomeeting-tutorial/config2.png)

	a. In the **How would you like to configure your SAML IDP?**, Select **Upload SAML metadata file** from the dropdown.

	b. To upload your downloaded metadata file, click **Upload metadata file**.

	c. Click **Save**.

12. For **Manual** perform the following steps:

	![GoToMeeting Configuration](./media/active-directory-saas-gotomeeting-tutorial/config3.png)

	a.  In **Sign-in page URL** textbox, paste the value of **SAML Single Sign-On Service URL** which you have copied from Azure portal.

	b.  In **Sign-out page URL** textbox, paste the value of **Sign-Out URL** which you have copied from Azure portal.

	c.  In **Identity Provider Entity ID** textbox, paste the value of **SAML Entity ID** which you have copied from Azure portal.

	d. Extract the X509Certificate from the downloaded metadata file and upload this certificate by clicking on **Upload certificate**.

	e. Click **Save**.

> [!TIP]
> You can now read a concise version of these instructions inside the [Azure portal](https://portal.azure.com), while you are setting up the app!  After adding this app from the **Active Directory > Enterprise Applications** section, simply click the **Single Sign-On** tab and access the embedded documentation through the **Configuration** section at the bottom. You can read more about the embedded documentation feature here: [Azure AD embedded documentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### Create an Azure AD test user

The objective of this section is to create a test user in the Azure portal called Britta Simon.

   ![Create an Azure AD test user][100]

**To create a test user in Azure AD, perform the following steps:**

1. In the Azure portal, in the left pane, click the **Azure Active Directory** button.

    ![The Azure Active Directory button](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_01.png)

2. To display the list of users, go to **Users and groups**, and then click **All users**.

    ![The "Users and groups" and "All users" links](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_02.png)

3. To open the **User** dialog box, click **Add** at the top of the **All Users** dialog box.

    ![The Add button](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_03.png)

4. In the **User** dialog box, perform the following steps:

    ![The User dialog box](./media/active-directory-saas-gotomeeting-tutorial/create_aaduser_04.png)

    a. In the **Name** box, type **BrittaSimon**.

    b. In the **User name** box, type the email address of user Britta Simon.

    c. Select the **Show Password** check box, and then write down the value that's displayed in the **Password** box.

    d. Click **Create**.
 
### Create a GoToMeeting test user

In this section, a user called Britta Simon is created in GoToMeeting. GoToMeeting supports just-in-time provisioning, which is enabled by default.

There is no action item for you in this section. If a user doesn't already exist in GoToMeeting, a new one is created when you attempt to access GoToMeeting.

> [!NOTE]
> If you need to create a user manually, Contact [GoToMeeting support team](https://support.logmeininc.com/gotomeeting).

### Assign the Azure AD test user

In this section, you enable Britta Simon to use Azure single sign-on by granting access to GoToMeeting.

![Assign the user role][200] 

**To assign Britta Simon to GoToMeeting, perform the following steps:**

1. In the Azure portal, open the applications view, and then navigate to the directory view and go to **Enterprise applications** then click **All applications**.

	![Assign User][201] 

2. In the applications list, select **GoToMeeting**.

	![The GoToMeeting link in the Applications list](./media/active-directory-saas-gotomeeting-tutorial/tutorial_gotomeeting_app.png)  

3. In the menu on the left, click **Users and groups**.

	![The "Users and groups" link][202]

4. Click **Add** button. Then select **Users and groups** on **Add Assignment** dialog.

	![The Add Assignment pane][203]

5. On **Users and groups** dialog, select **Britta Simon** in the Users list.

6. Click **Select** button on **Users and groups** dialog.

7. Click **Assign** button on **Add Assignment** dialog.
	
### Test single sign-on

In this section, you test your Azure AD single sign-on configuration using the Access Panel.

When you click the GoToMeeting tile in the Access Panel, you should get automatically signed-on to your GoToMeeting application.
For more information about the Access Panel, see [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md). 

## Additional resources

* [List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory](active-directory-saas-tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configure User Provisioning](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-citrixgotomeeting-provisioning-tutorial)


<!--Image references-->

[1]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-gotomeeting-tutorial/tutorial_general_203.png

