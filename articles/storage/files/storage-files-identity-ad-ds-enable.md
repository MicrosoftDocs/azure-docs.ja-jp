---
title: Azure ファイル共有への AD DS 認証を有効にする
description: SMB を使用して Azure ファイル共有へのオンプレミスの Active Directory Domain Services 認証を有効にする方法について説明します。 ドメインに参加している Windows 仮想マシンでは、AD DS 資格情報を使用して Azure ファイル共有にアクセスできます。
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 5ee4481b3151e28d5d37760e486a43adbc194994
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553223"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>パート 1: Azure ファイル共有に対する AD DS 認証を有効にする 

Active Directory Domain Services (AD DS) 認証を有効にする前に、[概要の記事](storage-files-identity-auth-active-directory-enable.md)を読んで、サポートされているシナリオと要件を理解してください。

この記事では、ストレージ アカウントで Active Directory Domain Services (AD DS) 認証を有効にするために必要なプロセスについて説明します。 この機能を有効にした後、AD DS の資格情報を使用して Azure ファイル共有に対して認証するために、ストレージ アカウントと AD DS を構成する必要があります。 Azure ファイル共有に対する SMB 経由の AD DS 認証を有効にするには、ストレージ アカウントを AD DS に登録してから、ストレージ アカウントに必要なドメイン プロパティを設定する必要があります。

ストレージ アカウントを AD DS に登録するには、それを表すアカウントを AD DS 内に作成します。 このプロセスは、オンプレミスの Windows ファイル サーバーを表すアカウントを AD DS 内に作成するのと同じように考えることができます。 機能は、ストレージ アカウントで有効になると、アカウント内のすべての新規および既存のファイル共有に適用されます。

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>オプション 1 (推奨):AzFilesHybrid PowerShell モジュールを使用する

AzFilesHybrid PowerShell モジュールのコマンドレットによって、ユーザーの代わりに必要な変更が行われ、機能が有効になります。 コマンドレットの一部はオンプレミスの AD DS と相互作用するため、ここではコマンドレットの動作について説明します。これにより、変更がコンプライアンス ポリシーとセキュリティ ポリシーに適合するかどうかを判断し、コマンドレットを実行するための適切なアクセス許可を持っていることを確実にすることができます。 AzFilesHybrid モジュールの使用をお勧めしますが、使用できない場合は、手動で実行するための手順を用意しています。

### <a name="download-azfileshybrid-module"></a>AzFilesHybrid モジュールをダウンロードする

