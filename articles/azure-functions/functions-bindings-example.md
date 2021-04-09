---
title: Azure Functions のトリガーとバインドの例
description: Azure Functions のバインドを構成する方法について学習します
author: craigshoemaker
ms.topic: reference
ms.custom: devx-track-csharp
ms.date: 02/18/2019
ms.author: cshoe
ms.openlocfilehash: c95524a5de6696bd0ffe7463451d152a9d3a19b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88205967"
---
# <a name="azure-functions-trigger-and-binding-example"></a>Azure Functions のトリガーとバインドの例

この記事では、Azure Functions の[トリガーとバインド](./functions-triggers-bindings.md)を構成する方法について説明します。

Azure Queue Storage に新しいメッセージが表示されるたびに Azure Table Storage に新しい行を書き込むとします。 このシナリオは、Azure Queue Storage トリガーと Azure Table Storage の出力バインドを使用して実装できます。 

このシナリオ用の *function.json* ファイルを次に示します。 

```json
{
  "bindings": [
    {
      "type": "queueTrigger",
      "direction": "in",
      "name": "order",
      "queueName": "myqueue-items",
      "connection": "MY_STORAGE_ACCT_APP_SETTING"
    },
    {
      "type": "table",
      "direction": "out",
      "name": "$return",
      "tableName": "outTable",
      "connection": "MY_TABLE_STORAGE_ACCT_APP_SETTING"
    }
  ]
}
```

`bindings` 配列の最初の要素は、Queue Storage トリガーです。 `type` および `direction` プロパティは、トリガーを識別します。 `name` プロパティは、キュー メッセージの内容を受信する関数パラメーターを識別します。 監視するキューの名前は `queueName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

`bindings` 配列の 2 番目の要素は、Azure Table Storage の出力バインドです。 `type` および `direction` プロパティは、バインドを識別します。 `name` プロパティは、その関数が (この場合は関数戻り値を使用して) 新しいテーブル行を提供する方法を指定します。 テーブルの名前は `tableName` に含まれ、接続文字列は `connection` で識別されるアプリ設定に含まれています。

Azure ポータルで *function.json* の内容を表示して編集するには、関数の **[統合]** タブにある **[詳細エディター]** オプションをクリックします。

> [!NOTE]
> `connection` の値は、接続文字列自体ではなく、接続文字列を含むアプリ設定の名前です。 "*function.json* にサービス シークレットを含めない" というベスト プラクティスを適用するために、バインドでは、アプリ設定に格納された接続文字列を使用します。

## <a name="c-script-example"></a>C# スクリプトの例

このトリガーとバインドで動作する C# のスクリプト コードを次に示します。 キュー メッセージの内容を提供するパラメーターの名前が `order` であることに注意してください。*function.json* の `name` プロパティ値が `order` であるため、この名前が必要になります。 

```cs
#r "Newtonsoft.Json"

using Microsoft.Extensions.Logging;
using Newtonsoft.Json.Linq;

// From an incoming queue message that is a JSON object, add fields and write to Table storage
// The method return value creates a new row in Table Storage
public static Person Run(JObject order, ILogger log)
{
    return new Person() { 
            PartitionKey = "Orders", 
            RowKey = Guid.NewGuid().ToString(),  
            Name = order["Name"].ToString(),
            MobileNumber = order["MobileNumber"].ToString() };  
}
 
public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

## <a name="javascript-example"></a>JavaScript の例

同じ *function.json* ファイルを JavaScript 関数でも使用できます。

```javascript
// From an incoming queue message that is a JSON object, add fields and write to Table Storage
// The second parameter to context.done is used as the value for the new row
module.exports = function (context, order) {
    order.PartitionKey = "Orders";
    order.RowKey = generateRandomId(); 

    context.done(null, order);
};

function generateRandomId() {
    return Math.random().toString(36).substring(2, 15) +
        Math.random().toString(36).substring(2, 15);
}
```

## <a name="class-library-example"></a>クラス ライブラリの例

クラス ライブラリでは、同じトリガーとバインディング情報 (&mdash;キュー名とテーブル名、ストレージ アカウント、入力と出力の関数パラメーター&mdash;) が function.json ファイルではなく、属性によって提供されます。 次に例を示します。

```csharp
public static class QueueTriggerTableOutput
{
    [FunctionName("QueueTriggerTableOutput")]
    [return: Table("outTable", Connection = "MY_TABLE_STORAGE_ACCT_APP_SETTING")]
    public static Person Run(
        [QueueTrigger("myqueue-items", Connection = "MY_STORAGE_ACCT_APP_SETTING")]JObject order,
        ILogger log)
    {
        return new Person() {
                PartitionKey = "Orders",
                RowKey = Guid.NewGuid().ToString(),
                Name = order["Name"].ToString(),
                MobileNumber = order["MobileNumber"].ToString() };
    }
}

public class Person
{
    public string PartitionKey { get; set; }
    public string RowKey { get; set; }
    public string Name { get; set; }
    public string MobileNumber { get; set; }
}
```

これで、Azure Queue によってトリガーされ、データを Azure Table Storage に出力する実用的な関数が完成しました。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Functions のバインド式のパターン](./functions-bindings-expressions-patterns.md)
