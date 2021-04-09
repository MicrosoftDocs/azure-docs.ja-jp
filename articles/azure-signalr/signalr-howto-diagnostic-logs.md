---
title: Azure SignalR Service のリソース ログ
description: Azure SignalR Service のリソース ログを設定する方法と、それを利用して自己トラブルシューティングを行う方法について学習します。
author: wanlwanl
ms.service: signalr
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: wanl
ms.openlocfilehash: 5650ff0e039d1e9211b8d0013726e101efdfab78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572255"
---
# <a name="resource-logs-for-azure-signalr-service"></a>Azure SignalR Service のリソース ログ

このチュートリアルでは、Azure SignalR Service のリソース ログの内容、その設定方法、トラブルシューティングの方法について説明します。 

## <a name="prerequisites"></a>前提条件
リソース ログを有効にするには、ログ データを格納する場所が必要になります。 このチュートリアルでは、Azure Storage と Log Analytics を使用します。

* [Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) - ポリシー監査、スタティック分析、またはバックアップのリソース ログを保持します。
* [Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) - Azure リソースによって生成された生ログの分析を可能にする柔軟なログ検索および分析ツール。

## <a name="set-up-resource-logs-for-an-azure-signalr-service"></a>Azure SignalR Service のリソース ログの設定

Azure SignalR Service のリソース ログを表示できます。 これらのログでは、Azure SignalR サービス インスタンスへの接続に関するより豊富なビューが提供されています。 リソース ログでは、すべての接続の詳細情報が提供されます。 たとえば、接続の基本情報 (ユーザー ID、接続 ID、トランスポートの種類など) やイベント情報 (接続、切断、中止イベントなど) です。 リソース ログは、問題の識別、接続の追跡、分析に使用できます。

### <a name="enable-resource-logs"></a>リソース ログの有効化

既定では、リソース ログは無効になっています。 リソース ログを有効にするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) の **[監視]** 下で、 **[診断設定]** をクリックします。

    ![ペインで診断設定に移動する](./media/signalr-tutorial-diagnostic-logs/diagnostic-settings-menu-item.png)

1. 次に、 **[診断設定を追加する]** をクリックします。

    ![リソース ログの追加](./media/signalr-tutorial-diagnostic-logs/add-diagnostic-setting.png)

1. 目的のアーカイブ先を設定します。 現時点では、 **[ストレージ アカウントへのアーカイブ]** および **[Log Analytics への送信]** がサポートされています。

1. アーカイブするログを選択します。

    ![[診断設定] ウィンドウ](./media/signalr-tutorial-diagnostic-logs/diagnostics-settings-pane.png)


1. 新しい診断設定を保存します。

新しい設定は、10 分ほどで有効になります。 その後、構成されたアーカイブ ターゲットのログが **[診断ログ]** ウィンドウに表示されます。

診断の構成の詳細については、[Azure リソース ログの概要](../azure-monitor/essentials/platform-logs-overview.md)に関するページを参照してください。

### <a name="resource-logs-categories"></a>リソース ログのカテゴリ

Azure SignalR Service では、1 つのカテゴリ内のリソース ログをキャプチャします。

* **[すべてのログ]** :Azure SignalR Service に接続される接続を追跡します。 ログには、接続/切断、認証、および調整に関する情報が記載されます。 詳細については、次のセクションを参照してください。

### <a name="archive-to-a-storage-account"></a>ストレージ アカウントへのアーカイブ

ログは、 **[診断ログ]** ペインで構成したストレージ アカウントに格納されます。 リソース ログを格納するために、`insights-logs-alllogs` という名前のコンテナーが自動的に作成されます。 コンテナー内では、ログはファイル `resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX/y=YYYY/m=MM/d=DD/h=HH/m=00/PT1H.json` に格納されます。 基本的に、パスは `resource ID` と `Date Time` によって連結されています。 ログ ファイルは `hour` 別に分割されています。 そのため、分数は常に `m=00` になります。

すべてのログは、JavaScript Object Notation (JSON) 形式で格納されます。 各エントリには、以下のセクションで説明している形式を使用する文字列フィールドがあります。

アーカイブ ログの JSON 文字列には、次の表に示す要素が含まれます。

**形式**

名前 | 説明
------- | -------
time | ログ イベント時間
level | ログ イベント レベル
resourceId | Azure SignalR サービスのリソース ID
location | Azure SignalR サービスの場所
category | ログ イベントのカテゴリ
operationName | イベントの操作名
callerIpAddress | サーバー/クライアントの IP アドレス
properties | このログ イベントに関連する詳細なプロパティ。 詳細については、次のプロパティの表を参照してください。

