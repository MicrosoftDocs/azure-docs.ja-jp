---
title: "Azure Functions 外部テーブル バインディング (プレビュー) | Microsoft Docs"
description: "Azure Functions での外部テーブル バインディングの使用"
services: functions
documentationcenter: 
author: alexkarcher-msft
manager: cfowler
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: alkarche
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: e6aa6913bdb2cf8e581b76b7680d0a96c9858bd1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="azure-functions-external-table-binding-preview"></a>Azure Functions 外部テーブル バインディング (プレビュー)
この記事では、関数内から組み込みバインディングを使って SaaS プロバイダー (SharePoint、Dynamics など) 上にある表形式データを操作する方法について説明します。 Azure Functions は、外部テーブルの入力バインディングと出力バインディングをサポートしています。

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="api-connections"></a>API 接続

テーブル バインディングでは、サード パーティの SaaS プロバイダーに対する認証に外部 API 接続を利用します。 

バインディングを割り当てるときに、新しい API 接続を作成するか、または同じリソース グループ内の既存の API 接続を使うことができます。

### <a name="supported-api-connections-tables"></a>サポートされている API 接続 (テーブル)

|コネクタ|トリガー|入力|出力|
|:-----|:---:|:---:|:---:|
|[DB2](https://docs.microsoft.com/azure/connectors/connectors-create-api-db2)||○|○
|[Dynamics 365 for Operations](https://ax.help.dynamics.com/wiki/install-and-configure-dynamics-365-for-operations-warehousing/)||○|○
|[Dynamics 365](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||○|○
|[Dynamics NAV](https://msdn.microsoft.com/library/gg481835.aspx)||○|○
|[Google スプレッドシート](https://docs.microsoft.com/azure/connectors/connectors-create-api-googledrive)||○|○
|[Informix](https://docs.microsoft.com/azure/connectors/connectors-create-api-informix)||○|○
|[Dynamics 365 for Financials](https://docs.microsoft.com/azure/connectors/connectors-create-api-crmonline)||○|○
|[MySQL](https://docs.microsoft.com/azure/store-php-create-mysql-database)||○|○
|[Oracle Database](https://docs.microsoft.com/azure/connectors/connectors-create-api-oracledatabase)||○|○
|[Common Data Service](https://docs.microsoft.com/common-data-service/entity-reference/introduction)||○|○
|[Salesforce](https://docs.microsoft.com/azure/connectors/connectors-create-api-salesforce)||○|○
|[SharePoint](https://docs.microsoft.com/azure/connectors/connectors-create-api-sharepointonline)||○|○
|[SQL Server](https://docs.microsoft.com/azure/connectors/connectors-create-api-sqlazure)||○|○
|[Teradata](http://www.teradata.com/products-and-services/azure/products/)||○|○
|UserVoice||○|○
|Zendesk||○|○


> [!NOTE]
> 外部テーブル接続を [Azure Logic Apps](https://docs.microsoft.com/azure/connectors/apis-list) で使用することもできます

### <a name="creating-an-api-connection-step-by-step"></a>API 接続の作成手順

1. 関数の作成画面で [カスタム関数] を選択します。![カスタム関数の作成](./media/functions-bindings-storage-table/create-custom-function.jpg)
1. [シナリオ] から [`Experimental`]  >  [`ExternalTable-CSharp` テンプレート] > [新しい `External Table connection` の作成] を選択します。
![テーブル入力テンプレートの選択](./media/functions-bindings-storage-table/create-template-table.jpg)
1. SaaS プロバイダーを選択し、接続を選択または作成します。![SaaS 接続の構成](./media/functions-bindings-storage-table/authorize-API-connection.jpg)
1. API 接続を選択し、関数を作成します。![テーブル関数の作成](./media/functions-bindings-storage-table/table-template-options.jpg)
1. [`Integrate`]  >  [`External Table`] を選択します。
    1. ターゲット テーブルを使用するための接続を構成します。 これらの設定は SaaS プロバイダーごとに異なります。 以下の図は、「[データ ソースの設定](#datasourcesettings)」で作成するテーブルの例です。
![テーブルの構成](./media/functions-bindings-storage-table/configure-API-connection.jpg)

## <a name="usage"></a>使用法

この例では、Id、LastName、FirstName の各列で構成された "Contact" という名前のテーブルに接続します。 以下のコードは、Contact テーブル内のエンティティを列挙し、ファースト ネームとラスト ネームをログに記録するものです。

### <a name="bindings"></a>バインド
```json
{
  "bindings": [
    {
      "type": "manualTrigger",
      "direction": "in",
      "name": "input"
    },
    {
      "type": "apiHubTable",
      "direction": "in",
      "name": "table",
      "connection": "ConnectionAppSettingsKey",
      "dataSetName": "default",
      "tableName": "Contact",
      "entityId": "",
    }
  ],
  "disabled": false
}
```
テーブル バインディングの場合、`entityId` は空にする必要があります。

API 接続文字列が格納されるアプリ設定は、`ConnectionAppSettingsKey` で指定します。 このアプリ設定は、[統合] の UI で API 接続を追加すると自動的に作成されます。

テーブル コネクタによってデータセットが得られ、それぞれのデータセットにテーブルが格納されます。 既定のデータセットの名前は "default" です。 以下の表は、各種 SaaS プロバイダーにおけるデータセットとテーブルのタイトルを一覧にしたものです。

|コネクタ|Dataset|テーブル|
|:-----|:---|:---| 
|**SharePoint**|サイト|SharePoint リスト
|**SQL**|データベース|テーブル 
|**Google シート**|スプレッドシート|ワークシート 
|**Excel**|Excel ファイル|シート 

<!--
See the language-specific sample that copies the input file to the output file.

* [C#](#incsharp)
* [Node.js](#innodejs)

-->
<a name="incsharp"></a>

### <a name="usage-in-c"></a>C# での使用方法 #

```cs
#r "Microsoft.Azure.ApiHub.Sdk"
#r "Newtonsoft.Json"

using System;
using Microsoft.Azure.ApiHub;

//Variable name must match column type
//Variable type is dynamically bound to the incoming data
public class Contact
{
    public string Id { get; set; }
    public string LastName { get; set; }
    public string FirstName { get; set; }
}

public static async Task Run(string input, ITable<Contact> table, TraceWriter log)
{
    //Iterate over every value in the source table
    ContinuationToken continuationToken = null;
    do
    {   
        //retreive table values
        var contactsSegment = await table.ListEntitiesAsync(
            continuationToken: continuationToken);

        foreach (var contact in contactsSegment.Items)
        {   
            log.Info(string.Format("{0} {1}", contact.FirstName, contact.LastName));
        }

        continuationToken = contactsSegment.ContinuationToken;
    }
    while (continuationToken != null);
}
```

<!--
<a name="innodejs"></a>

### Usage in Node.js

```javascript
module.exports = function(context) {
    context.log('Node.js Queue trigger function processed', context.bindings.myQueueItem);
    context.bindings.myOutputFile = context.bindings.myInputFile;
    context.done();
};
```
-->
<a name="datasourcesettings"></a>
## データ ソースの設定

### <a name="sql-server"></a>SQL Server

以下に示したのは、Contact テーブルを作成してデータを投入するためのスクリプトです。 dataSetName は "default" です。

```sql
CREATE TABLE Contact
(
    Id int NOT NULL,
    LastName varchar(20) NOT NULL,
    FirstName varchar(20) NOT NULL,
    CONSTRAINT PK_Contact_Id PRIMARY KEY (Id)
)
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (1, 'Bitt', 'Prad') 
GO
INSERT INTO Contact(Id, LastName, FirstName)
     VALUES (2, 'Glooney', 'Ceorge') 
GO
```

### <a name="google-sheets"></a>Google シート
Google Docs に対し、`Contact` という名前のワークシートを含んだスプレッドシートを作成します。 コネクタでスプレッドシートの表示名を使用することはできません。 内部的な名前 (太字部分) を dataSetName として使う必要があります (例: `docs.google.com/spreadsheets/d/`**`1UIz545JF_cx6Chm_5HpSPVOenU4DZh4bDxbFgJOSMz0`**)。先頭行に列名として `Id`、`LastName`、`FirstName` を追加し、それ以降の行にデータを投入しましょう。

### <a name="salesforce"></a>Salesforce
dataSetName は "default" です。

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]