- [AzFilesHybrid モジュール (GA モジュール: v0.2.0+) をダウンロードして解凍する](https://github.com/Azure-Samples/azure-files-samples/releases)AES 256 Kerberos 暗号化は、v0.2.2 以上でサポートされることに注意してください。 この機能を 0.2.2 未満の AzFilesHybrid バージョンで有効にし、AES 256 Kerberos 暗号化がサポートされるように更新する場合は、[この記事](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)をご覧ください。 
- ターゲット AD でサービス ログオン アカウントまたはコンピューター アカウントを作成する権限がある AD DS 資格情報を使用して、オンプレミスの AD DS に参加しているデバイスにモジュールをインストールして実行します。
-  Azure AD に同期されているオンプレミスの AD DS 資格情報を使用して、スクリプトを実行します。 オンプレミスの AD DS 資格情報には、ストレージ アカウント所有者または共同作成者の Azure ロールのアクセス許可が必要です。

### <a name="run-join-azstorageaccountforauth"></a>Join-AzStorageAccountForAuth を実行する

`Join-AzStorageAccountForAuth` コマンドレットでは、指定されたストレージ アカウントに代わってオフライン ドメイン参加に相当することを行います。 このスクリプトでは、コマンドレットを使用して、AD ドメインに[コンピューター アカウント](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)を作成します。 何らかの理由でコンピューター アカウントを使用できない場合は、代わりに[サービス ログオン アカウント](/windows/win32/ad/about-service-logon-accounts)を作成するようにスクリプトを変更できます。 このコマンドを手動で実行する場合は、環境に最も適したアカウントを選択する必要があります。

コマンドレットによって作成された AD DS アカウントは、ストレージ アカウントを表します。 AD DSアカウントが、パスワードの有効期限を適用する組織単位 (OU) の下で作成されている場合は、パスワードの有効期間の前にパスワードを更新する必要があります。 その日付の前までにアカウント パスワードが更新されない場合は、Azure ファイル共有にアクセスするときに認証エラーが発生します。 パスワードを更新する方法については、[AD DS アカウント パスワードを更新する](storage-files-identity-ad-ds-update-password.md)方法に関する記事を参照してください。

PowerShell で実行する前に、必ず、以下のパラメーターのプレースホルダーの値を実際の値に置き換えてください。
> [!IMPORTANT]
> ドメイン参加コマンドレットは、AD のストレージ アカウント (ファイル共有) を表す AD アカウントを作成します。 コンピューター アカウントまたはサービス ログオン アカウントのどちらとして登録するかを選択できます。詳細については、[FAQ](./storage-files-faq.md#security-authentication-and-access-control) を参照してください。 コンピューター アカウントの場合は、AD で 30 日に設定された既定のパスワードの有効期限が存在します。 同様に、サービス ログオン アカウントでも、既定のパスワードの有効期限が AD ドメインまたは組織単位 (OU) で設定されている可能性があります。
> どちらのアカウントの種類でも、AD 環境で構成されているパスワードの有効期限を確認し、パスワードの有効期間が終了する前に、AD アカウントの[ストレージ アカウント ID のパスワードを更新する](storage-files-identity-ad-ds-update-password.md)よう計画することをお勧めします。 [AD で新しい AD 組織単位 (OU) を作成](/powershell/module/addsadministration/new-adorganizationalunit)し、それぞれ[コンピューター アカウント](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11))またはサービス ログオン アカウントのパスワードの有効期限のポリシーを無効にすることを検討できます。 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>オプション 2:有効化アクションを手動で実行する

前述の `Join-AzStorageAccountForAuth` スクリプトを既に正常に実行している場合は、セクション[「機能が有効になっていることを確認する」](#confirm-the-feature-is-enabled)に進んでください。 次の手動の手順を実行する必要はありません。

### <a name="checking-environment"></a>環境の確認

まず、環境の状態を確認する必要があります。 具体的には、[Active Directory PowerShell](/powershell/module/addsadministration/) がインストールされているかどうかと、シェルが管理者特権で実行されているかどうかを確認する必要があります。 次に、[Az.Storage 2.0 モジュール](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)がインストールされているかどうかを確認し、まだの場合はインストールします。 これらの確認が完了したら、AD DS を確認し、[コンピューター アカウント](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (既定値)、あるいは "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net" などの SPN または UPN を使用して既に作成されている[サービス ログオン アカウント](/windows/win32/ad/about-service-logon-accounts)があるかどうかを確認します。 アカウントが存在しない場合は、次のセクションの説明に従って作成します。

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>AD でのストレージ アカウントを表す ID の手動による作成

このアカウントを手動で作成するには、ストレージ アカウント用の新しい Kerberos キーを作成します。 次に、以下の PowerShell コマンドレットを使用して、その Kerberos キーをアカウントのパスワードとして使用します。 このキーは、設定時にのみ使われ、ストレージ アカウントに対する制御やデータ プレーンの操作には使用できません。 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

そのキーを作成したら、OU の下にサービスまたはコンピューター アカウントを作成します。 次の仕様を使用します (テキスト例は実際のストレージ アカウント名に置き換えてください)。

SPN: "cifs/ここはご利用のストレージ アカウントの名前.file.core.windows.net"。パスワード: ストレージ アカウントの Kerberos キー。

OU でパスワードの有効期限が適用されている場合は、パスワードの有効期間の前にパスワードを更新し、Azure ファイル共有にアクセスするときの認証エラーを防ぐ必要があります。 詳細については、「[AD のストレージ アカウント ID のパスワードを更新する](storage-files-identity-ad-ds-update-password.md)」を参照してください。

新しく作成された ID の SID を保持します。これは次の手順で必要になります。 ストレージ アカウントを表す、作成した ID を Azure AD に同期する必要はありません。

### <a name="enable-the-feature-on-your-storage-account"></a>ストレージ アカウントで機能を有効にする

これで、ストレージ アカウントで機能を有効にすることができるようになりました。 次のコマンドでドメイン プロパティの構成の詳細をいくつか指定してから実行します。 次のコマンドで必要なストレージ アカウント SID は、[前のセクション](#creating-an-identity-representing-the-storage-account-in-your-ad-manually)の AD DS で作成した ID の SID です。

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>デバッグ

Debug-AzStorageAccountAuth コマンドレットを実行し、ログオンした AD ユーザーで AD の構成に対する一連の基本的なチェックを実行します。 このコマンドレットは、AzFilesHybrid バージョン 0.1.2 以降でサポートされています。 このコマンドレットで実行されるチェックの詳細については、Windows のトラブルシューティング ガイドの「[AD 資格情報を使用して Azure Files をマウントできない](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)」を参照してください。

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>機能が有効になっていることを確認する

次のスクリプトを使用して、ストレージ アカウントで機能が有効になっているかどうかを確認することができます。

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

正常に終了した場合、出力は次のようになります。

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>次のステップ

これで、ストレージ アカウントで機能が正常に有効になりました。 この機能を使用するには、共有レベルのアクセス許可を割り当てる必要があります。 次のセクションに進みます。

[パート 2: ID に共有レベルのアクセス許可を割り当てる](storage-files-identity-ad-ds-assign-permissions.md)