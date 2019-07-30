---
title: Azure Application Gateway のアクセス ログ、パフォーマンス ログ、バックエンドの正常性、メトリックの監視
description: Azure Application Gateway のアクセス ログとパフォーマンス ログを有効にし、管理する方法について説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: 39317c0448168bc2ed8fdd0455a210254887d496
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655385"
---
# <a name="back-end-health-diagnostic-logs-and-metrics-for-application-gateway"></a>Application Gateway のバックエンドの正常性、診断ログ、およびメトリック

Azure Application Gateway を使用すると、次の方法でリソースを監視できます。

* [バックエンドの正常性](#back-end-health):Application Gateway は、Azure portal と PowerShell を介して、バックエンド プール内のサーバーの正常性を監視する機能を提供します。 バックエンド プールの正常性は、パフォーマンスの診断ログでも確認できます。

* [ログ](#diagnostic-logging):リソースのパフォーマンス、アクセス、その他のデータを記録したログは、監視のために保存し使用することができます。

* [メトリック](#metrics):現在、Application Gateway にはパフォーマンス カウンターを表示する 7 つのメトリックがあります。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>バックエンドの正常性

Application Gateway は、ポータル、PowerShell、およびコマンド ライン インターフェイス (CLI) を介して、バックエンド プールの各メンバーの正常性を監視する機能を提供します。 バックエンド プールの正常性の集計された概要は、パフォーマンスの診断ログでも確認できます。 

バックエンドの正常性レポートには、バックエンドのインスタンスへの Application Gateway の正常性プローブの出力が反映されます。 プローブが正常に完了し、バックエンドがトラフィックを受信できる場合は、正常と見なされます。 それ以外の場合は異常と見なされます。

> [!IMPORTANT]
> Application Gateway サブネット上にネットワーク セキュリティ グループ (NSG) がある場合は、Application Gateway サブネットで 65503 ～ 65534 のポート範囲をインバウンド トラフィック用に開いてください。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに対する変更を開始することはできません。


### <a name="view-back-end-health-through-the-portal"></a>ポータルを介したバックエンドの正常性の表示

ポータルでは、バックエンドの正常性が自動的に提供されます。 既存の Application Gateway で、 **[監視]**  >  **[バックエンド正常性]** を選択します。 

バックエンド プール内の各メンバーはこのページに表示されます (NIC、IP、FQDN のいずれであっても)。 バックエンド プール名、ポート、バックエンドの HTTP 設定名、および正常性の状態が表示されます。 正常性の状態を表す有効値は、**正常**、**異常**、および**不明**です。

> [!NOTE]
> バックエンドの正常性の状態が**不明**と表示される場合、バックエンドへのアクセスが、NSG のルール、ユーザー定義ルート (UDR)、または仮想ネットワーク内のカスタム DNS によってブロックされていないことを確認します。

![バックエンドの正常性][10]

### <a name="view-back-end-health-through-powershell"></a>PowerShell を介したバックエンドの正常性の表示

次の PowerShell コードは、`Get-AzApplicationGatewayBackendHealth` コマンドレットを使用してバックエンドの正常性を表示する方法を示します。

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Azure CLI を介したバックエンドの正常性の表示

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>結果

次のスニペットは、応答の一例を示します。

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

## <a name="diagnostic-logging"></a>診断ログ

Azure の各種ログを使用して、アプリケーション ゲートウェイの管理とトラブルシューティングを行うことができます。 一部のログにはポータルからアクセスできます。 どのログも Azure Blob Storage から抽出し、[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)、Excel、Power BI などのさまざまなツールで表示できます。 各種ログの詳細については、以下の一覧を参照してください。

* **アクティビティ ログ**:[Azure アクティビティ ログ](../monitoring-and-diagnostics/insights-debugging-with-events.md) (以前の操作ログと監査ログ) を使用すると、Azure サブスクリプションに送信されるすべての操作とその操作の状態を表示できます。 アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。
* **アクセス ログ**:このログを使用して Application Gateway のアクセス パターンを表示し、重要な情報を分析できます。 これには、呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。アクセス ログは 300 秒ごとに収集されます。 このログには、Application Gateway のインスタンスごとに 1 つのレコードが含まれます。 Application Gateway のインスタンスは、instanceId プロパティで識別されます。
* **パフォーマンス ログ**:このログを使用すると、Application Gateway のインスタンスの実行状況を確認できます。 このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。 パフォーマンス ログは 60 秒ごとに収集されます。
* **ファイアウォール ログ**:このログを使用すると、Web アプリケーション ファイアウォールが構成された Application Gateway の、検出モードまたは防止モードでログに記録された要求を表示することができます。

> [!NOTE]
> ログは、Azure Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースには使用できません。 2 つのモデルについて理解を深めるには、「[Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)」を参照してください。

ログを保存するための 3 つのオプションがあります。

* **ストレージ アカウント**:ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* **イベント ハブ**:イベント ハブは、他のセキュリティ情報/イベント管理 (SEIM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。
* **Azure Monitor ログ**: Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

### <a name="enable-logging-through-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 アクセス ログとパフォーマンス ログで使用可能なデータの収集を開始するには、これらのログを有効にする必要があります。 ログ記録を有効にするには、次の手順に従います。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値の形式は、/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名\> です。 サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、Azure Portal で確認できます。

    ![ポータル: ストレージ アカウントのリソース ID](./media/application-gateway-diagnostics/diagnostics1.png)

2. ログを有効にするアプリケーション ゲートウェイのリソース ID をメモしておきます。 この値の形式は、/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Network/applicationGateways/\<Application Gateway 名\> です。 この情報は、ポータルで確認できます。

    ![ポータル: Application Gateway のリソース ID](./media/application-gateway-diagnostics/diagnostics2.png)

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>アクティビティ ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

### <a name="enable-logging-through-the-azure-portal"></a>Azure Portal を使用したログの有効化

1. Azure portal で、ご使用のリソースを見つけ、 **[診断設定]** を選択します。

   Application Gateway では、次の 3 つのログを使用できます。

   * アクセス ログ
   * パフォーマンス ログ
   * ファイアウォール ログ

2. データの収集を開始するには、 **[診断を有効にする]** を選択します。

   ![診断の有効化][1]

3. **[診断設定]** ページに、診断ログの設定が表示されます。 この例では、Log Analytics を使用してログを保存します。 イベント ハブとストレージ アカウントを使用して診断ログを保存することもできます。

   ![構成プロセスの開始][2]

5. 設定の名前を入力し、設定を確認した後、 **[保存]** を選択します。

### <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、既定では Azure によって生成されます。 ログは、Azure のイベント ログ ストアに 90 日間保存されます。 これらのログの詳細については、「[イベントとアクティビティ ログの表示](../monitoring-and-diagnostics/insights-debugging-with-events.md)」を参照してください。

### <a name="access-log"></a>アクセス ログ

アクセス ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次の v1 の例に示すように、Application Gateway の各アクセスは JSON 形式でログに記録されます。

|値  |説明  |
|---------|---------|
|instanceId     | 要求を処理した Application Gateway のインスタンス。        |
|clientIP     | 要求の送信元 IP。        |
|clientPort     | 要求の送信元ポート。       |
|httpMethod     | 要求で使用される HTTP メソッド。       |
|requestUri     | 受信した要求の URI。        |
|RequestQuery     | **Server-Routed**:要求が送信されたバックエンド プールのインスタンス。</br>**X-AzureApplicationGateway-LOG-ID**:要求に使用する関連付け ID。 この ID を使用すると、バックエンド サーバー上のトラフィックの問題をトラブルシューティングできます。 </br>**SERVER-STATUS**:Application Gateway がバックエンドから受信した HTTP 応答コード。       |
|UserAgent     | HTTP 要求ヘッダーからのユーザー エージェント。        |
|httpStatus     | Application Gateway からクライアントに返される HTTP 状態コード。       |
|httpVersion     | 要求の HTTP バージョン。        |
|receivedBytes     | 受信したパケットのサイズ (バイト単位)。        |
|sentBytes| 送信したパケットのサイズ (バイト単位)。|
|timeTaken| 要求の処理および応答の送信にかかった時間 (ミリ秒単位)。 これは、Application Gateway がHTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの間隔として計算されます。 通常、timeTaken フィールドには、要求パケットと応答パケットがネットワーク経由で移動する時間が含まれています。 |
|sslEnabled| バックエンド プールへの通信に SSL を使用するかどうか。 有効な値は on と off です。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off"
    }
}
```
Application Gateway と WAF v2 の場合、ログにはさらにいくつかの情報が表示されます。

|値  |説明  |
|---------|---------|
|instanceId     | 要求を処理した Application Gateway のインスタンス。        |
|clientIP     | 要求の送信元 IP。        |
|clientPort     | 要求の送信元ポート。       |
|httpMethod     | 要求で使用される HTTP メソッド。       |
|requestUri     | 受信した要求の URI。        |
|RequestQuery     | **Server-Routed**:要求が送信されたバックエンド プールのインスタンス。</br>**X-AzureApplicationGateway-LOG-ID**:要求に使用する関連付け ID。 この ID を使用すると、バックエンド サーバー上のトラフィックの問題をトラブルシューティングできます。 </br>**SERVER-STATUS**:Application Gateway がバックエンドから受信した HTTP 応答コード。       |
|UserAgent     | HTTP 要求ヘッダーからのユーザー エージェント。        |
|httpStatus     | Application Gateway からクライアントに返される HTTP 状態コード。       |
|httpVersion     | 要求の HTTP バージョン。        |
|receivedBytes     | 受信したパケットのサイズ (バイト単位)。        |
|sentBytes| 送信したパケットのサイズ (バイト単位)。|
|timeTaken| 要求の処理および応答の送信にかかった時間 (ミリ秒単位)。 これは、Application Gateway がHTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの間隔として計算されます。 通常、timeTaken フィールドには、要求パケットと応答パケットがネットワーク経由で移動する時間が含まれています。 |
|sslEnabled| バックエンド プールへの通信に SSL を使用するかどうか。 有効な値は on と off です。|
|sslCipher| SSL 通信に使用されている暗号スイート (SSL が有効な場合)|
|sslProtocol| 使用されている SSL プロトコル (SSL が有効な場合)|
|serverRouted| アプリケーション ゲートウェイから要求がルーティングされる先のバックエンド サーバー。|
|serverStatus| バックエンド サーバーの HTTP 状態コード。|
|serverResponseLatency| バックエンド サーバーからの応答の待機時間。|
|host| 要求のホスト ヘッダーに表示されているアドレス。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "52.231.230.101"
    }
}
```

### <a name="performance-log"></a>パフォーマンス ログ

パフォーマンス ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 パフォーマンス ログ データは、1 分間隔で生成されます。 次のデータがログに記録されます。


|値  |説明  |
|---------|---------|
|instanceId     |  パフォーマンス データを生成中の Application Gateway のインスタンス。 複数インスタンスの Application Gateway の場合、インスタンスごとに 1 行が使用されます。        |
|healthyHostCount     | バックエンド プール内の正常なホストの数。        |
|unHealthyHostCount     | バックエンド プール内の異常なホストの数。        |
|requestCount     | 処理された要求の数。        |
|latency | インスタンスから要求を処理するバックエンドへの要求の平均待機時間 (ミリ秒単位)。 |
|failedRequestCount| 失敗した要求の数。|
|throughput| 最後のログ以降の平均スループット (1 秒あたりのバイト数)。|

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

ファイアウォール ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 このログを使用するには、Application Gateway で Web アプリケーション ファイアウォールを構成する必要もあります。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次のデータがログに記録されます。


|値  |説明  |
|---------|---------|
|instanceId     | ファイアウォール データを生成中の Application Gateway のインスタンス。 複数インスタンスの Application Gateway の場合、インスタンスごとに 1 行が使用されます。         |
|clientIp     |   要求の送信元 IP。      |
|clientPort     |  要求の送信元ポート。       |
|requestUri     | 受信した要求の URL。       |
|ruleSetType     | ルール セットの種類。 使用できる値は OWASP です。        |
|ruleSetVersion     | 使用されるルール セットのバージョン。 使用できる値は 2.2.9 と 3.0 です。     |
|ruleId     | トリガーするイベントのルール ID。        |
|message     | トリガーするイベントのわかりやすいメッセージ。 詳細は details セクションに示されます。        |
|action     |  要求に対して実行されるアクション。 使用できる値は Blocked と Allowed です。      |
|site     | ログの生成対象のサイト。 ルールがグローバルであるため、現時点では Global のみ表示されます。|
|details     | トリガーするイベントの詳細。        |
|details.message     | ルールの説明。        |
|details.data     | 要求で見つかった、ルールに一致するデータ。         |
|details.file     | ルールが含まれている構成ファイル。        |
|details.line     | イベントをトリガーした、構成ファイル内の行番号。       |

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

* **Azure Tools**:Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)」を参照してください。
* **Power BI**:[Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>アクセス ログ、パフォーマンス ログ、ファイアウォール ログの表示と分析

[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)では、BLOB ストレージ アカウントからカウンターとイベントのログ ファイルを収集できます。 このツールには、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>GoAccess を介してアクセス ログを分析する

Microsoft は、人気のある [GoAccess](https://goaccess.io/) ログ アナライザーをインストールし、Application Gateway アクセス ログに対して実行する、Resource Manager テンプレートを発行しています。 GoAccess では、ユニーク ビジター、要求されたファイル、ホスト、オペレーティング システム、ブラウザー、HTTP 状態コードなど、重要な HTTP トラフィック統計情報が提供されます。 詳細については、[GitHub の Resource Manager テンプレート フォルダーにある Readme ファイル](https://aka.ms/appgwgoaccessreadme)を参照してください。

## <a name="metrics"></a>メトリック

メトリックは特定の Azure リソース用の機能で、ポータルでパフォーマンス カウンターを表示できます。 Application Gateway に関しては、次のメトリックを利用できます。

- **現在の接続数**
- **失敗した要求**
- **正常なホストの数**

   バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。


- **応答の状態**

   応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。

- **スループット**
- **要求の合計数**
- **異常なホストの数**

   バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常/異常なホストを表示できます。

アプリケーション ゲートウェイに移動して **[監視]** の **[メトリック]** を選択します。 利用できる値を表示するには、 **[メトリック]** ドロップダウン リストを選択します。

次の画像では、最後の 30 分間に表示された 3 つのメトリックの例を確認できます。

[![](media/application-gateway-diagnostics/figure5.png "メトリック ビュー")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

現在のメトリックの一覧を確認するには、「[Azure Monitor のサポートされるメトリック](../azure-monitor/platform/metrics-supported.md)」を参照してください。

### <a name="alert-rules"></a>アラート ルール

リソースのメトリックに基づいてアラート ルールを開始できます。 たとえば、Application Gateway のスループットが指定した期間にしきい値を上回るか下回る場合、またはしきい値に等しい場合、アラートで webhook を呼び出したり、管理者に電子メールを送信したりできます。

次の例では、スループットのしきい値を超えた後に管理者に電子メールを送信するアラート ルールの作成手順を説明します。

1. **[メトリック アラートの追加]** を選択して **[ルールの追加]** ページを開きます。 このページには、メトリック ページからもアクセスできます。

   ![[メトリック アラートの追加] ボタン][6]

2. **[ルールの追加]** ページで、名前、条件、通知の各セクションに入力して、 **[OK]** を選択します。

   * **[条件]** セレクターに入力できるのは、 **[より大きい]** 、 **[以上]** 、 **[未満]** 、または **[以下]** の 4 つの値です。

   * **[期間]** セレクターで、5 分から 6 時間までの期間を選択します。

   * **[メールの所有者、投稿者、閲覧者]** を選択すると、そのリソースのアクセス権を持つユーザーに基づいて電子メールを動的に送信できます。 それ以外の場合は、ユーザーのコンマ区切りの一覧を **[追加の管理者メール]** ボックスに指定できます。

   ![[ルールの追加] ページ][7]

しきい値を超えた場合、次の図のような電子メールが送信されます。

![しきい値違反の電子メール][8]

メトリック アラートを作成すると、アラートの一覧が表示されます。 この一覧には、すべてのアラート ルールの概要が示されます。

![アラートとルールの一覧][9]

アラート通知の詳細については、「[Microsoft Azure のアラートの概要](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」を参照してください。

webhook の詳細および webhook とアラートを使用する方法については、「[Azure メトリック アラートでの webhook の構成](../azure-monitor/platform/alerts-webhooks.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)を使用して、カウンターとイベント ログを視覚化します。
* [Power BI を使用した Azure アクティビティ ログの視覚化](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)に関するブログ
* [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ

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
