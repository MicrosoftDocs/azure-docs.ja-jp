---
title: 概念 - プライベート クラウドの更新とアップグレード
description: Azure VMware Solution の主要なアップグレード プロセスと機能について説明します。
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 9810de40944f70a4efb7ec81d17868ffdf256c7d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586150"
---
# <a name="azure-vmware-solution-private-cloud-updates-and-upgrades"></a>Azure VMware Solution のプライベート クラウドの更新とアップグレード

Azure VMware Solution のプライベート クラウドの利点の 1 つは、管理者に代わってプラットフォームのメンテナンスが行われることです。 メンテナンスには、VMware によって検証されたソフトウェア バンドルに対する自動更新が含まれており、Azure VMware Solution プライベート クラウド ソフトウェアの最新バージョンを使用していることを確認するのに役立ちます。

Azure VMware Solution プライベート クラウドには、具体的に次のものが含まれます。

- VMware ESXi ハイパーバイザーを使用してプロビジョニングされた専用のベアメタル サーバー ノード 
- ESXi と vSAN を管理するための vCenter サーバー 
- vSphere ワークロード VM 用の VMware NSX-T のソフトウェアによるネットワーク  
- VSphere ワークロード VM 用の VMware vSAN データストア  
- ワークロード モビリティ用の VMware HCX  

Azure VMware Solution プライベート クラウドには、Azure アンダーレイに接続やプライベート クラウドの運用に必要なリソースも含まれています。 Azure VMware Solution では、アンダーレイと VMware コンポーネントの両方の正常性が継続的に監視されます。 Azure VMware Solution でエラーが検出されると、失敗したコンポーネントを修復するアクションが実行されます。 

## <a name="what-components-get-updated"></a>更新されるコンポーネントについて   

Azure VMware Solution では、次の VMware コンポーネントが更新されます。 

- ベアメタル サーバー ノードで実行されている vCenter Server と ESXi 
- vSAN 
- NSX-T 

また、Azure VMware Solution では、ドライバー、ネットワーク スイッチ上のソフトウェア、ベアメタル ノードのファームウェアなど、アンダーレイのソフトウェアも更新されます。 

## <a name="types-of-updates"></a>更新プログラムの種類

Azure VMware Solution では、次の種類の更新プログラムが VMware コンポーネントに適用されます。

- パッチ:VMware によってリリースされたセキュリティ パッチおよびバグ修正プログラム。 
- 更新:1 つまたは複数の VMware コンポーネントのマイナー バージョンの更新。 
- アップグレード:1 つまたは複数の VMware コンポーネントのメジャー バージョンの更新。

パッチがプライベート クラウドに適用される前と後には、その旨が通知されます。 また、お客様のプライベート クラウドに更新やアップグレードが適用される前に、お客様と連携してメンテナンス期間をスケジュールします。 

## <a name="vmware-appliance-backup"></a>VMware アプライアンスのバックアップ 

Azure VMware Solution では、次の VMware コンポーネントの構成バックアップも作成されます。

- vCenter Server 
- NSX-T Manager 

障害が発生した際は、Azure VMware Solution を使用して構成のバックアップからこれらのコンポーネントを復元できます。 

## <a name="vmware-software-versions"></a>VMware ソフトウェアのバージョン
[!INCLUDE [vmware-software-versions](includes/vmware-software-versions.md)]


## <a name="next-steps"></a>次のステップ

Azure VMware Solution の主要なアップグレード プロセスと機能について理解したので、次について確認する必要がある可能性があります。

- [プライベート クラウドを作成する方法](tutorial-create-private-cloud.md)。
- [Azure VMware Solution リソースを有効にする方法](enable-azure-vmware-solution.md)。

<!-- LINKS - external -->

<!-- LINKS - internal -->
