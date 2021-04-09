---
title: Windows VM 用の Azure Disk Encryption のサンプル スクリプト
description: この記事は、Windows VM 用の Microsoft Azure Disk Encryption に関する付録です。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 413553165fcf74fa4590cb4661212b885a277579
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550639"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption のサンプル スクリプト 

この記事では、事前に暗号化された VHD の準備およびその他のタスクのためのサンプル スクリプトを提供します。

> [!NOTE]
> すべてのスクリプトでは、特に明記されている場合を除き、非 AAD の最新バージョンの ADE が参照されます。

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure Disk Encryption 用の PowerShell スクリプトのサンプル 


- **サブスクリプション内の暗号化された VM をすべて一覧表示する**

  [この PowerShell スクリプト](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)を使用して、サブスクリプションに存在するすべてのリソース グループの ADE で暗号化されたすべての VM と拡張バージョンを見つけることができます。

  また、これらのコマンドレットを使用すると、ADE で暗号化されたすべての VM が表示されます (拡張機能のバージョンは表示されません)。

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **サブスクリプション内のすべての暗号化された VMSS インスタンスを一覧表示する**
    
    [この PowerShell スクリプト](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)を使用して、サブスクリプションに存在するすべてのリソース グループの ADE で暗号化されたすべての VMSS インスタンスと拡張バージョンを見つけることができます。
 
- **キー コンテナー内の VM を暗号化するために使用されるディスクの暗号化シークレットをすべて一覧表示する**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Azure Disk Encryption の前提条件となる PowerShell スクリプトの使用

Azure Disk Encryption の前提条件に既に精通している場合は、[Azure Disk Encryption の前提条件となる PowerShell スクリプト](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )を使用できます。 この PowerShell スクリプトの使用例については、[VM の暗号化のクイック スタート](disk-encryption-powershell-quickstart.md)に関するページを参照してください。 既存のリソース グループ内の既存の VM のすべてのディスクを暗号化するために、スクリプトの 211 行目から始まるセクションのコメントを削除することができます。 

次の表は、PowerShell スクリプトでどのパラメーターを使用することができるかを示しています。 

|パラメーター|説明|必須|
|------|------|------|
|$resourceGroupName| KeyVault が属するリソース グループの名前。  該当するリソース グループがない場合は、この名前の付いた新しいリソース グループが作成されます。| True|
|$keyVaultName|暗号化キーが配置される KeyVault の名前。 該当するコンテナーが存在しない場合は、この名前の付いた新しいコンテナーが作成されます。| True|
|$location|KeyVault の場所。 KeyVault と暗号化する VM が同じ場所にあることを確認します。 場所の一覧を取得するには、`Get-AzLocation` を使用します。|True|
|$subscriptionId|使用する Azure サブスクリプションの識別子。  サブスクリプション ID を取得するには、`Get-AzSubscription` を使用します。|True|
|$aadAppName|シークレットを KeyVault に書き込むために使用される Azure AD アプリケーションの名前。 該当するアプリケーションがない場合は、この名前の付いた新しいアプリケーションが作成されます。 このアプリが既に存在する場合は、スクリプトに aadClientSecret パラメーターを渡します。|False|
|$aadClientSecret|前に作成した Azure AD アプリケーションのクライアント シークレット。|False|
|$keyEncryptionKeyName|KeyVault のオプションのキー暗号化キーの名前。 該当するキーが存在しない場合は、この名前の付いた新しいキーが作成されます。|False|

## <a name="resource-manager-templates"></a>Resource Manager テンプレート

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Azure AD アプリを使用せずに VM を暗号化また暗号化解除する

