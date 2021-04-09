---
title: Azure DDoS Protection Standard 用の DDoS 保護テレメトリの表示と構成
description: Azure DDoS Protection Standard 用の DDoS 保護テレメトリを表示および構成する方法を学習します。
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
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716195"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS 保護テレメトリの表示と構成

Azure DDoS Protection Standard では、DDoS 攻撃の分析情報により、攻撃の詳細な情報および視覚化が提供されます。 DDoS 攻撃から仮想ネットワークを保護している場合、攻撃の軽減策レポートと軽減策フロー ログによって、攻撃のトラフィック、および攻撃を緩和するために行われたアクションの詳細を視覚的に確認できます。 豊富なテレメトリは、DDoS 攻撃の間に、詳細なメトリックを含む Azure Monitor を通じて公開されます。 DDoS Protection によって公開される Azure Monitor の任意のメトリックに対して、アラートを構成することができます。 Azure Monitor 診断インターフェイスを介した高度な分析用に、ログを [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md)、Splunk (Azure Event Hubs)、OMS Log Analytics、Azure Storage とさらに統合できます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * DDoS 保護テレメトリを表示する
> * DDoS 軽減ポリシーを表示する
> * DDoS 保護テレメトリを検証し、テストする

### <a name="metrics"></a>メトリック

> [!NOTE]
> Azure portal 上では **[集計]** に複数のオプションが表示されますが、下の表に示す集計の種類のみが各メトリックに対してサポートされます。 紛らわしくて申し訳ありませんが、現在解決に向けて取り組み中です。

Azure DDoS Protection Standard では、次の[メトリック](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses)を利用できます。 これらのメトリックを、診断設定を使用してエクスポートすることもできます (「[DDoS 診断ログの表示と構成](diagnostic-logging.md)」を参照してください)。


