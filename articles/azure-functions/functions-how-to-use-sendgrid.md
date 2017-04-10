---
title: "Azure Functions で SendGrid を使用する方法 | Microsoft Docs"
description: "Azure Functions で SendGrid を使用する方法の説明"
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 01/31/2017
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: b95fcfa1ed4ea4cf1051f4920b9c05bb40a19cab
ms.openlocfilehash: 05c9f4e4a4351219da68af8b702c25f21d7d4d02
ms.lasthandoff: 02/22/2017


---
# <a name="how-to-use-sendgrid-in-azure-functions"></a>Azure Functions で SendGrid を使用する方法

## <a name="sendgrid-overview"></a>SendGrid の概要

Azure Functions では、関数で電子メール メッセージを送信できるように、SendGrid 出力バインドをサポートしています。必要なのは、数行のコードと SendGrid アカウントです。

Azure Functions で SendGrid API を使用するには、[SendGrid アカウント](http://SendGrid.com)が必要です。 さらに、SendGrid API キーが必要です。 SendGrid アカウントにログインし、**[設定]**、**[API キー]** の順にクリックして API キーを作成します。 このキーを保存して、今後の手順で利用できるようにしておいてください。

これで、Azure Function App を作成する準備が整いました。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成 

Azure Function App は、1 つまたは複数の Azure Functions のコンテナーです。 Azure Functions 自体は、1 つの関数に過ぎません。 各 Azure 関数は、1 つのトリガーに関連付けられます。トリガーとは関数が実行されるきっかけとなるイベントです。
各関数には、任意の数の入力または出力バインドを含めることができます。 バインドとは、関数の中で使用できるサービスのことです。 SendGrid は、電子メール送信に使用できる出力バインドです。 

1. Azure Portal にログインし、[Azure Function App を作成する](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function)か、既存の Function App を開きます。 
2. Azure 関数を作成します。 簡潔にするため、手動トリガーと C# を選択します。 

 ![Azure 関数の作成](./media/functions-how-to-use-sendgrid/functions-new-function-manual-trigger-page.png)

## <a name="configure-sendgrid-for-use-in-an-azure-function-app"></a>Azure Function App で使用する SendGrid を構成する

SendGrid API キーを関数内で使用するには、アプリの設定として保存する必要があります。 ApiKey フィールドは、実際の SendGrid API キーではなく、実際の API キーを表すように定義するアプリの設定です。 キーをこのように保存すると、ソース コード管理でチェックインされる可能性のあるコードやファイルからキーを切り離すことになるため、セキュリティが確保されます。

- Function App の **[アプリケーションの設定]** で **AppSettings** キーを作成します。

 ![Azure 関数の作成](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-api-key-app-settings.png)

## <a name="configure-sendgrid-output-bindings"></a>SendGrid 出力バインドの構成

SendGrid は、Azure 関数の出力バインドとして使用できます。 SendGrid の出力バインドを作成するには、以下の手順を実行します。

1. Azure Portal で、関数の **[統合]** タブを開きます。
2. **[新規出力]** をクリックし、SendGrid の出力バインドを作成します。
3. **API キー**と **[メッセージ パラメーター名]** プロパティを入力します。 その他のプロパティもここで入力できます。入力はせずにコーディングすることも可能です。 これらの設定は、既定値として使用できます。

 ![SendGrid 出力バインドの構成](./media/functions-how-to-use-sendgrid/functions-configure-sendgrid-output-bindings.png)

バインドを関数に追加することによって、**function.json** と呼ばれるファイルが関数のフォルダーに作成されます。 このファイルには Azure 関数の **[統合]** タブに表示されていた情報と同じものが含まれていますが、書式は Json フォーマットになっています。 **ApiKey**、**message**、**from** フィールドを設定することで、**function.json** ファイルに次のエントリが作成されます。 

```json
{
  "bindings": [    
    {
      "type": "sendGrid",
      "name": "message",
      "apiKey": "SendGridKey",
      "direction": "out",
      "from": "azure@contoso.com"
    }
  ],
  "disabled": false
}
```

このファイルをユーザーが直接編集することもできます。

Function App と関数の作成および構成が完了したら、電子メールを送信するコードを記述できます。

## <a name="write-code-that-creates-and-sends-email"></a>電子メールを作成して送信するためのコードの記述

SendGrid API には、電子メールを作成して送信するのに必要なコマンドがすべて含まれています。  

- 関数内のコードを次のコードに置き換えます。

```cs
#r "SendGrid"
using System;
using SendGrid.Helpers.Mail;

public static void Run(TraceWriter log, string input, out Mail message)
{
    message = new Mail
    {        
        Subject = "Azure news"          
    };

    var personalization = new Personalization();
    // change to email of recipient
    personalization.AddTo(new Email("MoreEmailPlease@contoso.com"));   

    Content content = new Content
    {
        Type = "text/plain",
        Value = input
    };
    message.AddContent(content);
    message.AddPersonalization(personalization);
}
```

SendGrid アセンブリを参照する ```#r``` ディレクティブが最初の行に含まれていることに注意してください。 その後、```using``` ステートメントを使用して、名前空間内のオブジェクトに簡単にアクセスできます。 コードの中では、電子メールを構成する ```Mail```、```Personalization```、```Content``` オブジェクトのインスタンスをSendGrid API から作成します。 メッセージを返すと、そのメッセージは SendGrid によって配信されます。 

関数の署名には、```message``` という名前の ```Mail``` 型の追加出力パラメーターも含まれます。 入力と出力のバインドは、コード内で関数パラメーターとして表現されます。 

2. **[テスト]** をクリックし、**[要求本文]** フィールドにメッセージを入力し、**[実行]** ボタンをクリックしてコードをテストします。

 ![コードのテスト](./media/functions-how-to-use-sendgrid/functions-develop-test-sendgrid.png)

3. 電子メールを確認し、SendGrid によって電子メールが送信されることを確認します。 手順 1 のコード内のアドレスにメールが送信され、**[要求本文]** のメッセージが含まれているはずです。

## <a name="next-steps"></a>次のステップ
この記事では、SendGrid サービスを使用して電子メールを作成し、送信する方法を説明しました。 アプリで Azure Functions を使用する方法についてさらに詳しく知りたい場合には、以下のトピックを参照してください。 

- [Azure Functions のベスト プラクティス](functions-best-practices.md)に関するページでは、Azure Functions を作成するときに使用するベスト プラクティスが掲載されています。

- [Azure Functions 開発者向けリファレンス](functions-reference.md)のページには、関数のコーディング、トリガーの定義、バインドの定義に関するプログラマ向けのリファレンスがあります。

- [Azure Functions のテスト](functions-test-a-function.md)に関するページでは、関数をテストするためのさまざまなツールと手法について説明しています。
