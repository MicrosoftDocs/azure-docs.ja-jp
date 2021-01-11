---
title: Web サービスで AAD ID を使用する
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service の Web サービスで AAD ID を使用し、スコアリング中にクラウド リソースにアクセスします。
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 02/10/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: f76e149339e80ddeba8431afffbd677a4b595ec3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319475"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes Service の Machine Learning Web サービスで Azure AD ID を使用する

このガイドでは、Azure Kubernetes Service でデプロイされた機械学習モデルに Azure Active Directory (AAD) ID を割り当てる方法について説明します。 [AAD ポッド ID](https://github.com/Azure/aad-pod-identity) プロジェクトでは、[マネージド ID](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) と Kubernetes プリミティブを使用することで、アプリケーションで AAD を使ってクラウド リソースに安全にアクセスできます。 これにより、資格情報を埋め込んだり、トークンを `score.py` スクリプト内で直接管理したりしなくても、お客様の Web サービスで Azure リソースに安全にアクセスできます。 この記事では、Azure ID を作成して Azure Kubernetes Service クラスターにインストールし、デプロイ済みの Web サービスに ID を割り当てる手順について説明します。

## <a name="prerequisites"></a>前提条件

- [Machine Learning service 向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning SDK for Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

- `kubectl` コマンドを使用した AKS クラスターへのアクセス。 詳細については、「[クラスターへの接続](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster)」を参照してください。

- お客様の AKS クラスターにデプロイされた Azure Machine Learning Web サービス。

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Azure ID を作成して AKS クラスターにインストールする

1. ご自分の AKS クラスターで RBAC が有効かどうかを調べるには、次のコマンドを使用します。

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    このコマンドは、RBAC が有効になっている場合に `true` の値を返します。 この値により、次の手順で使用するコマンドが決まります。

1. AKS クラスターに [AAD ポッド ID](https://github.com/Azure/aad-pod-identity#getting-started) をインストールするには、次のコマンドのいずれかを使用します。

    * AKS クラスターで **RBAC が有効**な場合、次のコマンドを使用します。
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * AKS クラスターで **RBAC が有効でない**場合、次のコマンドを使用します。
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        コマンドの出力は次のテキストのようになります。

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. AAD ポッド ID プロジェクトのページに記載されている手順に従って [Azure ID を作成します](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity)。

1. AAD ポッド ID プロジェクトのページに記載されている手順に従って [Azure ID をインストールします](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity)。

1. AAD ポッド ID プロジェクトのページに記載されている手順に従って [Azure ID のバインドをインストールします](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)。

1. 前の手順で作成した Azure ID がご自分の AKS クラスターと同じリソース グループに含まれていない場合は、AAD ポッド ID プロジェクトのページに記載されている手順に従って [MIC のアクセス許可を設定します](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic)。

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Machine Learning Web サービスに Azure ID を割り当てる

次の手順では、前のセクションで作成した Azure ID を、**セレクター ラベル**を通じて AKS Web サービスに割り当てます。

まず、Azure ID を割り当てたい AKS クラスター内にあるデプロイの名前と名前空間を特定します。 この情報は、次のコマンドを実行することで取得できます。 名前空間はご自分の Azure Machine Learning ワークスペース名にする必要があります。また、デプロイ名はポータルに表示されているご自分のエンドポイント名にする必要があります。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

デプロイの仕様を編集して、Azure ID セレクター ラベルをデプロイに追加します。セレクターの値は、手順 5 (「[Azure ID バインドのインストール](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)」) で定義したものである必要があります。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

デプロイを編集して、Azure ID セレクター ラベルを追加します。 `/spec/template/metadata/labels` の下にある次のセクションに移動します。 `isazuremlapp: “true”` などの値が表示されています。 次のように aad-pod-ID ラベルを追加します。

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

ラベルが正しく追加されたことを確認するには、次のコマンドを実行します。

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

すべてのポッドの状態を表示するには、次のコマンドを実行します。

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

ポッドが稼働状態になると、このデプロイの Web サービスは、コードに資格情報を埋め込まなくても、お客様の Azure ID を通じて Azure リソースにアクセスできるようになります。 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>適切なロールを Azure ID に割り当てる

他の Azure リソースにアクセスするために、[適切なロールを Azure マネージド ID に割り当てます](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal)。 割り当てようとしているロールに正しい**データ アクション**が含まれていることを確認します。 たとえば、[ストレージ BLOB データ閲覧者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader)ではストレージ BLOB への読み取りアクセス許可が付与されますが、汎用の[閲覧者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)では付与されません。

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Machine Learning Web サービスで Azure ID を使用する

AKS クラスターにモデルをデプロイします。 `score.py` スクリプトには、自分の Azure ID でアクセスできる Azure リソースを指定する操作を含めることができます。 アクセスしようとしているリソースに必要なクライアント ライブラリの依存関係がインストールされていることを確認してください。 Azure ID を使用して自分のサービスからさまざまな Azure リソースにアクセスする方法の例を次にいくつか示します。

### <a name="access-key-vault-from-your-web-service"></a>Web サービスから Key Vault にアクセスする

**キー コンテナー**内のシークレットへの Azure ID 読み取りアクセスを許可すると、次のコードを使用して `score.py` でそれにアクセスできます。

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> この例では、DefaultAzureCredential を使用します。 特定のアクセス ポリシーを使用して ID アクセス権を付与するには、「[パート4: Azure Key Vault からシークレットを取得する](../key-vault/general/authentication.md#part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python)」を参照してください。

### <a name="access-blob-from-your-web-service"></a>Web サービスから BLOB にアクセスする

**ストレージ BLOB** 内のデータへの Azure ID 読み取りアクセスを許可すると、次のコードを使用して `score.py` でそれにアクセスできます。

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>次のステップ

* Python Azure ID クライアント ライブラリの使用方法の詳細については、GitHub の[リポジトリ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python)を参照してください。
* Azure Kubernetes Service クラスターへのモデルのデプロイに関する詳細なガイドについては、[解説記事](how-to-deploy-azure-kubernetes-service.md)を参照してください。
