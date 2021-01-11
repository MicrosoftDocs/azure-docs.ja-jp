---
title: クイックスタート - Azure CLI を使用してキー コンテナーを作成する
description: Azure CLI を使用して Azure キー コンテナーを作成する方法を示すクイックスタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 07/20/2020
ms.author: mbaldwin
ms.openlocfilehash: f3b9758d52c4be8e4d85c398f5ef1d0b3fae7e86
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/03/2020
ms.locfileid: "87541806"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用してキー コンテナーを作成する

Azure Key Vault は、[キー](../keys/index.yml)、[シークレット](../secrets/index.yml)、[証明書](../certificates/index.yml)用の Secure Store を提供するクラウド サービスです。 Key Vault の詳細については、「[Azure Key Vault について](overview.md)」を参照してください。キー コンテナーに格納できる内容の詳細については、「[キー、シークレット、証明書について](about-keys-secrets-certificates.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

このクイックスタートでは、[Azure CLI](/cli/azure/) を使用してキー コンテナーを作成します。 Azure CLI は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。  CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*ContosoResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="create-a-key-vault"></a>Key Vault を作成します

前の手順のリソース グループにキー コンテナーを作成します。 いくつかの情報を指定する必要があります。

- キー コンテナー名:数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列

  > [!Important]
  > 各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

- リソース グループ名: **myResourceGroup**
- 場所: **EastUS**。

```azurecli
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "EastUS"
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **Vault Name**:上の --name パラメーターに指定した名前です。
- **Vault URI (コンテナー URI)** :この例では、これは https://&lt;your-unique-keyvault-name&gt;.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。

必要がなくなったら、Azure CLI の [az group delete](/cli/azure/group) コマンドを使用して、リソース グループおよびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成して削除しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](overview.md)を確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault?view=azure-cli-latest)のリファレンスを参照する
- [Azure Key Vault のベスト プラクティス](best-practices.md)を確認する
