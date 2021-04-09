---
title: Key Vault と Azure の Windows VM 上の SQL Server との統合 (Resource Manager) | Microsoft Docs
description: Azure Key Vault で使用するために SQL Server 暗号化の構成を自動化する方法について説明します。 このトピックでは、リソース マネージャーで作成される SQL 仮想マシンで Azure Key Vault 統合を使用する方法について説明します。
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97359899"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Azure VM で SQL Server 用に Azure Key Vault 統合を構成する (リソース マネージャー)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[透過的なデータ暗号化 (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption)、[列レベルの暗号化 (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)、[バックアップ暗号化](/sql/relational-databases/backup-restore/backup-encryption) など、SQL Server 暗号化機能が複数存在します。 これらの形態の暗号化では、暗号化に利用する暗号鍵を管理し、保存する必要があります。 Azure Key Vault サービスは、セキュリティを強化し、安全かつ可用性の高い場所で鍵を管理できるように設計されています。 [SQL Server コネクタ](https://www.microsoft.com/download/details.aspx?id=45344) を利用すると、SQL Server は Azure Key Vault にある鍵を利用できます。

SQL Server をオンプレミスで実行している場合、いくつかの手順を踏んで[オンプレミスの SQL Server インスタンスから Azure Key Vault にアクセス](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)できます。 ただし、Azure VM の SQL Server の場合、*Azure Key Vault の統合* 機能を利用することで時間を節約できます。

この機能が有効になっていると、SQL Server コネクタが自動的にインストールされ、Azure Key Vault にアクセスするように EKM プロバイダーが構成され、Vault へのアクセスを許可する資格情報が作成されます。 前述のオンプレミス文書の手順を見れば、この機能で手順 2 と 3 が自動化されることがわかります。 手動でしなければならないことは、Key Vault と鍵を作成することだけです。 そこから先は、SQL Server VM の設定全体が自動化されます。 この機能でこの設定が完了したら、Transact-SQL (T-SQL) ステートメントを実行し、通常どおり、データベースやバックアップの暗号化を開始できます。

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > 拡張キー管理 (EKM) プロバイダー バージョン 1.0.4.0 は、[SQL IaaS (サービスとしてのインフラストラクチャ) 拡張機能](./sql-server-iaas-agent-extension-automate-management.md)を通じて SQL Server VM にインストールされます。 SQL IaaS 拡張機能をアップグレードしても、プロバイダーのバージョンは更新されません。 必要に応じて、EKM プロバイダーのバージョンの手動アップグレードを検討してください (たとえば、SQL マネージド インスタンスへの移行時)。


## <a name="enabling-and-configuring-key-vault-integration"></a>Key Vault 統合の有効化と構成
Key Vault 統合はプロビジョニング時に有効にできます。または、既存の VM 用に構成できます。

### <a name="new-vms"></a>新しい VM
リソース マネージャーで新しい SQL 仮想マシンをプロビジョニングしている場合は、Azure Portal で Azure Key Vault 統合を有効にすることができます。 Azure Key Vault 機能は、SQL Server の Enterprise Edition、Developer Edition、および評価版でのみ使用できます。

![SQL Azure Key Vault Integration](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

プロビジョニングの詳細なチュートリアルについては、[Azure portal での SQL 仮想マシンのプロビジョニング](create-sql-vm-portal.md)に関するページをご覧ください。

### <a name="existing-vms"></a>既存の VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL 仮想マシンの場合、[[SQL 仮想マシン リソース]](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) を開き、 **[設定]** の **[セキュリティ]** を選択します。 **[有効にする]** を選択し、Azure Key Vault の統合を有効にします。 

![既存の VM 用の SQL Key Vault 統合](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

完了したら、 **[セキュリティ]** ページの下にある **[適用]** ボタンを選択して変更内容を保存します。

> [!NOTE]
> ここで作成した資格情報名は、後で SQL ログインにマップされます。 これにより、SQL ログインで Key Vault にアクセスできるようになります。 


> [!NOTE]
> テンプレートを使用して Key Vault 統合を構成することもできます。 詳細については、 [Azure Key Vault 統合用の Azure クイックスタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update)に関するページをご覧ください。


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]