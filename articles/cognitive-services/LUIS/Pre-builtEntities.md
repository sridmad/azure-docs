---
title: Pre-built entities in LUIS | Microsoft Docs
description: This article contains lists of the pre-built entities that are included in Language Understanding Intelligent Services (LUIS).
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 11/04/2017
ms.author: cahann
---

# Prebuilt entities

LUIS includes a set of prebuilt entities for recognizing common types of information, like dates, times, numbers, measurements and currency. Prebuilt entity support varies by the culture of your LUIS app. For a full list of the prebuilt entities that LUIS supports, including support by culture, see the [prebuilt entity reference](./luis-reference-prebuilt-entities.md).

> [!NOTE]
> **builtin.datetime** is deprecated. It is replaced by [**built-in.datetimeV2**](#builtindatetimeV2), which provides recognition of date and time ranges, as well as improved recognition of ambiguous dates and times.

## Add a prebuilt entity

1. Open your app by clicking its name on **My Apps** page, and then click **Entities** in the left panel. 
2. On the **Entities** page, click **Add prebuilt entity**.

    ![Entities Page - Add first entity](./media/luis-use-prebuilt-entity/add-prebuilt-entity-button.png)
3. In **Add prebuilt entities** dialog box, click the prebuilt entity you want to add (for example, "datetimeV2"). Then click **Save**.

    ![Add prebuilt entity dialog box](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

## Use a prebuilt number entity
When a prebuilt entity is included in your application, its predictions are included in your published application. 
The behavior of prebuilt entities is pre-trained and **cannot** be modified. Follow these steps to see how a prebuilt entity works:

1. Add a **number** entity to your app, then [Train](Train-test.md) and [publish](PublishApp.md) the app.
2. Click on the endpoint URL in the **Publish App** page to open the LUIS endpoint in a web browser. 
3. Append an utterance to the URL that contains an numerical expression. For example, you can type in `buy two plane ticktets`, and see that LUIS identifies `two` as a `builtin.number` entity, and identifies `2` as its value in the `resolution` field. The `resolution` field helps you resolve numbers and dates to a canonical form that's easier for your client application to use. 

    ![utterance in browser containing a number entity](./media/luis-use-prebuilt-entity/browser-query.png)

LUIS can intelligently recognize numbers that aren't in non standard form. Try out different numerical expressions in your utterances and see what LUIS returns.

The following example shows a JSON response from LUIS, that includes the resolution of the value 24, for the utterance "two dozen".

```json
{
  "query": "order two dozen tickets for group travel",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.905443209
  },
  "entities": [
    {
      "entity": "two dozen",
      "type": "builtin.number",
      "startIndex": 6,
      "endIndex": 14,
      "resolution": {
        "value": "24"
      }
    }
  ]
}
```
## Use a prebuilt datetimeV2 entity
The **datetimeV2** prebuilt entity recognizes dates, times, date ranges and time durations. Follow these steps to see how the `datetimeV2` prebuilt entity works:

1. Add a **datetimeV2** entity to your app, then [Train](Train-test.md) and [publish](PublishApp.md) the app.
2. Click on the endpoint URL in the **Publish App** page to open the LUIS endpoint in a web browser. 
3. Append an utterance to the URL that contains a date range. For example, you can type in `book a flight tomorrow`, and see that LUIS identifies `tomorrow` as a `builtin.datetimeV2.date` entity, and identifies tomorrow's date as its value in the `resolution` field. 

The following example shows what the JSON response from LUIS might look like if today's date were October 31st 2017.

```json
{
  "query": "book a flight tomorrow",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9063408
  },
  "entities": [
    {
      "entity": "tomorrow",
      "type": "builtin.datetimeV2.date",
      "startIndex": 14,
      "endIndex": 21,
      "resolution": {
        "values": [
          {
            "timex": "2017-11-01",
            "type": "date",
            "value": "2017-11-01"
          }
        ]
      }
    }
  ]
}
```

## Next Steps
Learn more about prebuilt entities in the [prebuilt entity reference](./luis-reference-prebuilt-entities.md).
<!-- 
Pre-built entity   |   ```En-us```   |   ```fr-FR```   |   ```it-IT```   |   ```es-ES```   |   ```zh-CN```   |   ```de-DE```   |   ```pt-BR```   |   ```ja-JP```   |   ```ko-kr```
------|------|------|------|------|------|------|------|------|------|
DatetimeV2   |    ✔   |   -   |   -   |   -   |    ✔   |   -   |   -   |   -   |   -   |
 Datetime   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Number   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Ordinal   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Percentage   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Temperature   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Dimension   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Money   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Age   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |    ✔   |   -   |
Geography   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
Encyclopedia   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
URL   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
Email   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |
Phone number   |    ✔   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |   -   |

## Examples of prebuilt entities
The following table lists prebuilt entities with example utterances and their return values.

Pre-built entity   |   Example utterance   |   JSON
------|------|------|
 ```builtin.number```     |  ```ten```   |``` { "type": "builtin.number", "entity": "ten" } ```|
 ```builtin.number```     |   ```3.1415```   |```  { "type": "builtin.number", "entity": "3 . 1415" }``` |
 ```builtin.ordinal```     |   ```first```   |```{ "type": "builtin.ordinal", "entity": "first" }``` |
 ```builtin.ordinal```     |   ```10th```   | ```{ "type": "builtin.ordinal", "entity": "10th" }``` |  
 ```builtin.temperature```     |   ```10 degrees celsius```   | ```{ "type": "builtin.temperature", "entity": "10 degrees celcius" }```|   
 ```builtin.temperature```     |   ```78 F```   |```{ "type": "builtin.temperature", "entity": "78 f" }```|
 ```builtin.dimension```     |   ```2 miles```   |```{ "type": "builtin.dimension", "entity": "2 miles" }```|
 ```builtin.dimension```     |  ```650 square kilometers```   |```{ "type": "builtin.dimension", "entity": "650 square kilometers" }```|
 ```builtin.money```     |   ```1000.00 US dollars```   |```{ "type": "builtin.money", "entity": "1000.00 us dollars" }```
 ```builtin.money```     |   ```$ 67.5 B```   |```{ "type": "builtin.money", "entity": "$ 67.5" }```|
 ```builtin.age```   |   ```100 year old```   |```{ "type": "builtin.age", "entity": "100 year old" }```|  
 ```builtin.age```   |   ```19 years old```   |```{ "type": "builtin.age", "entity": "19 years old" }```|
 ```builtin.percentage```   |   ```The stock price increase by 7 $ this year```   |```{ "type": "builtin.percentage", "entity": "7 %" }```|
 ```builtin.datetimeV2``` | See [builtin.datetimeV2](#builtindatetimev2) | See [builtin.datetimeV2](#builtindatetimev2) |
 ```builtin.datetime``` | See [builtin.datetime](#builtindatetime) | See [builtin.datetime](#builtindatetime) |
 ```builtin.geography``` | See separate table | See separate table following this table |
 ```builtin.encyclopedia``` | See separate table | See separate table following this table |
 
 The last 3 built-in entity types listed in the table above encompass multiple subtypes. These are covered later in this article.

## builtin.number resolution

There are many ways in which numeric values are used to quantify, express, and describe pieces of information, with more possibilities than the examples listed. LUIS interprets the variations in user utterances and returns consistent numeric values. 

| Utterance        | Entity   | Resolution |
| ------------- |:----------------:| --------------:|
| ```one thousand times```  | ```"one thousand"``` |   ```"1000"```      | 
| ```1,000 people```        | ```"1,000"```    |   ```"1000"```      |
| ```1/2 cup```         | ```"1 / 2"```    |    ```"0.5"```      |
|  ```one half the amount```     | ```"one half"```     |    ```"0.5"```      |
| ```one hundred fifty orders``` | ```"one hundred fifty"``` | ```"150"``` |
| ```one hundred and fifty books``` | ```"one hundred and fifty"``` | ```"150"```|
| ```a grade of one point five```| ```"one point five"``` |  ```"1.5"``` |
| ```buy two dozen eggs```    | ```"two dozen"``` | ```"24"``` |

-->

<!-- -

## Ordinal, percentage, and currency resolution

The **builtin.ordinal**, **builtin.percentage**, and **builtin.currency** entities also provide resolution to a value.


### Percentage resolution

The following example shows the resolution of the **builtin.percentage** entity.

```
{
  "query": "set a trigger when my stock goes up 2%",
  "topScoringIntent": {
    "intent": "SetTrigger",
    "score": 0.971157849
  },
  "intents": [
    {
      "intent": "SetTrigger",
      "score": 0.971157849
    },
    {
      "intent": "None",
      "score": 0.07398871
    },
    {
      "intent": "Help",
      "score": 2.57078386E-06
    }
  ],
  "entities": [
    {
      "entity": "2",
      "type": "builtin.number",
      "startIndex": 36,
      "endIndex": 36,
      "resolution": {
        "value": "2"
      }
    },
    {
      "entity": "2%",
      "type": "builtin.percentage",
      "startIndex": 36,
      "endIndex": 37,
      "resolution": {
        "value": "2%"
      }
    }
  ]
}
```

### Ordinal resolution

The following example shows the resolution of the **builtin.ordinal** entity.

```
{
  "query": "Order the second option",
  "topScoringIntent": {
    "intent": "OrderFood",
    "score": 0.9993253
  },
  "intents": [
    {
      "intent": "OrderFood",
      "score": 0.9993253
    },
    {
      "intent": "None",
      "score": 0.05046708
    }
  ],
  "entities": [
    {
      "entity": "second",
      "type": "builtin.ordinal",
      "startIndex": 10,
      "endIndex": 15,
      "resolution": {
        "value": "2"
      }
    }
  ]
}
```



### Currency resolution

The following example shows the resolution of the **builtin.currency** entity.

```
{
  "query": "search for items under $10.99",
  "topScoringIntent": {
    "intent": "SearchForItems",
    "score": 0.926173568
  },
  "intents": [
    {
      "intent": "SearchForItems",
      "score": 0.926173568
    },
    {
      "intent": "None",
      "score": 0.07376878
    }
  ],
  "entities": [
    {
      "entity": "10.99",
      "type": "builtin.number",
      "startIndex": 24,
      "endIndex": 28,
      "resolution": {
        "value": "10.99"
      }
    },
    {
      "entity": "$10.99",
      "type": "builtin.currency",
      "startIndex": 23,
      "endIndex": 28,
      "resolution": {
        "unit": "Dollar",
        "value": "10.99"
      }
    }
  ]
}
```

## builtin.datetimeV2

The **builtin.datetimeV2** prebuilt entity automatically recognizes dates, times, and ranges of dates and times. This entity also resolves dates, times and date ranges to values in a standardized format for client programs to consume. If an utterance contains a date or time that isn't fully specified, both past and future values are included in the resolution. 

> [!NOTE]
> **builtin.datetimeV2** is available only in the ```en-us``` and ```zh-cn``` locales.

<table>
<th> example </th><th>property descriptions</th>
<tr><td>
The following is an example of a JSON response containing a builtin.datetimeV2 entity, of type <code>datetime</code>. For examples of other types of datetimeV2 entities, see <a href=#subtypes-of-datetimev2>Subtypes of datetimeV2</a>.
<pre>
  "entities": [
    {
      "entity": "8am on may 2nd 2017",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 15,
      "endIndex": 30,
      "resolution": {
        "values": [
          {
            "timex": "2017-05-02T08",
            "type": "datetime",
            "value": "2017-05-02 08:00:00"
          }
        ]
      }
    }
  ]
   </pre></td>
   <td> 
   <table>
   <tr><td>entity</td><td><b>string</b>. Text extracted from the utterance, that represents a date, time, date range or time range.</td></tr>
   <tr><td>type</td><td><b>string</b>. One of the following <a href=#subtypes-of-datetimev2>subtypes of datetimeV2</a>: 
   <ul><li>builtin.datetimeV2.datetime
   <li>builtin.datetimeV2.date
   <li>builtin.datetimeV2.time
   <li>builtin.datetimeV2.daterange
   <li>builtin.datetimeV2.timerange
   <li>builtin.datetimeV2.datetimerange
   <li>builtin.datetimeV2.duration
   <li>builtin.datetimeV2.set </ul>
</td></tr>
   <tr><td>startIndex</td><td><b>int</b>. The index in the utterance at which the entity begins.</td></tr>
   <tr><td>endIndex</td><td><b>int</b>. The index in the utterance at which the entity ends.</td></tr>
   <tr><td>resolution</td><td>
   Contains a <code>values</code> array that has one, two, or four values. 
   <ul><li>The array has one element if the date or time in the utterance is fully specified and unambiguous.</li><li>The array has two elements if the date or date range is ambiguous as to year, or a time or time range is ambiguous as to AM or PM. When there is an ambiguous date, `values` contains the most recent past and most immediate future instances of the date. See <a href="#ambiguous-dates">Ambiguous dates</a> for more examples. When there is an ambiguous time, `values` contains both the AM and PM times.</li><li>The array has four elements if the utterance contains both a date or date range that is ambiguous as to year, and a time or time range that is ambiguous as to AM or PM. For example, 3:00 April 3rd.</li>
   </ul>
   <br/>Each element of <code>values</code> may contain the following fields: <br/>
   <table><tr><td>timex</td><td>time, date, or date range expressed in TIMEX format that follows the <a href="https://en.wikipedia.org/wiki/ISO_8601">ISO 8601 standard</a> as well as using the TIMEX3 attributes for  annotation using the TimeML language. This annotation is described in the <a href="http://www.timeml.org/tempeval2/tempeval2-trial/guidelines/timex3guidelines-072009.pdf">TIMEX guidelines</a>.</td></tr><tr><td>type</td><td>The subtype, which can be one of the following: datetime, date, time, daterange, timerange, datetimerange, duration, set.</td></tr><tr><td>value </td><td><b>Optional.</b> A datetime object in the Format yyyy:MM:dd  (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime). If <code>type</code> is <code>duration</code>, the value is the number of seconds (duration) <br/> Only used if <code>type</code> is <code>datetime</code> or <code>date</code>, <code>time</code>, or <code>duration</code>.</td></tr>
   <tr><td>start</td><td>A value representing the start of a time or date range, in the same format as <code>value</code>. Only used if <code>type</code> is <code>daterange</code>, <code>timerange</code, or <code>datetimerange</code>.</td></tr></table>
   </td></tr>
   <tr><td>end</td><td>A value representing the end of a time or date range, in the same format as <code>value</code>. Only used if <code>type</code> is <code>daterange</code>, <code>timerange</code, or <code>datetimerange</code>.</td></tr></table>
   </td></tr></table>
  </td></tr>
</table>


### Recognition of date values

The **builtin.datetimeV2** entity's `resolution` field has a `values` array that contains the resolution extracted from the utterance. 

| property | description |
|----------|-------------|
| type   | A string indicating the type of entity, for example `builtin.datetimeV2.datetime` |
| resolution   | Contains a `values` array that has one, two, or four values. <ul><li>The array has one element if the date or time in the utterance is unambiguous.</li><li>The array has two elements if the date or date range is ambiguous as to year, or a time or time range is ambiguous as to AM or PM. In the case of an ambiguous date, `values` contains the most recent past and most immediate future instances of the date. In the case of an ambiguous time, `values` contains both the AM and PM times.</li><li>The array has four elements if the utterance contains both a date or date range that is ambiguous as to year, and a time or time range that is ambiguous as to AM or PM. For example, 3:00 April 3rd.</li></ul><br/>Each element of `values` may contain the following fields: <br/><table><tr><td>timex</td><td>time, date, or date range expressed in TIMEX format that follows the [ISO 8601 standard](https://en.wikipedia.org/wiki/ISO_8601).</td></tr><tr><td>type</td><td>The subtype. For example, `datetime`.</td></tr><tr><td>value </td><td><b>Optional.</b> A datetime object in the Format yyyy:MM:dd  (date), HH:mm:ss (time) yyyy:MM:dd HH:mm:ss (datetime) <br/> This property is present if the entity is recognized as a date or time, but not a date range.</td></tr></table> |



The **builtin.datetimeV2** supports dates between the following ranges.

| Min | Max |
|----------|-------------|
| 1st January 1900   | 31st December 2099 |

### Ambiguous dates

If it's unclear from an utterance whether a date refers to that date in the past or the future, LUIS provides both the most immediate past and future instances of that date. One case of this occurrence is an utterance that includes the month and date, but not the year. If today's date precedes the date in the utterance in the current year, the most immediate past instance of that date is in the previous year. Otherwise the most immediate past date is in the current year. 

For example, given the utterance "May 2nd":
* If today's date is May 3rd 2017, LUIS provides both "2017-05-02" and "2018-05-02" as values. 
* If today's date is May 1st 2017, LUIS provides both "2016-05-02" and "2017-05-02" as values.

The following example shows the resolution of the entity "may 2nd" provided that today's date is a date between May 2nd 2017 and May 1st 2018.
Fields containing `X` in the `timex` field represent parts of the date that are not explicitly specified in the utterance.

```
  "entities": [
    {
      "entity": "may 2nd",
      "type": "builtin.datetimeV2.date",
      "startIndex": 0,
      "endIndex": 6,
      "resolution": {
        "values": [
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2017-05-02"
          },
          {
            "timex": "XXXX-05-02",
            "type": "date",
            "value": "2018-05-02"
          }
        ]
      }
    }
  ]
```

### Date range resolution examples

The datetimeV2 entity can recognize date and time ranges. The `start` and `end` fields specify the beginning and end of the range. For the utterance "May 2nd to May 5th", LUIS provides **daterange** values for both the current year and the following year. In the `timex` field, the `XXXX` values represent the year that is not explicitly specified in the utterance, and `P3D` indicates that the time period is 3 days long.

```
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

The following example shows how LUIS uses **datetimeV2** to resolve the utterance "Tuesday to Thursday". In this example, the current date is June 19th. LUIS includes **daterange** values for both of the date ranges that precede and follow the current date.

```
  "entities": [
    {
      "entity": "tuesday to thursday",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 19,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-13",
            "end": "2017-06-15"
          },
          {
            "timex": "(XXXX-WXX-2,XXXX-WXX-4,P2D)",
            "type": "daterange",
            "start": "2017-06-20",
            "end": "2017-06-22"
          }
        ]
      }
    }
  ]
```

### Date range resolution examples

The datetimeV2 entity can recognize date and time ranges. The `start` and `end` fields specify the beginning and end of the range. For the utterance "May 2nd to May 5th", LUIS provides **daterange** values for both the current year and the following year. In the `timex` field, the `XXXX` values represent the year that is not explicitly specified in the utterance, and `P3D` indicates that the time period is 3 days long.

```
"entities": [
    {
      "entity": "may 2nd to may 5th",
      "type": "builtin.datetimeV2.daterange",
      "startIndex": 0,
      "endIndex": 17,
      "resolution": {
        "values": [
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2017-05-02",
            "end": "2017-05-05"
          },
          {
            "timex": "(XXXX-05-02,XXXX-05-05,P3D)",
            "type": "daterange",
            "start": "2018-05-02",
            "end": "2018-05-05"
          }
        ]
      }
    }
  ]
```

The following example shows how LUIS uses **datetimeV2** to resolve the utterance "6pm to 7pm".

```
  "entities": [
    {
      "entity": "6pm to 7pm",
      "type": "builtin.datetimeV2.timerange",
      "startIndex": 0,
      "endIndex": 9,
      "resolution": {
        "values": [
          {
            "timex": "(T18,T19,PT1H)",
            "type": "timerange",
            "start": "18:00:00",
            "end": "19:00:00"
          }
        ]
      }
    }
  ]
```
### Subtypes of datetimeV2

The **builtin.datetimeV2** prebuilt entity has the following subtypes, and examples of each are provided in the table that follows:
* builtin.datetimeV2.date
* builtin.datetimeV2.time
* builtin.datetimeV2.daterange
* builtin.datetimeV2.timerange
* builtin.datetimeV2.datetimerange
* builtin.datetimeV2.duration
* builtin.datetimeV2.set


Pre-built entity   |   Example utterance   |   JSON
------|------|------|
```builtin.datetimeV2.date```    |   ```tomorrow```   |```{ "type": "builtin.datetimeV2.date", "entity": "tomorrow", "resolution": {"values": [{"timex": "2017-06-21","type": "date", "value": "2017-06-21"}]} }``` |
```builtin.datetimeV2.date```    |   ```january 10 2009```   |```{ "type": "builtin.datetimeV2.date", "entity": "january 10 2009", "resolution": { "values": [ {"timex": "2009-01-10", "type": "date", "value": "2009-01-10" }] } }```|
```builtin.datetimeV2.date```    |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-WXX-1", "type": "date", "value": "2017-06-19" },{"timex": "XXXX-WXX-1","type": "date", "value": "2017-06-26"}]} }```|
```builtin.datetimeV2.daterange```    |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.dateV2.daterange", "resolution": { "values": [{ "timex": "2017-W27", "type": "daterange", "start": "2017-06-26", "end": "2017-07-03"}] } }```|
```builtin.datetimeV2.date```    |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "2017-06-26", "type": "date", "value": "2017-06-26" }] } }```|
```builtin.datetimeV2.time```      |   ```3 : 00```   |```{ "type": "builtin.datetimeV2.time", "entity": "3 : 00", "resolution": { "values": [{ "timex": "T03:00", "type": "time", "value": "03:00:00" }, { "timex": "T15:00", "type": "time", "value": "15:00:00" }]}	}```|
```builtin.datetimeV2.time```      |   ```4 pm```     |```{ "type": "builtin.datetimeV2.time", "entity": "4 pm", "resolution": { "values": [{"timex": "T16",  "type": "time", "value": "16:00:00"}] }	}```|
```builtin.datetimeV2.timerange```    |   ```next week```   |```{ "entity": "6pm to 7pm", "type": "builtin.datetime.dateV2.timerange", "resolution": { "values": [{ "timex": "(T18,T19,PT1H)", "type": "timerange", "start": "18:00:00", "end": "19:00:00"}] } }```|
```builtin.datetimeV2.datetimerange```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetimev2.datetimerange", "resolution": { "values": [{"timex": "2017-06-21TMO","type": "datetimerange", "start": "2017-06-21 08:00:00", "end": "2017-06-21 12:00:00"}]} }```|
```builtin.datetimeV2.datetimerange```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetimeV2.datetimerange", "resolution": { "values": [{"timex": "2017-06-20TNI","type": "datetimerange", "start": "2017-06-20 20:00:00", "end": "2017-06-20 23:59:59"}]} }```|
```builtin.datetimeV2.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetimeV2.duration", "entity": "3 hours", "resolution": { "values": [{ "timex": "PT3H", "type": "duration", "value": "10800"}] } }```|  
```builtin.datetimeV2.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetimeV2.duration", "entity": "30 minutes", "resolution": { "values": [{ "timex": "PT30M", "type": "duration", "value": "1800"}] }	}```|    
```builtin.datetimeV2.duration```      |    ```all day```    |```{ "type": "builtin.datetimeV2.duration", "entity": "all day", "resolution": { "values": [{ "timex": "P1D", "type": "duration", "value": "86400"}] }	}```|
```builtin.datetimeV2.set```    |   ```daily```   |```{ "type": "builtin.datetimeV2.set", "entity": "daily", "resolution": { "values": [{ "timex": "P1D", "type": "set", "value": "not resolved"}]}	}```|
```builtin.datetimeV2.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetimeV2.set", "resolution": { "values": [{ "timex": "XXXX-WXX-2", "type": "set", "value": "not resolved"}]} }```|   
```builtin.datetimeV2.set```    |   ```every week```   |```{ "entity": "every week", "type": "builtin.datetimeV2.set", "resolution": {"time": "XXXX-WXX"} }```|

