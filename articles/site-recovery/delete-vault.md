---
title: Azure Site Recovery サービス用に構成された Recovery Services コンテナーを削除する
description: Azure Site Recovery 用に構成された Recovery Services コンテナーを削除する方法について説明します
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: a13dee2010688b02fd86fb05900826470a7d7a08
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67876040"
---
# <a name="delete-a-site-recovery-services-vault"></a>Site Recovery Services コンテナーを削除する

依存関係により、Azure Site Recovery コンテナーが削除できない場合があります。 実行する必要のあるアクションは、サイトの回復シナリオによって異なります。 Azure Backup で使用されているコンテナーを削除するには、「[Azure のバックアップ コンテナーを削除する](../backup/backup-azure-delete-vault.md)」を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="delete-a-site-recovery-vault"></a>Site Recovery コンテナーを削除する 
コンテナーを削除するには、シナリオに応じた推奨手順に従ってください。
### <a name="azure-vms-to-azure"></a>Azure VM を Azureに

1. 保護されている VM をすべて削除するには、「[VMware VM の保護の無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-azure-vm-azure-to-azure)」の手順に従います。
2. コンテナーを削除します。

### <a name="vmware-vms-to-azure"></a>VMware VM を Azureに

1. 保護されている VM をすべて削除するには、「[VMware VM の保護の無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)」の手順に従います。

2. レプリケーション ポリシーをすべて削除するには、「[レプリケーション ポリシーを削除する](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)」の手順に従います。

3. vCenter への関連付けを削除するには、[vCenter Server の削除に関するページ](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)の手順に従います。

4. 構成サーバーを削除するには、「[Decommission a configuration server](vmware-azure-manage-configuration-server.md#delete-or-unregister-a-configuration-server)」 (構成サーバーを使用停止にする) の手順に従います。

5. コンテナーを削除します。


### <a name="hyper-v-vms-with-vmm-to-azure"></a>Hyper-V VM (VMM を使用) を Azure に
1. 保護されている VM をすべて削除するには、[Hyper-V VM (VMM 使用) の保護の無効化](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-replicating-to-azure-using-the-system-center-vmm-to-azure-scenario)に関する記事の手順に従います。

2. コンテナー -> **[Site Recovery Infrastracture]\(Site Recovery インフラストラクチャ\)** - > **[For System Center VMM]\(System Center VMM\)**  ->  **[レプリケーション ポリシー]** にアクセスして、すべてのレプリケーション ポリシーを関連付け解除し、削除します。

3.  VMM サーバーへの関連付けを削除するには、「[接続されている VMM サーバーの登録解除](site-recovery-manage-registration-and-protection.md##unregister-a-vmm-server)」の手順に従います。

4.  コンテナーを削除します。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM (Virtual Machine Manager なし) から Azure へ
1. [Hyper-V 仮想マシンの保護の無効化 (Hyper-V から Azure へ)](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-hyper-v-virtual-machine-hyper-v-to-azure) に関するページの手順に従って、保護されている VM をすべて削除します。

2. コンテナー -> **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)** - > **[For Hyper-V Sites]\(Hyper-V サイト\)**  ->  **[レプリケーション ポリシー]** にアクセスして、すべてのレプリケーション ポリシーを関連付け解除し、削除します。

3. HYPER-V サーバーへの関連付けを削除するには、「[HYPER-V ホストの登録を解除](site-recovery-manage-registration-and-protection.md#unregister-a-hyper-v-host-in-a-hyper-v-site)」の手順に従います。

4. Hyper-V サイトを削除します。

5. コンテナーを削除します。


## <a name="use-powershell-to-force-delete-the-vault"></a>コンテナーを強制削除するために PowerShell を使用する 

> [!Important]
> 製品をテストしていて、データ損失を心配する必要はない場合は、コンテナーとそのすべての依存関係を強制削除の方法で削除することができます。
> この PowerShell コマンドでは、コンテナーのすべてのコンテンツが削除され、**元に戻すことはできません**。

保護された項目がある場合でも、Site Recovery コンテナーを削除するには、次のコマンドを使用します。

    Connect-AzAccount

    Select-AzSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzRecoveryServicesVault -Name "vaultname"

    Remove-AzRecoveryServicesVault -Vault $vault

[Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault) と [Remove-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) の詳細をご覧ください。
