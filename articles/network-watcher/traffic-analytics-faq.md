---
title: Azure トラフィック分析についてよく寄せられる質問 | Microsoft Docs
description: トラフィック分析についてよく寄せられる質問の回答を確認します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: damendo
ms.openlocfilehash: f4d97b81bf8f11b8380f04dcbfdb72bd658805ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579235"
---
# <a name="traffic-analytics-frequently-asked-questions"></a>Traffic Analytics についてよく寄せられる質問

この記事では、Azure Network Watcher のトラフィック分析についてよく寄せられる質問の多くをまとめました。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="what-are-the-prerequisites-to-use-traffic-analytics"></a>トラフィック分析を使用する前提条件は何ですか?

Traffic Analytics では、次の前提条件が必要です。

- Network Watcher 対応のサブスクリプション。
- 監視対象のネットワーク セキュリティ グループ (NSG) に対して有効になっている NSG フロー ログ。
- 生のフロー ログを格納するための Azure Storage アカウント。
- 読み取りと書き込みのアクセス権がある Log Analytics ワークスペース

トラフィック分析を有効にするには、アカウントが次のいずれかを満たしている必要があります。

- アカウントには、サブスクリプション スコープで、所有者、共同作成者、閲覧者、またはネットワーク共同作成者のいずれかの Azure ロールが割り当てられている必要があります。
- アカウントに上記のどのロールも割り当てられていない場合、サブスクリプション レベルで次のアクションが割り当てられるカスタム ロールを割り当てる必要があります。
            
    - Microsoft.Network/applicationGateways/read
    - Microsoft.Network/connections/read
    - Microsoft.Network/loadBalancers/read 
    - Microsoft.Network/localNetworkGateways/read 
    - Microsoft.Network/networkInterfaces/read 
    - Microsoft.Network/networkSecurityGroups/read 
    - Microsoft.Network/publicIPAddresses/read
    - Microsoft.Network/routeTables/read
    - Microsoft.Network/virtualNetworkGateways/read 
    - Microsoft.Network/virtualNetworks/read
        
サブスクリプションのユーザーに割り当てられているロールを確認するには、次の手順を実行します。

1. **Login-AzAccount** を使用して、Azure にサインインします。 

2. **Select-AzSubscription** を使用して、必要なサブスクリプションを選択します。 

3. 指定したユーザーに割り当てられているすべてのロールを一覧表示するには、**Get-AzRoleAssignment -SignInName [ユーザーの電子メール アドレス] -IncludeClassicAdministrators** を使用します。 

出力が表示されない場合は、各サブスクリプション管理者に連絡して、コマンドを実行するアクセス権を取得してください。 詳細については、「[Azure PowerShell を使用して Azure ロールの割り当てを追加または削除する](../role-based-access-control/role-assignments-powershell.md)」を参照してください。


## <a name="in-which-azure-regions-is-traffic-analytics-available"></a>Traffic Analytics はどの Azure リージョンで使用できますか?

NSG のトラフィック分析は、次のサポートされているどのリージョンでも使用できます。
- カナダ中部
- 米国中西部
- 米国東部
- 米国東部 2
- 米国中北部
- 米国中南部
- 米国中部
- 米国西部
- 米国西部 2
- フランス中部
- 西ヨーロッパ
- 北ヨーロッパ
- ブラジル南部
- 英国西部
- 英国南部
- オーストラリア東部
- オーストラリア南東部 
- 東アジア
- 東南アジア
- 韓国中部
- インド中部
- インド南部
- 東日本
- 西日本
- US Gov バージニア州
- 中国東部 2

Log Analytics ワークスペースは、次のリージョンに存在する必要があります。
- カナダ中部
- 米国中西部
- 米国東部
- 米国東部 2
- 米国中北部
- 米国中南部
- 米国中部
- 米国西部
- 米国西部 2
- フランス中部
- 西ヨーロッパ
- 北ヨーロッパ
- 英国西部
- 英国南部
- オーストラリア東部
- オーストラリア南東部
- 東アジア
- 東南アジア 
- 韓国中部
- インド中部
- 東日本
- US Gov バージニア州
- 中国東部 2

## <a name="can-the-nsgs-i-enable-flow-logs-for-be-in-different-regions-than-my-workspace"></a>フロー ログを有効化する NSG は、自分のワークスペースとは別のリージョンに存在できますか?

はい。これらの NSG は、Log Analytics ワークスペースとは異なるリージョンに存在することができます。

