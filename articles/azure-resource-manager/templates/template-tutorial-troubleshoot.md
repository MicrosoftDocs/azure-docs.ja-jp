---
title: デプロイのトラブルシューティング
description: Azure Resource Manager テンプレート (ARM テンプレート) のデプロイを監視およびトラブルシューティングする方法について学習します。 アクティビティ ログとデプロイ履歴を紹介します。
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 31c4e6383b5eaea2bb66dc1baafa0fbff4918a7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97589119"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>チュートリアル:ARM テンプレート デプロイのトラブルシューティング

Azure Resource Manager テンプレート (ARM テンプレート) デプロイ エラーのトラブルシューティング方法について説明します。 このチュートリアルでは、テンプレートに 2 つのエラーを設定し、アクティビティ ログとデプロイ履歴を使用して問題を解決する方法について学習します。

テンプレート デプロイに関連するエラーには、次の 2 種類があります。

- **検証エラー** は、デプロイ前に判断できるシナリオで発生します。 これには、テンプレートに構文エラーがある場合や、サブスクリプション クォータを超えるリソースをデプロイしようとしている場合などがあります。
- **デプロイ エラー** は、デプロイ プロセスで発生する状況に起因します。 これには、並列でデプロイされているリソースにアクセスしようとしている場合などがあります。

この 2 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーもアクティビティ ログに表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> - 問題のあるテンプレートの作成
> - 検証エラーのトラブルシューティング
> - デプロイ エラーのトラブルシューティング
> - リソースをクリーンアップする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

- Visual Studio Code と Resource Manager ツール拡張機能。 「[クイック スタート:Visual Studio Code を使用して ARM テンプレートを作成する](quickstart-create-templates-use-visual-studio-code.md)」を参照してください。

## <a name="create-a-problematic-template"></a>問題のあるテンプレートの作成

「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)」から [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) テンプレートを開き、2 つのテンプレートの問題を設定します。

1. Visual Studio Code から、 **[ファイル]**  >  **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. **[開く]** を選択して、ファイルを開きます。
4. `apiVersion` 行を次の行に変更します。

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - `apiVersion1` は、無効な要素名です。 これは検証エラーになります。
    - API バージョンは `"2018-07-01"` である必要があります。  これはデプロイ エラーになります。

5. **[ファイル]**  >  **[名前を付けて保存]** を選択し、ファイルを _azuredeploy.json_ としてご自身のローカル コンピューターに保存します。

## <a name="troubleshoot-the-validation-error"></a>検証エラーのトラブルシューティング

「[テンプレートのデプロイ](template-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してテンプレートをデプロイします。

シェルから次のようなエラーが返されます。

```azurepowershell
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

このエラー メッセージは、`apiVersion1` に問題があることを示しています。

Visual Studio Code を使用して `apiVersion1` を `apiVersion` に変更して問題を解決し、テンプレートを保存します。

## <a name="troubleshoot-the-deployment-error"></a>デプロイ エラーのトラブルシューティング

「[テンプレートのデプロイ](template-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してテンプレートをデプロイします。

シェルから次のようなエラーが返されます。

```azurepowershell
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

デプロイ エラーは、次の手順を使用して、Azure portal で見つけることができます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[リソース グループ]** を選択し、目的のリソース グループ名を選択して、リソース グループを開きます。 **[デプロイ]** の下に **[1 件失敗]** が表示されます。

    ![失敗したデプロイが強調表示されているスクリーンショット。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. **[エラーの詳細]** を選択します。

    ![[エラーの詳細] リンクが強調表示されているスクリーンショット。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    エラー メッセージは、前に示したものと同じです。

    ![エラーの詳細を示すスクリーンショット。](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

アクティビティ ログからエラーを見つけることもできます。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[監視]**  >  **[アクティビティ ログ]** を選択します。
3. フィルターを使用してログを見つけます。

    ![Resource Manager チュートリアルのトラブルシューティング](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Visual Studio Code を使用して問題を解決し、テンプレートを再デプロイします。

一般的なエラーの一覧については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ARM テンプレート デプロイ エラーのトラブルシューティング方法について説明しました。  詳細については、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](common-deployment-errors.md)」を参照してください。
