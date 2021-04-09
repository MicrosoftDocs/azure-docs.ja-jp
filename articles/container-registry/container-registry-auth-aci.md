---
title: Container Instances からのアクセス
description: Azure Active Directory サービス プリンシパルを使用して、Azure Container Instances からプライベート コンテナー レジストリ内のイメージへのアクセスを許可する方法を説明します。
ms.topic: article
ms.date: 04/23/2018
ms.openlocfilehash: b1bc8119c495dea99c6bdc4923db198d041a1e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456505"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Azure Container Instances から Azure Container Registry の認証を受ける

Azure Active Directory (Azure AD) サービス プリンシパルを使って、Azure Container Registry 内のプライベート コンテナー レジストリへのアクセスを提供できます。

この記事では、Azure AD サービス プリンシパルを作成し、レジストリへの *プル* 権限を構成する方法を説明します。 その後で、認証用のサービス プリンシパルを使って、プライベート レジストリからそのイメージをプルする Azure Container Instances (ACI) のコンテナーを起動します。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、自動的にまたはそれ以外の無人の方法でコンテナー インスタンスを作成するアプリケーションやサービスなどの **ヘッドレス シナリオ** で ACI から認証を受けるために使う必要があります。

たとえば、夜間に実行され、何らかのデータを処理する[タスク ベースのコンテナー インスタンス](../container-instances/container-instances-restart-policy.md)を作成する自動スクリプトがある場合、このスクリプトでは、プルのみのアクセス許可を持つサービス プリンシパルを使ってレジストリへの認証を受けることができます。 その後、サービス プリンシパルの資格情報を交換したり、他のサービスやアプリケーションにまったく影響を与えずにそのアクセスを取り消したりできます。

サービス プリンシパルは、レジストリの [admin ユーザー](container-registry-authentication.md#admin-account)が無効のときにも使う必要があります。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="authenticate-using-the-service-principal"></a>サービス プリンシパルを使って認証を受ける

サービス プリンシパルを使って Azure Container Instances 内のコンテナーを起動するには、`--registry-username` でその ID、`--registry-password` でそのパスワードを指定します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerregistry.azurecr.io/myimage:v1 \
    --registry-login-server mycontainerregistry.azurecr.io \
    --registry-username <service-principal-ID> \
    --registry-password <service-principal-password>
```

## <a name="sample-scripts"></a>サンプルのスクリプト

Azure CLI の以前のサンプル スクリプトを GitHub 上で検索できます。各バージョンのAzure PowerShell についても同様です。

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>次のステップ

次の記事には、サービス プリンシパルと ACR の使用方法の詳細が含まれています。

* [サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)
* [Azure Kubernetes Service (AKS) から Azure Container Registry の認証を受ける](../aks/cluster-container-registry-integration.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
