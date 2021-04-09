---
title: Linux の暗号化状態の確認 - Azure Disk Encryption
description: この記事では、プラットフォームおよび OS レベルの暗号化の状態を確認する手順について説明します。
author: kailashmsft
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: kaib
ms.date: 03/11/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 04654672cc5806465ec9f75b695772dcb2037eab
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102564137"
---
# <a name="verify-encryption-status-for-linux"></a>Linux の暗号化の状態を確認する 

この記事では、Azure portal、PowerShell、Azure CLI、仮想マシン (VM) のオペレーティング システムを使用した各種方法によって、仮想マシンの暗号化の状態を確認します。 

暗号化の状態は、次のいずれかの方法により暗号化中または暗号化後に確認することができます。

- 特定の VM にアタッチされたディスクを確認する。 
- 各ディスクの暗号化の設定を照会する。この方法は、ディスクがアタッチされているかどうかに関係しません。

このシナリオは、Azure Disk Encryption のデュアルパス拡張機能とシングルパス拡張機能を対象としています。 このシナリオに該当する環境は、Linux ディストリビューションのみです。

>[!NOTE] 
>この記事では、変数を使用しています。 値は適宜置き換えてください。

## <a name="portal"></a>ポータル

Azure portal の **[拡張機能]** セクションで、一覧から Azure Disk Encryption 拡張機能を選択します。 **[ステータス メッセージ]** の情報に、現在の暗号化の状態が示されます。

![ポータルでの確認 (状態、バージョン、ステータス メッセージを強調表示)](./media/disk-encryption/verify-encryption-linux/portal-check-001.png)

拡張機能の一覧には、対応する Azure Disk Encryption 拡張機能のバージョンが表示されます。 バージョン 0.x は Azure Disk Encryption デュアル パスに、バージョン 1.x は Azure Disk Encryption シングル パスに対応します。

拡張機能を選び、 **[詳細な状態の表示]** を選択すると、さらに詳しい情報が得られます。 暗号化プロセスの詳細な状態が、JSON 形式で表示されます。

![ポータルでの確認 ([詳細な状態の表示] リンクを強調表示)](./media/disk-encryption/verify-encryption-linux/portal-check-002.png)

![JSON 形式の詳細な状態](./media/disk-encryption/verify-encryption-linux/portal-check-003.png)

暗号化の状態は、 **[ディスクの設定]** セクションを見て確認することもできます。

![OS ディスクとデータ ディスクの暗号化状態](./media/disk-encryption/verify-encryption-linux/portal-check-004.png)

>[!NOTE] 
> この状態は、ディスクに暗号化の設定がスタンプされていることを意味するものであり、実際に OS レベルで暗号化されているという意味ではありません。
>
> 設計上、ディスクはまずスタンプされ、その後暗号化されます。 暗号化プロセスに失敗した場合、ディスクはスタンプされているものの暗号化されていない状態になります。 
>
> 実際にディスクが暗号化されているかどうかを確かめるために、OS レベルで各ディスクの暗号化をダブルチェックしてください。

## <a name="powershell"></a>PowerShell

暗号化された VM の "*全体的な*" 暗号化の状態は、次の PowerShell コマンドを使用して確認できます。

```azurepowershell
   $VMNAME="VMNAME"
   $RGNAME="RGNAME"
   Get-AzVmDiskEncryptionStatus -ResourceGroupName  ${RGNAME} -VMName ${VMNAME}
```
![PowerShell で確認した全体的な暗号化状態](./media/disk-encryption/verify-encryption-linux/verify-status-ps-01.png)

各ディスクの暗号化の設定を取得するには、次の PowerShell コマンドを使用します。

### <a name="single-pass"></a>シングル パス
シングル パスでは、各ディスク (OS とデータ) に暗号化設定がスタンプされます。 シングル パスの OS ディスクの暗号化設定は、次のようにして取得できます。

```powershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}  
 $Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
 Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
 Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
 Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
 Write-Host "============================================================================================================================================================="
```
![OS ディスクの暗号化設定](./media/disk-encryption/verify-encryption-linux/verify-os-single-ps-001.png)

ディスクに暗号化の設定がスタンプされていない場合、出力は空になります。

![空の出力](./media/disk-encryption/verify-encryption-linux/os-encryption-settings-2.png)

