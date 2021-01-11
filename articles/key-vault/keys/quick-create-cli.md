---
title: Azure Key Vault でキーの属性を作成し、取得する - Azure CLI
description: Azure CLI を使用して Azure Key Vault との間でキーの設定と取得を行う方法を紹介するクイック スタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: e59482c3dbc4bd0abb20a5e9094aad5f31c358cb
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87497411"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Key Vault との間でキーの設定と取得を行う

このクイックスタートでは、Azure CLI を使用して Azure Key Vault にキー コンテナーを作成します。 Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 Azure CLI は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 この作業を完了したら、キーを格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*ContosoResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault の作成

次に、前の手順で作成したリソース グループに Key Vault を作成します。 いくつかの情報を指定する必要があります。

- このクイック スタートでは、**Contoso-vault2** を使用します。 テストでは一意の名前を指定する必要があります。
- リソース グループ名: **ContosoResourceGroup**
- 場所: **米国東部**

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **Vault Name**:この例では、これは **Contoso-Vault2** です。 この名前を他の Key Vault コマンドに使用できます。
- **Vault URI (コンテナー URI)** :この例では、これは https://contoso-vault2.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="add-a-key-to-key-vault"></a>Key Vault にキーを追加する

キーをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 このキーは、アプリケーションによって使用される可能性があります。 

次のコマンドを入力して、呼び出される **ExampleKey** を作成します。

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

これで、Azure Key Vault に追加したキーは、その URI を使用すると参照できます。 **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** を使用して、現在のバージョンを取得します。 

以前に格納したキーを表示するには:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

これで、キー コンテナーを作成し、キーを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループおよびすべての関連リソースを削除できます。 次のように、リソースを削除できます。

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成してキーを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault?view=azure-cli-latest)のリファレンスを参照する
- [Azure Key Vault のベスト プラクティス](../general/best-practices.md)を確認する
