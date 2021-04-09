---
title: Azure Resource Manager のテンプレート
description: Recovery Services コンテナーと Azure Backup 機能で使用するための Azure Resource Manager テンプレート
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: a4c2f444cb821f7979571b9d777895a59450e7c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96309581"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Backup 用 Azure Resource Manager テンプレート

次の表に、Recovery Services コンテナーおよび Azure Backup 機能で使用するための Azure Resource Manager テンプレートへのリンクを示します。 JSON の構文とプロパティについては、「[Microsoft.RecoveryServices resource types (Microsoft.RecoveryServices のリソースの種類)](/azure/templates/microsoft.recoveryservices/allversions)」を参照してください。

| Template | 説明 |
|---|---|
|**Recovery Services コンテナー** | |
| [Recovery Services コンテナーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Recovery Services コンテナーを作成する。 コンテナーは、Azure Backup および Azure Site Recovery に使用できます。 |
|**仮想マシンのバックアップ**| |
| [Resource Manager VM のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | 既存の Recovery Services コンテナーとバックアップ ポリシーを使用して、同じリソース グループ内の Resource Manager 仮想マシンをバックアップします。|
| [Recovery Services コンテナーへの IaaS VM のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | クラシックおよび Resource Manager 仮想マシンをバックアップするためのテンプレート。 |
| [IaaS VM の週次バックアップ ポリシーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | このテンプレートを使用して、Recovery Services コンテナーと、クラシックおよび Resource Manager 仮想マシンをバックアップするための週次バックアップ ポリシーを作成します。|
| [IaaS VM の日次バックアップ ポリシーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | このテンプレートを使用して、Recovery Services コンテナーと、クラシックおよび Resource Manager 仮想マシンをバックアップするための日次バックアップ ポリシーを作成します。|
| [バックアップが有効な Windows Server VM のデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | このテンプレートを使用して、Windows Server VM と、既定のバックアップ ポリシーが有効な Recovery Services コンテナーを作成します。|
|**バックアップ ジョブの監視** |  |
| [Azure Backup での Azure Monitor ログの使用](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | このテンプレートを使用して、Azure Backup での Azure Monitor ログをデプロイします。これにより、Recovery Services コンテナーで使用されているバックアップおよび復元ジョブ、バックアップ アラート、およびクラウド ストレージを監視できます。|  
|**Azure VM の SQL Server のバックアップ** |  |
| [Azure VM の SQL Server のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | このテンプレートを使用して、Recovery Services コンテナーとワークロード固有のバックアップ ポリシーを作成します。 これにより、Azure Backup サービスに VM を登録し、その VM の保護を構成できます。 現在、これは SQL ギャラリー イメージでのみ機能します。 |
|**Azure ファイル共有のバックアップ** |  |
| [Azure ファイル共有のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-file-share) | このテンプレートでは、Recovery Services コンテナーとバックアップ ポリシーの適切な詳細を指定することによって、既存の Azure ファイル共有の保護を構成できます。 必要に応じて、新しい Recovery Services コンテナーとバックアップ ポリシーが作成され、ファイル共有を含むストレージ アカウントが Recovery Services コンテナーに登録されます。 |
|   |   |