データ ディスクの暗号化設定を取得するには、次のコマンドを使用します。

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$VM = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME}
 clear
 foreach ($i in $VM.StorageProfile.DataDisks|ForEach-Object{$_.Name})
 {
 Write-Host "============================================================================================================================================================="
 Write-Host "Encryption Settings:"
 Write-Host "============================================================================================================================================================="
 Write-Host "Checking Disk:" $i
 $Disk=(Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $i)
 Write-Host "Encryption Enable: " $Sourcedisk.EncryptionSettingsCollection.Enabled
 Write-Host "Encryption KeyEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl;
 Write-Host "Encryption DiskEncryptionKey: " $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl;
 Write-Host "============================================================================================================================================================="
 }
```
![データ ディスクの暗号化設定](./media/disk-encryption/verify-encryption-linux/verify-data-single-ps-001.png)

### <a name="dual-pass"></a>デュアル パス
デュアル パスでは、暗号化の設定は、個々のディスクではなく VM モデルにスタンプされます。

暗号化設定がデュアル パスでスタンプされていることを確認するには、次のコマンドを使用します。

```azurepowershell
$RGNAME = "RGNAME"
$VMNAME = "VMNAME"

$vm = Get-AzVm -ResourceGroupName ${RGNAME} -Name ${VMNAME};
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName $VM.StorageProfile.OsDisk.Name
clear
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
![デュアル パスでの暗号化設定](./media/disk-encryption/verify-encryption-linux/verify-dual-ps-001.png)

### <a name="unattached-disks"></a>アタッチされていないディスク

VM にアタッチされていないディスクの暗号化の設定を確認します。

### <a name="managed-disks"></a>マネージド ディスク
```powershell
$Sourcedisk = Get-AzDisk -ResourceGroupName ${RGNAME} -DiskName ${TARGETDISKNAME}
Write-Host "============================================================================================================================================================="
Write-Host "Encryption Settings:"
Write-Host "============================================================================================================================================================="
Write-Host "Enabled:" $Sourcedisk.EncryptionSettingsCollection.Enabled
Write-Host "Version:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettingsVersion
Write-Host "Source Vault:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SourceVault.Id
Write-Host "Secret URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.DiskEncryptionKey.SecretUrl
Write-Host "Key URL:" $Sourcedisk.EncryptionSettingsCollection.EncryptionSettings.KeyEncryptionKey.KeyUrl
Write-Host "============================================================================================================================================================="
```
## <a name="azure-cli"></a>Azure CLI

暗号化された VM の "*全体的な*" 暗号化の状態は、次の Azure CLI コマンドを使用して確認できます。

```azurecli
VMNAME="VMNAME"
RGNAME="RGNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"
```
![Azure CLI で確認した全体的な暗号化状態 ](./media/disk-encryption/verify-encryption-linux/verify-gen-cli.png)

### <a name="single-pass"></a>シングル パス
各ディスクの暗号化の設定を確認するには、次の Azure CLI コマンドを使用します。

```azurecli
az vm encryption show -g ${RGNAME} -n ${VMNAME} --query "disks[*].[name, statuses[*].displayStatus]"  -o table
```

![データ暗号化設定](./media/disk-encryption/verify-encryption-linux/data-encryption-settings-2.png)

>[!IMPORTANT]
> ディスクに暗号化の設定がスタンプされていない場合、 **"ディスク暗号化なし"** というテキストが表示されます。

詳細な状態と暗号化設定を取得するには、次のコマンドを使用します。

OS ディスク:

```bash
RGNAME="RGNAME"
VMNAME="VNAME"

disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![OS の詳細な状態と暗号化設定](./media/disk-encryption/verify-encryption-linux/os-single-cli.png)

データ ディスク:

```azurecli
RGNAME="RGNAME"
VMNAME="VMNAME"
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} --query "substatus"

for disk in `az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks[].name -o tsv`; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![データ ディスクの詳細な状態と暗号化設定](./media/disk-encryption/verify-encryption-linux/data-single-cli.png)

### <a name="dual-pass"></a>デュアル パス

```azurecli
az vm encryption show --name ${VMNAME} --resource-group ${RGNAME} -o table
```

![Azure CLI で取得したデュアル パスの全体的な暗号化設定](./media/disk-encryption/verify-encryption-linux/verify-gen-dual-cli.png)

暗号化設定は、OS ディスクの VM モデル ストレージ プロファイルでも確認できます。

```bash
disk=`az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.osDisk.name -o tsv`
for disk in $disk; do \
echo "============================================================================================================================================================="; \
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${disk} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${disk} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
done
```

