---
title: Azure CDNの監視、メトリック、生ログ
description: この記事では、Azure CDN の監視、メトリック、生ログを設定して使用する方法について説明します。
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 8a4b48586c564ee5d14a0768156b0477e2935ccf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575448"
---
# <a name="real-time-monitoring-metrics-and-access-logs-for-azure-cdn"></a>Azure CDN にのリアルタイムの監視、メトリック、アクセス ログ
Microsoft Azure CDN を使用すると、次の方法でリソースを監視して、問題のトラブルシューティング、追跡、デバッグに役立てることができます。 

* 生ログでは、CDN で受信されるすべての要求についての豊富な情報が提供されます。 生ログは、アクティビティ ログとは異なります。 アクティビティ ログは、Azure リソースに対して行われた操作を可視化します。
* メトリック。CDN に関する 4 つの主要なメトリック (バイト ヒット率、要求数、応答サイズ、合計待機時間) が表示されます。 メトリックを分類するためのさまざまなディメンションも用意されています。
* アラート。お客様は主要なメトリックに対するアラートを設定できます
* 追加メトリック。お客様は、Azure Log Analytics を使用して、価値のある追加のメトリックを有効にできます。 また、Azure Log Analytics の下にある他のいくつかのメトリックのクエリ サンプルも提供されています。

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

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="CDN プロファイルの診断設定を追加する。" border="true":::
    
    > [!IMPORTANT]
    > 生ログは、集計された HTTP ステータス コード ログがエンドポイント レベルで利用できるときにのみ、プロファイル レベルで利用できます。

4. **[診断設定]** の **[診断設定の名前]** で診断設定の名前を入力します。

5. **AzureCdnAccessLog** を選択し、保持期間を日数で設定します。

6. **[宛先の詳細]** を選択します。 出力先の選択肢:
    * **Log Analytics への送信**
        * **サブスクリプション** と **Log Analytics ワークスペース** を選択します。
    * **ストレージ アカウントへのアーカイブ**
        * **サブスクリプション** と **ストレージ アカウント** を選択します。
    * **イベント ハブへのストリーム**
        * **[サブスクリプション]** 、 **[イベント ハブの名前空間]** 、 **[イベント ハブ名 (オプション)]** 、 **[イベント ハブ ポリシー名]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="ログ設定の出力先を構成する。" border="true":::

7. **[保存]** を選択します。

## <a name="configuration---azure-powershell"></a>構成 - Azure PowerShell

生ログの診断設定を構成するには、[Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) を使用します。

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

