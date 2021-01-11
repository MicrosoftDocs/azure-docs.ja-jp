---
title: BLOB のバージョン管理 (プレビュー)
titleSuffix: Azure Storage
description: BLOB ストレージのバージョン管理 (プレビュー) では、以前のバージョンのオブジェクトが自動的に維持され、タイムスタンプで識別されます。 以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli
ms.openlocfilehash: 494c1fc1c1c91538240258ab0517c7ff79bdfa74
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056535"
---
# <a name="blob-versioning-preview"></a>BLOB のバージョン管理 (プレビュー)

BLOB ストレージのバージョン管理 (プレビュー) を有効にし、以前のバージョンのオブジェクトを自動的に維持することができます。  BLOB のバージョン管理が有効になっている場合は、以前のバージョンの BLOB を復元し、データが誤って変更または削除された場合に復旧することができます。

BLOB のバージョン管理はストレージ アカウントに対して有効になり、ストレージ アカウント内のすべての BLOB に適用されます。 ストレージ アカウントに対して BLOB のバージョン管理を有効にした後、Azure Storage によって、ストレージ アカウント内のすべての BLOB のバージョンが自動的に維持されます。

Microsoft では、優れたデータ保護を実現するために、BLOB のバージョン管理を使用して以前のバージョンの BLOB を維持することをお勧めします。 可能であれば、BLOB スナップショットではなく、BLOB のバージョン管理を使用して、以前のバージョンを維持するようにしてください。 BLOB スナップショットでは、以前のバージョンの BLOB が維持されるという点で似たような機能が提供されますが、スナップショットはアプリケーションを使用して手動で維持する必要があります。

BLOB のバージョン管理を有効にする方法については、「[BLOB のバージョン管理を有効にして管理する](versioning-enable.md)」を参照してください。

> [!IMPORTANT]
> ストレージ アカウントやコンテナーが誤って削除された場合、BLOB のバージョン管理はその復旧には役立ちません。 ストレージ アカウントが誤って削除されないようにするには、ストレージ アカウント リソースに対して **CannotDelete** ロックを構成します。 Azure リソースのロックの詳細については、「[リソースのロックによる予期せぬ変更の防止](../../azure-resource-manager/management/lock-resources.md)」を参照してください。

## <a name="how-blob-versioning-works"></a>BLOB のバージョン管理のしくみ

バージョンによって、指定された時点での BLOB の状態がキャプチャされます。 ストレージ アカウントに対して BLOB のバージョン管理が有効になっている場合、BLOB が変更または削除されるたびに、Azure Storage によって新しいバージョンの BLOB が自動的に作成されます。

バージョン管理が有効になっている BLOB を作成すると、新しい BLOB が現在のバージョンの BLOB (またはベース BLOB) になります。 その後、その BLOB を変更すると、変更される前の BLOB の状態をキャプチャするバージョンが Azure Storage によって作成されます。 変更された BLOB は、新しい現在のバージョンになります。 BLOB を変更するたびに、新しいバージョンが作成されます。

バージョン管理が有効になっている BLOB を削除すると、削除される前の BLOB の状態をキャプチャするバージョンが Azure Storage によって作成されます。 その後、現在のバージョンの BLOB は削除されますが、BLOB のバージョンは保持されるため、必要に応じて再作成することができます。 

BLOB のバージョンは変更できません。 既存の BLOB のバージョンの内容やメタデータを変更することはできません。

### <a name="version-id"></a>バージョン ID

各 BLOB のバージョンは、バージョン ID によって識別されます。 バージョン ID の値は、BLOB が書き込まれたか更新された時点のタイムスタンプです。 バージョン ID は、バージョンの作成時に割り当てられます。

バージョン ID を指定することにより、特定のバージョンの BLOB に対する読み取りや削除の操作を実行できます。 バージョン ID を省略した場合、現在のバージョン (ベース BLOB) に対して操作が行われます。

書き込み操作を呼び出して BLOB を作成または変更すると、Azure Storage によって、応答で *x-ms-version-id* ヘッダーが返されます。 このヘッダーには、書き込み操作で作成された現在のバージョンの BLOB のバージョン ID が含まれています。

バージョン ID はバージョンの有効期間中は変わりません。

### <a name="versioning-on-write-operations"></a>書き込み操作でのバージョン管理

