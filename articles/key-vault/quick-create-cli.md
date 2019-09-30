---
title: クイック スタート:Azure Key Vault との間でシークレットの設定と取得を行う
description: Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う方法を紹介したクイック スタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019
ms.date: 09/03/2019
ms.author: mbaldwin
ms.openlocfilehash: 70c450304c13a5c4b453226140ae699e0cabda19
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744767"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う

Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](key-vault-overview.md)に関するページを参照してください。 Azure CLI は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 このクイック スタートでは、キー コンテナーを作成します。 この作業を完了したら、シークレットを格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートでは、Azure CLI バージョン 2.0.4 以降を実行する必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

CLI を使用して Azure にサインインするには、次のように入力します。

```azurecli
az login
```

CLI を使用したログイン オプションの詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)」を参照してください

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*ContosoResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Key Vault の作成

次に、前の手順で作成したリソース グループに Key Vault を作成します。 いくつかの情報を指定する必要があります。

- このクイック スタートでは、**Contoso-vault2** を使用します。 テストでは一意の名前を指定する必要があります。
- リソース グループ名: **ContosoResourceGroup**。
- 場所: **米国東部**。

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

このコマンドレットの出力では、新しく作成したキー コンテナーのプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **Vault Name**:この例では、これは **Contoso-Vault2** です。 この名前を他の Key Vault コマンドに使用できます。
- **Vault URI (コンテナー URI)** :この例では、これは https://contoso-vault2.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 このパスワードは、アプリケーションによって使用される可能性があります。 パスワードは **ExamplePassword** と呼ばれ、値 **hVFkk965BuUv** がその中に格納されます。

次のコマンドを入力して、値 **hVFkk965BuUv** を保存する **ExamplePassword** という Key Vault にシークレットを作成します。

```azurecli
az keyvault secret set --vault-name "Contoso-Vault2" --name "ExamplePassword" --value "hVFkk965BuUv"
```

これで、Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 **https://ContosoVault.vault.azure.net/secrets/ExamplePassword** を使用して、現在のバージョンを取得します。 

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "Contoso-Vault2"
```

これで、キー コンテナーを作成し、シークレットを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のクイックスタートとチュートリアルは、このクイックスタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
必要がなくなったら、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループおよびすべての関連リソースを削除できます。 次のように、リソースを削除できます。

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](key-vault-overview.md)を確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault?view=azure-cli-latest)のリファレンスを参照する
- [キー、シークレット、証明書](about-keys-secrets-and-certificates.md)について学習する
- [Azure Key Vault のベスト プラクティス](key-vault-best-practices.md)を確認する