![Azure CLI で取得したデュアル パスの VM プロファイル](./media/disk-encryption/verify-encryption-linux/verify-vm-profile-dual-cli.png)

### <a name="unattached-disks"></a>アタッチされていないディスク

VM にアタッチされていないディスクの暗号化の設定を確認します。

### <a name="managed-disks"></a>マネージド ディスク

```bash
RGNAME="RGNAME"
TARGETDISKNAME="DISKNAME"
echo "============================================================================================================================================================="
echo -ne "Disk Name: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query name -o tsv; \
echo -ne "Encryption Enabled: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.enabled -o tsv; \
echo -ne "Version: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettingsVersion -o tsv; \
echo -ne "Disk Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].diskEncryptionKey.secretUrl -o tsv; \
echo -ne "key Encryption Key: "; az disk show -g ${RGNAME} -n ${TARGETDISKNAME} --query encryptionSettingsCollection.encryptionSettings[].keyEncryptionKey.keyUrl -o tsv; \
echo "============================================================================================================================================================="
```
### <a name="unmanaged-disks"></a>非管理対象ディスク

アンマネージド ディスクは、Azure Storage アカウントでページ BLOB として格納された VHD ファイルです。

特定のディスクの詳細を把握するには、次の情報を指定する必要があります。

- ディスクが含まれるストレージ アカウントの ID。
- 対象となる特定のストレージ アカウントの接続文字列。
- ディスクを格納するコンテナーの名前。
- ディスク名。

次のコマンドは、すべてのストレージ アカウントの ID を一覧表示するものです。

```azurecli
az storage account list --query [].[id] -o tsv
```
ストレージ アカウントの ID が、次の形式で一覧表示されます。

/subscriptions/\<subscription id>/resourceGroups/\<resource group name>/providers/Microsoft.Storage/storageAccounts/\<storage account name>

適切な ID を選択して変数に格納します。
```bash
id="/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>"
```

次のコマンドは、ある特定のストレージ アカウントの接続文字列を取得して変数に格納するものです。

```bash
ConnectionString=$(az storage account show-connection-string --ids $id --query connectionString -o tsv)
```

次のコマンドは、ストレージ アカウントにあるすべてのコンテナーを一覧表示するものです。
```azurecli
az storage container list --connection-string $ConnectionString --query [].[name] -o tsv
```
ディスクに使用されるコンテナーには通常、"vhds" という名前が付けられます。

そのコンテナー名を変数に格納します。 
```bash
ContainerName="name of the container"
```

特定のコンテナーにあるすべての BLOB を一覧表示するには、次のコマンドを使用します。
```azurecli 
az storage blob list -c ${ContainerName} --connection-string $ConnectionString --query [].[name] -o tsv
```
照会したいディスクを選んで、その名前を変数に格納します。
```bash
DiskName="diskname.vhd"
```
ディスクの暗号化設定を照会します。
```azurecli
az storage blob show -c ${ContainerName} --connection-string ${ConnectionString} -n ${DiskName} --query metadata.DiskEncryptionSettings
```

## <a name="operating-system"></a>オペレーティング システム
データ ディスクのパーティションが暗号化されている (かつ OS ディスクが暗号化されていない) かどうかを確認します。

暗号化されているパーティションまたはディスクは、 **"crypt"** タイプとして表示されます。 暗号化されていない場合は、 **"part" または "disk"** タイプとして表示されます。

```bash
lsblk
```

![パーティションの OS crypt レイヤー](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer.png)

以下の **lsblk** バリアントを使用すると、さらに詳しい情報を取得できます。 

拡張機能によってマウントされた **crypt** タイプ レイヤーが表示されます。 **crypto\_LUKS FSTYPE** の通常のディスクと論理ボリュームの例を、次に示します。

```bash
lsblk -o NAME,TYPE,FSTYPE,LABEL,SIZE,RO,MOUNTPOINT
```
![論理ボリュームと通常のディスクの OS crypt レイヤー](./media/disk-encryption/verify-encryption-linux/verify-os-crypt-layer-2.png)

さらに、データ ディスクにキーが読み込まれているかどうかも確認できます。

```bash
cryptsetup luksDump /dev/VGNAME/LVNAME
```

```bash
cryptsetup luksDump /dev/sdd1
```

また、どの **dm** デバイスが **crypt** として表示されるかを調べることもできます。

```bash
dmsetup ls --target crypt
```

## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)
