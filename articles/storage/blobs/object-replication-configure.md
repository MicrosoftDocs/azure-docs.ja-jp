---
title: オブジェクト レプリケーションの構成
titleSuffix: Azure Storage
description: オブジェクト レプリケーションを構成して、ブロック BLOB を 1 つのストレージ アカウントのコンテナーから別のコンテナーに非同期でコピーする方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2b6855d72b644a3fe1fa46c883eb7414383a1a57
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031703"
---
# <a name="configure-object-replication-for-block-blobs"></a>ブロック BLOB のオブジェクト レプリケーションを構成する

オブジェクト レプリケーションを使用すると、ソース ストレージ アカウントと宛先アカウントの間でブロック BLOB を非同期にコピーできます。 オブジェクト レプリケーションの詳細については、[オブジェクト レプリケーション](object-replication-overview.md)に関するページをご覧ください。

オブジェクト レプリケーションを構成するときに、ソース ストレージ アカウントと宛先アカウントを指定するレプリケーション ポリシーを作成します。 レプリケーション ポリシーには、ソース コンテナーと宛先コンテナーを指定し、ソース コンテナー内のどのブロック BLOB をレプリケートするかを示す 1 つ以上のルールが含まれています。

この記事では、Azure portal、PowerShell、または Azure CLI を使用して、ストレージ アカウントのオブジェクト レプリケーションを構成する方法について説明します。 Azure Storage リソース プロバイダーのクライアント ライブラリのいずれかを使用して、オブジェクト レプリケーションを構成することもできます。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="create-a-replication-policy-and-rules"></a>レプリケーション ポリシーとルールを作成する

オブジェクト レプリケーションを構成する前に、ソースと宛先のストレージ アカウントがまだ存在しない場合は作成します。 どちらのアカウントも汎用 v2 ストレージ アカウントである必要があります。 詳細については、[Azure Storage アカウントの作成](../common/storage-account-create.md)に関するページを参照してください。

オブジェクトのレプリケーションでは、ソース アカウントと宛先アカウントの両方で BLOB のバージョン管理が有効になっている必要があります。また、ソース アカウントで BLOB の変更フィードが有効になっている必要もあります。 BLOB のバージョン管理については、[BLOB のバージョン管理](versioning-overview.md)に関するページをご覧ください。 変更フィードの詳細については、[Azure Blob Storage の変更フィードのサポート](storage-blob-change-feed.md)に関するページをご覧ください。 これらの機能を有効にすると、追加コストが発生する可能性があることに注意してください。

1 つのストレージ アカウントを、最大 2 つの宛先アカウントのソース アカウントとして使用できます。 ソース アカウントと宛先アカウントが同じリージョンに存在していても、異なるリージョンに存在していてもかまいません。 また、異なるサブスクリプションや異なる Azure Active Directory (Azure AD) テナントに存在していてもかまいません。 アカウントのペアごとに作成できるレプリケーション ポリシーは、1 つのみです。

オブジェクト レプリケーションを構成する際に、Azure Storage リソース プロバイダー経由で宛先アカウントにレプリケーション ポリシーを作成します。 レプリケーション ポリシーが作成されると、Azure Storage でポリシー ID が割り当てられます。 その後、このポリシー ID を使用して、そのレプリケーション ポリシーをソース アカウントに関連付ける必要があります。 レプリケーションを実行するには、ソース アカウントと宛先アカウントのポリシー ID が同じである必要があります。

ストレージ アカウントにオブジェクト レプリケーション ポリシーを構成するには、ストレージ アカウントのレベル以上を対象とする Azure Resource Manager の **[共同作成者]** ロールが割り当てられている必要があります。 詳細については、Azure ロールベースのアクセス制御 (Azure RBAC) に関するドキュメントの「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

### <a name="configure-object-replication-when-you-have-access-to-both-storage-accounts"></a>両方のストレージ アカウントへのアクセス権を持っている場合にオブジェクト レプリケーションを構成する

ソース アカウントと宛先アカウントの両方へのアクセス権を持っている場合は、両方のアカウントでオブジェクト レプリケーション ポリシーを構成できます。

Azure portal でオブジェクト レプリケーションを構成する前に、ソース コンテナーと宛先コンテナーが存在しない場合は、それぞれのストレージ アカウントに作成します。 また、ソース アカウントで BLOB のバージョン管理と変更フィードを有効にし、宛先アカウントで BLOB のバージョン管理を有効にします。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

