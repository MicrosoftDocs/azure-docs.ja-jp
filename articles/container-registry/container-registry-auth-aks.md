---
title: Azure Kubernetes Service から Azure Container Registry の認証を受ける
description: Azure Active Directory サービス プリンシパルを使用して、Azure Kubernetes Service からプライベート コンテナー レジストリ内のイメージへのアクセスを許可する方法を説明します。
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: 1d7e130d619f580aeb82939e19ea5abf680ff039
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326478"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service から Azure Container Registry の認証を受ける

Azure Kubernetes Service (AKS) で Azure Container Registry (ACR) を使用する場合は、認証メカニズムを確立する必要があります。 この記事では、この 2 つの Azure サービス間で認証を行う場合に推奨される構成について詳しく説明します。

この記事では、AKS クラスターを既に作成しており、`kubectl` コマンド ライン クライアントを使用してクラスターにアクセスできることを前提としています。 

## <a name="grant-aks-access-to-acr"></a>ACR へのアクセス許可を AKS に付与する

AKS クラスターを作成するとき、Azure は他の Azure リソースとのクラスター運用性をサポートするサービス プリンシパルも作成します。 この自動生成されるサービス プリンシパルは、ACR レジストリでの認証に使用できます。 これを行うには、コンテナー レジストリに対するクラスターのサービス プリンシパルのアクセスを付与する Azure AD の[ロールの割り当て](../role-based-access-control/overview.md#role-assignments)を作成する必要があります。

AKS によって生成された、Azure Container Registry に対するサービス プリンシパルのプル アクセスを付与するには、次のスクリプトを使用します。 スクリプトを実行する前に、環境に合わせて `AKS_*` および `ACR_*` 変数を変更してください。

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Kubernetes シークレットを使用したアクセス

場合によっては、自動生成された AKS サービス プリンシパルに対して、ACR へのアクセスを付与するために必要なロールを割り当てられないことがあります。 たとえば、組織のセキュリティ モデルにより、AKS によって生成されたサービス プリンシパルにロールを割り当てるための十分なアクセス許可が Azure Active Directory テナントに存在しない場合があります。 サービス プリンシパルにロールを割り当てるには、Azure AD アカウントに Azure AD テナントへの書き込みアクセス許可が付与されている必要があります。 アクセス許可がない場合は、新しいサービス プリンシパルを作成し、Kubernetes のイメージ プル シークレットを使用してコンテナー レジストリへのアクセスを付与することができます。

新しいサービス プリンシパルを作成するには次のスクリプトを使用します (この資格情報を Kubernetes のイメージ プル シークレット用に使用します)。 スクリプトを実行する前に、環境に合わせて `ACR_NAME` 変数を変更してください。

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

これで、サービス プリンシパルの資格情報を Kubernetes の[イメージ プル シークレット][image-pull-secret]に格納し、AKS クラスターがコンテナーを実行するときに参照できるようになりました。

次の **kubectl** コマンドを使用して、Kubernetes シークレットを作成します。 `<acr-login-server>` を Azure コンテナー レジストリの完全修飾名に置き換えます ("acrname.azurecr.io" という形式になります)。 `<service-principal-ID>` および `<service-principal-password>` を、前のスクリプトを実行して取得した値に置き換えます。 `<email-address>` を整形式の任意の電子メール アドレスに置き換えます。

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

`imagePullSecrets` パラメーターに名前を指定することで (この場合は、"acr auth")、ポッドのデプロイメントで Kubernetes シークレットを使用できるようになりました。

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
