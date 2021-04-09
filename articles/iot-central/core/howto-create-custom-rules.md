---
title: カスタム ルールと通知を使用して Azure IoT Central を拡張する |Microsoft Docs
description: ソリューション開発者は、デバイスがテレメトリの送信を停止したときに電子メール通知を送信するように IoT Central アプリケーションを構成します。 このソリューションでは、Azure Stream Analytics、Azure Functions、SendGrid を使用します。
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 6146676121bac0089d5f520d60a97d74567a32bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179342"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Stream Analytics、Azure Functions、SendGrid を使用してカスタム ルールで Azure IoT Central を拡張する

この攻略ガイドでは、ソリューション開発者が、カスタム ルールと通知を使用して IoT Central アプリケーションを拡張する方法を説明します。 この例では、デバイスがテレメトリの送信を停止したときのオペレーターへの通知の送信を示します。 このソリューションでは、[Azure Stream Analytics](../../stream-analytics/index.yml) クエリを使用して、デバイスが利用統計情報の送信をいつ停止したかを検出します。 Stream Analytics ジョブは、[SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/) を使用して通知メールを送信するために、[Azure Functions](../../azure-functions/index.yml) を使用します。

この攻略ガイドでは、既に組み込みのルールとアクションを使用して実行できることを超えて IoT Central を拡張する方法を示します。

この攻略ガイドでは、以下の方法について説明します。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

## <a name="prerequisites"></a>前提条件

このハウツー ガイドの手順を完了するには、アクティブな Azure サブスクリプションが必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

### <a name="iot-central-application"></a>IoT Central アプリケーション

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、次の設定を使用して IoT Central アプリケーションを作成します。

| 設定 | 値 |
| ------- | ----- |
| 料金プラン | Standard |
| アプリケーション テンプレート | ストア内分析 – 条件監視 |
| アプリケーション名 | 既定値を受け入れるか、独自の名前を選択します |
| URL | 既定値を受け入れるか、独自の一意の URL プレフィックスを選択します |
| ディレクトリ | Azure Active Directory テナント |
| Azure サブスクリプション | お使いの Azure サブスクリプション |
| リージョン | 最も近いリージョン |

この記事の例とスクリーンショットでは、**米国** リージョンを使用します。 近くの場所を選択して、必ずすべてのリソースを同じリージョン内に作成してください。

このアプリケーション テンプレートには、テレメトリを送信する 2 つのシミュレートされたサーモスタット デバイスが含まれます。

### <a name="resource-group"></a>Resource group

