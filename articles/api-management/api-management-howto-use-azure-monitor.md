---
title: Azure API Management で発行された API を監視する | Microsoft Docs
description: このチュートリアルの手順に従って、Azure API Management で API を監視する方法を学びます。
services: api-management
author: vladvino
manager: cfowler
ms.service: api-management
ms.workload: mobile
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 7f6c7a651e133122dab86d6ed81572f239718b43
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86243241"
---
# <a name="monitor-published-apis"></a>発行された API を監視する

Azure Monitor を使用すると、Azure リソースのメトリックまたはログを視覚化、クエリ、ルーティング、アーカイブし、そのメトリックとログに対してアクションを実行できます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アクティビティ ログを表示する
> * リソース ログを表示する
> * API のメトリックを表示する 
> * API が許可されていない呼び出しを受けたときのアラート ルールをセットアップする

次のビデオは、Azure Monitor を使用して API Management を監視する方法を示しています。 

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Monitor-API-Management-with-Azure-Monitor/player]

## <a name="prerequisites"></a>前提条件

+ [Azure API Management の用語](api-management-terminology.md)について学習します。
+ 次のクイック スタートを完了すること:[Azure API Management インスタンスを作成する](get-started-create-service-instance.md)。
+ また、次のチュートリアルを完了すること: [最初の API のインポートと発行](import-and-publish.md)。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>API のメトリックを表示する

API Management はメトリックを 1 分間隔で出力するので、API の状態と正常性をほぼリアルタイムで把握できます。 最も頻繁に使用される 2 つのメトリックを次に示します。 使用できるすべてのメトリックの一覧については、[サポートされているメトリック](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice)に関するページを参照してください。

* 容量: APIM サービスのアップグレードとダウングレードに関する判断に役立ちます。 このメトリックは 1 分ごとに出力され、報告時のゲートウェイの容量を反映しています。 メトリックの範囲は 0 から 100 で、CPU やメモリの使用率などのゲートウェイ リソースに基づいて計算されます。
* 要求: 対象の APIM サービスを通過する API トラフィックを分析するのに役立ちます。 メトリックは 1 分ごとに出力され、応答コード、場所、ホスト名、エラーなどのディメンションと共にゲートウェイ要求の数を報告します。 

> [!IMPORTANT]
> 次のメトリックは 2019 年 5 月の時点で非推奨であり、2023 年 8 月に廃止される予定です: ゲートウェイ要求の合計、成功したゲートウェイ要求、未承認ゲートウェイ要求、失敗したゲートウェイ要求、その他のゲートウェイ要求。 同等の機能を提供する要求メトリックに移行してください。

![メトリックのグラフ](./media/api-management-azure-monitor/apim-monitor-metrics.png)

メトリックにアクセスするには、次の手順に従います。

1. ページの下部にあるメニューの **[メトリック]** を選択します。

    ![metrics](./media/api-management-azure-monitor/api-management-metrics-blade.png)

2. ドロップダウン リストで、関心のあるメトリックを選択します。 **Requests**など。 
3. グラフには、API 呼び出しの合計数が表示されます。
4. **[Requests]\(要求数\)** メトリックのディメンションを使用して、グラフをフィルター処理できます。 たとえば、 **[フィルターの追加]** をクリックし、 **[Backend Response Code]\(バックエンド応答コード\)** を選択し、値として「500」と入力します。 これで、API バックエンドで失敗した要求の数がグラフに表示されるようになりました。   

## <a name="set-up-an-alert-rule-for-unauthorized-request"></a>許可されていない要求に対するアラート ルールをセットアップする

メトリックとアクティビティ ログに基づいてアラートを受け取るように設定できます。 Azure Monitor では、アラートがトリガーされたときに次の処理を実行するように構成することができます。

* 電子メール通知を送信する
* Webhook を呼び出す
* Azure Logic App を呼び出す

アラートを構成するには、以下の手順に従います。

1. ページの下部にあるメニュー バーの **[アラート]** を選択します。

    ![alerts](./media/api-management-azure-monitor/alert-menu-item.png)

