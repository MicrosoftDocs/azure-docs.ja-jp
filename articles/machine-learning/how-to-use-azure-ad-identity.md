---
title: Web サービスで Azure AD ID を使用する
titleSuffix: Azure Machine Learning
description: Azure Kubernetes Service の Web サービスで Azure AD ID を使用し、スコアリング中にクラウド リソースにアクセスします。
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102520474"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure Kubernetes Service の Machine Learning Web サービスで Azure AD ID を使用する

このガイドでは、Azure Kubernetes Service でデプロイされた機械学習モデルに Azure Active Directory (Azure AD) ID を割り当てる方法について説明します。 [Azure AD ポッド ID](https://github.com/Azure/aad-pod-identity) プロジェクトでは、[マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) と Kubernetes プリミティブを使用することで、アプリケーションで Azure AD を使ってクラウド リソースに安全にアクセスできます。 これにより、資格情報を埋め込んだり、トークンを `score.py` スクリプト内で直接管理したりしなくても、お客様の Web サービスで Azure リソースに安全にアクセスできます。 この記事では、Azure ID を作成して Azure Kubernetes Service クラスターにインストールし、デプロイ済みの Web サービスに ID を割り当てる手順について説明します。

## <a name="prerequisites"></a>前提条件

- [Machine Learning service 向けの Azure CLI 拡張機能](reference-azure-machine-learning-cli.md)、[Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro)、または [Azure Machine Learning Visual Studio Code 拡張機能](tutorial-setup-vscode-extension.md)。

- `kubectl` コマンドを使用した AKS クラスターへのアクセス。 詳細については、「[クラスターへの接続](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)」を参照してください。

- お客様の AKS クラスターにデプロイされた Azure Machine Learning Web サービス。

## <a name="create-and-install-an-azure-identity"></a>Azure ID を作成してインストールする

1. ご自分の AKS クラスターで Kubernetes RBAC が有効かどうかを調べるには、次のコマンドを使用します。

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    このコマンドは、Kubernetes RBAC が有効になっている場合に `true` の値を返します。 この値により、次の手順で使用するコマンドが決まります。

1. [Azure AD ポッド ID](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) を AKS クラスターにインストールします。

1. Azure AD ポッド ID プロジェクトのページに記載されている手順に従って [Azure で ID を作成します](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure)。

1. Azure AD ポッド ID プロジェクトのページに記載されている手順に従って [AzureIdentity をデプロイします](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity)。

1. Azure AD ポッド ID プロジェクトのページに記載されている手順に従って [AzureIdentityBinding をデプロイします](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)。

1. 前の手順で作成した Azure ID がご自分の AKS クラスターと同じノード リソース グループに含まれていない場合は、Azure AD ポッド ID プロジェクトのページに示されている[ロールの割り当て](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group)手順に従います。

## <a name="assign-azure-identity-to-web-service"></a>Azure ID を Web サービスに割り当てる

次の手順では、前のセクションで作成した Azure ID を、**セレクター ラベル** を通じて AKS Web サービスに割り当てます。

まず、Azure ID を割り当てたい AKS クラスター内にあるデプロイの名前と名前空間を特定します。 この情報は、次のコマンドを実行することで取得できます。 名前空間はご自分の Azure Machine Learning ワークスペース名にする必要があります。また、デプロイ名はポータルに表示されているご自分のエンドポイント名にする必要があります。

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

デプロイの仕様を編集して、Azure ID セレクター ラベルをデプロイに追加します。セレクターの値は、[AzureIdentityBinding のデプロイ](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)に関するセクションの手順 5 で定義したものである必要があります。

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

ラベルが正しく追加されたことを確認するには、次のコマンドを実行します。 新しく作成されたポッドの状態も表示されます。

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

ポッドが稼働状態になると、このデプロイの Web サービスは、コードに資格情報を埋め込まなくても、お客様の Azure ID を通じて Azure リソースにアクセスできるようになります。

## <a name="assign-roles-to-your-azure-identity"></a>ロールを Azure ID に割り当てる

他の Azure リソースにアクセスするために、[適切なロールを Azure マネージド ID に割り当てます](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 割り当てようとしているロールに正しい **データ アクション** が含まれていることを確認します。 たとえば、[ストレージ BLOB データ閲覧者ロール](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)ではストレージ BLOB への読み取りアクセス許可が付与されますが、汎用の[閲覧者ロール](../role-based-access-control/built-in-roles.md#reader)では付与されません。

## <a name="use-azure-identity-with-your-web-service"></a>Web サービスで Azure ID を使用する

AKS クラスターにモデルをデプロイします。 `score.py` スクリプトには、自分の Azure ID でアクセスできる Azure リソースを指定する操作を含めることができます。 アクセスしようとしているリソースに必要なクライアント ライブラリの依存関係がインストールされていることを確認してください。 Azure ID を使用して自分のサービスからさまざまな Azure リソースにアクセスする方法の例を次にいくつか示します。

### <a name="access-key-vault-from-your-web-service"></a>Web サービスから Key Vault にアクセスする

**キー コンテナー** 内のシークレットへの Azure ID 読み取りアクセスを許可すると、次のコードを使用して `score.py` でそれにアクセスできます。

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
> この例では、DefaultAzureCredential を使用します。 特定のアクセス ポリシーを使用して ID アクセスを付与するには、[Azure CLI を使用して Key Vault アクセスを割り当てる](../key-vault/general/assign-access-policy-cli.md)方法に関するページを参照してください。

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