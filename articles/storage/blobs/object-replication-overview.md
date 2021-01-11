---
title: オブジェクト レプリケーションの概要 (プレビュー)
titleSuffix: Azure Storage
description: オブジェクト レプリケーション (プレビュー) を使用すると、ソース ストレージ アカウントと宛先アカウントの間でブロック BLOB を非同期にコピーできます。 オブジェクト レプリケーションを使用すると、読み取り要求の待機時間を最小化し、コンピューティング ワークロードの効率を高め、データ分散を最適化し、コストを最小限に抑えることができます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 434fe938261c1576a5f0c3c8f08ffa8ed3243a27
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230723"
---
# <a name="object-replication-for-block-blobs-preview"></a>ブロック BLOB のオブジェクト レプリケーション (プレビュー)

オブジェクト レプリケーション (プレビュー) を使用すると、ソース ストレージ アカウントと宛先アカウントの間でブロック BLOB を非同期にコピーできます。 オブジェクト レプリケーションでサポートされるシナリオには次のものがあります。

- **待機時間の最小化。** オブジェクト レプリケーションでは、クライアントが物理的に近いリージョンのデータを使用できるようにすることで、読み取り要求の待機時間を短縮できます。
- **コンピューティング ワークロードの効率の向上。** オブジェクト レプリケーションでは、コンピューティングワークロードが同じブロック BLOB セットを複数のリージョンで処理できます。
- **データ分散の最適化。** データを 1 つの場所で処理または分析し、その結果のみを別のリージョンにレプリケートできます。
- **コストの最適化。** データがレプリケートされた後に、ライフ サイクル管理ポリシーを使用してデータをアーカイブ層に移動することで、コストを削減できます。

次の図は、オブジェクト レプリケーションによって、あるリージョンのソース ストレージ アカウントから別の 2 つのリージョンの宛先アカウントにブロック BLOB がレプリケートされる様子を示しています。

:::image type="content" source="media/object-replication-overview/object-replication-diagram.svg" alt-text="オブジェクト レプリケーションのしくみを示す図":::

オブジェクト レプリケーションを構成する方法については、[オブジェクト レプリケーションの構成 (プレビュー)](object-replication-configure.md) に関するページを参照してください。

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="object-replication-policies-and-rules"></a>オブジェクト レプリケーションのポリシーとルール

オブジェクト レプリケーションを構成するときに、ソース ストレージ アカウントと宛先アカウントを指定するレプリケーション ポリシーを作成します。 レプリケーション ポリシーには、ソース コンテナーと宛先コンテナーを指定し、レプリケートするソース コンテナー内のブロック BLOB を示す 1 つ以上のルールが含まれます。

オブジェクト レプリケーションを構成すると、Azure Storage によってソース アカウントの変更フィードが定期的にチェックされ、書き込みまたは削除操作が宛先アカウントに非同期にレプリケートされます。 レプリケーションの待機時間は、レプリケートされるブロック BLOB のサイズによって異なります。

> [!IMPORTANT]
> ブロック BLOB データは非同期にレプリケートされるため、ソース アカウントと宛先アカウントはすぐには同期されません。現在、宛先アカウントにデータをレプリケートするための所要時間に関する SLA はありません。

### <a name="replications-policies"></a>レプリケーション ポリシー

オブジェクト レプリケーションを構成すると、Azure Storage リソース プロバイダー経由でソース アカウントと宛先アカウントの両方にレプリケーション ポリシーが作成されます。 レプリケーション ポリシーは、ポリシー ID で識別されます。 レプリケーションを実行するには、ソースと宛先のアカウントのポリシーに、同じポリシー ID が設定されている必要があります。

1 つのストレージ アカウントを、最大 2 つの宛先アカウントのソース アカウントとして使用できます。 また、宛先アカウントにはソース アカウントを最大 2 つまで含めることができます。 ソースと宛先のアカウントがすべて異なるリージョンに存在していてもかまいません。 それぞれの宛先アカウントにデータをレプリケートするように、個別のレプリケーション ポリシーを構成できます。

### <a name="replication-rules"></a>レプリケーション ルール

レプリケーション ルールでは、Azure Storage がソース コンテナーから宛先コンテナーにどのように BLOB をレプリケートするかを指定します。 レプリケーション ポリシーごとに最大 10 個のレプリケーション ルールを指定できます。 各ルールではソースと宛先のコンテナーが 1 つずつ定義され、ソースと宛先の各コンテナーは 1 つのルールでのみ使用できます。