BLOB のバージョン管理が有効になっている場合、BLOB に対する各書き込み操作で新しいバージョンが作成されます。 書き込み操作には [Put Blob](/rest/api/storageservices/put-blob)、[Put Block List](/rest/api/storageservices/put-block-list)、[Copy Blob](/rest/api/storageservices/copy-blob)、および [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata) が含まれます。

書き込み操作で新しい BLOB が作成された場合、結果として得られる BLOB が現在のバージョンの BLOB となります。 書き込み操作で既存の BLOB が変更された場合は、現在のバージョンである更新された BLOB に新しいデータがキャプチャされ、BLOB の以前の状態を保存するバージョンが Azure Storage によって作成されます。

わかりやすくするために、この記事に示されている図では、バージョン ID がシンプルな整数値として示されています。 実際には、バージョン ID はタイムスタンプです。 現在のバージョンは青色で示され、以前のバージョンは灰色で示されます。

次の図は、書き込み操作が BLOB のバージョンにどのように影響するかを示しています。 BLOB が作成されると、その BLOB が現在のバージョンになります。 同じ BLOB が変更されると、BLOB の以前の状態を保存するために新しいバージョンが作成され、更新された BLOB が現在のバージョンになります。

:::image type="content" source="media/versioning-overview/write-operations-blob-versions.png" alt-text="書き込み操作がバージョン管理された BLOB にどのように影響するかを示す図":::

> [!NOTE]
> ストレージ アカウントに対してバージョン管理を有効にする前に作成された BLOB には、バージョン ID はありません。 BLOB が変更されると、その変更された BLOB が現在のバージョンになり、更新前の BLOB の状態を保存するためにバージョンが作成されます。 バージョンには、その作成時刻であるバージョン ID が割り当てられます。

### <a name="versioning-on-delete-operations"></a>削除操作でのバージョン管理

BLOB を削除すると、現在のバージョンの BLOB が以前のバージョンになり、ベース BLOB が削除されます。 BLOB が削除されるときに、既存の以前のバージョンの BLOB はすべて保持されます。

バージョン ID を指定せずに [Delete Blob](/rest/api/storageservices/delete-blob) 操作を呼び出すと、ベース BLOB が削除されます。 特定のバージョンを削除するには、削除操作に対してそのバージョンの ID を指定します。

次の図は、バージョン管理された BLOB に対する削除操作の影響を示しています。

:::image type="content" source="media/versioning-overview/delete-versioned-base-blob.png" alt-text="バージョン管理された BLOB の削除を示す図":::

新しいデータを BLOB に書き込むと、新しいバージョンの BLOB が作成されます。 次の図に示すように、既存のバージョンは影響を受けません。

:::image type="content" source="media/versioning-overview/recreate-deleted-base-blob.png" alt-text="削除後のバージョン管理された BLOB の再作成を示す図":::

### <a name="blob-types"></a>BLOB の種類

ストレージ アカウントで BLOB のバージョン管理が有効になっている場合、ブロック BLOB に対するすべての書き込みおよび削除操作で新しいバージョンの作成がトリガーされます。ただし、[Put Block](/rest/api/storageservices/put-block) 操作は除きます。

ページ BLOB と追加 BLOB の場合、書き込みおよび削除操作のサブセットによってのみ、バージョンの作成がトリガーされます。 記録される操作には次のようなものがあります。

- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Block List](/rest/api/storageservices/put-block-list)
- [Delete Blob](/rest/api/storageservices/delete-blob)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Copy Blob](/rest/api/storageservices/copy-blob)

次の操作では、新しいバージョンの作成はトリガーされません。 これらの操作から変更をキャプチャするには、手動スナップショットを作成します。

- [Put Page](/rest/api/storageservices/put-page) (ページ BLOB)
- [Append Block](/rest/api/storageservices/append-block) (追加 BLOB)

すべてのバージョンの BLOB が、同じ BLOB の種類である必要があります。 BLOB に以前のバージョンがある場合、BLOB とそのすべてのバージョンを最初に削除しない限り、ある種類の BLOB を別の種類で上書きすることはできません。

### <a name="access-tiers"></a>アクセス層

[Set Blob Tier](/rest/api/storageservices/set-blob-tier) 操作を呼び出すことにより、現在のバージョンを含む、任意のバージョンのブロック BLOB を別の BLOB のアクセス層に移動することができます。 古いバージョンの BLOB をクールまたはアーカイブ層に移動することにより、より低い容量価格を利用できます。 詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ アクセス層](storage-blob-storage-tiers.md)」を参照してください。

