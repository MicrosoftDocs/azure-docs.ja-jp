---
title: ブロック BLOB のポイントインタイム リストア (プレビュー)
titleSuffix: Azure Storage
description: ブロック BLOB のポイントインタイム リストアでは、特定の時点でストレージ アカウントを以前の状態に復元できるようにすることで、誤った削除や破損を防ぐことができます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b9514bea1f9c34f0ed36bf530a7129b6fea46c4f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501969"
---
# <a name="point-in-time-restore-for-block-blobs-preview"></a>ブロック BLOB のポイントインタイム リストア (プレビュー)

ポイントインタイム リストアでは、ブロック BLOB データを以前の状態に復元できるようにすることで、誤った削除や破損を防ぐことができます。 ポイントインタイム リストアは、ユーザーまたはアプリケーションによって誤ってデータが削除された場合や、アプリケーション エラーによってデータが破損した場合に役立ちます。 また、ポイントインタイム リストアでは、さらにテストを実行する前にデータセットを既知の状態に戻す必要があるテスト シナリオも有効になります。

ストレージ アカウントのポイントインタイム リストアを有効にする方法については、「[ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)](point-in-time-restore-manage.md)」を参照してください。

## <a name="how-point-in-time-restore-works"></a>ポイントインタイム リストアのしくみ

ポイントインタイム リストアを有効にするには、ストレージ アカウントの管理ポリシーを作成し、保有期間を指定します。 保有期間中は、ブロック BLOB を現在の状態から前の時点の状態に復元できます。

ポイントインタイム リストアを開始するには、[Restore Blob Ranges](/rest/api/storagerp/storageaccounts/restoreblobranges) 操作を呼び出し、復元ポイントを UTC 時刻で指定します。 復元するコンテナーおよび BLOB の名前の辞書式範囲を指定することも、範囲を省略して、ストレージ アカウントのすべてのコンテナーを復元することもできます。 復元操作ごとに最大 10 個の辞書式範囲がサポートされます。

Azure Storage では、要求された復元ポイント (UTC 時刻で指定) から現在の時点までの間で、指定された BLOB に対して行われたすべての変更が分析されます。 復元操作はアトミックであるため、すべての変更を完全に復元できるか、または失敗します。 復元できない BLOB がある場合、操作は失敗し、影響を受けるコンテナーに対する読み取りと書き込み操作が再開されます。

ストレージ アカウントで一度に実行できる復元操作は 1 つだけです。 実行中に復元操作を取り消すことはできませんが、最初の操作を元に戻すために 2 番目の復元操作を実行できます。

**Restore Blob Ranges** 操作では、操作を一意に識別する復元 ID が返されます。 ポイントインタイム リストアの状態を確認するには、**Restore Blob Ranges** 操作から返された復元 ID を使用して **Get Restore Status** 操作を呼び出します。

復元操作には次の制限があることに注意してください。

- [Put Block](/rest/api/storageservices/put-block) または [Put Block from URL](/rest/api/storageservices/put-block-from-url) を使用してアップロードされたが、[Put Block List](/rest/api/storageservices/put-block-list) を使用してコミットされていないブロックは、BLOB の一部ではないため、復元操作の一部としては復元されません。
- アクティブなリースを持つ BLOB は復元できません。 アクティブなリースを持つ BLOB が、復元する BLOB の範囲に含まれている場合、復元操作はアトミックに失敗します。
- スナップショットは、復元操作の一環として作成または削除されません。 ベース BLOB のみが以前の状態に復元されます。
- 現在の時点から復元ポイントまでの間にホット層とクール層の間で BLOB が移動した場合、BLOB は以前の層に復元されます。 ただし、アーカイブ層に移動した BLOB は復元されません。

> [!IMPORTANT]
> 復元操作を実行すると、Azure Storage では操作中、復元される範囲内の BLOB に対するデータ操作がブロックされます。 読み取り、書き込み、および削除の各操作がプライマリ ロケーションでブロックされます。 このため、Azure portal でのコンテナーの一覧表示などの操作は、復元操作の実行中に予期したとおりに実行されない場合があります。
>
> ストレージ アカウントが geo レプリケートされている場合は、復元操作中にセカンダリ ロケーションからの読み取り操作を続行できます。

> [!CAUTION]
> ポイントインタイム リストアでは、ブロック BLOB に対する復元操作のみがサポートされます。 コンテナーに対する操作は復元できません。 ポイントインタイム リストアのプレビュー中に [Delete Container](/rest/api/storageservices/delete-container) 操作を呼び出してストレージ アカウントからコンテナーを削除した場合、そのコンテナーは復元操作を使って復元できません。 プレビュー中は、コンテナーを削除するのではなく、それらを復元する可能性がある場合は個々の BLOB を削除してください。

### <a name="prerequisites-for-point-in-time-restore"></a>ポイントインタイム リストアの前提条件

ポイントインタイム リストアでは、次の Azure Storage 機能が有効になっている必要があります。