## <a name="can-multiple-nsgs-be-configured-within-a-single-workspace"></a>1 つのワークスペース内に複数の NSG を構成できますか?

はい。

## <a name="can-i-use-an-existing-workspace"></a>既存のワークスペースを使用できますか?

はい。 既存のワークスペースを選択する場合は、それが新しいクエリ言語に移行されていることを確認してください。 そのワークスペースのアップグレードを望まない場合は、新しいワークスペースを作成する必要があります。 新しいクエリ言語の詳細については、[新しいログ検索への Azure Monitor ログのアップグレード](../azure-monitor/logs/log-query-overview.md)に関するページをご覧ください。

## <a name="can-my-azure-storage-account-be-in-one-subscription-and-my-log-analytics-workspace-be-in-a-different-subscription"></a>自分の Azure Storage アカウントと自分の Log Analytics ワークスペースをそれぞれ別のサブスクリプションに置くことができますか?

はい。自分の Azure Storage アカウントと自分の Log Analytics ワークスペースをそれぞれ別のサブスクリプションに置くことができます。

## <a name="can-i-store-raw-logs-in-a-different-subscription"></a>未処理のログを別のサブスクリプションで格納することはできますか?

はい。 別のサブスクリプションにあるストレージ アカウントに送信されるように NSG フロー ログを構成できます。ただし、適切な特権があり、ストレージ アカウントが NSG と同じリージョンに配置されている必要があります。 また、NSG と送信先のストレージ アカウントが同じ Azure Active Directory テナントを共有する必要もあります。

## <a name="what-if-i-cant-configure-an-nsg-for-traffic-analytics-due-to-a-not-found-error"></a>"見つかりません" エラーによってトラフィック分析用に NSG を構成できないとどうなりますか?

サポートされているリージョンを選択します。 サポートされていないリージョンを選択すると、[見つかりません] エラーが表示されます。 サポートされているリージョンについては、この記事で前述しています。

## <a name="what-if-i-am-getting-the-status-failed-to-load-under-the-nsg-flow-logs-page"></a>NSG フロー ログ ページの下に "読み込みに失敗しました" という状態が表示される場合はどうすればよいですか?

フロー ログが正常に機能するためには、Microsoft.Insights プロバイダーが登録されている必要があります。 サブスクリプションで Microsoft.Insights プロバイダーが登録されているかどうかわからない場合は、次のコマンドの *xxxxx-xxxxx-xxxxxx-xxxx* を置き換え、PowerShell から次のコマンドを実行します。

```powershell-interactive
**Select-AzSubscription** -SubscriptionId xxxxx-xxxxx-xxxxxx-xxxx
**Register-AzResourceProvider** -ProviderNamespace Microsoft.Insights
```

## <a name="i-have-configured-the-solution-why-am-i-not-seeing-anything-on-the-dashboard"></a>ソリューションを構成しました。 ダッシュボードに何も表示されないのはなぜですか?

ダッシュボードを最初に表示する際は最大で 30 分かかることがあります。 ソリューションで意味がある分析情報が導出されるには、まず十分なデータを集計する必要があります。 その後にレポートが生成されます。 

## <a name="what-if-i-get-this-message-we-could-not-find-any-data-in-this-workspace-for-selected-time-interval-try-changing-the-time-interval-or-select-a-different-workspace"></a>次のメッセージが表示された場合はどうすればよいですか? "選択した時間内のデータがこのワークスペースで見つかりませんでした。 時間間隔を変更してみるか、別のワークスペースを選択してください"。

次の方法を試してください。
- 上部のバーで期間を変更します。
- 上部のバーで別の Log Analytics ワークスペースを選択します。
- 30 分後に Traffic Analytics にアクセスしてみます (最近有効にした場合)。
    
問題が解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

## <a name="what-if-i-get-this-message-analyzing-your-nsg-flow-logs-for-the-first-time-this-process-may-take-20-30-minutes-to-complete-check-back-after-some-time-2-if-the-above-step-doesnt-work-and-your-workspace-is-under-the-free-sku-then-check-your-workspace-usage-here-to-validate-over-quota-else-refer-to-faqs-for-further-information"></a>次のメッセージが表示された場合はどうすればよいですか? "Analyzing your NSG flow logs for the first time. This process may take 20-30 minutes to complete. Check back after some time. 2) If the above step doesn’t work and your workspace is under the free SKU, then check your workspace usage here to validate over quota, else refer to FAQs for further information (上記のステップが機能せず、ワークスペースが無料 SKU にある場合は、ここでワークスペースの使用状況をチェックして超過クォータを検証します。該当しない場合は、詳細について FAQ を参照してください)"。

