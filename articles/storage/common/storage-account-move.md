---
title: Azure ストレージ アカウントを別のリージョンに移動する | Microsoft Docs
description: Azure ストレージ アカウントを別のリージョンに移動する方法を示します。
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.date: 05/11/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: d894ce71e0ffa5a0894a1f6b0035efe66271ded8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591462"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Azure ストレージ アカウントを別のリージョンに移動する

ストレージ アカウントを移動するには、別のリージョンにストレージ アカウントのコピーを作成します。 その後、AzCopy または選択した他のツールを使用して、そのアカウントにデータを移動します。

この記事では、次の方法について学習します。

> [!div class="checklist"]
> 
> * テンプレートをエクスポートします。
> * ターゲット リージョンとストレージ アカウント名を追加して、テンプレートを変更します。
> * テンプレートをデプロイして、新しいストレージ アカウントを作成します。
> * 新しいストレージ アカウントを構成します。
> * 新しいストレージ アカウントにデータを移動します。
> * ソース リージョンにあるリソースを削除します。

## <a name="prerequisites"></a>前提条件

- お使いのアカウントで使用されるサービスと機能が、ターゲット リージョンでサポートされていることを確認してください。

- プレビュー機能については、お使いのサブスクリプションがターゲット リージョンの許可リストに登録されていることを確認してください。

<a id="prepare"></a>

## <a name="prepare"></a>準備

作業を開始するには、Resource Manager テンプレートをエクスポートして変更します。 

### <a name="export-a-template"></a>テンプレートをエクスポートする

このテンプレートには、ストレージ アカウントを表す設定が含まれます。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してテンプレートをエクスポートするには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. **[すべてのリソース]** を選択してから、ストレージ アカウントを選択します。

3. **[設定]**  >  **[テンプレートのエクスポート]** を選択します。

4. **[テンプレートのエクスポート]** ブレードで **[ダウンロード]** を選択します。

5. ポータルからダウンロードした .zip ファイルを見つけて、選択したフォルダーにそのファイルを解凍します。

   この zip ファイルには、テンプレートとテンプレートをデプロイするためのスクリプトから構成される .json ファイルが含まれています。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してテンプレートをエクスポートするには:

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. 自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、移動するストレージ アカウントのサブスクリプションに設定します。

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. 元のストレージ アカウントのテンプレートをエクスポートします。 これらのコマンドによって、json テンプレートが現在のディレクトリに保存されます。

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>テンプレートの変更 

ストレージ アカウント名とリージョンを変更して、テンプレートを変更します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してテンプレートをデプロイするには:

1. Azure Portal で、 **[リソースの作成]** を選択します。

2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。

3. **[テンプレートのデプロイ]** を選択します。

    ![Azure Resource Manager のテンプレート ライブラリ](./media/storage-account-move/azure-resource-manager-template-library.png)

4. **［作成］** を選択します

5. **[Build your own template in the editor] \(エディターで独自のテンプレートをビルド\)** を選択します。

6. **[ファイルの読み込み]** を選択し、手順に従って、前のセクションでダウンロードした **template.json** ファイルを読み込みます。

