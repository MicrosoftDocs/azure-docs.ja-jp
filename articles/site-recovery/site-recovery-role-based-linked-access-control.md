---
title: "ロール ベースのアクセス制御を使用した Azure Site Recovery の管理 | Microsoft Docs"
description: "この記事では、ロール ベースのアクセス制御 (RBAC) を適用および使用して、Azure Site Recovery のデプロイを管理する方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: manayar
ms.openlocfilehash: 9dd74014bf05234a83c7678b67b42b96cd8b8d64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-role-based-access-control-to-manage-azure-site-recovery-deployments"></a>ロール ベースのアクセス制御を使用した Azure Site Recovery の管理

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用すると、チーム内で責任を分離して、特定のジョブを実行する必要に応じてユーザーに特定のアクセス許可のみを付与できます。

Azure Site Recovery には、Site Recovery の管理操作を制御するための組み込みロールが 3 つ用意されています。 [Azure RBAC の組み込みのロール](../active-directory/role-based-access-built-in-roles.md)について説明します。

* [Site Recovery 共同作成者](../active-directory/role-based-access-built-in-roles.md#site-recovery-contributor) - このロールには、Recovery Services コンテナーでの Azure Site Recovery の操作の管理に必要なすべてのアクセス許可があります。 ただし、このロールを持つユーザーは、Recovery Services コンテナーの作成や削除、または他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、必要に応じてアプリケーションまたは組織全体のディザスター リカバリーを有効にして管理できるディザスター リカバリー管理者に最も適しています。
* [Site Recovery オペレーター](../active-directory/role-based-access-built-in-roles.md#site-recovery-operator) - この役割には、フェールオーバーとフェールバックの操作を実行して管理するアクセス許可があります。 このロールのユーザーは、レプリケーションの有効化または無効化、コンテナーの作成または削除、新しいインフラストラクチャの登録、他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、実際の障害状況や DR ドリルのようなシミュレーション障害状況において、アプリケーション所有者および IT 管理者から指示されて、仮想マシンまたはアプリケーションをフェールオーバーできる、ディザスター リカバリー オペレーターに最も適しています。 障害が解決された後、DR オペレーターは仮想マシンを再び保護してフェールバックを行うことができます。
* [Site Recovery 閲覧者](../active-directory/role-based-access-built-in-roles.md#site-recovery-reader) - このロールには、すべての Site Recovery 管理操作を見るアクセス許可があります。 このロールは、保護の現在の状態を監視し、必要な場合にサポート チケットを発行できる、IT 監視担当役員に最も適しています。

制御を強化するために独自のロールを定義する場合は、Azure で [カスタム ロールを作成する](../active-directory/role-based-access-control-custom-roles.md)方法を参照してください。

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>新しい仮想マシンのレプリケーションの有効化に必要なアクセス許可
Azure Site Recovery を使用して新しい仮想マシンを Azure にレプリケートすると、ユーザーが Site Recovery に提供される Azure のリソースを使用するのに必要なアクセス許可を持っていることを確認するため、関連付けられているユーザーのアクセス レベルが検証されます。

新しい仮想マシンのレプリケーションを有効にするには、ユーザーには次のアクセス許可が必要です。
* 選択されたリソース グループ内に仮想マシンを作成するためのアクセス許可
* 選択された仮想ネットワーク内に仮想マシンを作成するためのアクセス許可
* 選択したストレージ アカウントに書き込むためのアクセス許可

新しい仮想マシンのレプリケーションを完了するには、ユーザーには次のアクセス許可が必要です。

> [!IMPORTANT]
>リソースのデプロイに使用されるデプロイメント モデル (Resource Manager/ Classic) ごとに関連するアクセス許可が追加されていることを確認します。

| **リソースの種類** | **デプロイメント モデル** | **アクセス許可** |
| --- | --- | --- |
| コンピューティング | Resource Manager | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | クラシック | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| ネットワーク | Resource Manager | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | クラシック | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | Resource Manager | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | クラシック | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| リソース グループ | Resource Manager | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Resource Manager と Classic のデプロイメント モデルそれぞれに、'仮想マシン共同作業者' と '従来の仮想マシン共同作業者' の[組み込みのロール](../active-directory/role-based-access-built-in-roles.md)を使用することを検討してください。

## <a name="next-steps"></a>次のステップ
* [ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md): Azure ポータルでの RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md)
  * [Azure CLI](../active-directory/role-based-access-control-manage-access-azure-cli.md)
  * [REST API](../active-directory/role-based-access-control-manage-access-rest.md)
* [ロールベースのアクセス制御のトラブルシューティング](../active-directory/role-based-access-control-troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
