---
title: Azure Backup 用 Azure Resource Manager テンプレート
description: Azure Backup PowerShell のサンプル
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c0b023b6bdd01bc64eb5a6deb5fafa8e3c34c616
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56819022"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Backup 用 Azure Resource Manager テンプレート

次の表に、Recovery Services コンテナーおよび Azure Backup 機能で使用するための Azure Resource Manager テンプレートへのリンクを示します。 JSON の構文とプロパティについては、「[Microsoft.RecoveryServices resource types (Microsoft.RecoveryServices のリソースの種類)](/azure/templates/microsoft.recoveryservices/allversions)」を参照してください。

|   |   |
|---|---|
|**Recovery Services コンテナー** | |
| [Recovery Services コンテナーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Recovery Services コンテナーを作成します。 コンテナーは、Azure Backup および Azure Site Recovery に使用できます。 |
|**仮想マシンのバックアップ**| |
| [Resource Manager VM のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | 既存の Recovery Services コンテナーとバックアップ ポリシーを使用して、同じリソース グループ内の Resource Manager 仮想マシンをバックアップします。|
| [Recovery Services コンテナーへの IaaS VM のバックアップ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | クラシックおよび Resource Manager 仮想マシンをバックアップするためのテンプレート。 |
| [IaaS VM の週次バックアップ ポリシーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | このテンプレートを使用して、Recovery Services コンテナーと、クラシックおよび Resource Manager 仮想マシンをバックアップするための週次バックアップ ポリシーを作成します。|
| [IaaS VM の日次バックアップ ポリシーの作成](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | このテンプレートを使用して、Recovery Services コンテナーと、クラシックおよび Resource Manager 仮想マシンをバックアップするための日次バックアップ ポリシーを作成します。|
| [バックアップが有効な Windows Server VM のデプロイ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | このテンプレートを使用して、Windows Server VM と、既定のバックアップ ポリシーが有効な Recovery Services コンテナーを作成します。|
|**バックアップ ジョブの監視** |  |
| [Azure Backup での Azure Monitor ログの使用](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | このテンプレートを使用して、Azure Backup での Azure Monitor ログをデプロイします。これにより、Recovery Services コンテナーで使用されているバックアップおよび復元ジョブ、バックアップ アラート、およびクラウド ストレージを監視できます。|  
|   |   |

