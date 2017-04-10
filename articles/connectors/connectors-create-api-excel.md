---
title: "Excel コネクタを追加する | Microsoft Docs"
description: "Excel コネクタと REST API パラメーターの概要"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 03af8652-9223-4348-9490-602872a680f0
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 5c585b988cc1989288e1e5953d1bdab6b5281707
ms.openlocfilehash: aa8b95bd92d6bed60ba20ffd28e53d26930d7a31
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-the-excel-connector"></a>Excel コネクタの使用
現時点では Logic Apps に Excel コネクタはありません。 

## <a name="to-use-excel-data"></a>Excel データを使用するには
[OneDrive](connectors-create-api-onedrive.md)などのストレージ フォルダーに、コンマ区切り値 (CSV) ファイルとして Excel データを格納できます。 この CSV ファイルは、 [フラット ファイル コネクタ](../logic-apps/logic-apps-enterprise-integration-flatfile.md)で使用することもできます。

<!---

There is no Excel connector in Logic Apps. Originally, this topic only referenced PowerApps. Removed all PowerApps references. 



Connect to Excel to insert a row, delete a row, and more. 

## Triggers and actions
Excel includes the following action. There are no triggers. 

|Trigger|Actions|
|--- | ---|
|None | <ul><li>Get rows</li><li>Insert row</li><li>Delete row</li><li>Get row</li><li>Get tables</li><li>Update row</li></ul>

All connectors support data in JSON and XML formats. 

## Swagger REST API reference
Applies to version: 1.0.

### Inserts a new row into an Excel table
```POST: /datasets/{dataset}/tables/{table}/items``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|item| |yes|body|none|Row to insert into the specified Excel table|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Retrieves a single row from an Excel table
```GET: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of row to retrieve|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Deletes a row from an Excel table
```DELETE: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of the row to delete|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




### Updates an existing row in an Excel table
```PATCH: /datasets/{dataset}/tables/{table}/items/{id}``` 



| Name| Data Type|Required|Located In|Default Value|Description|
| ---|---|---|---|---|---|
|dataset|string|yes|path|none|Excel file name|
|table|string|yes|path|none|Excel table name|
|id|string|yes|path|none|Unique identifier of the row to update|
|item| |yes|body|none|Row with updated values|


### Response

|Name|Description|
|---|---|
|200|OK|
|default|Operation Failed.|




## Object definitions

#### DataSetsMetadata

| Name | Data Type | Required|
|---|---|---|
|tabular|not defined|no|
|blob|not defined|no|

#### TabularDataSetsMetadata

| Name | Data Type |Required|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|
|tableDisplayName|string|no|
|tablePluralName|string|no|

#### BlobDataSetsMetadata

| Name | Data Type |Required|
|---|---|---|
|source|string|no|
|displayName|string|no|
|urlEncoding|string|no|

#### TableMetadata

| Name | Data Type |Required|
|---|---|---|
|name|string|no|
|title|string|no|
|x-ms-permission|string|no|
|schema|not defined|no|

#### DataSetsList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|

#### DataSet

| Name | Data Type |Required|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Table

| Name | Data Type |Required|
|---|---|---|
|Name|string|no|
|DisplayName|string|no|

#### Item

| Name | Data Type |Required|
|---|---|---|
|ItemInternalId|string|no|

#### TablesList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|

#### ItemsList

| Name | Data Type |Required|
|---|---|---|
|value|array|no|


## Next Steps
[Create a logic app](../logic-apps/logic-apps-create-a-logic-app.md)  


-->