ブロック BLOB を適切な層に移動するプロセスを自動化するには、BLOB のライフ サイクル管理を使用します。 ライフ サイクル管理の詳細については、「[Azure Blob Storage のライフ サイクルを管理する](storage-lifecycle-management-concepts.md)」を参照してください。

## <a name="enable-or-disable-blob-versioning"></a>BLOB のバージョン管理を有効または無効にする

BLOB のバージョン管理を有効または無効にする方法については、「[BLOB のバージョン管理を有効または無効にする](versioning-enable.md)」を参照してください。

BLOB のバージョン管理を無効にしても、既存の BLOB、バージョン、スナップショットは削除されません。 BLOB のバージョン管理を無効にした場合、既存のすべてのバージョンはストレージ アカウントで引き続きアクセス可能です。 その後、新しいバージョンは作成されません。

ストレージ アカウントに対してバージョン管理が無効になった後に BLOB が作成または変更された場合、BLOB を上書きすると新しいバージョンが作成されます。 更新された BLOB は現在のバージョンではなくなり、バージョン ID はありません。 BLOB に対する以降のすべての更新では、前の状態を保存せずにデータが上書きされます。

バージョン管理が無効になった後に、バージョン ID を使用してバージョンを読み取ったり削除したりすることができます。 また、バージョン管理が無効になった後に、BLOB のバージョンを一覧表示することもできます。

次の図は、バージョン管理が無効になった後に BLOB を変更すると、バージョン管理されない BLOB がどのように作成されるかを示しています。 BLOB に関連付けられている既存のすべてのバージョンが保持されます。

:::image type="content" source="media/versioning-overview/modify-base-blob-versioning-disabled.png" alt-text="バージョン管理が無効になった後に変更されたベース BLOB を示す図":::

## <a name="blob-versioning-and-soft-delete"></a>BLOB のバージョン管理と論理的な削除

最適なデータ保護を提供するために、BLOB のバージョン管理と BLOB の論理的な削除は連動します。 論理的な削除を有効にする場合は、Azure Storage で論理的に削除された BLOB を保持する期間を指定します。 論理的に削除された BLOB のバージョンはシステムで維持され、論理的な削除の保有期間内に削除を取り消すことができます。 BLOB の論理的な削除の詳細については、[Azure Storage BLOB の論理的な削除](storage-blob-soft-delete.md)に関するページを参照してください。

### <a name="deleting-a-blob-or-version"></a>BLOB またはバージョンの削除

論理的な削除では、BLOB のバージョンを削除するための追加の保護が提供されます。 ストレージ アカウントに対してバージョン管理と論理的な削除の両方が有効になっている場合、BLOB を削除すると、削除の直前に BLOB の状態を保存するための新しいバージョンが Azure Storage によって作成され、現在のバージョンが削除されます。 新しいバージョンは論理的に削除されず、論理的な削除の保有期間が過ぎても削除されません。

以前のバージョンの BLOB を削除すると、そのバージョンは論理的に削除されます。 論理的に削除されたバージョンは、ストレージ アカウントの論理的な削除の設定で指定された保有期間を通して保持され、論理的な削除の保有期間が過ぎると完全に削除されます。

以前のバージョンの BLOB を削除するには、バージョン ID を指定して明示的に削除します。

次の図は、BLOB または BLOB のバージョンを削除した場合の動作を示しています。

:::image type="content" source="media/versioning-overview/soft-delete-historical-version.png" alt-text="論理的な削除が有効になっているバージョンの削除を示す図":::

ストレージ アカウントに対してバージョン管理と論理的な削除の両方が有効になっている場合、BLOB または BLOB のバージョンが変更または削除されるときに、論理的に削除されたスナップショットは作成されません。

### <a name="restoring-a-soft-deleted-version"></a>論理的に削除されたバージョンの復元

論理的に削除された BLOB のバージョンは、論理的な削除の保有期間が有効になっている間に、[Undelete Blob](/rest/api/storageservices/undelete-blob) 操作を呼び出すことで復元できます。 **Undelete Blob** 操作では、論理的に削除されたすべてのバージョンの BLOB が復元されます。

