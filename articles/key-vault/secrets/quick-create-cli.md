---
title: クイックスタート - Azure Key Vault との間でシークレットの設定と取得を行う
description: Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う方法を紹介したクイック スタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: c2c6d106a198445fdbd08fbaeb7d03472b688b62
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071377"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Key Vault との間でシークレットの設定と取得を行う

このクイックスタートでは、Azure CLI を使用して Azure Key Vault にキー コンテナーを作成します。 Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 Azure CLI は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 この作業を完了したら、シークレットを格納します。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - このクイックスタートには、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Key Vault を作成します

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="add-a-secret-to-key-vault"></a>Key Vault にシークレットを追加する

シークレットをコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 このパスワードは、アプリケーションによって使用される可能性があります。 パスワードは **ExamplePassword** と呼ばれ、値 **hVFkk965BuUv** がその中に格納されます。

次のコマンドを入力して、値 **hVFkk965BuUv** を保存する **ExamplePassword** という Key Vault にシークレットを作成します。

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "ExamplePassword" --value "hVFkk965BuUv"
```

これで、Azure Key Vault に追加したパスワードは、その URI を使用すると参照できます。 現在のバージョンを取得するには、 **"https://<your-unique-keyvault-name>.vault.azure.net/secrets/ExamplePassword"** を使用します。

シークレットに格納されている値をプレーンテキストとして表示するには:

```azurecli
az keyvault secret show --name "ExamplePassword" --vault-name "<your-unique-keyvault-name>"
```

これで、キー コンテナーを作成し、シークレットを格納した後、取得しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Key Vault を作成してシークレットを格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure CLI az keyvault コマンド](/cli/azure/keyvault)のリファレンスを参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
