---
title: Azure VM での BitLocker ブート エラーのトラブルシューティング | Microsoft Docs
description: Azure VM での BitLocker ブート エラーをトラブルシューティングする方法について説明します
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 87bf311b5199ec187c24c28a42314d9dc6787998
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633029"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Azure VM での BitLocker ブート エラー

 この記事では、Microsoft Azure で Windows 仮想マシン (VM) を開始するときに発生する可能性がある BitLocker エラーについて説明します。

## <a name="symptom"></a>症状

 Windows VM が起動しません。 [[ブート診断]](./boot-diagnostics.md) ウィンドウでスクリーンショットを調べると、次のエラー メッセージのいずれかが表示されています。

- BitLocker キーを含んだ USB ドライバーを接続してください

- ロックアウトされました。 使用できる状態に戻すには回復キーを入力してください (キーボード レイアウト:US) 正しくないサインイン情報が何度も入力されたので、お使いの PC はプライバシーを保護するためにロックされました。 回復キーを取得するには、他の PC またはモバイル デバイスで https://windows.microsoft.com/recoverykeyfaq にアクセスしてください。 なお、キー ID は XXXXXXX です。 また、お使いの PC を初期状態に戻すこともできます。

- このドライブのロックを解除するためにパスワードを入力してください [ ] 入力時にパスワードを表示するには、挿入キー (Insert) を押してください。
- 回復キーの入力。USB デバイスから回復キーを読み込む。

## <a name="cause"></a>原因

この問題は、暗号化されたディスクの暗号化を解除するための BitLocker 回復キー (BEK) ファイルを VM が見つけることができない場合に発生する可能性があります。

## <a name="solution"></a>解決策

> [!TIP]
> VM の最新のバックアップがある場合は、[そのバックアップから VM の復元](../../backup/backup-azure-arm-restore-vms.md)を試行して、起動の問題を修正することができます。

この問題を解決するには、VM を停止して割り当てを解除した後、これを起動します。 この操作によって、VM は、Azure Key Vault から BEK ファイルを取得した後、それを暗号化されたディスクに保存します。 

この方法で問題が解決しない場合は、次の手順で BEK ファイルを手動で復元します。

1. バックアップとして、影響を受ける VM のシステム ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。
2. [復旧 VM にシステム ディスクを取り付ける](troubleshoot-recovery-disks-portal-windows.md)。 手順 7 で [manage-bde](/windows-server/administration/windows-commands/manage-bde) コマンドを実行するには、復旧 VM で **[BitLocker ドライブ暗号化]** 機能を有効にする必要があります。

    マネージド ディスクをアタッチすると、エラー メッセージ "contains encryption settings and therefore cannot be used as a data disk” が表示される場合があります。 この状況では、次のスクリプトを実行して、ディスクのアタッチを再試行します。

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Blob イメージから復元された VM にマネージド ディスクをアタッチすることはできません。

3. ディスクがアタッチされたら、復旧 VM へのリモート デスクトップ接続を行って、いくつかの Azure PowerShell スクリプトを実行できるようにします。 復旧 VM 上に[最新バージョンの Azure PowerShell](/powershell/azure/) がインストールされていることを確認します。

