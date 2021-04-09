---
title: Azure VMware Solution by CloudSimple - ワークロード VM をプライベート クラウドに移行する
description: オンプレミスの vCenter から CloudSimple プライベートクラウドの vCenter に仮想マシンを移行する方法について説明します
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899322"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>オンプレミスの vCenter からプライベートクラウドの vCenter 環境にワークロード VM を移行する

オンプレミスのデータセンターから CloudSimple プライベート クラウドに VM を移行するには、いくつかのオプションを使用できます。  プライベート クラウドでは VMware vCenter へのネイティブ アクセスが提供されており、VMware でサポートされているツールをワークロードの移行に使用できます。 この記事では、vCenter のいくつかの移行オプションについて説明します。

## <a name="prerequisites"></a>前提条件

オンプレミスのデータセンターから VM とデータを移行するには、データセンターからプライベート クラウド環境へのネットワーク接続が必要です。  ネットワーク接続を確立するには、次のいずれかの方法を使用します。

* オンプレミス環境とプライベート クラウドの間の[サイト間 VPN 接続](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)。
* オンプレミスの ExpressRoute 回線と CloudSimple ExpressRoute 回線の間の ExpressRoute Global Reach 接続。

vMotion を使用した VM の移行のために、オンプレミスの vCenter 環境からプライベート クラウドへのネットワーク パスが利用可能である必要があります。  オンプレミスの vCenter の vMotion ネットワークには、ルーティング機能が必要です。  オンプレミスの vCenter とプライベート クラウドの vCenter の間のすべての vMotion トラフィックがファイアウォールで許可されていることを確認します。 (プライベート クラウドでは、vMotion ネットワークでのルーティングは既定で構成されています)。

## <a name="migrate-isos-and-templates"></a>ISO とテンプレートを移行する

プライベート クラウドに新しい仮想マシンを作成するには、ISO と VM テンプレートを使用します。  ISO とテンプレートをプライベート クラウドの vCenter にアップロードして使用できるようにするには、次の方法を使用します。

1. VCenter UI からプライベート クラウドの vCenter に ISO をアップロードします。
2. プライベート クラウドの vCenter で[コンテンツ ライブラリを発行](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html)します。

    1. オンプレミスのコンテンツ ライブラリを発行します。
    2. プライベート クラウドの vCenter に新しいコンテンツ ライブラリを作成します。
    3. 発行されたオンプレミスのコンテンツ ライブラリをサブスクライブします。
    4. サブスクライブしたコンテンツにアクセスするためにコンテンツ ライブラリを同期します。

## <a name="migrate-vms-using-powercli"></a>PowerCLI を使用して VM を移行する

オンプレミスの vCenter からプライベート クラウドの vCenter に VM を移行するには、VMware PowerCLI または VMware Labs から入手できる Cross vCenter Workload Migration Utility を使用します。  次のサンプル スクリプトは、PowerCLI 移行コマンドを示しています。

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 宛先の vCenter サーバーと ESXi ホストの名前を使用するには、オンプレミスからプライベート クラウドへの DNS 転送を構成します。

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>NSX レイヤー 2 VPN を使用して VM を移行する

このオプションを使用すると、オンプレミスの VMware 環境から Azure のプライベート クラウドへのワークロードのライブ マイグレーションを実行できます。  この拡張レイヤー 2 ネットワークを使用すると、オンプレミスからのサブネットをプライベート クラウドで使用できるようになります。  移行後、VM に新しい IP アドレスの割り当ては必要ありません。

「[レイヤー 2 拡張済みネットワークを使用したワークロードの移行](migration-layer-2-vpn.md)」では、レイヤー 2 VPN を使用して、レイヤー 2 ネットワークをオンプレミス環境からプライベート クラウドに拡張する方法について説明しています。

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>バックアップ ツールとディザスター リカバリー ツールを使用して VM を移行する

VM のプライベート クラウドへの移行は、バックアップ/復元ツールとディザスター リカバリー ツールを使用して行うことができます。  サードパーティのツールを使用して作成されたバックアップからの復元のターゲットとして、プライベート クラウドを使用します。  また、プライベート クラウドは、VMware SRM またはサードパーティのツールを使用したディザスター リカバリーのターゲットとして使用することもできます。

これらのツールの詳細な使用方法については、次のトピックを参照してください。

* [Veeam B&R を使用して CloudSimple プライベート クラウドでワークロード仮想マシンをバックアップする](backup-workloads-veeam.md)
* [CloudSimple プライベート クラウドをオンプレミスの VMware ワークロード用のディザスター リカバリー サイトとして設定する](disaster-recovery-zerto.md)