**Undelete Blob** 操作で論理的に削除されたバージョンを復元しても、どのバージョンも現在のバージョンに昇格されません。 現在のバージョンを復元するには、最初に論理的に削除されたすべてのバージョンを復元してから、[Copy Blob](/rest/api/storageservices/copy-blob) 操作を使用して以前のバージョンをコピーし、BLOB を復元します。

次の図は、**Undelete Blob** 操作で論理的に削除された BLOB のバージョンを復元する方法と、**Copy Blob** 操作で現在のバージョンの BLOB を復元する方法を示しています。

:::image type="content" source="media/versioning-overview/undelete-version.png" alt-text="論理的に削除されたバージョンを復元する方法を示す図":::

論理的な削除の保有期間が経過した後、論理的に削除された BLOB のバージョンは完全に削除されます。

## <a name="blob-versioning-and-blob-snapshots"></a>BLOB のバージョン管理と BLOB のスナップショット

BLOB のスナップショットは、ある時点で作成された BLOB の読み取り専用コピーです。 BLOB のスナップショットと BLOB のバージョンは似ていますが、スナップショットがユーザーまたはアプリケーションによって手動で作成されるのに対し、BLOB のバージョンは、ストレージ アカウントで BLOB のバージョン管理が有効になっている場合に書き込みまたは削除操作で自動的に作成されます。

> [!IMPORTANT]
> Microsoft では、BLOB のバージョン管理を有効にした後で、ブロック BLOB のスナップショットの作成を停止するようにアプリケーションを更新することもお勧めします。 ストレージ アカウントでバージョン管理が有効になっている場合、すべてのブロック BLOB の更新と削除がキャプチャされ、バージョンによって保持されます。 BLOB のバージョン管理が有効になっている場合、スナップショットを作成してもブロック BLOB データに対する追加の保護は提供されず、コストとアプリケーションの複雑さが増す可能性があります。

### <a name="snapshot-a-blob-when-versioning-is-enabled"></a>バージョン管理が有効になっている場合に BLOB のスナップショットを作成する

お勧めしませんが、バージョン管理もされる BLOB のスナップショットを作成することができます。 バージョン管理を有効にしたときに、BLOB のスナップショットの作成を停止するようにアプリケーションを更新できない場合、アプリケーションではスナップショットとバージョンの両方をサポートできます。

バージョン管理された BLOB のスナップショットを作成すると、スナップショットの作成と同時に新しいバージョンが作成されます。 スナップショットの作成時に、新しい現在のバージョンも作成されます。

次の図は、バージョン管理された BLOB のスナップショットを作成した場合の動作を示しています。 この図では、バージョン ID が 2 および 3 の BLOB バージョンとスナップショットに同一のデータが含まれています。

:::image type="content" source="media/versioning-overview/snapshot-versioned-blob.png" alt-text="バージョン管理された BLOB のスナップショットを示す図 ":::

## <a name="authorize-operations-on-blob-versions"></a>BLOB のバージョンに対する操作を承認する

次のいずれかの方法を使用して、BLOB のバージョンへのアクセスを承認することができます。

- ロールベースのアクセス制御 (RBAC) を使用して、Azure Active Directory (Azure AD) セキュリティ プリンシパルにアクセス許可を付与する。 セキュリティと使いやすさのために、Azure AD を使用することをお勧めします。 BLOB 操作での Azure AD の使用に関する詳細については、「[Azure Active Directory を使用して BLOB とキューへのアクセスを承認する](../common/storage-auth-aad.md)」を参照してください。
- Shared Access Signature (SAS) を使用して、BLOB バージョンへのアクセスを委任する。 BLOB バージョンを表す、署名済みリソースの種類である `bv` のバージョン ID を指定して、特定のバージョンに対する操作の SAS トークンを作成します。 Shared Access Signature の詳細については、「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../common/storage-sas-overview.md)」を参照してください。
- アカウント アクセス キーを使用して、共有キーでの BLOB バージョンに対する操作を承認する。 詳細については、[共有キーによる承認](/rest/api/storageservices/authorize-with-shared-key)に関するページを参照してください。

BLOB のバージョン管理は、誤削除や悪意のある削除からデータを保護するように設計されています。 保護を強化するために、BLOB バージョンを削除するには特殊なアクセス許可が必要です。 以下のセクションでは、BLOB バージョンを削除するために必要なアクセス許可について説明します。