**プロパティの表**

名前 | 説明
------- | -------
type | ログ イベントの種類。 現時点では、Azure SignalR サービスへの接続に関する情報が提供されています。 `ConnectivityLogs` の種類のみが利用可能です
collection | ログ イベントのコレクション。 使用できる値: `Connection`、`Authorization`、および `Throttling`
connectionId | 接続の ID
transportType | 接続のトランスポートの種類。 使用できる値 : `Websockets` \| `ServerSentEvents` \| `LongPolling`
connectionType | 接続の種類。 使用できる値 : `Server` \| `Client`。 `Server`: サーバー側からの接続、`Client`: クライアント側からの接続
userId | ユーザーの ID
message | ログ イベントの詳細なメッセージ

次のコードは、アーカイブ ログの JSON 文字列の例です。

```json
{
    "properties": {
        "message": "Entered Serverless mode.",
        "type": "ConnectivityLogs",
        "collection": "Connection",
        "connectionId": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "userId": "User",
        "transportType": "WebSockets",
        "connectionType": "Client"
    },
    "operationName": "ServerlessModeEntered",
    "category": "AllLogs",
    "level": "Informational",
    "callerIpAddress": "xxx.xxx.xxx.xxx",
    "time": "2019-01-01T00:00:00Z",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/XXXX/PROVIDERS/MICROSOFT.SIGNALRSERVICE/SIGNALR/XXX",
    "location": "xxxx"
}
```

### <a name="archive-logs-schema-for-log-analytics"></a>Log Analytics のアーカイブ ログ スキーマ

リソース ログを表示するには、次の手順に従います。

1. 対象の Log Analytics の `Logs` をクリックします。

    ![Log Analytics メニュー項目](./media/signalr-tutorial-diagnostic-logs/log-analytics-menu-item.png)

2. 「`SignalRServiceDiagnosticLogs`」と入力し、リソース ログのクエリを実行する時間の範囲を選択します。 詳細なクエリについては、「[Azure Monitor で Log Analytics の使用を開始する](../azure-monitor/logs/log-analytics-tutorial.md)」を参照してください。

    ![Log Analytics のクエリ ログ](./media/signalr-tutorial-diagnostic-logs/query-log-in-log-analytics.png)

アーカイブ ログの列には、次の表に示す要素が含まれます。

名前 | 説明
------- | ------- 
TimeGenerated | ログ イベント時間
コレクション | ログ イベントのコレクション。 使用できる値: `Connection`、`Authorization`、および `Throttling`
OperationName | イベントの操作名
場所 | Azure SignalR サービスの場所
Level | ログ イベント レベル
CallerIpAddress | サーバー/クライアントの IP アドレス
Message | ログ イベントの詳細なメッセージ
UserId | ユーザーの ID
ConnectionId | 接続の ID
ConnectionType | 接続の種類。 使用できる値 : `Server` \| `Client`。 `Server`: サーバー側からの接続、`Client`: クライアント側からの接続
TransportType | 接続のトランスポートの種類。 使用できる値 : `Websockets` \| `ServerSentEvents` \| `LongPolling`

### <a name="troubleshooting-with-resource-logs"></a>リソース ログを使用したトラブルシューティング

Azure SignalR Service のトラブルシューティングを行うには、サーバー/クライアント側のログ上でエラーをキャプチャできるようにします。 現時点で、Azure SignalR Service ではリソース ログが公開されており、サービス側のログを有効にすることもできます。

接続が予期せず増加または削除される状況が発生した場合は、リソース ログを利用してトラブルシューティングできます。

多くの場合、一般的な問題は予期しない接続数の変化、接続数の接続上限への到達、承認エラーに関するものです。 トラブルシューティングの方法については、次のセクションを参照してください。

#### <a name="unexpected-connection-number-changes"></a>予期しない接続数の変化

##### <a name="unexpected-connection-dropping"></a>予期しない接続の削除

予期しない接続の削接が発生した場合は、まず、サービス、サーバー、およびクライアント側のログを有効にします。

接続が切断されると、リソース ログにこの切断イベントが記録されます。`operationName` には `ConnectionAborted` または `ConnectionEnded` が表示されます。

