---
title: "Application Gateway のアクセス ログ、パフォーマンス ログ、バッグエンドの正常性、およびメトリックの監視 | Microsoft Docs"
description: "Application Gateway のアクセス ログとパフォーマンス ログを有効にし、管理する方法について説明します。"
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 104ef38666957c1317b41a28244e05f3132e7fbf
ms.lasthandoff: 03/30/2017


---
# <a name="backend-health-diagnostics-logging-and-metrics-for-application-gateway"></a>Application Gateway のバッグエンドの正常性、診断ログ、およびメトリック

Azure は、ログとメトリックを使用してリソースを監視する機能を提供しています。 Application Gateway は、バックエンドの正常性、ログ、およびメトリックを使用して、このような機能を提供しています。

[**バックエンドの正常性**](#backend-health) - Application Gateway は、ポータルと PowerShell を介して、バックエンド プール内のサーバーの正常性を監視する機能を提供します。 バックエンド プールの正常性は、パフォーマンスの診断ログでも確認できます。

[**ログ**](#enable-logging-with-powershell) - リソースのパフォーマンス、アクセス、その他を記録したログは、監視のために保存し使用することができます。

[**メトリック**](#metrics) - アプリケーション ゲートウェイが持つメトリックは現在 1 つです。 このメトリックは、アプリケーション ゲートウェイの 1 秒あたりのスループットをバイト単位で測定したものです。

## <a name="backend-health"></a>バックエンドの正常性

Application Gateway は、ポータル、PowerShell、および CLI を介して、バックエンド プールの各メンバーの正常性を監視する機能を提供します。 バックエンド プールの正常性の集計された概要は、パフォーマンスの診断ログでも確認できます。 バックエンドの正常性レポートには、バックエンドのインスタンスへの Application Gateway の正常性プローブの出力が反映されます。 プローブが正常に完了し、バックエンドにトラフィックを送信できる場合は、正常と見なされます。それ以外の場合は異常と見なされます。

> [!IMPORTANT]
> Application Gateway サブネット上に NSG がある場合、Application Gateway サブネットで 65503 ～ 65534 のポート範囲をインバウンド トラフィック用に開いておく必要があります。 これらのポートは、バックエンドの正常性 API が機能するために必要です。


### <a name="view-backend-health-through-the-portal"></a>ポータルを介したバックエンドの正常性の表示

バックエンドの正常性を表示するために必要なものはありません。 既存の Application Gateway で、**[監視]** > **[Backend health (バックエンドの正常性)]** の順に移動します。 バックエンド プール内の各メンバーはこのページに表示されます (NIC、IP、FQDN のいずれであっても)。 バックエンド プール名、ポート、バックエンドの http 設定名、および正常性の状態が表示されます。 正常性の状態を表す有効値は、"正常"、"異常"、および "不明" です。

> [!WARNING]
> バックエンドの正常性状態として **[不明]** が表示される場合、バックエンドへのアクセスが、ネットワーク セキュリティ グループ (NSG) のルールまたは VNet 内のカスタム DNS によって、ブロックされていないことを確認します。

![バックエンドの正常性][10]

### <a name="view-backend-health-with-powershell"></a>PowerShell を使用してバックエンドの正常性を表示する

バックエンドの正常性は、PowerShell 経由でも取得できます。 次の PowerShell コードは、`Get-AzureRmApplicationGatewayBackendHealth` コマンドレットを使用してバックエンドの正常性を取得する方法を示します。

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

結果が返されます。次のスニペットが応答の一例です。

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>診断ログの記録

Azure の各種ログを使用して、アプリケーション ゲートウェイの管理とトラブルシューティングを行うことができます。 これらのログの一部はポータルからアクセスできます。また、すべてのログは、Azure Blob Storage から抽出して、[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)、Excel、PowerBI などのさまざまなツールで表示することができます。 各種ログの詳細については、以下の一覧を参照してください。

* **アクティビティ ログ:** [Azure アクティビティ ログ](../monitoring-and-diagnostics/insights-debugging-with-events.md) (以前の "操作ログ" と "監査ログ") を使用すると、Azure サブスクリプションに送信されているすべての操作とその操作の状態を表示できます。 アクティビティ ログ エントリは既定で収集され、また Azure ポータルで表示できます。
* **アクセス ログ:** このログを使用すると、アプリケーション ゲートウェイのアクセス パターンを確認し、重要な情報 (呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイト数など) を分析できます。 アクセス ログは 300 秒ごとに収集されます。 このログには、アプリケーション ゲートウェイのインスタンスごとに 1 つのレコードが含まれます。 アプリケーション ゲートウェイのインスタンスは、"instanceId" プロパティで識別できます。
* **パフォーマンス ログ:** このログを使用すると、アプリケーション ゲートウェイのインスタンスの実行状況を確認できます。 このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。 パフォーマンス ログは 60 秒ごとに収集されます。
* **ファイアウォール ログ:** このログを使用すると、Web アプリケーション ファイアウォールが構成されたアプリケーション ゲートウェイの、検出モードまたは防止モードでログに記録された要求を表示することができます。

> [!WARNING]
> ログは、リソース マネージャーのデプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイメント モデルのリソースには使用できません。 2 つのモデルについて理解を深めるには、「 [リソース マネージャー デプロイと従来のデプロイを理解する](../azure-resource-manager/resource-manager-deployment-model.md) 」を参照してください。

ログについては、ログの保存方法を選択するための 3 つのオプションがあります。

* ストレージ アカウント - ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* イベント ハブ - イベント ハブは、他の SEIM ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです
* ログ分析 - ログ分析は、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

### <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 アクセス ログとパフォーマンス ログで使用可能なデータの収集を開始するには、これらのログを有効にする必要があります。 ログ記録を有効にするには、次の手順に従って操作します。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値は、/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名\> のような形式です。 サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、プレビュー ポータルで確認できます。

    ![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics1.png)

2. ログを有効にするアプリケーション ゲートウェイのリソース ID をメモしておきます。 この値は、/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Network/applicationGateways/\<アプリケーション ゲートウェイ名\> のような形式です。 この情報は、プレビュー ポータルで確認できます。

    ![プレビュー ポータル - Application Gateway の診断](./media/application-gateway-diagnostics/diagnostics2.png)

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>アクティビティ ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

### <a name="enable-logging-with-azure-portal"></a>Azure ポータルを使用したログの有効化

#### <a name="step-1"></a>手順 1

Azure ポータルで、リソースに移動します。 **[診断ログ]**をクリックします。 初めて診断を構成する場合、ブレードは次の図のようになります。

アプリケーション ゲートウェイでは、次の 3 つのログを利用できます。

* アクセス ログ
* パフォーマンス ログ
* ファイアウォール ログ

データの収集を開始するには、**[診断を有効にする]** をクリックします。

![[診断設定] ブレード][1]

#### <a name="step-2"></a>手順 2.

**[診断設定]** ブレードで、診断ログの収集方法を設定します。 この例では、Log Analytics を使用してログを保存します。 **[Log Analytics]** の下にある **[構成]** をクリックして、ワークスペースを構成します。 Event Hubs とストレージ アカウントを使用して診断ログを保存することもできます。

![[診断] ブレード][2]

#### <a name="step-3"></a>手順 3.

既存の OMS ワークスペースを選択するか、新しい OMS ワークスペースを作成します。 この例では、既存のものを使用します。

![OMS ワークスペース][3]

#### <a name="step-4"></a>手順 4.

完了したら、設定を確認し、 **[保存]** をクリックして設定を保存します。

![選択内容の確認][4]

### <a name="activity-log"></a>アクティビティ ログ

監査ログ (以前の "操作ログ") は、既定では Azure によって生成されます。  ログは、Azure のイベント ログ ストアに 90 日間保存されます。 これらのログの詳細については、「[イベントとアクティビティ ログの表示](../monitoring-and-diagnostics/insights-debugging-with-events.md)」を参照してください。

### <a name="access-log"></a>アクセス ログ

このログは、前の手順で示したように、Application Gateway ごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次の例に示すように、Application Gateway の各アクセスは JSON 形式でログに記録されます。

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2016-04-11T04:24:37Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIP":"37.186.113.170",
        "clientPort":"12345",
        "httpMethod":"HEAD",
        "requestUri":"/xyz/portal",
        "requestQuery":"",
        "userAgent":"-",
        "httpStatus":"200",
        "httpVersion":"HTTP/1.0",
        "receivedBytes":"27",
        "sentBytes":"202",
        "timeTaken":"359",
        "sslEnabled":"off"
    }
}
```

### <a name="performance-log"></a>パフォーマンス ログ

このログは、前の手順で示したように、Application Gateway ごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次のデータがログに記録されます。

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 待機時間は、HTTP 要求の最初のバイトが受信されたときから、HTTP 応答の最後のバイトが送信されたときまでで計算されます。 これは、Application Gateway の処理時間、バックエンドへのネットワーク コスト、およびバックエンドが要求の処理に要した時間を加えたものです。

### <a name="firewall-log"></a>ファイアウォール ログ

このログは、前の手順で示したように、Application Gateway ごとにログを有効にした場合にのみ生成されます。 このログを使用するには、アプリケーション ゲートウェイで Web アプリケーション ファイアウォールを構成する必要もあります。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次のデータがログに記録されます。

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure プレビュー ポータルを使用して、アクティビティ ログから情報を取得します。  それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)」を参照してください。
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>アクセス ログ、パフォーマンス ログ、ファイアウォール ログの表示と分析

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) は、BLOB ストレージ アカウントからカウンターとイベントのログ ファイルを収集でき、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。
> 
> 

## <a name="metrics"></a>メトリック

メトリックは特定の Azure リソース用の機能で、ポータルでパフォーマンス カウンターを表示できます。 Application Gateway では、この記事の執筆時に使用できるメトリックは 1 つです。 そのメトリックとはスループットのことで、ポータルで確認することができます。 アプリケーション ゲートウェイに移動して **[メトリック]**をクリックします。 値を表示するには、**[Available metrics (使用可能なメトリック)]** セクションでスループットを選択します。 次の図は、さまざまな時間範囲のデータの表示に使用できるフィルターの例を示しています。

現在サポートされているメトリックの一覧を表示するには、「 [Supported metrics with Azure Monitor (Azure Monitor でサポートされているメトリック)](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![メトリック ビュー][5]

### <a name="alert-rules"></a>アラート ルール

アラート ルールは、リソースのメトリックに基づいて開始することができます。 つまり、アプリケーション ゲートウェイのスループットが指定した期間にしきい値を上回るか下回る場合、またはしきい値に等しい場合、アラートで webhook を呼び出したり、管理者に電子メールを送信したりできます。

次の例では、スループットのしきい値を超えた後に管理者に電子メールを送信するアラート ルールの作成手順を説明します。

#### <a name="step-1"></a>手順 1

**[Add metric alert (メトリック アラートの追加)]** をクリックして開始します。 このブレードには、メトリック ブレードからもアクセスできます。

![[アラート ルール] ブレード][6]

#### <a name="step-2"></a>手順 2.

**[ルールの追加]** ブレードで、名前、条件、通知セクションを入力し、完了したら **[OK]** をクリックします。

**[条件]** セレクターに入力できるのは、**[より大きい]**、**[以上]**、**[未満]**、または **[以下]** の 4 つの値です。

**[期間]** セレクターでは、5 分から 6 時間までの期間を選択できます。

**[メールの所有者、投稿者、閲覧者]** を選択すると、そのリソースのアクセス権を持つユーザーに基づいて電子メールを動的に送信できます。 それ以外の場合は、ユーザーのコンマ区切りの一覧を **[追加の管理者メール]** テキストボックスに指定できます。

![[ルールの追加] ブレード][7]

しきい値を超えた場合、次の図のような電子メールが送信されます。

![しきい値違反の電子メール][8]

メトリック アラートが作成されると、アラートの一覧が表示されるため、すべてのアラート ルールの概要を確認できます。

![アラート ルールの表示][9]

アラート通知の詳細については、 [アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

webhook と、webhook とアラートを使用する方法の詳細については、「 [Azure メトリック アラートでの webhook の構成](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>次のステップ

* [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* [Power BI を使用した Azure Activity Log の視覚化](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログをご覧ください。
* [Power BI などでの Azure Activity Log の表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログをご覧ください。

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