### <a name="rbac-action-to-delete-a-blob-version"></a>BLOB バージョンを削除する RBAC アクション

次の表には、BLOB または BLOB バージョンの削除をサポートする RBAC アクションが示されています。

| 説明 | Blob service の操作 | 必要な RBAC データ アクション | RBAC の組み込みロールのサポート |
|----------------------------------------------|------------------------|---------------------------------------------------------------------------------------|-------------------------------|
| 現在のバージョンの BLOB の削除 | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete** | ストレージ BLOB データ共同作成者 |
| バージョンの削除 | Delete Blob | **Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action** | ストレージ BLOB データ所有者 |

### <a name="shared-access-signature-sas-parameters"></a>Shared Access Signature (SAS) パラメーター

BLOB バージョンの署名済みリソースは、`bv` です。 詳細については、「[サービス SAS を作成する](/rest/api/storageservices/create-service-sas)」または「[ユーザー委任 SAS の作成](/rest/api/storageservices/create-user-delegation-sas)」を参照してください。

次の表には、BLOB バージョンを削除するために SAS に必要な権限が示されています。

| **権限** | **URI の略記** | **許可される操作** |
|----------------|----------------|------------------------|
| 削除         | x              | BLOB バージョンを削除します。 |

## <a name="about-the-preview"></a>プレビューについて

次のリージョンでは、BLOB のバージョン管理はプレビューでご利用いただけます。

- 米国東部 2
- 米国中部
- 北ヨーロッパ
- 西ヨーロッパ
- フランス中部
- カナダ東部
- カナダ中部

> [!IMPORTANT]
> BLOB のバージョン管理のプレビューは、非運用環境のみでの使用を意図されています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

バージョン 2019-10-10 以降の Azure Storage REST API では、BLOB のバージョン管理がサポートされています。

### <a name="storage-account-support"></a>ストレージ アカウントのサポート

BLOB のバージョン管理は、次の種類のストレージ アカウントで使用できます。

- 汎用 v2 ストレージ アカウント
- ブロック BLOB ストレージ アカウント
- BLOB ストレージ アカウント

ストレージ アカウントが汎用 v1 アカウントの場合は、Azure portal を使用して、汎用 v2 アカウントにアップグレードします。 ストレージ アカウントについて詳しくは、「[Azure ストレージ アカウントの概要](../common/storage-account-overview.md)」をご覧ください。

階層型名前空間が Azure Data Lake Storage Gen2 で使用できるストレージ アカウントは、現在、サポートされていません。

### <a name="register-for-the-preview"></a>プレビューに登録する

BLOB のバージョン管理プレビューに登録するには、PowerShell または Azure CLI を使用して、サブスクリプションに機能を登録するための要求を送信します。 要求が承認された後、新規または既存の汎用 v2、BLOB ストレージ、または Premium ブロック BLOB ストレージ アカウントを使用して、BLOB のバージョン管理を有効にすることができます。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell を使用して登録を行うには、[Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) コマンドを呼び出します。

```powershell
# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用して登録を行うには、[az feature register](/cli/azure/feature#az-feature-register) コマンドを呼び出します。

```azurecli
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### <a name="check-the-status-of-your-registration"></a>登録の状態を確認する

登録の状態を確認するには、PowerShell または Azure CLI を使用します。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