- [既存または実行中の Windows VM でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [実行中の Windows VM で暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Azure AD アプリを使用して VM を暗号化また暗号化解除する (以前のリリース) 
 
- [既存または実行中の Windows VM でディスク暗号化を有効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [実行中の Windows VM で暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [事前に暗号化された VHD/ストレージ BLOB から、新しい暗号化されたマネージド ディスクを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - 事前に暗号化された VHD とその対応する暗号化設定がある場合、新しい暗号化されたマネージド ディスクが作成されます

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>事前に暗号化された Windows VHD を準備する
以下のセクションに示すのは、事前に暗号化された Windows VHD を準備し、それを Azure IaaS 内の暗号化された VHD としてデプロイするために必要な情報です。 Azure Site Recovery や Azure 上に新しい Windows VM (VHD) を準備し、それらを起動する際には、これらの情報を使用してください。 VHD を準備してアップロードする方法の詳細については、「[汎用化した VHD をアップロードして Azure で新しい VM を作成する](upload-generalized-managed.md)」を参照してください。

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>グループ ポリシーを更新して非 TPM で OS を保護できるようにする
**[ローカル コンピューター ポリシー]**  >  **[コンピューターの構成]**  >  **[管理用テンプレート]**  >  **[Windows コンポーネント]** の下にある、 **[BitLocker ドライブ暗号化]** という BitLocker グループ ポリシー設定を構成します。 以下の図に示すように、 **[オペレーティング システムのドライブ]**  >  **[スタートアップ時に追加の認証を要求する]**  >  **[互換性のある TPM が装備されていない BitLocker を許可する]** の順に選択して、この設定を変更します。

![Azure での Microsoft マルウェア対策](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker 機能コンポーネントのインストール
Windows Server 2012 以降の場合は、次のコマンドを使用します。

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Windows Server 2008 R2 の場合は、次のコマンドを使用します。

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg` を使用して BitLocker の OS ボリュームを準備する
OS のパーティションを圧縮して、BitLocker 用にコンピューターを準備するには、必要に応じて、[bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) を実行します。

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker を使用して OS ボリュームを保護する
ブート ボリュームでの暗号化を、外部キーの保護機能を使用して有効化するには、[`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) コマンドを使用します。 また、外部ドライブまたは外部ボリューム上に外部キー (.bek ファイル) を配置します。 システム/ブート ボリュームでの暗号化は、次回のリブート後に有効になります。

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> BitLocker を使用して外部キーを取得する場合は、そのためのデータ/リソース VHD を別個に使用して VM を準備してください。

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>暗号化された VHD を Azure ストレージ アカウントにアップロードする
DM-Crypt 暗号化を有効にした後、ローカル環境で暗号化された VHD をストレージ アカウントにアップロードする必要があります。
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>事前に暗号化された VM 用のシークレットをご自分のキー コンテナーにアップロードする
前に取得したディスク暗号化シークレットを、ご自分のキー コンテナーにシークレットとしてアップロードする必要があります。  そのためには、シークレットをアップロードするアカウントに、set secret アクセス許可と wrapkey アクセス許可を付与する必要があります。

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>KEK で暗号化されないディスク暗号化シークレット
キー コンテナーでシークレットを設定するには、[Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) を使用します。 パスフレーズが base64 文字列としてエンコードされた後、キー コンテナーにアップロードされます。 また、Key Vault でシークレットを作成する際には、以下のタグが設定されます。

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


[KEK を使用せずに OS ディスクをアタッチする](#without-using-a-kek)場合は、次の手順で `$secretUrl` を使用します。

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK で暗号化されるディスク暗号化シークレット
必要であれば、シークレットを Key Vault にアップロードする前に、キー暗号化キーを使用してシークレットを暗号化できます。 最初にキー暗号化キーを使用してシークレットを暗号化するには、ラップ [API](/rest/api/keyvault/wrapkey) を使用します。 このラップ操作の出力は、base64 URL エンコードされた文字列です。これは、[`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) コマンドレットを使用してシークレットとしてアップロードできます。

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

[KEK を使用して OS ディスクをアタッチする](#using-a-kek)場合は、次の手順で `$KeyEncryptionKey` と `$secretUrl` を使用します。

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>OS ディスクをアタッチするときにシークレット URL を指定する

###  <a name="without-using-a-kek"></a>KEK を使用しない
OS ディスクをアタッチする際に、`$secretUrl` を渡す必要があります。 この URL は、「KEK で暗号化されないディスク暗号化シークレット」セクションで生成されたものです。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK を使用する
OS ディスクをアタッチする際に、`$KeyEncryptionKey` と `$secretUrl` を渡します。 この URL は、「KEK で暗号化されるディスク暗号化シークレット」セクションで生成されたものです。
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
