---
title: Azure Site Recovery コンテナーを削除する
description: Azure Site Recovery 用に構成された Recovery Services コンテナーを削除する方法について説明します
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/05/2019
ms.author: sideeksh
ms.openlocfilehash: a33e04a24013d5450c98b91048fa418958d16886
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426386"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services コンテナーを削除する

この記事では、Site Recovery の Recovery Services コンテナーを削除する方法について説明します。 Azure Backup で使用されているコンテナーを削除するには、「[Azure のバックアップ コンテナーを削除する](../backup/backup-azure-delete-vault.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="before-you-start"></a>開始する前に

コンテナーを削除する前に、登録済みのサーバーとコンテナー内の項目を削除する必要があります。 削除する必要があるものは、導入したレプリケーション シナリオによって異なります。 


## <a name="delete-a-vault-azure-vm-to-azure"></a>コンテナーの削除 - Azure VM から Azure

1. [これらの手順](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)に従って、保護されている VM をすべて削除します。
2. その後、コンテナーを削除します。

## <a name="delete-a-vault-vmware-vm-to-azure"></a>コンテナーの削除 - VMware VM から Azure

1. [これらの手順](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)に従って、保護されている VM をすべて削除します。
2. [これらの手順](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)に従って、すべてのレプリケーション ポリシーを削除します。
3. [これらの手順](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)を使用して、vCenter への参照を削除します。
4. [これらの手順](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)に従って、構成サーバーの使用を停止します。
5. その後、コンテナーを削除します。


## <a name="delete-a-vault-hyper-v-vm-with-vmm-to-azure"></a>コンテナーの削除 - Hyper-V VM (VMM 使用) から Azure

1. [これらの手順](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)に従って、System Center VMM によって管理されている Hyper-V VM を削除します。
2. すべてのレプリケーション ポリシーの関連付けを解除し、レプリケーション ポリシーを削除します。 これは、ご使用のコンテナー > **[Site Recovery インフラストラクチャ]**  >  **[For System Center VMM]\(System Center VMM\)**  >  **[レプリケーション ポリシー]** で実行します。
3. [これらの手順](site-recovery-manage-registration-and-protection.md#unregister-a-vmm-server)に従って、接続されている VMM サーバーの登録を解除します。
4. その後、コンテナーを削除します。

## <a name="delete-a-vault-hyper-v-vm-to-azure"></a>コンテナーの削除 - Hyper-V VM から Azure

1. [こちらの手順](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure)に従って、保護されている VM をすべて削除します。
2. すべてのレプリケーション ポリシーの関連付けを解除し、レプリケーション ポリシーを削除します。 これは、ご使用のコンテナー > **[Site Recovery インフラストラクチャ]**  >  **[For Hyper-V Sites]\(Hyper-V サイト\)**  >  **[レプリケーション ポリシー]** で実行します。
3. [これらの手順](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)に従って、Hyper-V ホストの登録を解除し ます。
4. Hyper-V サイトを削除します。
5. その後、コンテナーを削除します。


## <a name="use-powershell-to-force-delete-the-vault"></a>コンテナーを強制削除するために PowerShell を使用する 

> [!Important]
> 製品をテストしていて、データ損失を心配する必要はない場合は、コンテナーとそのすべての依存関係を強制削除の方法で削除することができます。
> この PowerShell コマンドでは、コンテナーのすべてのコンテンツが削除され、**元に戻すことはできません**。

保護された項目がある場合でも、Site Recovery コンテナーを削除するには、次のコマンドを使用します。

```azurepowershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName "XXXXX"

$vault = Get-AzRecoveryServicesVault -Name "vaultname"

Remove-AzRecoveryServicesVault -Vault $vault
```

[Get-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/get-azrecoveryservicesvault) と [Remove-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) の詳細をご覧ください。
