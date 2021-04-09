---
title: Azure Cosmos DB アカウントのカスタマー マネージド キーを構成する
description: Azure Key Vault で Azure Cosmos DB アカウントのカスタマー マネージド キーを構成する方法について説明します。
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/19/2021
ms.author: thweiss
ms.openlocfilehash: 3ee566a598ea7fdf060712c934305ef63467e548
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656518"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Azure Key Vault で Azure Cosmos アカウントのカスタマー マネージド キーを構成する
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

> [!NOTE]
> 現在、Azure Cosmos DB の[分析ストア](analytical-store-introduction.md)でカスタマー マネージド キーを使用するためには、ご利用のアカウントに特別な構成が必要となります。 詳細については、[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) にお問い合わせください。

Azure Cosmos アカウントに格納されているデータは、Microsoft が管理するキー (**サービス マネージド キー**) を使用して自動的かつシームレスに暗号化されます。 自分で管理するキー (**カスタマー マネージド キー**) を使用する暗号化の 2 番目のレイヤーを追加することもできます。

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="顧客データに関する暗号化のレイヤー":::

カスタマー マネージド キーは [Azure Key Vault](../key-vault/general/overview.md) に格納し、カスタマー マネージド キーが有効になっている Azure Cosmos アカウントごとにキーを指定する必要があります。 このキーは、そのアカウントに格納されているすべてのデータを暗号化するために使用されます。

> [!NOTE]
> 現在、カスタマー マネージド キーは新しい Azure Cosmos アカウントでのみ使用できます。 これらは、アカウントの作成時に構成します。

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Azure サブスクリプション用の Azure Cosmos DB リソース プロバイダーを登録する