宛先アカウントにポリシーを構成すると、Azure portal で自動的にソース アカウントに作成されます。

Azure portal でレプリケーション ポリシーを作成するには、次の手順を実行します。

1. Azure portal でソース ストレージ アカウントに移動します。
1. **[Blob service]** で、 **[オブジェクト レプリケーション]** を選択します。
1. **[レプリケーション規則の設定]** を選択します。
1. 宛先サブスクリプションとストレージ アカウントを選択します。
1. **[Container pairs]\(コンテナー ペア\)** セクションで、ソース アカウントからソース コンテナーを選択し、宛先アカウントから宛先コンテナーを選択します。 1 つのレプリケーション ポリシーにつき最大 10 個のコンテナー ペアを作成できます。

    次の図は、一連のレプリケーション ルールを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Azure portal のレプリケーション ルールを示すスクリーンショット":::

1. 必要に応じて、1 つ以上のフィルターを指定し、プレフィックス パターンに一致する BLOB のみをコピーします。 たとえば、プレフィックス `b` を指定すると、その文字で始まる名前の BLOB のみがレプリケートされます。 プレフィックスの一部として仮想ディレクトリを指定できます。 プレフィックス文字列では、ワイルドカード文字はサポートされません。

    次の図は、レプリケーション ルールの一部としてコピーされる BLOB を制限するフィルターを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="レプリケーション ルールのフィルターを示すスクリーンショット":::

1. 既定では、コピー スコープは新しいオブジェクトのみをコピーするように設定されています。 コンテナー内のすべてのオブジェクトをコピーするか、カスタムの日付と時刻からオブジェクトをコピーするには、**変更** リンクを選択し、コンテナー ペアのコピー スコープを構成します。

    次の図は、指定した日付と時刻からオブジェクトをコピーするカスタム コピー スコープを示しています。

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="オブジェクト レプリケーションのカスタム コピー スコープを示すスクリーンショット":::

1. **[保存して適用する]** を選択してレプリケーション ポリシーを作成し、データのレプリケーションを開始します。

オブジェクト レプリケーションを構成すると、次の図に示すように、Azure portal にレプリケーション ポリシーとルールが表示されます。

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Azure portal のオブジェクト レプリケーション ポリシーを示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell でレプリケーション ポリシーを作成するには、まず Az.Storage PowerShell モジュールのバージョン [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) 以降をインストールします。 Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

次の例は、ソース アカウントと宛先アカウントにレプリケーション ポリシーを作成する方法を示しています。 山かっこ内の値は、実際の値に置き換えてください。

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してレプリケーション ポリシーを作成するには、最初に Azure CLI バージョン 2.11.1 以降をインストールします。 詳細については、[Azure CLI の概要](/cli/azure/get-started-with-azure-cli)に関するページをご覧ください。

次に、[az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) コマンドを呼び出して、ソース ストレージ アカウントと宛先ストレージ アカウントで BLOB のバージョン管理を有効にし、ソース アカウントで変更フィードを有効にします。 山かっこ内の値は、実際の値に置き換えてください。

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

ソースと宛先のコンテナーをそれぞれのストレージ アカウントに作成します。

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
```

[az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) を呼び出して、宛先アカウントに新しいレプリケーション ポリシーと関連付けられたルールを作成します。

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2020-09-10T00:00:00Z' \
    --prefix-match a

```

新しいポリシーが作成されると、Azure Storage でポリシー ID が設定されます。 ポリシーにルールを追加するには、[az storage account or-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) を呼び出し、ポリシー ID を指定します。

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

次に、このポリシー ID を使用して、ソース アカウントにポリシーを作成します。

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

### <a name="configure-object-replication-when-you-have-access-only-to-the-destination-account"></a>宛先アカウントへのアクセス権のみを持っている場合にオブジェクト レプリケーションを構成する

ソース ストレージ アカウントへのアクセス許可を持っていない場合は、宛先アカウントでオブジェクト レプリケーションを構成し、ポリシー定義を含む JSON ファイルを別のユーザーに提供して、ソース アカウントでも同じポリシーを作成できます。 たとえば、ソース アカウントが宛先アカウントとは異なる Azure AD テナントにある場合は、この方法を使用してオブジェクト レプリケーションを構成できます。