`ConnectionAborted` と `ConnectionEnded` の違いは、`ConnectionEnded` はクライアントまたはサーバー側によってトリガーされた予期された切断であるという点です。 一方、`ConnectionAborted` は多くの場合、予期しない接続の削除イベントであり、中断の理由が `message` に示されます。

次の表に、中断の理由を示します。

理由 | 説明
------- | ------- 
接続数が上限に達している | 接続数が現在の価格レベルの上限に達しています。 サービス ユニットのスケールアップを検討してください
アプリケーション サーバーによって接続が閉じられた | アプリ サーバーでは中断がトリガーされます。 予期された中断と考えることができます
接続の ping がタイムアウトした | 通常は、ネットワークの問題によって発生します。 インターネットからアプリ サーバーの利用可否を確認することを検討してください
サービスの再読み込み、再接続 | Azure SignalR Service が再読み込み中です。 Azure SignalR では、自動での再接続をサポートしています。Azure SignalR Service に再接続されるまで待機するか、または手動で再接続することができます
内部サーバーの一時的なエラー | Azure SignalR Service で一時的なエラーが発生しています。自動的に復旧されます
サーバー接続が削除された | 不明なエラーによってサーバー接続が削除されています。まず、サービス/サーバー/クライアント側のログを利用して自己トラブルシューティングすることを検討してください。 基本的な問題 (ネットワークの問題、アプリ サーバー側の問題など) を除外してみてください。 問題が解決されない場合は、Microsoft にお問い合わせください。 詳細については、「[ヘルプの参照](#get-help)」セクションを参照してください。 

##### <a name="unexpected-connection-growing"></a>予期せずに接続が増加している

予期しない接続の増加に関するトラブルシューティングを行うには、最初に余計な接続をフィルターで除外する必要があります。 テスト クライアント接続に、一意のテスト ユーザー ID を追加できます。 次に、リソース ログを使用してそれを検証します。複数のクライアント接続に同じテスト ユーザー ID または IP があることがわかった場合は、クライアント側では想定よりも多くの接続が作成され確立される可能性があります。 クライアント側を確認します。

#### <a name="authorization-failure"></a>承認エラー

クライアント要求に対して未承認 401 が返された場合は、リソース ログを確認します。 `Failed to validate audience. Expected Audiences: <valid audience>. Actual Audiences: <actual audience>` が発生している場合は、アクセス トークン内のすべてのユーザーが無効になっていることを意味します。 ログ上に提示されている有効なユーザーを使用してみてください。


#### <a name="throttling"></a>Throttling

Azure SignalR Service への SignalR クライアント接続を確立できないことがわかった場合は、リソース ログを確認してください。 リソース ログ上で `Connection count reaches limit` が発生している場合は、確立している SignalR Service への接続が多すぎて、接続数の上限に到達しています。 SignalR サービスのスケールアップを検討してください。 リソース ログ上で `Message count reaches limit` が発生している場合は、Free レベルを利用しており、メッセージのクォータを最大まで使用していることを意味します。 さらにメッセージを送信する場合は、SignalR サービスを Standard レベルへ変更して追加のメッセージを送信することを検討してください。 詳細については、「[Azure SignalR Service の価格](https://azure.microsoft.com/pricing/details/signalr-service/)」を参照してください。

### <a name="get-help"></a>ヘルプの参照

最初に自分でトラブルシューティングを行うことをお勧めします。 ほとんどの問題は、アプリ サーバーまたはネットワークの問題によって引き起こされています。 根本原因を探るには、[リソース ログによるトラブルシューティングのガイド](#troubleshooting-with-resource-logs)と[基本のトラブルシューティング ガイド](https://github.com/Azure/azure-signalr/blob/dev/docs/tsg.md)に従ってください。
それでも問題が解決しない場合は、GitHub 上に問題を開くか、Azure portal 上でチケットを作成することを検討してください。
次を指定します。
1. 問題が発生したときの時間範囲約 30 分
2. Azure SignalR Service のリソース ID
3. 問題の詳細 (可能な限り具体的に示す)。たとえば、appserver からメッセージが送信されていない、クライアント接続が削除されたなど。
4. サーバー/クライアント側から収集されたログと、役に立つ可能性があるその他の資料
5. [省略可能] 再現コード

> [!NOTE]
> GitHub にイシューを開いた場合は、ご自分の機密情報 (リソース ID、サーバー/クライアント ログなど) は非公開にし、Microsoft 組織内のメンバーにのみ非公開で送信するようにしてください。