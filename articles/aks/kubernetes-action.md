---
title: GitHub アクションを使用した Azure Kubernetes Service へのコンテナーのビルド、テスト、デプロイ
description: GitHub アクションを使用してコンテナーを Kubernetes にデプロイする方法について説明します
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761456"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Kubernetes Service にデプロイするための GitHub アクション

[GitHub アクション](https://docs.github.com/en/actions)を使用すると、自動化されたソフトウェア開発ライフサイクル ワークフローを柔軟に構築できます。 複数の Kubernetes アクションを使用することで、GitHub Actions を使用して Azure Container Registry から Azure Kubernetes サービスにコンテナーをデプロイできます。 

## <a name="prerequisites"></a>前提条件 

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。  
- 動作している Kubernetes クラスター
    - [チュートリアル: Azure Kubernetes Service 用のアプリケーションの準備](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

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

[Azure CLI](/cli/azure/) の [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用すると、[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を作成できます。 このコマンドは、Azure portal の [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[使ってみる]** ボタンを選択して実行できます。

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

    ![リポジトリの [Add a new secret]\(新しいシークレットの追加\) リンクのスクリーンショット。](media/kubernetes-action/secrets.png)

2. 上記の `az cli` コマンドの内容を、シークレット変数の値として貼り付けます。 たとえば、「 `AZURE_CREDENTIALS` 」のように入力します。

3. 同様に、コンテナー レジストリの資格情報に次の追加シークレットを定義し、Docker のログイン アクションで設定します。 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. 定義されたシークレットは以下のように表示されます。

    ![リポジトリの既存のシークレットのスクリーンショット。](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>コンテナー イメージをビルドして Azure Kubernetes Service クラスターにデプロイする

コンテナー イメージのビルドとプッシュは `Azure/docker-login@v1` アクションを使用して行われます。 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Azure Kubernetes Service クラスターにデプロイする

AKS にコンテナー イメージをデプロイするには、`Azure/k8s-deploy@v1` アクションを使用する必要があります。 このアクションには、次の 5 つのパラメーターがあります。

| **パラメーター**  | **説明**  |
|---------|---------|
| **namespace** | (省略可能) ターゲットの Kubernetes 名前空間を選択します。 名前空間が指定されていない場合、コマンドは既定の名前空間で実行されます | 
| **manifests** |  (必須) デプロイに使用されるマニフェスト ファイルへのパス |
| **images** | (省略可能) マニフェスト ファイルでの置換に使用されるイメージの完全修飾リソース URL |
| **imagepullsecrets** | (省略可能) クラスター内に既に設定されている docker-registry シークレットの名前。 これらの各シークレット名は、入力マニフェスト ファイルにあるワークロードの imagePullSecrets フィールドの下に追加されます |
| **kubectl-version** | (省略可能) 特定のバージョンの kubectl バイナリをインストールします |


AKS にデプロイする前に、ターゲットの Kubernetes 名前空間を設定し、イメージのプル シークレットを作成する必要があります。 イメージのプルのしくみの詳細については、「[Azure コンテナー レジストリから Kubernetes クラスターにイメージをプルする](../container-registry/container-registry-auth-kubernetes.md)」を参照してください。 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


`k8s-deploy` アクションを使用してデプロイを完了します。 環境変数をアプリケーションの値に置き換えます。 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Kubernetes クラスター、コンテナー レジストリ、およびリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Kubernetes Service について学習する](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>その他の Kubernetes GitHub Actions

* [Kubectl ツール インストーラー](https://github.com/Azure/setup-kubectl) (`azure/setup-kubectl`):ランナーに特定のバージョンの kubectl をインストールします。
* [Kubernetes コンテキスト設定](https://github.com/Azure/k8s-set-context) (`azure/k8s-set-context`):他のアクションで使用されるターゲット Kubernetes クラスター コンテキストを設定するか、kubectl コマンドを実行します。
* [AKS コンテキスト設定](https://github.com/Azure/aks-set-context) (`azure/aks-set-context`):ターゲットの Azure Kubernetes Service クラスター コンテキストを設定します。
* [Kubernetes シークレット作成](https://github.com/Azure/k8s-create-secret) (`azure/k8s-create-secret`):Kubernetes クラスターに汎用シークレットまたは docker-registry シークレットを作成します。
* [Kubernetes デプロイ](https://github.com/Azure/k8s-deploy) (`azure/k8s-deploy`):マニフェストを Kubernetes クラスターにベイクして配置します。
* [Helm セットアップ](https://github.com/Azure/setup-helm) (`azure/setup-helm`):ランナーに特定のバージョンの Helm バイナリをインストールします。
* [Kubernetes ベイク](https://github.com/Azure/k8s-bake) (`azure/k8s-bake`):helm2、kustomize、または kompose を使用したデプロイに使用されるベイク マニフェスト ファイル。
* [Kubernetes lint](https://github.com/azure/k8s-lint) (`azure/k8s-lint`):マニフェスト ファイルの検証または lint を行います。