## builtin.datetime

<!--The **builtin.datetime** prebuilt entity is aware of the current date and time. In the following examples, the current date is 2017-06-21. Also, the **builtin.datetime** entity provides a resolution field that produces a machine-readable dictionary. -->
<!-- 
The **builtin.datetime** prebuilt entity is deprecated and replaced by [builtin.datetimeV2](#builtindatetimev2). 

To replace **builtin.datetime** with **builtin.datetimeV2** in your LUIS app, do the following:

1. Open the **Entities** pane of the LUIS web interface. 
2. Delete the **datetime** prebuilt entity.
3. Click **Add prebuilt entity**
4. Select **datetimeV2** and click **Save**.

The following table provides a comparison of datetime and datetimeV2. In the examples, the current date is 2017-06-20.

Pre-built entity   |   Example utterance   |   JSON
------|------|------|
```builtin.datetime.date```      |   ```tomorrow```   |```{ "type": "builtin.datetime.date", "entity": "tomorrow", "resolution": {"date": "2017-06-21"} }``` |
```builtin.datetimeV2.date```    |   ```tomorrow```   |```{ "type": "builtin.datetimeV2.date", "entity": "tomorrow", "resolution": {"values": [{"timex": "2017-06-21","type": "date", "value": "2017-06-21"}]} }``` |
```builtin.datetime.date```      |   ```january 10 2009```   |```{ "type": "builtin.datetime.date", "entity": "january 10 2009", "resolution": {"date": "2009-01-10"} }```|
```builtin.datetimeV2.date```    |   ```january 10 2009```   |```{ "type": "builtin.datetimeV2.date", "entity": "january 10 2009", "resolution": { "values": [ {"timex": "2009-01-10", "type": "date", "value": "2009-01-10" }] } }```|
```builtin.datetime.date```      |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetime.date", "resolution": {"date": "XXXX-WXX-1"} }```|
```builtin.datetimeV2.date```    |   ```monday```    |```{ "entity": "monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-WXX-1", "type": "date", "value": "2017-06-19" },{"timex": "XXXX-WXX-1","type": "date", "value": "2017-06-26"}]} }```|
```builtin.datetime.date```      |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.date", "resolution": {"date":  "2017-W26"} }```|
```builtin.datetimeV2.daterange```    |   ```next week```   |```{ "entity": "next week", "type": "builtin.datetime.dateV2.daterange", "resolution": { "values": [{ "timex": "2017-W27", "type": "daterange", "start": "2017-06-26", "end": "2017-07-03"}] } }```|
```builtin.datetime.date```      |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetime.date", "resolution": {"date": "2017-06-26"} }```|
```builtin.datetimeV2.date```    |   ```next monday```   |```{ "entity": "next monday", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "2017-06-26", "type": "date", "value": "2017-06-26" }] } }```|
```builtin.datetime.time```      |   ```3 : 00```   |```{ "type": "builtin.datetime.time", "entity": "3 : 00", "resolution": {"comment": "ampm", "time": "T03:00"}	}```|
```builtin.datetimeV2.time```      |   ```3 : 00```   |```{ "type": "builtin.datetimeV2.time", "entity": "3 : 00", "resolution": { "values": [{ "timex": "T03:00", "type": "time", "value": "03:00:00" }, { "timex": "T15:00", "type": "time", "value": "15:00:00" }]}	}```|
```builtin.datetime.time```      |   ```4 pm```     |```{ "type": "builtin.datetime.time", "entity": "4 pm", "resolution": {"time": "T16"}	}```|
```builtin.datetimeV2.time```      |   ```4 pm```     |```{ "type": "builtin.datetimeV2.time", "entity": "4 pm", "resolution": { "values": [{"timex": "T16",  "type": "time", "value": "16:00:00"}] }	}```|
```builtin.datetime.time```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetime.time", "resolution": {"time": "2015-08-15TMO"} }```|
```builtin.datetimeV2.datetimerange```      |   ```tomorrow morning```   |```{ "entity": "tomorrow morning", "type": "builtin.datetimev2.datetimerange", "resolution": { "values": [{"timex": "2017-06-21TMO","type": "datetimerange", "start": "2017-06-21 08:00:00", "end": "2017-06-21 12:00:00"}]} }```|
```builtin.datetime.time```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetime.time", "resolution": {"time": "2015-08-14TNI"} }```|
```builtin.datetimeV2.datetimerange```      |   ```tonight```  |```{ "entity": "tonight", "type": "builtin.datetimeV2.datetimerange", "resolution": { "values": [{"timex": "2017-06-20TNI","type": "datetimerange", "start": "2017-06-20 20:00:00", "end": "2017-06-20 23:59:59"}]} }```|
```builtin.datetime.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetime.duration", "entity": "3 hours", "resolution": {"duration": "PT3H"} }```|
```builtin.datetimeV2.duration```      |    ```for 3 hours```    |```{ "type": "builtin.datetimeV2.duration", "entity": "3 hours", "resolution": { "values": [{ "timex": "PT3H", "type": "duration", "value": "10800"}] } }```|
```builtin.datetime.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetime.duration", "entity": "30 minutes", "resolution": {"duration": "PT30M"}	}```|    
```builtin.datetimeV2.duration```      |    ```30 minutes long```   |```{ "type": "builtin.datetimeV2.duration", "entity": "30 minutes", "resolution": { "values": [{ "timex": "PT30M", "type": "duration", "value": "1800"}] }	}```|    
```builtin.datetime.duration```      |    ```all day```    |```{ "type": "builtin.datetime.duration", "entity": "all day", "resolution": {"duration": "P1D"}	}```|
```builtin.datetimeV2.duration```      |    ```all day```    |```{ "type": "builtin.datetimeV2.duration", "entity": "all day", "resolution": { "values": [{ "timex": "P1D", "type": "duration", "value": "86400"}] }	}```|
```builtin.datetime.set```    |   ```daily```   |```{ "type": "builtin.datetime.set", "entity": "daily", "resolution": "set": {"XXXX-XX-XX"}	}```|
```builtin.datetimeV2.set```    |   ```daily```   |```{ "type": "builtin.datetimeV2.set", "entity": "daily", "resolution": { "values": [{ "timex": "P1D", "type": "set", "value": "not resolved"}]}	}```|
```builtin.datetime.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetime.set", "resolution":  {"time": "XXXX-WXX-2"} }```|  
```builtin.datetimeV2.set```    |   ```every tuesday```   |```{ "entity": "every tuesday", "type": "builtin.datetimeV2.set", "resolution": { "values": [{ "timex": "XXXX-WXX-2", "type": "set", "value": "not resolved"}]} }```|   
```builtin.datetime.set```    |   every week   |```{ "entity": "every week", "type": "builtin.datetime.set", "resolution": {"time": "XXXX-WXX"} }```|
```builtin.datetimeV2.set```    |   every week   |```{ "entity": "every week", "type": "builtin.datetimeV2.set", "resolution": {"time": "XXXX-WXX"} }```|
-->
<!-- TODO: Verify whether the following are by design 
builtin.datetime.set    |   every morning   |```{ "type": "builtin.datetime.set", "entity": "every morning", "resolution": {"time": "XXXX-XX-XXTMO"}	}```|
builtin.datetimeV2.timerange    |   every morning   |```{ "type": "builtin.datetimeV2.timerange", "entity": "morning", "resolution": { "values": [{"timex": "TMO", "type": "timerange", "start": "08:00:00", "end": "12:00:00"}]	} }```|

builtin.datetime.date      |   week of september 30th   |```{ "entity": "week of september 30th", "type": "builtin.datetime.date", "resolution": {"comment": "weekof", "date": "XXXX-09-30"} }```|
builtin.datetimeV2.date      |   week of september 30th   |```{ "entity": "september 30th", "type": "builtin.datetimeV2.date", "resolution": { "values": [{ "timex": "XXXX-09-30", "type": "date", "value": "2016-09-30" },{"timex": "XXXX-09-30","type": "date", "value": "2017-09-30" }]} }```|


## builtin.geography

> [!NOTE]
> **builtin.geography** is available only in the en-us locale.

#### The builtin.geography built-in entity type has 3 sub-types:

Pre-built entity   |   Example utterance   |   JSON
------|------|------|
```builtin.geography.city```   |  ```seattle```    |```{ "type": "builtin.geography.city", "entity": "seattle" }```|
```builtin.geography.city```   |  ```paris```    |```{ "type": "builtin.geography.city", "entity": "paris" }```|
```builtin.geography.country```|  ```australia```    |```{ "type": "builtin.geography.country", "entity": "australia" }```|
```builtin.geography.country```|  ```japan```    |```{ "type": "builtin.geography.country", "entity": "japan" }```|
```builtin.geography.pointOfInterest```   |   ```amazon river``` |```{ "type": "builtin.geography.pointOfInterest", "entity": "amazon river" }```|
```builtin.geography.pointOfInterest```   |   ```sahara desert```|```{ "type": "builtin.geography.pointOfInterest", "entity": "sahara desert" }```|

## builtin.encyclopedia

> [!NOTE]
> **builtin.encyclopedia** is available only in the ```en-US``` locale.

##### The builtin.encyclopedia built-in entity includes over 100 sub-types in the following table. In addition, encyclopedia entities often map to multiple types. For example, the query Ronald Reagan yields: 
```
{
      "entity": "ronald reagan",
      "type": "builtin.encyclopedia.people.person"
    },
    {
      "entity": "ronald reagan",
      "type": "builtin.encyclopedia.film.actor"
    },
    {
      "entity": "ronald reagan",
      "type": "builtin.encyclopedia.government.us_president"
    },
    {
      "entity": "ronald reagan",
      "type": "builtin.encyclopedia.book.author"
    }
 ```


Pre-built entity   |   Pre-built entity (sub-types)   |   Example utterance
------|------|------|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.people.person ```|```bryan adams``` |
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.film.producer ```| ```walt disney``` |
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.film.cinematographer```| ```adam greenberg  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.royalty.monarch ```| ``` elizabeth ii  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.film.director ```| ```steven spielberg  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.film.writer  ```| ``` alfred hitchcock  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.film.actor  ```| ``` robert de niro  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.martial_arts.martial_artist  ```| ``` bruce lee  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.architecture.architect  ```| ```james gallier  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.geography.mountaineer  ```| ``` jean couzy  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.celebrities.celebrity  ```| ``` angelina jolie  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.music.musician  ```| ``` bob dylan  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.soccer.player ```| ``` diego maradona  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.baseball.player  ```| ``` babe ruth  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.basketball.player  ```| ``` heiko schaffartzik  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.olympics.athlete  ```| ``` andre agassi  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.basketball.coach  ```| ``` bob huggins  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.american_football.coach  ```| ``` james franklin  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.cricket.coach  ```| ``` andy flower  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.ice_hockey.coach  ```| ``` david quinn  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.ice_hockey.player  ```| ``` vincent lecavalier  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.government.politician  ```| ``` harold nicolson  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.government.us_president  ```| ``` barack obama  ```|
```builtin.encyclopedia.people.person```| ```builtin.encyclopedia.government.us_vice_president  ```| ``` dick cheney  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.organization.organization  ```| ``` united nations  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.sports.league  ```| ``` american league  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.ice_hockey.conference  ```| ``` western hockey league  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.baseball.division  ```| ``` american league east  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.baseball.league  ```| ``` major league baseball  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.basketball.conference  ```| ``` national basketball league  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.basketball.division  ```| ``` pacific division  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.soccer.league  ```| ``` premier league  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.american_football.division  ```| ``` afc north  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.broadcast.broadcast```| ```nebraska educational telecommunications```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.broadcast.tv_station  ```| ``` abc  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.broadcast.tv_channel  ```| ``` cnbc world  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.broadcast.radio_station  ```| ``` bbc radio 1  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.business.operation  ```| ``` bank of china  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.music.record_label  ```| ``` pixar  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.aviation.airline  ```| ``` air france  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.automotive.company  ```| ``` general motors  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.music.musical_instrument_company```| ```gibson guitar corporation``` |
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.tv.network  ```| ``` cartoon network  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.education.educational_institution  ```| ``` cornwall hill college``` |
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.education.school  ```| ``` boston arts academy  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.education.university  ```| ``` johns hopkins university  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.sports.team  ```| ``` united states national handball team  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.basketball.team  ```| ``` chicago bulls  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.sports.professional_sports_team  ```| ``` boston celtics  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.cricket.team  ```| ``` mumbai indians  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.baseball.team  ```| ``` houston astros  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.american_football.team  ```| ``` green bay packers  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.ice_hockey.team  ```| ``` hamilton bulldogs  ```|
```builtin.encyclopedia.organization.organization```| ```builtin.encyclopedia.soccer.team  ```| ``` fc bayern munich  ```|
```builtin.encyclopedia.organization.organization |builtin.encyclopedia.government.political_party|pertubuhan kebangsaan melayu singapura```|
```builtin.encyclopedia.time.event  ```| ``` builtin.encyclopedia.time.event  ```| ``` 1740 batavia massacre   ```|
```builtin.encyclopedia.time.event  ```| ``` builtin.encyclopedia.sports.championship_event  ```| ``` super bowl xxxix  ```|
```builtin.encyclopedia.time.event  ```| ``` builtin.encyclopedia.award.competition  ```| ``` eurovision song contest 2003  ```|
```builtin.encyclopedia.tv.series_episode  ```| ``` builtin.encyclopedia.tv.series_episode  ```| ``` the magnificent seven  ```|
```builtin.encyclopedia.tv.series_episode  ```| ``` builtin.encyclopedia.tv.multipart_tv_episode  ```| ``` the deadly assassin  ```|
```builtin.encyclopedia.commerce.consumer_product  ```| ``` builtin.encyclopedia.commerce.consumer_product  ```| ``` nokia lumia 620  ```|
```builtin.encyclopedia.commerce.consumer_product  ```| ``` builtin.encyclopedia.music.album  ```| ``` dance pool  ```|
```builtin.encyclopedia.commerce.consumer_product  ```| ``` builtin.encyclopedia.automotive.model  ```| ``` pontiac fiero  ```|
```builtin.encyclopedia.commerce.consumer_product  ```| ``` builtin.encyclopedia.computer.computer  ```| ```toshiba satellite  ```|
```builtin.encyclopedia.commerce.consumer_product  ```| ``` builtin.encyclopedia.computer.web_browser  ```| ``` internet explorer  ```|
```builtin.encyclopedia.commerce.brand  ```| ``` builtin.encyclopedia.commerce.brand  ```| ``` diet coke  ```|
```builtin.encyclopedia.commerce.brand  ```| ``` builtin.encyclopedia.automotive.make  ```| ``` chrysler  ```|
```builtin.encyclopedia.music.artist  ```| ``` builtin.encyclopedia.music.artist  ```| ``` michael jackson  ```|
```builtin.encyclopedia.music.artist  ```| ``` builtin.encyclopedia.music.group  ```| ``` the yardbirds  ```|
```builtin.encyclopedia.music.music_video  ```| ``` builtin.encyclopedia.music.music_video  ```| ``` the beatles anthology  ```|
```builtin.encyclopedia.theater.play  ```| ``` builtin.encyclopedia.theater.play  ```| ``` camelot  ```|
```builtin.encyclopedia.sports.fight_song  ```| ``` builtin.encyclopedia.sports.fight_song  ```| ``` the cougar song  ```|
```builtin.encyclopedia.film.series  ```| ``` builtin.encyclopedia.film.series  ```| ``` the twilight saga  ```|
```builtin.encyclopedia.tv.program  ```| ``` builtin.encyclopedia.tv.program  ```| ``` late night with david letterman  ```|
```builtin.encyclopedia.radio.radio_program  ```| ``` builtin.encyclopedia.radio.radio_program  ```| ``` grand ole opry  ```|
```builtin.encyclopedia.film.film  ```| ``` builtin.encyclopedia.film.film  ```| ``` alice in wonderland  ```|
```builtin.encyclopedia.cricket.tournament  ```| ``` builtin.encyclopedia.cricket.tournament  ```| ``` cricket world cup  ```|
```builtin.encyclopedia.government.government  ```| ``` builtin.encyclopedia.government.government  ```| ``` european commission  ```|
```builtin.encyclopedia.sports.team_owner  ```| ``` builtin.encyclopedia.sports.team_owner ```| ``` bob castellini  ```|
```builtin.encyclopedia.music.genre  ```| ``` builtin.encyclopedia.music.genre  ```| ``` eastern europe  ```|
```builtin.encyclopedia.ice_hockey.division  ```| ``` builtin.encyclopedia.ice_hockey.division  ```| ``` hockeyallsvenskan  ```|
```builtin.encyclopedia.architecture.style  ```| ``` builtin.encyclopedia.architecture.style  ```| ``` spanish colonial revival architecture  ```|
```builtin.encyclopedia.broadcast.producer  ```| ``` builtin.encyclopedia.broadcast.producer  ```| ``` columbia tristar television  ```|
```builtin.encyclopedia.book.author  ```| ``` builtin.encyclopedia.book.author  ```| ``` adam maxwell  ```|
```builtin.encyclopedia.religion.founding_figur  ```| ``` builtin.encyclopedia.religion.founding_figur  ```| ``` gautama buddha  ```|
```builtin.encyclopedia.martial_arts.martial_art  ```| ``` builtin.encyclopedia.martial_arts.martial_art  ```| ``` american kenpo  ```|
```builtin.encyclopedia.sports.school  ```| ``` builtin.encyclopedia.sports.school  ```| ``` yale university  ```|
```builtin.encyclopedia.business.product_line  ```| ``` builtin.encyclopedia.business.product_line  ```| ``` canon powershot  ```|
```builtin.encyclopedia.internet.website  ```| ``` builtin.encyclopedia.internet.website  ```| ``` bing  ```|
```builtin.encyclopedia.time.holiday  ```| ``` builtin.encyclopedia.time.holiday  ```| ``` easter  ```|
```builtin.encyclopedia.food.candy_bar  ```| ``` builtin.encyclopedia.food.candy_bar  ```| ``` cadbury dairy milk  ```|
```builtin.encyclopedia.finance.stock_exchange  ```| ``` builtin.encyclopedia.finance.stock_exchange  ```| ``` tokyo stock exchange  ```|
```builtin.encyclopedia.film.festival  ```| ``` builtin.encyclopedia.film.festival  ```| ``` berlin international film festival  ```|

-->

