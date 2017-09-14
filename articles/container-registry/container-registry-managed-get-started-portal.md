---
title: "プライベート Docker レジストリの作成 - Azure Portal | Microsoft Docs"
description: "Azure Portal を使用したプライベート Docker コンテナー レジストリの作成と管理について、概要を説明します。"
services: container-registry
documentationcenter: 
author: neilpeterson
manager: timlt
editor: na
tags: 
keywords: 
ms.assetid: 53a3b3cb-ab4b-4560-bc00-366e2759f1a1
ms.service: container-registry
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: nepeters
ms.custom: na
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 560aee42b0c5a61c37c594d7937f833ab7183d49
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---

# <a name="create-a-managed-container-registry-using-the-azure-portal"></a>管理されたコンテナー レジストリを Azure Portal で作成する

Azure Container Registry は、プライベート Docker コンテナー イメージを保管するための管理された Docker コンテナー レジストリ サービスです。 このガイドでは、管理された Azure Container Registry インスタンスを Azure Portal で作成する方法について詳しく説明します。

管理された Azure Container Registry はプレビュー段階であり、一部のリージョンでは利用できません。

## <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal (http://portal.azure.com) にログインします。

## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

1. Azure ポータルの左上隅にある **[新規]** ボタンをクリックします。

2. Marketplace で **Azure Container Registry** を探して選択します。

3. **[作成]** をクリックして、ACR の作成ブレードを開きます。

    ![Container Registry の設定](./media/container-registry-get-started-portal/managed-container-registry-settings.png)

4. **[Azure コンテナー レジストリ]** ブレードで、次の情報を入力します。 完了したら、**[作成]** をクリックします。

    a. **[Registry name (レジストリ名)]** - 特定のレジストリのグローバルに一意のトップレベル ドメイン名。 この例のレジストリの名前は *myAzureContainerRegistry1* ですが、独自の一意の名前に置き換えてください。 名前に使用できるのは、アルファベットと数字のみです。

    b. **[リソース グループ]** - 既存の[リソース グループ](../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。

    c. **[場所]** - サービスを[利用可能](https://azure.microsoft.com/regions/services/)な Azure データセンターの場所を選択します (例: **米国中南部**)。

    d. **[Admin user (管理者ユーザー)]** - 必要に応じて、レジストリにアクセスする管理者ユーザーを有効にします。 この設定は、レジストリの作成後に変更できます。

    e. **[Use managed registry]\(管理されたレジストリの使用\)**: レジストリ ストレージの管理を ACR で自動化すると共に、webhook および AAD 認証を使用する場合は、[はい] を選択します。

    f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[価格レベル]**: 価格レベルを選択します。詳細については、ACR の価格に関する情報を参照してください。

## <a name="log-in-to-acr-instance"></a>ACR インスタンスへのログイン

コンテナー イメージをプッシュしたりプルしたりするには、あらかじめ ACR インスタンスにログインしておく必要があります。 

そのためには、Azure CLI 2.0 を使用します。 まず必要に応じて、[az login](/cli/azure/#login) コマンドで Azure にログインします。 

```azurecli
az login
```

次に、[az acr login](/cli/azure/acr#login) コマンドで Azure Container Registry にログインします。

```azurecli-interactive
az acr login --name myAzureContainerRegistry1
```

## <a name="use-azure-container-registry"></a>Azure Container Registry の使用

### <a name="list-container-images"></a>コンテナー イメージの一覧表示

`az acr` CLI コマンドを使用して、リポジトリ内のイメージとタグを照会します。

> [!NOTE]
> 現在、Container Registry では、`docker search` コマンドによるイメージとタグの照会をサポートしていません。

### <a name="list-repositories"></a>リポジトリの一覧を表示する

次の例では、JSON (JavaScript Object Notation) 形式でレジストリ内のリポジトリの一覧を表示します。

```azurecli
az acr repository list -n myContainerRegistry1 -o json
```

### <a name="list-tags"></a>タグの一覧を表示する

次の例では、JSON 形式で **samples/nginx** リポジトリのタグの一覧を表示します。

```azurecli
az acr repository show-tags -n myContainerRegistry1 --repository samples/nginx -o json
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、管理された Azure Container Registry インスタンスを Azure Portal で作成しました。

> [!div class="nextstepaction"]
> [Docker CLI を使用した最初のイメージのプッシュ](container-registry-get-started-docker-cli.md)
