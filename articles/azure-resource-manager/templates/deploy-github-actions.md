---
title: GitHub Actions を使用した Resource Manager テンプレートのデプロイ
description: GitHub Actions を使用して Azure Resource Manager テンプレートをデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 07/02/2020
ms.openlocfilehash: 313354499901bc69ec6e00f0ba7c385065cae615
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85854742"
---
# <a name="deploy-azure-resource-manager-templates-by-using-github-actions"></a>GitHub Actions を使用した Azure Resource Manager テンプレートのデプロイ

[GitHub Actions](https://help.github.com/en/actions) を使用すると、Azure Resource Manager (ARM) テンプレートが保存されている GitHub リポジトリに、カスタム ソフトウェア開発ライフサイクル ワークフローを直接作成できます。 [ワークフロー](https://help.github.com/actions/reference/workflow-syntax-for-github-actions)は YAML ファイルで定義されます。 ワークフローには 1 つ以上のジョブがあり、各ジョブには個々のタスクを実行する一連のステップが含まれています。 ステップでは、コマンドを実行したり、アクションを使用したりできます。 独自のアクションを作成することも、[GitHub コミュニティ](https://github.com/marketplace?type=actions)で共有されているアクションを使用することもでき、必要に応じてカスタマイズできます。 この記事では、[Azure CLI アクション](https://github.com/marketplace/actions/azure-cli-action)を使用して、Resource Manager テンプレートをデプロイする方法について説明します。

Azure CLI アクションには、次の 2 つの依存アクションがあります。

- **[チェックアウト](https://github.com/marketplace/actions/checkout)** : ワークフローが指定された Resource Manager テンプレートにアクセスできるように、リポジトリをチェックアウトします。
- **[Azure ログイン](https://github.com/marketplace/actions/azure-login)** : Azure 資格情報でログインします。

Resource Manager テンプレートをデプロイするための基本的なワークフローには、次の 3 つの手順があります。

1. テンプレート ファイルをチェックアウトします。
2. Azure にサインインします。
3. Resource Manager テンプレートをデプロイします。

## <a name="prerequisites"></a>前提条件

Resource Manager テンプレートとワークフロー ファイルを保存するための GitHub リポジトリが必要です。 リポジトリを作成するには、[新しいリポジトリの作成](https://help.github.com/en/enterprise/2.14/user/articles/creating-a-new-repository)に関するページをご覧ください。

## <a name="configure-deployment-credentials"></a>デプロイ資格情報の構成

Azure ログイン アクションでは、サービス プリンシパルを使用して Azure に対する認証を行います。 通常、CI/CD ワークフローのプリンシパルには、Azure リソースをデプロイするために組み込みの共同作成者権限が必要です。

次の Azure CLI スクリプトは、Azure リソース グループに対する共同作成者のアクセス許可を持つ Azure サービス プリンシパルを生成する方法を示しています。 ワークフローでは、Resource Manager テンプレートで定義されたリソースをこのリソース グループにデプロイします。

```azurecli
$projectName="[EnterAProjectName]"
$location="centralus"
$resourceGroupName="${projectName}rg"
$appName="http://${projectName}"
$scope=$(az group create --name $resourceGroupName --location $location --query 'id')
az ad sp create-for-rbac --name $appName --role Contributor --scopes $scope --sdk-auth
```

このスクリプトの **$projectName** と **$location** の値をカスタマイズします。 リソース グループの名前は、**rg** が付加されたプロジェクト名です。 ワークフローのリソース グループ名を指定する必要があります。

このスクリプトでは、次のような JSON オブジェクトが出力されます。

```json
{
   "clientId": "<GUID>",
   "clientSecret": "<GUID>",
   "subscriptionId": "<GUID>",
   "tenantId": "<GUID>",
   (...)
}
```

JSON 出力をコピーし、GitHub リポジトリに GitHub シークレットとして保存します。 リポジトリがまだない場合は、「[前提条件](#prerequisites)」をご覧ください。

1. GitHub リポジトリで、 **[Settings]\(設定\)** タブを選択します。
1. 左側のメニューで **[Secrets]\(シークレット\)** を選択します。
1. 次の値を入力します。

    - **Name**:AZURE_CREDENTIALS
    - **値**: (JSON 出力を貼り付けます)
1. **[Add secret]\(シークレットの追加\)** を選択します。

ワークフローのシークレット名を指定する必要があります。

## <a name="add-resource-manager-template"></a>Resource Manager テンプレートを追加する

Resource Manager テンプレートを GitHub リポジトリに追加します。 テンプレートがない場合は、次のテンプレートを使用できます。 このテンプレートでは、ストレージ アカウントが作成されます。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

ファイルは、リポジトリ内のどこに置いてもかまいません。 次のセクションのワークフロー サンプルでは、テンプレート ファイル名が **azuredeploy.json** であり、リポジトリのルートにある **templates** というフォルダーに保存されていることを想定しています。

## <a name="create-workflow"></a>ワークフローを作成する

ワークフロー ファイルは、リポジトリのルートにある **.github/workflows** フォルダーに保存する必要があります。 ワークフロー ファイルの拡張子には、 **.yml** または **.yaml** を指定できます。

ワークフロー ファイルを作成し、ファイルをリポジトリにプッシュ/アップロードするか、次の手順を使用できます。

1. GitHub リポジトリの上部のメニューで、 **[Actions]\(アクション\)** を選択します。
1. **[New workflow]\(新しいワークフロー\)** を選択します。
1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. **main.yml** 以外の別の名前を使用する場合は、ワークフロー ファイルの名前を変更します。 (例: **deployStorageAccount.yml**)。
1. yml ファイルの内容を以下に置き換えます。

    ```yml
    name: Deploy ARM Template

    on:
      push:
        branches:
          - master
        paths:
          - ".github/workflows/deployStorageAccount.yml"
          - "templates/azuredeploy.json"

    jobs:
      deploy-storage-account-template:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout source code
            uses: actions/checkout@master

          - name: Login to Azure
            uses: azure/login@v1
            with:
              creds: ${{ secrets.AZURE_CREDENTIALS }}


          - name: Deploy ARM Template
            uses: azure/CLI@v1
            with:
              inlineScript: |
                az deployment group create --resource-group myResourceGroup --template-file ./templates/azuredeploy.json
    ```

    このワークフロー ファイルには、次の 3 つのセクションがあります。

    - **name**:ワークフローの名前。
    - **on**: ワークフローをトリガーする GitHub イベントの名前。 マスター ブランチでプッシュ イベントが発生し、指定された 2 つのファイルの少なくとも 1 つが変更されると、ワークフローがトリガーされます。 この 2 つのファイルは、ワークフロー ファイルとテンプレート ファイルです。

        > [!IMPORTANT]
        > 2 つのファイルとそのパスが実際のものと一致することを確認します。
    - **jobs**: ワークフロー実行は、1 つ以上のジョブで構成されます。 **deploy-storage-account-template** というジョブが 1 つだけあります。  このジョブには、次の 3 つのステップがあります。

        - **Checkout source code (ソース コードをチェックアウトする)** 。
        - **Login to Azure (Azure にログインする)** 。

            > [!IMPORTANT]
            > シークレット名が、リポジトリに保存したものと一致することを確認します。 「[デプロイ資格情報の構成](#configure-deployment-credentials)」をご覧ください。
        - **Deploy ARM Template (ARM テンプレートをデプロイする)** 。 **resourceGroupName** の値を置き換えます。  「[デプロイ資格情報の構成](#configure-deployment-credentials)」で Azure CLI スクリプトを使用した場合、生成されるリソース グループ名は、**rg** が付加されたプロジェクト名です。 **templateLocation** の値を確認します。

1. **[Start commit]\(コミットの開始\)** を選択します。
1. **[Commit directly to the master branch]\(マスター ブランチに直接コミットする\)** を選択します。
1. **[Commit new file]\(新しいファイルをコミットする\)** (または **[Commit changes]\(変更をコミットする\)** ) を選択します。

ワークフローは、ワークフロー ファイルまたはテンプレート ファイルの更新によってトリガーされるように構成されているため、変更をコミットするとすぐにワークフローが開始されます。

## <a name="check-workflow-status"></a>ワークフローの状態を確認する

1. **[Actions]\(アクション\)** タブを選択します。**Create deployStorageAccount.yml** ワークフローが表示されます。 ワークフローの実行には 1 から 2 分かかります。
1. ワークフローを選択して開きます。
1. 左側のメニューで、**deploy-storage-account-template** (ジョブ名) を選択します。 ジョブ名はワークフローで定義されています。
1. **Deploy ARM Template** (ステップ名) を選択して展開します。 REST API の応答を確認できます。

## <a name="next-steps"></a>次のステップ

テンプレートの作成手順について説明したチュートリアルについては、「[チュートリアル:初めての ARM テンプレートを作成してデプロイする](template-tutorial-create-first-template.md)」を参照してください。
