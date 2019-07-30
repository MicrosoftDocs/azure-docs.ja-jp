---
title: CloudSimple プライベート クラウドによる Azure VMware ソリューションの管理
description: CloudSimple プライベート クラウドのリソースとアクティビティを管理するために使用できる機能について説明します
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 05a2fb451b3acce1011c1d5f4cf17f0a865d57d0
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332367"
---
# <a name="manage-private-cloud-resources-and-activity"></a>プライベート クラウドのリソースとアクティビティを管理する

プライベート クラウドは CloudSimple ポータルから管理されます。  CloudSimple ポータルから、状態、使用可能なリソース、プライベート クラウドでのアクティビティ、その他の設定を確認します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

[CloudSimple ポータル](access-cloudsimple-portal.md)にアクセスします。

## <a name="view-the-list-of-private-clouds"></a>プライベート クラウドの一覧を表示する

**[リソース]** ページの **[Private Clouds]\(プライベート クラウド\)** ページには、サブスクリプション内のすべてのプライベート クラウドが一覧表示されます。 情報には、名前、vSphere クラスターの数、場所、プライベート クラウドの現在の状態、およびリソース情報が含まれます。

![[プライベート クラウド] ページ](media/manage-private-cloud.png)

追加情報を表示する、またアクションを実行するプライベート クラウドを選択します。

## <a name="private-cloud-summary"></a>プライベート クラウドの概要

選択したプライベート クラウドの包括的な概要を表示します。  概要ページには、プライベート クラウドにデプロイされている DNS サーバーが含まれます。  オンプレミスの DNS サーバーからプライベート クラウドの DNS サーバーへの DNS 転送を設定できます。  DNS 転送の詳細については、[プライベート クラウド vCenter の名前解決用 DNS をオンプレミスから構成する方法](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)に関するページを参照してください。

![プライベート クラウドの概要](media/private-cloud-summary.png)

### <a name="available-actions"></a>使用可能なアクション

* [vSphere クライアントの起動](https://docs.azure.cloudsimple.com/vsphere-access/)。 このプライベート クラウドの vCenter にアクセスします。
* [ノードの購入](create-nodes.md)。 このプライベート クラウドにノードを追加します。
* [拡大](expand-private-cloud.md)。 このプライベート クラウドにノードを追加します。
* **更新する**。 このページの情報を更新します。
* **削除**。 プライベート クラウドはいつでも削除できます。 **削除する前に、すべてのシステムとデータがバックアップ済みであることを確認してください。** プライベート クラウドを削除すると、すべての VM、vCenter 構成、およびデータが削除されます。 選択したプライベート クラウドの概要セクションで **[削除]** をクリックします。 削除後、すべてのプライベート クラウド データは、安全で、コンプライアンスに厳格に準拠した消去プロセスで消去されます。
* [vSphere 権限の変更](escalate-private-cloud-privileges.md)。  このプライベート クラウドに対する特権をエスカレーションします。

## <a name="private-cloud-vlanssubnets"></a>[Private Cloud VLANS/subnets] (プライベート クラウドの VLAN/サブネット)

選択したプライベート クラウドに対して定義されている VLAN/サブネットの一覧を表示します。  一覧には、プライベート クラウドの作成時に作成された管理 VLAN/サブネットが含まれています。

![プライベート クラウド - VLAN/サブネット](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>使用可能なアクション

* [VLAN/サブネットの追加](https://docs.azure.cloudsimple.com/create-vlan-subnet/)。 VLAN/サブセットをこのプライベート クラウドに追加します。

以下のアクションのために VLAN/サブネットを選択します
* [ファイアウォール テーブルのアタッチ](https://docs.azure.cloudsimple.com/firewall/)。 ファイアウォール テーブルをこのプライベート クラウドにアタッチします。
* **Edit (編集)**
* **削除** (ユーザー定義の VLAN/サブネットのみ)

## <a name="private-cloud-activity"></a>プライベート クラウド アクティビティ

選択したプライベート クラウドの次の情報を表示します。  アクティビティ情報は、すべてのアクティビティから、選択したプライベート クラウドで絞り込んだ一覧です。  このページには最近のアクティビティが最大 25 件表示されます。

* 最近のアラート
* 最近のイベント
* 最近のタスク
* 最近の監査

![プライベート クラウド - アクティビティ](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>クラウド ラック

クラウド ラックはプライベート クラウドの構成要素です。 各ラックは容量の単位を提供します。 プライベート クラウドを作成または拡張するときに、CloudSimple は、選択に基づいてクラウド ラックを自動的に構成します。  それぞれが割り当てられているプライベート クラウドを含む、クラウド ラックの完全な一覧を表示します。

![プライベート クラウド - クラウド ラック](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 管理ネットワーク

現在プライベート クラウドで構成されている VMware 管理リソースおよび仮想マシンの一覧。 情報には、ソフトウェアのバージョン、完全修飾ドメイン名 (FQDN)、リソースの IP アドレスが含まれます。

![プライベート クラウド - vSphere 管理ネットワーク](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>次の手順

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [プライベート クラウド](cloudsimple-private-cloud.md)の詳細を確認する