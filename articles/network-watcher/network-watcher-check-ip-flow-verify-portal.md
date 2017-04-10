---
title: "Azure Network Watcher の IP フロー検証を使用したトラフィックの検証 - Azure Portal | Microsoft Docs"
description: "この記事では、仮想マシンから送受信されるトラフィックが許可されているか拒否されているかを確認する方法について説明します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e0e3e9a8-70eb-409a-a744-0ce9deb27148
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1aa8fff31d8d1908b1ea93cb970487d08ea4adf6
ms.openlocfilehash: ddd6fcc8de133a5ff5f427d070c6c6ec207d9236
ms.lasthandoff: 03/31/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Azure Network Watcher の IP フロー検証コンポーネントを使用して VM から送受信されるトラフィックが許可されているか拒否されているかを確認する

> [!div class="op_single_selector"]
> - [Azure ポータル](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST API](network-watcher-check-ip-flow-verify-rest.md)

IP フロー検証は Network Watcher の機能であり、仮想マシンから送受信されるトラフィックが許可されているかどうかを確認できます。 検証は、受信または送信トラフィックに対して実行できます。 このシナリオは、仮想マシンが現在外部リソースまたは別のリソースと通信可能な状態であるかどうかを確認する際に役立ちます。 IP フロー検証を使用すると、ネットワーク セキュリティ グループ (NSG) 規則が適切に構成されているかどうかを検証し、NSG 規則によってブロックされているフローのトラブルシューティングを行うことができます。 IP フロー検証を使用するもう&1; つの理由は、ブロック対象のトラフィックが NSG によって適切にブロックされているかどうかを確認するためです。

## <a name="before-you-begin"></a>開始する前に

このシナリオは、[Network Watcher の作成](network-watcher-create.md)に関する記事の手順に従って Network Watcher を作成済みであるか、または既存の Network Watcher インスタンスがあることを前提としています。 また、有効な仮想マシンがあるリソース グループを使用することも前提としています。

## <a name="scenario"></a>シナリオ

このシナリオでは、IP フロー検証を使用して、仮想マシンがポート 443 で別のマシンと通信可能かどうかを確認します。 トラフィックが拒否された場合、そのトラフィックを拒否しているセキュリティ規則が返されます。 IP フロー検証の詳細については、[IP フロー検証の概要](network-watcher-ip-flow-verify-overview.md)に関する記事をご覧ください。

### <a name="run-ip-flow-verify"></a>IP フロー検証の実行

Network Watcher に移動し、**[IP フローの確認]** をクリックします。 トラフィックを確認する仮想マシンとネットワーク インターフェイスを選択します。 その他のフィルター情報を入力し、**[チェック]** をクリックします。

**[チェック]** をクリックすると、指定した条件に基づいてフローが確認されます。 結果は、**[アクセス許可]** または **[アクセス拒否]** のいずれかになります。 アクセスが拒否された場合、トラフィックをブロックしているネットワーク セキュリティ グループ (NSG) とセキュリティ規則が表示されます。 トラフィックの拒否が想定された動作の場合、規則が正常に適用されています。

> [!NOTE]
> IP フロー検証では、VM リソースを割り当てる必要があります。

次の図からわかるように、送信 HTTPS トラフィックが許可されています。

![IP フロー検証の概要][1]

次の図に示すように、トラフィックを受信に変更し、受信ポートを 123 に変更します。 今回はトラフィックが拒否され、トラフィックを拒否したネットワーク セキュリティ グループおよびセキュリティ規則と共に、"アクセス拒否" というメッセージが表示されます。

![IP フローの結果][2]

## <a name="next-steps"></a>次のステップ

ブロックされるべきでないトラフィックがブロックされている場合は、[ネットワーク セキュリティ グループの管理](../virtual-network/virtual-network-manage-nsg-arm-portal.md)に関する記事を参照して、ネットワーク セキュリティ グループと定義済みのセキュリティ規則を突き止めます。

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














