---
title: Azure Portal を使用した Azure DDoS Protection Standard の管理
titlesuffix: Azure Virtual Network
description: Azure Monitor で Azure DDoS Protection Standard テレメトリを使用して攻撃を軽減する方法について説明します。
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: jdial
ms.openlocfilehash: 32c4516c7dc68b04826b362d34841160936d682d
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2019
ms.locfileid: "57244521"
---
# <a name="manage-azure-ddos-protection-standard-using-the-azure-portal"></a>Azure Portal を使用した Azure DDoS Protection Standard の管理

分散型サービス拒否 (DDoS) Protection の有効と無効を切り替える方法と、テレメトリを使用して Azure DDoS Protection Standard で DDoS 攻撃を軽減する方法について説明します。 DDoS Protection Standard は、Azure [パブリック IP アドレス](virtual-network-public-ip-address.md)が割り当てられた Azure リソース (仮想マシン、ロード バランサー、アプリケーション ゲートウェイなど) を保護します。 DDoS Protection Standard とその機能の詳細については、「[Azure DDoS Protection Standard の概要](ddos-protection-overview.md)」を参照してください。

このチュートリアルの手順を始める前に、[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または「[アクセス許可](#permissions)」の一覧で示されている適切なアクセス許可を割り当てられた[カスタム ロール](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)が割り当てられたアカウントを使って、Azure portal (https://portal.azure.com) にログインします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-ddos-protection-plan"></a>DDoS Protection プランを作成する

DDoS Protection プランでは、サブスクリプションの境界を越えて、DDoS Protection Standard が有効になった仮想ネットワークのセットを定義します。 組織で 1 つの DDoS Protection プランを構成し、複数のサブスクリプションから同じプランに仮想ネットワークをリンクできます。 DDoS Protection プラン自体は、プランの作成時に選択したサブスクリプションにも関連付けられます。 プランが関連付けられているサブスクリプションでは、プランに対する月額料金が請求されるほか、保護されているパブリック IP アドレスの数が 100 を超える場合は超過料金が請求されます。 DDoS 価格について詳しくは、[価格の詳細](https://azure.microsoft.com/pricing/details/ddos-protection/)に関するページをご覧ください。

ほとんどの組織では、複数のプランを作成する必要はありません。 プランをサブスクリプション間で移行することはできません。 プランが関連付けられているサブスクリプションを変更する必要がある場合は、[既存のプランを削除](#work-with-ddos-protection-plans)し、新しいプランを作成する必要があります。

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
2. *DDoS* を検索します。 検索結果に **[DDoS protection plan]\(DDoS Protection プラン\)** が表示されたら、それを選択します。
3. **作成**を選択します。
4. 独自の値を入力または選択するか次の例の値を入力または選択し、**[作成]** を選択します。

    |Setting        |値                                              |
    |---------      |---------                                          |
    |Name           | myDdosProtectionPlan                              |
    |サブスクリプション   | サブスクリプションを選択します。                         |
    |リソース グループ | **[新規作成]** を選択し、「*myResourceGroup*」と入力します |
    |Location       | 米国東部                                           |

## <a name="enable-ddos-for-a-new-virtual-network"></a>新しい仮想ネットワークに対して DDoS を有効にする

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、**[仮想ネットワーク]** を選択します。
3. 独自の値を入力または選択するか次の例の値を入力または選択し、残りの既定値はそのまま受け入れて、**[作成]** を選択します。

    | Setting         | 値                                                        |
    | ---------       | ---------                                                    |
    | Name            | myVirtualNetwork                                             |
    | サブスクリプション    | サブスクリプションを選択します。                                    |
    | リソース グループ  | **[既存のものを使用]**、**[myResourceGroup]** の順に選択します |
    | Location        | 米国東部                                                      |
    | DDos Protection | **[DDos Protection]** で **[Standard]** を選択し、**[myDdosProtectionPlan]** を選択します。 選択したプランは仮想ネットワークと同じサブスクリプションまたは異なるサブスクリプションのどちらにあっても構いませんが、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。|

仮想ネットワークに対して DDoS Standard が有効になっている場合、仮想ネットワークを別のリソース グループまたはサブスクリプションに移動することはできません。 DDoS Standard が有効になっている仮想ネットワークを移動する必要がある場合は、まず DDoS Standard を無効にし、仮想ネットワークを移動してから、DDoS Standard を有効にします。 移動後に、仮想ネットワーク内のすべての保護されたパブリック IP アドレスの自動調整されたポリシーしきい値がリセットされます。

## <a name="enable-ddos-for-an-existing-virtual-network"></a>既存の仮想ネットワークに対して DDoS を有効にする

1. 既存の DDoS Protection プランがない場合は、「[DDoS Protection プランを作成する](#create-a-ddos-protection-plan)」の手順を実行して DDoS Protection プランを作成します。
2. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
3. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\) ボックス**に、DDoS Protection Standard を有効にする仮想ネットワークの名前を入力します。 仮想ネットワークの名前が検索結果に表示されたら、それを選びます。
4. **[設定]** で、**[DDoS Protection]** を選択します。
5. **[Standard]** を選択します。 **[DDoS protection plan]\(DDoS Protection プラン\)** で、既存の DDoS Protection プランまたは手順 1. で作成したプランを選び、**[保存]** を選択します。 選択したプランは仮想ネットワークと同じサブスクリプションまたは異なるサブスクリプションのどちらにあっても構いませんが、両方のサブスクリプションが同じ Azure Active Directory テナントに関連付けられている必要があります。

## <a name="disable-ddos-for-a-virtual-network"></a>新しい仮想ネットワークに対して DDoS を無効にする

1. ポータルの上部にある **[Search resources, services, and docs]\(リソース、サービス、ドキュメントの検索\) ボックス**に、DDoS Protection Standard を無効にする仮想ネットワークの名前を入力します。 仮想ネットワークの名前が検索結果に表示されたら、それを選びます。
2. **[設定]** で、**[DDoS Protection]** を選択します。
3. **[DDoS protection plan]\(DDoS Protection プラン\)** で **[Basic]** を選択し、**[保存]** を選択します。

## <a name="work-with-ddos-protection-plans"></a>DDoS Protection プランを処理する

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*DDoS*」と入力します。 結果に **[DDoS protection plans]\(DDoS Protection プラン\)** が表示されたら、それを選択します。
3. 一覧から、表示する Protection プランを選択します。
4. プランに関連付けられているすべての仮想ネットワークが一覧表示されます。
5. プランを削除する場合は、最初にそのプランからすべての仮想ネットワークの関連付けを解除する必要があります。 仮想ネットワークからプランの関連付けを解除するには、「[新しい仮想ネットワークに対して DDoS を無効にする](#disable-ddos-for-a-virtual-network)」をご覧ください。

## <a name="configure-alerts-for-ddos-protection-metrics"></a>DDoS Protection メトリックのアラートを構成する

Azure Monitor のアラート構成を使用すると、利用可能な DDoS Protection メトリックをどれでも選び、攻撃中に有効な軽減策がある場合にそのメトリックのアラートを受けることができます。 条件が満たされると、指定したアドレスにアラート メールが届きます。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[共有サービス]** で **[メトリック]** を選択します。
4. 独自の値を入力または選択するか次の例の値を入力し、残りの既定値はそのまま受け入れて、**[OK]** を選択します。

    |Setting                  |値                                                                                               |
    |---------                |---------                                                                                           |
    |Name                     | myDdosAlert                                                                                        |
    |サブスクリプション             | アラートを受信するパブリック IP アドレスを含むサブスクリプションを選択します。        |
    |リソース グループ           | アラートを受信するパブリック IP アドレスを含むリソース グループを選択します。      |
    |リソース                 | アラートを受信するパブリック IP アドレスを含むパブリック IP アドレスを選択します。 DDoS は、仮想ネットワーク内のリソースに割り当てられているパブリック IP アドレスを監視します。 仮想ネットワーク内のパブリック IP アドレスを持つリソースがない場合は、最初にパブリック IP アドレスを使用してリソースを作成する必要があります。 Azure App Service Environment と Azure VPN Gateway を除き、[Azure サービスの仮想ネットワーク](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関するページに一覧表示されている Resource Manager (クラシックではない) を通じてデプロイされているすべてのリソースのパブリック IP アドレスを監視できます。 このチュートリアルを続行するために、[Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンをすばやく作成できます。                   |
    |メトリック                   | DDoS 攻撃中かどうか                                                                            |
    |Threshold                | 1 - **1** は、攻撃を受けていることを示します。 **0** は、攻撃を受けていないことを示します。                         |
    |期間                   | 選んだ任意の値を選択します。                                                                   |
    |電子メールによる通知         | チェック ボックスをオンにします                                                                                  |
    |Additional administrator (追加の管理者) | 電子メールの所有者、共同作成者、またはサブスクリプションの閲覧者でない場合は自分のメール アドレスを入力します。 |

    攻撃の検出から数分以内に、次の図のような電子メールが Azure Monitor メトリックから届きます。

    ![攻撃アラート](./media/manage-ddos-protection/ddos-alert.png)


DDoS 攻撃をシミュレートしてアラートを検証するには、「[DDoS 検出を検証する](#validate-ddos-detection)」をご覧ください。

また、[webhook の構成](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json)や、アラートを作成するための[ロジック アプリ](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)の詳細についても学習できます。

## <a name="use-ddos-protection-telemetry"></a>DDoS Protection テレメトリを使用する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 このテレメトリは、パブリック IP アドレスが軽減策のもとにある期間中のみ使用できます。 攻撃が軽減される前または軽減された後、テレメトリは表示されません。

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[共有サービス]** で **[メトリック]** を選択します。
4. テレメトリが必要なパブリック IP アドレスを含む**サブスクリプション**と**リソース グループ**を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、テレメトリが必要な特定のパブリック IP アドレスを選択します。
6. 一連の **[利用可能なメトリック]** が画面の左側に表示されます。 これらのメトリックは、選択すると、概要画面の **Azure Monitor のメトリック グラフ**に表示されます。

メトリック名は、異なるパケットの種類と、バイト数かパケット数かを表します。各メトリックでのタグ名の基本的な構成は、次のようになっています。

- **Dropped (削除) タグ名** (例: **Inbound Packets Dropped DDoS** (DDoS 受信パケット削除数)):DDoS 保護システムによって削除/除去されたパケットの数。
- **Forwarded (転送) タグ名** (例: **Inbound Packets Forwarded DDoS** (DDoS 受信パケット転送数)):DDoS システムによって宛先 VIP に転送されたパケットの数 (フィルター処理されなかったトラフィック)。
- **タグ名なし** (例: **Inbound Packets DDoS** (DDoS 受信パケット数)):除去システムに入力されたパケットの総数 (削除および転送されたパケットの総数を表します)。

DDoS 攻撃をシミュレートしてテレメトリを検証するには、「[DDoS 検出を検証する](#validate-ddos-detection)」をご覧ください。

## <a name="view-ddos-mitigation-policies"></a>DDoS 軽減ポリシーを表示する

DDoS Protection Standard は、DDoS が有効になっている仮想ネットワーク内で、保護されたリソースのパブリック IP アドレスごとに、3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP) を適用します。 次の図に示すように、**[Inbound TCP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 TCP パケット数\)** と **[Inbound UDP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 UDP パケット数\)** のメトリックを選択してポリシーのしきい値を表示することができます。

![軽減ポリシーを表示する](./media/manage-ddos-protection/view-mitigation-policies.png)

ポリシーのしきい値は、Azure Machine Learning ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して DDoS 軽減が行われます。

## <a name="configure-ddos-attack-analytics"></a>DDoS 攻撃の分析を構成する
Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 

## <a name="configure-ddos-attack-mitigation-reports"></a>DDoS 攻撃の軽減策レポートを構成する
攻撃の軽減策レポートでは、リソースへの攻撃に関する詳細情報を提供するために集計される、Netflow プロトコル データが使用されます。 パブリック IP リソースが攻撃を受けるたびに、軽減策が開始されるとすぐにレポートの生成が開始されます。 5 分ごとに増分レポートが生成され、軽減策の期間全体に対して軽減策後のレポートが生成されます。 これにより、DDoS 攻撃が長時間継続する場合に、確実に 5 分ごとに軽減策レポートの最新のスナップショットを表示し、攻撃の軽減策が完了したら完全な概要を表示することができます。 

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[設定]** で **[診断設定]** を選択します。
4. ログに記録するパブリック IP アドレスを含む**サブスクリプション**と**リソース グループ**を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、メトリックをログに記録する特定のパブリック IP アドレスを選択します。
6. **[Turn on diagnostics to collect the DDoSMitigationReports log]\(診断を有効にして DDoSMitigationReports ログを収集する\)** を選択し、必要に応じて次のオプションを任意の数だけ選択します。

    - **ストレージ アカウントへのアーカイブ**:データは Azure Storage アカウントに書き込まれます。 このオプションについて詳しくは、[診断ログのアーカイブ](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
    - **イベント ハブへのストリーム配信**:ログの受信者が Azure Event Hub を使用してログを取得できるようにします。 イベント ハブにより、Splunk やその他の SIEM システムとの統合が可能になります。 このオプションについて詳しくは、[イベント ハブへの診断ログのストリーミング](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
    - **[Log Analytics への送信]**:Azure Monitor サービスにログを書き込みます。 このオプションについて詳しくは、[Azure Monitor ログで使用するログの収集](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。

増分レポートと攻撃の軽減策後のレポートには、両方とも次のフィールドが含まれます。
- 攻撃ベクトル
- トラフィック統計情報
- パケットが削除された理由
- 関与するプロトコル
- 上位 10 の攻撃元の国またはリージョン
- 上位 10 の攻撃元の ASN

## <a name="configure-ddos-attack-mitigation-flow-logs"></a>DDoS 攻撃の軽減フロー ログを構成する
攻撃の軽減フロー ログを使用すると、アクティブな DDoS 攻撃中に、削除されたトラフィック、転送されたトラフィック、およびその他の有用なデータ ポイントをほぼリアルタイムで確認できます。 この一定のデータ ストリームをほぼリアルタイムに監視するために、イベント ハブを使用して SIEM システム内に取り込み、可能なアクションを実行して、防御操作の必要性に対処することができます。 

1. ポータルの左上にある **[すべてのサービス]** を選択します。
2. **[フィルター]** ボックスに「*Monitor*」と入力します。 結果に **[Monitor]** が表示されたら、それを選択します。
3. **[設定]** で **[診断設定]** を選択します。
4. ログに記録するパブリック IP アドレスを含む**サブスクリプション**と**リソース グループ**を選択します。
5. **[リソースの種類]** に **[パブリック IP アドレス]** を選択し、メトリックをログに記録する特定のパブリック IP アドレスを選択します。
6. **[Turn on diagnostics to collect the DDoSMitigationFlowLogs log]\(診断を有効にして DDoSMitigationFlowLogs ログを収集する\)** を選択し、必要に応じて次のオプションを任意の数だけ選択します。

    - **ストレージ アカウントへのアーカイブ**:データは Azure Storage アカウントに書き込まれます。 このオプションについて詳しくは、[診断ログのアーカイブ](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
    - **イベント ハブへのストリーム配信**:ログの受信者が Azure Event Hub を使用してログを取得できるようにします。 イベント ハブにより、Splunk やその他の SIEM システムとの統合が可能になります。 このオプションについて詳しくは、[イベント ハブへの診断ログのストリーミング](../azure-monitor/platform/diagnostic-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
    - **[Log Analytics への送信]**:Azure Monitor サービスにログを書き込みます。 このオプションについて詳しくは、[Azure Monitor ログで使用するログの収集](../azure-monitor/platform/collect-azure-metrics-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関する記事をご覧ください。
1. Azure analytics ダッシュボードでフロー ログ データを表示するには、 https://github.com/Anupamvi/Azure-DDoS-Protection/raw/master/flowlogsbyip.zip からサンプル ダッシュボードをインポートします。

フロー ログには次のフィールドがあります。 
- 発信元 IP
- 宛先 IP
- 発信元ポート 
- 宛先ポート 
- プロトコルの種類 
- 軽減時に実行されたアクション



## <a name="validate-ddos-detection"></a>DDoS 検出を検証する

マイクロソフトは [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) と提携して、シミュレーションのために DDoS Protection を有効にしたパブリック IP アドレスに対してトラフィックを生成できるインターフェイスを構築しました。 BreakPoint Cloud シミュレーションを使用して、以下の操作を行うことができます。

- Microsoft Azure DDoS Protection が Azure リソースを DDoS 攻撃から保護する方法を検証します
- DDoS 攻撃を受けているときのインシデント対応プロセスを最適化します
- DDoS コンプライアンスを文書化します
- ネットワーク セキュリティ チームのトレーニングを行います

## <a name="permissions"></a>アクセス許可

DDoS 保護プランに関する作業を行うには、使用するアカウントが[ネットワークの共同作業者](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor)ロール、または次の表の適切なアクションが割り当てられた[カスタム](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ロールに、割り当てられている必要があります。

| Action                                            | Name                                     |
| ---------                                         | -------------                            |
| Microsoft.Network/ddosProtectionPlans/read        | DDoS 保護プランを読み取る              |
| Microsoft.Network/ddosProtectionPlans/write       | DDoS 保護プランを作成または更新する  |
| Microsoft.Network/ddosProtectionPlans/delete      | DDoS 保護プランを削除する            |
| Microsoft.Network/ddosProtectionPlans/join/action | DDoS 保護プランを結合する              |

仮想ネットワークに対する DDoS 保護を有効にするには、使うアカウントに[仮想ネットワークの適切なアクション](manage-virtual-network.md#permissions)も割り当てられている必要があります。

## <a name="next-steps"></a>次の手順

- [Azure ポリシー](policy-samples.md)を作成して仮想ネットワークに適用します