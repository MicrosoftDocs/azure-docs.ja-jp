---
title: GitHub Actions を使用した Resource Manager テンプレートのデプロイ
description: GitHub Actions を使用して Azure Resource Manager テンプレート (ARM テンプレート) をデプロイする方法について説明します。
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 564a21d565fb80eba605eece95562a809a93246f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103471926"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>GitHub Actions を使用した ARM テンプレートのデプロイ

[GitHub Actions](https://docs.github.com/en/actions) は GitHub の一連の機能であり、コードを格納するのと同じ場所でソフトウェア開発ワークフローを自動化したり、プル要求や問題に対して共同作業を行ったりするために使用します。

[Azure Resource Manager テンプレートのデプロイ アクション](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template)を使って、Azure Resource Manager テンプレート (ARM テンプレート) の Azure へのデプロイを自動化します。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- GitHub アカウント。 ない場合は、[無料](https://github.com/join)でサインアップしてください。
    - Resource Manager テンプレートとワークフロー ファイルを保存するための GitHub リポジトリ。 リポジトリを作成するには、[新しいリポジトリの作成](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)に関するページをご覧ください。


## <a name="workflow-file-overview"></a>ワークフロー ファイルの概要

ワークフローは、お使いのリポジトリの `/.github/workflows/` パスの YAML (.yml) ファイルに定義されます。 この定義には、ワークフローを構成するさまざまな手順とパラメーターが含まれます。

このファイルには 2 つのセクションがあります。

|Section  |タスク  |
|---------|---------|
|**認証** | 1.サービス プリンシパルを定義します。 <br /> 2.GitHub シークレットを作成します。 |
|**デプロイする** | 1.Resource Manager テンプレートをデプロイします。 |

## <a name="generate-deployment-credentials"></a>デプロイ資格情報を生成する


[サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)は、[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して作成できます。 このコマンドは、Azure portal で [Azure Cloud Shell](https://shell.azure.com/) を使用するか、 **[試してみる]** ボタンを選択して実行します。

リソース グループがまだない場合は、リソース グループを作成します。

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
```

`myApp` のプレースホルダーはアプリケーションの名前に置き換えます。

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

上記の例で、プレースホルダーを実際のサブスクリプション ID とリソース グループ名に置き換えます。 これにより、以下のようなご自分の App Service アプリにアクセスするためのロールの割り当て資格情報を含む JSON オブジェクトが出力されます。 この JSON オブジェクトを後のためにコピーします。 必要なのは、`clientId`、`clientSecret`、`subscriptionId`、および `tenantId` の値を持つセクションだけです。

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> 常に最小限のアクセス権を付与することをお勧めします。 前の例のスコープは、リソース グループに限定されています。



## <a name="configure-the-github-secrets"></a>GitHub シークレットを構成する

Azure の資格情報、リソース グループ、およびサブスクリプションのシークレットを作成する必要があります。

1. [GitHub](https://github.com/) でリポジトリを参照します。

1. **[Settings]\(設定\) > [Secrets]\(シークレット\) > [New secret]\(新しいシークレット\)** の順に選択します。

1. Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに貼り付けます。 シークレットに `AZURE_CREDENTIALS` と名前を付けます。

1. `AZURE_RG` という名前の別のシークレットを作成します。 リソース グループの名前をシークレットの値フィールドに追加します (例: `myResourceGroup`)。

1. `AZURE_SUBSCRIPTION` という名前の追加のシークレットを作成します。 シークレットの値フィールドにサブスクリプション ID を追加します (例: `90fd3f9d-4c61-432d-99ba-1273f236afa2`)。

## <a name="add-resource-manager-template"></a>Resource Manager テンプレートを追加する

Resource Manager テンプレートを GitHub リポジトリに追加します。 このテンプレートでは、ストレージ アカウントが作成されます。

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

ファイルは、リポジトリ内のどこに置いてもかまいません。 次のセクションのワークフロー サンプルでは、テンプレート ファイル名が **azuredeploy.json** であり、リポジトリのルートに保存されていることを想定しています。

## <a name="create-workflow"></a>ワークフローを作成する

ワークフロー ファイルは、リポジトリのルートにある **.github/workflows** フォルダーに保存する必要があります。 ワークフロー ファイルの拡張子には、 **.yml** または **.yaml** を指定できます。

1. GitHub リポジトリの上部のメニューで、 **[Actions]\(アクション\)** を選択します。
1. **[New workflow]\(新しいワークフロー\)** を選択します。
1. **[Set up a workflow yourself]\(ワークフローを自分でセットアップする\)** を選択します。
1. **main.yml** 以外の別の名前を使用する場合は、ワークフロー ファイルの名前を変更します。 (例: **deployStorageAccount.yml**)。
1. yml ファイルの内容を以下に置き換えます。

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > 代わりに、ARM デプロイ アクション (例: `.azuredeploy.parameters.json`) に JSON 形式のパラメーター ファイルを指定できます。

    ワークフロー ファイルの最初のセクションには次のものが含まれます。

    - **name**:ワークフローの名前。
    - **on**: ワークフローをトリガーする GitHub イベントの名前。 メイン ブランチでプッシュ イベントが発生し、指定された 2 つのファイルの少なくとも 1 つが変更されると、ワークフローがトリガーされます。 この 2 つのファイルは、ワークフロー ファイルとテンプレート ファイルです。

1. **[Start commit]\(コミットの開始\)** を選択します。
1. **[Commit directly to the main branch]\(メイン ブランチに直接コミットする\)** を選択します。
1. **[Commit new file]\(新しいファイルをコミットする\)** (または **[Commit changes]\(変更をコミットする\)** ) を選択します。

ワークフローは、ワークフロー ファイルまたはテンプレート ファイルの更新によってトリガーされるように構成されているため、変更をコミットするとすぐにワークフローが開始されます。

## <a name="check-workflow-status"></a>ワークフローの状態を確認する

1. **[Actions]\(アクション\)** タブを選択します。**Create deployStorageAccount.yml** ワークフローが表示されます。 ワークフローの実行には 1 から 2 分かかります。
1. ワークフローを選択して開きます。
1. メニューから **[Run ARM deploy]\(ARM デプロイを実行\)** を選択して、デプロイを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする
リソース グループとリポジトリが不要になったら、リソース グループと GitHub リポジトリを削除して、デプロイしたリソースをクリーンアップします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [最初の ARM テンプレートを作成する](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Learn モジュール: GitHub Actions を使用して ARM テンプレートのデプロイを自動化する](/learn/modules/deploy-templates-command-line-github-actions/)
