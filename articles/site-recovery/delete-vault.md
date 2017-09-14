---
title: "Site Recovery コンテナーを削除する"
description: "サイトの回復シナリオに基づいて、Azure Site Recovery コンテナーを削除する方法を説明します。"
service: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/04/2017
ms.author: rajani-janaki-ram
ms.translationtype: HT
ms.sourcegitcommit: 8021f8641ff3f009104082093143ec8eb087279e
ms.openlocfilehash: b95b9defa0a037f7d7d3ef36b99bc7c53c751050
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="delete-a-site-recovery-vault"></a>Site Recovery コンテナーを削除する
依存関係により、Azure Site Recovery コンテナーが削除できない場合があります。 実行する必要のあるアクションは、サイトの回復シナリオ (VMware から Azure、Hyper-v (System Center Virtual Machine Manager あり/なし) から Azure と Azure Backup) によって異なります。 Azure Backup で使用されているコンテナーを削除するには、「[Azure のバックアップ コンテナーを削除する](../backup/backup-azure-delete-vault.md)」を参照してください。

>[!Important]
>製品をテストしていて、データ損失を心配する必要はない場合は、コンテナーとそのすべての依存関係を強制削除の方法で削除することができます。

> この PowerShell コマンドでは、コンテナーのすべてのコンテンツが削除され、元に戻すことはできません。

## <a name="use-powershell-to-force-delete-the-vault"></a>コンテナーを強制削除するために PowerShell を使用する 

保護された項目がある場合でも、Site Recovery コンテナーを削除するには、次のコマンドを使用します。

    Login-AzureRmAccount

    Select-AzureRmSubscription -SubscriptionName "XXXXX"

    $vault = Get-AzureRmSiteRecoveryVault -Name "vaultname"

    Remove-AzureRmSiteRecoveryVault -Vault $vault


## <a name="delete-a-site-recovery-vault"></a>Site Recovery コンテナーを削除する 
コンテナーを削除するには、シナリオに応じた推奨手順に従ってください。

### <a name="vmware-vms-to-azure"></a>VMware VM を Azureに

1. 保護されている VM をすべて削除するには、「[VMware VM の保護の無効化](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)」の手順に従います。

2. レプリケーション ポリシーをすべて削除するには、「[レプリケーション ポリシーを削除する](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)」の手順に従います。

3. vCenter への関連付けを削除するには、「[vCenter の削除](site-recovery-vmware-to-azure-manage-vCenter.md##delete-a-vcenter-in-azure-site-recovery)」の手順に従います。

4. 構成サーバーを削除するには、「[Decommission a configuration server](site-recovery-vmware-to-azure-manage-configuration-server.md##decommissioning-a-configuration-server)」 (構成サーバーを使用停止にする) の手順に従います。

5. コンテナーを削除します。


### <a name="hyper-v-vms-with-virtual-machine-manager-to-azure"></a>Hyper-V VM (Virtual Machine Manager あり) から Azure へ
1. 保護されている VM をすべて削除するには、「[VMware VM または物理サーバーの保護の無効化](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)」の手順に従います。

2. レプリケーション ポリシーをすべて削除するには、「[レプリケーション ポリシーを削除する](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)」の手順に従います。

3.  Virtual Machine Manager サーバーへの関連付けを削除するには、「[接続されている VMM サーバーの登録解除](site-recovery-manage-registration-and-protection.md##unregister-a-connected-vmm-server)」の手順に従います。

4.  コンテナーを削除します。

### <a name="hyper-v-vms-without-virtual-machine-manager-to-azure"></a>Hyper-V VM (Virtual Machine Manager なし) から Azure へ
1. 保護されている VM をすべて削除するには、「[VMware VM または物理サーバーの保護の無効化](site-recovery-manage-registration-and-protection.md##disable-protection-for-a-vmware-vm-or-physical-server)」の手順に従います。

2. レプリケーション ポリシーをすべて削除するには、「[レプリケーション ポリシーを削除する](site-recovery-setup-replication-settings-vmware.md##delete-a-replication-policy)」の手順に従います。

3. HYPER-V サーバーへの関連付けを削除するには、「[HYPER-V ホストの登録を解除](/site-recovery-manage-registration-and-protection.md##unregister-a-hyper-v-host-in-a-hyper-v-site)」の手順に従います。

4. Hyper-V サイトを削除します。

5. コンテナーを削除します。

