---
title: カスタム ルールと通知を使用して Azure IoT Central を拡張する |Microsoft Docs
description: ソリューション開発者は、デバイスがテレメトリの送信を停止したときに電子メール通知を送信するように IoT Central アプリケーションを構成します。 このソリューションでは、Azure Stream Analytics、Azure Functions、SendGrid を使用します。
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: bdaa08e8c3b104c7269c1fb4169779d98b4e0880
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895728"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid-preview-features"></a>Stream Analytics、Azure Functions、SendGrid を使用してカスタム ルールで Azure IoT Central を拡張する (プレビュー機能)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

この攻略ガイドでは、ソリューション開発者が、カスタム ルールと通知を使用して IoT Central アプリケーションを拡張する方法を説明します。 この例では、デバイスがテレメトリの送信を停止したときのオペレーターへの通知の送信を示します。 このソリューションでは、[Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) クエリを使用して、デバイスが利用統計情報の送信をいつ停止したかを検出します。 Stream Analytics ジョブは、[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/) を使用して通知メールを送信するために、[Azure Functions](https://docs.microsoft.com/azure/azure-functions/) を使用します。

この攻略ガイドでは、既に組み込みのルールとアクションを使用して実行できることを超えて IoT Central を拡張する方法を示します。

この攻略ガイドでは、以下の方法について説明します。

* *継続的データ エクスポート*を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="iot-central-application"></a>IoT Central アプリケーション

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、次の設定を使用して IoT Central アプリケーションを作成します。

| Setting | 値 |
| ------- | ----- |
| 支払プラン | 従量課金制 |
| アプリケーション テンプレート | ストア内分析 – 条件監視 |
| アプリケーション名 | 既定値を受け入れるか、独自の名前を選択します |
| URL | 既定値を受け入れるか、独自の一意の URL プレフィックスを選択します |
| Directory | Azure Active Directory テナント |
| Azure サブスクリプション | お使いの Azure サブスクリプション |
| リージョン | 最も近いリージョン |

この記事の例とスクリーンショットでは、**米国**リージョンを使用します。 近くの場所を選択して、必ずすべてのリソースを同じリージョン内に作成してください。

このアプリケーション テンプレートには、テレメトリを送信する 2 つのシミュレートされたサーモスタット デバイスが含まれます。

### <a name="resource-group"></a>Resource group

作成するその他のリソースを格納するため、**DetectStoppedDevices** という [リソース グループを Azure portal で作成](https://portal.azure.com/#create/Microsoft.ResourceGroup)します。 Azure リソースは IoT Central アプリケーションと同じ場所に作成してください。

### <a name="event-hubs-namespace"></a>Event Hubs 名前空間

以下の設定を使用して、[Azure portal で Event Hubs 名前空間を作成](https://portal.azure.com/#create/Microsoft.EventHub)します。

| Setting | 値 |
| ------- | ----- |
| 名前    | 名前空間名を選択します |
| Pricing tier | Basic |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | East US |
| スループット ユニット | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics ジョブ

以下の設定を使用して、[Azure portal で Stream Analytics ジョブを作成](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)します。

| Setting | 値 |
| ------- | ----- |
| 名前    | ジョブ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | 米国東部 |
| ホスティング環境 | クラウド |
| [ストリーミング ユニット] | 3 |

### <a name="function-app"></a>関数アプリ

以下の設定を使用して、[Azure portal で関数アプリを作成](https://portal.azure.com/#create/Microsoft.FunctionApp)します。

| Setting | 値 |
| ------- | ----- |
| アプリの名前    | 関数アプリ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| ホスティング プラン | 従量課金プラン |
| Location | East US |
| ランタイム スタック | .NET |
| Storage | 新規作成 |

### <a name="sendgrid-account"></a>SendGrid アカウント

以下の設定を使用して、[Azure portal で SendGrid アカウントを作成](https://portal.azure.com/#create/Sendgrid.sendgrid)します。

| Setting | 値 |
| ------- | ----- |
| 名前    | SendGrid アカウント名を選択します |
| パスワード | パスワードを作成します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Pricing tier | F1 Free |
| 連絡先情報 | 必須情報を入力します |

必要なすべてのリソースを作成すると、次のスクリーンショットのような **DetectStoppedDevices** リソース グループが表示されます。

![停止したデータのリソース グループを検出](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>イベント ハブの作成

テレメトリをイベント ハブに連続してエクスポートするように IoT Central アプリケーションを構成できます。 このセクションでは、IoT Central アプリケーションからテレメトリを受信するイベント ハブを作成します。 このイベント ハブは、処理するためにテレメトリを Stream Analytics ジョブに配信します。

1. Azure portal で Event Hubs 名前空間に移動し、 **[+ イベント ハブ]** を選択します。
1. イベント ハブに **centralexport** という名前を付けて、 **[作成]** を選択します。

次のスクリーンショットのような Event Hubs 名前空間が作成されます。

![Event Hubs 名前空間](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API キーを取得する

関数アプリでは、メール メッセージを送信するために SendGrid API キーが必要です。 SendGrid API キーを作成するには、次の手順に従います。

1. Azure portal で、SendGrid アカウントに移動します。 次に、 **[管理]** を選択して SendGrid アカウントにアクセスします。
1. SendGrid アカウントで **[設定]** 、 **[API キー]** の順に選択します。 **[API キーの作成]** を選択します。

    ![SendGrid API キーを作成する](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. **[API キーの作成]** ページで、 **[フル アクセス]** のアクセス許可を持つ「**AzureFunctionAccess**」という名前のキーを作成します。
1. API キーをメモしておいてください。これは関数アプリを構成するときに必要になります。

## <a name="define-the-function"></a>関数を定義する

このソリューションでは、Azure Functions アプリを使用して、Stream Analytics ジョブが停止したデバイスを検出したときにメール通知を送信します。 関数アプリを作成するには、次の手順に従います。

1. Azure portal で、**DetectStoppedDevices** リソース グループ内の **[App Service]** インスタンスに移動します。
1. **+** を選択して、新しい関数を作成します。
1. **[開発環境を選択する]** ページで **[ポータル内]** を選択してから、 **[続行]** を選択します。
1. **[関数の作成]** ページで、 **[Webhook + API]** を選択してから **[作成]** を選択します。

ポータルで **HttpTrigger1** という既定の関数が作成されます。

![既定の HTTP トリガー関数](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>関数バインドを構成する

SendGrid を使用してメールを送信するには、次のように関数バインドを構成する必要があります。

1. **[統合]** を選択し、出力 **[HTTP ($return)]** を選択して、 **[削除]** を選択します。
1. **[+ 新しい出力]** を選択し、 **[SendGrid]** を選択して、 **[選択]** を選択します。 **[インストール]** を選択して SendGrid 拡張機能をインストールします。
1. インストールが完了したら、 **[関数の戻り値を使用する]** を選択します。 メール通知を受信する有効な **[宛先アドレス]** を追加します。  メール送信者として使用する有効な **[差出人アドレス]** を追加します。
1. **[SendGrid API キーのアプリ設定]** の横の **[新規]** を選択します。 キーとして「**SendGridAPIKey**」と入力し、値として前にメモした SendGrid API キーを入力します。 **[作成]** を選択します。
1. **[保存]** を選択して、関数の SendGrid バインドを保存します。

統合設定は次のスクリーンショットのようになります。

![関数アプリの統合](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>関数コードを追加する

関数を実装するには、次のように、受信 HTTP 要求を解析してメールを送信するための C# コードを追加します。

1. 関数アプリで **HttpTrigger1** 関数を選択し、C# コードを次のコードに置き換えます。

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    新しいコードを保存するまで、エラー メッセージが表示される場合があります。

1. **[保存]** を選択して関数を保存します。

### <a name="test-the-function-works"></a>関数の動作をテストする

ポータルで関数をテストするには、まずコード エディターの下部にある **[ログ]** を選択します。 次に、コード エディターの右側にある **[テスト]** をクリックします。 **[要求本文]** として次の JSON を使用します。

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

**[ログ]** パネルに関数ログのメッセージが表示されます。

![関数のログ出力](media/howto-create-custom-rules/function-app-logs.png)

数分後に、 **[宛先]** メール アドレスに次の内容のメールが届きます。

```txt
The following device(s) have stopped sending telemetry:

Device ID   Time
test-device-1   2019-05-02T14:23:39.527Z
test-device-2   2019-05-02T14:23:50.717Z
test-device-3   2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics クエリを追加する

このソリューションでは、Stream Analytics クエリを使用して、デバイスが 120 秒以上にわたってテレメトリの送信を停止したときに検出します。 このクエリは、入力としてイベント ハブからのテレメトリを使用します。 ジョブはクエリの結果を関数アプリに送信します。 このセクションでは、Stream Analytics ジョブを構成します。

1. Azure portal で Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** で **[入力]** を選択し、 **[+ ストリーム入力の追加]** を選択してから、 **[イベント ハブ]** を選択します。
1. 次の表の情報を使用して、以前に作成したイベント ハブを使用して入力を構成してから、 **[保存]** を選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 入力のエイリアス | centraltelemetry |
    | サブスクリプション | 該当するサブスクリプション |
    | Event Hub 名前空間 | Event Hub 名前空間 |
    | イベント ハブ名 | 既存のものを使用 - **centralexport** |

1. **[ジョブ トポロジ]** で **[出力]** を選択し、 **[+ 追加]** を選択してから、 **[Azure 関数]** を選択します。
1. 次の表の情報を使用して出力を構成してから、 **[保存]** を選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 出力エイリアス | emailnotification |
    | サブスクリプション | 該当するサブスクリプション |
    | 関数アプリ | 関数アプリ |
    | Function  | HttpTrigger1 |

1. **[ジョブ トポロジ]** で **[クエリ]** を選び、既存のクエリを次の SQL に置き換えます。

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. **[保存]** を選択します。
1. Stream Analytics ジョブを開始するには、 **[概要]** 、 **[開始]** 、 **[今すぐ]** 、 **[開始]** の順に選択します。

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>IoT Central でエクスポートを構成する

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、Contoso テンプレートから作成した IoT Central アプリケーションに移動します。 このセクションでは、シミュレートされたデバイスからイベント ハブにテレメトリをストリーム配信するようにアプリケーションを構成します。 エクスポートを構成するには、次の手順に従います。

1. **[データのエクスポート]** ページに移動し、 **[+ 新規]** を選択してから、 **[Azure Event Hubs]** を選択します。
1. 以下の設定を使用してエクスポートを構成してから、 **[保存]** を選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 表示名 | Event Hubs へのエクスポート |
    | 有効 | On |
    | Event Hubs 名前空間 | Event Hubs 名前空間の名前 |
    | イベント ハブ | centralexport |
    | 測定 | On |
    | デバイス | オフ |
    | デバイス テンプレート | オフ |

![継続的データ エクスポート構成](media/howto-create-custom-rules/cde-configuration.png)

エクスポートの状態が **[実行中]** になるまで待ってから、続行します。

## <a name="test"></a>テスト

ソリューションをテストするために、IoT Central からシミュレートされた停止しているデバイスへの継続的データ エクスポートを無効にすることができます。

1. IoT Central アプリケーションで **[継続的データ エクスポート]** ページに移動し、 **[Event Hubs にエクスポート]** のエクスポート構成を選択します。
1. **[有効]** を **[オフ]** に設定し、 **[保存]** を選択します。
1. 少なくとも 2 分後に、次の例の内容のような 1 つまたは複数のメールが **[宛先]** メール アドレスに届きます。

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>片付ける

この手順の後で片付けて、不要なコストがかからないようにするには、Azure portal で **DetectStoppedDevices** リソース グループを削除します。

アプリケーション内の **[管理]** ページから IoT Central アプリケーションを削除することができます。

## <a name="next-steps"></a>次の手順

この攻略ガイドで学習した内容は次のとおりです。

* *継続的データ エクスポート*を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

カスタム ルールと通知を作成する方法がわかったので、次は [カスタム分析を使用して Azure IoT Central を拡張する](howto-create-custom-analytics.md)方法を学習することをお勧めします。