2. このアラートに対して **[新しいアラート ルール]** をクリックします。
3. **[条件の追加]** をクリックします。
4. [シグナルの種類] ドロップダウンの **[メトリック]** を選択します。
5. 監視するシグナルとして、 **[Unauthorized Gateway Requests]\(未承認ゲートウェイ要求\)** を選択します。

    ![alerts](./media/api-management-azure-monitor/signal-type.png)

6. **[シグナル ロジックの構成]** ビューで、アラートをトリガーするしきい値を指定して、 **[完了]** をクリックします。

    ![alerts](./media/api-management-azure-monitor/threshold.png)

7. 既存のアクション グループを選択するか、新しいアクション グループを作成します。 次の例では、メールが管理者に送信されます。 

    ![alerts](./media/api-management-azure-monitor/action-details.png)

8. 名前、アラート ルールの説明を入力し、重大度を選択します。 
9. **[アラート ルールの作成]** を押します。
10. ここで、API キーなしで、Conference API を呼び出してみます。 アラートがトリガーされ、メールが管理者に送信されます。 

## <a name="activity-logs"></a>アクティビティ ログ

アクティビティ ログは、API Management サービスで実行された操作に関する情報を提供します。 アクティビティ ログを使用すると、API Management サービスで発生した書き込み操作 (PUT、POST、DELETE) について、"いつ誰が何を" 行ったのかを確認できます。

> [!NOTE]
> アクティビティ ログには、読み取り (GET) 操作、Azure Portal で実行された操作、または元の管理 API の使用に関する情報は含まれません。

アクティビティ ログには API Management サービスでアクセスするか、Azure Monitor ですべての Azure リソースのログにアクセスできます。 

![アクティビティ ログ](./media/api-management-azure-monitor/apim-monitor-activity-logs.png)

アクティビティ ログを表示するには、次の手順に従います。

1. APIM サービス インスタンスを選びます。
2. **[アクティビティ ログ]** をクリックします。

    ![アクティビティ ログ](./media/api-management-azure-monitor/api-management-activity-logs-blade.png)

3. 目的のフィルター処理の範囲を選択し、 **[適用]** をクリックします。

## <a name="resource-logs"></a>リソース ログ

リソース ログは、監査とトラブルシューティングを行うために重要な、操作とエラーについての豊富な情報を提供します。 リソース ログは、アクティビティ ログとは異なります。 アクティビティ ログは、Azure リソースで実行された操作に関する分析情報を提供します。 リソース ログは、自分のリソースが実行した操作に関する分析情報を提供します。

リソース ログを構成するには、次の手順に従います。

1. APIM サービス インスタンスを選びます。
2. **[診断設定]** をクリックします。

    ![リソース ログ](./media/api-management-azure-monitor/api-management-diagnostic-logs-blade.png)

3. **[診断を有効にする]** をクリックします。 リソース ログをメトリックと共にストレージ アカウントにアーカイブし、それらをイベント ハブにストリーム配信したり、Azure Monitor ログに送信したりすることができます。 

現時点では、API Management は、個々の API 要求についてのリソース ログ (1 時間ごとにバッチ処理) を、次のスキーマを持つエントリで提供します。

```json
{  
    "isRequestSuccess" : "",
    "time": "",
    "operationName": "",
    "category": "",
    "durationMs": ,
    "callerIpAddress": "",
    "correlationId": "",
    "location": "",
    "httpStatusCodeCategory": "",
    "resourceId": "",
    "properties": {   
        "method": "", 
        "url": "", 
        "clientProtocol": "", 
        "responseCode": , 
        "backendMethod": "", 
        "backendUrl": "", 
        "backendResponseCode": ,
        "backendProtocol": "",  
        "requestSize": , 
        "responseSize": , 
        "cache": "", 
        "cacheTime": "", 
        "backendTime": , 
        "clientTime": , 
        "apiId": "",
        "operationId": "", 
        "productId": "", 
        "userId": "", 
        "apimSubscriptionId": "", 
        "backendId": "",
        "lastError": { 
            "elapsed" : "", 
            "source" : "", 
            "scope" : "", 
            "section" : "" ,
            "reason" : "", 
            "message" : ""
        } 
    }      
}  
```

