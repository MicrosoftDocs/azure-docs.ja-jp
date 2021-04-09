---
title: Azure VM 間の接続に関する問題のトラブルシューティング | Microsoft Docs
description: Azure VM 間で発生することがある接続に関する問題を解決する方法について説明します。
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 6ece3e639e0ef3516696e2a0bad7deeb833433a6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219200"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Azure VM 間の接続に関する問題のトラブルシューティング

Azure 仮想マシン (VM) 間の接続について問題が発生することがあります。 この記事では、この問題の解決に役立つトラブルシューティング手順について説明します。 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>症状

ある Azure VM が別の Azure VM に接続できない。

## <a name="troubleshooting-guidance"></a>トラブルシューティング ガイダンス 

1. [NIC の構成が正しいかどうかを確認する](#step-1-check-whether-nic-is-misconfigured)
2. [ネットワーク トラフィックが NSG または UDR によってブロックされているかどうかを確認する](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [ネットワーク トラフィックが VM ファイアウォールによってブロックされているかどうかを確認する](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [VM のアプリまたはサービスがポートをリッスンしているかどうかを確認する](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [問題の原因が SNAT であるかどうかを確認する](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [トラフィックがクラシック VM の ACL によってブロックされているかどうかを確認する](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [クラシック VM のエンドポイントが作成されているかどうかを確認する](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [VM ネットワーク共有への接続を試みる](#step-8-try-to-connect-to-a-vm-network-share)
9. [VNET 間接続を確認する](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順

次の手順に従って、問題のトラブルシューティングを行います。 各手順を完了したら、問題が解決されたかどうかを確認します。 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>手順 1:NIC の構成が正しいかどうかを確認する

「[Azure Windows VM のネットワーク インターフェイスをリセットする方法](../virtual-machines/troubleshooting/reset-network-interface.md)」の手順に従います。 

ネットワーク インターフェイス (NIC) の変更後に問題が発生した場合は、次の手順を実行します。

**マルチ NIC VM**

1. NIC を追加します。
2. NIC の問題を解決するか、問題のある NIC を削除します。  NIC を再度追加します。

詳細については、「[仮想マシンに対するネットワーク インターフェイスの追加と削除](virtual-network-network-interface-vm.md)」を参照してください。

**シングル NIC VM** 

- [Windows VM の再デプロイ](../virtual-machines/troubleshooting/redeploy-to-new-node-windows.md)
- [Linux VM の再デプロイ](../virtual-machines/troubleshooting/redeploy-to-new-node-linux.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>手順 2:ネットワーク トラフィックが NSG または UDR によってブロックされているかどうかを確認する

[Network Watcher の IP フロー検証](../network-watcher/network-watcher-ip-flow-verify-overview.md)に関するページと [NSG フローのログ記録](../network-watcher/network-watcher-nsg-flow-logging-overview.md)に関するページを参照して、トラフィック フローを妨害するネットワーク セキュリティ グループ (NSG) またはユーザー定義ルート (UDR) が存在するかどうかを確認します。

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>手順 3:ネットワーク トラフィックが VM ファイアウォールによってブロックされているかどうかを確認する

ファイアウォールを無効にして、結果をテストします。 問題が解決したら、ファイアウォールの設定を確認し、ファイアウォールを再度有効にします。

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>手順 4:VM のアプリまたはサービスがポートをリッスンしているかどうかを確認する

VM のアプリまたはサービスがポートをリッスンしているかどうかを確認するには、次のいずれかの方法を使用します。

- 次のコマンドを実行して、サーバーがポートをリッスンしているかどうかを確認します。

**Windows VM**

```console
netstat –ano
```

**Linux VM**

```console
netstat -l
```

- 仮想マシン自体で **telnet** コマンドを実行して、ポートをテストします。 テストが失敗する場合は、アプリケーションまたはサービスがポートをリッスンするように構成されていません。

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>手順 5:問題の原因が SNAT であるかどうかを確認する

一部のシナリオでは、Azure 外のリソースに依存する負荷分散ソリューションの背後に VM が配置されます。 これらのシナリオで断続的な接続の問題が発生した場合、[SNAT ポートの枯渇](../load-balancer/load-balancer-outbound-connections.md)が原因である可能性があります。 この問題を解決するには、ロード バランサーの背後にある各 VM の VIP (クラシックの場合は ILPIP) を作成し、NSG または ACL で保護します。 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>手順 6:トラフィックがクラシック VM の ACL によってブロックされているかどうかを確認する

アクセス制御リスト (ACL) を使用して、仮想マシン エンドポイントのトラフィックを選択的に許可または拒否することができます。 詳細については、「[エンドポイントの ACL の管理](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)」を参照してください。

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>手順 7:クラシック VM のエンドポイントが作成されているかどうかを確認する

クラシック デプロイ モデルを使用して Azure で作成したすべての VM は、プライベート ネットワーク チャネルを介して、同じクラウド サービスまたは仮想ネットワーク内の他の仮想マシンと自動的に通信できます。 ただし、他の仮想ネットワーク上にあるコンピューターと通信するには、受信ネットワーク トラフィックを仮想マシンに転送するエンドポイントが必要になります。 詳細については、[エンドポイントの設定方法](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints)に関するページを参照してください。

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>手順 8:VM ネットワーク共有への接続を試みる

VM ネットワーク共有に接続できない場合の原因として、使用できない NIC が VM にあることが考えられます。 使用できない NIC を削除するには、[使用できない NIC の削除方法](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)に関するページを参照してください。

### <a name="step-9-check-inter-vnet-connectivity"></a>手順 9:VNET 間接続を確認する

[Network Watcher の IP フロー検証](../network-watcher/network-watcher-ip-flow-verify-overview.md)に関するページと [NSG フローのログ記録](../network-watcher/network-watcher-nsg-flow-logging-overview.md)に関するページを参照して、トラフィック フローを妨害する NSG または UDR が存在するかどうかを確認します。 VNET 間構成を[ここ](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections)で確認することもできます。

### <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。