---
title: Azure Resource Manager テンプレートのデプロイ時に成果物をセキュリティで保護する | Microsoft Docs
description: Azure Resource Manager テンプレート内で使用される成果物をセキュリティで保護する方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: bf004f07558ae1f252a6bd26b4fd59ea9e4eea6e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069265"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>チュートリアル: Azure Resource Manager テンプレートのデプロイ時に成果物をセキュリティで保護する

Azure Resource Manager テンプレート内で使用される成果物を Azure Storage アカウントの Shared Access Signature (SAS) を使用してセキュリティで保護する方法について説明します。 デプロイの成果物は、メイン テンプレート ファイルに加え、デプロイを完了するために必要なすべてのファイルです。 たとえば「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」では、Azure SQL Database がメイン テンプレートによって作成されます。テーブルを作成してデータを挿入するために BACPAC ファイルも呼び出されます。 この BACPAC ファイルが成果物です。 成果物は、パブリック アクセスを持つ Azure ストレージ アカウントに格納されます。 このチュートリアルでは、SAS を使用して、ご自分の Azure Storage アカウント内の BACPAC ファイルへの制限付きアクセスを許可します。 SAS の詳細については、「[Shared Access Signatures (SAS) の使用](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

リンクされたテンプレートをセキュリティで保護する方法については、「[チュートリアル: リンクされた Azure Resource Manager テンプレートの作成](./resource-manager-tutorial-create-linked-templates.md)」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * BACPAC ファイルを準備する
> * 既存のテンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ
> * デプロイを検証する

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Resource Manager ツール拡張機能を持つ [Visual Studio Code](https://code.visualstudio.com/)。 [拡張機能のインストールに関する記事](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)を参照してください。
* 「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」を確認します。 このチュートリアルで使用するテンプレートは、そのチュートリアルで開発したものです。 この記事では、完成したテンプレートのダウンロード リンクを提供しています。
* セキュリティを向上させるために、生成されたパスワードを SQL Server 管理者アカウントに対して使用します。 パスワードを生成するためのサンプルを次に示します。

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Azure Key Vault は、暗号化キーおよびその他のシークレットを保護するために設計されています。 詳細については、「[チュートリアル: Resource Manager テンプレートのデプロイで Azure Key Vault を統合する](./resource-manager-tutorial-use-key-vault.md)」を参照してください。 パスワードは 3 か月ごとに更新することをお勧めします。

## <a name="prepare-a-bacpac-file"></a>BACPAC ファイルを準備する

このセクションでは、Resource Manager テンプレートをデプロイするときに、セキュリティで保護された方法で BACPAC ファイルにアクセスできるようにそのファイルを準備します。 このセクションには、5 つの手順があります。

* BACPAC ファイルをダウンロードします。
* Azure ストレージ アカウントを作成します。
* ストレージ アカウントに BLOB コンテナーを作成します。
* BACPAC ファイルをコンテナーにアップロードします。
* BACPAC ファイルの SAS トークンを取得します。

PowerShell スクリプトを使用してこれらの手順を自動化するには、「[リンクされたテンプレートをアップロードする](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template)」のスクリプトを参照してください。

### <a name="download-the-bacpac-file"></a>BACPAC ファイルをダウンロードする

[BACPAC ファイル](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac)をダウンロードし、お使いのローカル コンピューターに同じ名前 (**SQLDatabaseExtension.bacpac**) で保存します。

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

1. 次の画像を選択して、Azure portal で Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. 次のプロパティを入力します。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択し、名前を付けます。 リソース グループは、管理目的で使用される Azure リソース用のコンテナーです。 このチュートリアルでは、ストレージ アカウントと Azure SQL Database で同じリソース グループを使用できます。 このリソース グループ名を書き留めておいてください。このチュートリアルの中で、後で Azure SQL Database を作成するときに必要です。
    * **場所**: リージョンを選択します。 たとえば **[米国中部]** です。
    * **ストレージ アカウントの種類**: 既定値 (**Standard_LRS**) を使用します。
    * **場所**: 既定値 ( **[resourceGroup().location]** ) を使用します。 これは、ストレージ アカウント用のリソース グループの場所を使用することを意味します。
    * **上記の使用条件に同意する**: (選択済み)
3. **[購入]** を選択します。
4. ポータルの右上隅にある通知アイコン (ベル アイコン) を選択して、デプロイ状態を確認します。

    ![Resource Manager チュートリアルのポータルの通知ウィンドウ](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. ストレージ アカウントが正常にデプロイされたら、通知ウィンドウで **[リソース グループに移動]** を選択して、リソース グループを開きます。

### <a name="create-a-blob-container"></a>BLOB コンテナーを作成する

ファイルをアップロードする前に、BLOB コンテナーが必要です。

1. ストレージ アカウントを選択して開きます。 リソース グループにストレージ アカウントが 1 つだけ表示されていることを確認できます。 ご自分のストレージ アカウント名は、次のスクリーン ショットに示されているものとは異なります。

    ![Resource Manager チュートリアルのストレージ アカウント](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. **[BLOB]** タイルを選択します。

    ![Resource Manager チュートリアルの BLOB](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. 上部の **[+ コンテナー]** を選択して、新しいコンテナーを作成します。
4. 次の値を入力します。

    * **名前**: 「**sqlbacpac**と入力します。
    * **パブリック アクセス レベル**: 既定値 ( **[プライベート (匿名アクセスなし)]** ) を使用します。
5. **[OK]** を選択します。
6. **[sqlbacpac]** を選択して、新規作成されたコンテナーを開きます。

### <a name="upload-the-bacpac-file-to-the-container"></a>BACPAC ファイルをコンテナーにアップロードする

1. **[アップロード]** を選択します。
2. 次の値を入力します。

    * **ファイル**: 指示に従って、前にダウンロードした BACPAC ファイルを選択します。 既定の名前は **SQLDatabaseExtension.bacpac** です。
    * **認証の種類**: **[SAS]** を選択します。  *[SAS]* は既定値です。
3. **[アップロード]** を選択します。  ファイルのアップロードが成功すると、ファイル名がコンテナー内に一覧表示されます。

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />SAS トークンを生成する

1. コンテナーの **SQLDatabaseExtension.bacpac** を右クリックした後、 **[SAS の生成]** を選択します。
2. 次の値を入力します。

    * **アクセス許可**: 既定値である **[読み取り]** を使用します。
    * **開始日時と終了日時**: 既定値では、SAS トークンは 8 時間使用できます。 このチュートリアルを完了するためにもっと時間が必要な場合は、 **[有効期限]** を更新します。
    * **使用できる IP アドレス**: このフィールドは空のままにします。
    * **許可されるプロトコル**: 既定値である **[HTTPS]** を使用します。
    * **署名キー**: 既定値である **[キー 1]** を使用します。
3. **[BLOB SAS トークンおよび URL を生成]** を選択します。
4. **BLOB SAS URL** のコピーを作成します。 このURL の中央に、ファイル名 **SQLDatabaseExtension.bacpac** があります。  URL は、このファイル名によって 3 つの部分に分割されます。

   - **成果物の場所**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/ 場所の末尾が "/" であることを確認します。
   - **BACPAC ファイル名**: SQLDatabaseExtension.bacpac。
   - **[Artifact location SAS token]\(成果物の場所の SAS トークン\)** : トークンの前に "?" があることを確認します。

     これら 3 つの値は、「[テンプレートのデプロイ](#deploy-the-template)」で必要です。

## <a name="open-an-existing-template"></a>既存のテンプレートを開く

このセッションでは、「[チュートリアル: Azure Resource Manager テンプレートを使用して SQL BACPAC ファイルをインポートする](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)」で作成したテンプレートを変更し、SAS トークンを使用して BACPAC ファイルを呼び出します。  SQL 拡張機能チュートリアルで開発されたテンプレートは、[https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json) で共有されます。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。

    テンプレート内に定義されているリソースは 5 つあります。

   * `Microsoft.Sql/servers` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers)をご覧ください。
   * `Microsoft.SQL/servers/securityAlertPolicies` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies)をご覧ください。
   * `Microsoft.SQL/servers/filewallRules` [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules)をご覧ください。
   * `Microsoft.SQL/servers/databases`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases)をご覧ください。
   * `Microsoft.SQL/server/databases/extensions`  [テンプレート リファレンス](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions)をご覧ください。

     カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
4. **[ファイル]** > **[Save As]\(名前を付けて保存\)** を選択し、このファイルのコピーを **azuredeploy.json** という名前でローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

次の追加パラメーターを追加します。

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Resource Manager チュートリアルの成果物をセキュリティで保護するためのパラメーター](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

次の 2 つの要素の値を更新します。

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

デプロイ手順については、「[テンプレートのデプロイ](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template)」セクションを参照してください。 代わりに次の PowerShell デプロイ スクリプトを使用します。

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

生成されたパスワードを使用します。 「[前提条件](#prerequisites)」を参照してください。
_artifactsLocation、_artifactsLocationSasToken、および bacpacFileName の値については、「[Generate a SAS token](#generate-a-sas-token)」(SAS トークンの生成) を参照してください。

## <a name="verify-the-deployment"></a>デプロイを検証する

ポータルで、新しくデプロイされたリソース グループから SQL データベースを選択します。 **[クエリ エディター (プレビュー)]** を選択してから、管理者の資格情報を入力します。 データベースにインポートされた 2 つのテーブルが表示されます。

![Azure Resource Manager による SQL 拡張機能 BACPAC のデプロイ](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 6 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、SQL サーバーと SQL データベースをデプロイし、SAS トークンを使用して BACPAC ファイルをインポートしました。 Azure パイプラインを作成し、継続的に Resource Manager テンプレートを開発およびデプロイする方法については、以下を参照してください。

> [!div class="nextstepaction"]
> [Azure Pipelines を使用した継続的インテグレーション](./resource-manager-tutorial-use-azure-pipelines.md)