| メトリック | メトリックの表示名 | ユニット | 集計の種類 | 説明 |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | DDoS 受信バイト破棄数 | BytesPerSecond | 最大値 | DDoS 受信バイト破棄数| 
| BytesForwardedDDoS | DDoS 受信バイト転送数 | BytesPerSecond | 最大値 | DDoS 受信バイト転送数 |
| BytesInDDoS | DDoS 受信バイト数 | BytesPerSecond | 最大値 | DDoS 受信バイト数 |
| DDoSTriggerSYNPackets | DDoS 軽減をトリガーする受信 SYN パケット数 | CountPerSecond | 最大値 | DDoS 軽減をトリガーする受信 SYN パケット数 |
| DDoSTriggerTCPPackets | DDoS 軽減をトリガーする受信 TCP パケット数 | CountPerSecond | 最大値 | DDoS 軽減をトリガーする受信 TCP パケット数 |
| DDoSTriggerUDPPackets | DDoS 軽減をトリガーする受信 UDP パケット数 | CountPerSecond | 最大値 | DDoS 軽減をトリガーする受信 UDP パケット数 |
| IfUnderDDoSAttack | DDoS 攻撃中かどうか | Count | 最大値 | DDoS 攻撃中かどうか |
| PacketsDroppedDDoS | DDoS 受信パケット破棄数 | CountPerSecond | 最大値 | DDoS 受信パケット破棄数 |
| PacketsForwardedDDoS | DDoS 受信パケット転送数 | CountPerSecond | 最大値 | DDoS 受信パケット転送数 |
| PacketsInDDoS | DDoS 受信パケット数 | CountPerSecond | 最大値 | DDoS 受信パケット数 |
| TCPBytesDroppedDDoS | DDoS 受信 TCP バイト破棄数 | BytesPerSecond | 最大値 | DDoS 受信 TCP バイト破棄数 |
| TCPBytesForwardedDDoS | DDoS 受信 TCP バイト転送数 | BytesPerSecond | 最大値 | DDoS 受信 TCP バイト転送数 |
| TCPBytesInDDoS | DDoS 受信 TCP バイト数 | BytesPerSecond | 最大値 | DDoS 受信 TCP バイト数 |
| TCPPacketsDroppedDDoS | DDoS 受信 TCP パケット破棄数 | CountPerSecond | 最大値 | DDoS 受信 TCP パケット破棄数 |
| TCPPacketsForwardedDDoS | DDoS 受信 TCP パケット転送数 | CountPerSecond | 最大値 | DDoS 受信 TCP パケット転送数 |
| TCPPacketsInDDoS | DDoS 受信 TCP パケット数 | CountPerSecond | 最大値 | DDoS 受信 TCP パケット数 |
| UDPBytesDroppedDDoS | DDoS 受信 UDP バイト破棄数 | BytesPerSecond | 最大値 | DDoS 受信 UDP バイト破棄数 |
| UDPBytesForwardedDDoS | DDoS 受信 UDP バイト転送数 | BytesPerSecond | 最大値 | DDoS 受信 UDP バイト転送数 |
| UDPBytesInDDoS | DDoS 受信 UDP バイト数 | BytesPerSecond | 最大値 | DDoS 受信 UDP バイト数 |
| UDPPacketsDroppedDDoS | DDoS 受信 UDP パケット破棄数 | CountPerSecond | 最大値 | DDoS 受信 UDP パケット破棄数 |
| UDPPacketsForwardedDDoS | Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数) | CountPerSecond | 最大値 | Inbound UDP packets forwarded DDoS (DDoS 受信 UDP パケット転送数) |
| UDPPacketsInDDoS | DDoS 受信 UDP パケット数 | CountPerSecond | 最大値 | DDoS 受信 UDP パケット数 |

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。
- このチュートリアルの手順を実行する前に、まず [Azure DDoS Standard 保護プラン](manage-ddos-protection.md)を作成し、仮想ネットワーク上で DDoS Protection Standard を有効にしておく必要があります。
- DDoS は、仮想ネットワーク内のリソースに割り当てられているパブリック IP アドレスを監視します。 仮想ネットワーク内のパブリック IP アドレスを持つリソースがない場合は、最初にパブリック IP アドレスを使用してリソースを作成する必要があります。 Azure App Service Environment を除き、[Azure サービスの仮想ネットワーク](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)に関する記事に一覧表示されている Resource Manager (クラシックではない) を通じてデプロイされている、すべてのリソースのパブリック IP アドレスを監視できます (バックエンド仮想マシンが仮想ネットワーク内にある Azure Load Balancer を含む)。 このチュートリアルを続行するために、[Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) または [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 仮想マシンをすばやく作成できます。  

## <a name="view-ddos-protection-telemetry"></a>DDoS 保護テレメトリを表示する

攻撃のテレメトリは、Azure Monitor 経由でリアルタイムに提供されます。 テレメトリは、パブリック IP アドレスに軽減策が適用されている場合にのみ使用できます。 

1. [Azure portal](https://portal.azure.com/) にサインインし、DDoS Protection プランに移動します。
2. **[監視]** で **[メトリック]** を選びます。
3. **[スコープ]** を選択します。 ログするパブリック IP アドレスを含む **サブスクリプション** を選択し、 **[リソースの種類]** で **[パブリック IP アドレス]** を選択します。メトリックをログする特定のパブリック IP アドレスを選択し、 **[適用]** を選択します。
4. **集計** の種類として **[最大]** を選択します。

メトリック名は、異なるパケットの種類と、バイト数かパケット数かを表します。各メトリックでのタグ名の基本的な構成は、次のようになっています。

- **Dropped (削除) タグ名** (例: **Inbound Packets Dropped DDoS** (DDoS 受信パケット削除数)):DDoS 保護システムによって削除/除去されたパケットの数。
- **Forwarded (転送) タグ名** (例: **Inbound Packets Forwarded DDoS** (DDoS 受信パケット転送数)):DDoS システムによって宛先 VIP に転送されたパケットの数 (フィルター処理されなかったトラフィック)。
- **タグ名なし** (例: **Inbound Packets DDoS** (DDoS 受信パケット数)):除去システムに入力されたパケットの総数 (削除および転送されたパケットの総数を表します)。

## <a name="view-ddos-mitigation-policies"></a>DDoS 軽減ポリシーを表示する

DDoS Protection Standard は、DDoS が有効になっている仮想ネットワーク内で、保護されたリソースのパブリック IP アドレスごとに、3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP) を適用します。 次の図に示すように、 **[Inbound TCP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 TCP パケット数\)** と **[Inbound UDP packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする受信 UDP パケット数\)** のメトリックを選択し、**集計** の種類を [最大] にして、ポリシーのしきい値を表示することができます。

![軽減ポリシーを表示する](./media/manage-ddos-protection/view-mitigation-policies.png)

ポリシーのしきい値は、Azure Machine Learning ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して DDoS 軽減が行われます。

## <a name="validate-and-test"></a>検証とテスト

DDoS 攻撃をシミュレートして DDoS 保護テレメトリを検証す方法については、[DDoS 検出の検証に関する記事](test-through-simulations.md)を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

- DDoS Protection メトリックのアラートを構成する
- DDoS 保護テレメトリを表示する
- DDoS 軽減ポリシーを表示する
- DDoS 保護テレメトリを検証し、テストする

攻撃の軽減策レポートとフロー ログの構成方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [DDoS 診断ログの表示と構成](diagnostic-logging.md)