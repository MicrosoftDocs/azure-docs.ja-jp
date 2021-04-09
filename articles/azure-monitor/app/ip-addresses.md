---
title: Azure Monitor で使用される IP アドレス
description: Application Insights で必要なサーバー ファイアウォール例外
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 56ff33cc0a34cb254ca88f96d69a07bc131bebf4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101714036"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>Azure Monitor で使用される IP アドレス

[Azure Monitor](../overview.md) ではたくさんの IP アドレスが使用されます。 Azure Monitor は Log Analytics と Application Insights に加え、中心的なプラットフォーム メトリクスとログで構成されています。 監視しているアプリまたはインフラストラクチャがファイアウォールの背後でホストされているとき、場合によっては、これらのアドレスを知っている必要があります。

> [!NOTE]
> これらは静的アドレスですが、しばしば変更の必要が生じることがあります。 Application Insights のトラフィックは、受信ファイアウォール規則を必要とする可用性の監視と webhook を除き、すべて送信トラフィックです。

> [!TIP]
> Azure ネットワーク セキュリティ グループを使用している場合は、Azure [ネットワーク サービス タグ](../../virtual-network/service-tags-overview.md)を使用してアクセスを管理できます。 ハイブリッド/オンプレミスのリソースのアクセスを管理している場合は、対応する IP アドレス リストを [JSON ファイル](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)としてダウンロードできます。これは毎週更新されます。 この記事のすべての例外に対応するには、サービス タグ `ActionGroup`、`ApplicationInsightsAvailability`、および `AzureMonitor` を使用する必要があります。

または、 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom をお気に入りの RSS/ATOM リーダーに追加してこのページを RSS フィードとしてサブスクライブすると、最新の変更に関する通知を受け取ることができます。


## <a name="outgoing-ports"></a>送信ポート

Application Insights SDK や Status Monitor がポータルにデータを送信できるように、サーバーのファイアウォールでいくつかの送信ポートを開く必要があります。

| 目的 | URL | IP | Port |
| --- | --- | --- | --- |
| テレメトリ |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| ライブ メトリック ストリーム | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Status Monitor

Status Monitor の構成 - 変更を加える場合にのみ必要です。

| 目的 | URL | IP | Port |
| --- | --- | --- | --- |
| 構成 |`management.core.windows.net` | |`443` |
| 構成 |`management.azure.com` | |`443` |
| 構成 |`login.windows.net` | |`443` |
| 構成 |`login.microsoftonline.com` | |`443` |
| 構成 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 構成 |`auth.gfx.ms` | |`443` |
| 構成 |`login.live.com` | |`443` |
| インストール | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>可用性テスト

これは [可用性 Web テスト](./monitor-web-app-availability.md) の実行元のアドレスの一覧です。 アプリで Web テストを実行しようとするが、Web サーバーが特定のクライアントの処理に制限されている場合は、可用性テスト サーバーからの着信トラフィックを許可する必要があります。


> [!NOTE]
> パブリック Azure の可用性テスト エージェントとの直接の受信通信を許可できないプライベート仮想ネットワーク内にあるリソースの場合、唯一のオプションは、[独自のカスタム可用性テストを作成してホストする](availability-azure-functions.md)ことです。

### <a name="service-tag"></a>サービス タグ

Azure ネットワーク セキュリティ グループを使用している場合は、単に **受信ポートの規則** を追加して Application Insights 可用性テストからのトラフィックを許可します。そのためには、**サービスタグ** を **ソース** として選択し、**ApplicationInsightsAvailability** を **ソースサービスタグ** として選択します。

