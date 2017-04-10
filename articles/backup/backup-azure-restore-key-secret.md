---
title: "Azure Backup を使用して暗号化された VM の Key Vault キーとシークレットを復元 | Microsoft Docs"
description: "Azure Backup で PowerShell を使用して Key Vault のキーとシークレットを復元する方法を説明します。"
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 45214083-d5fc-4eb3-a367-0239dc59e0f6
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: pajosh
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 82b7541ab1434179353247ffc50546812346bda9
ms.openlocfilehash: ddb9e7909eb4ab97204059d21690795ceb6ff9e8
ms.lasthandoff: 03/02/2017


---
# <a name="restore-an-encrypted-virtual-machine-from-an-azure-backup-recovery-point"></a>Azure Backup 復旧ポイントから暗号化された仮想マシンを復元する
この記事では、キーとシークレットが Key Vault に存在しない場合に、暗号化された Azure VM の復元を Azure VM Backup を使用して実行する方法を説明します。 次に示す手順は、復元した VM のキー (Key 暗号化キー) とシークレット (BitLocker 暗号化キー) の個別のコピーを保持する場合にも使用できます。

## <a name="pre-requisites"></a>前提条件
1. **暗号化された VM のバックアップ** - 暗号化された Azure VM が Azure Backup を使用してバックアップされています。 暗号化された Azure VM のバックアップ方法について詳しくは、[PowerShell を使用した Azure VM のバックアップと復元の管理に関する記事](backup-azure-vms-automation.md)をご覧ください。
2. **Azure Key Vault の構成** – キーとシークレットの復元先の Key Vault が既に存在しています。 Key Vault の管理について詳しくは、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。

## <a name="setup-recovery-services-vault"></a>Recovery Services コンテナーのセットアップ
次の手順に従い、PowerShell にログインし、Recovery Services コンテナー コンテキストを設定します。

### <a name="log-in-to-azure-powershell"></a>Azure PowerShell へのログイン
次のコマンドレットを使用して、Azure アカウントにログインします。

```
PS C:\> Login-AzureRmAccount
```

### <a name="set-recovery-services-vault-context"></a>Recovery Services コンテナーのコンテキストの設定
ログインしたら、次のコマンドレットを使用して、利用可能なサブスクリプションの一覧を取得します。

```
PS C:\> Get-AzureRmSubscription
```

リソースが利用可能なサブスクリプションを選択します。

```
PS C:\> Set-AzureRmContext -SubscriptionId "<subscription-id>"
```

暗号化された VM のバックアップが有効化された Recovery Services コンテナーを使用して、コンテナー コンテキストを設定します。

```
PS C:\> Get-AzureRmRecoveryServicesVault -ResourceGroupName "<rg-name>" -Name "<rs-vault-name>" | Set-AzureRmRecoveryServicesVaultContext
```

### <a name="get-recovery-point"></a>復旧ポイントの取得
暗号化された Azure 仮想マシンを表す Key Vault 内のコンテナーを選択します。

```
PS C:\> $namedContainer = Get-AzureRmRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -Name "<vm-name>"
```

このコンテナーを使用して、対応する仮想マシンのバックアップ項目を取得します。

```
PS C:\> $backupitem = Get-AzureRmRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM"
```

変数 rp で、選択したバックアップ項目の復旧ポイントの配列を取得します。

```
PS C:\> $startDate = (Get-Date).AddDays(-7)
PS C:\> $endDate = Get-Date
PS C:\> $rp = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()
```

## <a name="restore-encrypted-virtual-machine"></a>暗号化された仮想マシンの復元
次の手順に従って、暗号化された VM およびそのキーとシークレットを復元します。

### <a name="restore-key"></a>キーの復元
前述の配列 $rp は時間の逆順で並べ替えられています。最新の復旧ポイントのインデックスは 0 です。 例: $rp[0] は、最新の復旧ポイントを選択します。

```
PS C:\> $rp1 = Get-AzureRmRecoveryServicesBackupRecoveryPoint -RecoveryPointId $rp[0].RecoveryPointId -Item $backupItem -KeyFileDownloadLocation "C:\Users\downloads"
```

> [!NOTE]
> このコマンドレットが正常に実行すると、実行したマシンの指定フォルダーに BLOB ファイルが生成されます。 この BLOB ファイルは Key 暗号化キーを暗号化された形式で表します。
>
>

次のコマンドレットを使用して、キーを Key Vault に復元します。

```
PS C:\> Restore-AzureKeyVaultKey -VaultName "contosokeyvault" -InputFile "C:\Users\downloads\key.blob"
```

### <a name="restore-secret"></a>シークレットの復元
前の手順で取得した復旧ポイントからシークレット データを復元します。

```
PS C:\> $rp1.KeyAndSecretDetails.SecretUrl

https://contosokeyvault.vault.azure.net/secrets/B3284AAA-DAAA-4AAA-B393-60CAA848AAAA/20aaae9eaa99996d89d99a29990d999a
```

> [!NOTE]
> vault.azure.net の前のテキストは元の Key Vault 名を表します。 secrets/ の後のテキストはシークレット名を表します。
>
>

同じシークレット名を使用したい場合は、前の手順で実行したコマンドレットの出力からシークレットの名前と値を取得します。 それ以外の場合は、新しいシークレット名を使用するように次の $secretname を更新してください。

```
PS C:\> $secretname = "B3284AAA-DAAA-4AAA-B393-60CAA848AAAA"
PS C:\> $secretdata = $rp1.KeyAndSecretDetails.SecretData
PS C:\> $Secret = ConvertTo-SecureString -String $secretdata -AsPlainText -Force
```

VM も復元する必要がある場合には、シークレットのタグを設定します。 タグ DiskEncryptionKeyFileName には、値として、使用する予定のシークレットの名前を含めます。

```
PS C:\> $Tags = @{'DiskEncryptionKeyEncryptionAlgorithm' = 'RSA-OAEP';'DiskEncryptionKeyFileName' = 'B3284AAA-DAAA-4AAA-B393-60CAA848AAAA.BEK';'DiskEncryptionKeyEncryptionKeyURL' = 'https://contosokeyvault.vault.azure.net:443/keys/KeyName/84daaac999949999030bf99aaa5a9f9';'MachineName' = 'vm-name'}
```

> [!NOTE]
> DiskEncryptionKeyFileName の値は、前に取得したシークレット名と同じです。 DiskEncryptionKeyEncryptionKeyURL の名前は、キーを復元した後で、[Get-AzureKeyVaultKey](https://msdn.microsoft.com/library/dn868053.aspx) コマンドレットを使用して、Key Vault から取得できます。    
>
>

Key Vault にシークレットを戻すように設定します。

```
PS C:\> Set-AzureKeyVaultSecret -VaultName "contosokeyvault" -Name $secretname -SecretValue $secret -Tags $Tags -SecretValue $Secret -ContentType  "Wrapped BEK"
```

### <a name="restore-virtual-machine"></a>仮想マシンの復元
暗号化された VM を Azure VM Backup を使用してバックアップしておいた場合、上記の PowerShell コマンドレットを使用して、キーとシークレットを Key Vault に復元することができます。 これらを復元した後で、暗号化された VM を復元するには、[PowerShell を使用した Azure VM のバックアップと復元の管理に関する記事](backup-azure-vms-automation.md)をご覧ください。

