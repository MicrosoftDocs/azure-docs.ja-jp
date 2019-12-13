---
title: Azure テナントにまたがる Log Analytics ワークスペースに Azure アクティビティ ログを収集する | Microsoft Docs
description: Event Hubs と Logic Apps を使用して Azure アクティビティ ログからデータを収集し、それを別のテナント内の Azure Monitor の Log Analytics ワークスペースに送信します。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 02/06/2019
ms.openlocfilehash: e202885c695e4d8cdadaf8640d7ed01b05b70ad9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931837"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants"></a>Azure Active Directory テナントにまたがる Azure Monitor に Azure Activity ログを収集する

> [!WARNING]
> リソース ログの収集方法と同様に、診断設定を使用してアクティビティ ログを Log Analytics ワークスペースに収集できるようになりました。 「[Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor (Azure Monitor の Log Analytics ワークスペースで Azure アクティビティ ログを収集して分析する)](diagnostic-settings-subscription.md)」を参照してください。

この記事では、Logic Apps 用の Azure Log Analytics データ コレクター コネクタを使用して、Azure アクティビティ ログを Azure Monitor の Log Analytics ワークスペースに収集する方法について説明します。 異なる Azure Active Directory テナントのワークスペースにログを送信する必要があるときには、この記事の手順を使用します。 たとえば、マネージド サービス プロバイダーとしては、顧客のサブスクリプションからアクティビティ ログを収集して、自身のサブスクリプションの内の Log Analytics ワークスペースに格納する必要があります。

Log Analytics ワークスペースが同一の Azure サブスクリプションにある場合、または同じ Azure Active Directory 内の別のサブスクリプションにある場合は、[Azure Monitor で Log Analytics ワークスペースに Azure アクティビティ ログを収集し、分析する](activity-log-collect.md)方法に関する記事の手順を使用して Azure アクティビティ ログを収集します。

## <a name="overview"></a>概要

このシナリオで使用される戦略では、Azure アクティビティ ログがイベントを[イベント ハブ](../../event-hubs/event-hubs-about.md)に送信するようにします。そこから、[ロジック アプリ](../../logic-apps/logic-apps-overview.md) が Log Analytics ワークスペースにイベントを送信します。 

![アクティビティ ログから Log Analytics ワークスペースへのデータ フローの図](media/collect-activity-logs-subscriptions/data-flow-overview.png)

このアプローチの長所:
- Azure アクティビティ ログはイベント ハブにストリーミングされるため待機時間が少なくなります。  その後、ロジック アプリがトリガーされ、データをワークスペースに投稿します。 
- 必要なコードは最小限であり、展開するサーバー インフラストラクチャはありません。

この記事では、次の方法について説明します。
1. イベント ハブの作成。 
2. Azure アクティビティ ログのエクスポート プロファイルを使用して、アクティビティ ログをイベント ハブにエクスポートします。
3. イベント ハブから読み取り、イベントを Log Analytics ワークスペースに送信するロジック アプリを作成します。

## <a name="requirements"></a>必要条件
このシナリオで使用される Azure リソースの要件は次のとおりです。

- イベント ハブ名前空間は、ログを出力するサブスクリプションと同じサブスクリプションに存在する必要はありません。 設定を構成するユーザーは、両方のサブスクリプションに対して適切なアクセス許可を持っている必要があります。 同じ Azure Active Directory に複数のサブスクリプションがある場合、すべてのサブスクリプションのアクティビティ ログを 1 つのイベント ハブに送信できます。
- ロジック アプリは、イベント ハブと別のサブスクリプションに配置でき、同じ Azure Active Directory に存在する必要もありません。 ロジック アプリはイベント ハブのアクセス キーを使用してイベント ハブから読み取ります。
- Log Analytics ワークスペースは、ロジック アプリとは別のサブスクリプションおよび Azure Active Directory に配置できますが、わかりやすくするために同じサブスクリプションに置くことをお勧めします。 ロジック アプリは、Log Analytics ワークスペースの ID とキーを使用してワークスペースに送信します。