>[!div class="mx-imgBorder"]
>![[設定] で [受信セキュリティ規則] を選択してから、タブの最上部にある [追加] を選択します](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![受信セキュリティ規則タブを追加します](./media/ip-addresses/add-inbound-security-rule2.png)

これらのアドレスからの着信トラフィック用にポート 80 (http) と 443 (https) を開きます （IP アドレスは場所別にグループ化されます）。

### <a name="addresses-grouped-by-location"></a>場所別にグループ化されたアドレス

> [!NOTE]
> これらのアドレスは、クラスレス ドメイン間ルーティング (CIDR) 表記を使用して一覧表示されます。 つまり、`51.144.56.112/28` のようなエントリは、`51.144.56.112` で始まり、`51.144.56.127`で終わる 16 IP に相当します。

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

#### <a name="azure-government"></a>Azure Government

Azure パブリック クラウドのお客様の場合は必要ありません。

```
USGov Virginia
52.227.229.80/31


USGov Arizona
52.244.35.112/31


USGov Texas
52.243.157.80/31


USDoD Central
52.182.23.96/31


USDoD East
52.181.33.96/31

```

## <a name="application-insights--log-analytics-apis"></a>Application Insights および Log Analytics API

| 目的 | URI |  IP | Port |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Azure Pipeline 注釈拡張機能 | aigs1.aisvc.visualstudio.com |動的|443 | 

## <a name="application-insights-analytics"></a>Application Insights Analytics

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| Analytics ポータル | analytics.applicationinsights.io | 動的 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 動的 | 80,443 |
| メディア CDN | applicationanalyticsmedia.azureedge.net | 動的 | 80,443 |

注: *. applicationinsights.io ドメインは Application Insights チームによって所有されています。

## <a name="log-analytics-portal"></a>Log Analytics Portal

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| ポータル | portal.loganalytics.io | 動的 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 動的 | 80,443 |

注: *.loganalytics.io ドメインは Log Analytics チームによって所有されています。

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure portal 拡張機能

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| Application Insights 拡張機能 | stamp2.app.insightsportal.visualstudio.com | 動的 | 80,443 |
| Application Insights 拡張機能 CDN | insightsportal prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 動的 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net<br/>js.monitor.azure.com | 動的 | 80,443 |

## <a name="action-group-webhooks"></a>アクション グループ Webhook

アクション グループによって使用される IP アドレスの一覧は、[Get-AzNetworkServiceTag PowerShell コマンド](/powershell/module/az.network/Get-AzNetworkServiceTag)で問い合わせることができます。

### <a name="action-groups-service-tag"></a>アクション グループ サービス タグ
ソース IP アドレスの変更管理は、非常に時間がかかることがあります。 **サービス タグ** を使用すると、構成を更新する必要がなくなります。 サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 Microsoft は IP アドレスを管理し、アドレスが変更されたとき、サービス タグを自動更新します。アクション グループのネットワーク セキュリティ規則を更新する必要がありません。

1. [Azure サービス] の下の Azure portal で、"*ネットワーク セキュリティ グループ*" を検索します。
2. **[追加]** をクリックして、ネットワーク セキュリティ グループを作成します。

   1. リソース グループ名を追加し、"*インスタンスの詳細*" を入力します。
   1. **[確認および作成]** をクリックして、 *[作成]* をクリックします。
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="ネットワーク セキュリティ グループの作成方法の例。"border="true":::

3. [リソース グループ] にアクセスし、作成した "*ネットワーク セキュリティ グループ*" をクリックします。

    1. *[受信セキュリティ規則]* を選択します。
    1. **[追加]** をクリックします。
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="サービス タグを追加する方法の例。" border="true":::

4. 新しいウィンドウが右ペインに表示されます。
    1.  ソースを選択します。"**サービス タグ**"
    1.  [ソース サービス タグ]:**ActionGroup**
    1.  **[追加]** をクリックします。
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="サービス タグを追加する方法の例。" border="true":::


## <a name="profiler"></a>プロファイラー

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| エージェント | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| ポータル | gateway.azureserviceprofiler.net | 動的 | 443
| ストレージ | *.core.windows.net | 動的 | 443

## <a name="snapshot-debugger"></a>スナップショット デバッガー

> [!NOTE]
> プロファイラーとスナップショット デバッガーは、同じ IP アドレスのセットを共有します。

| 目的 | URI | IP | Port |
| --- | --- | --- | --- |
| エージェント | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| ポータル | gateway.azureserviceprofiler.net | 動的 | 443
| ストレージ | *.core.windows.net | 動的 | 443