﻿---
title: Add entities in LUIS apps | Microsoft Docs
description: Add entities (key data in your application's domain) in Language Understanding Intelligent Service (LUIS) apps.
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 03/01/2017
ms.author: cahann
---

# Add entities
Entities are key data in your application’s domain. An entity represents a class including a collection of similar objects (places, things, people, events, or concepts). 
Entities describe information relevant to the intent, and sometimes they are essential for your app to perform its task. 

For example, a News Search app may include entities such as “topic”, “source”, “keyword” and “publishing date”, which are key data to search for news. In a travel booking app, the "location", "date", "airline", "travel class" and "tickets" are key information for flight booking. These terms are relevant to the "BookFlight" intent, so you can add them as entities.

You do not need to create entities for every concept in your app, but only for the concepts that the app requires to take action. 

You can add, edit, or delete entities in your app through the **Entities list** on the **Entities** page. LUIS offers many types of entities. These include prebuilt entities, custom entities and list entities.


## Add prebuilt entities
LUIS provides a set of prebuilt, system-defined entities. These entities cover many common concepts such as date, age, temperature, percentage, dimension, cardinal and ordinal numbers.  

For example, the TravelAgent app may receive a request like "Book me a flight to Boston on May 4." Your app needs to understand date and time words in order to interpret the request properly. Rather than creating entities for such concepts from scratch, you can enable a ready-made prebuilt entity called "datetimeV2." 

As another example, a request like “Book me the first flight to Boston” might be sent to a travel agent app. This requires understanding of the ordinal words "first" and "second". You can add a prebuilt entity called "ordinal", for your app to recognize ordinal numbers.

For a full list of prebuilt entities and their use, see [Prebuilt Entities List](Pre-builtEntities.md).

**To add a prebuilt entity:**

1. Open the TravelAgent app by clicking its name on **My Apps** page, and then click **Entities** in the left panel. 
2. On the **Entities** page, click **Add prebuilt entity**.

    ![Entities Page - Add first entity](./Images/EntitiesPage-AddFirstEntity.JPG)
3. In **Add prebuilt entities** dialog box, click the prebuilt entity you want to add (for example, "datetimeV2"). Then click **Save**.

    ![Add prebuilt entity dialog box](./Images/AddPrebuilt-Dialogbox.JPG)


## Custom entities
Custom entities are the entities you create in your LUIS app. The following are types of custom entities:

| Entity Type          | Description           |
| ------------- | -----------------------|
| Simple | A *simple entity* is a generic entity that describes a single concept.  |
| Hierarchical | A *hierarchical entity* defines a category and its members, or a type and its subtypes. For example, a TravelClass hierarchical entity may have FirstClass and EconomyClass as child entities. |
| Composite | A *composite entity* is made up of other entities that form parts of a whole. For example, a FlightOrder composite entity may have TravelClass, FlightNumber, OriginLocation, DestinationLocation, and NumberOfTickets as child entities.| 
| List | A *list entity* is a fixed list of synonyms or related words. | 

### Add simple entities
A simple entity is a generic entity that describes a single concept. In the example of the TravelAgent app, a user may say "Book me a flight to London tomorrow on British Airways", where "British Airways" is the name of an airline company. In order to capture the notion of airline names, let's create the entity "Airline." 

**To add a simple entity:**

1. Open the TravelAgent app by clicking its name on **My Apps** page, and then click **Entities** in the left panel. 
2. On the **Entities** page, click **Add custom entity**.
3. In the **Add Entity** dialog box, type "Airline" in the **Entity name** box,  select **Simple** from the **Entity type** list, and then click **Save**.

    ![Add Entity Dialog box - Simple](./Images/AddSimpleEntity.jpg)


### Add hierarchical entities
A hierarchical entity defines a relationship between a category and its members, or a type and its subtypes. In these relationships, the hierarchical entity acts as the parent. The child entities are subtypes that share some of the parent entity's characteristics. For example, in the TravelAgent app, you can add three hierarchical entities:

* "Location", including the child entity "FromLocation" and "ToLocation", representing source and destination locations.
* "TravelClass", including the travel classes as children ("first", "business" and "economy")
* "Category", including ticket categories ("adult", "child" and "infant").

Do the following steps to add hierarchical entities. Make sure to add the child entities at the same time that you create the parent entity. You can add up to 10 child entities for each parent.

**To add a hierarchical entity:**

1. Open the TravelAgent app by clicking its name on **My Apps** page, and then click **Entities** in the left panel. 
2. On the **Entities** page, click **Add custom entity**.
3. In the **Add Entity** dialog box, type "Location" in the **Entity name** box, and then select **Hierarchical** from the **Entity type** list.

    ![Add hierarchical entity](./Images/AddHierarchicalEntity.JPG)

4. Click **Add Child**, and then type "FromLocation" in **Child #1** box. 
5. Click **Add Child**, and then type "ToLocation" in **Child #2** box. 
    >[!NOTE]
    >To delete a child (in case of a mistake), click the trash bin icon next to it.

6. Click **Save**.

 
### Add composite entities
You can also define relationships between entities by creating “composite entities.” A composite entity is created by combining two or more existing entities (simple or hierarchical) and treating them as one entity. Unlike a hierarchical entity, the composite entity and the children forming it are not in a parent-child relationship. They are independent of each other and they do not need to share common characteristics. The composite pattern enables your app to identify entities, not only individually, but also in groups. 

In the TravelAgent app example, a user may say “Book 2 adult business tickets to Paris next Monday.” In this example, we can create a composite entity called “TicketsOrder”, including three children entities: “number”, “category” and "class", which describe the tickets to be booked. Before creating a composite entity, you must first add the entities forming it, if they do not already exist. 

**To add the entities forming the composite:**

1. Add the prebuilt entity “number.” For instructions, see the preceding section [Add Prebuilt Entities](#add-prebuilt-entities). 
2. Add the hierarchical entity "Category", including the subtypes: “adult”, “child” and “infant”, and "TravelClass" including "first", "business" and "economy". For more instructions, see the preceding section [Add hierarchical entities](#add-hierarchical-entities). 

**To add the composite entity:**

1. Open the TravelAgent app by clicking its name on **My Apps** page and click **Entities** in the app's left panel.
2. On the **Entities** page, click **Add custom entity**.
3. In the **Add Entity** dialog box, type "TicketsOrder" in the **Entity name** box, and then select **Composite** from the **Entity type** list.
4. Click **Add Child** to add a new child.
5. In **Child #1**, select the entity "number" from the list.
6. In **Child #2**, select the parent entity "Category" from the list. 
7. In **Child #3**, select the parent entity "TravelClass" from the list. 

    ![Add composite entity](./Images/AddCompositeEntity.jpg)

7. Click **Save**.

    >[!NOTE]
    >To delete a child (in case of a mistake), click the trash button next to it.


## List entities
A list entity is an entity that is defined by a list of all its values. This entity type is identified in utterances, not by active learning of context, but by the direct matching of utterance text to the defined values.   

In some use cases, you may have a fixed list of definite values for an entity. In this case, you can create a list entity including these values. For each value, you define a standard form "Canonical Form" and variant forms "Synonyms." Your app identifies this entity in an utterance if the utterance includes an exact match of any of the entity values (canonical forms/synonyms), so the list entity is predicted as existing in the utterance, with a prediction score of 100%.

For example, in the TravelAgent app, we can create a list entity named “Coastal Cities” that includes values such as "Barcelona", "Miami", and "Venice" as canonical forms. For each canonical form, you can add synonyms. For example, synonyms of “Barcelona” can be “Capital City of Catalonia”, “BCN”, “Barna” and “Second Spanish City”. 

List entity values can be added individually, or collectively by importing them as a JSON file. Furthermore, if your app culture is English, LUIS can propose some relevant values which you can add to your list. The following procedures explain how to add a list entity, and how to add its values by different methods.
 

**To add a list entity:**

1. Open the TravelAgent app by clicking its name on **My Apps** page and click **Entities** in the app's left panel.
2. On the **Entities** page, click **Add custom entity**.
3. In the **Add Entity** dialog box, type "Coastal Cities" in the **Entity name** box and select **List** as the **Entity type**.
 
  ![Add a list entity](./media/luis-how-to-add-entities/add-list-entity.jpg)
  
4. Click **Save**. The list entity "Coastal Cities" is added and the details page where you should define its values is displayed. 

 ![List entity details page](./media/luis-how-to-add-entities/list-entity.jpg)

**To add list entity values individually:**

1. On the "Coastal Cities" list entity page, type a standard form for each value in the **Canonical Form** column (for example, Barcelona), and type other forms of it in **Synonyms** (for example, "BCN", "Barna"). After typing each synonym, press Enter.

2. When you finish typing all synonyms, click **Add**.

 ![Add list entity values](./media/luis-how-to-add-entities/list-entity-values.jpg)
 
3. Repeat the above two steps to add more values to the entity list.

**To import a list of values as a JSON file:**

 1. On the "Coastal Cities" list entity page, click **Import Lists**.
 2. In **Import New Entries** dialog box, click **Choose File** and select the JSON file that includes the list.

  ![Import list entity values](./media/luis-how-to-add-entities/list-entity-import.jpg)
  
 3. To learn about the supported list syntax in JSON, click **Learn about supported list syntax** to expand the dialog and display an example of allowed syntax. To collapse the dialog and hide syntax, click it again.

 ![Allowed syntax of imported lists](./media/luis-how-to-add-entities/list-entity-import-syntx.jpg)
  
 4. Click **Import**.

**To get a list of proposed values (only for English culture apps):**

1. On the "Coastal Cities" list entity page, with at least one canonical form added (for example, Barcelona), click **Show Related Values**.

 ![Show Related Values](./media/luis-how-to-add-entities/list-entity-related-values.jpg)
 
2. Click **Recommend**. You get a number of proposed values that are semantically related to the added canonical form (Barcelona in this example). The following screenshot shows the proposed values.

 ![Proposed Values](./media/luis-how-to-add-entities/list-entity-proposed-values.jpg)
 
3. Click a value to add it to your list as a canonical form, or click **Add All** to add all values.

> [!NOTE]
> * The proposed values are added as canonical forms not synonyms; synonyms should be typed manually.
> * To delete an added canonical form, click the trash bin button corresponding to it.

  
## Edit or delete entities
You can edit or delete entities from the **Entities list** on the **Entities** page of your app. 

**To edit an entity:**

1. On the **Entities** page, click the entity in the **Entities list**.
2. In the **Edit Entity** dialog box, you can edit the entity name and child entity names. For hierarchical or composite entities, you can also add more child entities. The entity type is not editable. 

    ![Edit Entity dialog box](./Images/EditEntity-dialogbox.JPG)

3. Click **Save**.

**To delete an entity:**

* In the **Entities list**, click the trash bin icon next to the entity you want to delete. Then, click **OK** in the confirmation message to confirm deletion.
 
    ![Delete Entity](./Images/DeleteEntity-Confirmation.jpg)

    >[!NOTE]
    >* Deleting a hierarchical entity deletes all its children entities.
    >* Deleting a composite entity deletes only the composite and breaks the composite relationship, but doesn't delete the entities forming it.


## Review labeled utterances for entities
To review the labeled utterances that contain a specific entity, click the **Labeled Utterances** tab on the **Entities** page, and choose the entity for which you want to display all labeled utterances. You can modify entity labels in labeled utterances, if required, and then click **Save**.

![Labeled Utterances for an entity](./Images/Entities-LabeledUtter.JPG)


## Next steps
Now that you have added intents, utterances and entities, you have a basic LUIS app ready to be trained and tested for publishing. 
For more information on how to train and test your app, [click here](Train-Test.md).
 