## <a name="step-1---create-an-event-hub"></a>手順 1 - イベント ハブの作成

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Azure Portal で、 **[リソースの作成]**  >  **[モノのインターネット]**  >  **[Event Hubs]** の順に選択します。

   ![Marketplace の新規イベント ハブ](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. **[名前空間の作成]** の下で、新しい名前空間を入力するか既存の名前空間を選択します。 その名前が使用できるかどうかがすぐに自動で確認されます。

   ![イベント ハブを作成するダイアログの画像](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. 価格レベル (Basic または Standard)、Azure サブスクリプション、リソース グループ、新しいリソースの場所を選択します。  **[作成]** をクリックして、名前空間を作成します。 システムがリソースを完全にプロビジョニングするまで、数分間待つことが必要な場合があります。
6. 一覧で、先ほど作成した名前空間をクリックします。
7. **[共有アクセス ポリシー]** を選択し、 **[RootManageSharedAccessKey]** をクリックします。

   ![イベント ハブの共有アクセス ポリシーの画像](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. コピー ボタンをクリックして **RootManageSharedAccessKey** 接続文字列をクリップボードにコピーします。 

   ![イベント ハブの共有アクセス キーの画像](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. メモ帳など一時的な場所に、イベント ハブ名とイベント ハブのプライマリまたはセカンダリの接続文字列のコピーを保存します。 ロジック アプリでこれらの値が必要です。  イベント ハブの接続文字列としては **RootManageSharedAccessKey** 接続文字列を使用するか、別の接続文字列を作成できます。  使用する接続文字列は、`Endpoint=sb://` で開始し、**Manage** アクセス ポリシーを持つポリシーに対応する必要があります。


## <a name="step-2---export-activity-logs-to-event-hub"></a>手順 2 - アクティビティ ログの Event Hubs へのエクスポート

アクティビティ ログのストリーミングを有効にするには、イベント ハブ名前空間と、その名前空間の共有アクセス ポリシーを選択します。 イベント ハブがその名前空間に作成されるのは、新しいアクティビティ ログ イベントが最初に発生するときです。 

ログを出力するサブスクリプションと同じサブスクリプションにないイベント ハブ名前空間を使用できますが、それらのサブスクリプションは同じ Azure Active Directory に存在する必要があります。 設定を構成するユーザーは、両方のサブスクリプションにアクセスするための適切な RBAC を持っている必要があります。 

1. Azure Portal で **[監視]**  >  **[アクティビティ ログ]** を選択します。
3. ページの上部にある **[エクスポート]** ボタンをクリックします。

   ![Azure の監視の操作中の画像](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. エクスポート元の **[サブスクリプション]** を選択し、 **[リージョン]** ドロップダウンで **[すべて選択]** をクリックしてから、すべてのリージョンのリソースについてイベントを選択します。 **[イベント ハブにエクスポート]** チェック ボックスをオンにします。
7. **[Service Bus 名前空間]** をクリックしてから、イベント ハブがある **[サブスクリプション]** 、**イベント ハブ名前空間**、**イベント ハブ ポリシー名** を選択します。

    ![イベント ハブにエクスポートするためのページの画像](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. **[OK]** と **[保存]** を順にクリックして設定を保存します。 設定はサブスクリプションにすぐに適用されます。

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>手順 3 - ロジック アプリの作成

アクティビティ ログがイベント ハブに書き込まれるようになったら、イベント ハブからログを収集し、それを Log Analytics ワークスペースに書き込むロジック アプリを作成します。

ロジック アプリには次を含めます。
- イベント ハブから読み取る[イベント ハブ コネクタ](https://docs.microsoft.com/connectors/eventhubs/) トリガー。
- JSON イベントを抽出する [Parse JSON アクション](../../logic-apps/logic-apps-content-type.md)。
- JSON をオブジェクトに変換する [Compose アクション](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action)。
- データを Log Analytics ワークスペースに投稿する [Log Analytics データ送信コネクタ](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/)。

   ![イベント ハブ トリガーのロジック アプリへの追加の画像](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>ロジック アプリの要件
ロジック アプリを作成する前に、前の手順で説明した次の情報があることを確認します。
- イベント ハブ名
- イベント ハブ名前空間のイベント ハブ接続文字列 (プライマリまたはセカンダリ)
- Log Analytics ワークスペース ID
- Log Analytics 共有キー

イベント ハブ名と接続文字列を取得するには、「[Event Hubs 名前空間を確認して、接続文字列を検索する](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string)」の手順に従います。


### <a name="create-a-new-blank-logic-app"></a>新しい空のロジック アプリの作成

1. Azure Portal で、 **[リソースの作成]**  >  **[エンタープライズ統合]**  >  **[ロジック アプリ]** を選択します。

    ![Marketplace の新しいロジック アプリ](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. 次の表に示す設定を入力します。

    ![ロジック アプリを作成する](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Setting | 説明  |
   |:---|:---|
   | 名前           | ロジック アプリの一意の名前。 |
   | サブスクリプション   | ロジック アプリを含める Azure サブスクリプションを選択します。 |
   | リソース グループ | ロジック アプリのために、既存の Azure リソース グループを選択するか、新しいものを作成します。 |
   | Location       | ロジック アプリをデプロイするデータセンターのリージョンを選択します。 |
   | Log Analytics  | ロジック アプリの各実行の状態を Log Analytics ワークスペースにログ記録するかどうかを選択します。  |

    
3. **作成** を選択します。 **[デプロイメントに成功しました]** という通知が表示されたら、 **[リソースに移動]** をクリックしてロジック アプリを開きます。

4. **[テンプレート]** で **[空のロジック アプリ]** を選択します。 

Logic Apps デザイナーには、使用可能なコネクタとそのトリガーが表示されています。これらは、ロジック アプリ ワークフローを開始するために使用します。

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>イベント ハブ トリガーの追加

1. ロジック アプリ デザイナーの検索ボックスに、フィルターとして「*event hubs*」を入力します。 **[Event Hubs - When events are available in Event Hub]\(イベント ハブ - イベント ハブでイベントを使用できるとき\)** トリガーを選択します。

   ![イベント ハブ トリガーのロジック アプリへの追加の画像](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. 資格情報の入力を求められたら、Event Hubs の名前空間に接続します。 コピーしておいた接続の名前と接続文字列を入力します。  **作成** を選択します。

   ![イベント ハブ接続のロジック アプリへの追加の画像](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. 接続を作成した後は、トリガーの設定を編集します。 まず、**insights-operational-logs** を **[イベント ハブ名]** ドロップダウン リストから選択します。

   ![[When events are available in Event Hub]\(イベント ハブでイベントを使用できるとき\) ダイアログ](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. **[詳細オプションの表示]** を開き、 **[コンテンツの種類]** を *application/json* に変更します。

   ![イベント ハブ構成のダイアログ](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Parse JSON アクションの追加

Event Hub からの出力には、レコードの配列を持つ JSON ペイロードが含まれています。 [Parse JSON](../../logic-apps/logic-apps-content-type.md) アクションは、Log Analytics ワークスペースに送信するためのレコードの配列のみを抽出するために使用されます。

1. **[新しいステップ]**  >  **[アクションの追加]** をクリックします。
2. 検索ボックスに、フィルターとして「*parse json*」と入力します。 **[Data Operations - Parse JSON]\(データ操作 - Parse JSON\)** アクションを選択します。

   ![ロジック アプリへの Parse JSON アクションの追加](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. **[コンテンツ]** フィールドをクリックし、"*本文*" を選択します。

4. 次のスキーマをコピーして、 **[スキーマ]** フィールドに貼り付けます。  このスキーマはイベント ハブ アクションの出力と一致します。  

   ![[JSON の解析] ダイアログ](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> **[実行]** をクリックし、イベント ハブの **[Raw Output]\(未加工の出力\)** を確認すると、サンプルのペイロードを取得できます。  その後、 **[JSON の解析]** アクティビティの **[サンプルのペイロードを使用してスキーマを生成する]** でこの出力を使用すると、スキーマを生成できます。

### <a name="add-compose-action"></a>Compose アクションの追加
[Compose](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) アクションは JSON 出力を受け取り、Log Analytics アクションで使用できるオブジェクトを作成します。

1. **[新しいステップ]**  >  **[アクションの追加]** をクリックします。
2. フィルターとして「*compose*」と入力し、 **[Data Operations - Compose]\(データ操作 - Compose\)** アクションを選択します。

    ![Compose アクションの追加](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. **[入力]** フィールドをクリックし、 **[JSON の解析]** アクティビティの下の **[本文]** を選択します。


### <a name="add-log-analytics-send-data-action"></a>Log Analytics データ送信アクションの追加
[Azure Log Analytics データ コレクター](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) アクションは、Compose アクションからオブジェクトを取得し、それを Log Analytics ワークスペースに送信します。

1. **[新しいステップ]**  >  **[アクションの追加]** をクリックします。
2. フィルターとして「*log analytics*」と入力し、 **[Azure Log Analytics Data Collector - Send Data]\(Azure Log Analytics データ コレクター - データの送信\)** アクションを選択します。

   ![ロジック アプリへのログ分析データ送信アクションの追加](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. 接続の名前を入力し、ご使用の Log Analytics ワークスペースの **[ワークスペース ID]** と **[ワークスペース キー]** を貼り付けます。  **Create** をクリックしてください。

   ![ロジック アプリへのログ分析接続の追加](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. 接続を作成した後は、次の表のように設定を編集します。 

    ![データ送信アクションの構成](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Setting        | 値           | 説明  |
   |---------------|---------------------------|--------------|
   |[JSON Request body]\(JSON 要求本文\)  | **Compose** アクションの**出力** | Compose アクションの本文からレコードを取得します。 |
   | [カスタム ログの名前] | AzureActivity | インポートされたデータを保持するために Log Analytics ワークスペース内に作成するカスタム ログ テーブルの名前。 |
   | [Time-generated-field]\(時刻生成フィールド\) | time | **time** に対応する JSON フィールドを選択せずに、「time」と入力してください。 JSON フィールドを選択すると、デザイナーによって**データ送信**アクションが *For Each* ループに配置され、意図した結果になりません。 |




10. **[保存]** をクリックして、ロジック アプリに加えた変更を保存します。

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>手順 4 - ロジック アプリのテストとトラブルシューティング
ワークフローが完了したら、デザイナーでテストを行い、エラーなしで機能することを確認できます。

Logic Apps デザイナーで **[実行]** をクリックしてロジック アプリをテストします。 ロジック アプリのステップごとにステータス アイコンが表示されます。緑色の円で囲まれた白いチェック マークは成功を意味します。

   ![ロジック アプリのテスト](media/collect-activity-logs-subscriptions/test-logic-app.png)

各ステップの詳細な情報を表示するには、ステップの名前をクリックして展開します。 **[未加工入力の表示]** と **[未加工出力の表示]** をクリックすると、各ステップで送受信されるデータのさらに詳しい情報が表示されます。

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>手順 5 - Log Analytics での Azure アクティビティ ログの表示
最後の手順では、Log Analytics ワークスペースを調べて、データが意図したとおりに収集されていることを確認します。

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics ワークスペースの一覧からワークスペースを選択します。
3.  **[ログ検索]** タイルをクリックし、[ログ検索] ウィンドウのクエリ フィールドに「`AzureActivity_CL`」と入力してから Enter キーを押すか、クエリ フィールドの右側にある検索ボタンをクリックします。 カスタム ログに *AzureActivity* という名前を付けなかった場合は、選択した名前を入力して末尾に `_CL` を追加します。

>[!NOTE]
> 新しいカスタム ログが Log Analytics ワークスペースに初めて送信されるときは、カスタム ログが検索可能になるまでに最大 1 時間かかる可能性があります。

>[!NOTE]
> アクティビティ ログはカスタム テーブルに書き込まれ、[アクティビティ ログ ソリューション](./activity-log-collect.md)には表示されません。


![ロジック アプリのテスト](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>次の手順

この記事では、イベント ハブから Azure アクティビティ ログを読み取り、分析のためにそれを Log Analytics ワークスペースに送信するロジック アプリを作成しました。 ワークスペース内のデータの視覚化 (ダッシュボードの作成を含む) の詳細については、データの視覚化のチュートリアルを確認してください。

> [!div class="nextstepaction"]
> [ログ検索データの視覚化のチュートリアル](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
