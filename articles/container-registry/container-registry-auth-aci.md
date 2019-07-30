---
title: Azure Container Instances から Azure Container Registry の認証を受ける
description: Azure Active Directory サービス プリンシパルを使用して、Azure Container Instances からプライベート コンテナー レジストリ内のイメージへのアクセスを許可する方法を説明します。
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 04/23/2018
ms.author: danlep
ms.openlocfilehash: d2099de9ad909b23b79a92a831d7730b1cf126e3
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311636"
---
# <a name="authenticate-with-azure-container-registry-from-azure-container-instances"></a>Azure Container Instances から Azure Container Registry の認証を受ける

Azure Active Directory (Azure AD) サービス プリンシパルを使って、Azure Container Registry 内のプライベート コンテナー レジストリへのアクセスを提供できます。

この記事では、Azure AD サービス プリンシパルを作成し、レジストリへの*プル*権限を構成する方法を説明します。 その後で、認証用のサービス プリンシパルを使って、プライベート レジストリからそのイメージをプルする Azure Container Instances (ACI) のコンテナーを起動します。

## <a name="when-to-use-a-service-principal"></a>サービス プリンシパルを使う場合

サービス プリンシパルは、自動的にまたはそれ以外の無人の方法でコンテナー インスタンスを作成するアプリケーションやサービスなどの**ヘッドレス シナリオ**で ACI から認証を受けるために使う必要があります。

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

## <a name="next-steps"></a>次の手順

次の記事には、サービス プリンシパルと ACR の使用方法の詳細が含まれています。

* [サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)
* [Azure Kubernetes Service (AKS) から Azure Container Registry の認証を受ける](container-registry-auth-aks.md)

<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