| プロパティ  | Type | 説明 |
| ------------- | ------------- | ------------- |
| isRequestSuccess | boolean | 応答の状態コードが 2xx または 3xx の範囲内で HTTP 要求が完了した場合は True |
| time | date-time | ゲートウェイが要求の処理を開始した日時のタイムスタンプ |
| operationName | string | "Microsoft.ApiManagement/GatewayLogs" (定数値) |
| category | string | "GatewayLogs" (定数値) |
| durationMs | 整数 (integer) | ゲートウェイが要求を受信した時点から、応答全体が送信された時点までのミリ秒数。 これには clienTime、cacheTime、backendTime が含まれます。 |
| callerIpAddress | string | 直接 (中間の場合もあります) のゲートウェイ呼び出し元の IP アドレス |
| correlationId | string | API Management によって割り当てられる一意の http 要求識別子 |
| location | string | 要求を処理したゲートウェイが存在する Azure リージョンの名前 |
| httpStatusCodeCategory | string | HTTP 応答状態コードのカテゴリ: 成功 (301 以下または 304 または 307)、未承認 (401、403、429)、エラー (400、500 から 600)、その他 |
| resourceId | string | API Management リソース /SUBSCRIPTIONS/\<subscription>/RESOURCEGROUPS/\<resource-group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/\<name> の ID |
| properties | object | 現在の要求のプロパティ |
| method | string | 受信要求の HTTP メソッド |
| url | string | 受信要求の URL |
| clientProtocol | string | 受信要求の HTTP プロトコル バージョン |
| responseCode | 整数 (integer) | クライアントに送信された HTTP 応答の状態コード |
| backendMethod | string | バックエンドに送信された要求の HTTP メソッド |
| backendUrl | string | バックエンドに送信された要求の URL |
| backendResponseCode | 整数 (integer) | バックエンドから受信した HTTP 応答のコード |
| backendProtocol | string | バックエンドに送信された要求の HTTP プロトコル バージョン | 
| requestSize | 整数 (integer) | 要求の処理中にクライアントから受信したバイト数 | 
| responseSize | 整数 (integer) | 要求の処理中にクライアントに送信されたバイト数 | 
| cache | string | 要求の処理における API Management キャッシュ使用の状態 (ヒット、ミス、なし) | 
| cacheTime | 整数 (integer) | API Management キャッシュの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数 | 
| backendTime | 整数 (integer) | バックエンドの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数 | 
| clientTime | 整数 (integer) | クライアントの IO 全体 (接続バイト、送信バイト、受信バイト) に費やされたミリ秒数 | 
| apiId | string | 現在の要求の API エンティティ識別子 | 
| operationId | string | 現在の要求の操作エンティティ識別子 | 
| productId | string | 現在の要求の製品エンティティ識別子 | 
| userId | string | 現在の要求のユーザー エンティティ識別子 | 
| apimSubscriptionId | string | 現在の要求のサブスクリプション エンティティ識別子 | 
| backendId | string | 現在の要求のバックエンド エンティティ識別子 | 
| lastError | object | 最後の要求処理エラー | 
| elapsed | 整数 (integer) | ゲートウェイが要求を受信してからエラーが発生した時点までの経過ミリ秒数 | 
| source | string | エラーの原因となったポリシー (または処理中の内部ハンドラー) の名前 | 
| scope | string | エラーの原因となったポリシーを含んでいるポリシー ドキュメントのスコープ | 
| section | string | エラーの原因となったポリシーを含んでいるポリシー ドキュメントのセクション | 
| reason | string | エラーの理由 | 
| message | string | エラー メッセージ | 

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * アクティビティ ログを表示する
> * リソース ログを表示する
> * API のメトリックを表示する
> * API が許可されていない呼び出しを受けたときのアラート ルールをセットアップする

次のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [呼び出しをトレースする](api-management-howto-api-inspector.md)
