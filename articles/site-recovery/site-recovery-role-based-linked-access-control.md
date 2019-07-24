---
title: ロールベースのアクセス制御 (RBAC) を使用した Azure Site Recovery のアクセス管理 | Microsoft Docs
description: この記事では、ロールベースのアクセス制御 (RBAC) を適用して、Azure Site Recovery のアクセスを管理する方法について説明します。
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 33fc2cd19152fb6cbbffb106aa058948d39555f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59281711"
---
# <a name="manage-site-recovery-access-with-role-based-access-control-rbac"></a>ロールベースのアクセス制御 (RBAC) を使用して Site Recovery のアクセスを管理する

Azure のロールベースのアクセス制御 (RBAC) では、Azure のアクセス権の詳細な管理を実現します。 RBAC を使用すると、チーム内で責任を分離して、特定のジョブを実行する必要に応じてユーザーに特定のアクセス許可のみを付与できます。

Azure Site Recovery には、Site Recovery の管理操作を制御するための組み込みロールが 3 つ用意されています。 [Azure RBAC の組み込みのロール](../role-based-access-control/built-in-roles.md)について説明します。

* [Site Recovery 共同作成者](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - このロールには、Recovery Services コンテナーでの Azure Site Recovery の操作の管理に必要なすべてのアクセス許可があります。 ただし、このロールを持つユーザーは、Recovery Services コンテナーの作成や削除、または他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、必要に応じてアプリケーションまたは組織全体のディザスター リカバリーを有効にして管理できるディザスター リカバリー管理者に最も適しています。
* [Site Recovery オペレーター](../role-based-access-control/built-in-roles.md#site-recovery-operator) - この役割には、フェールオーバーとフェールバックの操作を実行して管理するアクセス許可があります。 このロールのユーザーは、レプリケーションの有効化または無効化、コンテナーの作成または削除、新しいインフラストラクチャの登録、他のユーザーへのアクセス権の割り当てを行うことはできません。 このロールは、実際の障害状況や DR ドリルのようなシミュレーション障害状況において、アプリケーション所有者および IT 管理者から指示されて、仮想マシンまたはアプリケーションをフェールオーバーできる、ディザスター リカバリー オペレーターに最も適しています。 障害が解決された後、DR オペレーターは仮想マシンを再び保護してフェールバックを行うことができます。
* [Site Recovery 閲覧者](../role-based-access-control/built-in-roles.md#site-recovery-reader) - このロールには、すべての Site Recovery 管理操作を見るアクセス許可があります。 このロールは、保護の現在の状態を監視し、必要な場合にサポート チケットを発行できる、IT 監視担当役員に最も適しています。

制御を強化するために独自のロールを定義する場合は、Azure で [カスタム ロールを作成する](../role-based-access-control/custom-roles.md)方法を参照してください。

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
| Compute | リソース マネージャー | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | クラシック | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| ネットワーク | リソース マネージャー | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | クラシック | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Storage | リソース マネージャー | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | クラシック | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| リソース グループ | リソース マネージャー | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

Resource Manager デプロイ モデルとクラシック デプロイ モデルそれぞれに、'仮想マシン共同作業者' と '従来の仮想マシン共同作業者' の[組み込みのロール](../role-based-access-control/built-in-roles.md)を使用することを検討してください。

## <a name="next-steps"></a>次の手順
* [ロールベースのアクセス制御](../role-based-access-control/role-assignments-portal.md):Azure portal での RBAC の基本について説明します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [ロールベースのアクセス制御のトラブルシューティング](../role-based-access-control/troubleshooting.md):一般的な問題の修正に関する推奨事項を紹介します。
