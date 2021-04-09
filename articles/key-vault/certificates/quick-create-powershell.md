---
title: クイックスタート - Azure PowerShell を使用して Azure Key Vault 証明書の設定と表示を行う
description: Azure PowerShell を使用して Azure Key Vault との間で証明書の設定と取得を行う方法を紹介したクイックスタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 587815cf9628df35f1e1efdbc6a7a3c89a27ed55
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99071919"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>クイック スタート:Azure PowerShell を使用して Azure Key Vault から証明書の設定と取得を行う

このクイックスタートでは、Azure PowerShell を使用して Azure Key Vault にキー コンテナーを作成します。 Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 Azure PowerShell は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 この作業を完了したら、証明書を格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 バージョンを確認するには、「`$PSVersionTable.PSVersion`」と入力します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-certificate-to-key-vault"></a>証明書を Key Vault に追加する

証明書をコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 この証明書は、アプリケーションによって使用される可能性があります。 

次のコマンドを入力して、**ExampleCertificate** というポリシーを含む自己署名証明書を作成します。

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

これで、URI を使用して、Azure Key Vault に追加したこの証明書を参照できるようになりました。 現在のバージョンを取得するには、 **"https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate"** を使用します。 

以前に格納した証明書を表示するには:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

これで、キー コンテナーを作成し、証明書を格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成して証明書を格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure PowerShell の Key Vault コマンドレット](/powershell/module/az.keyvault/)のリファレンスを参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