このメッセージは、次の理由で表示される場合があります。
- Traffic Analytics は最近有効化されたため、意味のある分析情報を導出するために十分なデータをまだ集計していない可能性があります。
- 無料版の Log Analytics ワークスペースを使用しており、クォータ制限を超えています。 容量の大きいワークスペースを使用しなければならない可能性があります。
    
問題が解決しない場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。
    
## <a name="what-if-i-get-this-message-looks-like-we-have-resources-data-topology-and-no-flows-information-meanwhile-click-here-to-see-resources-data-and-refer-to-faqs-for-further-information"></a>次のメッセージが表示された場合はどうすればよいですか? "選択したワークスペースに関して存在するのはリソース データ (トポロジ) のみで、フローの情報はない可能性があります。 まず、ここをクリックしてリソース データを確認し、詳細については FAQ を参照してください。"

ダッシュボードにはリソース情報が表示されていますが、フロー関連の統計がありません。 リソース間の通信フローがないためにデータが示されない可能性があります。 60 分間待ってから、状態を再確認します。 問題が解決せず、リソース間の通信フローが存在することが確実な場合は、[User Voice フォーラム](https://feedback.azure.com/forums/217313-networking?category_id=195844)に問題を投稿してください。

## <a name="can-i-configure-traffic-analytics-using-powershell-or-an-azure-resource-manager-template-or-client"></a>PowerShell または Azure Resource Manager テンプレートまたはクライアントを使用してトラフィック分析を構成できますか?

バージョン 6.2.1 以降の Windows PowerShell を使用してトラフィック分析を構成できます。 Set コマンドレットを使用して特定の NSG のフロー ログとトラフィック分析を構成するには、[Set-AzNetworkWatcherConfigFlowLog](/powershell/module/az.network/set-aznetworkwatcherconfigflowlog) を参照してください。 特定の NSG のフロー ログとトラフィック分析状態を取得するには、[Get-AzNetworkWatcherFlowLogStatus](/powershell/module/az.network/get-aznetworkwatcherflowlogstatus) を参照してください。

現在、Azure Resource Manager テンプレートを使用してトラフィック分析を構成することはできません。

Azure Resource Manager クライアントを使用してトラフィック分析を構成するには、次の例を参照してください。

**Set コマンドレットの例:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<name of NSG>"
$TAstorageId = "/subscriptions/<storage subscription id>/resourcegroups/<storage resource group name> /providers/microsoft.storage/storageaccounts/<storage account name>"
$networkWatcherResourceGroupName = "<network watcher resource group name>"
$networkWatcherName = "<network watcher name>"

$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}',
    'properties': 
    {
        'storageId': '${TAstorageId}',
        'enabled': '<true to enable flow log or false to disable flow log>',
        'retentionPolicy' : 
        {
            days: <enter number of days like to retail flow logs in storage account>,
            enabled: <true to enable retention or false to disable retention>
        }
    },
    'flowAnalyticsConfiguration':
    {
                'networkWatcherFlowAnalyticsConfiguration':
      {
        'enabled':,<true to enable traffic analytics or false to disable traffic analytics>
        'workspaceId':'bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb',
        'workspaceRegion':'<workspace region>',
        'workspaceResourceId':'/subscriptions/<workspace subscription id>/resourcegroups/<workspace resource group name>/providers/microsoft.operationalinsights/workspaces/<workspace name>'
        
      }

    }
}
"@
$apiversion = "2016-09-01"

armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/configureFlowlog?api-version=${apiversion}" $requestBody
```
**Get コマンドレットの例:**
```
#Requestbody parameters
$TAtargetUri ="/subscriptions/<NSG subscription id>/resourceGroups/<NSG resource group name>/providers/Microsoft.Network/networkSecurityGroups/<NSG name>"


$requestBody = 
@"
{
    'targetResourceId': '${TAtargetUri}'
}
“@


