---
title: Azure PowerShell を使用して Azure Attestation を設定する
description: Azure PowerShell を使用して構成証明プロバイダーを設定、構成する方法。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: cbc415411e05d6fdecee1acf2fbc02b3c170b9d6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501126"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Azure Attestation を設定する

以下の手順に沿って、Azure PowerShell を使用して構成証明プロバイダーを作成、構成します。 Azure PowerShell をインストールして実行する方法については、[Azure PowerShell の概要](/powershell/azure/)に関するページを参照してください。

PowerShell ギャラリーには、非推奨のトランスポート層セキュリティ (TLS) バージョン 1.0 および 1.1 が存在するので注意してください。 TLS 1.2 以降のバージョンが推奨されます。 したがって、次のエラーが表示される場合があります。

- 警告:パッケージ ソース 'https://www.powershellgallery.com/api/v2 ' を解決できません
- PackageManagement\Install-Package: 指定された検索条件とモジュール名に一致するものが見つかりませんでした 

PowerShell ギャラリーの操作を続行するには、Install-Module コマンドの前に次のコマンドを実行してください。

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Az.Attestation PowerShell モジュールをインストールする

Azure PowerShell がインストールされているマシンに、Azure Attestation 用のコマンドレットを含んだ Az.Attestation PowerShell モジュールをインストールします。  

### <a name="initial-installation"></a>初期インストール

既存の PowerShell ウィンドウはすべて終了してください。

"現在のユーザー" を対象にインストールする場合は、管理者特権を使わずに PowerShell ウィンドウを起動して、次を実行します。

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

"すべてのユーザー" を対象にインストールする場合は、管理者特権の PowerShell ウィンドウを起動して、次を実行します。

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

管理者特権の PowerShell コンソールを閉じます。

### <a name="update-the-installation"></a>インストールを更新する

既存の PowerShell ウィンドウはすべて終了してください。

"現在のユーザー" を対象に更新する場合は、管理者特権を使わずに PowerShell ウィンドウを起動して、次を実行します。

```powershell
Update-Module -Name Az.Attestation
```

"すべてのユーザー" を対象に更新する場合は、管理者特権の PowerShell ウィンドウを起動して、次を実行します。

```powershell
Update-Module -Name Az.Attestation
```

管理者特権の PowerShell コンソールを閉じます。

### <a name="get-installed-modules"></a>インストールされているモジュールを取得する

構成証明の操作をサポートするうえで必要な Az モジュールの最小バージョン:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

すべての Az モジュールについてインストールされているバージョンを確認するには、次のコマンドを実行します。 

```powershell
Get-InstalledModule
```
バージョンが最小要件を満たしていない場合は、Update-Module コマンドを実行します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

(昇格されたアクセス特権を使わず) PowerShell コンソールで Azure にサインインします。

```powershell
Connect-AzAccount
```

必要に応じて、Azure Attestation に使用するサブスクリプションに切り替えます。

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Microsoft.Attestation リソース プロバイダーを登録する

