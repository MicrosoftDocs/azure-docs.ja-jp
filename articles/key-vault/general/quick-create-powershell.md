---
title: クイック スタート - Azure PowerShell を使用して Azure Key Vault を作成する
description: Azure PowerShell を使用して Azure Key Vault を作成する方法を示すクイック スタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: e77493bc73bc2d6f590d9bdcf891171fbd71f74e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99070232"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>クイック スタート:PowerShell を使用してキー コンテナーを作成する

Azure Key Vault は、[キー](../keys/index.yml)、[シークレット](../secrets/index.yml)、[証明書](../certificates/index.yml)用の Secure Store を提供するクラウド サービスです。 Key Vault の詳細については、「[Azure Key Vault について](overview.md)」を参照してください。キー コンテナーに格納できる内容の詳細については、「[キー、シークレット、証明書について](about-keys-secrets-certificates.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

このクイック スタートでは、[Azure PowerShell](/powershell/azure/) を使用してキー コンテナーを作成します。 PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 バージョンを確認するには、「`$PSVersionTable.PSVersion`」と入力します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure PowerShell を使用してキー コンテナーを作成しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](overview.md)を確認する
- [Azure PowerShell の Key Vault コマンドレット](/powershell/module/az.keyvault/)のリファレンスを参照する
- 「[Azure Key Vault のセキュリティの概要](security-overview.md)」を確認する