| プロパティ  | 説明 |
| ------------- | ------------- |
| BackendHostname | 要求がバックエンドに転送されている場合、このフィールドはバックエンドのホスト名を表します。 要求がリージョンのキャッシュにリダイレクトまたは転送された場合は (ルーティング規則でキャッシュが有効になっている場合)、このフィールドは空白になります。 |
| CacheStatus | キャッシュのシナリオの場合、このフィールドでは POP でのキャッシュのヒットとミスが定義されます |
| ClientIp | 要求を行ったクライアントの IP アドレス。 要求に X-Forwarded-For ヘッダーがあった場合、同じものからクライアント IP が選択されます。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| HttpMethod | 要求で使用される HTTP メソッド。 |
| HttpStatusCode | プロキシから返された HTTP 状態コード。 |
| HttpStatusDetails | 要求の結果の状態。 この文字列値の意味は、状態参照テーブルで確認できます。 |
| HttpVersion | 要求または接続の種類。 |
| POP | 要求が到着したエッジの短い名前。 |
| RequestBytes | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。 |
| RequestUri | 受信した要求の URI。 |
| ResponseBytes | 応答としてバックエンド サーバーによって送信されたバイト数。  |
| RoutingRuleName | 要求が一致したルーティング規則の名前。 |
| RulesEngineMatchNames | 要求が一致した規則の名前。 |
| SecurityProtocol | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。 |
| SentToOriginShield </br> (非推奨) * **次のセクションの非推奨に関する注意事項を参照してください。**| true の場合、要求は、エッジ ポップではなく、オリジン シールド キャッシュから応答されました。 オリジン シールドは、キャッシュ ヒット率を向上させる目的で使用される親キャッシュです。 |
| isReceivedFromClient | true の場合、要求はクライアントから送信されます。 false の場合、要求はエッジ (子 POP) で不成功となり、オリジン シールド (親 POP) から応答されます。 |
| TimeTaken | Front Door への要求の最初のバイトから、出される応答の最後のバイトまでの時間の長さ (秒単位)。 |
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |
| UserAgent | クライアントで使用されたブラウザーの種類。 |
| ErrorInfo | このフィールドには、トラブルシューティング領域を絞り込むための特定の種類のエラーが含まれています。 </br> 次の値を指定できます。 </br> **NoError**:エラーが見つからなかったことを示します。 </br> **CertificateError**:一般的な SSL 証明書エラー。</br> **CertificateNameCheckFailed**:SSL 証明書のホスト名が無効であるか、一致しません。 </br> **ClientDisconnected**:クライアント ネットワーク接続による要求の失敗。 </br> **UnspecifiedClientError**:一般的なクライアント エラー。 </br> **InvalidRequest**:無効な要求。 ヘッダー、本文、URL の形式が間違っていることが原因で発生する可能性があります。 </br> **DNSFailure**:DNS エラー。 </br> **DNSNameNotResolved**:サーバーの名前またはアドレスを解決できませんでした。 </br> **OriginConnectionAborted**:配信元との接続が突然停止しました。 </br> **OriginConnectionError**:一般的な配信元接続エラー。 </br> **OriginConnectionRefused**:配信元との接続を確立できませんでした。 </br> **OriginError**:一般的な配信元エラー。 </br> **OriginInvalidResponse**:配信元から無効な応答または認識できない応答が返されました。 </br> **OriginTimeout**:配信元要求のタイムアウト期間が経過しました。 </br> **ResponseHeaderTooBig**:配信元から返された応答ヘッダーが大きすぎます。 </br> **RestrictedIP**:制限付き IP のため、要求はブロックされました。 </br> **SSLHandshakeError**:SSL ハンドシェイク エラーのため、配信元との接続を確立できません。 </br> **UnspecifiedError**:テーブルのいずれのエラーにも適合しなかったエラーが発生しました。 |
| TimeToFirstByte | Microsoft CDN が要求を受信してから最初のバイトがクライアントに送信されるまでの時間の長さ (ミリ秒単位)。 時間は Microsoft 側からのみ測定されます。 クライアント側のデータは測定されません。 |
> [!NOTE]
> クエリを実行することによって、Log Analytics プロファイルでログを表示できます。 サンプル クエリは次のようになります。
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Sent to origin shield の非推奨化
生ログのプロパティ **isSentToOriginShield** は非推奨化され、新しいフィールド **isReceivedFromClient** に置き換えられました。 非推奨化されたフィールドを既に使用している場合は、新しいフィールドを使用してください。 

生ログには、CDN エッジ (子 POP) とオリジン シールドの両方から生成されたログが含まれます。 オリジン シールドとは、世界中に戦略的に配置された親ノードを指します。 これらのノードが配信元サーバーと通信を行うことで、配信元におけるトラフィック負荷が軽減されます。 

オリジン シールドに向かう各要求について、2 つのログ エントリが存在します。

* エッジ ノード由来
* オリジン シールド由来 

エグレスまたは応答がエッジ ノード由来かオリジン シールド由来かは、**isReceivedFromClient** フィールドを使用して正しいデータを取得することで区別できます。 

この値が false の場合、その要求に対する応答はオリジン シールドからエッジ ノードに返されたことを意味します。 このアプローチは、生ログを課金データと比較する際に有効です。 オリジン シールドからエッジ ノードへのエグレスに料金は発生しません。 料金は、エッジ ノードからクライアントへのエグレスで発生します。 

**Log Analytics のオリジン シールドで生成されたログを除外するための Kusto クエリ サンプル。**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> HTTP 生ログ機能は、**2020 年 2 月 25 日** 以降に作成または更新されたプロファイルで自動的に使用可能になります。 それよりも前に作成した CDN プロファイルの場合、ログ記録のセットアップ後に CDN エンドポイントを更新する必要があります。 たとえば、CDN エンドポイントで geo フィルタリングに移動し、そのワークロードに関係のない国および地域をブロックし、保存することができます。


## <a name="metrics"></a>メトリック
Microsoft Azure CDN は Azure Monitor と統合されており、問題の追跡、トラブルシューティング、デバッグに役立つ 4 つの CDN メトリックが発行されます。 

メトリックはグラフとして表示され、PowerShell、CLI、API を使用してアクセスできます。 CDN メトリックは無料です。

Microsoft Azure CDN では、メトリックが 60 秒間隔で測定されて送信されます。 メトリックがポータルに表示されるまでに、最大で 3 分かかることがあります。 

