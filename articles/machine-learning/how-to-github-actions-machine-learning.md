---
title: CI/CD 用の GitHub Actions
titleSuffix: Azure Machine Learning
description: Azure Machine Learning でモデルをトレーニングするための GitHub Actions ワークフローを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: b21f53f8ec76257fc19e0e30cd025ecc46ad2188
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102218283"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Azure Machine Learning で GitHub Actions を使用する

Azure Machine Learning でモデルをトレーニングするために [GitHub Actions](https://docs.github.com/en/actions) を使ってみましょう。 

> [!NOTE]
> Azure Machine Learning 向けの GitHub Actions は現状のまま提供されており、Microsoft によって完全にはサポートされていません。 特定のアクションで問題が発生した場合は、アクションのリポジトリでイシューを開いてください。 たとえば、aml-deploy アクションで問題が発生した場合は、[https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) リポジトリで問題を報告してください。

## <a name="prerequisites"></a>前提条件 

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 お持ちでない場合は、[無料](https://github.com/join)でサインアップしてください。  

## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 4 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**のインスタンスに接続するときには、** | 1.Machine Learning ワークスペースに接続します。 <br /> 2.コンピューティング先に接続します。 |
|**[実行]** | 1.トレーニングの実行を送信します。 |
|**デプロイする** | 1.Azure Machine Learning レジストリにモデルを登録します。 1. モデルをデプロイします。 |

## <a name="create-repository"></a>リポジトリを作成する

[GitHub Actions と Azure Machine Learning テンプレートを使用した ML Ops](https://github.com/machine-learning-apps/ml-template-azure) から新しいリポジトリを作成します。 

1. GitHub で[テンプレート](https://github.com/machine-learning-apps/ml-template-azure)を開きます。 
2. **[このテンプレートを使用]** を選択します。 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="[このテンプレートを使用]"::: を選択します。
3. テンプレートから新しいリポジトリを作成します。 リポジトリ名を `ml-learning` または任意の名前に設定します。 


## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する

[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

上記の例で、プレースホルダーをご利用のサブスクリプション ID、リソース グループ名、アプリ名に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>GitHub シークレットの構成

1. [GitHub](https://github.com/) で、自分のリポジトリを参照し、 **[設定] > [シークレット] > [新しいシークレットの追加]** を選択します。

2. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

## <a name="connect-to-the-workspace"></a>ワークスペースに接続する

[Azure Machine Learning ワークスペース アクション](https://github.com/marketplace/actions/azure-machine-learning-workspace)を使用して、Azure Machine Learning ワークスペースに接続します。 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

既定では、このアクションには `workspace.json` ファイルが必要です。 JSON ファイルに別の名前が付いている場合は、`parameters_file` 入力パラメーターを使用して指定できます。 ファイルがない場合は、リポジトリ名を使用して新しいファイルが作成されます。


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
アクションによって、ワークスペース Azure Resource Manager (ARM) のプロパティが構成ファイルに書き込まれます。このファイルは、今後のすべての Azure Machine Learning GitHub Actions によって選択されます。 ファイルは `GITHUB_WORKSPACE/aml_arm_config.json` に保存されます。 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Azure Machine Learning でコンピューティング先に接続する

Azure Machine Learning でコンピューティング先に接続するには、[Azure Machine Learning コンピューティング アクション](https://github.com/Azure/aml-compute)を使用します。  コンピューティング先が存在する場合は、アクションによってそこに接続されます。 それ以外の場合、アクションによって新しいコンピューティング先が作成されます。 [AML コンピューティング アクション](https://github.com/Azure/aml-compute)でサポートされるのは、Azure ML コンピューティング クラスターと Azure Kubernetes Service (AKS) のみです。 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>トレーニングの実行の送信

[Azure Machine Learning トレーニング アクション](https://github.com/Azure/aml-run)を使用して、ScriptRun、推定器、またはパイプラインを Azure Machine Learning に送信します。 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>レジストリにモデルを登録する

[Azure Machine Learning モデル登録アクション](https://github.com/Azure/aml-registermodel)を使用して、モデルを Azure Machine Learning に登録します。

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Azure Machine Learning へのモデルを ACI にデプロイする

[Azure Machine Learning デプロイ アクション](https://github.com/Azure/aml-deploy)を使用して、モデルをデプロイし、モデルのエンドポイントを作成します。 Azure Machine Learning デプロイを使用して、Azure Kubernetes Service にデプロイすることもできます。 Azure Kubernetes Service にデプロイするモデルについては、[こちらのサンプル ワークフロー](https://github.com/Azure-Samples/mlops-enterprise-template)を参照してください。

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>コード例全体

モデルをトレーニングし、Azure Machine Learning にデプロイします。 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループとリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。 

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Machine Learning SDK で機械学習パイプラインを作成して管理する](./how-to-create-machine-learning-pipelines.md)