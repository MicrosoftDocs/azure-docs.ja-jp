---
title: "Azure における Linux VMS 向けデプロイ モデル間での移行 | Microsoft Docs"
description: "この記事では、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について技術的に詳しく説明します。"
services: virtual-machines-linux
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 29267453-f894-4180-bb67-dce2a0e062bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 233116deaaaf2ac62981453b05c4a5254e836806
ms.openlocfilehash: c30916c0b5078a6e346ac2a7ddeb07eda3bda218


---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細
この記事では、リソースと機能のレベルで移行について技術的に説明します。これは、プラットフォームで、リソースがクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行されるしくみを理解するのに役立ちます。 詳細については、サービス告知記事の「 [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)」を参照してください。

## <a name="detailed-guidance-on-migration-at-a-resource-and-feature-level"></a>リソースと機能レベルの移行の詳しいガイダンス
次の表では、リソースはクラシックと Resource Manager で呼称が異なる場合があります。 この表にない機能とリソースは現時点ではサポートされていません。

| クラシック表示 | Resource Manager の表示 | 注記 |
| --- | --- | --- |
| クラウド サービス名 |DNS name |移行中、 `<cloudservicename>-migrated`の命名パターンで、すべてのクラウド サービスに新しいリソース グループが作成されます。 このリソース グループにはすべてのリソースが含まれます。 クラウド サービス名は、パブリック IP アドレスが関連付けられた DNS 名になります。 |
| 仮想マシン |仮想マシン |VM 固有のプロパティはそのまま移行されます。 コンピューター名など、一部の osProfile 情報はクラシック デプロイメント モデルに保存されておらず、移行後も空のままであることに注意してください。 |
| VM に接続されているディスク リソース |VM に接続されている暗黙的なディスク |ディスクは、Resource Manager デプロイメント モデルの最上位リソースとしてモデル化されません。 VM の下で暗黙的なディスクとして移行されます。 現時点では、VM に接続されているディスクのみに対応しています。 移行を有効にするために、クラシック ストレージ アカウントを Resource Manager VM で使用できるようになりました。 これにより、更新がなくても、ディスクを Resource Manager モデルに簡単に移行できます。 |
| VM 拡張機能 |VM 拡張機能 |XML 拡張機能を除くすべてのリソース拡張機能が、クラシック デプロイメント モデルから移行されます。 |
| 仮想マシン証明書 |Azure Key Vault の証明書 |クラウド サービスにサービス証明書が含まれている場合は、クラウド サービスごとに新しい Azure Key Vault が作成され、証明書はそこに移動されます。 VM は、Key Vault の証明書を参照するように更新されます。 |
| WinRM 構成 |osProfile の WinRM 構成 |Windows リモート管理の構成は、移行の一環として、そのまま移動されます。 |
| 可用性セットのプロパティ |可用性セットのリソース |クラシック デプロイメント モデルでは、可用性セット仕様は VM のプロパティでした。 可用性セットは、移行の一環として、上位のリソースになります。 1 つのクラウド サービスに対して複数の可用性セット、または&1; つ以上の可用性セットとクラウド サービスの可用性セットにない VM という構成はサポートされていません。 |
| VM のネットワーク構成 |プライマリ ネットワーク インターフェイス |移行後、VM のネットワーク構成がプライマリ ネットワーク インターフェイス リソースとして表示されます。 仮想ネットワークにない VM の場合は、内部 IP アドレスが移行中に変わります。 |
| VM の複数のネットワーク インターフェイス |ネットワーク インターフェイス |VM に複数のネットワーク インターフェイスが関連付けられている場合は、Resource Manager デプロイメント モデルの移行の一環として、各ネットワーク インターフェイスが、すべてのプロパティとともに上位のリソースになります。 |
| 負荷分散エンドポイント セット |Load Balancer |クラシック デプロイメント モデルでは、クラウド サービスごとに暗黙的なロード バランサーが割り当てられました。 移行中に、新しいロード バランサー リソースが作成され、その負荷分散エンドポイント セットはロード バランサー ルールになります。 |
| 受信 NAT のルール |受信 NAT のルール |VM に定義されている入力エンドポイントは、移行中、ロード バランサーで受信ネットワーク アクセス変換ルールに変換されます。 |
| VIP アドレス |DNS 名のあるパブリック IP アドレス |仮想 IP アドレスがパブリック IP アドレスになり、ロード バランサーに関連付けられます。 |
| Virtual Network |Virtual Network |仮想ネットワークとそのすべてのプロパティが Resource Manager デプロイメント モデルに移行されます。 `-migrated`という名前で新しいリソース グループが作成されます。 [サポートされていない構成](virtual-machines-windows-migration-classic-resource-manager.md)をご確認ください。 |
| 予約済み IP |静的割り当て方法のあるパブリック IP アドレス |ロード バランサーに関連付けられている予約済み IP が、クラウド サービスまたは仮想マシンの移行に伴って移行されます。 現時点では、関連付けられていない予約済み IP 移行はサポートされていません。 |
| VM ごとのパブリック IP アドレス |動的割り当て方法のあるパブリック IP アドレス |VM に関連付けられているパブリック IP アドレスは、割り当て方法が静的に設定されているパブリック IP アドレス リソースとして変換されます。 |
| NSG |NSG |サブネットに関連付けられているネットワーク セキュリティ グループは、Resource Manager デプロイメント モデルへの移行の一環として複製されます。 移行中、クラシック デプロイメント モデルの NSG は削除されないことに注意してください。 ただし、NSG の管理プレーン操作は、移行中はブロックされます。 |
| DNS サーバー |DNS サーバー |仮想ネットワークまたは VM に関連付けられている DNS サーバーは、該当するリソース移行の一環として、すべてのプロパティと共に移行されます。 |
| UDR |UDR |サブネットに関連付けられているユーザー定義のルートは、Resource Manager デプロイメント モデルへの移行の一環として複製されます。 移行中、クラシック デプロイメント モデルの UDR は削除されないことに注意してください。 ただし、UDR の管理プレーン操作は、移行中はブロックされます。 |
| VM のネットワーク構成の IP 転送プロパティ |NIC の IP 転送プロパティ |VM上 の IP 転送プロパティは、移行中、ネットワーク インターフェイスでプロパティに変換されます。 |
| 複数の IP のあるロード バランサー |複数のパブリック IP リソースのあるロード バランサー |移行後、ロード バランサーが関連付けられているすべてのパブリック IP がパブリック IP リソースに変換され、ロード バランサーに関連付けられます。 |
| VM の内部 DNS 名 |NIC の内部 DNS 名 |移行中、VM の内部 DNS サフィックスは、NIC の "InternalDomainNameSuffix" という名前の読み取り専用プロパティに移行されます。 サフィックス名は移行後もそのままで、VM 解決は前と同じように動作し続けます。 |

## <a name="illustration-of-a-simple-migration-walkthrough"></a>シンプルな移行手順の図
以下のサンプル スクリーンショットでは、準備段階後のクラウド サービスと VM (仮想ネットワーク外) を確認できます。

![準備後のクラシック表示を示すスクリーン ショット](./media/virtual-machines-windows-migration-classic-resource-manager/classic-migration-prepare-portal.png)
![準備後の Resource Manager 表示を示すスクリーン ショット](./media/virtual-machines-windows-migration-classic-resource-manager/resourcemanager-migration-prepare-portal.png)

## <a name="next-steps"></a>次のステップ
これで Resource Manager へのクラシック IaaS リソースの移行について理解できました。次は、リソースの移行を開始します。

* [PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](virtual-machines-linux-cli-migration-classic-resource-manager.md)
* [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
* [コミュニティ PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを複製する](virtual-machines-windows-migration-scripts.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO5-->


