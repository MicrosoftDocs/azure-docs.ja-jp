---
title: 移動エラーを解決する
description: 新しいリソース グループまたはサブスクリプションへのリソースの移動に関するトラブルシューティング。
ms.topic: conceptual
ms.date: 08/27/2019
ms.openlocfilehash: 41b1e2435caf9874f3582a3394664c7b7f5a8d29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90054164"
---
# <a name="troubleshoot-moving-azure-resources-to-new-resource-group-or-subscription"></a>新しいリソース グループまたはサブスクリプションへの Azure リソースの移動に関するトラブルシューティング

この記事では、リソースを移動するときに問題の解決に役立つ方法を示します。

## <a name="upgrade-a-subscription"></a>サブスクリプションをアップグレードする

実際に Azure サブスクリプションをアップグレードする場合 (無料から従量課金制への切り替えなど)、お使いのサブスクリプションを変換する必要があります。

* 無料試用版をアップグレードするには、「[無料試用版または Microsoft Imagine Azure サブスクリプションを従量課金制にアップグレードする](../../cost-management-billing/manage/upgrade-azure-subscription.md)」をご覧ください。
* 従量課金制のアカウントを変更するには、「[Azure の従量課金制サブスクリプションを別のオファーに変更する](../../cost-management-billing/manage/switch-azure-offer.md)」をご覧ください。

サブスクリプションを変換できない場合は、[Azure サポート要求を作成](../../azure-portal/supportability/how-to-create-azure-support-request.md)してください。 問題の種類として **[サブスクリプション管理]** を選択します。

## <a name="service-limitations"></a>サービスの制限事項

一部のサービスでは、リソースを移動するときに追加の考慮が必要になります。 次のサービスを移動する場合は、必ずガイダンスと制限事項を確認してください。

* [App Services](./move-limitations/app-service-move-limitations.md)
* [Azure DevOps Services](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
* [クラシック デプロイ モデル](./move-limitations/classic-model-move-limitations.md)
* [ネットワーク](./move-limitations/networking-move-limitations.md)
* [Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
* [Virtual Machines](./move-limitations/virtual-machines-move-limitations.md)

## <a name="large-requests"></a>大規模な要求

可能であれば、大規模な移動は個別の移動操作に分けます。 1 回の操作に含まれるリソースが 800 を超えると、Resource Manager から直ちにエラーが返されます。 ただし、800 未満のリソースの移動でも、タイムアウトで失敗になることがあります。

## <a name="resource-not-in-succeeded-state"></a>正常な状態にないリソース

リソースが正常な状態にないために移動できないことを示すエラー メッセージが表示された場合、実際に移動をブロックしているのは依存リソースであることがあります。 通常、エラー コードは **MoveCannotProceedWithResourcesNotInSucceededState** です。

ソースまたはターゲットのリソース グループに仮想ネットワークが含まれている場合は、移動中に仮想ネットワークのすべての依存リソースの状態が確認されます。 この確認には、仮想ネットワークに直接的または間接的に依存するそのようなリソースが含まれます。 これらのリソースのいずれかがエラー状態の場合、移動はブロックされます。 たとえば、仮想ネットワークを使用する仮想マシンで障害が発生した場合、移動はブロックされます。 移動は、仮想マシンが移動するリソースの 1 つではなく、移動用のリソース グループのいずれにも存在しない場合でもブロックされます。

このエラーが発生した場合は、2 つの選択肢があります。 仮想ネットワークのないリソース グループにリソースを移動するか、[サポートにお問い合わせください](../../azure-portal/supportability/how-to-create-azure-support-request.md)。

## <a name="next-steps"></a>次のステップ

リソースの移動のコマンドについては、「[新しいリソース グループまたはサブスクリプションへのリソースの移動](move-resource-group-and-subscription.md)」を参照してください。
