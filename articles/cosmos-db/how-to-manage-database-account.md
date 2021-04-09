---
title: Azure Cosmos DB でデータベース アカウントを管理する方法について
description: Azure portal、PowerShell、CLI、Azure Resource Manager テンプレートを使用して、Azure Cosmos DB リソースを管理する方法について説明します。
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 01/06/2021
ms.author: mjbrown
ms.openlocfilehash: d542e2b4e5db86fd3354514790e718f0694a09a5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489754"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos アカウントを管理する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この記事では、Azure portal、Azure PowerShell、Azure CLI、Azure Resource Manager テンプレートを使用し、Azure Cosmos アカウントでさまざまなタスクを管理する方法を紹介します。

## <a name="create-an-account"></a>アカウントを作成する

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

[Azure CLI を使用した Azure Cosmos DB アカウントの作成](manage-with-cli.md#create-an-azure-cosmos-db-account)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

[PowerShell を使用した Azure Cosmos DB アカウントの作成](manage-with-powershell.md#create-account)に関する記事を参照してください。

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager テンプレート

[Azure Resource Manager テンプレートを使用した Azure Cosmos DB アカウントの作成](./manage-with-templates.md)に関する記事を参照してください。

## <a name="addremove-regions-from-your-database-account"></a>データベース アカウントのリージョンの追加/削除

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

1. リージョンを追加するには、目的のリージョンに対応する六角形 ( **+** ラベルが付いたもの) をマップ上で選択します。 別の方法でリージョンを追加するには、 **[+ リージョンの追加]** オプションを選択し、ドロップダウン メニューからリージョンを選択します。

1. リージョンを削除するには、チェック マークの付いた青い六角形をマップ上で選択して、1 つ以上のリージョンを消去します。 または、右側で、リージョンの横にある "ごみ箱" (🗑) アイコンを選択します。

1. 変更を保存するには、 **[OK]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="リージョンの追加や削除を行うメニュー":::

単一リージョン書き込みモードでは、書き込みリージョンを削除できません。 その現在の書き込みリージョンを削除する前に、別のリージョンへのフェールオーバーを行う必要があります。

複数リージョン書き込みモードでは、少なくとも 1 つのリージョンがあれば、どのリージョンも追加または削除できます。

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

[Azure CLI を使用したリージョンの追加または削除](manage-with-cli.md#add-or-remove-regions)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

[PowerShell を使用したリージョンの追加または削除](manage-with-powershell.md#update-account)に関する記事を参照してください。

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>複数の書き込みリージョンの構成

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

**[データをグローバルにレプリケートする]** タブを開き、 **[有効]** を選択して複数リージョンの書き込みを有効にします。 複数リージョンの書き込みを有効にすると、アカウントで現在用意されているすべての読み取りリージョンが読み書きリージョンになります。

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="Azure Cosmos アカウントの複数リージョン書き込み構成のスクリーンショット":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

[Azure CLI を使用した複数書き込みリージョンの有効化](manage-with-cli.md#enable-multiple-write-regions)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

[PowerShell を使用した複数書き込みリージョンの有効化](manage-with-powershell.md#multi-region-writes)に関する記事を参照してください。

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager テンプレート

アカウントと設定 `enableMultipleWriteLocations: true` の作成に使用された Resource Manager テンプレートをデプロイすることで、アカウントを単一書き込みリージョンから複数書き込みリージョンに移行できます。 次の Azure Resource Manager テンプレートは、2 つのリージョンと複数の書き込み場所を有効にして、SQL API 用の Azure Cosmos アカウントをデプロイする最小限のテンプレートです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2019-08-01",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Azure Cosmos アカウントでの自動フェールオーバーの有効化

自動フェールオーバーを選択すると、あるリージョンが利用できなくなったとき、フェールオーバーの優先順位が最も高いリージョンに Azure Cosmos DB がフェールオーバーされます。ユーザー側の操作は必要ありません。 自動フェールオーバーが有効になっているとき、リージョン優先順位を変更できます。 自動フェールオーバーを有効にするには、アカウントに 2 つ以上のリージョンを用意する必要があります。

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。 

4. **[保存]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自動フェールオーバーのポータル メニュー":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

[Azure CLI を使用した自動フェールオーバーの有効化](manage-with-cli.md#enable-automatic-failover)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

[PowerShell を使用した自動フェールオーバーの有効化](manage-with-powershell.md#enable-automatic-failover)に関する記事を参照してください。

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos アカウントでのフェールオーバーの優先度の設定

Cosmos アカウントに自動フェールオーバーを構成した後、リージョンのフェールオーバーの優先順位を変更できます。

> [!IMPORTANT]
> アカウントに自動フェールオーバーを構成しているとき、書き込みリージョン (フェールオーバーの優先順位がゼロ) は変更できません。 書き込みリージョンを変更するには、自動フェールオーバーを無効にし、手動フェールオーバーを実行する必要があります。

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントで、 **[データをグローバルにレプリケートする]** ウィンドウを開きます。

2. ウィンドウの上部で、 **[自動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[自動フェールオーバー]** ウィンドウで、 **[自動フェールオーバーの有効化]** を **[ON]** に設定してください。

4. フェールオーバーの優先度を変更するには、カーソルを置くと行の左側に表示される 3 つのドットを使い、読み込みリージョンをドラッグします。

5. **[保存]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="自動フェールオーバーのポータル メニュー":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

[Azure CLI を使用したフェールオーバー優先度の設定](manage-with-cli.md#set-failover-priority)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

[PowerShell を使用したフェールオーバー優先度の設定](manage-with-powershell.md#modify-failover-priority)に関する記事を参照してください。

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Azure Cosmos アカウントで手動フェールオーバーを実行する

> [!IMPORTANT]
> この操作を成功させるには、Azure Cosmos アカウントに手動フェールオーバーを構成する必要があります。

手動フェールオーバーを実行するプロセスには、アカウントの書き込みリージョン (フェールオーバーの優先順位 = 0) をそのアカウントに構成されている別のリージョンに変更することが含まれます。

> [!NOTE]
> 複数書き込みリージョンのアカウントを手動でフェールオーバーすることはできません。 Azure Cosmos SDK を使用するアプリケーションの場合、リージョンが利用できなくなると SDK によってそれが検出され、最も近くにあるリージョンに自動的にリダイレクトされます。

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. お使いの Azure Cosmos アカウントに移動して、 **[データをグローバルにレプリケートする]** メニューを開きます。

2. メニューの上部で、 **[手動フェールオーバー]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[データをグローバルにレプリケートする] メニュー":::

3. **[手動フェールオーバー]** メニューで、新しい書き込みリージョンを選択します。 チェック ボックスをオンにして、このオプションによって書き込みリージョンが変更されることを理解していることを示します。

4. フェールオーバーをトリガーするには、 **[OK]** を選択します。

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="手動フェールオーバーのポータル メニュー":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

[Azure CLI を使用した手動フェールオーバーのトリガー](manage-with-cli.md#trigger-manual-failover)に関する記事を参照してください。

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

[PowerShell を使用した手動フェールオーバーのトリガー](manage-with-powershell.md#trigger-manual-failover)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Cosmos アカウント、データベース、コンテナーの管理方法に関する詳細と例については、次の記事をお読みください。

* [Azure PowerShell を使用した Azure Cosmos DB の管理](manage-with-powershell.md)
* [Azure CLI を使用した Azure Cosmos DB の管理](manage-with-cli.md)