4. 管理者特権の Azure PowerShell セッション (管理者として実行) を開きます。 次のコマンドを実行して、Azure サブスクリプションにサインインします。

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. 次のスクリプトを実行して、BEK ファイルの名前を確認します。

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    出力データの例を次に示します。 この場合、ファイル名が EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK であると想定しています。

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    2 つの重複するボリュームが表示されている場合は、タイムスタンプが新しいほうのボリュームが、復旧 VM で使用される現在の BEK ファイルになります。

    **Content Type** の値が **Wrapped BEK** の場合は、「[Key Encryption Key (KEK) のシナリオ](#key-encryption-key-scenario)」に進んでください。

    これで、ドライブ用の BEK ファイルの名前がわかったので、secret-file-name.BEK ファイルを作成して、BEK ファイル ドライブのロックを解除する必要があります。

6.  BEK ファイルを復元ディスクにダウンロードします。 次のサンプルは、BEK ファイルを C:\BEK フォルダーに保存します。 スクリプトを実行する前に、`C:\BEK\` パスが存在することを確認します。

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  アタッチされたディスクのロックを BEK ファイルを使用して解除するには、次のコマンドを実行します。

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    この例では、アタッチされた OS ディスクはドライブ F です。適切なドライブ文字を使用していることを確認してください。 

8. BEK キーを使用してディスクが正常にロック解除された後、そのディスクを復旧 VM からデタッチし、この新しい OS ディスクを使用して VM を再作成します。

    > [!NOTE]
    > ディスクの暗号化を使用している VM では、OS ディスクのスワップはサポートされません。

9. それでも新しい VM が正常に起動しない場合は、ドライブのロックを解除した後、次のいずれかの手順を試してください。

    - 保護を中断して、次のように実行して BitLocker を一時的に無効にします。

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - ドライブを完全に復号化します。 そのためには、次のコマンドを実行します。

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Key Encryption Key のシナリオ

Key Encryption Key のシナリオでは、次の手順に従います。

1. **[ユーザー] > [キーのアクセス許可] > [暗号化操作] > [Unwrap Key]\(キーのラップ解除\)** で、ログインするユーザー アカウントでは、Key Vault アクセス ポリシーの "ラップ解除" アクセス許可が必要であることを確認します。
2. 次のスクリプトを .PS1 ファイルに保存します。

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. パラメーターを設定します。 このスクリプトは、KEK シークレットを処理して BEK キーを作成した後、それを復旧 VM 上のローカル フォルダーに保存します。 スクリプトの実行時にエラーが発生した場合は、「[スクリプトのトラブルシューティング](#script-troubleshooting)」セクションを参照してください。

4. スクリプトが開始されると、次の出力が表示されます。

    GAC バージョンの場所                                                                              
    ---    -------        --------                                                                              
    False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...False  v4.0.30319     C:\Program Files\WindowsPowerShell\Modules\Az.Accounts\...

    スクリプトが終了すると、次の出力が表示されます。

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. アタッチされたディスクのロックを BEK ファイルを使用して解除するには、次のコマンドを実行します。

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    この例では、アタッチされた OS ディスクはドライブ F です。適切なドライブ文字を使用していることを確認してください。 

6. BEK キーを使用してディスクが正常にロック解除された後、そのディスクを復旧 VM からデタッチし、この新しい OS ディスクを使用して VM を再作成します。 

    > [!NOTE]
    > ディスクの暗号化を使用している VM では、OS ディスクのスワップはサポートされません。

7. それでも新しい VM が正常に起動しない場合は、ドライブのロックを解除した後、次のいずれかの手順を試してください。

    - 保護を中断して、次のコマンドを実行して BitLocker を一時的に無効にします。

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - ドライブを完全に復号化します。 そのためには、次のコマンドを実行します。

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>スクリプトのトラブルシューティング

**エラー:Could not load file or assembly (ファイルまたはアセンブリを読み込めませんでした)**

このエラーは、ADAL アセンブリのパスが間違っているために発生します。 `Az.Accounts` フォルダーを検索して正しいパスを検索することができます。

**エラー:Get-AzKeyVaultSecret or Get-AzKeyVaultSecret is not recognized as the name of a cmdlet (Get-AzKeyVaultSecret または Get-AzKeyVaultSecret はコマンドレットの名前として認識されません)**

以前の AZ PowerShell モジュールを使用している場合、2 つのコマンドを `Get-AzureKeyVaultSecret` と `Get-AzureKeyVaultSecret` に変更する必要があります。

**パラメーターのサンプル**

| パラメーター  | 値のサンプル  |説明   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | キーを格納するキー コンテナーの名前 |
|$kekName   |mykey   | VM の暗号化に使用されるキーの名前|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | VM キーのシークレットの名前|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D.BEK |BEK ファイルの書き込みパス。|
|$adTenant  |contoso.onmicrosoft.com   | キー コンテナーをホストする Azure Active Directory の FQDN または GUID |
