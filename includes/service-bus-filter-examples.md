---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 01/22/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7ddc0234accd9f434aad850d2404e2f3001c4bae
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742687"
---
## <a name="examples"></a>例

### <a name="filter-on-system-properties"></a>システム プロパティのフィルター処理
フィルター内でシステム プロパティを参照するには、`sys.<system-property-name>` の形式を使用します。 

```csharp
sys.Label LIKE '%bus%'`
sys.messageid = 'xxxx'
sys.correlationid like 'abc-%'
```

### <a name="filter-on-message-properties"></a>メッセージ プロパティのフィルター処理
フィルター内でメッセージ プロパティを使用する例をこちらに示します。 メッセージ プロパティには、`user.property-name` または単に `property-name` を使用してアクセスできます。

```csharp
MessageProperty = 'A'
SuperHero like 'SuperMan%'
```

### <a name="filter-on-message-properties-with-special-characters"></a>特殊文字を含むメッセージ プロパティのフィルター処理
メッセージ プロパティ名に特殊文字が含まれている場合は、二重引用符 (`"`) を使用してプロパティ名を囲みます。 たとえば、プロパティ名が `"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName"` である場合は、フィルターで次の構文を使用します。 

```csharp
"http://schemas.microsoft.com/xrm/2011/Claims/EntityLogicalName" = 'account'
```

### <a name="filter-on-message-properties-with-numeric-values"></a>数値を含むメッセージ プロパティのフィルター処理
次の例は、数値を含むプロパティをフィルター内で使用する方法を示しています。 

```csharp
MessageProperty = 1
MessageProperty > 1
MessageProperty > 2.08
MessageProperty = 1 AND MessageProperty2 = 3
MessageProperty = 1 OR MessageProperty2 = 3
```

### <a name="parameter-based-filters"></a>パラメーター ベースのフィルター
ここでは、パラメーター ベースのフィルターの使用例をいくつか示します。 これらの例では、`DataTimeMp` は `DateTime` 型のメッセージ プロパティで、`@dtParam` は `DateTime` オブジェクトとしてフィルターに渡されるパラメーターです。

```csharp
DateTimeMp < @dtParam
DateTimeMp > @dtParam

(DateTimeMp2-DateTimeMp1) <= @timespan //@timespan is a parameter of type TimeSpan
DateTimeMp2-DateTimeMp1 <= @timespan
```

### <a name="using-in-and-not-in"></a>IN と NOT IN の使用

```csharp
StoreId IN('Store1', 'Store2', 'Store3')"

sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'

sys.To NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Store8')
```

C# のサンプルについては、[GitHub のトピック フィルターのサンプル](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Azure.Messaging.ServiceBus/BasicSendReceiveTutorialwithFilters)を参照してください。


### <a name="set-rule-action-for-a-sql-filter"></a>SQL フィルターのルール アクションの設定

```csharp
// instantiate the ManagementClient
this.mgmtClient = new ManagementClient(connectionString);

// create the SQL filter
var sqlFilter = new SqlFilter("source = @stringParam");

// assign value for the parameter
sqlFilter.Parameters.Add("@stringParam", "orders");

// instantiate the Rule = Filter + Action
var filterActionRule = new RuleDescription
{
    Name = "filterActionRule",
    Filter = sqlFilter,
    Action = new SqlRuleAction("SET source='routedOrders'")
};

// create the rule on Service Bus
await this.mgmtClient.CreateRuleAsync(topicName, subscriptionName, filterActionRule);
```

### <a name="correlation-filter-using-correlationid"></a>CorrelationID を使用した相関関係フィルター

```csharp
new CorrelationFilter("Contoso");
```

`CorrelationID` が `Contoso` に設定されたメッセージをフィルター処理します。 

### <a name="correlation-filter-using-system-and-user-properties"></a>システムおよびユーザーのプロパティを使用した相関関係フィルター

```csharp
var filter = new CorrelationFilter();
filter.Label = "Important";
filter.ReplyTo = "johndoe@contoso.com";
filter.Properties["color"] = "Red";
```

これは `sys.ReplyTo = 'johndoe@contoso.com' AND sys.Label = 'Important' AND color = 'Red'` と同等です。

