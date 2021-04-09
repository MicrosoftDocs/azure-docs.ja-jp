---
title: Azure サービスの推奨ポリシー
description: Azure Virtual Machines などの Azure サービスの推奨ポリシーを検索して適用する方法について説明します。
ms.date: 09/02/2020
ms.topic: conceptual
ms.customer: generated
ms.openlocfilehash: 019541f034ce8f0c3728c38d2ae4425308b4e2a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350281"
---
# <a name="recommended-policies-for-azure-services"></a>Azure サービスの推奨ポリシー

Azure Policy を初めて利用するお客様は、多くの場合、リソースを管理および制御するための一般的なポリシー定義を探そうとします。 Azure Policy の **推奨ポリシー** では、最初に使用すべき一般的なポリシー定義に特化した一覧が示されています。 サポートされているリソースの **推奨ポリシー** エクスペリエンスは、そのリソースのポータル エクスペリエンス内に埋め込まれています。

その他の Azure Policy の組み込みについては、[Azure Policy 組み込み定義](../samples/built-in-policies.md)に関するページを参照してください。

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

[Azure Virtual Machines](../../../virtual-machines/index.yml) に対する **推奨ポリシー** は、仮想マシンの **[概要]** ページと、 **[機能]** タブにあります。_Azure Policy_ カードで、[未構成] または [# assigned]\(# 割り当て済み\) のテキストを選択して、推奨ポリシーを含むサイド ウィンドウを開きます。 仮想マシンがメンバーとなっているスコープに既に割り当てられているポリシー定義がグレーで表示されます。この仮想マシンに適用する推奨ポリシーを選択し、 **[ポリシーの割り当て]** を選択してそれぞれの割り当てを作成します。

組織が[リソースとリソース階層を整理する](/azure/cloud-adoption-framework/ready/azure-best-practices/organize-subscriptions)成熟度に達したときは、これらのポリシーの割り当てをリソースごとに 1 つから、サブスクリプションまたは[管理グループ](../../management-groups/index.yml) レベルに移行することをお勧めします。

### <a name="azure-virtual-machines-recommended-policies"></a>Azure Virtual Machines 推奨ポリシー

|名前<br /><sub>(Azure portal)</sub> |説明 |効果 |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[ディザスター リカバリーを構成されていない仮想マシンの監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0015ea4d-51ff-4ce3-8d8c-f3f8f0179a56) |ディザスター リカバリーが構成されていない仮想マシンを監査します。 ディザスター リカバリーの詳細については、[https://aka.ms/asr-doc](../../../site-recovery/index.yml) にアクセスしてください。 |auditIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |
|[Managed Disks を使用していない VM の監査](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F06a78e20-9358-41c9-923c-fb736d382a4d) |このポリシーは、マネージド ディスクを使用していない VM を監査します |監査 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |
|[仮想マシンに対して Azure Backup を有効にする必要がある](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |このポリシーは、すべての仮想マシンで Azure Backup サービスが有効になっているかどうかを監査するのに役立ちます。 Azure Backup は、コスト効果の高いワンクリック バックアップ ソリューションで、データの回復を簡略化し、他のクラウド バックアップ サービスよりも簡単に有効にできます。 |AuditIfNotExists、Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Policy の効果について](./effects.md)」を確認します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。