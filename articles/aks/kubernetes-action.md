---
title: GitHub アクションを使用した Azure Kubernetes Service へのコンテナーのビルド、テスト、デプロイ
description: GitHub アクションを使用してコンテナーを Kubernetes にデプロイする方法について説明します
services: container-service
author: azooinmyluggage
ms.service: container-service
ms.topic: article
ms.date: 11/04/2019
ms.author: atulmal
ms.openlocfilehash: cc2d6df952b2e0aa9b9f4d4e1dcb4859a5bb3790
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74130533"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes Service にデプロイするための GitHub アクション

[GitHub アクション](https://help.github.com/en/articles/about-github-actions)を使用すると、自動化されたソフトウェア開発ライフサイクル ワークフローを柔軟に構築できます。 Kubernetes アクション [azure/aks-set-context@v1](https://github.com/Azure/aks-set-context) は、Azure Kubernetes Service クラスターへのデプロイを支援します。 このアクションによりターゲット AKS クラスター コンテキストが設定されます。これは、[azure/k8s-deploy](https://github.com/Azure/k8s-deploy/tree/master)、[azure/k8s-create-secret](https://github.com/Azure/k8s-create-secret/tree/master) などの他のアクションで使用されたり、kubectl コマンドを実行したりすることができます。

> [!IMPORTANT]
> GitHub Actions は現在ベータ版です。 まず、ご自分の GitHub アカウントを使用し、[新規登録し、プレビューに参加](https://github.com/features/actions)する必要があります。
> 

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

AKS をターゲットとするワークフローでは、ファイルに次の 3 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | プライベート コンテナー レジストリ (ACR) へのログイン |
|**ビルド** | コンテナー イメージのビルドとプッシュ  |
|**デプロイする** | 1.ターゲット AKS クラスターを設定する |
| |2.Kubernetes クラスターで汎用/docker-registry シークレットを作成する  |
||3.Kubernetes クラスターにデプロイする|

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

[Azure CLI](https://docs.microsoft.com/cli/azure/) の [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行できます。

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

上記のコマンドで、プレースホルダーを実際のサブスクリプション ID とリソース グループに置き換えます。 これにより、ご自分のリソースにアクセスするためのロールの割り当て資格情報が出力されます。 コマンドでは、次のような JSON オブジェクトが出力されます。

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
この JSON オブジェクトをコピーします。このオブジェクトは、GitHub に対する認証に使用します。

## <a name="configure-the-github-secrets"></a>GitHub シークレットを構成する

手順に従って、シークレットを構成します。

1. [GitHub](https://github.com/) でご自分のリポジトリを参照し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** を選択します。

    ![secrets](media/kubernetes-action/secrets.png)

2. 上記の `az cli` コマンドの内容を、シークレット変数の値として貼り付けます。 たとえば、「 `AZURE_CREDENTIALS` 」のように入力します。

3. 同様に、コンテナー レジストリの資格情報に次の追加シークレットを定義し、Docker のログイン アクションで設定します。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 定義されたシークレットは以下のように表示されます。

    ![kubernetes-secrets](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>コンテナー イメージをビルドして Azure Kubernetes Service クラスターにデプロイする

コンテナー イメージのビルドとプッシュは `Azure/docker-login@v1` アクションを使用して行われます。 AKS にコンテナー イメージをデプロイするには、`Azure/k8s-deploy@v1` アクションを使用する必要があります。 このアクションには、次の 5 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **namespace** | (省略可能) ターゲットの Kubernetes 名前空間を選択します。 名前空間が指定されていない場合、コマンドは既定の名前空間で実行されます | 
| **manifests** |  (必須) デプロイに使用されるマニフェスト ファイルへのパス |
| **images** | (省略可能) マニフェスト ファイルでの置換に使用されるイメージの完全修飾リソース URL |
| **imagepullsecrets** | (省略可能) クラスター内に既に設定されている docker-registry シークレットの名前。 これらの各シークレット名は、入力マニフェスト ファイルにあるワークロードの imagePullSecrets フィールドの下に追加されます |
| **kubectl-version** | (省略可能) 特定のバージョンの kubectl バイナリをインストールします |

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにデプロイする

コンテナー イメージをビルドし、Azure Kubernetes Service クラスターにデプロイするためのエンドツーエンドのワークフロー。

```yaml
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@master
    
    - uses: Azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/k8sdemo:${{ github.sha }}
        docker push contoso.azurecr.io/k8sdemo:${{ github.sha }}
      
    # Set the target AKS cluster.
    - uses: Azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: contoso
        resource-group: contoso-rg
        
    - uses: Azure/k8s-create-secret@v1
      with:
        container-registry-url: contoso.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: demo-k8s-secret

    - uses: Azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          demo.azurecr.io/k8sdemo:${{ github.sha }}
        imagepullsecrets: |
          demo-k8s-secret
```

## <a name="next-steps"></a>次の手順

一連のアクションは GitHub の異なるリポジトリにあり、それぞれに、CI/CD に GitHub を使用し、ご自身のアプリを Azure にデプロイする際に役立つドキュメントとサンプルが含まれています。

- [setup-kubectl](https://github.com/Azure/setup-kubectl)

- [k8s-set-context](https://github.com/Azure/k8s-set-context)

- [aks-set-context](https://github.com/Azure/aks-set-context)

- [k8s-create-secret](https://github.com/Azure/k8s-create-secret)

- [k8s-deploy](https://github.com/Azure/k8s-deploy)

- [webapps-container-deploy](https://github.com/Azure/webapps-container-deploy)

- [actions-workflow-samples](https://github.com/Azure/actions-workflow-samples)