PowerShell での登録の状態を確認するには、[Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature) コマンドを呼び出します。

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI での登録の状態を確認するには、[az feature](/cli/azure/feature#az-feature-show) コマンドを呼び出します。

```azurecli
az feature show --namespace Microsoft.Storage --name Versioning
```

---

## <a name="pricing-and-billing"></a>価格と課金

BLOB のバージョン管理を有効にすると、アカウントに追加のデータ ストレージ料金が発生する可能性があります。 不要なコストを抑えるためにも、アプリケーションを設計する際は、この料金が発生するしくみを理解しておくことが重要です。

BLOB のスナップショットと同様に、BLOB のバージョンは、アクティブなデータと同じレートで課金されます。 バージョンでブロックまたはページをそのベース BLOB と共有している場合は、バージョンとベース BLOB の間で共有されていない追加のブロックまたはページに対してのみ料金が発生します。

> [!NOTE]
> 頻繁に上書きされるデータに対してバージョン管理を有効にすると、ストレージ容量の料金が増えたり、操作を一覧表示している間の待ち時間が長くなったりすることがあります。 これらの問題を軽減するには、頻繁に上書きされるデータを、バージョン管理が無効になっている別のストレージ アカウントに格納します。

### <a name="important-billing-considerations"></a>課金に関する重要な考慮事項

BLOB のバージョン管理を有効にするときは、必ず次の点を考慮してください。

- ストレージ アカウントには、一意のブロックまたはページに対する料金が発生します。それらが BLOB と以前のバージョンの BLOB のどちらに含まれているかは関係ありません。 ベースになっている BLOB を更新するまでは、BLOB に関連付けられているバージョンについて、アカウントに追加料金が発生することはありません。 BLOB を更新した後、その以前のバージョンから分岐されます。 この場合、各 BLOB またはバージョンの一意のブロックまたはページに対して課金されます。
- ブロック BLOB 内のブロックを置き換えた場合、以後そのブロックは一意のブロックとして課金されます。 これは、ブロックのブロック ID とデータが同じである場合でも当てはまります。 ブロックが再度コミットされた後、バージョン内の対応するブロックから分岐し、そのデータに対する料金が発生します。 これは、まったく同じデータでページ BLOB 内のページを更新した場合にも当てはまります。
- BLOB ストレージには、2 つのブロックに同一のデータが含まれているかどうかを判断する手段はありません。 アップロードされてコミットされたブロックは、同じデータや同じブロック ID がある場合でも、それぞれが一意のものとして扱われます。 料金は一意のブロックに対して発生するため、バージョン管理が有効になっている場合に BLOB を更新すると、一意のブロックが増ええ、追加料金が発生することを考慮することが重要です。
- BLOB のバージョン管理が有効になっている場合は、ブロック BLOB に対して更新操作を設計し、可能な限り少ない数のブロックが更新されるようにします。 ブロックの詳細な制御を許可する書き込み操作は、[Put Block](/rest/api/storageservices/put-block) と [Put Block List](/rest/api/storageservices/put-block-list) です。 一方、[Put Blob](/rest/api/storageservices/put-blob) 操作では、BLOB の内容全体が置き換えられるため、追加料金が発生する可能性があります。

### <a name="versioning-billing-scenarios"></a>バージョン管理の課金シナリオ

ブロック BLOB とそのバージョンについて料金が発生するしくみを、次のシナリオで説明します。

#### <a name="scenario-1"></a>シナリオ 1

シナリオ 1 では、BLOB に以前のバージョンがあります。 バージョンが作成されてから BLOB は更新されていません。したがって、一意のブロックである 1、2、3 に対してのみ、料金が発生します。

![Azure Storage のリソース](./media/versioning-overview/versions-billing-scenario-1.png)

#### <a name="scenario-2"></a>シナリオ 2

シナリオ 2 では、BLOB の 1 つのブロック (図のブロック 3) が更新されています。 更新されたブロックに同じデータと ID が含まれている場合でも、以前のバージョンのブロック 3 と同じではありません。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/versioning-overview/versions-billing-scenario-2.png)

#### <a name="scenario-3"></a>シナリオ 3

シナリオ 3 では、BLOB は更新されていますが、バージョンは更新されていません。 ベース BLOB のブロック 3 がブロック 4 に置き換わっていますが、以前のバージョンでは引き続きブロック 3 が反映されています。 このため、4 ブロック分の料金がアカウントに課金されます。

![Azure Storage のリソース](./media/versioning-overview/versions-billing-scenario-3.png)

#### <a name="scenario-4"></a>シナリオ 4

シナリオ 4 では、ベース BLOB が完全に更新されており、元のブロックは 1 つも含まれていません。 その結果、アカウントには、8 つ (ベース BLOB の 4 つと、以前のバージョンの 4 つ) のすべての一意のブロックの料金が課金されます。 このシナリオは、Put Blob 操作で BLOB に書き込む場合に発生する可能性があります。これは、ベース BLOB の内容全体が置き換えられるためです。

![Azure Storage のリソース](./media/versioning-overview/versions-billing-scenario-4.png)

## <a name="see-also"></a>関連項目

- [BLOB のバージョン管理を有効にする](versioning-enable.md)
- [BLOB のスナップショットの作成](/rest/api/storageservices/creating-a-snapshot-of-a-blob)
- [Azure Storage BLOB の論理的な削除](storage-blob-soft-delete.md)
