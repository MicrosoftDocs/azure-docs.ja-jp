---
title: "ジェネリック webhook でトリガーされる Azure 関数の作成 | Microsoft Docs"
description: "Azure Functions を使用して、Azure で webhook によって呼び出されるサーバーなしの関数を作成します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: fafc10c0-84da-4404-b4fa-eea03c7bf2b1
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/12/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: f283f8d79c5ae5fb6a72c84c9e9edb7bb8de4a83
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-function-triggered-by-a-generic-webhook"></a>ジェネリック webhook でトリガーされる関数の作成

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、サーバーレス環境でコードを実行できます。 たとえば、Azure Monitor によって生成されたアラートにトリガーされる関数を構成することができます。 このトピックでは、リソース グループがサブスクリプションに追加されたときに C# コードを実行する方法を示します。   

![Azure Portal でのジェネリック webhook によってトリガーされる関数](./media/functions-create-generic-webhook-triggered-function/function-completed.png)

## <a name="prerequisites"></a>前提条件 

このチュートリアルを完了するには、以下が必要です。

+ Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

次に、新しい Function App で関数を作成します。

## <a name="create-function"></a>ジェネリック webhook でトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 この関数が関数アプリで初めての関数の場合は、**[カスタム関数]** を選択します。 関数テンプレートの完全なセットが表示されます。

    ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-generic-webhook-triggered-function/add-first-function.png)

2. **Generic WebHook - C#** テンプレートを選択します。 C# 関数の名前を入力し、**[作成]** を選択します。

     ![Azure Portal でのジェネリック webhook によってトリガーされる関数の作成](./media/functions-create-generic-webhook-triggered-function/functions-create-generic-webhook-trigger.png) 

2. 新しい関数で、**[</> 関数の URL の取得]** をクリックし、値をコピーして保存します。 この値を使用して、webhook を構成します。 

    ![関数コードの確認](./media/functions-create-generic-webhook-triggered-function/functions-copy-function-url.png)
         
次に、Azure Monitor のアクティビティ ログ アラートに webhook エンドポイントを作成します。 

## <a name="create-an-activity-log-alert"></a>アクティビティ ログ アラートの作成

1. Azure Portal で **[Monitor]\(監視\)** サービスに移動し、**[アラート]** を選択し、**[アクティビティ ログ アラートの追加]** をクリックします。   

    ![監視](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert.png)

2. 次の表で指定されている設定を使用してください。

    ![アクティビティ ログ アラートの作成](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings.png)

    | 設定      |  推奨値   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アクティビティ ログ アラート名** | resource-group-create-alert | アクティビティ ログ アラートの名前。 |
    | **サブスクリプション** | 該当するサブスクリプション | このチュートリアルで使用しているサブスクリプション。 | 
    |  **リソース グループ** | myResourceGroup | アラート リソースのデプロイ先のリソース グループ。 関数アプリと同じリソース グループを使用すると、チュートリアルの完了後に簡単にクリーンアップできます。 |
    | **イベント カテゴリ** | 管理 | このカテゴリには、Azure リソースに対する変更が含まれています。  |
    | **リソースの種類** | リソース グループ | アラートを、リソース グループのアクティビティを対象にしてフィルター処理します。 |
    | **リソース グループ**<br/>および**リソース** | すべて | すべてのリソースを監視します。 |
    | **操作名** | リソース グループの作成 | アラートを、作成操作を対象にしてフィルター処理します。 |
    | **Level** | 情報 | 情報レベルのアラートを含めます。 | 
    | **状態** | Succeeded | アラートを、正常に完了したアクションを対象にしてフィルター処理します。 |
    | **アクション グループ** | 新規 | アラートが発生したときに実行するアクションを定義する、新しいアクション グループを作成します。 |
    | **アクション グループ名** | function-webhook | アクション グループを識別する名前。  | 
    | **短い名前** | funcwebhook | アクション グループの短い名前。 |  

3. **[アクション]** で、次の表で指定されている設定を使用してアクションを追加します。 

    ![アクション グループを追加する](./media/functions-create-generic-webhook-triggered-function/functions-monitor-add-alert-settings-2.png)

    | 設定      |  推奨値   | Description                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **名前** | CallFunctionWebhook | アクションの名前。 |
    | **アクションの種類** | Webhook | アラートへの対応は、webhook の URL を呼び出すことです。 |
    | **詳細** | 関数の URL | 前にコピーしておいた、関数の webhook URL を貼り付けます。 |v

4. **[OK]** をクリックして、アラートとアクション グループを作成します。  

webhook は、サブスクリプションでリソース グループが作成されるときに呼び出されるようになりました。 次に、要求の本文内の JSON ログ データを処理するように、関数のコードを更新します。   

## <a name="update-the-function-code"></a>関数コードを更新する

1. ポータルで関数アプリに戻り、関数を展開します。 

2. ポータルで関数内の C# スクリプト コードを次のコードに置き換えます。

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"Webhook was triggered!");
    
        // Get the activityLog object from the JSON in the message body.
        string jsonContent = await req.Content.ReadAsStringAsync();
        JToken activityLog = JObject.Parse(jsonContent.ToString())
            .SelectToken("data.context.activityLog");
    
        // Return an error if the resource in the activity log isn't a resource group. 
        if (activityLog == null || !string.Equals((string)activityLog["resourceType"], 
            "Microsoft.Resources/subscriptions/resourcegroups"))
        {
            log.Error("An error occured");
            return req.CreateResponse(HttpStatusCode.BadRequest, new
            {
                error = "Unexpected message payload or wrong alert received."
            });
        }
    
        // Write information about the created resource group to the streaming log.
        log.Info(string.Format("Resource group '{0}' was {1} on {2}.",
            (string)activityLog["resourceGroupName"],
            ((string)activityLog["subStatus"]).ToLower(), 
            (DateTime)activityLog["submissionTimestamp"]));
    
        return req.CreateResponse(HttpStatusCode.OK);    
    }
    ```

これで、サブスクリプションに新しいリソース グループを作成して、関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal の左側にあるリソース グループのアイコンをクリックし、**[+ 追加]** を選択して、**リソース グループ名**を入力します。**[作成]** を選択して、空のリソース グループを作成します。
    
    ![リソース グループを作成します。](./media/functions-create-generic-webhook-triggered-function/functions-create-resource-group.png)

2. 関数に戻り、**[ログ]** ウィンドウを展開します。 リソース グループが作成されると、アクティビティ ログ アラートによって webhook がトリガーされ、関数が実行されます。 新しいリソース グループの名前がログに書き込まれたのがわかります。  

    ![テスト アプリケーション設定を追加します。](./media/functions-create-generic-webhook-triggered-function/function-view-logs.png)

3. (省略可能) 前に戻って、作成したリソース グループを削除します。 このアクティビティは関数をトリガーしないことに注意してください。 これは、削除操作がアラートのフィルターによって除外されるためです。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

ジェネリック webhook からの要求が受信されたときに実行される関数を作成しました。 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

webhook トリガーの詳細については、「[Azure Functions における HTTP と Webhook のバインド](functions-bindings-http-webhook.md)」を参照してください。 C# での関数開発の詳細については、「[Azure Functions C# スクリプト開発者向けリファレンス](functions-reference-csharp.md)」を参照してください。

