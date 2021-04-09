---
title: チュートリアル:PowerShell を使用して複数の Azure VM をバックアップする
description: このチュートリアルでは、Azure PowerShell を使用して、複数の Azure VM を Recovery Services コンテナーにバックアップする方法について詳しく説明します。
ms.topic: tutorial
ms.date: 03/05/2019
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 53a5df430a3ea57201ecb19bac68ef9f073beb14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980959"
---
# <a name="back-up-azure-vms-with-powershell"></a>PowerShell を使用して Azure VM をバックアップする

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルでは、[Azure Backup](backup-overview.md) Recovery Services コンテナーをデプロイし、PowerShell を使用して複数の Azure VM をバックアップする方法について説明します。  

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * Recovery Services のコンテナーを作成し、コンテナーのコンテキストを設定する。
> * バックアップ ポリシーを定義する
> * 複数の仮想マシンを保護するバックアップ ポリシーを適用する
> * 保護された仮想マシン用のオンデマンドのバックアップ ジョブをトリガーする 仮想マシンをバックアップ (または保護) する前に、[前提条件](backup-azure-arm-vms-prepare.md)をすべて満たして、VM を保護するための環境を準備する必要があります。

> [!IMPORTANT]
> このチュートリアルでは、リソース グループと Azure 仮想マシンを既に作成していることを前提としています。

## <a name="sign-in-and-register"></a>サインインして登録する

1. `Connect-AzAccount` コマンドを使用して Azure サブスクリプションにサインインし、画面上の指示に従います。

    ```powershell
    Connect-AzAccount
    ```

2. Azure Backup を最初に使用するときは、[Register-AzResourceProvider](/powershell/module/az.Resources/Register-azResourceProvider) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。 既に登録してある場合は、この手順をスキップします。

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

[Recovery Services コンテナー](backup-azure-recovery-services-vault-overview.md)は、Azure VM などの保護されたリソースのバックアップ データを格納する論理コンテナーです。 バックアップ ジョブを実行すると、Recovery Services コンテナー内に復旧ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

* このチュートリアルでは、バックアップする VM と同じリソース グループと場所にコンテナーを作成します。
* Azure Backup では、バックアップされるデータのストレージを自動的に処理します。 既定では、コンテナーには [geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) が使用されます。 geo 冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンに、バックアップされたデータが確実にレプリケートされます。

次のようにコンテナーを作成します。

1. [New-AzRecoveryServicesVault](/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) を使用して、コンテナーを作成します。 バックアップする VM の場所とリソース グループ名を指定します。

    ```powershell
    New-AzRecoveryServicesVault -Name myRSvault -ResourceGroupName "myResourceGroup" -Location "EastUS"
    ```

2. Azure Backup コマンドレットの多くは、入力として Recovery Services コンテナー オブジェクトを必要としています。 このため、Backup Recovery Services コンテナー オブジェクトを変数に格納すると便利です。

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault –Name myRSVault
    ```

3. [Set-AzRecoveryServicesVaultContext](/powershell/module/az.RecoveryServices/Set-azRecoveryServicesVaultContext) を使用して、コンテナーのコンテキストを設定します。

   * コンテナーのコンテキストとは、コンテナーで保護されるデータの種類です。
   * コンテキストが設定されたら、後続のすべてのコマンドレットに適用されます

     ```powershell
     Get-AzRecoveryServicesVault -Name "myRSVault" | Set-AzRecoveryServicesVaultContext
     ```

## <a name="back-up-azure-vms"></a>Azure VM のバックアップ

バックアップ ポリシーに指定されているスケジュールに従って、バックアップが実行されます。 Recovery Services コンテナーの作成時に、既定の保護およびアイテム保持ポリシーも付属しています。

* 既定の保護ポリシーによって、1 日 1 回、指定された時刻にバックアップ ジョブがトリガーされます。
* 既定のアイテム保持ポリシーは、毎日の復旧ポイントを 30 日間保持します。

このチュートリアルで Azure VM を有効にしてバックアップするには、以下のようにします。

1. [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer) を使用して、バックアップ データを保持するコンテナー内のコンテナーを指定します。
2. バックアップする各 VM は項目です。 バックアップ ジョブを開始するには、[Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem) を使用して、VM に関する情報を取得します。
3. [Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem) を使用して、オンデマンド バックアップを実行します。
    * 最初の初回バックアップ ジョブによって、完全な復旧ポイントが作成されます。
    * 初回バックアップ後の各バックアップ ジョブでは、増分復旧ポイントが作成されます。
    * 増分復元ポイントでは、前回のバックアップ以降に行われた変更のみを転送対象とすることで、高い保存効率と時間効率を実現します。

次のように、バックアップを有効にして実行します。

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered -FriendlyName "V2VM"
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType AzureVM
$job = Backup-AzRecoveryServicesBackupItem -Item $item
```

## <a name="troubleshooting"></a>トラブルシューティング

仮想マシンのバックアップ中に問題が発生した場合は、この[トラブルシューティングに関する記事](backup-azure-vms-troubleshoot.md)を参照してください。

### <a name="deleting-a-recovery-services-vault"></a>Recovery Services コンテナーの削除

コンテナーを削除する必要がある場合は、まず、コンテナーのすべての復旧ポイントを削除し、次のように、コンテナーの登録を解除します。

```powershell
$Cont = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVM -Status Registered
$PI = Get-AzRecoveryServicesBackupItem -Container $Cont[0] -WorkloadType AzureVm
Disable-AzRecoveryServicesBackupProtection -RemoveRecoveryPoints $PI[0]
Unregister-AzRecoveryServicesBackupContainer -Container $namedContainer
Remove-AzRecoveryServicesVault -Vault $vault1
```

## <a name="next-steps"></a>次のステップ

* PowerShell での Azure VM のバックアップと復元に関するより詳細なチュートリアルを[確認](backup-azure-vms-automation.md)する。
* [Azure VM を管理および監視する](backup-azure-manage-vms.md)
* [Azure VM の復元](backup-azure-arm-restore-vms.md)
