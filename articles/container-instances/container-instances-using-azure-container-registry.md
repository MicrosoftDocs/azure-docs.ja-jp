---
title: "Azure Container Registry から Azure Container Instances へのデプロイ | Azure Docs"
description: "Azure Container Registry から Azure Container Instances へのデプロイ"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/02/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: aa1c4ea379c10dff246e2f924a345f9fa444aa64
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---

# <a name="deploy-to-azure-container-instances-from-the-azure-container-registry"></a>Azure Container Registry から Azure Container Instances へのデプロイ

Azure Container Registry は、Docker コンテナー イメージ用の Azure ベースのプライベート レジストリです。 この記事では、Azure Container Registry に格納されているコンテナー イメージを Azure Container Instances にデプロイする方法について説明します。

## <a name="using-the-azure-cli"></a>Azure CLI の使用

Azure CLI には、Azure Container Instances でコンテナーを作成および管理するためのコマンドが含まれています。 `create` コマンドでプライベート イメージを指定する場合は、コンテナー レジストリでの認証に必要なイメージ レジストリ パスワードも指定できます。

```azurecli-interactive
az container create --name myprivatecontainer --image mycontainerregistry.azurecr.io/mycontainerimage:v1 --registry-password myRegistryPassword --resource-group myresourcegroup
```

`create` コマンドでは、`registry-login-server` と `registry-username` の指定もサポートされます。 ただし、Azure Container Registry のログイン サーバーは常に *registryname*.azurecr.io であり、既定のユーザー名は *registryname* であるため、これらの値が明示的に指定されていない場合はイメージ名から推測されます。

## <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

`imageRegistryCredentials` プロパティをコンテナー グループに含めることで、Azure Resource Manager テンプレート内に Azure Container Registry のプロパティを指定できます。

```json
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

コンテナー レジストリのパスワードをテンプレートに直接保存することを避けるため、パスワードを [Azure Key Vault](../key-vault/key-vault-manage-with-cli2.md) にシークレットとして保存し、[Azure Resource Manager と Key Vault のネイティブ統合](../azure-resource-manager/resource-manager-keyvault-parameter.md)を使用してテンプレート内で参照することをお勧めします。

## <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure Container Registry にコンテナー イメージを保持している場合は、Azure ポータルを使用して Azure Container Instances 内にコンテナーを簡単に作成できます。

1. Azure ポータルで、自分のコンテナー レジストリに移動します。

2. [リポジトリ] を選択します。

    ![Azure ポータルの Azure Container Registry メニュー][acr-menu]

3. デプロイ元となるリポジトリを選択します。

4. デプロイするコンテナー イメージのタグを右クリックします。

    ![Azure Container Instances でコンテナーを起動するためのコンテキスト メニュー][acr-runinstance-contextmenu]

5. コンテナーの名前とリソース グループの名前を入力します。 必要に応じて既定の値を変更することもできます。

    ![Azure Container Instances の [作成] メニュー][acr-create-deeplink]

6. デプロイが完了したら、通知ウィンドウからコンテナー グループに移動して、その IP アドレスとその他のプロパティを確認できます。

    ![Azure Container Instances のコンテナー グループの詳細ビュー][aci-detailsview]

## <a name="next-steps"></a>次のステップ

[チュートリアルを完了](container-instances-tutorial-prepare-app.md)することで、コンテナーを作成し、そのコンテナーをプライベート コンテナー レジストリにプッシュして、Azure Container Instances にデプロイします。

<!-- IMAGES -->
[acr-menu]: ./media/container-instances-using-azure-container-registry/acr-menu.png

[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png

[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png