ポリシーを作成するには、宛先ストレージ アカウントのレベル以上を対象とする Azure Resource Manager の **[共同作成者]** ロールが割り当てられている必要があることに注意してください。 詳細については、Azure ロールベースのアクセス制御 (Azure RBAC) に関するドキュメントの「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

次の表は、各シナリオで JSON ファイル内のポリシー ID とルール ID に使用される値をまとめたものです。

| このアカウントに JSON ファイルを作成する場合は、 | ポリシー ID をこの値に設定します | ルール ID をこの値に設定します |
|-|-|-|
| 宛先アカウント | 文字列の *既定値*。 Azure Storage によって自動的にポリシー ID 値が作成されます。 | 空の文字列。 Azure Storage によって自動的にルール ID 値が作成されます。 |
| ソース アカウント | 宛先アカウントで定義されているポリシーを JSON ファイルとしてダウンロードすると、ポリシー ID の値が返されます。 | 宛先アカウントで定義されているポリシーを JSON ファイルとしてダウンロードすると、ルール ID の値が返されます。 |

次の例では、プレフィックス *b* が一致する 1 つのルールを使用して宛先アカウントにレプリケーション ポリシーを定義し、レプリケート対象の BLOB に最小作成時間を設定しています。 山かっこ内の値は、実際の値に置き換えてください。