armclient login
armclient post "https://management.azure.com/subscriptions/<NSG subscription id>/resourceGroups/<network watcher resource group name>/providers/Microsoft.Network/networkWatchers/<network watcher name>/queryFlowLogStatus?api-version=${apiversion}" $requestBody
```


## <a name="how-is-traffic-analytics-priced"></a>Traffic Analytics はどのように課金されますか?

Traffic Analytics は従量制です。 測定は、サービスによるフロー ログ データの処理と、その結果として生成された拡張ログの Log Analytics ワークスペースへの格納に基づいています。 

たとえば、[価格プラン](https://azure.microsoft.com/pricing/details/network-watcher/)に従い、米国中西部リージョンを考えてみます。ストレージ アカウントに格納され、Traffic Analytics によって処理されるフロー ログ データが 10 GB で、Log Analytics ワークスペースに取り込まれる拡張ログが 1 GB の場合、適用される料金は次のようになります: 10 x 2.3$ + 1 x 2.76$ = 25.76$

## <a name="how-frequently-does-traffic-analytics-process-data"></a>Traffic Analytics ではデータがどのような頻度に処理されますか?

Traffic Analytics のスキーマとデータ集計に関するドキュメントの「[データ集計](./traffic-analytics-schema.md#data-aggregation)」セクションを参照してください。

## <a name="how-does-traffic-analytics-decide-that-an-ip-is-malicious"></a>Traffic Analytics では悪意のある IP であるかどうかがどのように判断されますか? 

Traffic Analytics では、IP を悪意のあるものとみなす上で、Microsoft の内部的な脅威インテリジェンス システムに依存しています。 これらのシステムでは、Microsoft の製品とサービス、Microsoft デジタル犯罪対策ユニット (DCU)、Microsoft セキュリティ レスポンス センター (MSRC)、外部フィードなどのさまざまなテレメトリ ソースを活用し、その上に多くのインテリジェンスが構築されます。 このデータの一部は Mircosoft の内部的なものです。 既知の IP に悪意のあることを示すフラグが設定された場合、その詳細を知るにはサポート チケットを発行してください。

## <a name="how-can-i-set-alerts-on-traffic-analytics-data"></a>Traffic Analytics のデータに対してアラートを設定するにはどうすればよいですか?

Traffic Analytics には、アラートに対する組み込みのサポートがありません。 ただし、Traffic Analytics のデータは Log Analytics に格納されるので、カスタム クエリを作成し、それらに対してアラートを設定することができます。 手順:
- Traffic Analytics では Log Analytics への短縮リンクを使用することができます。 
- [こちらで説明するスキーマ](traffic-analytics-schema.md)を使用して、ご自分のクエリを記述します。 
- [新しいアラート ルール] をクリックして、アラートを作成します。
- [ログ アラートのドキュメント](../azure-monitor/alerts/alerts-log.md)を参照して、アラートを作成します。

## <a name="how-do-i-check-which-vms-are-receiving-most-on-premises-traffic"></a>受信するオンプレミス トラフィックが多い VM を確認するにはどうすればよいですか?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by vm
| render timechart
```

  IP の場合:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d 
| make-series TotalTraffic = sum(traffic) default = 0 on FlowStartTime_t from datetime(<time>) to datetime(<time>) step 1m by IP
| render timechart
```

時刻には、yyyy-mm-dd 00:00:00 の形式を使用します。

## <a name="how-do-i-check-standard-deviation-in-traffic-received-by-my-vms-from-on-premises-machines"></a>自分の VM がオンプレミスのマシンから受信したトラフィックの標準偏差を確認するにはどうすればよいですか?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand vm = pack_array(VM1_s, VM2_s) to typeof(string)
| where isnotempty(vm) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + utboundBytes_d
| summarize deviation = stdev(traffic)  by vm
```

