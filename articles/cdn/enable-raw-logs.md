---
title: Azure CDN HTTP 生ログ
description: この記事では、Azure CDN HTTP 生ログについて説明します。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040161"
---
# <a name="azure-cdn-http-raw-logs"></a>Azure CDN HTTP 生ログ
生ログは、監査やトラブルシューティングにとって重要な操作とエラーに関する豊富な情報を提供します。 生ログは、アクティビティ ログとは異なります。 アクティビティ ログは、Azure リソースに対して行われた操作を可視化します。 生ログからは、リソースの操作記録が提供されます。 CDN が受信する各要求についての豊富な情報が生ログから得られます。 

> [!IMPORTANT]
> HTTP 生ログ機能は、Microsoft の Azure CDN からご利用いただけます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="configuration---azure-portal"></a>構成 - Azure portal

Microsoft プロファイルから Azure CDN の生ログを構成するには: 

1. Azure portal メニューから、 **[すべてのリソース]**  >>  **\<your-CDN-profile>** を選択します。

2. **[監視]** で **[診断設定]** を選択します。

3. **[+ 診断設定の追加]** を選択します。

    ![CDN 診断設定](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > 生ログは、集計された HTTP ステータス コード ログがエンドポイント レベルで利用できるときにのみ、プロファイル レベルで利用できます。

4. **[診断設定]** の **[診断設定の名前]** で診断設定の名前を入力します。

5. **ログ**を選択し、保有期間を日数で設定します。

6. **[宛先の詳細]** を選択します。 出力先の選択肢:
    * **Log Analytics への送信**
        * **サブスクリプション**と **Log Analytics ワークスペース**を選択します。
    * **ストレージ アカウントへのアーカイブ**
        * **サブスクリプション**と**ストレージ アカウント**を選択します。
    * **イベント ハブへのストリーム**
        * **[サブスクリプション]** 、 **[イベント ハブの名前空間]** 、 **[イベント ハブ名 (オプション)]** 、 **[イベント ハブ ポリシー名]** を選択します。

    ![CDN 診断設定](./media/cdn-raw-logs/raw-logs-02.png)

7. **[保存]** を選択します。

## <a name="configuration---azure-powershell"></a>構成 - Azure PowerShell

生ログの診断設定を構成するには、[Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) を使用します。

保持データは、このコマンドの **-RetentionInDays** オプションで定義されます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>ストレージ アカウントの診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. ストレージ アカウントの診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Log Analytics ワークスペースの診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Log Analytics ワークスペースの診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>イベント ハブの名前空間の診断ログを有効にする

1. Azure PowerShell にサインインします。

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. イベント ハブの名前空間の診断ログを有効にするには、これらのコマンドを入力します。 変数は、実際の値に置き換えてください。

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>生ログのプロパティ

Microsoft サービスの Azure CDN は現在、生ログを提供しています。 生ログでは、次のスキーマを使用した各エントリが個々の API 要求に提供されます。 

| プロパティ              | 説明                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |
| HttpMethod            | 要求で使用される HTTP メソッド。                                                                                                                                                                     |
| HttpVersion           | 要求または接続の種類。                                                                                                                                                                   |
| RequestUri            | 受信した要求の URI。                                                                                                                                                                         |
| RequestBytes          | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。                                                                                                   |
| ResponseBytes         | 応答としてバックエンド サーバーによって送信されたバイト数。                                                                                                                                                    |
| UserAgent             | クライアントで使用されたブラウザーの種類。                                                                                                                                                               |
| ClientIp              | 要求を行ったクライアントの IP アドレス。                                                                                                                                                  |
| TimeTaken             | アクションにかかった時間の長さ (ミリ秒単位)。                                                                                                                                            |
| SecurityProtocol      | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。                                                                                                                           |
| エンドポイント              | CDN エンドポイント ホストは、親 CDN プロファイルで構成されています。                                                                                                                                   |
| Backend Host name     | 要求が送信されているバックエンド ホストまたはオリジンの名前。                                                                                                                                |
| Sent to origin shield </br> (非推奨) * **非推奨化については、下記の注意事項を参照してください。** | true の場合、要求は、エッジ ポップではなく、オリジン シールド キャッシュから応答されました。 オリジン シールドは、キャッシュ ヒット率を向上させる目的で使用される親キャッシュです。                                       |
| isReceivedFromClient | true の場合、要求はクライアントから送信されます。 false の場合、要求はエッジ (子 POP) で不成功となり、オリジン シールド (親 POP) から応答されます。 
| HttpStatusCode        | プロキシから返された HTTP 状態コード。                                                                                                                                                        |
| HttpStatusDetails     | 要求の結果の状態。 この文字列値の意味は、状態参照テーブルで確認できます。                                                                                              |
| Pop                   | ユーザー要求に応答したエッジ ポップ。 POP の略語はそれぞれの都市の空港コードです。                                                                                   |
| Cache Status          | オブジェクトがキャッシュから返されたか、配信元から届いたかを意味します。                                                                                                             |
> [!NOTE]
> クエリを実行することによって、Log Analytics プロファイルでログを表示できます。 クエリの例は次のとおりです。              AzureDiagnostics | where Category == "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Sent to origin shield の非推奨化
生ログのプロパティ **isSentToOriginShield** は非推奨化され、新しいフィールド **isReceivedFromClient** に置き換えられました。 非推奨化されたフィールドを既に使用している場合は、新しいフィールドを使用してください。 

生ログには、CDN エッジ (子 POP) とオリジン シールドの両方から生成されたログが含まれます。 オリジン シールドとは、世界中に戦略的に配置された親ノードを指します。 これらのノードが配信元サーバーと通信を行うことで、配信元におけるトラフィック負荷が軽減されます。 

オリジン シールドに向かう各要求について、2 つのログ エントリが存在します。

* エッジ ノード由来
* オリジン シールド由来 

エグレスまたは応答がエッジ ノード由来かオリジン シールド由来かは、isReceivedFromClient フィールドを使用して正しいデータを取得することで区別できます。 

この値が false の場合、その要求に対する応答はオリジン シールドからエッジ ノードに返されたことを意味します。 このアプローチは、生ログを課金データと比較する際に有効です。 オリジン シールドからエッジ ノードへのエグレスに料金は発生しません。 料金は、エッジ ノードからクライアントへのエグレスで発生します。 

**Log Analytics のオリジン シールドで生成されたログを除外するための Kusto クエリ サンプル。**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> HTTP 生ログ機能は、**2020 年 2 月 25 日**以降に作成または更新されたプロファイルで自動的に使用可能になります。 それよりも前に作成した CDN プロファイルの場合、ログ記録のセットアップ後に CDN エンドポイントを更新する必要があります。 たとえば、CDN エンドポイントで geo フィルタリングに移動し、そのワークロードに関係のない国および地域をブロックし、保存することができます。 

## <a name="next-steps"></a>次の手順
この記事では、Microsoft CDN サービスの HTTP 生ログを有効にしました。

Azure CDN とこの記事で言及しているその他の Azure サービスの詳細については、次をご覧ください。

* Azure CDN の使用パターンを[分析する](cdn-log-analysis.md)。

* 詳細については、「[Azure Monitor の概要](https://docs.microsoft.com/azure/azure-monitor/overview)」を参照してください。

* [Azure Monitor で Log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) を構成する。
