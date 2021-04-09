---
title: Azure DDoS Protection Standard のレポートとフロー ログ
description: レポートとフロー ログを構成する方法について説明します。
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: cc5b3b85d6d13fda532da0993fa7f733126b8eae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591871"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>DDoS 診断ログの表示と構成

Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 豊富なテレメトリは、DDoS 攻撃の間に、詳細なメトリックを含む Azure Monitor を通じて公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェイスを介した高度な分析用に、ログを [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure Event Hubs)、OMS Log Analytics、Azure Storage とさらに統合できます。

Azure DDoS Protection Standard には、次の診断ログを使用できます。 

- **DDoSProtectionNotifications**:パブリック IP リソースが攻撃を受けているとき、および攻撃の軽減が終了したときに、通知を受け取ります。
- **DDoSMitigationFlowLogs**:攻撃の軽減フロー ログを使用すると、アクティブな DDoS 攻撃中に、削除されたトラフィック、転送されたトラフィック、およびその他の有用なデータ ポイントをほぼリアルタイムで確認できます。 この一定のデータ ストリームをほぼリアルタイムに監視するために、イベント ハブを使用して Azure Sentinel またはサードパーティの SIEM システム内に取り込み、可能なアクションを実行して、防御操作の必要性に対処することができます。
- **DDoSMitigationReports**:攻撃の軽減策レポートでは、リソースへの攻撃に関する詳細情報を提供するために集計される、Netflow プロトコル データが使用されます。 パブリック IP リソースが攻撃を受けるたびに、軽減策が開始されるとすぐにレポートの生成が開始されます。 5 分ごとに増分レポートが生成され、軽減策の期間全体に対して軽減策後のレポートが生成されます。 これにより、DDoS 攻撃が長時間継続する場合に、確実に 5 分ごとに軽減策レポートの最新のスナップショットを表示し、攻撃の軽減策が完了したら完全な概要を表示することができます。 
- **AllMetrics**:DDoS 攻撃が行われている間に使用できる可能性があるすべてのメトリックが表示されます。 

このチュートリアルでは、次の方法について説明します。

