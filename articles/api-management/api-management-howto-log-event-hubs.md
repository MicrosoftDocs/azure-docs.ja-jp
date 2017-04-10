---
title: "Azure API Management で Azure Event Hubs にイベントを記録する方法 | Microsoft Docs"
description: "Azure API Management で Azure Event Hubs にイベントを記録する方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 2969e6063d7bc59a6c8ca733912904abeeb7e7e8
ms.openlocfilehash: afecb15f36525c53a66f30047dffe8a3e8f36107
ms.lasthandoff: 02/03/2017


---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Azure API Management で Azure Event Hubs にイベントを記録する方法
Azure Event Hubs は、1 秒間に数百万件のイベントを取り込むことができる高度にスケーラブルなデータ受信サービスであり、接続されたデバイスとアプリケーションで生成される大量のデータを処理および分析できます。 Event Hubs はイベント パイプラインの「玄関」として機能し、Event Hubs に収集されたデータは、任意のリアルタイム分析プロバイダーまたはバッチ処理/ストレージ アダプターを使用して変換および格納できます。 Event Hubs はイベント ストリームの生成とイベントの使用を分離し、イベント コンシューマーが独自のスケジュールでイベントにアクセスできるようにします。

この記事は [Azure API Management と Event Hubs の統合](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) 動画に付随するものであり、Azure Event Hubs を利用して API Management イベントをログに記録する方法を説明しています。

