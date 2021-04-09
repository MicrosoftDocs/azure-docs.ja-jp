---
title: テンプレート リファレンスの使用
description: Azure Resource Manager テンプレート (ARM テンプレート) リファレンスを利用してテンプレートを作成します。
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584138"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>チュートリアル:ARM テンプレート リファレンスを利用する

テンプレート スキーマ情報を見つける方法のほか、その情報を使用して Azure Resource Manager テンプレート (ARM テンプレート) を作成する方法について説明します。

このチュートリアルでは、Azure クイックスタート テンプレートから入手したベース テンプレートを使用します。 テンプレート リファレンス ドキュメントを使用して、テンプレートをカスタマイズします。

![Resource Manager テンプレート リファレンスを利用したストレージ アカウントのデプロイ](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの理解
> * テンプレート リファレンスの検索
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)は、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、 **[ファイル]**  >  **[ファイルを開く]** を選択します。
1. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. **[開く]** を選択して、ファイルを開きます。
1. **[ファイル]**  >  **[名前を付けて保存]** を選択し、ファイルを _azuredeploy.json_ としてご自身のローカル コンピューターに保存します。

## <a name="understand-the-schema"></a>スキーマの概要

1. Visual Studio Code から、テンプレートをルート レベルに折りたたみます。 次の要素を含む最も単純な構造があります。

    ![Resource Manager テンプレートの最も単純な構造](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: テンプレート言語のバージョンが記述されている JSON スキーマ ファイルの場所を指定します。
    * `contentVersion`: この要素に値を指定して、テンプレートの大きな変更を記述します。
    * `parameters`: リソースのデプロイをカスタマイズするためにデプロイを実行するときに提供される値を指定します。
    * `variables`: テンプレート言語式を簡略化するためにテンプレート内で JSON フラグメントとして使用される値を指定します。
    * `resources`: リソース グループ内でデプロイまたは更新されるリソースの種類を指定します。
    * `outputs`: デプロイ後に返される値を指定します。

1. `resources` を展開します。 `Microsoft.Storage/storageAccounts` リソースが定義されています。 SKU 名ではパラメーター値を使用します。 パラメーター名は `storageAccountType` です。

    ![Resource Manager テンプレートのストレージ アカウント定義](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. `parameters` を展開して、`storageAccountType` がどのように定義されているかを確認します。 このパラメーターには、4 つの許容値が含まれています。 他の許容値が見つかったら、パラメーター定義を修正してください。

    ![Resource Manager テンプレートのストレージ アカウント リソース SKU](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>テンプレート リファレンスの検索

1. [Azure テンプレート リファレンス](/azure/templates/)を参照します。
1. **[タイトルでフィルター処理します]** ボックスに「**ストレージ アカウント**」と入力し、 **[リファレンス] > [ストレージ]** の 1 つ目の **[ストレージ アカウント]** を選択します。

    ![Resource Manager テンプレート参照ストレージ アカウント](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    リソースプロバイダーには通常、複数の API バージョンがあります。

    ![Resource Manager テンプレート リファレンスのストレージ アカウントのバージョン](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. 左ペインの **[ストレージ]** で **[すべてのリソース]** を選択します。 このページには、ストレージ リソースプロバイダーのバージョンとリソースの種類が一覧表示されます。 テンプレートに定義するリソースの種類には、最新の API バージョンの使用をお勧めします。

    ![Resource Manager テンプレート リファレンスのストレージ アカウントの種類とバージョン](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. リソースの種類として最新バージョンの `storageAccount` を選択します。 この記事の執筆時点での最新バージョンは **2019-06-01** です。 このバージョンがテンプレートのストレージ アカウント リソースに使用されているバージョンと一致していることを確認してください。 API バージョンを更新する場合は、リソース定義がテンプレート リファレンスと一致していることを確認してください。

1. このページには、リソースの種類としての storageAccount の詳細が一覧表示されます。 たとえば、指定できる **Sku オブジェクト** の値が一覧表示されます。 先ほど開いたクイックスタート テンプレートに列挙されている SKU の他にも SKU は存在します。 クイックスタート テンプレートをカスタマイズすれば、使用可能なストレージの種類をすべて追加することができます。

    ![Resource Manager テンプレート リファレンスのストレージ アカウントの SKU](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>テンプレートの編集

Visual Studio Code から、次のスクリーンショットに示したストレージ アカウントの種類を新たに追加します。

![Resource Manager テンプレートのストレージ アカウント リソース](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. [Azure Cloud Shell](https://shell.azure.com) にサインインします。

1. 左上の **[PowerShell]** または **[Bash]** (CLI の場合) を選択して、希望の環境を選択します。  切り替えた場合は、シェルを再起動する必要があります。

    ![Azure portal の Cloud Shell のファイルのアップロード](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. **[ファイルのアップロード/ダウンロード]** を選択し、 **[アップロード]** を選択します。 先のスクリーンショットをご覧ください。 前のセクションで保存したファイルを選択します。 ファイルをアップロードした後、`ls` コマンドと `cat` コマンドを使用して、ファイルが正常にアップロードされたことを確認できます。

1. Cloud Shell で次のコマンドを実行します。 PowerShell コードまたは CLI コードを表示するタブを選択します。

   テンプレートをデプロイするときは、`storageAccountType` パラメーターに、新しく追加する値を指定します (**Standard_RAGRS** など)。 元のクイックスタート テンプレートを使用した場合、**Standard_RAGRS** という値は許可されないため、デプロイに失敗します。

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
1. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
1. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
1. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、テンプレート参照を使用して既存のテンプレートをカスタマイズする方法について説明しました。 複数のストレージ アカウント インスタンスを作成する方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [複数のインスタンスの作成](./template-tutorial-create-multiple-instances.md)