> [!div class="checklist"]
> * 通知、軽減レポート、軽減フロー ログなどの DDoS 診断ログを構成します。 
> * 定義されたスコープ内のすべてのパブリック IP で診断ログを有効にします。
> * ブック内のログ データを表示します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このチュートリアルの手順を実行する前に、まず [Azure DDoS Standard 保護プラン](manage-ddos-protection.md)を作成し、仮想ネットワーク上で DDoS Protection Standard を有効にしておく必要があります。
- DDoS は、仮想ネットワーク内のリソースに割り当てられているパブリック IP アドレスを監視します。 仮想ネットワーク内のパブリック IP アドレスを持つリソースがない場合は、最初にパブリック IP アドレスを使用してリソースを作成する必要があります。 Azure App Service Environment を除き、[Azure サービスの仮想ネットワーク](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関する記事に一覧表示されている Resource Manager (クラシックではない) を通じてデプロイされている、すべてのリソースのパブリック IP アドレスを監視できます (バックエンド仮想マシンが仮想ネットワーク内にある Azure Load Balancer を含む)。 このチュートリアルを続行するために、[Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンをすばやく作成できます。    

## <a name="configure-ddos-diagnostic-logs"></a>DDoS 診断ログを構成する

環境内のすべてのパブリック IP で診断ログを自動的に有効にする場合は、スキップして「[すべてのパブリック IP で診断ログを有効にする](#enable-diagnostic-logging-on-all-public-ips)」に進みます。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[設定]** で **[診断設定]** を選択します。
4. ログに記録するパブリック IP アドレスを含む **サブスクリプション** と **リソース グループ** を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、ログを有効にする特定のパブリック IP アドレスを選択します。
6. **[診断設定の追加]** を選択します。 **[カテゴリの詳細]** で、次のオプションを必要な数だけ選択し、 **[保存]** を選択します。

    ![DDoS の診断設定](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. **[宛先の詳細]** で、次のオプションを必要な数だけ選択します。

    - **[ストレージ アカウントへのアーカイブ]** :データは Azure Storage アカウントに書き込まれます。 このオプションについて詳しくは、[リソース ログのアーカイブ](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage)に関する記事をご覧ください。
    - **[イベント ハブへのストリーム]** :ログの受信者が Azure Event Hub を使用してログを取得できるようにします。 イベント ハブにより、Splunk やその他の SIEM システムとの統合が可能になります。 このオプションについて詳しくは、[イベント ハブへのリソース ログのストリーミング](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs)に関する記事をご覧ください。
    - **[Log Analytics への送信]** :Azure Monitor サービスにログを書き込みます。 このオプションについて詳しくは、[Azure Monitor ログで使用するログの収集](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace)に関する記事をご覧ください。

### <a name="log-schemas"></a>ログ スキーマ

次の表にフィールド名と説明を示します。

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| フィールド名 | 説明 |
| --- | --- |
| **TimeGenerated** | 通知が作成された日時 (UTC)。 |
| **ResourceId** | パブリック IP アドレスのリソース ID。 |
| **カテゴリ** | 通知の場合、これは `DDoSProtectionNotifications` になります。|
| **ResourceGroup** | パブリック IP アドレスと仮想ネットワークを含むリソース グループ。 |
| **SubscriptionId** | DDoS 保護プランのサブスクリプション ID。 |
| **リソース** | パブリック IP アドレスの名前。 |
| **ResourceType** | これは常に `PUBLICIPADDRESS` になります。 |
| **OperationName** | 通知の場合、これは `DDoSProtectionNotifications` になります。  |
| **メッセージ** | 攻撃の詳細。 |
| **Type** | 通知の種類。 指定できる値は `MitigationStarted` です。 `MitigationStopped`. |
| **PublicIpAddress** | お客様のパブリック IP アドレス。 |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| フィールド名 | 説明 |
| --- | --- |
| **TimeGenerated** | フロー ログが作成された日時 (UTC)。 |
| **ResourceId** | パブリック IP アドレスのリソース ID。 |
| **カテゴリ** | フロー ログの場合、これは `DDoSMitigationFlowLogs` になります。|
| **ResourceGroup** | パブリック IP アドレスと仮想ネットワークを含むリソース グループ。 |
| **SubscriptionId** | DDoS 保護プランのサブスクリプション ID。 |
| **リソース** | パブリック IP アドレスの名前。 |
| **ResourceType** | これは常に `PUBLICIPADDRESS` になります。 |
| **OperationName** | フロー ログの場合、これは `DDoSMitigationFlowLogs` になります。 |
| **メッセージ** | 攻撃の詳細。 |
| **SourcePublicIpAddress** | パブリック IP アドレスへのトラフィックを生成しているクライアントのパブリック IP アドレス。 |
| **SourcePort** | 0 から 65535 の範囲のポート番号。 |
| **DestPublicIpAddress** | お客様のパブリック IP アドレス。 |
| **DestPort** | 0 から 65535 の範囲のポート番号。 |
| **プロトコル** | プロトコルの種類。 指定できる値は `tcp`、`udp`、`other` です。|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| フィールド名 | 説明 |
| --- | --- |
| **TimeGenerated** | レポートが作成された日時 (UTC)。 |
| **ResourceId** | パブリック IP アドレスのリソース ID。 |
| **カテゴリ** | 通知の場合、これは `DDoSProtectionNotifications` になります。|
| **ResourceGroup** | パブリック IP アドレスと仮想ネットワークを含むリソース グループ。 |
| **SubscriptionId** | DDoS 保護プランのサブスクリプション ID。 |
| **リソース** | パブリック IP アドレスの名前。 |
| **ResourceType** | これは常に `PUBLICIPADDRESS` になります。 |
| **OperationName** | 軽減レポートの場合、これは `DDoSMitigationReports` になります。 |
| **ReportType** | 指定できる値は `Incremental`、`PostMitigation` です。|
| **MitigationPeriodStart** | 軽減策が開始された日時 (UTC)。  |
| **MitigationPeriodEnd** | 軽減策が終了した日時 (UTC)。 |
| **IPAddress** | お客様のパブリック IP アドレス。 |
| **AttackVectors** |  攻撃の種類の内訳。 キーは `TCP SYN flood`、`TCP flood`、`UDP flood`、`UDP reflection`、`Other packet flood` です。|
| **TrafficOverview** |  攻撃トラフィックの内訳。 キーは `Total packets`、`Total packets dropped`、`Total TCP packets`、`Total TCP packets dropped`、`Total UDP packets`、`Total UDP packets dropped`、`Total Other packets`、`Total Other packets dropped` です。 |
| **プロトコル** | 関連するプロトコルの内訳。 キーは `TCP`、`UDP`、`Other` です。 |
| **DropReasons** | ドロップされたパケットの理由の内訳。 キーは `Protocol violation invalid TCP syn`、`Protocol violation invalid TCP`、`Protocol violation invalid UDP`、`UDP reflection`、`TCP rate limit exceeded`、`UDP rate limit exceeded`、`Destination limit exceeded`、`Other packet flood`、`Rate limit exceeded`、`Packet was forwarded to service` です。 |
| **TopSourceCountries** | 受信トラフィックの上位 10 個のソース国の内訳。 |
| **TopSourceCountriesForDroppedPackets** | 軽減された攻撃トラフィックの上位 10 個のソース国の内訳。 |
| **TopSourceASNs** | 受信トラフィックの上位 10 個のソース自律システム番号 (ASN) の内訳。  |
| **SourceContinents** | 受信トラフィックのソース大陸の内訳。 |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>すべてのパブリック IP で診断ログを有効にする

この[テンプレート](https://aka.ms/ddosdiaglogs)を使用すると、定義されたスコープ内のすべてのパブリック IP ログに対して診断ログを自動的に有効にする Azure Policy 定義を作成できます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FEnable%2520Diagnostic%2520Logging%2FAzure%2520Policy%2FDDoSLogs.json)

## <a name="view-log-data-in-workbooks"></a>ブック内のログ データを表示する

### <a name="azure-sentinel-data-connector"></a>Azure Sentinel のデータ コネクタ

ログを Azure Sentinel に接続し、ブック内のデータを表示および分析して、カスタムアラートを作成し、それを調査プロセスに組み込むことができます。 Azure Sentinel に接続するには、[Azure Sentinel への接続に関するページ](../sentinel/connect-azure-ddos-protection.md)を参照してください。 

![Azure Sentinel の DDoS コネクタ](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Azure DDoS Protection のブック

[この Azure Resource Manager (ARM) テンプレート](https://aka.ms/ddosworkbook)を使用して、攻撃分析ブックをデプロイできます。 このブックを使用すると、複数のフィルター可能なパネルにわたって攻撃データを視覚化して、危機にさらされているものを簡単に把握できます。 

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520DDoS%2520Protection%2520Workbook%2FAzureDDoSWorkbook_ARM.json)

![DDoS Protection のブック](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>検証とテスト

DDoS 攻撃をシミュレートしてログを検証するには、「[DDoS 検出を検証する](test-through-simulations.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

- 通知、軽減レポート、軽減フロー ログなどの DDoS 診断ログを構成します。 
- 定義されたスコープ内のすべてのパブリック IP で診断ログを有効にします。
- ブック内のログ データを表示します。

攻撃アラートを構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 保護アラートの表示と構成](alerts.md)
