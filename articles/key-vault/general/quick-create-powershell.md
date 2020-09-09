---
title: クイック スタート - Azure PowerShell を使用して Azure Key Vault を作成する
description: Azure PowerShell を使用して Azure Key Vault を作成する方法を示すクイック スタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: e24f1e546de1ce01896e271dbc9155c47f6c2bb6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101324"
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

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Key Vault を作成します

前の手順のリソース グループにキー コンテナーを作成します。 いくつかの情報を指定する必要があります。

- キー コンテナー名:数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列

  > [!Important]
  > 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

- リソース グループ名: **myResourceGroup**
- 場所: **EastUS**。

```azurepowershell-interactive
New-AzKeyVault -Name "&lt;your-unique-key-vault-name&gt; -ResourceGroupName "myResourceGroup" -Location "East US"
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **Vault Name**:上の --name パラメーターに指定した名前です。
- **Vault URI (コンテナー URI)** :この例では、これは https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 他のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

必要がなくなったら、Azure PowerShell の [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用して、リソース グループとすべての関連リソースを削除できます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](overview.md)を確認する
- [Azure PowerShell の Key Vault コマンドレット](/powershell/module/az.keyvault/?view=azps-2.6.0#key_vault)のリファレンスを参照する
- [Azure Key Vault のベスト プラクティス](best-practices.md)を確認する