作成するその他のリソースを格納するため、**DetectStoppedDevices** という [リソース グループを Azure portal で作成](https://portal.azure.com/#create/Microsoft.ResourceGroup)します。 Azure リソースは IoT Central アプリケーションと同じ場所に作成してください。

### <a name="event-hubs-namespace"></a>Event Hubs 名前空間

以下の設定を使用して、[Azure portal で Event Hubs 名前空間を作成](https://portal.azure.com/#create/Microsoft.EventHub)します。

| 設定 | 値 |
| ------- | ----- |
| Name    | 名前空間名を選択します |
| Pricing tier | Basic |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | East US |
| スループット単位 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics ジョブ

以下の設定を使用して、[Azure portal で Stream Analytics ジョブを作成](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)します。

| 設定 | 値 |
| ------- | ----- |
| Name    | ジョブ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| Location | 米国東部 |
| ホスティング環境 | クラウド |
| [ストリーミング ユニット] | 3 |

### <a name="function-app"></a>関数アプリ

以下の設定を使用して、[Azure portal で関数アプリを作成](https://portal.azure.com/#create/Microsoft.FunctionApp)します。

| 設定 | 値 |
| ------- | ----- |
| アプリの名前    | 関数アプリ名を選択します |
| サブスクリプション | 該当するサブスクリプション |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| ホスティング プラン | 従量課金プラン |
| Location | East US |
| ランタイム スタック | .NET |
| ストレージ | 新規作成 |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid アカウントと API キー

Sendgrid アカウントをお持ちでない場合は、開始する前に[無料アカウント](https://app.sendgrid.com/)を作成してください。

1. 左側のメニューにある Sendgrid ダッシュボードの [設定] で、 **[API キー]** を選択します。
1. **[API キーの作成]** をクリックします。
1. 新しい API キーに **AzureFunctionAccess** という名前を指定します。
1. **[Create & View]\(作成して表示\)** をクリックします。

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="SendGrid API キーの作成のスクリーン ショット。":::

この後、API キーが付与されます。 後で使用できるようにこの文字列を保存します。

## <a name="create-an-event-hub"></a>イベント ハブの作成

テレメトリをイベント ハブに連続してエクスポートするように IoT Central アプリケーションを構成できます。 このセクションでは、IoT Central アプリケーションからテレメトリを受信するイベント ハブを作成します。 このイベント ハブは、処理するためにテレメトリを Stream Analytics ジョブに配信します。

1. Azure portal で Event Hubs 名前空間に移動し、 **[+ イベント ハブ]** を選択します。
1. イベント ハブに **centralexport** という名前を付けて、 **[作成]** を選択します。

次のスクリーンショットのような Event Hubs 名前空間が作成されます。 

:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Event Hubs 名前空間のスクリーンショット。" border="false":::


## <a name="define-the-function"></a>関数を定義する

このソリューションでは、Azure Functions アプリを使用して、Stream Analytics ジョブが停止したデバイスを検出したときにメール通知を送信します。 関数アプリを作成するには、次の手順に従います。

1. Azure portal で、**DetectStoppedDevices** リソース グループ内の **[App Service]** インスタンスに移動します。
1. **+** を選択して、新しい関数を作成します。
1. **[HTTP トリガー]** を選択します。
1. **[追加]** を選択します。

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="既定の HTTP トリガー関数の画像"::: 

## <a name="edit-code-for-http-trigger"></a>HTTP トリガーのコードの編集

ポータルで **HttpTrigger1** という既定の関数が作成されます。

:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="HTTP トリガー関数の編集のスクリーンショット。":::


1. C# コードを次のコードに置き換えます。

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
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

## <a name="add-sendgrid-key"></a>SendGrid キーの追加

SendGrid API キーを追加するには、次のように **関数キー** に追加する必要があります。

1. **[関数キー]** を選択します。
1. **[新しいファンクション キー]** を選択します。
1. 以前作成した API キーの *名前* と *値* を入力します。
1. **[OK]** をクリックします。

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Sangrid キー追加のスクリーンショット。":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>SendGrid を使用するように HttpTrigger 関数を構成する

SendGrid を使用してメールを送信するには、次のように関数バインドを構成する必要があります。

1. **[統合]** を選択します。
1. **[HTTP ($return)]** の **[出力の追加]** を選択します。
1. **[削除]** を選択します。
1. **[新しい出力]** を選択します。
1. バインドの種類として **SendGrid** を選択します。
1. SendGrid API キー設定の種類として [新規] をクリックします。
1. SendGrid API キーの *名前* と *値* を入力します。
1. 以下の情報を追加します。

| 設定 | 値 |
| ------- | ----- |
| メッセージ パラメーター名 | 名前を選択 |
| 宛先アドレス | 宛先アドレスの名前を選択 |
| 差出人アドレス | 差出人アドレスの名前を選択 |
| メッセージの件名 | 件名ヘッダーを入力 |
| [メッセージ テキスト] | 統合からメッセージを入力 |

1. **[OK]** を選択します。

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="SandGrid 出力の追加のスクリーンショット。":::


### <a name="test-the-function-works"></a>関数の動作をテストする

ポータルで関数をテストするには、まずコード エディターの下部にある **[ログ]** を選択します。 次に、コード エディターの右側にある **[テスト]** をクリックします。 **[要求本文]** として次の JSON を使用します。

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

**[ログ]** パネルに関数ログのメッセージが表示されます。

:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="関数のログ出力":::

数分後に、 **[宛先]** メール アドレスに次の内容のメールが届きます。

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics クエリを追加する

このソリューションでは、Stream Analytics クエリを使用して、デバイスが 120 秒以上にわたってテレメトリの送信を停止したときに検出します。 このクエリは、入力としてイベント ハブからのテレメトリを使用します。 ジョブはクエリの結果を関数アプリに送信します。 このセクションでは、Stream Analytics ジョブを構成します。

1. Azure portal で Stream Analytics ジョブに移動し、 **[ジョブ トポロジ]** で **[入力]** を選択し、 **[+ ストリーム入力の追加]** を選択してから、 **[イベント ハブ]** を選択します。
1. 次の表の情報を使用して、以前に作成したイベント ハブを使用して入力を構成してから、 **[保存]** を選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 入力のエイリアス | centraltelemetry |
    | サブスクリプション | 該当するサブスクリプション |
    | Event Hub 名前空間 | Event Hub 名前空間 |
    | イベント ハブ名 | 既存のものを使用 - **centralexport** |

1. **[ジョブ トポロジ]** で **[出力]** を選択し、 **[+ 追加]** を選択してから、 **[Azure 関数]** を選択します。
1. 次の表の情報を使用して出力を構成してから、 **[保存]** を選択します。

    | 設定 | 値 |
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

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Stream Analytics のスクリーンショット。":::

## <a name="configure-export-in-iot-central"></a>IoT Central でエクスポートを構成する 

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で、作成した IoT Central アプリケーションに移動します。

このセクションでは、シミュレートされたデバイスからイベント ハブにテレメトリをストリーム配信するようにアプリケーションを構成します。 エクスポートを構成するには、次の手順に従います。

1. **[データのエクスポート]** ページに移動し、 **[+ 新規]** を選択してから、 **[Azure Event Hubs]** を選択します。
1. 以下の設定を使用してエクスポートを構成してから、 **[保存]** を選択します。 

    | 設定 | 値 |
    | ------- | ----- |
    | 表示名 | Event Hubs へのエクスポート |
    | 有効 | On |
    | エクスポートするデータの種類 | 製品利用統計情報 |
    | エンリッチメント | エクスポートされたデータの編成方法についての必要なキーと値を入力します | 
    | 宛先 | 新規作成し、データのエクスポート先の情報を入力します |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="データのエクスポートのスクリーンショット。":::

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

## <a name="next-steps"></a>次のステップ

この攻略ガイドで学習した内容は次のとおりです。

* *継続的データ エクスポート* を使用してテレメトリをストリーム配信します。
* デバイスがデータの送信を停止したときに検出する Stream Analytics クエリを作成します。
* Azure Functions と SendGrid サービスを使用してメール通知を送信します。

カスタム ルールと通知を作成する方法がわかったので、次は [カスタム分析を使用して Azure IoT Central を拡張する](howto-create-custom-analytics.md)方法を学習することをお勧めします。
