---
title: "Azure Functions における Twilio のバインド | Microsoft Docs"
description: "Azure Functions で Twilio バインドを使用する方法を説明します。"
services: functions
documentationcenter: na
author: wesmc7777
manager: erikre
editor: 
tags: 
keywords: "Azure Functions, 関数, イベント処理, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: a60263aa-3de9-4e1b-a2bb-0b52e70d559b
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/20/2016
ms.author: wesmc, glenga
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2fd12dd32ed3c8479c7460cbc0a1cac3330ff4f4
ms.openlocfilehash: 9355aae6e3fbf70aae08cc829d7addd2decc44fd
ms.lasthandoff: 03/01/2017


---
# <a name="send-sms-messages-from-azure-functions-using-the-twilio-output-binding"></a>Twilio 出力バインディングを使用した Azure Functions からの SMS メッセージの送信
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

この記事では、Azure Functions で Twilio バインドを構成および使用する方法について説明します。 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

Azure Functions では、関数で SMS テキスト メッセージを送信できるように、Twilio 出力バインドをサポートしています。必要なのは、数行のコードと [Twilio](https://www.twilio.com/) アカウントです。 

## <a name="functionjson-for-the-twilio-output-binding"></a>Twilio 出力バインディングの function.json
function.json ファイルは、次のプロパティを提供します。

* `name`: Twilio SMS テキスト メッセージの関数コードで使用される変数名です。
* `type`: *"twilioSms"* に設定する必要があります。
* `accountSid`: この値には、Twilio アカウント SID を保持するアプリ設定の名前を指定する必要があります。
* `authToken`: この値には、Twilio 認証トークンを保持するアプリ設定の名前を指定する必要があります。
* `to`: この値は、SMS テキストの送信先の電話番号に設定されます。
* `from`: この値は、SMS テキストの送信元の電話番号に設定されます。
* `direction` : *"out"*に設定する必要があります。
* `body`: この値は、SMS テキスト メッセージを関数のコードで動的に設定する必要がない場合に、メッセージをハード コーディングするために使用できます。 

function.json の例:

```json
{
  "type": "twilioSms",
  "name": "message",
  "accountSid": "TwilioAccountSid",
  "authToken": "TwilioAuthToken",
  "to": "+1704XXXXXXX",
  "from": "+1425XXXXXXX",
  "direction": "out",
  "body": "Azure Functions Testing"
}
```


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Twilio 出力バインドでのサンプル C# キュー トリガー
#### <a name="synchronous"></a>同期
Azure Storage キュー トリガー用のこの同期サンプル コードは、出力パラメーターを使用して、注文を行った顧客にテキスト メッセージを送信します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    message = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    message.Body = msg;
    message.To = order.mobileNumber;
}
```

#### <a name="asynchronous"></a>非同期
Azure Storage キュー トリガー用のこの非同期サンプル コードは、注文を行った顧客にテキスト メッセージを送信します。

```cs
#r "Newtonsoft.Json"
#r "Twilio.Api"

using System;
using Newtonsoft.Json;
using Twilio;

public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
{
    log.Info($"C# Queue trigger function processed: {myQueueItem}");

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    dynamic order = JsonConvert.DeserializeObject(myQueueItem);
    string msg = "Hello " + order.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the SMSMessage variable.
    SMSMessage smsText = new SMSMessage();

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    smsText.Body = msg;
    smsText.To = order.mobileNumber;

    await message.AddAsync(smsText);
}
```

## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Twilio 出力バインドでのサンプル Node.js キュー トリガー
この Node.js の例は、注文を行った顧客にテキスト メッセージを送信します。

```javascript
module.exports = function (context, myQueueItem) {
    context.log('Node.js queue trigger function processed work item', myQueueItem);

    // In this example the queue item is a JSON string representing an order that contains the name of a 
    // customer and a mobile number to send text updates to.
    var msg = "Hello " + myQueueItem.name + ", thank you for your order.";

    // Even if you want to use a hard coded message and number in the binding, you must at least 
    // initialize the message binding.
    context.bindings.message = {};

    // A dynamic message can be set instead of the body in the output binding. In this example, we use 
    // the order information to personalize a text message to the mobile number provided for
    // order status updates.
    context.bindings.message = {
        body : msg,
        to : myQueueItem.mobileNumber
    };

    context.done();
};
```

## <a name="next-steps"></a>次のステップ
[!INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]