IP の場合:

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowType_s == "S2S" 
//| where <Scoping condition>
| mvexpand IP = pack_array(SrcIP_s, DestIP_s) to typeof(string)
| where isnotempty(IP) 
| extend traffic = AllowedInFlows_d + DeniedInFlows_d + AllowedOutFlows_d + DeniedOutFlows_d // For bytes use: | extend traffic = InboundBytes_d + OutboundBytes_d
| summarize deviation = stdev(traffic)  by IP
```

## <a name="how-do-i-check-which-ports-are-reachable-or-blocked-between-ip-pairs-with-nsg-rules"></a>NSG ルールを使用して IP ペア間で到達可能な (またはブロックされた) ポートを確認するにはどうすればよいですか?

```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and TimeGenerated between (startTime .. endTime)
| extend sourceIPs = iif(isempty(SrcIP_s), split(SrcPublicIPs_s, " ") , pack_array(SrcIP_s)),
destIPs = iif(isempty(DestIP_s), split(DestPublicIPs_s," ") , pack_array(DestIP_s))
| mvexpand SourceIp = sourceIPs to typeof(string)
| mvexpand DestIp = destIPs to typeof(string)
| project SourceIp = tostring(split(SourceIp, "|")[0]), DestIp = tostring(split(DestIp, "|")[0]), NSGList_s, NSGRule_s, DestPort_d, L4Protocol_s, FlowStatus_s 
| summarize DestPorts= makeset(DestPort_d) by SourceIp, DestIp, NSGList_s, NSGRule_s, L4Protocol_s, FlowStatus_s
```

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-geo-map-view"></a>geo マップ ビューでキーボードを使用して操作するにはどうすればよいですか?

geo マップ ページには主に次の 2 つのセクションがあります。
    
- **バナー**: geo マップの上部のバナーには、トラフィック分布フィルター (たとえば、デプロイ、国/地域からのトラフィック、悪意のある、など) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがマップに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブなデータセンターが強調表示されます。
- **マップ**: バナーの下にある [マップ] セクションには、Azure のデータセンターや各国/リージョンのトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーについて geo マップ ページで選択されているのは [Azure DC] フィルターです。
- 別のフィルターに移動するには、`Tab` または `Right arrow` キーを使用します。 戻るには、`Shift+Tab` または `Left arrow` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- `Enter` または `Down` 方向キーを押して、選択したフィルターを適用します。 フィルターの選択とデプロイに基づいて、[マップ] セクションの 1 つまたは複数のノードが強調表示されます。
- バナーとマップを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-map"></a>マップでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、マップ ビューで強調されたノードの 1 つ (**Azure データセンター** または **国/リージョン**) にフォーカスが移動します。
- マップ内の他の強調表示されたノードに移動するには、前方移動に `Tab` キーまたは `Right arrow` キーを使用します。 後方移動には、`Shift+Tab` キーまたは `Left arrow` キーを使用します。
- マップ内で強調表示されたノードを選択するには、`Enter` キーまたは `Down arrow` キーを使用します。
- そのようなノードを選択すると、そのノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、 **[Information Tool Box]\(情報ツール ボックス\)** の [閉じる] ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する際は、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、 **[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスがあるときに `Tab`を押すと、フォーカスは、選択したノードと同じ大陸のエンドポイントに移動します。 `Right arrow` キーと `Left arrow` キーを使用して、これらのエンドポイント間を移動できます。
- 他のフロー エンドポイントまたは大陸クラスターに移動するには、前方移動では `Tab`、後方移動では `Shift+Tab` を使用します。
- フォーカスが **大陸クラスター** にある場合は、`Enter` または `Down` 方向キーを使用して、大陸クラスター内のエンドポイントを強調表示します。 大陸クラスターのエンドポイントと情報ボックスの [閉じる] ボタンを移動するには、前方移動では `Right arrow` キー、後方移動では `Left arrow` キーをそれぞれ使用します。 任意のエンドポイントで、`Shift+L` を使用すると、選択したノードからエンドポイントへの接続線に切り替えることができます。 もう一度 `Shift+L` を押して、選択したエンドポイントに移動できます。
        
### <a name="keyboard-navigation-at-any-stage"></a>任意の段階のキーボード ナビゲーション
    
- `Esc` を押すと、展開された選択範囲が折りたたまれます。
- `Up-arrow` キーは `Esc` と同じ動作を実行します。 `Down arrow` キーは `Enter` と同じ動作を実行します。
- `Shift+Plus` を使用して拡大、`Shift+Minus` を使用して縮小します。

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-virtual-network-topology-view"></a>仮想ネットワーク トポロジ ビューでキーボードを使用して操作するにはどうすればよいですか?

仮想ネットワーク トポロジ ページには、主に 2 つのセクションがあります。
    
- **バナー**: 仮想ネットワーク トポロジの上部バナーには、トラフィック分布フィルター (たとえば、接続された仮想ネットワーク、切断された仮想ネットワーク、パブリック IP など) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがトポロジに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブな仮想ネットワークが強調表示されます。
- **トポロジ**:バナーの下の [トポロジ] セクションには、仮想ネットワーク全体でのトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーの仮想ネットワーク トポロジ ページでの選択は、[接続された VNet] フィルターです。
- 別のフィルターに移動する場合、前方に移動するには `Tab` キーを使用します。 後方に移動するには、`Shift+Tab` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- 選択したフィルターを適用するには、`Enter` を押します。 フィルターの選択とデプロイに基づいて、[トポロジ] セクションの 1 つまたは複数のノード (仮想ネットワーク) が強調表示されます。
- バナーとトポロジーを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-topology"></a>トポロジでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、トポロジ ビューで強調されたノードの 1 つ (**VNet**) にフォーカスが移動します。
- トポロジ ビュー内の他の強調表示されたノードに移動するには、`Shift+Right arrow` キーを使用して前方に移動します。 
- 強調表示されたノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、 **[Information Tool Box]\(情報ツール ボックス\)** の **[詳細]** ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する場合、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、 **[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- このようなノードを選択する際は、`Shift+Left arrow` キーを押して、すべての接続に 1 つずつアクセスできます。 フォーカスはその接続の **[Information Tool Box]\(情報ツール ボックス\)** に移動します。 いずれの時点でも、`Shift+Right arrow` を再度押して、フォーカスをノードに戻すことができます。
    

## <a name="how-can-i-navigate-by-using-the-keyboard-in-the-subnet-topology-view"></a>サブネット トポロジ ビューでキーボードを使用して操作するにはどうすればよいですか?

仮想サブネットワーク トポロジ ページには、主に 2 つのセクションがあります。
    
- **バナー**: 仮想サブネット ネットワーク トポロジの上部のバナーには、トラフィック分布フィルター (たとえば、アクティブ、中、ゲートウェイのサブネットなど) を選択するボタンがあります。 ボタンを選択すると、それに対応するフィルターがトポロジに適用されます。 たとえば、[アクティブ] ボタンを選択すると、マップでデプロイ内のアクティブな仮想サブネットワークが強調表示されます。
- **トポロジ**:バナーの下の [トポロジ] セクションには、仮想サブネットワーク全体でのトラフィック分布が表示されます。
    
### <a name="keyboard-navigation-on-the-banner"></a>バナーでのキーボード ナビゲーション
    
- 既定では、バナーの仮想サブネットワーク トポロジ ページでの選択は、[サブネット] フィルターです。
- 別のフィルターに移動する場合、前方に移動するには `Tab` キーを使用します。 後方に移動するには、`Shift+Tab` キーを使用します。 前方の移動は、左から右、次に上から下の順になります。
- 選択したフィルターを適用するには、`Enter` を押します。 フィルターの選択とデプロイに基づいて、[トポロジ] セクションの 1 つまたは複数のノード (サブネット) が強調表示されます。
- バナーとトポロジーを切り替えるには、`Ctrl+F6` を押します。
        
### <a name="keyboard-navigation-on-the-topology"></a>トポロジでのキーボード ナビゲーション
    
- バナーでフィルターを選択してから `Ctrl+F6` を押すと、トポロジ ビューで強調されたノードの 1 つ (**サブネット**) にフォーカスが移動します。
- トポロジ ビュー内の他の強調表示されたノードに移動するには、`Shift+Right arrow` キーを使用して前方に移動します。 
- 強調表示されたノードの **[Information Tool Box]\(情報ツール ボックス\)** にフォーカスが移動します。 既定では、 **[Information Tool Box]\(情報ツール ボックス\)** の **[詳細]** ボタンにフォーカスが移動します。 **[ボックス]** ビュー内で移動する際は、前方には `Right arrow` キー、後方には `Left arrow` キーを使用します。 `Enter` を押すと、 **[Information Tool Box]\(情報ツール ボックス\)** でフォーカスがあるボタンを選択したのと同じ効果があります。
- このようなノードを選択する際は、`Shift+Left arrow` キーを押して、すべての接続に 1 つずつアクセスできます。 フォーカスはその接続の **[Information Tool Box]\(情報ツール ボックス\)** に移動します。 いずれの時点でも、`Shift+Right arrow` を再度押して、フォーカスをノードに戻すことができます。

## <a name="are-classic-nsgs-supported"></a>従来の NSG はサポートされていますか?
いいえ。Traffic Analytics は従来の NSG をサポートしていません。 従来のリソースが[非推奨](../virtual-machines/classic-vm-deprecation.md)になるため、IaaS リソースを従来のものから Azure Resource Manager に移行することをお勧めします。 [ツールの実行方法](../virtual-machines/migration-classic-resource-manager-overview.md)については、この記事を参照してください。