## <a name="create-an-azure-event-hub"></a>Azure Event Hub を作成します
新しいイベント ハブを作成するには、[Azure クラシック ポータル](https://manage.windowsazure.com)にサインインして **[新規]**、->**[App Services]**、->->**[Service Bus]**、**[イベント ハブ]**、->**[簡易作成]** の順にクリックします。 イベント ハブの名前とリージョンを入力し、サブスクリプションを選択して、名前空間を選択します。 名前空間の作成がまだ済んでいない場合は、 **[名前空間]** ボックスに名前を入力して作成してください。 すべてのプロパティの構成が完了したら、 **[新しいイベント ハブの作成]** をクリックしてイベント ハブを作成します。

![イベント ハブの作成][create-event-hub]

次に、新しいイベント ハブの **[構成]** タブに移動し、**共有アクセス ポリシー**を&2; つ作成します。 1 つは "**Sending**" という名前を付けて、**送信**アクセス許可を与えます。

![Sending policy][sending-policy]

もう&1; つには "**Receiving**" という名前を付けて**リッスン** アクセス許可を追加し、**[保存]** をクリックします。

![Receiving policy][receiving-policy]

アプリケーションは、この&2; つの共有アクセス ポリシーによって、イベント ハブにイベントを送信したり、イベント ハブからイベントを受信したりすることができます。 これらのポリシーの接続文字列にアクセスするには、イベント ハブの **[ダッシュボード]** タブに移動し、**[接続情報]** をクリックします。

![接続文字列][event-hub-dashboard]

**Sending** 接続文字列はイベントをログに記録するときに、**Receiving** 接続文字列はイベント ハブからイベントをダウンロードするときに使用されます。

![接続文字列][event-hub-connection-string]

## <a name="create-an-api-management-logger"></a>API Management ロガーの作成
イベント ハブが完成したら、そこにイベントを記録できるようにするための構成を API Management サービスの [ロガー](https://msdn.microsoft.com/library/azure/mt592020.aspx) に対して行います。

API Management のロガーは、 [API Management REST API](http://aka.ms/smapi)を使用して構成します。 REST API を初めて使用する前には、[前提条件](https://msdn.microsoft.com/library/azure/dn776326.aspx#Prerequisites)を確認し、[REST API へのアクセスを有効化](https://msdn.microsoft.com/library/azure/dn776326.aspx#EnableRESTAPI)してください。

ロガーを作成するには、次の URL テンプレートを使用して HTTP PUT 要求を送信します。

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2014-02-14-preview`

* `{your service}` は、実際の API Management サービス インスタンスの名前です。
* `{new logger name}` は、新しいロガーに付ける名前です。 [log-to-eventhub](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub) ポリシーを構成するときに、この名前を参照します。

次のヘッダーを要求に追加します。

* Content-Type : application/json
* Authorization : SharedAccessSignature uid=...
  * `SharedAccessSignature` を生成する手順については、 [Azure API Management REST API の認証に関するページ](https://msdn.microsoft.com/library/azure/dn798668.aspx)を参照してください。

次のテンプレートを使用して要求の本文を指定します。

```json
{
  "type" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the Azure Classic Portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `type` は `AzureEventHub` に設定する必要があります。
* `description` は、ロガーの説明です (省略可能)。必要に応じて、長さゼロの文字列にしてください。
* `credentials` には、Azure Event Hubs の `name` と `connectionString` が含まれます。

要求を実行したとき、ロガーが作成されると、状態コード `201 Created` が返されます。

> [!NOTE]
> その他のリターン コードとその理由については、 [ロガーの作成に関するページ](https://msdn.microsoft.com/library/azure/mt592020.aspx#PUT)を参照してください。 その他、リスト、更新、削除など、各種操作の実行方法については、 [ロガー](https://msdn.microsoft.com/library/azure/mt592020.aspx) エンティティのドキュメントを参照してください。
>
>

## <a name="configure-log-to-eventhubs-policies"></a>log-to-eventhub ポリシーの構成
API Management でロガーを構成したら、必要なイベントを記録するための構成を log-to-eventhub ポリシーに対して行います。 log-to-eventhub ポリシーは、inbound と outbound のどちらかのポリシー セクションで使用します。

ポリシーを構成するには、[Azure Portal](https://portal.azure.com) にサインインして API Management サービスに移動し、**[パブリッシャー ポータル]** をクリックしてパブリッシャー ポータルにアクセスします。

![発行者ポータル][publisher-portal]

左側にある [API Management] メニューの **[ポリシー]** をクリックし、目的の製品と API を選択して、**[ポリシーの追加]** をクリックします。 この例では、全製品 (**[無制限]**) の **[Echo API]** にポリシーを追加しています。

![ポリシーの追加][add-policy]

`inbound` ポリシー セクションにカーソルを置き、**[イベント ハブへの記録]** ポリシーをクリックして `log-to-eventhub` ポリシー ステートメント テンプレートを挿入します。

![ポリシー エディター][event-hub-policy]

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```

`logger-id` の部分は、先行する手順で構成した API Management ロガーの名前に置き換えてください。

`log-to-eventhub` 要素の値は、文字列を返す式であれば何でもかまいません。 この例では、日付と時刻、サービス名、要求 ID、要求の IP アドレス、操作の名前から成る文字列が記録されます。

**[保存]** をクリックして、更新済みのポリシーの構成を保存します。 保存後すぐポリシーが有効となり、指定したイベント ハブにイベントが記録されます。

## <a name="next-steps"></a>次のステップ
* Azure Event Hubs の詳細
  * [Azure Event Hubs の使用](../event-hubs/event-hubs-c-getstarted-send.md)
  * [EventProcessorHost を使用したメッセージの受信](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Event Hubs のプログラミング ガイド](../event-hubs/event-hubs-programming-guide.md)
* API Management と Event Hubs の統合の詳細
  * [ロガーのエンティティ リファレンス](https://msdn.microsoft.com/library/azure/mt592020.aspx)
  * [log-to-eventhub ポリシー リファレンス](https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub)
  * [Azure API Management、Event Hubs、Runscope を使用した API の監視](api-management-log-to-eventhub-sample.md)    

## <a name="watch-a-video-walkthrough"></a>ビデオ チュートリアルを視聴する
> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Integrate-Azure-API-Management-with-Event-Hubs/player]
>
>

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png