- [論理的な削除](soft-delete-overview.md)
- [変更フィード (プレビュー)](storage-blob-change-feed.md)
- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)

ポイントインタイム リストアを有効にする前に、ストレージ アカウントに対してこれらの機能を有効にします。 変更フィードと BLOB バージョン管理のプレビューを有効にする前に、必ず登録してください。

### <a name="retention-period-for-point-in-time-restore"></a>ポイントインタイム リストアの保有期間

ストレージ アカウントのポイントインタイム リストアを有効にする場合は、保有期間を指定します。 ストレージ アカウントのブロック BLOB は、保有期間中に復元できます。

保有期間は、ポイントインタイム リストアを有効にすると開始されます。 保有期間の開始前に BLOB を状態に復元することはできません。 たとえば、5 月 1 日に保有期間を 30 日に設定してポイントインタイム リストアを有効にした場合、5 月 15 日に最大 15 日間まで復元することができます。 6 月 1 日には、1 - 30 日間のデータを復元できます。

ポイントインタイム リストアの保有期間は、論理的な削除に指定された保有期間よりも 1 日以上短くする必要があります。 たとえば、論理的な削除の保有期間が 7 日間に設定されている場合、ポイントインタイム リストアの保有期間は 1 - 6 日間の範囲で指定できます。

### <a name="permissions-for-point-in-time-restore"></a>ポイントインタイム リストアのアクセス許可

復元操作を開始するには、クライアントはストレージ アカウントのすべてのコンテナーに対する書き込みアクセス許可を持っている必要があります。 Azure Active Directory (Azure AD) で復元操作を承認するアクセス許可を付与するには、ストレージ アカウント、リソースグループ、またはサブスクリプションのレベルでセキュリティ プリンシパルに**ストレージ アカウント共同作成者**ロールを割り当てます。

## <a name="about-the-preview"></a>プレビューについて

ポイントインタイム リストアは、汎用 v2 ストレージ アカウントでのみサポートされています。 ポイントインタイム リストアでは、ホットおよびクール アクセス層のデータのみを復元できます。

次のリージョンでは、プレビュー段階でのポイントインタイム リストアがサポートされています。

- カナダ中部
- カナダ東部
- フランス中部

プレビューには、次の制限があります。

- Premium ブロック BLOB の復元はサポートされていません。
- アーカイブ層での BLOB の復元はサポートされていません。 たとえば、ホット層の BLOB が 2 日前にアーカイブ層に移動し、復元操作によって 3 日前の時点に復元された場合、BLOB はホット層に復元されません。
- Azure Data Lake Storage Gen2 のフラット名前空間と階層名前空間の復元はサポートされていません。
- 顧客が指定したキーを使用したストレージ アカウントの復元はサポートされていません。

> [!IMPORTANT]
> ポイントインタイム プレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

### <a name="register-for-the-preview"></a>プレビューに登録する

プレビューに登録するには、次のコマンドを実行します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Register for the point-in-time restore preview
Register-AzProviderFeature -FeatureName RestoreBlobRanges -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -FeatureName Versioning -ProviderNamespace Microsoft.Storage

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature register --namespace Microsoft.Storage --name RestoreBlobRanges
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-registration-status"></a>登録状態を確認する

ポイントインタイム リストアの登録は自動的に実行されます。所要時間は 10 分未満です。 登録の状態を確認するには、次のコマンドを実行します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName RestoreBlobRanges

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/RestoreBlobRanges')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## <a name="pricing-and-billing"></a>価格と課金

ポイントインタイム リストアの課金は、復元操作を実行するために処理されるデータの量によって異なります。 処理されるデータの量は、復元ポイントから現在の時点までの間に発生した変更の数に基づいています。 たとえば、ストレージ アカウントのブロック BLOB データの変更数が比較的一定であると仮定した場合、1 日前に戻す復元操作のコストは、10 日前に戻す復元の 1/10 になります。

復元操作のコストを見積もるには、変更フィード ログを確認して、復元期間中に変更されたデータの量を見積もります。 たとえば、変更フィードの保有期間が 30 日間で、変更フィードのサイズが 10 MB の場合、10 日前の時点に復元すると、そのリージョンの LRS アカウントに対して記載されている価格の約 1/3 になります。 27 日前の時点に復元すると、記載されている価格の約 9 分の 1 になります。

ポイントインタイム リストアの価格の詳細については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」を参照してください。

## <a name="ask-questions-or-provide-feedback"></a>質問したり、フィードバックを提供したりする

ポイントインタイム リストアのプレビューについて質問したり、フィードバックを提供したりするには、Microsoft (pitrdiscussion@microsoft.com) にお問い合わせください。

## <a name="next-steps"></a>次のステップ

- [ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)](point-in-time-restore-manage.md)
- [Azure Blob Storage の変更フィードのサポート (プレビュー)](storage-blob-change-feed.md)
- [BLOB の論理的な削除を有効にする](soft-delete-enable.md)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)
