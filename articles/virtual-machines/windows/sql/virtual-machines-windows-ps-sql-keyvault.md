---
title: Key Vault と Azure の Windows VM 上の SQL Server との統合 (Resource Manager) | Microsoft Docs
description: Azure Key Vault で使用するために SQL Server 暗号化の構成を自動化する方法について説明します。 このトピックでは、リソース マネージャーで作成される SQL Server 仮想マシンで Azure Key Vault 統合を使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ad8eea21c10726b2c3eaf1e10bfd5efba4d1e48
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358696"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Azure Virtual Machines 上の SQL Server 向け Azure Key Vault 統合の構成 (Resource Manager)

> [!div class="op_single_selector"]
> * [リソース マネージャー](virtual-machines-windows-ps-sql-keyvault.md)
> * [クラシック](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>概要
[透過的なデータ暗号化 (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)、[列レベルの暗号化 (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)、[バックアップ暗号化](https://msdn.microsoft.com/library/dn449489.aspx) など、SQL Server 暗号化機能が複数存在します。 これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。 Azure Key Vault (AKV) サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。 [SQL Server コネクタ](https://www.microsoft.com/download/details.aspx?id=45344) を利用すると、SQL Server は Azure Key Vault にある鍵を利用できます。

SQL Server をオンプレミス コンピューターで実行している場合、 [いくつかの手順を踏んでオンプレミスの SQL Server コンピューターから Azure Key Vault にアクセスできます](https://msdn.microsoft.com/library/dn198405.aspx)。 ただし、Azure VM の SQL Server の場合、 *Azure Key Vault 統合* 機能を利用し、時間を節約できます。

この機能が有効になっていると、SQL Server コネクタが自動的にインストールされ、Azure Key Vault にアクセスするように EKM プロバイダーが構成され、Vault へのアクセスを許可する資格情報が作成されます。 前述のオンプレミス文書の手順を見れば、この機能で手順 2 と 3 が自動化されることがわかります。 手動でしなければならないことは、Key Vault と鍵を作成することだけです。 そこから先は、SQL VM の設定全体が自動化されます。 この機能でこの設定が完了したら、T-SQL ステートメントを実行し、通常どおり、データベースやバックアップの暗号化を開始できます。

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > EKM プロバイダー バージョン 1.0.4.0 は、[SQL IaaS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)から SQL Server VM にインストールされます。 SQL IaaS 拡張機能をアップグレードしても、プロバイダーのバージョンは更新されません。 必要に応じて、EKM プロバイダーのバージョンの手動アップグレードを検討してください (たとえば、SQL マネージド インスタンスへの移行時)。


## <a name="enabling-and-configuring-akv-integration"></a>AKV 統合の有効化と構成
AKV 統合はプロビジョニング時に有効にできます。または、既存の VM 用に構成できます。

### <a name="new-vms"></a>新しい VM
リソース マネージャーで新しい SQL Server 仮想マシンをプロビジョニングしている場合は、Azure Portal で Azure Key Vault 統合を有効にすることができます。 Azure Key Vault 機能は、SQL Server の Enterprise Edition、Developer Edition、および評価版でのみ使用できます。

![SQL Azure Key Vault Integration](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

プロビジョニングの詳細なチュートリアルについては、「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」をご覧ください。

### <a name="existing-vms"></a>既存の VM
既存の SQL Server 仮想マシンの場合は、ご使用の SQL Server 仮想マシンを選択します。 **[設定]** ブレードの **[SQL Server の構成]** セクションを選択します。

![既存の VM 用の SQL AKV 統合](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

**[SQL Server の構成]** ブレードの [Automated Key Vault integration (Key Vault の自動統合)] セクションで **[編集]** ボタンをクリックします。

![既存の VM 用の SQL AKV 統合を構成する](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

終了したら、**[SQL Server の構成]** ブレードの下部にある **[OK]** ボタンをクリックして変更を保存します。

> [!NOTE]
> ここで作成した資格情報名は、後で SQL ログインにマップされます。 これにより、SQL ログインで Key Vault にアクセスできるようになります。 
>
>

> [!NOTE]
> テンプレートを使用して AKV 統合を構成することもできます。 詳細については、 [Azure Key Vault 統合用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)に関するページをご覧ください。
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