Microsoft.Attestation リソース プロバイダーをサブスクリプションに登録します。 Azure リソース プロバイダーについて、またリソース プロバイダーを構成、管理する方法について詳しくは、「[Azure リソース プロバイダーと種類](../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。 リソース プロバイダーを登録する必要があるのは、サブスクリプションにつき 1 回のみであることに注意してください。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Azure Attestation を利用できるリージョン

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Azure リソース グループの作成

構成証明プロバイダー用のリソース グループを作成します。 同じリソース グループに他の Azure リソース (クライアント アプリケーション インスタンスがインストールされている仮想マシンを含む) を配置できることに注意してください。

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>構成証明プロバイダーを作成して管理する

New-AzAttestation によって構成証明プロバイダーが作成されます。

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile は、信頼済みの一連の署名キーを指定するファイルです。 PolicySignerCertificateFile パラメーターにファイル名を指定した場合、構成証明プロバイダーの構成には必ず、署名された JWT 形式のポリシーを使用する必要があります。 それ以外の場合は、テキストまたは署名されていない JWT 形式でポリシーを構成することができます。

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

PolicySignersCertificateFile のサンプルについては、[ポリシー署名者証明書の例](policy-signer-examples.md)を参照してください。

Get-AzAttestation では、ステータスや AttestURI など、構成証明プロバイダーのプロパティを取得します。 AttestURI は書き留めておいてください。後で必要になります。

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

上のコマンドからは、次のような出力が生成されます。 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

構成証明プロバイダーは、Remove-AzAttestation コマンドレットを使用して削除できます。  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>ポリシー管理

ポリシーを管理するには、Azure AD ユーザーに "Actions" の次のアクセス許可が必要になります。
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

これらのアクセス許可は、"所有者" (ワイルドカードのアクセス許可)、"共同作成者" (ワイルドカードのアクセス許可)、"構成証明の共同作成者" (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。  

ポリシーを読み込むには、Azure AD ユーザーに "Actions" の次のアクセス許可が必要になります。
- Microsoft.Attestation/attestationProviders/attestation/read

このアクセス許可は、"閲覧者" (ワイルドカードのアクセス許可) や "構成証明リーダー" (Azure Attestation 専用のアクセス許可) などのロールを経由して AD ユーザーに割り当てることができます。

構成証明プロバイダー用のポリシーの管理は、以下の PowerShell コマンドレットで行うことができます (一度に 1 TEE)。

Get-AzAttestationPolicy からは、指定の TEE の現行ポリシーが返されます。 このコマンドレットでは、ポリシーのテキストと JWT 形式の両方でポリシーが表示されます。

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

サポートされる TEE タイプは "SgxEnclave"、"OpenEnclave"、および "VbsEnclave" です。

Set-AttestationPolicy では、指定された TEE の新しいポリシーを設定します。 このコマンドレットは、テキストまたは JWT 形式のポリシーを受け取り、PolicyFormat パラメーターで制御されます。 PolicyFormat の既定値は "Text" です。 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

構成証明プロバイダーの作成中に PolicySignerCertificateFile が指定された場合、ポリシーの構成は必ず、署名された JWT 形式で行う必要があります。 それ以外の場合は、テキストまたは署名されていない JWT 形式でポリシーを構成することができます。

JWT 形式の構成証明ポリシーには、"AttestationPolicy" という名前の要求が含まれている必要があります。 署名済みのポリシーの場合、JWT は、既存のポリシー署名者証明書のいずれかに対応する秘密キーで署名する必要があります。

ポリシーのサンプルについては、「[構成証明ポリシーの例](policy-examples.md)」を参照してください。

Reset-AzAttestationPolicy では、指定された TEE の既定値にポリシーをリセットします。

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>ポリシー署名者証明書の管理

構成証明プロバイダー用のポリシー署名者証明書の管理は、以下の PowerShell コマンドレットで行うことができます。

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

ポリシー署名者証明書は、"maa-policyCertificate" という名前の要求を含んだ署名済みの JWT です。 要求の値は、追加する信頼済み署名キーを含んだ JWK です。 この JWT は、既存のポリシー署名者証明書のいずれかに対応する秘密キーで署名されている必要があります。

ポリシー署名者証明書のセマンティック操作はすべて、PowerShell の外で実行しなければならないことに注意してください。 PowerShell からは、単純な文字列と見なされます。

ポリシー署名者証明書のサンプルについては、[ポリシー署名者証明書の例](policy-signer-examples.md)を参照してください。

コマンドレットとそのパラメーターの詳細については、[Azure Attestation PowerShell のコマンドレット](/powershell/module/az.attestation/#attestation)を参照してください。 

## <a name="next-steps"></a>次のステップ

- [構成証明ポリシーを作成して署名する方法](author-sign-policy.md)
- [コード サンプルを使用して SGX エンクレーブの構成証明を行う](/samples/browse/?expanded=azure&terms=attestation)
