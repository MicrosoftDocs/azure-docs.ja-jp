---
title: "Azure Portal を使用した VM へのポートの開放 | Microsoft Docs"
description: "Azure Portal で Resource Manager デプロイメント モデルを使用して、Windows VM へのポートを開き、エンドポイントを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/21/2017
ms.author: iainfou
ms.openlocfilehash: 33bc0be0aeae6d0276fd8999b9ac0a010e3067ba
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Azure Portal を使用して仮想マシンへのポートを開く方法
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>クイック コマンド
[これらの手順は、Azure PowerShell を使用して実行](nsg-quickstart-powershell.md)することもできます。

最初に、ネットワーク セキュリティ グループを作成します。 ポータルでリソース グループを選択し、**[追加]** を選択して、**[ネットワーク セキュリティ グループ]** を探して選択します。

![ネットワーク セキュリティ グループの追加](./media/nsg-quickstart-portal/add-nsg.png)

ネットワーク セキュリティ グループの名前を入力し、リソース グループを選択または作成してから場所を選択します。 入力し終えたら **[作成]** を選択します。

![ネットワーク セキュリティ グループの作成](./media/nsg-quickstart-portal/create-nsg.png)

新しいネットワーク セキュリティ グループを選択します。 [受信セキュリティ規則] を選択し、**[追加]** を選択して規則を作成します。

![受信規則の追加](./media/nsg-quickstart-portal/add-inbound-rule.png)

**[サービス]** ボックスの一覧で共通のサービスを選択します (たとえば、*[HTTP]*)。 *[カスタム]* を選択して、使用する特定のポートを指定することもできます。 必要な場合は、優先度または名前を変更します。 優先度は、ルールが適用される順序に影響します。数値が小さいほど、ルールが早く適用されます。 この画面の上部にある **[Advanced]\(詳細設定\)** を選択すると、特定の送信元 IP ブロックやポート範囲などを入力することもできます。 準備ができたら、**[OK]** を選択してルールを作成します。

![受信規則の作成](./media/nsg-quickstart-portal/create-inbound-rule.png)

最後に、ネットワーク セキュリティ グループを、サブネットまたは特定のネットワーク インターフェイスに関連付けます。 ネットワーク セキュリティ グループをサブネットに関連付けてみましょう。 **[サブネット]** を選択し、**[関連付け]** を選択します。

![ネットワーク セキュリティ グループとサブネットの関連付け](./media/nsg-quickstart-portal/associate-subnet.png)

仮想ネットワークを選択し、適切なサブネットを選択します。

![ネットワーク セキュリティ グループと仮想ネットワークの関連付け](./media/nsg-quickstart-portal/select-vnet-subnet.png)

これで、ネットワーク セキュリティ グループの作成、ポート 80 のトラフィックを許可する受信規則の作成、ネットワーク セキュリティ グループとサブネットの関連付けが完了しました。 そのサブネットに接続するすべての VM に、ポート 80 で到達できます。

## <a name="more-information-on-network-security-groups"></a>ネットワーク セキュリティ グループの詳細
このページのクイック コマンドでは、VM にフローするトラフィックの開始と実行を行うことができます。 ネットワーク セキュリティ グループには優れた機能が多数用意されており、リソースへのアクセスをきめ細かく制御できます。 詳細については、 [ネットワーク セキュリティ グループと ACL 規則の作成](../../virtual-network/virtual-networks-create-nsg-arm-ps.md)に関するページをご覧ください。

高可用性 Web アプリケーション用に、Azure Load Balancer の背後に VM を配置する必要があります。 ロード バランサーは、トラフィックをフィルターできるネットワーク セキュリティ グループとともに、VM のトラフィックを分散します。 詳細については、[Azure の Linux 仮想マシンを負荷分散して高可用性アプリケーションを作成する方法](tutorial-load-balancer.md)に関するページをご覧ください。

## <a name="next-steps"></a>次のステップ
この例では、HTTP トラフィックを許可する単純な規則を作成します。 より精密な環境の作成については、次の記事で確認できます。

* [Azure リソース マネージャーの概要](../../azure-resource-manager/resource-group-overview.md)
* [ネットワーク セキュリティ グループ (NSG) について](../../virtual-network/virtual-networks-nsg.md)