レプリケーション ルールを作成すると、既定では、その後にソース コンテナーに追加された新しいブロック BLOB のみがコピーされます。 新しいブロック BLOB と既存のブロック BLOB の両方がコピーされるように指定することも、指定した時間以降に作成されたブロック BLOB をコピーするカスタム コピー スコープを定義することもできます。

また、レプリケーション ルールの一部として 1 つ以上のフィルターを指定して、ブロック BLOB をプレフィックスでフィルター処理することもできます。 プレフィックスを指定すると、ソース コンテナー内のそのプレフィックスと一致する BLOB のみが宛先コンテナーにコピーされます。

ルールでソースと宛先のコンテナーを指定する前に、それらの両方が存在している必要があります。 レプリケーション ポリシーを作成すると、宛先コンテナーは読み取り専用になります。 宛先コンテナーへの書き込みを試みると、エラー コード 409 (競合) で失敗します。 ただし、宛先コンテナーの BLOB で [Set Blob Tier](/rest/api/storageservices/set-blob-tier) 操作を呼び出して、それをアーカイブ層に移動することはできます。 アーカイブ層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md#archive-access-tier)」を参照してください。

## <a name="about-the-preview"></a>プレビューについて

オブジェクト レプリケーションは、汎用 v2 ストレージ アカウントでのみサポートされます。 オブジェクト レプリケーションは、次のリージョンでプレビューとして公開されています。

- フランス中部
- カナダ東部
- カナダ中部
- 米国東部 2
- 米国中部

オブジェクト レプリケーションを使用するには、ソースと宛先の両方のアカウントがこれらのリージョンのいずれかに存在している必要があります。 アカウントが 2 つの異なるリージョンに存在していてもかまいません。

プレビュー期間中は、ストレージ アカウント間でのデータのレプリケートに関連する追加コストは発生しません。

> [!IMPORTANT]
> このオブジェクト レプリケーションのプレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

### <a name="prerequisites-for-object-replication"></a>オブジェクト レプリケーションの前提条件

オブジェクト レプリケーションを使用するには、次の Azure Storage 機能が有効になっている必要があります。 
- [変更フィード](storage-blob-change-feed.md)
- [バージョン管理](versioning-overview.md)

オブジェクト レプリケーションを構成する前に、その前提条件を有効にしてください。 変更フィードはソース アカウントで有効にする必要があり、BLOB バージョン管理はソースと宛先の両方のアカウントで有効にする必要があります。 これらの機能を有効にする方法の詳細については、次の記事を参照してください。

- [変更フィードを有効または無効にする](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)

変更フィードと BLOB バージョン管理のプレビューを有効にする前に、必ずそれらのプレビューに登録してください。

変更フィードと BLOB バージョン管理を有効にすると、追加のコストが発生する場合があります。 詳細については、[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)に関するページを参照してください。

### <a name="register-for-the-preview"></a>プレビューに登録する

オブジェクト レプリケーションのプレビューに登録するには、PowerShell または Azure CLI を使用します。 変更フィードと BLOB バージョン管理のプレビューにまだ登録していない場合は、そちらにも登録してください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell でプレビューに登録するには、次のコマンドを実行します。

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for Blob versioning
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI でプレビューに登録するには、次のコマンドを実行します。

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>登録状態を確認する

登録リクエストの状態は、PowerShell または Azure CLI を使用して確認できます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して登録リクエストの状態を確認するには、次のコマンドを実行します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して登録リクエストの状態を確認するには、次のコマンドを実行します。

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="ask-questions-or-provide-feedback"></a>質問やフィードバックを送る

オブジェクト レプリケーションのプレビューに関する質問やフィードバックについては、Microsoft (AzureStorageFeedback@microsoft.com) にご連絡ください。 [Azure Storage フィードバック フォーラム](https://feedback.azure.com/forums/217298-storage)では、Azure Storage に関するアイデアや提案をお待ちしています。

## <a name="next-steps"></a>次のステップ

- [オブジェクト レプリケーションを構成する (プレビュー)](object-replication-configure.md)
- [Azure Blob Storage の変更フィードのサポート (プレビュー)](storage-blob-change-feed.md)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)