7. **template.json** ファイル内で、ストレージ アカウント名の既定値を設定して、ターゲット ストレージ アカウントに名前を付与します。 この例では、ストレージ アカウント名の既定値を `mytargetaccount` に設定します。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    リージョンの場所コードを取得するには、「[Azure の場所](https://azure.microsoft.com/global-infrastructure/locations/)」を参照してください。  リージョンのコードは、スペースを含まないリージョン名です (**Central US** = **centralus**)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してテンプレートをデプロイするには:

1. **template.json** ファイル内で、ストレージ アカウント名の既定値を設定して、ターゲット ストレージ アカウントに名前を付与します。 この例では、ストレージ アカウント名の既定値を `mytargetaccount` に設定します。
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. **template.json** ファイル内の **location** プロパティを編集して、ターゲット リージョンを指定します。 この例では、ターゲット リージョンを `eastus` に設定します。

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    リージョン コードを取得するには、[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを実行します。

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move"></a>

## <a name="move"></a>詳細ビュー

テンプレートをデプロイして、ターゲット リージョンに新しいストレージ アカウントを作成します。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. **template.json** ファイルを保存します。

2. プロパティ値を入力または選択します。

- **サブスクリプション**:Azure サブスクリプションを選択します。

- **[リソース グループ]** : **[新規作成]** を選択して、リソース グループに名前を付けます。

- **[場所]** :Azure の場所を選択します。

3. **[上記の使用条件に同意する]** チェック ボックスをオンにして、 **[Select Purchase]\(購入の選択\)** ボタンをクリックします。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) を使って、ターゲット パブリック IP をデプロイするサブスクリプション ID を取得します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. テンプレートをデプロイするには、次のコマンドを使用します。

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>新しいストレージ アカウントを構成する

一部の機能はテンプレートにエクスポートされないため、新しいストレージ アカウントに追加する必要があります。 

次の表に、これらの機能と、新しいストレージ アカウントに追加するためのガイダンスを一覧表示します。

| 機能    | ガイダンス    |
|--------|-----------|
| **ライフサイクル管理ポリシー** | [Azure Blob Storage のライフサイクルの管理](../blobs/storage-lifecycle-management-concepts.md) |
| **静的な Web サイト** | [Azure Storage で静的 Web サイトをホストする](../blobs/storage-blob-static-website-how-to.md) |
| **イベントのサブスクリプション** | [Blob Storage のイベント処理](../blobs/storage-blob-event-overview.md) |
| **警告** | [Azure Monitor を使用してアクティビティ ログ アラートを作成、表示、管理する](../../azure-monitor/alerts/alerts-activity-log.md) |
| **Content Delivery Network (CDN)** | [Azure CDN を使用して HTTPS 経由でカスタム ドメインを使用した BLOB にアクセスする](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> 元のストレージ アカウントに対して CDN を設定する場合は、単に既存の CDN の配信元を新しいアカウントのプライマリ BLOB サービス エンドポイント (またはプライマリ静的 Web サイト エンドポイント) に変更します。 

### <a name="move-data-to-the-new-storage-account"></a>新しいストレージ アカウントにデータを移動する

AzCopy は、データの移動に推奨されるツールです。 パフォーマンスのために最適化されています。  より迅速な方法では、データがストレージ サーバー間で直接コピーされます。その場合、AzCopy では、コンピューターのネットワーク帯域幅を使用しません。 コマンド ラインまたはカスタム スクリプトの一部として AzCopy を使用します。 [AzCopy の作業開始](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)に関するページを参照してください。

Azure Data Factory を使用してデータを移動することもできます。 これには、直感的なユーザー インターフェイスが用意されています。 Azure Data Factory を使用するには、以下のリンクのいずれかを参照してください。 

  - [Copy data to or from Azure Blob storage by using Azure Data Factory (Azure Data Factory を使用して、Azure Blob ストレージをコピー先、またはコピー元にして、データをコピーする)](/azure/data-factory/connector-azure-blob-storage)
  - [Azure Data Factory を使用して Azure Data Lake Storage Gen2 との間でデータをコピーする](/azure/data-factory/connector-azure-data-lake-storage)
  - [Azure Data Factory を使用して File Storage をコピー元またはコピー先としてデータをコピーする](/azure/data-factory/connector-azure-file-storage)
  - [Azure Data Factory を使用した Azure Table Storage との間でのデータのコピー](/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>破棄またはクリーンアップ

デプロイ後に、最初からやり直す場合は、ターゲット ストレージ アカウントを削除し、この記事の「[準備](#prepare)」と「[移動](#move)」のセクションに示した手順を繰り返します。

変更をコミットしてストレージ アカウントの移動を完了するには、元のストレージ アカウントを削除します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してストレージ アカウントを削除するには:

1. Azure portal 上で、左側のメニューを展開してサービスのメニューを開き、 **[ストレージ アカウント]** を選択して、ストレージ アカウントの一覧を表示します。

2. 削除するターゲット ストレージ アカウントを見つけて、一覧の右側にある **[詳細]** ボタン ( **...** ) を右クリックします。

3. **[削除]** を選択して、確定します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

新しいストレージ アカウントを含め、リソース グループとそれに関連付けられているリソースを削除するには、[Remove-AzStorageAccount](/powershell/module/az.storage/remove-azstorageaccount) コマンドを使用します。

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure ストレージ アカウントをあるリージョンから別のリージョンに移動し、元のリソースをクリーンアップしました。  リージョン間でのリソースの移動と Azure でのディザスター リカバリーの詳細については、以下を参照してください。


- [リソースを新しいリソース グループまたはサブスクリプションに移動する](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Azure VM を別のリージョンに移動する](../../site-recovery/azure-to-azure-tutorial-migrate.md)