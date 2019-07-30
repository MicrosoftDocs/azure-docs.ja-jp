---
title: Azure Backup を使用して暗号化された VM の Key Vault のキーとシークレットを復元
description: Azure Backup で PowerShell を使用して Key Vault のキーとシークレットを復元する方法を説明します。
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: geg
ms.openlocfilehash: bdc732ef02e9995e3d6dc17aa1f79eb97d895a73
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465573"
---
# <a name="restore-key-vault-key-and-secret-for-encrypted-vms-using-azure-backup"></a>Azure Backup を使用して暗号化された VM の Key Vault のキーとシークレットを復元

この記事では、キーとシークレットが Key Vault に存在しない場合に、暗号化された Azure VM の復元を Azure VM Backup を使用して実行する方法を説明します。 次に示す手順は、復元した VM のキー (Key 暗号化キー) とシークレット (BitLocker 暗号化キー) の個別のコピーを保持する場合にも使用できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

* **暗号化された VM のバックアップ** - 暗号化された Azure VM が Azure Backup を使用してバックアップされています。 暗号化された Azure VM のバックアップ方法について詳しくは、[PowerShell を使用した Azure VM のバックアップと復元の管理に関する記事](backup-azure-vms-automation.md)をご覧ください。
* **Azure Key Vault の構成** – キーとシークレットの復元先の Key Vault が既に存在しています。 Key Vault の管理について詳しくは、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* **ディスクの復元** - [PowerShell ステップ](backup-azure-vms-automation.md#restore-an-azure-vm)を使って暗号化された VM のディスクを復元するために、復元ジョブを必ずトリガーしてください。 このジョブでは、暗号化された VM の復元先となる、キーとシークレットを含むストレージ アカウント内に、JSON ファイルを生成するためです。

## <a name="get-key-and-secret-from-azure-backup"></a>Azure Backup からのキーとシークレットの取得

> [!NOTE]
> 暗号化された VM のディスクが復元されたら、以下を確認してください。
> * [ディスクの復元セクションの PowerShell ステップ](backup-azure-vms-automation.md#restore-an-azure-vm)に記載されているように、$details にはディスクの復元ジョブの詳細が設定されます。
> * **キーとシークレットが Key Vault に復元された後**にのみ、復元されたディスクから VM が作成されます。

復元されたディスクのプロパティに対し、ジョブの詳細を照会します。

```powershell
$properties = $details.properties
$storageAccountName = $properties["Target Storage Account Name"]
$containerName = $properties["Config Blob Container Name"]
$encryptedBlobName = $properties["Encryption Info Blob Name"]
```

Azure ストレージ コンテキストを設定し、暗号化された VM のキーとシークレットの詳細を含む JSON 構成ファイルを復元します。

```powershell
Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName '<rg-name>'
$destination_path = 'C:\vmencryption_config.json'
Get-AzStorageBlobContent -Blob $encryptedBlobName -Container $containerName -Destination $destination_path
$encryptionObject = Get-Content -Path $destination_path  | ConvertFrom-Json
```

## <a name="restore-key"></a>キーの復元

上記の宛先パスに JSON ファイルが生成された後に、JSON からキー BLOB ファイルを生成して、このファイルをフィードしてキーのコマンドレットを復元し、Key Vault にキー (KEK) を戻します。

```powershell
$keyDestination = 'C:\keyDetails.blob'
[io.file]::WriteAllBytes($keyDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyBackupData))
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile $keyDestination
```

## <a name="restore-secret"></a>シークレットの復元

前の手順で生成した JSON ファイルを使用してシークレットの名前と値を取得し、これをフィードしてシークレットのコマンドレットを設定し、Key Vault にシークレット (BEK) を戻します。 **VM が BEK と KEK を使用して暗号化される場合**は、次のコマンドレットを使用します。

**Windows VM が BEK と KEK を使用して暗号化される場合は、次のコマンドレットを使用します。**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

**Linux VM が BEK と KEK を使用して暗号化される場合は、次のコマンドレットを使用します。**

```powershell
$secretdata = $encryptionObject.OsDiskKeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'LinuxPassPhraseFileName';'DiskEncryptionKeyEncryptionKeyURL' = $encryptionObject.OsDiskKeyAndSecretDetails.KeyUrl;'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $Secret -ContentType  'Wrapped BEK' -Tags $Tags
```

前の手順で生成した JSON ファイルを使用してシークレットの名前と値を取得し、これをフィードしてシークレットのコマンドレットを設定し、Key Vault にシークレット (BEK) を戻します。 **VM が BEK のみを使用して暗号化される場合**は、次のコマンドレットを使用します。

```powershell
$secretDestination = 'C:\secret.blob'
[io.file]::WriteAllBytes($secretDestination, [System.Convert]::FromBase64String($encryptionObject.OsDiskKeyAndSecretDetails.KeyVaultSecretBackupData))
Restore-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -InputFile $secretDestination -Verbose
  ```

> [!NOTE]
> * $encryptionObject.OsDiskKeyAndSecretDetails.SecretUrl の出力を参照し、secrets/ の後に続くテキスト (output secret URL is https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 and secret name is B3284AAA-DAAA-4AAA-B393-60CAA848AAAA など) を使って、$secretname の値を取得できます。
> * タグ DiskEncryptionKeyFileName の名前は、シークレットの名前と同じです。
>
>

## <a name="create-virtual-machine-from-restored-disk"></a>復元されたディスクからの仮想マシンの作成

暗号化された VM を Azure VM Backup を使用してバックアップしておいた場合、上記の PowerShell コマンドレットを使用して、キーとシークレットをキー コンテナーに復元することができます。 これらを復元した後で、復元したディスク、キー、およびシークレットから暗号化された VM を作成するには、[PowerShell を使用した Azure VM のバックアップと復元の管理](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)に関する記事をご覧ください。

## <a name="legacy-approach"></a>従来の手法

上述した手法はすべての復旧ポイントで有効です。 ただし、BEK と KEK を使用して暗号化された VM の場合、2017 年 7 月 11 日より前の復旧ポイントでは、復旧ポイントからキーとシークレットを取得する従来の手法も有効です。 [PowerShell ステップ](backup-azure-vms-automation.md#restore-an-azure-vm)を使って、暗号化された VM のディスクの復元 ジョブが完了したら、$rp に有効な値が設定されていることを確認してください。

### <a name="restore-key"></a>キーの復元

以下のコマンドレットを使用して復旧ポイントからキー (KEK) 情報を取得し、この情報をフィードしてキーのコマンドレットを復元し、Key Vault に戻します。

```powershell
$rp1 = Get-AzRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation 'C:\Users\downloads'
Restore-AzureKeyVaultKey -VaultName '<target_key_vault_name>' -InputFile 'C:\Users\downloads'
```

### <a name="restore-secret"></a>シークレットの復元

以下のコマンドレットを使用して復旧ポイントからシークレット (BEK) 情報を取得し、この情報をフィードしてシークレットのコマンドレットを設定し、Key Vault に戻します。

```powershell
$secretname = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA'
$secretdata = $rp1.KeyAndSecretDetails.SecretData
$Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
$Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://mykeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
Set-AzureKeyVaultSecret -VaultName '<target_key_vault_name>' -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  'Wrapped BEK'
```

> [!NOTE]
> * $rp1.KeyAndSecretDetails.SecretUrl の出力を参照し、secrets/ の後に続くテキスト (output secret URL is https://keyvaultname.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/xx000000xx0849999f3xx30000003163 and secret name is B3284AAA-DAAA-4AAA-B393-60CAA848AAAA など) を使って、$secretname の値を取得できます。
> * タグ DiskEncryptionKeyFileName の名前は、シークレットの名前と同じです。
> * DiskEncryptionKeyEncryptionKeyURL の名前は、キーを復元した後で、[Get-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/get-azurekeyvaultkey) コマンドレットを使用して、Key Vault から取得できます。
>
>

## <a name="next-steps"></a>次の手順

キーとシークレットを Key Vault に復元した後で、復元したディスク、キー、およびシークレットから暗号化された VM を作成するには、[PowerShell を使用した Azure VM のバックアップと復元の管理](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)に関する記事をご覧ください。