```json
{
  "properties": {
    "policyId": "default",
    "sourceAccount": "<source-account>",
    "destinationAccount": "<dest-account>",
    "rules": [
      {
        "ruleId": "",
        "sourceContainer": "<source-container>",
        "destinationContainer": "<destination-container>",
        "filters": {
          "prefixMatch": [
            "b"
          ],
          "minCreationTime": "2020-08-028T00:00:00Z"
        }
      }
    ]
  }
}
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal で JSON ファイルを使用して宛先アカウントにオブジェクト レプリケーションを構成するには、次の手順に従います。

1. 宛先アカウントにレプリケーション ポリシーを定義するローカル JSON ファイルを作成します。 Azure Storage でポリシー ID が定義されるように、**policyId** フィールドを *既定値* に設定します。

    レプリケーション ポリシーを定義する JSON ファイルを簡単に作成する方法は、まず、Azure portal で 2 つのストレージ アカウント間にテスト レプリケーション ポリシーを作成することです。 その後、レプリケーション ルールをダウンロードし、必要に応じて JSON ファイルを変更できます。

1. Azure portal で宛先アカウントの **[オブジェクト レプリケーション]** 設定に移動します。
1. **[レプリケーション ルールのアップロード]** を選択します。
1. JSON ファイルをアップロードします。 Azure portal には、次の図に示すように、作成されるポリシーとルールが表示されます。

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="JSON ファイルをアップロードしてレプリケーション ポリシーを定義する方法を示すスクリーンショット":::

1. **[アップロード]** を選択して、宛先アカウントにレプリケーション ポリシーを作成 ます。

その後、別のユーザーに提供できるポリシー定義を含む JSON ファイルをダウンロードして、ソース アカウントを構成できます。 この JSON ファイルをダウンロードするには、次の手順に従います。

1. Azure portal で宛先アカウントの **[オブジェクト レプリケーション]** 設定に移動します。
1. 次の図に示すように、ダウンロードするポリシーの横にある **[詳細]** ボタンを選択し、 **[ルールのダウンロード]** を選択します。

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="レプリケーション ルールを JSON ファイルにダウンロードする方法を示すスクリーンショット":::

1. JSON ファイルをローカル コンピューターに保存して別のユーザーと共有して、ソース アカウントにポリシーを構成します。

ダウンロードした JSON ファイルには、Azure Storage で宛先アカウントのポリシーに作成されたポリシー ID が含まれています。 ソース アカウントでオブジェクト レプリケーションを構成するには、同じポリシー ID を使用する必要があります。

JSON ファイルをアップロードして、Azure portal 経由で宛先アカウントにレプリケーション ポリシーを作成しても、自動的に同じポリシーがソース アカウントに作成されないことに注意してください。 Azure Storage でオブジェクトのレプリケートを開始する前に、別のユーザーがソース アカウントにポリシーを作成する必要があります。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

宛先アカウントのレプリケーション ポリシー定義を含む JSON ファイルを PowerShell からダウンロードするには、[Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) コマンドを呼び出してポリシーを返します。 次に、次の例に示すように、ポリシーを JSON に変換してローカル ファイルとして保存します。 山かっこ内の値とファイル パスは、実際の値に置き換えてください。

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

JSON ファイルを使用して、PowerShell でソース アカウントにレプリケーション ポリシーを定義するには、ローカル ファイルを取得し、JSON からオブジェクトに変換します。 次に、次の例に示すように、[Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) コマンドを呼び出して、ソース アカウントにポリシーを構成します。 山かっこ内の値とファイル パスは、実際の値に置き換えてください。

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

宛先アカウントのレプリケーション ポリシー定義を Azure CLI から JSON ファイルに書き込むには、[az storage account or-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) コマンドを呼び出して、ファイルに出力します。

次の例では、ポリシー定義を *policy.json* という名前の JSON ファイルに書き込みます。 山かっこ内の値とファイル パスは、実際の値に置き換えてください。

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Azure CLI で JSON ファイルを使用してソース アカウントにレプリケーション ポリシーを構成するには、[az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) コマンドを呼び出して、*policy.json* ファイルを参照します。 山かっこ内の値とファイル パスは、実際の値に置き換えてください。

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>BLOB のレプリケーションの状態を確認する

Azure portal、PowerShell、または Azure CLI を使用して、ソース アカウントの BLOB のレプリケーションの状態を確認できます。 レプリケーションが完了するか失敗するまで、オブジェクト レプリケーションのプロパティは設定されません。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でソース アカウントの BLOB のレプリケーションの状態を確認するには、次の手順に従います。

1. Azure portal でソース アカウントに移動します。
1. ソース BLOB が含まれているコンテナーを探します。
1. BLOB を選択して、そのプロパティを表示します。 BLOB が正常にレプリケートされている場合は、状態が *[完了]* に設定されていることが **[オブジェクト レプリケーション]** セクションに示されます。 このコンテナーのオブジェクト レプリケーションを管理するルールのレプリケーション ポリシー ID と ID も表示されます。

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="ソース アカウントの BLOB のレプリケーションの状態を示すスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用してソース アカウントの BLOB のレプリケーションの状態を確認するには、次の例に示すように、オブジェクト レプリケーションの **ReplicationStatus** プロパティの値を取得します。 山かっこ内の値は、実際の値に置き換えてください。

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してソース アカウントの BLOB のレプリケーションの状態を確認するには、次の例に示すように、オブジェクト レプリケーションの **status** プロパティの値を取得します。

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

ソース アカウントの BLOB のレプリケーションの状態が失敗を示している場合は、次の考えられる原因を調査します。

- 宛先アカウントに対してオブジェクト レプリケーション ポリシーが構成されていることを確認します。
- 宛先コンテナーがまだ存在することを確認します。
- 書き込み操作の一部としてカスタマー指定のキーでソース BLOB が暗号化されている場合、オブジェクトのレプリケーションは失敗します。 カスタマー指定のキーの詳細については、「[BLOB ストレージに対する要求で暗号化キーを指定する](encryption-customer-provided-keys.md)」を参照してください。

## <a name="remove-a-replication-policy"></a>レプリケーション ポリシーを削除する

レプリケーション ポリシーとそれに関連するルールを削除するには、Azure portal、PowerShell、または CLI を使用します。

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Azure portal でレプリケーション ポリシーを削除するには、次の手順を実行します。

1. Azure portal でソース ストレージ アカウントに移動します。
1. **[設定]** で、 **[オブジェクト レプリケーション]** を選択します。
1. ポリシー名の横にある **[詳細]** ボタンをクリックします。
1. **[Delete Rules]\(ルールの削除\)** を選択します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

レプリケーション ポリシーを削除するには、ソース アカウントと宛先アカウントの両方からポリシーを削除します。 ポリシーを削除すると、それに関連付けられているルールも削除されます。

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

レプリケーション ポリシーを削除するには、ソース アカウントと宛先アカウントの両方からポリシーを削除します。 ポリシーを削除すると、それに関連付けられているルールも削除されます。

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>次のステップ

- [オブジェクト レプリケーションの概要](object-replication-overview.md)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)
- [Azure Blob Storage の変更フィードを処理する](storage-blob-change-feed-how-to.md)
