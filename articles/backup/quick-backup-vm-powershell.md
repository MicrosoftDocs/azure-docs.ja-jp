---
title: Azure クイック スタート - PowerShell で VM をバックアップする
description: Azure PowerShell を使用して仮想マシンをバックアップする方法
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 04/16/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3766b3b7f9dbab23673498eefd3f335b8e7f6c16
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467154"
---
# <a name="back-up-a-virtual-machine-in-azure-with-powershell"></a>Azure PowerShell を使用した Azure の仮想マシンのバックアップ

[Azure PowerShell AZ](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-1.4.0) モジュールは、コマンド ラインやスクリプトから Azure リソースを作成および管理するために使用します。 

[Azure Backup](backup-overview.md) では、オンプレミスのマシンとアプリ、および Azure VM をバックアップします。 この記事では、AZ モジュールを使用して Azure VM をバックアップする方法を説明します。 代わりに [Azure CLI](quick-backup-vm-cli.md) または [Azure portal](quick-backup-vm-portal.md) を使用して VM をバックアップすることもできます。

このクイック スタートでは、既存の Azure VM のバックアップを実行できます。 VM を作成する必要がある場合は、[Azure PowerShell を使用して VM を作成](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)できます。

このクイック スタートには、Azure PowerShell AZ モジュール バージョン 1.0.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-and-register"></a>サインインして登録する

1. `Connect-AzAccount` コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

    ```powershell
    Connect-AzAccount
    ```
2. Azure Backup を最初に使用するときは、次のように、[Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は、Azure VM などの保護されたリソースのバックアップ データを格納する論理コンテナーです。 バックアップ ジョブを実行すると、Recovery Services コンテナー内に復旧ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

コンテナーを作成する場合:

- リソース グループと場所については、バックアップする VM のリソース グループと場所を指定します。
- この[サンプル スクリプト](../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json)を使用して VM を作成した場合、リソース グループは **myResourceGroup**、VM は **myVM** になり、リソースは **WestEurope** リージョンに格納されます。
- Azure Backup では、バックアップされるデータのストレージを自動的に処理します。 既定では、コンテナーには [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy-grs.md) が使用されます。 geo 冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンに、バックアップされたデータが確実にレプリケートされます。

それでは、コンテナーを作成します。


1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) を使用して、コンテナーを作成します。

    ```powershell
    New-AzRecoveryServicesVault `
        -ResourceGroupName "myResourceGroup" `
        -Name "myRecoveryServicesVault" `
    -Location "WestEurope"
    ```

2. 次のように、[Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) を使用して、コンテナーのコンテキストを設定します。

    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesVaultContext
    ```

3. [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty)を使用して、コンテナーの記憶域冗長構成 (LRS/GRS) を次のように変更します。
    
    ```powershell
    Get-AzRecoveryServicesVault `
        -Name "myRecoveryServicesVault" | Set-AzRecoveryServicesBackupProperty -BackupStorageRedundancy LocallyRedundant/GeoRedundant
    ```
    > [!NOTE]
    > 記憶域冗長は、コンテナーに保護されているバックアップ項目がない場合にのみ変更できます。

## <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする

Azure VM のバックアップを有効にし、バックアップ ポリシーを指定します。

- ポリシーでは、バックアップを実行するタイミングと、バックアップによって作成された復旧ポイントを保持する期間を定義します。
- 既定の保護ポリシーでは、1 日 1 回 VM のバックアップが実行され、作成された復旧ポイントは 30 日間保持されます。 この既定のポリシーを使用して、VM をすぐに保護できます。 

次のようにバックアップを有効にします。

1. まず、[Get-AzRecoveryServicesBackupProtectionPolicy](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) を使用して、既定のポリシーを設定します。

    ```powershell
    $policy = Get-AzRecoveryServicesBackupProtectionPolicy     -Name "DefaultPolicy"
    ```

2. [Enable-AzRecoveryServicesBackupProtection](/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) を使用して、VM のバックアップを有効にします。 ポリシー、リソース グループ、および VM の名前を指定します。

    ```powershell
    Enable-AzRecoveryServicesBackupProtection `
        -ResourceGroupName "myResourceGroup" `
        -Name "myVM" `
        -Policy $policy
    ```


## <a name="start-a-backup-job"></a>バックアップ ジョブを開始する

バックアップ ポリシーに指定されているスケジュールに従ってバックアップが実行されます。 アドホック バックアップを実行することもできます。

- 最初の初回バックアップ ジョブによって、完全な復旧ポイントが作成されます。
- 初回バックアップ後の各バックアップ ジョブでは、増分復旧ポイントが作成されます。
- 増分復元ポイントでは、前回のバックアップ以降に行われた変更のみが転送されるため、保存効率と時間効率が向上します。

アドホック バックアップを実行するには、[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) を使用します。 
- [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) を使用して、バックアップ データを保持するコンテナー内のコンテナーを指定します。
- バックアップする各 VM は、項目として扱われます。 バックアップ ジョブを開始するには、[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) を使用して、VM に関する情報を取得します。

次のように、アドホック バックアップ ジョブを実行します。

1. コンテナーを指定し、VM 情報を取得して、バックアップを実行します。

    ```powershell
    $backupcontainer = Get-AzRecoveryServicesBackupContainer `
        -ContainerType "AzureVM" `
        -FriendlyName "myVM"

    $item = Get-AzRecoveryServicesBackupItem `
        -Container $backupcontainer `
        -WorkloadType "AzureVM"

    Backup-AzRecoveryServicesBackupItem -Item $item
    ```

2. 最初のバックアップ ジョブでは完全な復旧ポイントが作成されるため、最大 20 分かかる場合があります。 次の手順の説明に従って、ジョブを監視します。


## <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する

1. [Get-AzRecoveryservicesBackupJob](/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) を実行して、ジョブの状態を監視します。

    ```powershell
    Get-AzRecoveryservicesBackupJob
    ```
    出力は次の例のようになります。ジョブが **InProgress** であることが示されています。

    ```
    WorkloadName   Operation         Status       StartTime              EndTime                JobID
    ------------   ---------         ------       ---------              -------                -----
    myvm           Backup            InProgress   9/18/2017 9:38:02 PM                          9f9e8f14
    myvm           ConfigureBackup   Completed    9/18/2017 9:33:18 PM   9/18/2017 9:33:51 PM   fe79c739
    ```

2. ジョブの状態が **Completed** の場合、VM が保護されていて、完全な復旧ポイントが格納されています。


## <a name="clean-up-the-deployment"></a>デプロイをクリーンアップする

VM をバックアップする必要がなくなった場合は、クリーンアップできます。
- VM の復元を試す場合は、クリーンアップをスキップします。
- 既存の VM を使用した場合は、最後の [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドレットを省略すると、リソース グループと VM をそのままの状態にしておくことができます。

保護を無効にし、復元ポイントとコンテナーを削除します。 その後、リソース グループおよび関連付けられているVM リソースを次のように削除します。

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```


## <a name="next-steps"></a>次の手順

このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。 

- Azure portal 内で VM をバックアップする[方法の詳細](tutorial-backup-vm-at-scale.md)
- VM をすばやく復元する[方法の詳細](tutorial-restore-disk.md)
