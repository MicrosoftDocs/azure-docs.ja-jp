---
title: "Azure Functions における SendGrid のバインディング | Microsoft Docs"
description: "Azure Functions における SendGrid のバインディングのリファレンス"
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/16/2017
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 0cd7e7c55e77863c142800cdc11d6ea144c38293
ms.lasthandoff: 03/18/2017


---
# <a name="azure-functions-sendgrid-bindings"></a>Azure Functions における SendGrid のバインディング

この記事では、Azure Functions で SendGrid のバインディングを構成および操作する方法について説明します。 SendGrid を使用すると、Azure Functions を使って、カスタマイズされた電子メールをプログラムによって送信できます。

この記事は、Azure Functions の開発者向けリファレンス情報です。 Azure Functions を初めて使用する場合は、先に次のリソースを参照してください。

[初めての Azure 関数の作成](functions-create-first-azure-function.md)。 
[C#](functions-reference-csharp.md)、[F#](functions-reference-fsharp.md)、または [Node](functions-reference-node.md) 開発者向けリファレンス。

## <a name="functionjson-for-sendgrid-bindings"></a>SendGrid バインディング用 function.json

Azure Functions には、SendGrid 用の出力バインディングが用意されています。 SendGrid 出力バインディングを使用すると、電子メールをプログラムによって作成および送信できます。 

SendGrid バインディングでは次のプロパティがサポートされます。

- `name`: 必須 - 要求または要求本文の関数コードで使用される変数名。 戻り値が 1 つの場合、この値は ```$return``` です。 
- `type`: 必須 - "SendGrid" に設定する必要があります。
- `direction`: 必須 - "out" に設定する必要があります。
- `apiKey`: 必須 - Function App のアプリ設定に格納されている API キー名に設定する必要があります。
- `to`: 受信者の電子メール アドレス。
- `from`: 送信者の電子メール アドレス。
- `subject`: 電子メールの件名。
- `text`: 電子メールの本文。

**function.json** の例:

```json 
{
    "bindings": [
        {
            "name": "$return",
            "type": "sendGrid",
            "direction": "out",
            "apiKey" : "MySendGridKey",
            "to": "{ToEmail}",
            "from": "{FromEmail}",
            "subject": "SendGrid output bindings"
        }
    ]
}
```

> [!NOTE]
> Azure Functions では接続情報と API キーがアプリ設定として保存されるため、これらはソース管理リポジトリにチェックインされません。 このアクションにより、機密情報が保護されます。
>
>

## <a name="c-example-of-the-sendgrid-output-binding"></a>C# の SendGrid 出力バインディングの例

```csharp
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static Mail Run(TraceWriter log, string input, out Mail message)
{
     message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    personalization.AddTo(new Email("recipient@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

## <a name="node-example-of-the-sendgrid-output-binding"></a>Node の SendGrid 出力バインディングの例

```javascript
module.exports = function (context, input) {    
    var message = {
        to: "recipient@contoso.com",
        from: "sender@contoso.com",        
        subject: "Azure news",
        content: [{
            type: 'text/plain',
            value: input
        }]
    };

    context.done(null, message);
};

```

## <a name="next-steps"></a>次のステップ
Azure Functions の他のバインディングおよびトリガーについては、次を参照してください 
- [Azure Functions のトリガーとバインディングの開発者用リファレンス](functions-triggers-bindings.md)

- [Azure Functions のベスト プラクティス](functions-best-practices.md)に関するページでは、Azure Functions を作成するときに使用するベスト プラクティスが掲載されています。

- [Azure Functions 開発者向けリファレンス](functions-reference.md)のページには、関数のコーディング、トリガーの定義、バインドの定義に関するプログラマ向けのリファレンスがあります。