1. [Azure portal](https://portal.azure.com/) にサインインし、お使いの Azure サブスクリプションに移動して **[設定]** タブの **[リソース プロバイダー]** を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="左側のメニューの [リソース プロバイダー] エントリ":::

1. **Microsoft.DocumentDB** リソース プロバイダーを検索します。 そのリソース プロバイダーが既に登録済みとしてマークされているどうかを確認します。 そうでない場合は、リソース プロバイダーを選択して **[登録]** を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Microsoft.DocumentDB リソース プロバイダーの登録":::

## <a name="configure-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスを構成する

Azure Cosmos DB でカスタマー マネージド キーを使用するには、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスで 2 つのプロパティを設定する必要があります。**論理的な削除** と **消去保護** です。

新しい Azure Key Vault インスタンスを作成する場合は、作成時にこれらのプロパティを有効にします。

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="新しい Azure Key Vault インスタンスの論理的な削除と消去保護を有効にする":::

既存の Azure Key Vault インスタンスを使用している場合は、Azure portal の **[プロパティ]** セクションを見て、これらのプロパティが有効であることを確認できます。 これらのプロパティのいずれかが有効になっていない場合は、次のいずれかの記事の「消去保護を有効にする」と「論理的な削除を有効にする」のセクションを参照してください。

- [PowerShell で論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)
- [Azure CLI で論理的な削除を使用する方法](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Azure Key Vault インスタンスにアクセス ポリシーを追加する

1. Azure portal から、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスに移動します。 左側のメニューの **[アクセス ポリシー]** を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="左側のメニューの [アクセス ポリシー]":::

1. **[+ アクセス ポリシーの追加]** を選択します。

1. **[キーのアクセス許可]** ドロップダウン メニューで、 **[取得]** 、 **[キーの折り返しを解除]** 、および **[キーを折り返す]** アクセス許可を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="適切なアクセス許可の選択":::

1. **[プリンシパルの選択]** で、 **[選択されていません]** を選択します。 次に、**Azure Cosmos DB** プリンシパルを検索して選択します (検索しやすくするために、Azure Government リージョンを除くすべての Azure リージョンはプリンシパル ID: `a232010e-820c-4083-83bb-3ace5fc29d0b`、Azure Government リージョンはプリンシパル ID: `57506a73-e302-42a9-b869-6f12d9ec29e9` を使用して検索することもできます)。 最後に、一番下の **[選択]** を選択します。 **Azure Cosmos DB** プリンシパルが一覧にない場合は、この記事の [リソース プロバイダーの登録](#register-resource-provider)に関するセクションの説明に従って **Microsoft.DocumentDB** リソース プロバイダーを再登録することが必要になる場合があります。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Azure Cosmos DB プリンシパルを選択する":::

1. **[追加]** を選択して新しいアクセス ポリシーを追加します。

1. すべての変更を保存するには、Key Vault インスタンスで **[保存]** を選択します。

## <a name="generate-a-key-in-azure-key-vault"></a>Azure Key Vault でキーを生成する

1. Azure portal から、暗号化キーをホストするために使用しようとしている Azure Key Vault インスタンスに移動します。 次に、左側のメニューの **[キー]** を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="左側のメニューの [キー] エントリ":::

1. **[生成/インポート]** を選択し、新しいキーに名前を付け、RSA キー サイズを選択します。 最高のセキュリティを得るには、最小で 3072 をお勧めします。 次に、 **[作成]** を選択します。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="新しいキーの作成":::

1. キーが作成されたら、新しく作成されたキーを選択し、次にその現在のバージョンを選択します。

1. 最後のスラッシュの後の部分を除き、キーの **[キー識別子]** をコピーします。

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="キーのキー識別子のコピー":::

## <a name="create-a-new-azure-cosmos-account"></a>新しい Azure Cosmos アカウントを作成する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

Azure portal から新しい Azure Cosmos DB アカウントを作成する場合は、 **[暗号化]** の手順で **[カスタマー マネージド キー]** を選択します。 **[キー URI]** フィールドで、前の手順でコピーした Azure Key Vault キーの URI/キー識別子を貼り付けます。

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Azure portal での CMK パラメーターの設定":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Azure PowerShell の使用

PowerShell で新しい Azure Cosmos DB アカウントを作成する場合、次を実行します。

- 前に **PropertyObject** の **keyVaultKeyUri** プロパティでコピーした Azure Key Vault キーの URI を渡します。

- API バージョンとして **2019-12-12** 以降を使用します。

> [!IMPORTANT]
> アカウントがカスタマー マネージド キーで正常に作成されるようにするには、`locations` プロパティを明示的に設定する必要があります。

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

アカウントが作成されたら、Azure Key Vault キーの URI をフェッチすることで、カスタマー マネージド キーが有効であることを確認できます。

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager テンプレートを使用して新しい Azure Cosmos アカウントを作成する場合、次を実行します。

- 前に **properties** オブジェクトの **keyVaultKeyUri** プロパティでコピーした Azure Key Vault キーの URI を渡します。

- API バージョンとして **2019-12-12** 以降を使用します。

> [!IMPORTANT]
> アカウントがカスタマー マネージド キーで正常に作成されるようにするには、`locations` プロパティを明示的に設定する必要があります。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

次の PowerShell スクリプトを使用してテンプレートをデプロイします。

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Azure CLI の使用

Azure CLI を使用して新しい Azure Cosmos アカウントを作成する場合は、先に `--key-uri` パラメーターでコピーした Azure Key Vault キーの URI を渡します。

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

アカウントが作成されたら、Azure Key Vault キーの URI をフェッチすることで、カスタマー マネージド キーが有効であることを確認できます。

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>キーの交換

Azure Cosmos アカウントで使用されるカスタマー マネージド キーのローテーションは、次の 2 つの方法で行うことができます。

- Azure Key Vault から、現在使用されているキーの新しいバージョンを作成します。

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="新しいキー バージョンを作成する":::

- アカウントのキー URI を更新して、現在使用されているキーをまったく別のキーに切り替えます。 Azure portal から、Azure Cosmos アカウントに移動し、左側のメニューから **[データ暗号化]** を選択します。

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="[データ暗号化] のメニュー エントリ":::

    次に、 **[キー URI]** を使用する新しいキーに置き換え、 **[保存]** を選択します。

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="[キー URI] の更新":::

    PowerShell で同じ結果を実現するには、次のようにします。

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

前のキーまたはキー バージョンは、24 時間後、または [Azure Key Vault 監査ログ](../key-vault/general/logging.md)に Azure Cosmos DB からそのキーまたはキー バージョンに対するアクティビティが出現しなくなった後に無効にすることができます。
    
## <a name="error-handling"></a>エラー処理

Azure Cosmos DB でカスタマー マネージド キー (CMK) を使用しているときにエラーが発生した場合、Azure Cosmos DB は、エラーの詳細を HTTP サブ状態コードと共に応答で返します。 このサブ状態コードを使用して、問題の根本原因をデバッグできます。 サポートされている HTTP サブ状態コードの一覧については、「[Azure Cosmos DB の HTTP 状態コード](/rest/api/cosmos-db/http-status-codes-for-cosmosdb)」を参照してください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にするために追加料金は発生しますか?

いいえ、この機能を有効にするためにかかる料金はありません。

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>カスタマー マネージド キーは容量計画にどのような影響がありますか?

カスタマー マネージド キーを使用する場合、データベース操作によって使用される[要求ユニット](./request-units.md)は、データの暗号化と復号化を実行するために必要な追加の処理を反映して増加します。 これにより、プロビジョニングされた容量の使用率が若干高くなる可能性があります。 次の表を参考にしてください。

| 操作の種類 | 要求ユニットの増加 |
|---|---|
| ポイント読み取り (ID による項目のフェッチ) | + 5%/操作 |
| 任意の書き込み操作 | + 6%/操作<br/>約 + 0.06 RU/インデックス プロパティ |
| クエリ、変更フィードの読み取り、または競合フィード | + 15%/操作 |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>カスタマー マネージド キーでどのようなデータが暗号化されますか?

カスタマー マネージド キーでは、次のメタデータを除き、ご自分の Azure Cosmos アカウントに格納されているすべてのデータが暗号化されます。

- Azure Cosmos DB [アカウント、データベース、およびコンテナー](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)の名前

- [ストアド プロシージャ](./stored-procedures-triggers-udfs.md)の名前

- [インデックス作成ポリシー](./index-policy.md)で宣言されているプロパティ パス

- お使いのコンテナーの[パーティション キー](./partitioning-overview.md)の値

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>カスタマー マネージド キーは既存の Azure Cosmos アカウントでサポートされますか?

この機能は現在、新しいアカウントでのみ使用できます。

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Azure Cosmos DB の[分析ストア](analytical-store-introduction.md)とカスタマー マネージド キーを組み合わせて使用することはできますか?

できます。ただし現時点では、アカウントに特別な構成が必要です。 詳細については、[azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com) にお問い合わせください。

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>アカウント レベルのキーより細かい粒度をサポートする計画はありますか?

現時点ではありませんが、コンテナー レベルのキーが検討されています。

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Azure Cosmos アカウントでカスタマー マネージド キーが有効かどうかを確認するにはどうすればよいですか?

Azure portal から、Azure Cosmos アカウントに移動し、左側のメニューで **[データ暗号化]** のエントリを探します。このエントリが存在する場合は、カスタマー マネージド キーがアカウントで有効になっています。

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="[データ暗号化] のメニュー エントリ":::

プログラムで Azure Cosmos アカウントの詳細をフェッチして、`keyVaultKeyUri` プロパティの存在を確認することもできます。 [PowerShell で](#using-powershell)、また [Azure CLI を使用して](#using-azure-cli)これを行う方法については、上記を参照してください。

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>カスタマー マネージド キーはバックアップにどのように影響しますか?

Azure Cosmos DB は、アカウントに格納されているデータの[定期的な自動バックアップ](./online-backup-and-restore.md)を取得します。 この操作では、暗号化されたデータがバックアップされます。 復元されたバックアップを使用するには、バックアップの時点で使用していた暗号化キーが必要です。 つまり、失効されておらず、バックアップの時点で使用していたキーのバージョンが依然有効になっている必要があります。

### <a name="how-do-i-revoke-an-encryption-key"></a>暗号化キーを失効させるにはどうすればよいですか?

キーの失効は、そのキーの最新バージョンを無効にすることによって行われます。

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="キーのバージョンを無効にする":::

あるいは、Azure Key Vault インスタンスからすべてのキーを失効させるために、Azure Cosmos DB プリンシパルに付与されているアクセス ポリシーを削除することもできます。

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Azure Cosmos DB プリンシパルのアクセス ポリシーの削除":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>カスタマー マネージド キーが失効した後、どのような操作を使用できますか?

暗号化キーが失効しているときに使用できる唯一の操作はアカウントの削除です。

## <a name="next-steps"></a>次のステップ

- [Azure Cosmos DB でのデータの暗号化](./database-encryption-at-rest.md)について学習します。
- [Cosmos DB 内のデータへのセキュリティで保護されたアクセス](secure-access-to-data.md)の概要を理解します。