詳細については、「[Azure Monitor metrics](../azure-monitor/essentials/data-platform-metrics.md)」(Azure Monitor メトリック) を参照してください。

**Microsoft Azure CDN でサポートされているメトリック**

| メトリック  | 説明 | Dimensions |
| ------------- | ------------- | ------------- |
| バイト ヒット率* | 総エグレスに対して計算される、CDN キャッシュからのエグレスの割合。 | エンドポイント |
| RequestCount | CDN によって処理されたクライアント要求の数。 | エンドポイント </br> クライアントの国。 </br> クライアントのリージョン。 </br> HTTP の状態です。 </br> HTTP 状態コード。 |
| ResponseSize | CDN エッジからクライアントに応答として送信されたバイト数。 |エンドポイント </br> クライアントの国。 </br> クライアントのリージョン。 </br> HTTP の状態です。 </br> HTTP 状態コード。 |
| TotalLatency | クライアント要求が CDN によって受信されてから、**最後の応答バイトが CDN からクライアントに送信されるまで** の合計時間。 |エンドポイント </br> クライアントの国。 </br> クライアントのリージョン。 </br> HTTP の状態です。 </br> HTTP 状態コード。 |

***バイト ヒット率 = (エッジからのエグレス - オリジンからのエグレス)/エッジからのエグレス**

バイト ヒット率の計算で除外されるシナリオ:

* ルール エンジンまたはクエリ文字列キャッシュ動作を使用して、キャッシュなしを明示的に構成します。
* ストアなしまたはプライベート キャッシュを使用して、キャッシュ制御ディレクティブを明示的に構成します。

### <a name="metrics-configuration"></a>メトリックの構成

1. Azure portal メニューから、 **[すべてのリソース]**  >>  **\<your-CDN-profile>** を選択します。

2. **[監視]** で **[メトリック]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="CDN プロファイルのメトリック。" border="true":::

3. **[メトリックの追加]** を選択し、追加するメトリックを選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="CDN プロファイルのメトリックを追加して選択する。" border="true":::

4. フィルターを追加するには、 **[フィルターの追加]** を選択します。
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="メトリックにフィルターを適用する。" border="true":::

5. さまざまなディメンション別の傾向を表示するには、 **[分割を適用する]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="メトリックに分割を適用する。" border="true":::

6. 新しいグラフを追加するには、 **[新しいグラフ]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="メトリック ビューに新しいグラフを追加する。" border="true":::

### <a name="alerts"></a>警告

**[監視]**  >>  **[アラート]** を選択することで、Microsoft CDN にアラートを設定できます。

[メトリック] セクションに表示されているメトリックの **[新しいアラート ルール]** を選択します。

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="CDN エンドポイントのアラートを構成する。" border="true":::

アラートは Azure Monitor に基づいて課金されます。 アラートの詳細については、[Azure Monitor のアラート](../azure-monitor/alerts/alerts-overview.md)に関する記事を参照してください。

### <a name="additional-metrics"></a>追加のメトリック
追加のコストがかかりますが、Azure Log Analytics と生ログを使用して、追加のメトリックを有効にすることができます。

1. 診断を有効にする上記の手順に従って、生ログを Log Analytics に送信します。

2. 作成した Log Analytics ワークスペースを選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Log Analytics ワークスペースを選択する" border="true":::   

3. Log Analytics ワークスペースで、 **[全般]** の **[ログ]** を選択します。  次に、 **[作業の開始]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Log Analytics リソース ワークスペース。" border="true":::   
 
4. **[CDN プロファイル]** を選択します。  クエリの例を選択して実行するか、例の画面を閉じてカスタム クエリを入力します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="クエリの例の画面。" border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="クエリの実行。" border="true":::   

4. グラフでデータを表示するには、 **[グラフ]** を選択します。  Azure ダッシュボードにグラフをピン留めするには、 **[ダッシュボードにピン留めする]** を選択します。

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="ダッシュボードにグラフをピン留めします。" border="true"::: 

## <a name="next-steps"></a>次の手順
この記事では、Microsoft CDN サービスの HTTP 生ログを有効にしました。

Azure CDN とこの記事で言及しているその他の Azure サービスの詳細については、次をご覧ください。

* Azure CDN の使用パターンを[分析する](cdn-log-analysis.md)。

* 詳細については、「[Azure Monitor の概要](../azure-monitor/overview.md)」を参照してください。

* [Azure Monitor で Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) を構成する。